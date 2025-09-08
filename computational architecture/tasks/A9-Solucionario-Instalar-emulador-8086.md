# A9: Solucionario - Instalar emulador 8086

## 📝 Resumen de la Actividad
**Objetivo:** Instalar y probar un emulador de CPU 8086, documentando el proceso y evidenciando su funcionamiento.

---

## 🎯 Solución Paso a Paso

### 1. Selección del Emulador

#### **🏆 Emuladores Recomendados por Plataforma:**

**Windows:**
- **EMU8086** - Más completo y educativo
- **DOSBox** - Para ejecutar herramientas reales de desarrollo
- **MASM32** - Entorno completo de desarrollo

**Linux:**
- **DOSBox** - Mejor compatibilidad
- **NASM + GDB** - Herramientas nativas
- **QEMU** - Emulación completa de sistema

**macOS:**
- **DOSBox** - Funciona bien en macOS
- **Docker con DOSBox** - Alternativa containerizada

#### **🎯 Recomendación Principal: EMU8086**
- **Ventajas:** Interfaz gráfica amigable, debugger integrado, ejemplos incluidos
- **Plataforma:** Principalmente Windows (funciona en Wine/Linux)
- **Descarga:** http://www.emu8086.com/

---

### 2. Instalación de EMU8086 (Windows)

#### **📥 Proceso de Descarga:**

**Paso 1: Descargar el Instalador**
1. Navegar a: http://www.emu8086.com/
2. Buscar "Download EMU8086"
3. Descargar archivo: `emu8086v408r11.zip`
4. Verificar tamaño: ~5.2 MB

**Paso 2: Extracción e Instalación**
```
1. Extraer el archivo ZIP descargado
2. Localizar archivo: setup.exe
3. Ejecutar como Administrador (clic derecho → "Run as administrator")
4. Seguir asistente de instalación:
   - Aceptar licencia
   - Seleccionar directorio: C:\emu8086
   - Crear accesos directos
   - Finalizar instalación
```

#### **🔧 Configuración Inicial:**

**Primer Arranque:**
1. Ejecutar EMU8086 desde menú inicio
2. Aparece ventana principal con:
   - Editor de código
   - Botones de control (Compile, Emulate, etc.)
   - Panel de registros
   - Ventana de memoria

**Configurar Preferencias:**
```
Menu → Options → Preferences
- Syntax Highlighting: Enabled
- Auto-complete: Enabled  
- Font Size: 12pt (ajustar según preferencia)
- Color Scheme: Dark/Light según gusto
```

---

### 3. Instalación Alternativa: DOSBox + MASM

#### **🐧 Instalación en Linux (Ubuntu/Debian):**

**Paso 1: Instalar DOSBox**
```bash
# Actualizar repositorios
sudo apt update

# Instalar DOSBox
sudo apt install dosbox

# Verificar instalación
dosbox --version
```

**Paso 2: Obtener MASM (Microsoft Macro Assembler)**
```bash
# Crear directorio de trabajo
mkdir ~/dos-tools
cd ~/dos-tools

# Descargar MASM (ejemplo con wget)
wget http://www.website.masmforum.com/masm32/masmdl.htm
# Nota: Verificar enlaces actuales en sitios oficiales

# Crear estructura de directorios
mkdir masm
mkdir projects
```

**Paso 3: Configurar DOSBox**
```bash
# Editar archivo de configuración
nano ~/.dosbox/dosbox-0.74-3.conf

# Agregar al final del archivo:
[autoexec]
mount c ~/dos-tools
c:
cd masm
```

#### **🍎 Instalación en macOS:**

**Usando Homebrew:**
```bash
# Instalar Homebrew si no está disponible
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Instalar DOSBox
brew install dosbox

# Verificar instalación
dosbox --version
```

---

### 4. Verificación del Funcionamiento

#### **✅ Prueba Básica con EMU8086:**

**Programa de Prueba Simple:**
```assembly
; Programa: Hola Mundo en EMU8086
; Descripción: Mostrar mensaje en pantalla

name "hello"

org 100h

mov dx, offset msg
mov ah, 9
int 21h

mov ah, 4ch
int 21h

msg db 'Hola Mundo desde EMU8086!$'

end
```

**Pasos para Ejecutar:**
1. **Nuevo Archivo:** File → New
2. **Escribir Código:** Copiar el código anterior
3. **Compilar:** Clic en "Compile" o F9
4. **Emular:** Clic en "Emulate" o F10
5. **Ejecutar:** Clic en "Run" en el emulador
6. **Verificar Salida:** Debe aparecer "Hola Mundo desde EMU8086!"

#### **🔍 Verificación de Componentes:**

**Panel de Registros:**
```
Verificar que se muestren:
- AX, BX, CX, DX (registros de propósito general)
- SP, BP, SI, DI (registros de índice)
- CS, DS, ES, SS (registros de segmento)
- FLAGS (registro de banderas)
```

**Panel de Memoria:**
```
Verificar funcionalidades:
- Visualización hexadecimal
- Navegación por direcciones
- Modificación de valores
- Diferentes vistas (bytes, words)
```

**Panel de Código:**
```
Verificar características:
- Syntax highlighting funcional
- Numeración de líneas
- Breakpoints configurables
- Ejecución paso a paso
```

---

### 5. Pruebas Avanzadas

#### **🧪 Programa de Prueba Integral:**

```assembly
; Programa: Test Completo del Emulador
; Prueba: Registros, memoria, operaciones aritméticas

name "test_emulator"
org 100h

; Test 1: Operaciones básicas
mov ax, 1234h
mov bx, 5678h
add ax, bx
mov result1, ax

; Test 2: Operaciones con memoria
mov si, offset data_array
mov cx, 5
mov bx, 0

sum_loop:
    add bx, [si]
    inc si
    inc si
    loop sum_loop

mov result2, bx

; Test 3: Operaciones de cadenas
mov si, offset source_str
mov di, offset dest_str
mov cx, 10
cld
rep movsb

; Test 4: Llamada a subrutina
call test_procedure

; Terminar programa
mov ah, 4ch
int 21h

; Subrutina de prueba
test_procedure proc
    mov ax, 9999h
    ret
test_procedure endp

; Datos de prueba
data_array dw 10, 20, 30, 40, 50
source_str db "TEST12345", 0
dest_str db 10 dup(0)
result1 dw ?
result2 dw ?

end
```

#### **📊 Resultados Esperados:**

**Después de la ejecución:**
```
result1 = 68ACh (1234h + 5678h)
result2 = 96h (10 + 20 + 30 + 40 + 50 = 150 decimal = 96h)
dest_str = "TEST12345" (copiado desde source_str)
AX = 9999h (desde la subrutina)
```

---

### 6. Características del Emulador Verificadas

#### **🎮 Funcionalidades del Debugger:**

**Breakpoints:**
```
1. Colocar breakpoint: Clic en línea de código
2. Ejecutar hasta breakpoint: F5
3. Verificar estado de registros
4. Continuar ejecución: F5
5. Remover breakpoint: Clic nuevamente
```

**Ejecución Paso a Paso:**
```
- F7: Step Into (entrar en subrutinas)
- F8: Step Over (saltar subrutinas)
- F6: Ejecutar instrucción individual
- Ctrl+F2: Reset del programa
```

**Visualización de Memoria:**
```
- View → Memory: Ventana de memoria
- Navegación por direcciones
- Edición de valores en tiempo real
- Diferentes formatos de visualización
```

#### **🔧 Herramientas Adicionales:**

**Calculadora Integrada:**
```
Tools → Calculator
- Conversiones entre bases (hex, dec, bin)
- Operaciones aritméticas básicas
- Útil para verificar resultados
```

**Tabla ASCII:**
```
Help → ASCII Table
- Referencia completa de caracteres
- Códigos decimales y hexadecimales
- Caracteres especiales y de control
```

---

### 7. Documentación del Proceso

#### **📸 Capturas de Pantalla Requeridas:**

**Captura 1: Instalación Exitosa**
```
Elementos a incluir:
- Ventana principal de EMU8086
- Panel de registros visible
- Editor de código vacío
- Menús y barras de herramientas
```

**Captura 2: Código de Prueba**
```
Elementos a incluir:
- Código del programa "Hola Mundo" 
- Syntax highlighting funcionando
- Numeración de líneas
- Sin errores de compilación
```

**Captura 3: Ejecución Exitosa**
```
Elementos a incluir:
- Ventana del emulador ejecutándose
- Salida del programa visible
- Estado de registros actualizado
- Sin errores de ejecución
```

**Captura 4: Debugger en Acción**
```
Elementos a incluir:
- Programa pausado en breakpoint
- Registros con valores específicos
- Memoria visible y accesible
- Controles de debugging visibles
```

#### **📋 Template de Documentación:**

```markdown
# Instalación de Emulador 8086 - Reporte

## Información del Sistema
- **Sistema Operativo:** Windows 10 Pro 64-bit
- **RAM:** 8GB
- **Procesador:** Intel Core i5-8400
- **Espacio en disco:** 50GB disponible

## Emulador Seleccionado
- **Nombre:** EMU8086 v4.08
- **Fuente:** http://www.emu8086.com/
- **Tamaño:** 5.2 MB
- **Licencia:** Shareware

## Proceso de Instalación

### Paso 1: Descarga
- Tiempo de descarga: 2 minutos
- Archivo: emu8086v408r11.zip
- Hash verificado: [incluir hash si disponible]

### Paso 2: Instalación
- Método: Instalador setup.exe
- Directorio: C:\emu8086
- Tiempo de instalación: 3 minutos
- Problemas encontrados: Ninguno

### Paso 3: Configuración
- Configuración inicial completada
- Preferencias ajustadas según necesidades
- Atajos de teclado verificados

## Pruebas Realizadas

### Programa de Prueba 1: Hola Mundo
**Resultado:** ✅ Exitoso
**Salida:** "Hola Mundo desde EMU8086!"
**Tiempo de ejecución:** <1 segundo

### Programa de Prueba 2: Operaciones Aritméticas
**Resultado:** ✅ Exitoso
**Valores verificados:**
- Suma: 1234h + 5678h = 68ACh ✓
- Registros actualizados correctamente ✓

## Funcionalidades Verificadas
- [✅] Editor de código con syntax highlighting
- [✅] Compilador integrado
- [✅] Emulador paso a paso
- [✅] Debugger con breakpoints
- [✅] Visualización de registros
- [✅] Acceso a memoria
- [✅] Calculadora hexadecimal
- [✅] Tabla ASCII

## Problemas Encontrados
**Problema 1:** [Descripción si aplica]
**Solución:** [Cómo se resolvió]

**Problema 2:** [Descripción si aplica]
**Solución:** [Cómo se resolvió]

## Conclusiones
El emulador EMU8086 se instaló exitosamente y todas las funcionalidades básicas están operativas. La interfaz es intuitiva y permite el desarrollo y debugging efectivo de programas en lenguaje ensamblador 8086.

## Recomendaciones
- Crear backup de la configuración personal
- Explorar ejemplos incluidos en el software
- Practicar con programas de complejidad creciente
- Utilizar funcionalidad de debugging para aprendizaje
```

---

### 8. Troubleshooting Común

#### **❌ Problemas Frecuentes y Soluciones:**

**Error: "Cannot run on 64-bit Windows"**
```
Solución:
1. Ejecutar en modo compatibilidad Windows XP
2. Usar máquina virtual con Windows 32-bit
3. Alternativa: Usar DOSBox + MASM
```

**Error: "Access denied during installation"**
```
Solución:
1. Ejecutar instalador como Administrador
2. Desactivar temporalmente antivirus
3. Verificar permisos de escritura en C:\
```

**Error: "Program doesn't compile"**
```
Solución:
1. Verificar sintaxis del código
2. Revisar directivas del preprocesador
3. Consultar ejemplos incluidos
4. Verificar codificación de caracteres
```

**Error: "Emulator window doesn't open"**
```
Solución:
1. Verificar que compilación fue exitosa
2. Reiniciar EMU8086
3. Verificar requisitos del sistema
4. Actualizar drivers de video
```

#### **🔧 Optimizaciones Recomendadas:**

**Configuración de Rendimiento:**
```
- Ajustar velocidad de emulación
- Configurar auto-save frequency
- Optimizar tamaño de buffer de memoria
- Ajustar nivel de detail en debugging
```

**Personalización del Entorno:**
```
- Configurar shortcuts personalizados
- Ajustar colores del syntax highlighting
- Configurar templates de código
- Establecer directorio de trabajo predeterminado
```

---

### 9. Alternativas por Plataforma

#### **🐧 Linux: DOSBox + NASM**

**Instalación completa:**
```bash
# Instalar dependencias
sudo apt install dosbox nasm build-essential

# Configurar entorno
mkdir ~/asm-dev
cd ~/asm-dev

# Crear script de ensamblado
cat << 'EOF' > compile.sh
#!/bin/bash
nasm -f bin $1.asm -o $1.com
dosbox -c "mount c ." -c "c:" -c "$1.com" -c "exit"
EOF

chmod +x compile.sh
```

#### **🍎 macOS: Docker Solution**

**Dockerfile para entorno 8086:**
```dockerfile
FROM ubuntu:20.04

RUN apt-get update && apt-get install -y \
    dosbox \
    nasm \
    vim \
    wget

WORKDIR /asm

COPY . .

CMD ["dosbox"]
```

**Uso:**
```bash
docker build -t asm8086 .
docker run -it -v $(pwd):/asm asm8086
```

---

### 10. Recursos Adicionales

#### **📚 Recursos de Aprendizaje:**

**Tutoriales Online:**
- https://www.tutorialspoint.com/assembly_programming/
- https://en.wikibooks.org/wiki/X86_Assembly
- https://www.cs.virginia.edu/~evans/cs216/guides/x86.html

**Documentación Oficial:**
- Intel 8086 Family User's Manual
- Microsoft MASM Reference
- EMU8086 Help Documentation

**Libros Recomendados:**
- "Assembly Language for x86 Processors" - Kip Irvine
- "The Art of Assembly Language Programming" - Randy Hyde
- "Programming from the Ground Up" - Jonathan Bartlett

#### **🛠️ Herramientas Complementarias:**

**Hex Editors:**
- HxD (Windows)
- GHex (Linux)
- Hex Fiend (macOS)

**Disassemblers:**
- IDA Free
- Ghidra (NSA)
- Radare2

---

## 📋 Entregables

### **📄 Documento Final Requerido:**

**Estructura:**
1. **Información del sistema utilizado**
2. **Emulador seleccionado y justificación**
3. **Proceso de instalación paso a paso**
4. **Capturas de pantalla del proceso**
5. **Programa de prueba implementado**
6. **Resultados de las verificaciones**
7. **Problemas encontrados y soluciones**
8. **Conclusiones y recomendaciones**

**Evidencia Visual:**
- Screenshot de descarga/instalación
- Captura del emulador funcionando
- Código de prueba ejecutándose
- Resultados visibles en pantalla

---

## 📋 Notas de Evaluación

**Criterios Cumplidos:**
- ✅ Emulador 8086 instalado correctamente
- ✅ Funcionalidad básica verificada
- ✅ Programa de prueba ejecutado exitosamente
- ✅ Documentación clara y completa del proceso
- ✅ Evidencia visual del funcionamiento
- ✅ Problemas y soluciones documentados

**Calificación Esperada:** 9-10 puntos (Excelente)

**Criterios de Excelencia Adicionales:**
- Comparación entre múltiples emuladores
- Configuración avanzada del entorno
- Programas de prueba más complejos
- Scripts de automatización
- Análisis de rendimiento del emulador
