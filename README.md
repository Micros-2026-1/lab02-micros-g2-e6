[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/KzqfxGd5)
[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=22829015&assignment_repo_type=AssignmentRepo)
# Lab02 - Caracterización de osciladores (externo vs. interno)


## 1. Integrantes

## 2. Documentación
En este laboratorio se analizó el funcionamiento de los osciladores interno y externo del microcontrolador PIC18F45K22. Se programó el sistema para generar una señal periódica y medir su frecuencia utilizando un osciloscopio. Con los resultados obtenidos se comparó la precisión y estabilidad de ambos osciladores, observando que el oscilador externo ofrece mayor exactitud, mientras que el oscilador interno es más simple de implementar al no requerir componentes adicionales.

### 2.1 Descripción del laboratorio
Los microcontroladores requieren una señal de reloj para sincronizar la ejecución de instrucciones y el funcionamiento de los periféricos internos. Esta señal es generada por un oscilador, el cual define la velocidad a la que trabaja el sistema.

Existen dos tipos principales de osciladores en los microcontroladores:

-Oscilador externo, que utiliza componentes externos como un cristal de cuarzo o un resonador.

-Oscilador interno, integrado dentro del microcontrolador.

En este laboratorio se analiza el comportamiento de ambos tipos de osciladores utilizando el microcontrolador PIC18F45K22, comparando su frecuencia, precisión y estabilidad ante variaciones como cambios de temperatura.

### 2.2 Explicación del código implementado

A continuación, el código define una serie de bits de configuración mediante directivas #pragma config. Estos parámetros determinan el comportamiento básico del microcontrolador antes de que el programa comience a ejecutarse. En este caso, se desactiva el Watchdog Timer (WDTEN = OFF), que normalmente reinicia el microcontrolador si el programa se bloquea. También se desactiva la programación de bajo voltaje (LVP = OFF), se configuran los pines del puerto B como digitales (PBADEN = OFF) y se deshabilita la protección de código (CP0, CP1, CP2, CP3 = OFF), permitiendo leer el programa almacenado en memoria. Además, se desactiva el Brown-Out Reset (BOREN = OFF), que reinicia el sistema cuando el voltaje baja demasiado, así como el monitor de fallo de reloj (FCMEN = OFF) y el cambio automático entre osciladores (IESO = OFF).

Posteriormente, el programa define el modo de oscilador mediante la constante MODE. Esta variable permite seleccionar el tipo de fuente de reloj que utilizará el microcontrolador. Existen tres opciones: usar el oscilador interno, utilizar un cristal externo de alta velocidad o emplear un circuito RC externo. En este caso se selecciona el modo 1, que corresponde al oscilador interno del microcontrolador. Dependiendo del modo elegido, el compilador configura automáticamente el parámetro FOSC, que determina cómo se generará la señal de reloj del sistema.

Después de seleccionar el modo de oscilador, el código define la frecuencia de operación del microcontrolador mediante la constante _XTAL_FREQ. En este programa se establece una frecuencia de 16 MHz. Esta definición es importante porque el compilador utiliza este valor para calcular correctamente los tiempos de retardo cuando se emplean funciones como __delay_ms().

El programa también define una función llamada delay_ms, cuyo propósito es generar retardos en milisegundos. Esta función recibe como parámetro un número entero que indica la cantidad de milisegundos a esperar. Internamente utiliza un ciclo while que llama repetidamente a la función __delay_ms(1), produciendo retardos de un milisegundo hasta completar el tiempo solicitado.

Luego se define la función init_pins, encargada de configurar los pines del microcontrolador. En esta función se establece que el pin RC0 funcionará como salida digital, lo cual se logra colocando el bit correspondiente del registro TRISC en cero. También se inicializa el estado del pin en nivel bajo utilizando el registro LATC. Este pin será el encargado de generar la señal intermitente del programa. Además, se incluye una condición para configurar el pin RA6 como salida solo si el modo de oscilador lo permite, ya que en algunos modos este pin puede utilizarse como salida del reloj del sistema.

Posteriormente se encuentra la función init_oscillator, que se encarga de habilitar el PLL (Phase Locked Loop) en caso de que esté activado. El PLL permite multiplicar la frecuencia del oscilador para aumentar la velocidad de operación del microcontrolador. En este código la opción está deshabilitada, por lo que el sistema funciona directamente a 16 MHz.

Finalmente se encuentra la función principal main, que es el punto de inicio del programa. En primer lugar, se llaman las funciones de inicialización para configurar los pines y el oscilador. Después, el programa entra en un ciclo infinito mediante la instrucción while(1), lo cual es típico en los sistemas embebidos porque el microcontrolador debe ejecutar continuamente su tarea principal.

Dentro de este ciclo infinito se genera una señal digital en el pin RC0. Primero se coloca el pin en nivel alto, luego se espera un milisegundo utilizando la función de retardo. Después se coloca el pin en nivel bajo y nuevamente se espera un milisegundo. Este proceso se repite continuamente, lo que produce una señal cuadrada. Debido a que el tiempo total de un ciclo completo es de aproximadamente 2 milisegundos (1 ms en alto y 1 ms en bajo), la frecuencia de la señal generada es cercana a 500 Hz. Esta señal puede utilizarse para pruebas, para hacer parpadear un LED o como señal de reloj para otros circuitos electrónicos.

### 2.3 Análisis y comparación

#### Tabla 1: Medición en frío

| Modo de oscilador | Freq. teórica Fosc | RA6 medible (CLKO)? | Freq. medida RA6 (Hz) | Freq. teórica RC0 (Hz)| Freq. medida RC0 (Hz) | Error RC0 (%) |  
|------------------|------------------|---------------------|---------------|---------------------|---------------|---------------|
| INTOSC (interno) | 16,000,000       | Sí                 |                     |                500                 |               |               | |
| HS (cristal externo 16 MHz) | 16,000,000 | No |     NA      |               500                 |               |               |
| RC externo       | ~16,000,000*     | No                                    |       N/A        | 500                 |               |               | |

#### Tabla 2: Medición con calor

| Modo de oscilador | Freq. teórica Fosc | RA6 medible (CLKO)? | Freq. medida RA6 (Hz) | Freq. teórica RC0 (Hz)| Freq. medida RC0 (Hz) | Error RC0 (%) |  
|------------------|------------------|---------------------|---------------|---------------------|---------------|---------------|
| INTOSC (interno) | 16,000,000       | Sí                 |                     |                500                 |               |               | |
| HS (cristal externo 16 MHz) | 16,000,000 | No |     NA      |               500                 |               |               |
| RC externo       | ~16,000,000*     | No                                    |       N/A        | 500                 |               |               | |

#### Tabla 3: Deriva

| Modo de oscilador |RC0 deriva (Hz) |
|------------------|--------------------|
| INTOSC (interno) |                    |                
| HS (cristal externo 16 MHz) |                |                |
| RC externo       |                 |                


<!-- Agregar tablas para valores usando PLL -->

<!-- Complemente con análisis de lo registrado en tablas -->

## 2.4 Diagramas

## 2.5 Formas de onda

### INTOSC (interno) 


### HS

## RC

## 3. Evidencias de implementación

## 4. Preguntas

* ¿En qué modo se obtuvo la medición más cercana a la frecuencia teórica?

* ¿Fue posible evidenciar el fenómeno de deriva? ¿Qué factores podrían explicar la variación de frecuencia al calentar el PIC?

* ¿Cuál es más preciso en cuanto a frecuencia teórica vs. medida?


* Explique cómo usar RC0 para estimar la frecuencia del oscilador cuando RA6 no está disponible.

* Si se quisiera duplicar la frecuencia del PIC usando PLL, ¿en qué modos se podría aplicar?

* Enliste ventajas y desventajas de cada modo.

## 5. Referencias