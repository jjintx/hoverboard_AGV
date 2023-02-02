# ENTREGABLE PROYECTO HOVERBOARD - AGV
En el siguiente repositorio se resume la entrega grupal realizada para la asignatura de Tecnologías Industriales del máster en Fabricación Digital, impartido en IMH por Tecnalia.

El proyecto trata de construir y controlar un robot que sea capaz de operar de una manera autónoma, así como poder realizar una captura de datos de sensores para finalmente ser visualizado en un dashboard.

El equipo está formado por los siguientes integrantes: Constanza Jimena Garcia Conde, Juanjo Inchaurtieta e Igor Irastorza.

## Introducción
El desarrollo del presente proyecto se enmarca dentro de la asignatura de Tecnologías Industriales del máster de Fabricación Digital en IMH. El objetivo de la asignatura, y por lo tanto, del presente trabajo, es abordar el carácter multidisciplinar del máster aunando diferentes ramas de conocimiento: hardware y software de automatización, redes de comunicaciones, sensórica y TICs.

Teniendo esto en cuenta, el proyecto a desarrollar parte de varios elementos iniciales con los que hemos contado todos los equipos:

- **Hoverboard**: es una plataforma con dos ruedas y dos espacios para poner los pies, que funciona con dos motores, un microcontrolador, diversos sensores y una batería recargable de litio.

![Hoverboard, controlador PWM y Arduino](https://github.com/IgorIrastorza/hoverboard_AGV/blob/44bb1ae847d0f8a0b10358670d9ccf6c0886eff9/media/Hoverboard.jpg)

- **Microcontrolador Arduino Nano 33 BLE Sense**: el Arduino es una placa basada en un microcontrolador ATMEL. En este caso, es el modelo Nano 33 BLE Sense, que trae de serie la posibilidad de utilizar varios sensores y tipos de conexiones como Blueetoth, acelerometro, giroscopio, variómetro, micrófono... Como se observa en la siguiente imagen, la placa permite programar y conectar múltiples entradas y salidas a los pines que se especifican en el esquema.

![Arduino Nano 33 BLE Sense](https://github.com/IgorIrastorza/hoverboard_AGV/blob/96be8f06ed8653b7e883ac898e369ca5069f0c5f/media/arduino_nano_33_ble_sense.jpg)

- **Openbot**: Es un proyecto maker y de código abierto desarrollado por Mathias Müller y Vladlen Koltun, que trata de convertir los smartphones que la mayoria de las personas dispone hoy en día en robots. Para ello, han desarrollado una aplicación movil con varias funciones: control manual del robot, identificación y seguimiento de personas (mediante IA) para el control automático de robots, lectura de sensores y cámaras... Además, también han desarrollado un programa Arduino para varios tipos de controles de motores y robots, con el que poder hacer el 'puente' entre el smartphone y el robot/motores. Se puede revisar y descargar el código fuente y la bibliografía del proyecto original en el siguiente [enlace](https://github.com/isl-org/OpenBot).

- **Microcontrolador motor PWM RioRand 400W 6-60V PWM DC**: es un controlador para motores que trabaja mediante PWM, que en este caso permitiría conectar el Arduino directamente al motor (pin PWM Arduino - microcontrolador PWM - Motor), sin tener que pasar por la placa base del hoverboard (la conexión sería mediante serial).

- **Ordenador portatil y smartphone**: tal y como se puede intuir, también hemos dispuesto de ordenadores portatiles y smartphones para poder desarrollar y desplegar toda el software y hardware anteriormente mencionado.

## Caso base
El caso base del proyecto era conseguir primeramente controlar el hoverboard al completo de forma remota, utilizando para ello la aplicación RemoTv o el mismo Openbot a traves del smartphone. Estas comunicaciones podían ser realizadas de la siguiente manera:

- **Arduino - Placa base del hoverboard**: esta conexión debería ser realizada mediante el puerto serial del Arduino, que envía el input de la velocidad y dirección para que finalmente el microcontrolador del patinete lo convierta en señales PWM al motor. La comunicación mediante el puerto serie para este patinete en concreto debe ser en formato string con la estructura `[dirección, velocidad]`, donde la dirección tiene un rango de (-1000, 1000) y la velocidad (-250, 250).
- **Arduino - Controladores PWM - Motores**: también existe la posibilidad de saltarse la placa del hoverboard y conectar las señales directamente al motor, utilizando para ello los microcontroladores PWM RioRand mencionados anteriormente. Esto podría realizarse utilizando 2 pines que admitan PWM en el Arduino, conectando cada salida al microcontrolador (habría 2, uno para cada motor), y finalmente el motor al microcontrolador. A continuación se detallan las entradas/salidas del controlador PWM utilizado en clase:

![Entradas/Salidas microcontrolador motor PWM RioRand 400W 6-60V PWM DC](https://github.com/IgorIrastorza/hoverboard_AGV/blob/17901050c4b418338189f64e204adf3f368de21d/media/EE_PWM_RioRand.jpg)

En este caso, la alternativa desarrollada y programada ha sido la segunda, la cual se resume a continuación:

### Código de programación en Arduino
La mayoria de las lineas de código utilizadas derivan del programa original creado por Mathias Müller y Vladlen Koltun. Se puede revisar y descargar el código fuente y la bibliografía del proyecto original en el siguiente [enlace](https://github.com/isl-org/OpenBot).

No obstante, se procederá a revisar y explicar las partes y funciones más importantes del código original, así como las pertinentes modificaciones realizadas.

Cabe destacar que aunque el programa está preparado para varios tipos de robots y controles, en este caso el que se ha utilizado ha sido el `RC_CAR`.

- **Definición variables y pines.**

```
#elif (OPENBOT == RC_CAR)
// Incluimos la librería 'Servo.h' para poder controlar el servo.
#include <Servo.h>
// Declaramos cuales serán las variables para poder mandar los valores de velocidad al servo (ESC Y SERVO).
Servo ESC;
Servo SERVO;
const String robot_type = "RC_CAR";
#define HAS_VOLTAGE_DIVIDER 0
const float VOLTAGE_DIVIDER_FACTOR = (20 + 10) / 10;
const float VOLTAGE_MIN = 4.2f;
const float VOLTAGE_LOW = 4.6f;
const float VOLTAGE_MAX = 5.5f;
const float ADC_FACTOR = 5.0 / 1023;
#define HAS_INDICATORS 0
#define HAS_SONAR 0
#define SONAR_MEDIAN 0
// Se definen los pines a los que se conectará las salidas de control de los motores (PIN_PWM_T, PIN_PWM_S): A0 Y A1.
const int PIN_PWM_T = A0;
const int PIN_PWM_S = A1;
const int PIN_VIN = A7;
const int PIN_TRIGGER = 4;
const int PIN_ECHO = 4;
const int PIN_LED_LI = 7;
const int PIN_LED_RI = 8;
const int PIN_DIR_R = 10;
const int PIN_DIR_L = 9;

```
- **Función setup para definir los parámetros de las variables de los controles de los servos.**

```
#if (OPENBOT == RC_CAR)
// Se modelan los parámetros para las variables de control ESC Y SERVO y sus correspondientes pines
  pinMode(PIN_PWM_T, OUTPUT);
  pinMode(PIN_PWM_S, OUTPUT);
  // Función 'attach' para asignar las variables de control del servo a las variables que guardan los pines correspondientes (A0, A1)
  // También define los siguientes parámetros de control PWM: mínimo y máximo del ancho de pulso, en microsegundos
  ESC.attach(PIN_PWM_T, 1000, 2000);   // (pin, min pulse width, max pulse width in microseconds)
  SERVO.attach(PIN_PWM_S, 1000, 2000); // (pin, min pulse width, max pulse width in microseconds)

```




### Test y Resultados


No obstante, un percance con la batería del hoverboard, que no hacía llegar la corriente necesaria en intensidad para hacer mover los motores correctamente, sumado al tiempo limitado de las clases (6 días), nos obligó a desarrollar una alternativa que fuera viable en un espacio de tiempo tan corto.

## Caso adaptado
Ante la negativa de encontrar alguna alternativa para suministrar la corriente necesaria a los motores del hoverboard, la alternativa desarrollada se ha basado en sustituir los motores del hoverboard por un sencillo servomotor.

![Servomotor estandar MG996R](https://github.com/IgorIrastorza/hoverboard_AGV/blob/17901050c4b418338189f64e204adf3f368de21d/media/EE_PWM_RioRand.jpg)

### Archivo CAD

### Esquema eléctrico

### Código de programación en Arduino

### Test y Resultados



## Captura y monitorización de datos