# A20: Solucionario - Configurar Wokwi

## 📝 Resumen de la Actividad
**Objetivo:** Aprender a configurar y usar la plataforma Wokwi para simulación de circuitos electrónicos y microcontroladores de forma virtual.

---

## 🎯 Solución Paso a Paso

### 1. Acceso y Registro en Wokwi

#### **Paso 1: Acceder a la Plataforma**
1. Abrir navegador web (Chrome, Firefox, Edge)
2. Navegar a: https://wokwi.com
3. La plataforma es 100% online, sin necesidad de instalación

#### **Paso 2: Crear Cuenta**
- **Opción A:** Registro con Google Account
  - Clic en "Sign in with Google"
  - Autorizar permisos de Wokwi
  - Acceso inmediato al dashboard

- **Opción B:** Registro con Email
  - Clic en "Sign Up"
  - Ingresar email y contraseña
  - Verificar email de confirmación
  - Completar perfil básico

#### **Paso 3: Verificar Acceso**
- Dashboard principal debe mostrar:
  - Proyectos recientes (vacío inicialmente)
  - Templates disponibles
  - Opción "New Project"

---

### 2. Configuración del Entorno

#### **🔧 Configuración Inicial**

**Seleccionar Microcontrolador:**
1. Clic en "New Project"
2. Seleccionar **Arduino Uno** (recomendado para principiantes)
3. Confirmar creación del proyecto

**Interfaz Principal - Elementos:**
- **Simulator Canvas:** Área de diseño del circuito
- **Code Editor:** Editor de código Arduino IDE integrado
- **Components Library:** Biblioteca de componentes
- **Control Panel:** Play/Stop/Reset de simulación

**Configuraciones Recomendadas:**
- **Theme:** Dark (opcional, más cómodo para la vista)
- **Auto-save:** Habilitado por defecto
- **Simulation Speed:** Real-time (velocidad real)

---

### 3. Circuito de Ejemplo - LED Parpadeante

#### **📋 Componentes Necesarios:**
- 1x Arduino Uno (pre-cargado)
- 1x LED (5mm)
- 1x Resistencia 220Ω
- Cables de conexión (wires)

#### **🔌 Diseño del Circuito:**

**Conexiones:**
1. **LED Ánodo (+)** → Pin Digital 13 (Arduino)
2. **LED Cátodo (-)** → Resistencia 220Ω
3. **Resistencia** → GND (Arduino)

**Diagrama Esquemático:**
```
Arduino Pin 13 ----[LED]----[220Ω]---- GND
                    +    -    
```

#### **⚙️ Pasos para Armar el Circuito:**

1. **Agregar LED:**
   - Buscar "LED" en Components Library
   - Arrastrar al canvas
   - Posicionar cerca del Arduino

2. **Agregar Resistencia:**
   - Buscar "Resistor" en Components
   - Seleccionar 220Ω (rojo-rojo-marrón)
   - Colocar en serie con el LED

3. **Realizar Conexiones:**
   - Clic en pin 13 del Arduino
   - Arrastrar cable al ánodo del LED (pata larga)
   - Conectar cátodo del LED a resistencia
   - Conectar resistencia a GND del Arduino

4. **Verificar Conexiones:**
   - LED: ánodo a pin 13, cátodo a resistencia
   - Resistencia: del cátodo del LED a GND
   - Sin conexiones flotantes

---

### 4. Programación del Microcontrolador

#### **💻 Código Arduino - LED Parpadeante:**

```cpp
/*
  Proyecto: LED Parpadeante
  Descripción: Hace parpadear un LED conectado al pin 13
  Autor: [Tu nombre]
  Fecha: [Fecha actual]
*/

// Definir el pin donde está conectado el LED
const int LED_PIN = 13;

// Función setup - se ejecuta una sola vez al inicio
void setup() {
  // Configurar el pin 13 como salida
  pinMode(LED_PIN, OUTPUT);
  
  // Inicializar comunicación serie (opcional, para debug)
  Serial.begin(9600);
  Serial.println("Sistema iniciado - LED Parpadeante");
}

// Función loop - se ejecuta continuamente
void loop() {
  // Encender LED
  digitalWrite(LED_PIN, HIGH);
  Serial.println("LED: ON");
  
  // Esperar 1 segundo (1000 milisegundos)
  delay(1000);
  
  // Apagar LED
  digitalWrite(LED_PIN, LOW);
  Serial.println("LED: OFF");
  
  // Esperar 1 segundo
  delay(1000);
}
```

#### **📝 Explicación del Código:**

**Declaraciones:**
- `const int LED_PIN = 13`: Define el pin donde está conectado el LED
- `pinMode(LED_PIN, OUTPUT)`: Configura el pin como salida
- `Serial.begin(9600)`: Inicia comunicación serie para monitoreo

**Lógica Principal:**
- `digitalWrite(LED_PIN, HIGH)`: Envía 5V al pin (LED ON)
- `delay(1000)`: Pausa la ejecución por 1000ms (1 segundo)
- `digitalWrite(LED_PIN, LOW)`: Envía 0V al pin (LED OFF)
- `loop()`: Se repite infinitamente

---

### 5. Simulación y Pruebas

#### **▶️ Ejecutar la Simulación:**

1. **Verificar Código:**
   - Copiar el código en el editor
   - Verificar sintaxis (no debe haber errores rojos)

2. **Iniciar Simulación:**
   - Clic en botón "Play" (▶️)
   - Observar compilación del código
   - Verificar que no hay errores

3. **Monitoreo de Resultados:**
   - **Visual:** LED debe parpadear cada segundo
   - **Serie:** Abrir Serial Monitor para ver mensajes
   - **Timing:** Verificar que los intervalos son correctos

#### **🔍 Verificaciones de Funcionamiento:**

**Checklist de Pruebas:**
- ✅ LED enciende y apaga correctamente
- ✅ Intervalo de 1 segundo entre cambios
- ✅ Sin errores en consola serie
- ✅ Circuito visualmente correcto
- ✅ Simulación fluida sin trabas

**Posibles Problemas y Soluciones:**
| Problema | Causa Probable | Solución |
|----------|---------------|----------|
| LED no enciende | Conexión incorrecta | Verificar polaridad del LED |
| LED muy débil | Sin resistencia | Agregar resistencia 220Ω |
| Error de compilación | Sintaxis incorrecta | Revisar código, cerrar llaves |
| Simulación lenta | Navegador sobrecargado | Cerrar otras pestañas |

---

### 6. Circuito Adicional - Semáforo Básico

#### **🚦 Proyecto Avanzado (Opcional):**

**Componentes:**
- 1x Arduino Uno
- 3x LEDs (Rojo, Amarillo, Verde)
- 3x Resistencias 220Ω
- Cables de conexión

**Conexiones:**
- LED Rojo → Pin 11
- LED Amarillo → Pin 12  
- LED Verde → Pin 13
- Todos los cátodos → GND (a través de resistencias)

**Código del Semáforo:**
```cpp
// Pines de los LEDs
const int LED_ROJO = 11;
const int LED_AMARILLO = 12;
const int LED_VERDE = 13;

void setup() {
  pinMode(LED_ROJO, OUTPUT);
  pinMode(LED_AMARILLO, OUTPUT);
  pinMode(LED_VERDE, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  // Verde - 5 segundos
  digitalWrite(LED_VERDE, HIGH);
  Serial.println("VERDE - Siga");
  delay(5000);
  digitalWrite(LED_VERDE, LOW);
  
  // Amarillo - 2 segundos
  digitalWrite(LED_AMARILLO, HIGH);
  Serial.println("AMARILLO - Precaución");
  delay(2000);
  digitalWrite(LED_AMARILLO, LOW);
  
  // Rojo - 5 segundos
  digitalWrite(LED_ROJO, HIGH);
  Serial.println("ROJO - Alto");
  delay(5000);
  digitalWrite(LED_ROJO, LOW);
}
```

---

### 7. Documentación del Proceso

#### **📄 Reporte de Configuración:**

**1. Proceso de Instalación:**
- ✅ Acceso exitoso a https://wokwi.com
- ✅ Cuenta creada con [email/Google]
- ✅ Dashboard configurado correctamente
- ✅ Primer proyecto creado sin errores

**2. Configuración del Entorno:**
- **Microcontrolador seleccionado:** Arduino Uno
- **Tema de interfaz:** [Dark/Light]
- **Velocidad de simulación:** Tiempo real
- **Auto-guardado:** Habilitado

**3. Circuito Implementado:**
- **Tipo:** LED Parpadeante básico
- **Componentes:** Arduino Uno, LED, Resistencia 220Ω
- **Funcionamiento:** ✅ Correcto
- **Frecuencia:** 1Hz (1 segundo ON, 1 segundo OFF)

**4. Código Desarrollado:**
- **Lenguaje:** C++ (Arduino IDE)
- **Líneas de código:** ~25 líneas
- **Funcionalidades:** Control de LED, comunicación serie
- **Compilación:** ✅ Sin errores

---

### 8. Capturas de Pantalla Requeridas

#### **📸 Evidencia Visual:**

1. **Dashboard de Wokwi:**
   - Pantalla principal con proyectos
   - Usuario logueado visible

2. **Editor de Circuitos:**
   - Circuito armado completo
   - Componentes correctamente conectados
   - Labels de conexiones visibles

3. **Código en Editor:**
   - Código completo visible
   - Sin errores de sintaxis
   - Comentarios incluidos

4. **Simulación en Funcionamiento:**
   - LED encendido (captura 1)
   - LED apagado (captura 2)
   - Serial Monitor con mensajes

5. **Monitor Serie:**
   - Mensajes "LED: ON" y "LED: OFF"
   - Timestamps de ejecución
   - Comunicación estable

---

### 9. Análisis Técnico

#### **⚙️ Funcionamiento del Circuito:**

**Principios Eléctricos:**
- **Ley de Ohm:** V = I × R
- **Corriente del LED:** I = (5V - 2V) / 220Ω ≈ 13.6mA
- **Potencia disipada:** P = I² × R ≈ 0.04W

**Funcionamiento del Arduino:**
- **Voltaje de salida digital:** 0V (LOW) o 5V (HIGH)
- **Corriente máxima por pin:** 40mA (suficiente para el LED)
- **Frecuencia de trabajo:** 16MHz (cristal interno)

**Protocolo de Comunicación:**
- **Serie:** UART a 9600 baudios
- **Formato:** 8 bits de datos, sin paridad, 1 bit de stop
- **Buffer:** 64 bytes (suficiente para mensajes cortos)

---

### 10. Ventajas de Wokwi

#### **✅ Beneficios de la Plataforma:**

**Técnicas:**
- Simulación en tiempo real precisa
- Amplia biblioteca de componentes
- Editor de código integrado con sintaxis highlighting
- Debugging visual de circuitos

**Educativas:**
- Sin necesidad de hardware físico
- Experimentación segura (sin riesgo de dañar componentes)
- Acceso desde cualquier dispositivo con navegador
- Proyectos compartibles fácilmente

**Económicas:**
- Plataforma gratuita para uso básico
- No requiere compra de componentes iniciales
- Reducción de costos de laboratorio
- Accesible para estudiantes sin recursos

---

## 📋 Entregables

### **📄 Documento Final:**

**Estructura Recomendada:**
1. **Portada:** Título, nombre, fecha
2. **Introducción:** Objetivos y metodología
3. **Proceso de Configuración:** Pasos detallados
4. **Circuito Implementado:** Esquema y conexiones
5. **Código Desarrollado:** Listado completo comentado
6. **Resultados:** Capturas y análisis
7. **Conclusiones:** Aprendizajes y recomendaciones
8. **Anexos:** Capturas adicionales

**Formato:** PDF con máximo 10 páginas

---

## 📋 Notas de Evaluación

**Criterios Cumplidos:**
- ✅ Acceso y configuración correcta de Wokwi
- ✅ Circuito con mínimo 2 componentes implementado
- ✅ Código funcional y bien documentado
- ✅ Evidencia visual del funcionamiento
- ✅ Documentación clara del proceso
- ✅ Análisis técnico fundamentado

**Calificación Esperada:** 9-10 puntos (Excelente)

**Criterios de Excelencia Adicionales:**
- Implementación de circuito adicional (semáforo)
- Uso de comunicación serie para debugging
- Análisis eléctrico detallado
- Documentación profesional con diagramas
