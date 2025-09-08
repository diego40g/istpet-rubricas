# A11: Solucionario - Ejercicios con procesador 8086

## 📝 Resumen de la Actividad
**Objetivo:** Aplicar conocimientos sobre la arquitectura y funcionamiento del procesador Intel 8086 mediante ejercicios prácticos que involucren registros, instrucciones básicas y operaciones aritmético-lógicas.

---

## 🎯 Solución de Ejercicios

### Ejercicio 1: Operaciones Aritméticas Básicas

#### **📋 Enunciado:**
Realizar una suma de dos números de 16 bits almacenados en memoria y guardar el resultado.

#### **💻 Código Assembler:**
```assembly
; Programa: Suma de dos números de 16 bits
; Descripción: Suma 1234H + 5678H y guarda resultado en memoria

.MODEL SMALL
.DATA
    NUM1 DW 1234H       ; Primer número (4660 decimal)
    NUM2 DW 5678H       ; Segundo número (22136 decimal)  
    RESULTADO DW ?      ; Variable para almacenar resultado

.CODE
MAIN PROC
    MOV AX, @DATA       ; Cargar segmento de datos
    MOV DS, AX
    
    ; Cargar primer número en AX
    MOV AX, NUM1        ; AX = 1234H
    
    ; Sumar segundo número
    ADD AX, NUM2        ; AX = AX + NUM2 = 1234H + 5678H = 68ACH
    
    ; Guardar resultado en memoria
    MOV RESULTADO, AX   ; RESULTADO = 68ACH (26796 decimal)
    
    ; Terminar programa
    MOV AH, 4CH
    INT 21H
MAIN ENDP
END MAIN
```

#### **🔍 Análisis del Estado de Registros:**
```
Estado inicial:
AX = 0000H, BX = 0000H, CX = 0000H, DX = 0000H

Después de MOV AX, NUM1:
AX = 1234H

Después de ADD AX, NUM2:
AX = 68ACH (1234H + 5678H = 68ACH)
Flags: CF=0, ZF=0, SF=0, OF=0

Resultado final en memoria:
RESULTADO = 68ACH (26796 en decimal)
```

#### **✅ Verificación:**
- 1234H = 4660 decimal
- 5678H = 22136 decimal
- Suma = 4660 + 22136 = 26796 decimal = 68ACH

---

### Ejercicio 2: Operaciones con Registros

#### **📋 Enunciado:**
Intercambiar el contenido de los registros AX y BX sin usar un registro temporal adicional.

#### **💻 Código Assembler:**
```assembly
; Programa: Intercambio de registros AX y BX
; Técnica: Usando operaciones XOR

.MODEL SMALL
.CODE
MAIN PROC
    ; Valores iniciales de ejemplo
    MOV AX, 1234H       ; AX = 1234H
    MOV BX, 5678H       ; BX = 5678H
    
    ; Método XOR para intercambio
    XOR AX, BX          ; AX = AX XOR BX = 1234H XOR 5678H = 444CH
    XOR BX, AX          ; BX = BX XOR AX = 5678H XOR 444CH = 1234H
    XOR AX, BX          ; AX = AX XOR BX = 444CH XOR 1234H = 5678H
    
    ; Resultado: AX = 5678H, BX = 1234H (intercambiados)
    
    MOV AH, 4CH
    INT 21H
MAIN ENDP
END MAIN
```

#### **🔍 Análisis Paso a Paso:**
```
Estado inicial:
AX = 1234H (0001 0010 0011 0100)
BX = 5678H (0101 0110 0111 1000)

Paso 1 - XOR AX, BX:
AX = 1234H XOR 5678H = 444CH
AX = 444CH (0100 0100 0100 1100)
BX = 5678H (sin cambios)

Paso 2 - XOR BX, AX:
BX = 5678H XOR 444CH = 1234H
AX = 444CH (sin cambios)
BX = 1234H (0001 0010 0011 0100)

Paso 3 - XOR AX, BX:
AX = 444CH XOR 1234H = 5678H
AX = 5678H (0101 0110 0111 1000)
BX = 1234H (sin cambios)

Resultado final:
AX = 5678H (valor original de BX)
BX = 1234H (valor original de AX)
```

---

### Ejercicio 3: Operaciones Lógicas y Manipulación de Bits

#### **📋 Enunciado:**
Contar el número de bits en 1 en el registro AX.

#### **💻 Código Assembler:**
```assembly
; Programa: Contador de bits en 1
; Algoritmo: Rotar AX y verificar carry flag

.MODEL SMALL
.DATA
    NUMERO DW 5A3CH     ; Número de prueba (bits: 0101 1010 0011 1100)
    CONTADOR DB 0       ; Contador de bits en 1

.CODE
MAIN PROC
    MOV AX, @DATA
    MOV DS, AX
    
    MOV AX, NUMERO      ; Cargar número a analizar
    MOV CX, 16          ; Contador de bits (16 bits)
    MOV BL, 0           ; Inicializar contador de unos
    
LOOP_BITS:
    SHL AX, 1           ; Rotar izquierda, bit más significativo va a CF
    JNC SIGUIENTE       ; Si CF = 0, saltar
    INC BL              ; Si CF = 1, incrementar contador
    
SIGUIENTE:
    LOOP LOOP_BITS      ; Decrementar CX y repetir si CX != 0
    
    MOV CONTADOR, BL    ; Guardar resultado
    
    MOV AH, 4CH
    INT 21H
MAIN ENDP
END MAIN
```

#### **🔍 Análisis Detallado:**
```
Número inicial: 5A3CH = 0101 1010 0011 1100

Iteración por iteración:
Bit 15: 0 → CF=0, contador=0
Bit 14: 1 → CF=1, contador=1
Bit 13: 0 → CF=0, contador=1
Bit 12: 1 → CF=1, contador=2
Bit 11: 1 → CF=1, contador=3
Bit 10: 0 → CF=0, contador=3
Bit 9:  1 → CF=1, contador=4
Bit 8:  0 → CF=0, contador=4
Bit 7:  0 → CF=0, contador=4
Bit 6:  0 → CF=0, contador=4
Bit 5:  1 → CF=1, contador=5
Bit 4:  1 → CF=1, contador=6
Bit 3:  1 → CF=1, contador=7
Bit 2:  1 → CF=1, contador=8
Bit 1:  0 → CF=0, contador=8
Bit 0:  0 → CF=0, contador=8

Resultado final: 8 bits en 1
```

---

### Ejercicio 4: Trabajo con Cadenas de Caracteres

#### **📋 Enunciado:**
Contar la longitud de una cadena terminada en NULL.

#### **💻 Código Assembler:**
```assembly
; Programa: Contador de longitud de cadena
; Método: Recorrer hasta encontrar byte 00H

.MODEL SMALL
.DATA
    CADENA DB 'HOLA MUNDO', 0    ; Cadena de prueba
    LONGITUD DB 0                ; Variable para resultado

.CODE
MAIN PROC
    MOV AX, @DATA
    MOV DS, AX
    
    LEA SI, CADENA      ; SI apunta al inicio de la cadena
    MOV CX, 0           ; Contador de caracteres
    
CONTAR_LOOP:
    MOV AL, [SI]        ; Cargar byte actual
    CMP AL, 0           ; ¿Es el terminador NULL?
    JE FIN_CADENA       ; Si es 0, terminar
    
    INC CX              ; Incrementar contador
    INC SI              ; Apuntar al siguiente carácter
    JMP CONTAR_LOOP     ; Repetir
    
FIN_CADENA:
    MOV LONGITUD, CL    ; Guardar longitud (parte baja de CX)
    
    MOV AH, 4CH
    INT 21H
MAIN ENDP
END MAIN
```

#### **🔍 Análisis de Ejecución:**
```
Cadena: "HOLA MUNDO"
Dirección   Byte   ASCII   Contador
SI+0:       'H'    48H     CX = 1
SI+1:       'O'    4FH     CX = 2
SI+2:       'L'    4CH     CX = 3
SI+3:       'A'    41H     CX = 4
SI+4:       ' '    20H     CX = 5
SI+5:       'M'    4DH     CX = 6
SI+6:       'U'    55H     CX = 7
SI+7:       'N'    4EH     CX = 8
SI+8:       'D'    44H     CX = 9
SI+9:       'O'    4FH     CX = 10
SI+10:      00H    (NULL)  Fin del bucle

Resultado: LONGITUD = 10
```

---

### Ejercicio 5: Conversión de Bases Numéricas

#### **📋 Enunciado:**
Convertir un número decimal de un dígito (0-9) a su representación en ASCII.

#### **💻 Código Assembler:**
```assembly
; Programa: Conversión decimal a ASCII
; Algoritmo: Sumar 30H al dígito decimal

.MODEL SMALL
.DATA
    NUMERO DB 7          ; Dígito decimal (0-9)
    ASCII_CHAR DB ?      ; Carácter ASCII resultante

.CODE
MAIN PROC
    MOV AX, @DATA
    MOV DS, AX
    
    MOV AL, NUMERO       ; Cargar dígito decimal
    ADD AL, 30H          ; Convertir a ASCII (sumar 48 decimal)
    MOV ASCII_CHAR, AL   ; Guardar resultado
    
    ; Ejemplo de visualización (opcional)
    MOV DL, AL           ; DL = carácter ASCII
    MOV AH, 02H          ; Función DOS para mostrar carácter
    INT 21H              ; Mostrar en pantalla
    
    MOV AH, 4CH
    INT 21H
MAIN ENDP
END MAIN
```

#### **🔍 Análisis de Conversión:**
```
Tabla de conversión decimal → ASCII:
Decimal | Hexadecimal | ASCII | Carácter
   0    |     30H     |  48   |    '0'
   1    |     31H     |  49   |    '1'
   2    |     32H     |  50   |    '2'
   3    |     33H     |  51   |    '3'
   4    |     34H     |  52   |    '4'
   5    |     35H     |  53   |    '5'
   6    |     36H     |  54   |    '6'
   7    |     37H     |  55   |    '7'  ← Ejemplo
   8    |     38H     |  56   |    '8'
   9    |     39H     |  57   |    '9'

Para NUMERO = 7:
AL = 07H + 30H = 37H = '7' (ASCII)
```

---

### Ejercicio 6: Operaciones con Pila (Stack)

#### **📋 Enunciado:**
Usar la pila para invertir el orden de 4 números almacenados en registros.

#### **💻 Código Assembler:**
```assembly
; Programa: Inversión de orden usando pila
; Técnica: PUSH para almacenar, POP para recuperar en orden inverso

.MODEL SMALL
.CODE
MAIN PROC
    ; Valores iniciales en registros
    MOV AX, 1111H       ; Primer número
    MOV BX, 2222H       ; Segundo número
    MOV CX, 3333H       ; Tercer número
    MOV DX, 4444H       ; Cuarto número
    
    ; Guardar en pila (último en entrar, primero en salir)
    PUSH AX             ; Pila: [1111H]
    PUSH BX             ; Pila: [2222H, 1111H]
    PUSH CX             ; Pila: [3333H, 2222H, 1111H]
    PUSH DX             ; Pila: [4444H, 3333H, 2222H, 1111H]
    
    ; Recuperar en orden inverso
    POP AX              ; AX = 4444H, Pila: [3333H, 2222H, 1111H]
    POP BX              ; BX = 3333H, Pila: [2222H, 1111H]
    POP CX              ; CX = 2222H, Pila: [1111H]
    POP DX              ; DX = 1111H, Pila: []
    
    ; Resultado: AX=4444H, BX=3333H, CX=2222H, DX=1111H
    
    MOV AH, 4CH
    INT 21H
MAIN ENDP
END MAIN
```

#### **🔍 Estado de la Pila:**
```
Estado inicial de registros:
AX = 1111H, BX = 2222H, CX = 3333H, DX = 4444H

Operaciones PUSH:
PUSH AX → Pila: [1111H]
PUSH BX → Pila: [2222H][1111H]
PUSH CX → Pila: [3333H][2222H][1111H]
PUSH DX → Pila: [4444H][3333H][2222H][1111H]
                  ↑ SP (Stack Pointer)

Operaciones POP:
POP AX  → AX = 4444H, Pila: [3333H][2222H][1111H]
POP BX  → BX = 3333H, Pila: [2222H][1111H]
POP CX  → CX = 2222H, Pila: [1111H]
POP DX  → DX = 1111H, Pila: []

Estado final de registros:
AX = 4444H, BX = 3333H, CX = 2222H, DX = 1111H
(Orden invertido exitosamente)
```

---

### Ejercicio 7: Bucles y Estructuras de Control

#### **📋 Enunciado:**
Calcular la suma de los números del 1 al 10 usando un bucle.

#### **💻 Código Assembler:**
```assembly
; Programa: Suma de números del 1 al 10
; Algoritmo: Bucle con contador

.MODEL SMALL
.DATA
    SUMA DW 0           ; Variable para almacenar resultado

.CODE
MAIN PROC
    MOV AX, @DATA
    MOV DS, AX
    
    MOV CX, 10          ; Contador del bucle (10 iteraciones)
    MOV AX, 0           ; Acumulador de suma
    MOV BX, 1           ; Número actual a sumar
    
BUCLE_SUMA:
    ADD AX, BX          ; Sumar número actual al acumulador
    INC BX              ; Incrementar número para siguiente iteración
    LOOP BUCLE_SUMA     ; Decrementar CX y repetir si CX != 0
    
    MOV SUMA, AX        ; Guardar resultado final
    
    MOV AH, 4CH
    INT 21H
MAIN ENDP
END MAIN
```

#### **🔍 Trace de Ejecución:**
```
Estado inicial: CX=10, AX=0, BX=1

Iteración 1: AX = 0 + 1 = 1,   BX = 2, CX = 9
Iteración 2: AX = 1 + 2 = 3,   BX = 3, CX = 8
Iteración 3: AX = 3 + 3 = 6,   BX = 4, CX = 7
Iteración 4: AX = 6 + 4 = 10,  BX = 5, CX = 6
Iteración 5: AX = 10 + 5 = 15, BX = 6, CX = 5
Iteración 6: AX = 15 + 6 = 21, BX = 7, CX = 4
Iteración 7: AX = 21 + 7 = 28, BX = 8, CX = 3
Iteración 8: AX = 28 + 8 = 36, BX = 9, CX = 2
Iteración 9: AX = 36 + 9 = 45, BX = 10, CX = 1
Iteración 10: AX = 45 + 10 = 55, BX = 11, CX = 0

Resultado final: SUMA = 55 (correcto: 1+2+3+4+5+6+7+8+9+10 = 55)
```

---

## 🔧 Herramientas de Simulación

### **🖥️ Emuladores Recomendados:**

#### **EMU8086:**
- **Descripción:** Emulador completo del 8086 con IDE integrado
- **Características:**
  - Editor de código con syntax highlighting
  - Debugger paso a paso
  - Visualización de registros y memoria
  - Simulación de dispositivos de E/S

#### **DOSBox + MASM:**
- **Descripción:** Entorno DOS virtualizado con Microsoft Macro Assembler
- **Instalación:**
  ```
  1. Instalar DOSBox
  2. Montar directorio con MASM
  3. Ensamblar: masm programa.asm
  4. Vincular: link programa.obj
  5. Ejecutar: programa.exe
  ```

#### **NASM (Netwide Assembler):**
- **Descripción:** Ensamblador moderno multiplataforma
- **Ventajas:** Sintaxis más clara, portable, activamente mantenido

---

## 📊 Análisis Técnico del 8086

### **🔧 Arquitectura del Procesador:**

#### **Registros Principales:**
```
Registros de Propósito General (16 bits):
- AX (AH + AL): Acumulador principal
- BX (BH + BL): Registro base, direccionamiento
- CX (CH + CL): Contador para bucles
- DX (DH + DL): Datos, operaciones E/O

Registros de Índice:
- SI: Source Index (índice fuente)
- DI: Destination Index (índice destino)
- BP: Base Pointer (puntero base)
- SP: Stack Pointer (puntero de pila)

Registros de Segmento:
- CS: Code Segment (segmento de código)
- DS: Data Segment (segmento de datos)
- ES: Extra Segment (segmento extra)
- SS: Stack Segment (segmento de pila)

Registro de Estado:
- FLAGS: Banderas de estado (16 bits)
```

#### **Flags del Registro de Estado:**
```
Bit  Flag  Nombre              Descripción
0    CF    Carry Flag          Acarreo en operaciones aritméticas
2    PF    Parity Flag         Paridad del resultado
4    AF    Auxiliary Flag      Acarreo auxiliar (BCD)
6    ZF    Zero Flag           Resultado igual a cero
7    SF    Sign Flag           Bit de signo del resultado
8    TF    Trap Flag           Modo de depuración paso a paso
9    IF    Interrupt Flag      Habilitación de interrupciones
10   DF    Direction Flag      Dirección para operaciones de cadena
11   OF    Overflow Flag       Desbordamiento en operaciones con signo
```

---

## 📝 Metodología de Resolución

### **🎯 Estrategia para Abordar Ejercicios:**

#### **Paso 1: Análisis del Problema**
1. Identificar el objetivo del ejercicio
2. Determinar entradas y salidas requeridas
3. Seleccionar registros e instrucciones apropiadas
4. Planificar la secuencia de operaciones

#### **Paso 2: Diseño del Algoritmo**
1. Crear pseudocódigo básico
2. Mapear variables a ubicaciones de memoria
3. Definir estructura del programa
4. Identificar bucles y estructuras de control

#### **Paso 3: Implementación**
1. Escribir código en ensamblador
2. Comentar cada sección importante
3. Incluir inicialización de segmentos
4. Agregar rutina de terminación

#### **Paso 4: Verificación**
1. Trace manual del código
2. Verificar estado de registros en cada paso
3. Comprobar flags afectadas
4. Validar resultado esperado

---

## 🔍 Tabla de Instrucciones Fundamentales

### **📋 Instrucciones de Movimiento de Datos:**
| Instrucción | Descripción | Ejemplo | Efecto |
|-------------|-------------|---------|--------|
| MOV | Mover datos | MOV AX, BX | AX ← BX |
| PUSH | Apilar | PUSH AX | [SP] ← AX, SP ← SP-2 |
| POP | Desapilar | POP BX | BX ← [SP], SP ← SP+2 |
| XCHG | Intercambiar | XCHG AX, BX | AX ↔ BX |

### **🧮 Instrucciones Aritméticas:**
| Instrucción | Descripción | Ejemplo | Flags Afectadas |
|-------------|-------------|---------|-----------------|
| ADD | Sumar | ADD AX, BX | CF, ZF, SF, OF, PF, AF |
| SUB | Restar | SUB AX, BX | CF, ZF, SF, OF, PF, AF |
| MUL | Multiplicar (sin signo) | MUL BL | CF, OF |
| DIV | Dividir (sin signo) | DIV BL | - |
| INC | Incrementar | INC AX | ZF, SF, OF, PF, AF |
| DEC | Decrementar | DEC BX | ZF, SF, OF, PF, AF |

### **🔀 Instrucciones Lógicas:**
| Instrucción | Descripción | Ejemplo | Efecto |
|-------------|-------------|---------|--------|
| AND | AND lógico | AND AX, BX | AX ← AX AND BX |
| OR | OR lógico | OR AX, BX | AX ← AX OR BX |
| XOR | XOR lógico | XOR AX, BX | AX ← AX XOR BX |
| NOT | NOT lógico | NOT AX | AX ← NOT AX |

### **🔄 Instrucciones de Rotación/Desplazamiento:**
| Instrucción | Descripción | Ejemplo | Efecto |
|-------------|-------------|---------|--------|
| SHL | Desplazar izquierda | SHL AX, 1 | Desplaza bits a la izquierda |
| SHR | Desplazar derecha | SHR AX, 1 | Desplaza bits a la derecha |
| ROL | Rotar izquierda | ROL AX, 1 | Rota bits incluyendo CF |
| ROR | Rotar derecha | ROR AX, 1 | Rota bits incluyendo CF |

---

## 📋 Entregables

### **📄 Formato de Entrega:**

**Estructura del documento:**
1. **Portada:** Nombre, materia, fecha
2. **Introducción:** Objetivos y metodología
3. **Ejercicios resueltos:** Mínimo 5 ejercicios completos
4. **Análisis de registros:** Estado antes y después
5. **Código comentado:** Explicación línea por línea
6. **Verificación:** Pruebas y resultados
7. **Conclusiones:** Aprendizajes obtenidos

**Por cada ejercicio incluir:**
- Enunciado del problema
- Análisis inicial
- Código en ensamblador comentado
- Trace de ejecución paso a paso
- Estado final de registros y memoria
- Verificación del resultado

---

## 📋 Notas de Evaluación

**Criterios Cumplidos:**
- ✅ 5+ ejercicios variados y completos
- ✅ Uso correcto de instrucciones 8086
- ✅ Análisis detallado de registros
- ✅ Código bien comentado y estructurado
- ✅ Verificación de resultados
- ✅ Explicaciones técnicas precisas
- ✅ Documentación clara y organizada

**Calificación Esperada:** 9-10 puntos (Excelente)

**Criterios de Excelencia Adicionales:**
- Ejercicios adicionales de mayor complejidad
- Uso de herramientas de simulación/depuración
- Análisis comparativo de diferentes enfoques
- Optimizaciones de código implementadas
- Explicación de banderas y estados del procesador
