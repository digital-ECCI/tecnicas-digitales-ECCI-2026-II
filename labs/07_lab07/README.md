# Laboratorio de Electrónica Digital
## Proyecto Final — Enlace de Comunicación Serial UART entre FPGA y PC
**ECCI · Tecnicas Digitales**

---

## 1. Objetivos

Al finalizar el proyecto, el estudiante será capaz de:

- Diseñar e implementar un módulo de comunicación serial asíncrona (**UART**) en Verilog.
- Integrar en una sola solución lógica combinacional y secuencial: transmisor, receptor, generador de baudios, conversión de datos y entrada/salida de usuario.
- Establecer comunicación en **ambos sentidos** entre la FPGA y un PC mediante una terminal serial (PuTTY).
- Verificar el comportamiento del sistema mediante simulación e implementación en la tarjeta **DE10-Lite** (MAX 10).

---

## 2. Descripción del Sistema

El sistema establece un enlace serial entre la tarjeta y el PC. La correspondencia de recursos es directa: la DE10-Lite tiene **10 switches** y **10 LEDs**, es decir **10 bits**, que cubren exactamente el rango **0 a 1023**.

El funcionamiento comprende los dos sentidos de la comunicación, y ambos se evidencian en la simulación y en la demostración final:

**Sentido FPGA → PC (transmisión).**
El usuario fija un número de 0 a 1023 en los **switches** `SW9..SW0` y presiona un **botón** (`KEY0`). La FPGA transmite ese valor por UART y el número aparece impreso en la terminal **PuTTY**.

**Sentido PC → FPGA (recepción).**
El usuario escribe un número de 0 a 1023 en **PuTTY** y lo confirma con *Enter*. La FPGA recibe el dato por UART y lo representa en **binario** sobre los **10 LEDs** `LEDR9..LEDR0`.

```
        PC (PuTTY)                              FPGA (DE10-Lite)
      ┌────────────┐                     ┌──────────────────────────────┐
      │            │ ── TX ───► RX ──────┤ Receptor UART ─► LEDs (0..1023)│
      │  Terminal  │   (USB-TTL 3.3 V)   │                               │
      │   serial   │ ◄─ RX ◄─── TX ──────┤ Transmisor UART ◄─ Switches+KEY│
      └────────────┘                     └──────────────────────────────┘
```

---

## 3. Marco Teórico

### 3.1 Comunicación serial asíncrona (UART)

La UART transmite los datos **bit a bit** por una sola línea y de forma **asíncrona**: no se envía una señal de reloj, sino que emisor y receptor acuerdan de antemano la misma **velocidad** (baud rate) y el mismo formato de trama. La línea en reposo se mantiene en nivel alto.

### 3.2 La trama UART

```
  IDLE | START |  D0 D1 D2 D3 D4 D5 D6 D7  | STOP | IDLE
   1   |   0   |     8 bits (LSB primero)  |  1   |  1
```

- **Start** (0): un bit en bajo marca el inicio.
- **Datos**: 8 bits, el menos significativo primero.
- **Stop** (1): un bit en alto cierra la trama.
- Formato típico **8-N-1**: 8 bits de datos, sin paridad, 1 bit de stop.

### 3.3 Velocidad (baud rate) y generación

La duración de un bit es $T_{bit} = 1/\text{baud}$. Un contador (divisor) sobre el reloj de 50 MHz marca cada bit:

```
N = f_clk / baud   ->   50 000 000 / 9600 ≈ 5208 cuentas por bit
```

### 3.4 Representación del número: 8 bits vs. 10 bits

Un byte UART transporta **8 bits (0–255)**, pero el rango 0–1023 requiere **10 bits**. Por eso el número **viaja como texto (dígitos ASCII)** y en cada extremo se realiza una conversión:

- **Transmisión:** el valor de 10 bits de los switches se convierte a dígitos decimales ASCII (hasta `"1023"`) y se envían uno a uno.
- **Recepción:** se reciben los dígitos ASCII escritos en PuTTY y se acumulan (`valor = valor*10 + dígito`) hasta el *Enter*, obteniendo el valor de 10 bits para los LEDs.

Esta conversión binario↔decimal es parte central del proyecto.

### 3.5 Transmisor y receptor

- **Transmisor (TX):** una FSM carga el byte y lo desplaza por la línea (start → 8 datos → stop). Es el bloque más sencillo.
- **Receptor (RX):** una FSM detecta el flanco de bajada del **start**, muestrea cada bit **en su centro** (sobremuestreo) y reconstruye el byte. Valida el bit de **stop** (error de trama si no es 1).

---

## 4. Recursos y Materiales

- Tarjeta de desarrollo FPGA **DE10-Lite** (MAX 10).
- Software **Quartus** y terminal **PuTTY** en el PC.
- **Adaptador USB-TTL** (CP2102, CH340 o FT232) configurado a **3.3 V**.
- Cables de conexión.

> Conexión: `GPIO_TX` (salida de la FPGA) → **RX** del adaptador; `GPIO_RX` (entrada de la FPGA) ← **TX** del adaptador; **GND común**. El GPIO de la DE10-Lite es de **3.3 V**: no use 5 V ni niveles RS-232. Configuración sugerida: **9600 baudios, 8-N-1**.

---

## 5. Procedimiento

### Paso 1 — Diseño

En el informe:

1. Diagrama de bloques del sistema (TX, RX, generador de baudios, conversores, E/S).
2. Trama UART y formato elegido (baud, bits de datos, stop).
3. Diagramas de estado de las FSM de transmisión y recepción.
4. Plan de conversión binario↔decimal para los dos sentidos.

### Paso 2 — Implementación en Verilog 

Cree el proyecto en **Quartus** con los archivos de la plantilla base y complete los espacios marcados con `// TODO`:


| Archivo (Sugerencia) | Descripción |
|---------|-------------|
| `proyecto_uart.v` | Módulo superior: integra UART, switches, botón y LEDs |
| `uart_tx.v`       | Transmisor UART (serializa un byte) |
| `uart_rx.v`       | Receptor UART (deserializa un byte) |
| `baud_gen.v`      | Generador de baudios (divisor de frecuencia) |
| `bin2dec.v`       | Convierte el valor de 10 bits a dígitos decimales ASCII (TX) |
| `dec2bin.v`       | Acumula los dígitos ASCII recibidos en un valor de 10 bits (RX) |
| `tb_proyecto_uart.v` | Testbench de simulación |

> Sincronice las entradas asíncronas (línea `RX`, switches y botón) con dos flip-flops. Detecte el **flanco** del botón para transmitir una sola vez por pulsación.

### Paso 3 — Simulación

1. Simule el transmisor: al pulsar el botón, verifique en la forma de onda la trama de cada dígito enviado.
2. Simule el receptor: inyecte una trama y verifique que el valor reconstruido aparece en los LEDs.
3. Capture las formas de onda de los **dos sentidos** para el informe.

### Paso 4 — Implementación física

1. Asigne los pines: `SW9..SW0`, `KEY0`, `LEDR9..LEDR0`, `GPIO_TX`, `GPIO_RX`.
2. Conecte el adaptador USB-TTL y abra PuTTY con la configuración acordada.
3. Compruebe los dos sentidos:
   - Switches + botón → el número se imprime en PuTTY.
   - Número escrito en PuTTY → se muestra en binario en los LEDs.
4. Suba el proyecto al repositorio con la documentación y las evidencias.

---

## 6. Entregables

1. **Descripción de hardware** del sistema en Verilog.
2. **Documentación** del diseño en el `README.md` del repositorio.
3. **Simulaciones** con evidencias (formas de onda) de la transmisión **y** la recepción.
4. **Implementación física** en la DE10-Lite, con la demostración de los dos sentidos de la comunicación durante la sustentación.

---

## 7. Preguntas de Análisis

1. ¿Por qué un solo byte UART no alcanza para representar los números 0–1023, y cómo lo resuelve su diseño?
2. Calcule el número de cuentas del generador de baudios para 9600 baudios con el reloj de 50 MHz.
3. ¿Por qué el **receptor** es más complejo que el **transmisor**?
4. ¿Por qué se detecta el **flanco** del botón y no su nivel para transmitir?
5. ¿Qué es un **error de trama** (*framing error*) y cómo lo detectaría el receptor?
6. ¿Por qué es necesario **sincronizar** la línea `RX` antes de usarla en la FSM?
7. ¿Qué diferencia habría entre enviar el número en binario crudo y enviarlo como texto ASCII?

---

*Electrónica Digital — ECCI · Elaborado para uso académico*