### Eventos y Acciones del Modelo Actuator Statechart (Accionar)
A continuación se describen los Eventos y Acciones del modelo **Actuator**, encargado de traducir las decisiones lógicas del sistema central en acciones físicas reales. Para este modelo, utilizaremos un LED como representación de una salida física (*Output*).

#### Eventos (Triggers)
Los eventos que excitan al modelo Actuator son únicamente mensajes lógicos (signals) que provienen del modelo *System*. El Actuador no piensa ni toma decisiones, solo obedece estas órdenes:

* **`EV_ACT_LED_ON`**: Evento (signal) recibido desde el *System* que indica la orden explícita de encender o activar la salida.
* **`EV_ACT_LED_OFF`**: Evento (signal) recibido desde el *System* que indica la orden explícita de apagar o desactivar la salida.

#### Acciones (Effects)
Las acciones del modelo Actuator son las modificaciones justas sobre el mundo físico,  cambiando el estado de los pines del microcontrolador:

* **`led = 1`**: Acción física que pone en estado lógico alto (`HIGH`) el pin de salida, encendiendo el LED. En la máquina real, esto equivaldría a encender el motor de la barrera o la impresora.
* **`led = 0`**: Acción física que pone en estado lógico bajo (`LOW`) el pin de salida, apagando el LED.
