# Laboratorio de Electrónica Digital
## Lab 06 — Caja Fuerte Digital: FSM con Cerrojo de Servomotor en FPGA
**ECCI · Tecnicas Digitales**

---

## 1. Objetivos

Al finalizar este laboratorio, el estudiante será capaz de:

- Diseñar una Máquina de Estado Finito (FSM) tipo Moore controlada por **entradas del usuario** (no por tiempo).
- Integrar lógica combinacional y secuencial en una solución completa: FSM de control, comparación de una combinación, conteo de intentos y actuación física.
- Generar una señal **PWM** para controlar la posición de un **servomotor** como cerrojo.
- Mostrar mensajes y realimentación en los displays de 7 segmentos.
- Sintetizar, programar y demostrar el sistema sobre la tarjeta **DE10-Lite** (MAX 10).

---

## 2. Marco Teórico

### 2.1 La caja fuerte como FSM

El sistema es una cerradura electrónica que valida una **combinación** ingresada por el usuario. Su comportamiento depende de la secuencia de dígitos introducidos y del número de intentos realizados, por lo que se modela naturalmente como una **FSM tipo Moore**: cada estado define de forma única las salidas (servo, LEDs y display).

A diferencia del semáforo (Lab 05), cuyas transiciones dependían del **tiempo**, aquí las transiciones dependen de las **entradas del usuario** (switches + botón). Es el otro gran tipo de FSM.

### 2.2 Ingreso de la combinación

- La **combinación** consta de **3 dígitos**, cada uno un valor de 4 bits (0–F).
- El usuario fija cada dígito en los switches `SW3..SW0` y lo confirma con un pulso del botón `KEY0`.
- Tras los 3 dígitos, la FSM compara la secuencia ingresada contra la combinación secreta (almacenada con `localparam`).
- La comparación se hace **al final**, con los tres dígitos completos, para no revelar cuál dígito fue el incorrecto.

El sistema dispone de **3 intentos** en total.

### 2.3 Diagrama de Estados

```
                  KEY0            KEY0            KEY0
   ┌──────────┐ (cap. d1) ┌──────┐ (cap. d2) ┌──────┐ (cap. d3) ┌──────────┐
   │  INICIO  │──────────▶│ CAP1 │──────────▶│ CAP2 │──────────▶│  VERIF   │
   │ (cerrado)│           │      │           │      │           │ (compara)│
   └──────────┘           └──────┘           └──────┘           └────┬─────┘
        ▲                                                            │
        │                                        clave correcta      │  clave incorrecta
        │ KEY1 (reset)                   ┌───────────────────────────┤
        │                                ▼                           ▼
        │                          ┌──────────┐              ┌──────────────┐
        │                          │ ABIERTO  │              │    ERROR     │
        │                          │ servo ▲  │              │ intentos-1   │
        │                          │ LED verde│              │ LED rojo     │
        │                          └────┬─────┘              └──────┬───────┘
        │                               │ KEY1 (reset)              │
        └───────────────────────────────┘          intentos > 0    │   intentos = 0
                                         ▲──────────────────────────┤
                                         │ (regresa a INICIO)        ▼
                                         │                   ┌──────────────┐
                                         │     KEY1 (master) │   BLOQUEO    │
                                         └───────────────────│ LED rojo (*) │
                                                             └──────────────┘
```

### 2.4 Tabla de estados y salidas

| Estado | Cod. | Servo | LED verde | LED rojo | HEX[5:0] | Descripción |
|--------|:----:|:-----:|:---------:|:--------:|:--------:|-------------|
| `S_INICIO`  | `000` | cerrado | 0 | 0 | `------` | Reposo, esperando dígito 1 |
| `S_CAP1`    | `001` | cerrado | 0 | 0 | eco dígito | Dígito 1 capturado |
| `S_CAP2`    | `010` | cerrado | 0 | 0 | eco dígito | Dígito 2 capturado |
| `S_VERIF`   | `011` | cerrado | 0 | 0 | `------` | Compara la combinación |
| `S_ABIERTO` | `100` | **abierto** | **1** | 0 | `AccESO` | Combinación correcta |
| `S_ERROR`   | `101` | cerrado | 0 | **1** | `Int.  n` | Errada; `n` intentos restantes |
| `S_BLOQUEO` | `110` | cerrado | 0 | **1** (*) | `bLOquE` | Intentos agotados |

(*) En `S_BLOQUEO` el LED rojo puede parpadear (1 Hz) reutilizando el divisor de frecuencia.

Mensajes sugeridos (todos representables en 7 segmentos; pueden personalizarse):
`AccESO` (acceso concedido), `Int.  n` (intentos restantes), `bLOquE` (bloqueo).

### 2.5 Control del servomotor con PWM

El servo se posiciona con una señal **PWM de 50 Hz** (periodo de 20 ms), donde el **ancho del pulso** define el ángulo:

- Pulso de **1 ms** → ≈ 0° (cerrojo **cerrado**).
- Pulso de **x ms** → ≈ 90° (cerrojo **abierto**).

Con el reloj de 50 MHz de la tarjeta:

```
Periodo (20 ms)  ->  N = 20 ms / 20 ns = 1 000 000 cuentas   (contador de 20 bits)
Pulso 1 ms       ->      1 ms  / 20 ns =    50 000 cuentas
Pulso 2 ms       ->      2 ms  / 20 ns =   100 000 cuentas
```

La salida PWM está en alto mientras `contador < ancho`, donde `ancho` vale 50 000 (cerrado) o 100 000 (abierto). Es el mismo principio del divisor/contador ya trabajado.

### 2.6 Estructura de tres bloques en Verilog

La FSM se implementa con la plantilla ya conocida, separando lo secuencial de lo combinacional:

```
always @(posedge clk) → Registro de estado + registros de dígitos/intentos
always @(*)           → Lógica de próximo estado (case)
always @(*)           → Lógica de salida (servo, LEDs, mensaje del display)
```

El generador PWM del servo y el decodificador de mensajes van en módulos aparte.

---

## 3. Recursos y Materiales

- Tarjeta de desarrollo FPGA **DE10-Lite** (MAX 10).
- Software **Quartus**.
- **1 servomotor SG90** (micro servo, 180°).
- **LED verde** y **LED rojo** externos, con sus resistencias (los LED de la placa son solo rojos).
- Fuente de **5 V externa** para el servo (power bank o fuente USB) — comparte solo **GND** con la FPGA.
- Protoboard, cables y una **caja/maqueta** (cartón o impresa 3D) para el cerrojo.

> Conexión del servo: la señal (naranja) va a un pin del **GPIO**; alimentación (rojo) a **5 V externos**; tierra (café) a **GND común**. No alimente el servo desde la FPGA.

---

## 4. Procedimiento

### Paso 1 — Diseño de la FSM

Antes de escribir código, elabore en el informe:

1. El diagrama de burbujas con los **7 estados** y sus salidas (notación Moore).
2. El diagrama de bloques del sistema (FSM + PWM del servo + decodificador de display).
3. Las condiciones de transición (pulsos de `KEY0`, resultado de la comparación, intentos restantes).
4. La definición de su combinación secreta (3 dígitos de 4 bits).

### Paso 2 — Implementación en Verilog

Creen los modulo necesarios que permitan describir la maquia de estados de la caja fuerte, integrando los modulos necesarios para el display 7 segmentos, los divisores de frecuencia y los controles PWM.

### Paso 3 — Simulación

1. Agregue el testbench al proyecto.
2. Verifique los tres escenarios:
   - Combinación **correcta** → el PWM pasa a 2 ms (servo abierto) y se activa `LED verde` con `AccESO`.
   - Combinación **incorrecta** → `LED rojo`, se decrementan los intentos y el display muestra los restantes.
   - **Tres fallos** → estado `BLOQUEO` con el mensaje correspondiente.
3. Capture las formas de onda para el informe (incluya el ancho del pulso PWM en cada caso).

### Paso 4 — Implementación física

1. Asigne los pines: `SW3..SW0`, `KEY0` (enter), `KEY1` (reset), GPIO del servo y de los LEDs, y los `HEX`.
2. Programe la tarjeta y monte el servo en la maqueta como cerrojo.
3. Demuestre en la sesión: apertura con clave correcta, realimentación de intentos y bloqueo.
4. Suba el proyecto al repositorio con la documentación y las evidencias.

---

## 5. Preguntas de Análisis

Responda en el informe:

1. ¿Por qué esta FSM es tipo **Moore**? ¿Cómo se vería si fuera **Mealy**?
2. ¿Cuántos **flip-flops** necesita la codificación de estado con 7 estados? ¿Y con *one-hot*?
3. Calcule el número de cuentas para los pulsos de 1 ms y 2 ms con el reloj de 50 MHz, y el tamaño en bits del contador del PWM.
4. ¿Por qué conviene **comparar la combinación al final** y no dígito por dígito?
5. ¿Cuántas combinaciones distintas son posibles con 3 dígitos de 4 bits? ¿Cómo aumentaría la seguridad?
6. ¿Por qué es necesario **sincronizar/antirrebotar** las entradas del usuario antes de usarlas en la FSM?
7. ¿Por qué el servo **no** debe alimentarse desde la FPGA?

---

*Electrónica Digital — ECCI · Elaborado para uso académico*