# Lab 01: Introducción a lógica combinacional.

Contenido:

- [Lab 01: Introducción a lógica combinacional.](#lab-01-introducción-a-lógica-combinacional)
  - [1. Objetivos de aprendizaje](#1-objetivos-de-aprendizaje)
  - [2. Fundamento teórico](#2-fundamento-teórico)
      - [Implementación en HDL](#implementación-en-hdl)
  - [3. Actividad](#3-actividad)
    - [3.1 Parte 1: Compuertas lógicas](#31-parte-1-compuertas-lógicas)
    - [3.2 Parte 2: Diseño de circuito combinacional](#32-parte-2-diseño-de-circuito-combinacional)
    - [3.3 Parte 3: Sumador de 1 bit](#33-parte-3-sumador-de-1-bit)
  - [4. Entregables](#4-entregables)



## 1. Objetivos de aprendizaje

- Diseñar y construir sistemas digitales basados en lógica combinacional, enfocándose en la implementación de sumadores.
- Diseñar, construir e instanciar módulos utilizando lenguajes de descripción de *hardware* (HDL)
- Familiarizarse con el flujo completo de diseño e implementación en *hardware*, desde la especificación inicial hasta la síntesis e implementación en FPGA.
- Aprender a verificar y validar el funcionamiento del diseño en un entorno de simulación, identificando y corrigiendo errores antes de la implementación física en *hardware*.
- Explorar la implementación de diseños digitales en tarjeta de desearrollo basadas en FPGAs.

## 2. Fundamento teórico


En diseño digital, un sumador de 1 bit es un circuito combinacional que realiza la suma de dos bits junto con un bit de acarreo de entrada. Es uno de los bloques fundamentales en la construcción de sumadores de mayor tamaño, que son esenciales en operaciones aritméticas dentro de procesadores y sistemas digitales. También se conoce como sumador completo.  A continuación se muestra su respectivo bloque funcional:

<p align="center">
 <img src="/labs/figs/lab1/1bit.png" alt="alt text" width=300 >
</p>
<p align="center">
 Figura 1
</p>

El sumador de 1 bit toma tres entradas: los dos bits que se desean sumar (```A``` y ```B```) y un bit de acarreo de entrada (```Ci```) que puede provenir de una posición menos significativa en un sumador más grande. El circuito produce dos salidas: el bit de suma (```So```) y el bit de acarreo de salida (```Co```).


A continuación se presenta la tabla de verdad del sumador completo de 1 bit.

<p align="center">

|   A  |   B  |  Ci |   Co  |   So  |
|------|------|-----|-------|-------|
|   0  |   0  |  0  | **0** | **0** |
|   0  |   0  |  1  | **0** | **1** |
|   0  |   1  |  0  | **0** | **1** |
|   0  |   1  |  1  | **1** | **0** | 
|   1  |   0  |  0  | **0** | **1** |
|   1  |   0  |  1  | **1** | **0** |
|   1  |   1  |  0  | **1** | **0** |
|   1  |   1  |  1  | **1** | **1** | 
</p>


A partir de la tabla de verdad, mediante simplificacion de maxterminos, se obtienen las expresiones que definen el sumador de 1 bit, las cuales son:

<p align="center">
<img src="/labs/figs/lab1/karnaugh.png" alt="alt text" width=800 >
</p>

A partir de las expresiones obtenidas se puede construir el siguiente circuito:

<p align="center">
<img src="/labs/figs/lab1/Circuito_sumador.png" alt="alt text" width=600 >
</p>
<p align="center">
 Figura 2
</p>

#### Implementación en HDL

En la descripción de *hardware*, los sumadores de 1 bit pueden implementarse utilizando diferentes enfoques, según el nivel de abstracción deseado.

  * **Implementación estructural**:

    Se definen explícitamente los componentes individuales (AND, OR, XOR) necesarios para calcular la salida de suma (```So```) y el acarreo (```Co```) según las ecuaciones lógicas obtenidas de la tabla de verdad. Esto se puede realizar de dos formas:

    1. Usando operadores lógicos

        * Descripción: Los operadores lógicos son símbolos o funciones que representan operaciones lógicas sobre las señales. En HDL, estos operadores se usan para crear expresiones que definen cómo las señales de entrada se combinan para obtener una salida. Los operadores lógicos más comunes incluyen AND (```&```), OR (```|```), NOT (```~```), XOR (```^```), etc.

        * Ejemplo:
        
          ```
          assign salida = A & B;  // Operador AND
          ```

        * Características:
        Son más abstractos y concisos.
        Se usan dentro de las expresiones lógicas para realizar combinaciones entre señales.
        Pueden operar sobre señales o vectores de bits.
        No están ligados a ninguna implementación física específica. El sintetizador de hardware es el que decide cómo implementarlos en puertas lógicas (AND, OR, etc.) en función de la optimización.

    2. Usando primitivas

        * Descripción: Las primitivas son elementos de hardware básicos predefinidos en un lenguaje de descripción de hardware (HDL). Estos pueden ser puertas lógicas (como AND, OR, NOT), flip-flops, multiplexores, entre otros. En lugar de escribir operaciones lógicas en forma de expresiones, se utilizan estos bloques como componentes predefinidos y directos de un diseño.

        * Ejemplo:

          ```
          and (salida, A, B);  // Primitiva AND
          ```


        * Características:

          Las primitivas son más cercanas al hardware real, pues cada primitiva corresponde a un bloque o componente físico específico.
          Permiten una descripción explícita del diseño usando componentes predefinidos (puertas, registros, etc.).
          En muchos HDL (como Verilog), las primitivas están predefinidas en la biblioteca y pueden ser más eficientes para la síntesis, ya que el sintetizador ya sabe cómo mapearlas a los recursos del hardware físico.


En la siguiente tabla podrán encontrár los operadores lógicos de verilog.

<p align="center">
<img src="/labs/figs/lab1/operadores.png" alt="alt text" width=300 >
</p>



## 3. Actividad

### 3.1 Parte 1: Compuertas lógicas

Cada grupo debera realizar lla descripción de hardware mediante verilog de cada una de las compuertas lógicas, para posteriormente comprobar, mediante una simulación, las tablas de verdad de cada una. 


***Compuerta NOT***

|  A   | S |  
|------|---|           
|   0  | **1** | 
|   1  | **0** | 


***Compuerta AND***

|   A  |   B  | **S** |         
|------|------|-------|
|   0  |   0  | **0** | 
|   0  |   1  | **0** | 
|   1  |   0  | **0** | 
|   1  |   1  | **1** |  


***Compuerta OR***

| E~1~ | E~2~ | S |  
|------|------|---|
|   0  |   0  | **0** | 
|   0  |   1  | **1** | 
|   1  |   0  | **1** | 
|   1  |   1  | **1** |  


***Compuerta XOR***

| E~1~ | E~2~ | S |  
|------|------|---|
|   0  |   0  | **0** | 
|   0  |   1  | **1** | 
|   1  |   0  | **1** | 
|   1  |   1  | **0** |  

***Compuerta XNOR***

| E~1~ | E~2~ | S |  
|------|------|---|
|   0  |   0  | **1** | 
|   0  |   1  | **0** | 
|   1  |   0  | **0** | 
|   1  |   1  | **1** |  


<p> <span style="color: red;">NOTA:</span></p> 

1. Cada grupo es libre de utilizar el número de entradas que crea conveniente para la simulación de cada una de las compuertas descritas.

2. Se debe realizár la descripción de cada compuerta mediante el uso de primitivas (el caso de ser posible) y comportamiento estructural.

### 3.2 Parte 2: Diseño de circuito combinacional

Cada grupo deberá diseñár un circuito que permita detectar si un número binario de 3 bits es un número primo o no.


### 3.3 Parte 3: Sumador de 1 bit

Cada grupo deberá comprobár, mediante  simulación, la tabla de verdad del sumador completo de 1 bit descrito en la sección ```Fundamento teórico```.

## 4. Entregables

1. Realice la descripción de hardware de las partes 1, 2 y 3 anteriormente mencionadas.

2. Explique el ítem anterior en su respectivo archivo ```README.md```.

3. Realice la respectiva simulaciones y muestre evidencias en su archivo ```README.md```.

4. Implemente la descripción HDL en la tarjeta de desarrollo, empleando la ```IDE Quartus``` y muestre en el laboratorio el funcionamiento, empleando los periféricos que requiera.




