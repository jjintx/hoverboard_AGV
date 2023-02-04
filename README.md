# ENTREGABLE PROYECTO HOVERBOARD - AGV

En el siguiente repositorio se resume la entrega grupal realizada para la asignatura de Tecnologías Industriales del máster en Fabricación Digital, impartido en IMH por Tecnalia.

El proyecto trata de construir y controlar un robot que sea capaz de operar de una manera autónoma, así como poder realizar una captura de datos de sensores para finalmente ser visualizado en un dashboard.

El equipo está formado por los siguientes integrantes: Constanza Jimena Garcia Conde, Juanjo Inchaurtieta e Igor Irastorza.

## Introducción
El desarrollo del presente proyecto se enmarca dentro de la asignatura de Tecnologías Industriales del máster de Fabricación Digital en IMH. El objetivo de la asignatura, y por lo tanto, del presente trabajo, es abordar el carácter multidisciplinar del máster aunando diferentes ramas de conocimiento: hardware y software de automatización, redes de comunicaciones, sensórica y TICs.

Teniendo esto en cuenta, el proyecto a desarrollar parte de varios elementos iniciales con los que hemos contado todos los equipos:

- **Hoverboard**: es una plataforma con dos ruedas y dos espacios para poner los pies, que funciona con dos motores tipo BLDC, un microcontrolador, diversos sensores y una batería recargable de litio.

![Hoverboard, controlador PWM y Arduino](https://github.com/IgorIrastorza/hoverboard_AGV/blob/44bb1ae847d0f8a0b10358670d9ccf6c0886eff9/media/Hoverboard.jpg)

- **Microcontrolador Arduino Nano 33 BLE Sense**: el Arduino es una placa basada en un microcontrolador ATMEL. En este caso, es el modelo Nano 33 BLE Sense, que trae de serie la posibilidad de utilizar varios sensores y tipos de conexiones como Blueetoth, acelerometro, giroscopio, variómetro, micrófono... Como se observa en la siguiente imagen, la placa permite programar y conectar múltiples entradas y salidas a los pines que se especifican en el esquema.

![Arduino Nano 33 BLE Sense](https://github.com/IgorIrastorza/hoverboard_AGV/blob/96be8f06ed8653b7e883ac898e369ca5069f0c5f/media/arduino_nano_33_ble_sense.jpg)

- **Openbot**: Es un proyecto maker y de código abierto desarrollado por Mathias Müller y Vladlen Koltun, que trata de convertir los smartphones que la mayoría de las personas dispone hoy en día en robots. Para ello, han desarrollado una aplicación móvil con varias funciones: control manual del robot, identificación y seguimiento de personas (mediante IA) para el control automático de robots, lectura de sensores y cámaras... Además, también han desarrollado un programa Arduino para varios tipos de controles de motores y robots, con el que poder hacer el 'puente' entre el smartphone y el robot/motores. Se puede revisar y descargar el código fuente y la bibliografía del proyecto original en el siguiente [enlace](https://github.com/isl-org/OpenBot).

- **Microcontrolador motor PWM RioRand 400W 6-60V PWM DC**: es un controlador para motores que sirve para controlar motores BLDC. Recibiría como input las señales PWM que generá el arduino, para después convertirlo en señales que un motor BLDC pueda entender. Por lo tanto, permitiría conectar el Arduino directamente al motor (pin PWM Arduino - microcontrolador motor - motor), sin tener que pasar por la placa base del hoverboard (en ese caso la conexión sería mediante serial).

- **Ordenador portatil y smartphone**: tal y como se puede intuir, también hemos dispuesto de ordenadores portatiles y smartphones para poder desarrollar y desplegar toda el software y hardware anteriormente mencionado.

## Caso base
El caso base del proyecto era conseguir primeramente controlar el hoverboard al completo de forma remota, utilizando para ello la aplicación Openbot a traves del smartphone. Estas comunicaciones podían ser realizadas de la siguiente manera:

- **Arduino - Placa base del hoverboard**: esta conexión debería ser realizada mediante el puerto serial del Arduino, que envía el input de la velocidad y dirección para que finalmente el microcontrolador del patinete lo convierta en señales de control al motor. La comunicación mediante el puerto serie para este patinete en concreto debe ser en formato string con la estructura `[dirección, velocidad]`, donde la dirección tiene un rango de (-1000, 1000) y la velocidad (-250, 250).
- **Arduino - Controladores PWM - Motores**: también existe la posibilidad de saltarse la placa del hoverboard y conectar las señales directamente al motor, utilizando para ello los microcontroladores PWM RioRand mencionados anteriormente. Esto podría realizarse utilizando 2 pines que admitan PWM en el Arduino, conectando cada salida al microcontrolador (habría 2, uno para cada motor), y finalmente el motor al microcontrolador. A continuación se detallan las entradas/salidas del controlador de motores BLDC utilizado en clase:

![Entradas/Salidas microcontrolador motor PWM RioRand 400W 6-60V PWM DC](https://github.com/IgorIrastorza/hoverboard_AGV/blob/2b1120578f6fb78754f0188e3e4749adce3b9bf5/media/EE_PWM_RioRand.jpg)

En este caso, la alternativa desarrollada y programada ha sido la segunda, la cual se resume en el siguiente esquema:

![Esquema general de conexiones y elementos](https://github.com/IgorIrastorza/hoverboard_AGV/blob/f74daef4899eb014989c7d621eb3241a21e47f3d/media/esquema_conexiones.jpg)

### Archivo CAD
El primer desarrollo realizado en el proyecto, antes de entrar en la programación, ha sido el diseño 3D del ensamblaje entre el robot y el hoverboard. El archivo del diseño se encuentra en la carpeta `CAD` del presente repositorio, donde se encuentra un archivo `.stp` para poder abrir el ensamblaje en cualquier software PLM, así como los archivos originales guardados en una carpeta comprimida, en caso de que se disponga de Solid Edge.

No obstante, a continuación se incluye una imagen del diseño final de todo el ensamblaje:
![Ensamblaje final hoverboard-robot](https://github.com/IgorIrastorza/hoverboard_AGV/blob/8469123b8340d215dc03ad8fbfe5a40f8fd55d9a/media/ensamblaje_robot_hoverboard.jpg)

Como se puede observar en la anterior imagen, los principales componentes son los siguientes:
- **Hoverboard**.
- **Base inferior del hoverboard**: se encuentra ensamblado al hoverboard y es la estructura principal del robot.
- **Base superior del hoverboard**: soporte para todos los elementos auxiliares (seta, soportes, arduino, ruedas...).
- **Seta de emergencia**: posibilita la parada total del robot, en caso de haber algún incidente. 
- **Soporte arduino**: estructura de apoyo del arduino con un agujero para la salida de todos los cables. Evitará cualquier movimiento del arduino no deseado cuando el robot esté en funcionamiento.
- **Soporte smartphone**: estructura de apoyo para el smartphone, que permite tener una visión sin obstaculos a la cámara del móvil mientras se ejecuta la aplicación Openbot y manda datos al arduino.
- **Ruedas delanteras**: ruedas de apoyo del robot, que disponen de libertad de movimiento en 2 ejes.

### Aplicación Openbot para SO Android
Tal y como se ha mencionado en la introducción, una de las claves del proyecto reside en el desarrollo de la aplicación de conducción autónoma realizado por Mathias Müller y Vladlen Koltun (repositorio del proyecto y APKs de la app disponible en el siguiente [enlace](https://github.com/isl-org/OpenBot)).

Aunque existen 2 aplicaciones distintas desarrolladas, este proyecto ha estado focalizado en la aplicación catalogada como `Openbot`, que incluye el módulo de conducción autónoma del robot. Se ejecuta en cualquier dispositivo móvil que cumpla los siguientes requisitos:
- Android Studio 3.2 o superior.
- Dispositivo Android y entorno de desarrollo Android con API 21 como mínimo.

Actualmente, la app registra las lecturas de los siguientes sensores: cámara, giroscopio, acelerómetro, magnetómetro, sensor de luz ambiental y barómetro. Utilizando la API de Android, también se pueden obtener las siguientes lecturas de los sensores: imágenes RGB, velocidad angular, aceleración lineal, gravedad, intensidad del campo magnético, intensidad de la luz, presión atmosférica, latitud, longitud, altitud, rumbo y velocidad. Además de los sensores del propio smarpthone, también se registran las lecturas de los sensores del arduino (odometría de las ruedas, distancia de los obstáculos y voltaje de la batería), que se transmiten a través del puerto serial conectado al arduino y al teléfono móvil. Por último, se integran varios algoritmos de redes neuronales para el seguimiento de personas y la navegación autónoma, que permite convertir el movimiento de las personas en inputs a los 2 motores del hoverboard y así poder seguir a las personas de una forma autonóma.

![Captura de pantalla de la app Openbot](https://github.com/isl-org/OpenBot/blob/3e5e73068f263ce32e8f4821e1b90712f1523bb4/docs/images/screen_default.jpg)

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
void setup()
```

```
#if (OPENBOT == RC_CAR)
// Se modelan los parámetros para las variables de control ESC Y SERVO y sus correspondientes pines.
  pinMode(PIN_PWM_T, OUTPUT);
  pinMode(PIN_PWM_S, OUTPUT);
  // Función 'attach' para asignar las variables de control del servo a las variables que guardan los pines correspondientes (A0, A1).
  // También define los siguientes parámetros de control PWM: mínimo y máximo del ancho de pulso, en microsegundos.
  ESC.attach(PIN_PWM_T, 1000, 2000);   // (pin, min pulse width, max pulse width in microseconds)
  SERVO.attach(PIN_PWM_S, 1000, 2000); // (pin, min pulse width, max pulse width in microseconds)
```
- **Función para el control de los dos motores del hoverboard.**

```
void update_vehicle()
// Función que transforma los datos de input de la aplicación y algoritmo de Openbot (conducción autónoma)...
//...en output para definir los parámetros de funcionamiento de los 2 motores (que irán al microcontrolador PWM-BLDC).
{
#if (OPENBOT == RC_CAR)
  //update_throttle();
  //update_steering();
  motors_v2();

```

```
void motors_v2()
// Las variables de entrada son: ctrl_left y ctrl_right.
// El valor de estas 2 variables depende del algoritmo de IA que intenta seguir a la persona mediante la app y la cámara del smartphone.
// Dependiendo de a donde quiera ir el robot, variará el valor de ctrl_left y ctrl_right.
{
  if(ctrl_left<0 && ctrl_right<0)
  // La función digitalWrite y el HIGH/LOW se utilizan para definir el sentido de giro del motor.
    {
      digitalWrite(PIN_PWM_T, HIGH);
      digitalWrite(PIN_PWM_S, HIGH);
    }
  else if(ctrl_left<0 && ctrl_right>0)
    {
      digitalWrite(PIN_PWM_T, LOW);
      digitalWrite(PIN_PWM_S, HIGH);
    }
  else if(ctrl_left>0 && ctrl_right<0)
    {
      digitalWrite(PIN_PWM_T, HIGH);
      digitalWrite(PIN_PWM_S, LOW);
    }
   else if(ctrl_left>0 && ctrl_right>0)
    {
      digitalWrite(PIN_PWM_T, LOW);
      digitalWrite(PIN_PWM_S, LOW);
    }
  // La función .write, que es parte de la librería 'Servo.h', tiene como input el valor de la velocidad para el servo definido en la variable (ESC Y SERVO).
  // La función .write, junto con el resto de funciones de la librería 'Servo.h', realizará a posteriori la conversión a parámetros que un motor tipo PWM pueda procesar.
  // La señal PWM llegará al microcontrolador PWM-BLDC, que finalmente mandará la señal a los motores BLDC del hoverboard.
  // En este caso, dependiendo de la magnitud de las variables de entrada(ctrl_left, ctrl_right), la velocidad asignada a cada motor será directamente proporcional.
  ESC.write(fabs(ctrl_left));
  SERVO.write(fabs(ctrl_right));
}

```
### Test y Resultados
El siguiente paso lógico del proyecto hubiera sido realizar un test físico del programa, cableando para ello las conexiones necesarias entre la aplicación Openbot (smartphone), Arduino y el hoverboard.

No obstante, un percance con la batería del hoverboard, que no hacía llegar el voltaje y la intensidad necesaria para hacer mover los motores correctamente, sumado al tiempo limitado de las clases (6 días), nos obligó a desarrollar una alternativa que fuera viable en un espacio de tiempo tan corto.

## Caso adaptado
Ante la negativa de encontrar alguna alternativa para suministrar la corriente necesaria a los motores del hoverboard, la opción desarrollada se ha basado en sustituir los motores del hoverboard por un sencillo servomotor. Al contrario que los motores del hoverboard, este servomotor solo necesita una alimentación de 5V, que puede ser obtenida de una salida USB de cualquier ordenador.

![Servomotor estandar MG996R](https://github.com/IgorIrastorza/hoverboard_AGV/blob/3992450b49c1ca5266d7ec051343d350a34fb1b0/media/servo_MG996R.jpg)

A continuación se detalla todo el proceso seguido para testear el código desarrollado en arduino para integrarse con la aplicación móvil Openbot.

### Esquema eléctrico
El primer paso ha sido realizar todo el cableado y las conexiones entre el smartphone, el arduino y el servomotor. Para ello, se ha dispuesto de soldador automático de estaño, así como todo el cableado necesario.

En el siguiente esquema se resumen las conexiones realizadas, donde se visualiza lo siguiente:
- Alimentación de 5V del servomotor. En la realidad fue realizado utilizando la corriente de una salida USB de un ordenador portatil.
- Tierra o GND tanto del servomotor como del arduino.
- Pin PWM del arduino (A0 en este caso) conectado al cable amarillo del servo para el control de posición del motor.
- Alimentación del arduino mediante la entrada USB/serial, que está conectada a un smartphone (no es posible visualizarlo en el esquema eléctrico debido a limitaciones de software).
- Comunicación via puerto serial con el smartphone, para poder recibir toda la información de la aplicación Openbot y los algoritmos de control del robot que se ejecutan en el terminal (no es posible visualizarlo en el esquema eléctrico debido a limitaciones de software).

![Esquema eléctrico](https://github.com/IgorIrastorza/hoverboard_AGV/blob/3866a8efe609d6766f5d04f69e32774e34f25686/media/EE_OpenBot-Arduino-Servo.jpg)

Si se dispone del software Fritzing, es posible abrir directamente el esquema eléctrico, que se encuentra disponible en el repositorio del presente proyecto, en la carpeta `esquemas_electricos`.

Para finalizar con el apartado, se muestran varias imagenes de como quedaron las conexiones anteriormente detalladas en la realidad:

![Conexiones test 1](https://github.com/IgorIrastorza/hoverboard_AGV/blob/765d9a26d44badcaf252b4b7a6609b94b7feed6a/media/EE_test_1.jpg)

![Conexiones test 2](https://github.com/IgorIrastorza/hoverboard_AGV/blob/765d9a26d44badcaf252b4b7a6609b94b7feed6a/media/EE_test_2.jpg)

![Conexiones test 3](https://github.com/IgorIrastorza/hoverboard_AGV/blob/765d9a26d44badcaf252b4b7a6609b94b7feed6a/media/EE_test_3.jpg)

### Código de programación en Arduino
La principal diferencia entre los motores del hoverboard (BLDC) y el servomotor estandar utilizado en el test reside en que el primero es un servo de rotación continua (gracias al microcontrolador utilizado), en el que la señal que se envía al microcontrolador controla la velocidad de giro. Al contrario, en el servo utilizado en el test (servo convencional), la señal que se envía al servo controla la posición angular, que viene dada en grados.

Es por ello que, en la función desarrollada `void motors_v2()`, el valor de lo que las funciones `.write()` reciben como input pasará de ser velocidad a ser posición angular del servo.
```
  ESC.write(fabs(ctrl_left));
  SERVO.write(fabs(ctrl_right));

```
Como todo el código esta pensado para que el valor recibido en la función `.write()` sea la velocidad, al hacer el cambio a la posición angular puede que los movimientos del servomotor estandar no sean del todo lógicos.

Además, al haber conectado solo un pin al servo (A0 en este caso), solo funcionará la variable de servo `ESC`, que está asignada a la variable de pin `PIN_PWM_T`.

No obstante, siendo conocedores de las limitaciones del servo estandar utilizado, se ha decidido no realizar ninguna modificación al programa arduino elaborado inicialmente, ya que supondría modificar toda la lógica tanto del código de la app móvil como la del arduino. Esta decisión se basa en que el objetivo final del proyecto (aunque al final sea tratado en este trabajo) es que el código funcione en el hoverboard, y no en un servo estandar. Por ello, las pruebas se destinarán a confirmar en que las comunicaciones se realizan correctamente entre los distintos elementos y que la aplicación móvil Openbot funciona correctamente.


### Test y Resultados
Por tanto, el test se ha limitado a validar que todas las comunicaciones entre el smartphone, arduino y servomotor son realizadas correctamente, así como que el algoritmo de redes neuronales ejecutado en el smartphone es capaz de detectar personas y mandar inputs de ordenes para el movimiento de los motores al arduino.

En conclusión, los resultados del test han sido un exito, consiguiendo que el servo responda y se mueva ante los movimientos de personas que detecta la aplicación Openbot a traves de la cámara del smartphone donde se ejecuta el programa. En los siguientes enlaces se pueden descargar los 2 videos de los test realizados:

- [Video 1](https://github.com/IgorIrastorza/hoverboard_AGV/blob/ddbe54801d81005c55cb873a7e9e9c9af46892c5/media/video_test1.mp4).
- [Video 2](https://github.com/IgorIrastorza/hoverboard_AGV/blob/ddbe54801d81005c55cb873a7e9e9c9af46892c5/media/video_test2.mp4).


## Captura y monitorización de datos

## Captura de datos

La estructura de captura de datos propuesta se basa en generar registros discretos de datos que serán leidos periodicamente.  Para esto en primer luegar se tiene un programa (.ino) que se carga en arduino y que permite que este lea desde los sensores de:

- Sensor de presión: LPS22HB barometrico con rango de medición entre 260 to 1260 hPa. 

- Unidad de medición inercial LSM9DS1 que tiene un acelerómetro,  giroscopio y magnetómetro 3D que permiten detectar la orientación, el movimiento o las vibraciones en el proyecto.

El primer paso es inicializar una conexión serial entre el sensor y el Arduino para enviar los datos leídos, la comunicación entre el Arduino y los sensores se establece en 9600 baud según lo que se indica en los ejemplos establecidos para cada librería. Posterior a esto, se inicializan los sensores y se crean las variables que almacenaran los valores leídos desde los sensores. 

```
#include <Arduino_LSM9DS1.h>
#include <Arduino_LPS22HB.h>

void setup(){
	Serial.begin(9600); 
	
	while (!Serial) {
		; //Espera hasta que el monitor conecte
	}
	// Inicializar IMU
	if (!IMU.begin()) { 
		Serial.println("Fallo al inicializar IMU!");
		while (1);
	}
	
	//Inicializar el sensor de presión
	if (!BARO.begin()) { 
		Serial.println("Fallo al iniciar el sensor de presión!");
		while (1);
	}

	float accel_x, accel_y, accel_z;
	float gyro_x, gyro_y, gyro_z;
	float mag_x, mag_y, mag_z;
	float Pressure;

	void loop() {
	
	// Valores captados por el acelerometro
	if (IMU.accelerationAvailable()) {
		IMU.readAcceleration(accel_x, accel_y, accel_z);
	}
	
	// Valores captados por el giroscopio
	if (IMU.gyroscopeAvailable()) {
	IMU.readGyroscope(gyro_x, gyro_y, gyro_z);
	}
	
	// Valores captados por el magnetometro
	if (IMU.magneticFieldAvailable()) {
		IMU.readMagneticField(mag_x, mag_y, mag_z);
	}

	// Valores captados por el sensor de presión	
	Pressure = BARO.readPressure();
}
```

Finalmente, se agregará un retraso de 1 segundo con la función ```delay()``` para permitir una lectura adecuada. Es importante destacar que este valor se puede modificar de acuerdo a la frecuencia de medición que usuario deseé. 

Una vez que se tiene la rutina cargada en el arduino y este enviado datos, se utilizan dos fichero python que permiten leerlos directamente desde el puerto serial y almacenarlos. Para este último paso, dentro de la lógica se ha incluido una variable con la cual el usuario puede controlar cuantos registros necesita. A continuación se explica de forma detallada la estructura y funcionamiento de cada programa realizado con Python. 

1. serialExcel.py: 

En este fichero se crea una clase que tendrá como objetivo leer los valores que el arduino esta tomando del sensor y posteriormente comunicando via puerto serie. Para  el uso de esta libreria será necesario contar con:

| Libreria | Comando            |
| -------- | ------------------ |
| serial   | pip install pyserial |
| xlwt     | pip install xlwt   |

En primer lugar se crea la clase ```SerialExcel()```, a la cual se le ingresara como paramétros de entradas las siguientes variables: 

- puerto: Variable en la cual se debe almacenar en formato texo el puerto serie al cual se ha conectado el arduino.

- velocidad: Variable numerica en la cual se debe indicar el baud rate con el que el arduino se esta comunicando con el sensor

Mediante la libreria "xlwt" se crea un "Workbook de excel" con la hoja "Datos" y una primera columna "Fecha Hora". 

```
class SerialExcel:
	def __init__(self,puerto,velocidad):
		self.puerto = puerto
		self.velocidad = velocidadself.wb = xlwt.Workbook()
		self.ws = self.wb.add_sheet("Datos",cell_overwrite_ok=True)
		self.columns = ["Fecha Hora"]
		self.number = 100
```

Posteriormente se crean dos funciones bajo las cuales se configuraran las columnas que deberá tener el archivo y la cantidad de registros que queremos leer. 

```
def setColumns(self,col):
	self.columns.extend(col)
	
def setRecordsNumber(self,number):
	self.number = number
```

Finalmente, una tercera y cuarta función donde la primera es utilizada para configurar la carga y el formato del registro leido desde el puerto serie, y la última para escribir y guardar el excel en la ruta desde donde se ejecutarán los ficheros. 
```
def readPort(self):
ser = serial.Serial(self.puerto, self.velocidad, timeout=1)
	c = 0
		for col in self.columns:
		self.ws.write(1, c, col)
		c = c + 1
	self.fila = 2
	
	i = 0
	while(i<self.number):
		line = str(ser.readline())
		if(len(line) > 0):
			now = datetime.now()
			date_time = now.strftime("%m/%d/%Y, %H:%M:%S")
			line = line.strip('\r\n')
			print(date_time,line)
			if(line.find(",")):
				c = 1
				self.ws.write(self.fila, 0, date_time)
				columnas = line.split(",")
				for col in columnas:
					self.ws.write(self.fila, c, col)
					c = c + 1
			i = i + 1
			self.fila = self.fila + 1
```

2. lectura.py:

A partir de la importación del fichero anterior, se realiza una llamada a la clase SerialExcel() donde se le debe indicar el puerto y el baud rate configurado.
```
from serialExcel import SerialExcel

# Indicar Puerto serie de conexión y baud rate
puerto = "COM17"
baud = 9600

serialExcel = SerialExcel(puerto,baud)
```

En segundo lugar se debe indicar, las columnas y el número de datos que se quiere registrar. 
```
columnas = ["Nro Lectura","Valor"]
serialExcel.setColumns(["Nro Lectura","Valor"])
serialExcel.setRecordsNumber(10)
```

Finalmente, se utiliza el comando ```.readport()``` creado en la clase ```SerialExcel()``` para iniciar la lectura y luego crear el archivo excel. 
```
serialExcel.readPort()
serialExcel.writeFile("DataImportada.xls")
```

## Conclusiones y líneas futuras

Respecto a la metodología planteada para la captura y almacenamiento de datos, es posible concluir lo siguiente:

- Se ha desarrollado una lógica sencilla y funcional de leer los datos que el arduino pueda transmitir de forma discreta y con una priodicidad configurable por el usuario. 

- La metodología es sencillad de implementar y no se requieren conocimientos avanzados para reconfigurar si se quiere variar la frecuencia de captura.

- Los datos son almacenados en ficheros excel que son facilmente explotables para obtener información relevante a partir de ellos. 

- Si bien actualmente no se plantea una visualización en línea de los registros, es posible conseguirlo modificando el código y por ejemplo, generar un programa que lea constantemente los datos que estan entrando y los suba directo a una base de datos. Para esto se debe tener en consideración, la frecuencia con la que se reciben los datos y cada cuanto tiempo el programa python deberá captarlos. 
