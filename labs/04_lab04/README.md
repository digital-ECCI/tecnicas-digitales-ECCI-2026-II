# Lab 04: Contador Digital en Display de 7 Segmentos

## Objetivo

Implementar un cronómetro digital capaz de contar segundos, décimas, centésimas y milésimas de segundo, visualizando el resultado en el display de 7 segmentos integrado en la FPGA.

---

## Marco Teórico

### Lógica Secuencial

La lógica secuencial es una rama de la electrónica digital que estudia sistemas cuyo comportamiento depende no solo de las entradas actuales, sino también del historial de estados anteriores. A diferencia de la lógica combinacional —donde la salida es función exclusiva de las entradas presentes— en los sistemas secuenciales la salida evoluciona según la secuencia de eventos pasados, lo que les permite *recordar* información.

![Diagrama de lógica secuencial](/labs/figs/lab4/Diagrama_LogSec.png)

---

## Componentes y Principios Fundamentales

### Flip-Flops

Los flip-flops son los elementos de memoria básicos de la lógica secuencial. Cada uno almacena un bit de información y mantiene su estado hasta recibir una señal de activación proveniente del reloj del sistema. Los tipos más utilizados son:

- **Tipo D** – Captura el valor de la entrada en el flanco activo del reloj.
- **Tipo T** – Conmuta su estado cada vez que la entrada está activa.
- **Tipo SR** – Permite *Set* y *Reset* independientes de su salida.
- **Tipo JK** – Generalización del SR que elimina el estado prohibido.

![Tipos de flip-flop](/labs/figs/lab4/ff.png)
![Esquemático de flip-flop](/labs/figs/lab4/esq_ff.png)

### Registros

Un registro es un conjunto de flip-flops que opera de forma coordinada para almacenar múltiples bits de información. Los registros de desplazamiento, en particular, permiten mover datos entre flip-flops adyacentes con cada pulso de reloj, siendo una operación fundamental en muchos sistemas digitales.

### Contadores

Los contadores son circuitos construidos a partir de flip-flops encadenados, diseñados para llevar la cuenta de pulsos de reloj. Según su configuración, pueden contar en secuencia binaria, BCD, Gray u otras codificaciones.

---

### Divisor de Frecuencia

Un divisor de frecuencia es un circuito cuya señal de salida tiene una frecuencia menor que la de entrada. Se implementa típicamente mediante un contador módulo *N*: la señal que se desea dividir alimenta la entrada de reloj del contador y, al alcanzar el estado *N−1*, se genera un pulso de salida. La frecuencia resultante es exactamente *N* veces menor que la frecuencia de entrada.

![Diagrama de divisor de frecuencia](/labs/figs/lab4/fdivisor.png)

En la figura anterior se ilustran las siguientes señales:

| Señal | Descripción |
|-------|-------------|
| `clk` | Reloj principal del sistema |
| `rst` | Reset síncrono; inicializa todas las variables en cero |
| `counter` | Contador que cicla de 0 a N−1 (en el ejemplo, hasta 4) |
| `fdiv` | Nueva señal de reloj con frecuencia N veces menor que `clk` |

---

## Actividad

Diseñar e implementar un cronómetro digital que cuente desde **0000** hasta **9999**, representando respectivamente milésimas, centésimas, décimas y segundos. El resultado debe visualizarse en tiempo real sobre el display de 7 segmentos de la FPGA, tal como se muestra en el siguiente [video de referencia](https://youtu.be/N6tfK1eepyU).

El sistema debe cumplir con los siguientes requisitos:

- **Dos switches de selección** para elegir entre los distintos modos de conteo (segundos, décimas, centésimas o milésimas).
- **Un botón de reset** que reinicie el contador a cero en cualquier momento.

---

## Entregables

1. **Descripción de hardware** del cronómetro-contador en HDL.
2. **Documentación** del diseño en el archivo `README.md` del repositorio.
3. **Simulaciones** del sistema, con evidencias (capturas o formas de onda) incluidas en el `README.md`.
4. **Implementación física** de la descripción HDL sobre la tarjeta de desarrollo usando **Quartus IDE**, demostrando el funcionamiento con los periféricos requeridos durante la sesión de laboratorio.