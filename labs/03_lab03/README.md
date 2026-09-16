# Lab 03: Implementación de 7 segmentos.

---


### 1. Objetivo.

El objetivo de este laboratorio es lograr visualizar el resultado de un sumador de 3 bits en los display 7 segmentos de la tarjeta de desarrollo DE10-Lite.

## 2. Fundamento teórico

### 2.1 BCD (Binary Coded Decimal): 
-------------------------------

BCD significa Décimal Codificado en Binario y representa el sistema de numeración digital en el que podemos representar cada número décimal utilizando 4 bits de números binarios.


Como sabemos hay 10 dígitos en el sistema décimal, para representarlos necesitamos 10 combinaciones de 4 bits binarios.

<p align="center">

|   Dígito  |   Bit $_3$  |  Bit $_2$ |   Bit $_1$  |   Bit $_0$  |
|-----------|-------------|-----------|-------------|-------------|
|     0     |      0      |     0     |      0      |      0      |
|     1     |      0      |     0     |      0      |      1      |
|     2     |      0      |     0     |      1      |      0      |
|     3     |      0      |     0     |      1      |      1      | 
|     4     |      0      |     1     |      0      |      0      |
|     5     |      0      |     1     |      0      |      1      |
|     6     |      0      |     1     |      1      |      0      |
|     7     |      0      |     1     |      1      |      1      | 
|     8     |      1      |     0     |      0      |      0      | 
|     9     |      1      |     0     |      0      |      1      | 
</p>

Ahora bien, también es posible representar de forma binaria los números décimales del 10 al 15 pero empleando su correspondiente representación en el sistema hexadécimal:

<p align="center">

|   Dígito  |   Bit $_3$  |  Bit $_2$ |   Bit $_1$  |   Bit $_0$  |
|-----------|-------------|-----------|-------------|-------------|
|     A     |      1      |     0     |      1      |      0      |
|     B     |      1      |     0     |      1      |      1      |
|     C     |      1      |     0     |      0      |      0      |
|     D     |      1      |     1     |      0      |      1      | 
|     E     |      1      |     1     |      1      |      0      |
|     F     |      1      |     1     |      1      |      1      |
</p>


### 2.2 Display de 7 segmentos:
-------------------------------


El display de siete segmentos es un dispositivo electrónico que consta de siete diodos emisores de luz (LED) dispuestos en un patrón definido; encender una combinación particular de éstos permite representar un dígito décimal o hexadécimal Existen dos tipos de display LED de siete segmentos:

* Tipo de cátodo común: en este tipo de display, todos los cátodos de los siete LEDs están conectados entre sí a tierra o $-Vcc$ (por lo tanto, cátodo común) y el LED muestra dígitos cuando se suministra un nivel alto a los ánodos individuales.
    
* Tipo de ánodo común: en este tipo de display, todos los ánodos de los siete LEDs están conectados a $+Vcc$ (por lo tanto, ánodo común) y el LED muestra dígitos cuando se suministra un nivel al bajo a los cátodos individuales.


En las siguientes figuras se muestra cómo se distribuyen los 7 segmentos en el display cuando se tiene una configuración de ánodo común:

<p align="center">
 <img src="/labs/03_lab03/imagenes/7seg.jpg" alt="alt text" width=500 >
</p>


<p align="center">
 <img src="https://exploreembedded.com/wiki/images/1/1a/0SevenSegment.gif" alt="alt text" width=400 >
</p>



------------

## 3. Procedimiento


**Primera Parte**

Cada grupo debe lograr visualizar, en un display de 7 segmentos, los digitos del 0 a 9, formato decimal, ingresando por medio de los switch cada uno de los números, asi mismo modificar el código para poder visualizar los digitos de 0 a F, formato hexadecimal. También deben realizar la simulación para corroborar su correcto funcionamiento.

**Segunda Parte**

Cada grupo debe lograr visualizar, en un display de 7 segmentos, la suma de dos números de 3 bits, ingresados mediante los switch de la tarjeta de desarrollo. El resultado de la suma debe verse en formato hexadecimal.




## 4. Entregables

1. Descripción de hardware de las Partes 1 y 2.

2. Documentación del ítem anterior en su respectivo archivo ```README.md```.

3. Realice la respectiva simulaciones y muestre evidencias en su archivo ```README.md```.

4. Implemente la descripción HDL en la tarjeta de desarrollo, empleando la ```IDE Quartus``` y muestre en el laboratorio el funcionamiento, empleando los periféricos que requiera.


