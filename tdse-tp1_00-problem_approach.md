# Solución de COMA Electronics: Parking Ticket Dispenser Machine (Entry)

El equipo de  COMA Electronics consiste en una máquina que imprime tickets, diseñada para controlar el ingreso automatizado a un estacionamiento.
Su funcionamiento inicia cuando un vehículo se acerca al acceso y es detectado de forma automática por los sensores del sistema. Al detenerse frente a la máquina, el conductor presiona un botón para solicitar el ingreso, o acerca una tarjeta si es un cliente. En ese momento, el equipo imprime y entrega un ticket. Una vez que el usuario retira el ticket, la máquina envía una señal a la barrera para que se levante y permita el paso del vehículo.
Para facilitar la interacción con el usuario, el tótem tiene una pantalla LCD, la cual emite indicaciones por voz para guiar el proceso, y además, dispone de un botón de ayuda, útil en caso de que ocurra algún inconveniente o el usuario necesite asistencia.

# Implementación de la Parking Ticket Dispenser Machine (Entry)

Para llevar a cabo la simulación y el desarrollo del firmware de este equipo, la implementación se basará en una Estructura de Aplicación Modular dividida en tres partes funcionales: escrutar (*Scrutinize*), procesar (*Process*) y actuar (*Act*). Estos módulos operan mediante ejecución periódica para garantizar una respuesta en tiempo real:


**Módulo Sensor** (escrutar): Se encarga de revisar constantemente el entorno y capturar las señales de entrada. Para simular el funcionamiento en el entorno de desarrollo, los sensores reales del equipo comercial (como la cámara, el botón y la bobina sensora) se deben reemplazar por pulsadores o interruptores, los cuales actúan como entradas digitales (*Digital Inputs*).


**Módulo System** (procesar): Este bloque actúa como el núcleo lógico. Toma la información recolectada por el módulo sensor, procesa los datos basándose en el diagrama de estados del sistema y determina qué órdenes deben ejecutarse a continuación.


**Módulo Actuator** (actuar): Es el responsable de ejecutar las respuestas físicas del equipo. Para la simulación, los actuadores reales de la máquina (que incluyen la pantalla, la impresora, la barrera y el servidor) se reemplazan por LEDs, funcionando como salidas digitales (*Digital Outputs*) para indicar visualmente la activación de cada componente.

# Modelos de comportamiento para los módulos de código C

Para describir el comportamiento del código C del tipo temporizado (Update by Time Code, period = 1mS), la lógica se modela estructurando el sistema en los siguientes tres Diagramas de Estado (*Statecharts*):


Para escrutar → **Sensor**: Se utiliza el modelo Sensor Statechart, encargado de adquirir los eventos desde las entradas digitales (pulsadores y llaves On/Off) para enviar los mensajes correspondientes.


Para procesar → **System**: Se utiliza el modelo System Statechart, encargado de recibir los mensajes de los sensores, procesar el estado de la máquina y sincronizar las respuestas del equipo.


Para actuar → **Actuator**: Se utiliza el modelo Actuator Statechart, encargado de recibir las órdenes del sistema y ejecutar el comportamiento no bloqueante sobre las salidas digitales (LEDs).
