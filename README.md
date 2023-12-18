# Active-noise-cancellation-simulation
Simulation of a control system of active noise cancellation.

## Introducción
La cancelación de ruido tiene distintas aplicaciones, el principio de funcionamiento de esta metodología de control es el fenómeno de interferencia destructiva y superposición lineal de onda. La idea es generar una señal idéntica a la señal de ruido que se propaga en el entorno acústico sobre el cual vamos a estar trabajando.  

Controlar y, en su caso, reducir el ruido es un reto tecnológico importante, por la complejidad temporal y espacial que presenta. La reducción del ruido implica además un considerable coste económico, por lo que en numerosas ocasiones se prefiere disminuirlo a niveles aceptables para la población que los padece, en lugar de cancelarlo completamente. Este desarrollo se va encargar de implementar los conceptos investigados para el control activo de ruido y sus funcionalidades.

Por control activo de ruido acústico (CAR) se entiende aquella técnica capaz de atenuar el ruido acústico existente en un determinado recinto, mediante la emisión de otro ruido acústico; esto es posible cuando las ondas de presión acústica de ambos ruidos están en oposición de fase. 

![image](https://github.com/Leon7reynosa/Active-noise-cancellation-simulation/assets/37784693/9d2f24b0-3156-4a63-b7b6-903b6ed96685)

En cuanto a nuestra señal que vamos a estar utilizando en el proyecto, vamos a trabajar en profundidad con ondas acústicas, que es un fundamento principal para comprender los principios detrás de la cancelación de ruido. Varios conceptos acústicos son clave para la implementación exitosa de sonido en un contexto de sistemas de cancelación activa de ruido (ANC). 

Este sistema de control va a trabajar con las distintas procedimientos que estuvimos desarrollando a lo largo del cuatrimestre, enfoncandonos en realizar un sistema ed lazo cerrado capaz de controlar la variable de sonido mencionada anteriormente. La estructura de este sistema de control va a estar representada por el siguiente diagrama de bloques:

<center><img src="https://github.com/Leon7reynosa/Active-noise-cancellation-simulation/assets/37784693/3bfe0e3b-14fa-4d6e-9695-bac9e3c3e484" width="850"/></center>

## Objetivo y alcance
Para este proyecto vamos a realizar la simulación de un sistema de control de audio para llevar a cabo la cancelación activa de ruido, este sistema de control va a poder ser aplicados en distintos ambitos. Para tener una visualización más clara de la simulación, vamos a representar un sistema de cancelación de ruido de unos auriculares con ANC incorporado. Esta simulación se basará en el diagrama de control que se visualiza anteriormente, y para realizar este desarrollo vamos a implementar los conceptos estudiados en la materia de Teoría de control, dictada por la Universidad Tecnologica Nacional.  
  
El objetivo principal va a ser lograr obtener las ondas senoidales de sonidos opuestas de distintos ruidos ambientes, estos sonidos ambientes los vamos a obtener de un dataset de acceso público.  
Al realizar este proyecto nos vamos a encontrar con ciertas limitantes que van a acotar el alcance del proyecto. A continuación se listan los puntos limitantes que dan lugar al alcance del trabajo:  
- Vamos a representar la medición como una mezcla del sonido simulado y una entrada de sonido ambiente.
- Los parametros del algoritmo LMS pueden mejorar o empeorar la cancelación de ruido. Actualmente se toman parametros determinados aptos para la simulación hecha en el contexto en el cual me encuentro trabajando.

## Implementación

### Entrada
Lo primero que vamos a realizar, va a ser generar una entrada la cual va a ser la salida esperada de nuestro sistema. Luego a esta entrada vamos a introducir las señales captadas por el microfono de sonido ambiente, que generarán ruido en nuestras ondas de sonido de entrada.  

Para generar el sonido simulado vamos a tener que definir una frecuencia, duración, frecuencia de muestreo y amplitud del sonido. Para este trabajo vamos a definir estos valores de la siguiente forma:
- La frecuencia será de 25Hz para poder visualizar limpiamente los datos en los distintos graficos
- La duración de los sonidos con los que trabajaremos será de 1 segundo.
- La frecuencia de muestreo se define según el teorema de Nyquist, donde la frecuencia máxima con la que trabajaremos será de 5000 Hz (frecuencia de audición más sensible para los humanos).
- La amplitud del sonido será de 2.

![image](https://github.com/Leon7reynosa/Active-noise-cancellation-simulation/assets/37784693/bc954404-87f2-4255-b157-c15d01a50b80)

La entrada si bien la mostramos en el dominio continuo, en realidad el sistema está manejandose en el tiempo discreto, ya que nos manejamos con un Array de valores que varian en amplitud.  
A continuación observaremos un gráfico con una submuestra de los valores muestreados en el dominio discreto. Utilizamos una submuestra para poder visualizar más clara la muestra obtenida.

![image](https://github.com/Leon7reynosa/Active-noise-cancellation-simulation/assets/37784693/edee3f87-b476-42c9-86b1-ddf545068d57)

### Medición
Para realizar un sistema de lazo cerrado va a ser fundamental tener un flujo de realimentación, a este flujo de realimentación vamos a agregar un elemento de medición que va a ser en este caso un microfono que escuche la salida de audio del sistema. Para este caso vamos a realizar una simulación de la salida general del sistema, lo que vamos a hacer va a ser obtener mediante el microfono de la computadora una muestra de sonido ambiente, que nos servirá para adicionarla a la onda senoidal simulada para simular la salida con ruido.  
  
Para realizar la muestra de sonido ambiente vamos a utilizar la biblioteca sounddevice. Una vez obtenida la muestra vamos a mezclar ambos sonidos y reproducirlos tanto analogica como graficamente.

![image](https://github.com/Leon7reynosa/Active-noise-cancellation-simulation/assets/37784693/19636d91-c8e8-41ce-a785-7c186efda942)

Le medición realizada por el microfono se realiza mediante el uso de la biblioteca **sounddevice**, el sonido se captura en el dominio del tiempo continuo. El micrfono convierte las variaciones de presión acústica en señales eléctricas que se registran de manera continua en el tiempo.  
En resumen, convertiremos la señal acústica analógica en una señal eléctrica analógica, que luego se discretiza para crear una representación digital y poder trabajar con ella.  

Este proceso de digitalización de señales es comunmente conocido como "conversión analógico a digital", o ADC. En conclusión, el proceso de medición va a tener embebido el **ADC** que nos brindará las señales digitales con las cuales trabajaremos.  

A continuación se muestra la señal medida en el dominio discreto:

![image](https://github.com/Leon7reynosa/Active-noise-cancellation-simulation/assets/37784693/97c8c0b8-512f-4e57-9992-63515a880735)

### Punto suma - Controlador
Para el punto suma del sistema, que va a realizar la señal de error entre la entrada y la realimentación, va a ser en parte nuestro algoritmo de filtrado adaptativo "LMS", que a su vez cumple la función de ser el controlador del sistema.  
  
El algoritmo LMS es un algoritmo adaptativo que se utiliza para ajustar un filtro en tiempo real para minimizar el error cuadrático medio entre la salida deseada y la salida real del sistema. Lo vamos a utilizar para obtener un filtro que nos va a permitir obtener una señal de error, que va a ser necesaria para poder definir como realizar la cancelación de sonido.  
  
El Algoritmo LMS se utilizará en parte como controlador también, y el tipo de control que ejercerá será del tipo PD, proporcional-derivativo. Esto se puede ver reflejado en la forma en que se calcula el arreglo de coeficientes, que sigue la siguiente formula:  

<center><img src="https://github.com/Leon7reynosa/Active-noise-cancellation-simulation/assets/37784693/ad693694-4ee9-4c8c-94f2-edcf3ad45e47" width="800"/></center>

Donde tenemos que Xk representa los valores de la señal de entrada (simulada), Wt el conjunto de coeficientes y ek el error en el instante k-esimo:

<center><img src="https://github.com/Leon7reynosa/Active-noise-cancellation-simulation/assets/37784693/15f61244-6ac8-466c-a422-4131b7f652b8" width="400"/></center>

Como se puede observar, el algoritmo actualiza los coeficientes realizando cambios de forma proporcional al gradiente, definienndo el gradiente como la derivada del cuadrado del error con respecto a cada uno de los coeficientes. Visto de esta forma podemos determinar el mu sería nuestro Kd y Kp sería igual a 1. 

Como se puede observar, mediante el procedimiento del algoritmo LMS pudimos obtener los coeficientes que vamos a aplicar más adelante en el filtrado (Proceso). Del algoritmo LMS podemos destacar que es un algoritmo adaptativo, en este caso utilizamos la señal simulada para poder realizar la definición de los coeficientes, pero si estaríamos trabajando en un sistema dinámico donde la señal deseada sea desconocida, deberemos trabajar de manera diferente.  

Para un sistema dinámico donde la señal deseada sea desconocida, vamos a tener que re-definir la forma en la que se determinan los coeficientes del filtro adaptativo. Para un sistema dinámico vamos a tener que trabajar con retardos, vistos a lo largo de la cursada, donde cada retardo va a utilizarse como un aproximado del valor deseado y de esta forma utilizarse para determinar los coeficientes, podría verse de la siguiente forma:  

<center><img src="https://github.com/Leon7reynosa/Active-noise-cancellation-simulation/assets/37784693/544498c7-b85e-4df5-b3f7-5967ff2d8224" width="600"/></center>

Utilizar estos retardos ocasionará que en primera instancia no tendremos corrección, ya que los coeficientes se mantendrán en 0, pero a medida que avance el tiempo empezaremos a definir los coeficientes.  

### Proceso
Como lo definimos en el diagrama de bloques, el proceso va a estar compuesto por dos partes funamentales:  
- Filtrado
- Altavoz

Con los coeficientes obtenidos en el algoritmo LMS, ahora procederemos a applicar el filtrado para obtener los valores del Array que conforman el sonido cancelado.

### Salida
Para la salida vamos a aplicar el filtro de cancelación de ruido que obtuvimos anteriormente. Una vez hecho esto vamos a tener una nueva onda senoidal definida para el sonido cancelado, que va a ser la mezcla del sonido simulado y el sonido ambiente, pero intentando reducir al máximo posible las ondas de ruido.

![image](https://github.com/Leon7reynosa/Active-noise-cancellation-simulation/assets/37784693/682a036d-e983-4fff-9ac8-7bd47e1f3fc9)

Finalmente vamos a mostrar en un mismo gráfico como quedaron las 3 ondas senoidales en el dominio continuo: 

![image](https://github.com/Leon7reynosa/Active-noise-cancellation-simulation/assets/37784693/e3252cd7-3d1d-4251-a8a1-bb55e9997126)

Esta salida que nosotros estamos emitiendo, lo hacemos mediante el uso de los altavoces de la computadora. Estos altavoces, en conjunto con la implementaciòn de la biblioteca **sounddevice**, conforman el proceso principal que se encargará de producir la salida obtenida luego del sistema de control.  
Incluso cabe destacar que estamos en presencia de una transformaciòn de la variable con la cual trabajamos, estamos transformando la señal perteneciente a un dominio digital, en uno analógico, lo cual significa que estamos en presencia de un **DAC** (Conversor digital-analógico), el cual está embebido dentro del altavoz. 


