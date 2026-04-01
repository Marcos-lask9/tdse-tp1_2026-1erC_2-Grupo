### Eventos y Acciones del Modelo System Statechart (Procesar)

A continuación se describen los Eventos y Acciones del modelo **System**, encargado de procesar la lógica central de la máquina expendedora de tickets 

#### Eventos (Triggers)
A diferencia de los sensores que leen pines físicos, los eventos del modelo System son únicamente lógicos. Son mensajes asincrónicos (signals) que provienen del exterior (de los distintos módulos Sensor). Al estar ya filtrados (sin rebotes), el System confía plenamente en ellos.

* **`EV_SYS_BTN_DOWN`** (y **`UP`**): Eventos recibidos desde el módulo Sensor que indican que el usuario presionó (o liberó) el botón para pedir el ticket.
* **`EV_SYS_COIL_DOWN`** (y **`UP`**): Eventos recibidos desde el módulo Sensor que indican que la bobina de detección (*Sensor coil*) detectó la llegada (o retirada) de un vehículo.
* **`EV_SYS_CAM_DOWN`** (y **`UP`**): Eventos recibidos desde el módulo Sensor que indican que la cámara detectó (o dejó de detectar) una patente válida para registrar.
* **`tick`**: Evento de reloj del sistema (ocurre cada 1mS), utilizado para temporizaciones internas de la lógica de procesamiento.

#### Acciones (Effects)
Las acciones del modelo System son las respuestas lógicas de la máquina de estados. Pueden ser inicializaciones de variables de control (timers) o el envío de signals (Eventos) para el modelo Actuator:

* **`raise EV_ACT_XX_ON`**: Señal (mensaje) enviada al modelo Actuator indicando que debe encender o accionar una salida digital. Ejemplos: `raise EV_ACT_PRINTER_ON` (imprimir ticket) o `raise EV_ACT_BARRIER_OPEN` (levantar barrera).
* **`raise EV_ACT_XX_OFF`**: Señal (mensaje) enviada al modelo Actuator indicando que debe apagar o detener una salida digital. Ejemplo: `raise EV_ACT_BARRIER_CLOSE` (bajar barrera).
* **`timer = DEL_SYS_XX_MAX`**: Acción interna de inicialización de una variable de control (timer). Se utiliza para establecer tiempos de espera lógicos (por ejemplo, el tiempo que la barrera permanece abierta esperando que el vehículo pase).

