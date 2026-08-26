
## Lab02: Sumadorde 4 bits 


Contenido:

- [Lab02: Sumadorde 4 bits](#lab02-sumadorde-4-bits)
- [1. Objetivos de aprendizaje](#1-objetivos-de-aprendizaje)
- [2. Fundamento teórico](#2-fundamento-teórico)
  - [Sumador de 4 bits](#sumador-de-4-bits)
    - [Funcionamiento](#funcionamiento)
    - [Implementación en HDL](#implementación-en-hdl)
- [3. Entregables](#3-entregables)

## 1. Objetivos de aprendizaje

* 

* Reutilizar un sumador de 4 bits para operaciones de resta mediante intancias.

* Aprender a verificar y validar el funcionamiento del diseño en un entorno de simulación, identificando y corrigiendo errores antes de la implementación física en *hardware*.


## 2. Fundamento teórico


### Sumador de 4 bits


Para crear un sumador de 4 bits, se utilizan cuatro sumadores de 1 bit conectados en serie. Así, el acarreo de salida de un sumador de 1 bit se convierte en el acarreo de entrada del siguiente sumador. Cada bit de los dos números que se están sumando se procesa de manera paralela. 

Para construir un sumador de 4 bits utilizando el sumador de 1 bit como módulo base, se debe **instanciar** varios módulos del sumador de 1 bit y conectar sus entradas y salidas de manera que manejen el acarreo entre cada bit.

Un sumador de 4 bits suma dos números de 4 bits (```[3:0] A``` y ```[3:0] B```) y produce una suma de 4 bits (```[3:0] So```) y un acarreo de salida (```Co```). Para lograr esto, se utilizan 4 sumadores de 1 bit, cada uno manejando una posición de la salida ```So``` (0 a 3) y el acarreo hacia la siguiente posición.  A continuación se muestra su respectivo bloque funcional:

![fpga](/labs/figs/lab1/4bit.png)
<p align="center">
 Figura 3
</p>


La implementación del sumador de 4 bits utilizando instancias del sumador de 1 bit es un ejemplo de diseño estructural en HDL, en donde se utiliza el sumador de 1 bit para construir un sumador de 4 bits de manera modular.

#### Funcionamiento

* Cada instancia del sumador de 1 bit toma 1 bits de las entradas ```A``` y ```B```, y un acarreo de entrada Ci. Calcula la suma de estos bits y produce una suma de un bit ```So``` y un acarreo de salida ```Co```.

* El acarreo de salida de un sumador de 1 bit se usa como acarreo de entrada para el siguiente sumador de 1 bit en la cadena.

* El sumador de 4 bits produce una salida final ```So``` de 4 bits y un acarreo de salida final ```Co```.

#### Implementación en HDL

1. **Concepto de instancia**

    En el contexto de diseño digital y descripción HDL, una instancia se refiere a la creación de un módulo a partir de una definición previamente definida. Instanciar un módulo significa utilizar el módulo definido anteriormente como un bloque en un diseño más grande, proporcionando conexiones específicas para las entradas y salidas del módulo.

    En Verilog podemos utilizar la siguiente sintaxis:

    ```
    module_name instance_name(.port_0(signal_0),..,.port_n(signal_n))
    ```


    donde: 

      * ```module_name```: Es el nombre del módulo que queremos instanciar.

      * ```instance_name```: Es el nombre de la instancia que vamos a generar a nivel local.

      * ```port_0``` ... ```port_1```: Representa al nombre del puerto o variable declarada como entrada o salida del módulo que queremos instanciar, es decir, los nombres de los puertos que aparecen en el prototipo de dicho módulo.

      * ```signal_0``` ... ```signal_n```: Corresponde al nombre de las señales que tenemos en el módulo en el cual nos encontramos trabajando y que nos servirán para interactuar con otros del diseño dentro de dicho módulo.


## 3. Entregables

1. Descripción de hardware del sumador de 4 bits.

2. Documentación del ítem anterior en su respectivo archivo ```README.md```.

3. Realice la respectiva simulaciones y muestre evidencias en su archivo ```README.md```.

4. Implemente la descripción HDL en la tarjeta de desarrollo, empleando la ```IDE Quartus``` y muestre en el laboratorio el funcionamiento, empleando los periféricos que requiera.
