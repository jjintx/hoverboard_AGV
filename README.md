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

- **Openbot**: Es un proyecto maker y de código abierto desarrollado por Mathias Müller y Vladlen Koltun, que trata de convertir los smartphones que la mayoria de las persona dispone hoy en día en robots. Para ello, han desarrollado una aplicación movil con varias funciones: control manual del robot, identificación y seguimiento de personas (mediante IA) para el control automático de personas, lectura de sensores y cámaras... Además, también han desarrollado un programa Arduino para varios tipos de controles de motores y robots, con el que poder hacer el 'puente' entre el smartphone y el robot/motores.

- **Microcontrolador motor PWM RioRand 400W 6-60V PWM DC**: es un controlador para motores que trabajan mediante PWM, que en este caso permitiría conectar el Arduino directamente al motor (pin PWM-microcontrolador PWM), sin tener que pasar por la placa base del hoverboard (la conexión seria mediante serial).


## Caso base


## Caso adaptado

### Archivo CAD

### Esquema eléctrico

### Código de programación en Arduino

### Resultados



## Captura y monitorización de datos