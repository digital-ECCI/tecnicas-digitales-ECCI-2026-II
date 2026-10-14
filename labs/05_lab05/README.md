# Laboratorio de Electrónica Digital
## Lab 05 — Máquinas de Estado Finito: Semáforo Básico en FPGA
**ECCI · Tecnicas Digitales**

---

## 1. Objetivos

Al finalizar este laboratorio, el estudiante será capaz de:

- Comprender el modelo de Máquina de Estado Finito (FSM) tipo Moore.
- Diseñar e implementar una FSM en Verilog con separación clara de bloques combinacionales y secuenciales.
- Sintetizar y programar el diseño en la tarjeta **DE10.Lite** (MAX 10).
- Verificar el comportamiento temporal del semáforo mediante simulación y prueba en hardware.

---

## 2. Marco Teórico

### 2.1 Máquinas de Estado Finito (FSM)

Una Máquina de Estado Finito es un modelo matemático de cómputo secuencial que consta de:

| Elemento | Descripción |
|----------|-------------|
| **Q** | Conjunto finito de estados |
| **Σ** | Alfabeto de entradas |
| **δ** | Función de transición: `Q × Σ → Q` |
| **λ** | Función de salida |
| **q₀** | Estado inicial |

Existen dos modelos principales:

- **Moore**: las salidas dependen **solo** del estado actual. La salida es más estable (menos glitches).
- **Mealy**: las salidas dependen del estado **y de las entradas** actuales. Puede requerir menos estados.

En este laboratorio se implementa el **modelo Moore**, ya que cada luz del semáforo corresponde directamente a un estado.

### 2.2 Diagrama de Estados del Semáforo

El semáforo controla una intersección de un solo cruce con el siguiente ciclo fijo:

```
  
   ┌─────────┐              ┌─────────┐              ┌─────────┐
   │  ROjO   │              │AMARILLO │              │  VERDE  │
   |         |  N1 ciclos   |A VERDE  |   N2 ciclos  |         | 
   │  R=1    │─────────────▶│  R=0    │─────────────▶│  R=0    │
   │  A=0    │              │  A=1    │              │  A=0    │
   │  V=0    │              │  V=0    │              │  V=1    │
   └─────────┘              └─────────┘              └─────────┘  
        ▲                                                 |            
        |                                                 |  
        |                   ┌─────────┐                   |  
        |                   │AMARILLO │                   |          
        |     N2 ciclos     | A ROJO  |    N3 ciclos      |  
        |───────────────────│  R=0    │◀──────────────────|  
                            │  A=1    │
                            │  V=0    │
                            └─────────┘ 
              
```

| Estado | Codificación | Verde | Amarillo | Rojo | Duración |
|--------|:------------:|:---:|:---:|:---:|:---:|
| `S_ROJO`              | `2'b00` | 1 | 0 | 0 | 10 s |
| `S_AMARILLO A VERDE`  | `2'b01` | 0 | 1 | 0 | 3 s  |
| `S_ROJO`              | `2'b10` | 0 | 0 | 1 | 10 s |
| `S_AMARILLO A ROJO`   | `2'b11` | 0 | 1 | 0 | 3 s  |

### 2.3 Implementación en Verilog: Estructura de Tres Bloques

La FSM en Verilog se implementa con **tres bloques `always` separados** para claridad y buenas prácticas de síntesis:

```
always @(posedge clk) → Registro de estado (bloque secuencial)
always @(*)           → Lógica de transición (bloque combinacional)
always @(*)           → Lógica de salida (bloque combinacional)
```

Esta separación evita latch inferidos y facilita la verificación.

---

## 3. Recursos y Materiales

- Tarjeta de desarrollo - FPGA **DE10-Lite** (Max10)
- Software **Quartus**
- Modulo semaforo
---

## 4. Procedimiento

### Paso 1 — Diseño del Diagrama de Estados

Antes de escribir código, dibuje en papel o en el informe:

1. El diagrama burbujas de la FSM con los **3 estados**.
2. Diagrama de bloques.
3. Las **condiciones de transición** (contador de tiempo).
4. Las **salidas** de cada estado en notación Moore.


### Paso 2 — Implementación en Verilog

Cree un nuevo proyecto en Quartus con los archivos provistos en la plantilla base:

| Archivo | Descripción |
|---------|-------------|
| `traffic_light.v` | Módulo principal FSM |
| `clk_divider.v`   | Divisor de reloj (50 MHz → 1 Hz) |
| `tb_traffic_light.v` | Testbench de simulación |


Complete los espacios marcados con `// TODO` en `traffic_light.v`.

### Paso 3 — Simulación (Iverilog)

1. Agregue el testbench al proyecto.
2. Verifique en la forma de onda que los estados cambian en el orden correcto: `VERDE → AMARILLO → ROJO → VERDE`.
3. Capture pantalla de las formas de onda para el informe.


### Paso 4 — Implementación en FPGA

A partir de dos modulos de semaforo (o 5 leds de colores rojo, amarillo y verde) implementen la maquina de estados que permita visualizar el funcinamiento de un semaforo vehicular y de uno peatonal para el mismo cruce vial. Como bono adicional puede agregar un boton que indique mediante una alarma sonora que el semaforo peatonal se encuentra en verde, siempre y cuando, previamente se haya oprimido dicho boton.


## 5. Preguntas de Análisis

Responda en el informe:

1. ¿Por qué se usa el modelo **Moore** en lugar de **Mealy** para este semáforo? ¿Cuál sería la diferencia en el diagrama de estados?
2. ¿Cuántos *flip-flops* requiere mínimamente la codificación de estado con 3 estados? ¿Y con *one-hot encoding*? Compare los recursos de síntesis.
3. Si se quisiera agregar un estado de **intermitente amarillo** (parpadeo a 1 Hz), ¿cómo modificaría el diagrama de estados y el código?
4. ¿Qué problema ocurriría si el bloque de lógica de transición **y** el registro de estado estuvieran en un solo bloque `always @(posedge clk)` con señales combinacionales?
5. Calcule la frecuencia mínima de reloj necesaria para que el contador de 10 s no desborde un registro de 20 bits.

---