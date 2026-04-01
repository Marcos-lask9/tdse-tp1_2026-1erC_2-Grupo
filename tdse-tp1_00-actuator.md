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

### Actuator Statechart - State Transition Table
A continuación, se detalla la tabla de transición de estados que describe el comportamiento del módulo de código C del tipo temporizado (período = 1mS) para la tarea de "actuar". Esta tabla modela una salida física genérica utilizando un LED como representación:

| Current State | Event | [Guard] | Next State | Actions |
| :--- | :--- | :--- | :--- | :--- |
| `ST_ACT_OFF` | `EV_ACT_LED_ON` | | `ST_ACT_ON` | `led = 1` |
| `ST_ACT_ON`  | `EV_ACT_LED_OFF`| | `ST_ACT_OFF`| `led = 0` |

**Explicación lógica de la secuencia:**
1. **Estado Apagado (`ST_ACT_OFF`):** Es el estado inicial. El actuador físico (la barrera, el display, etc.) se encuentra desactivado. Cuando recibe la orden explícita `EV_ACT_LED_ON` desde el módulo System, cambia su estado y ejecuta la acción en el hardware (`led = 1`).
2. **Estado Encendido (`ST_ACT_ON`):** El hardware está activo. Permanece en este estado hasta que el módulo System le envíe la señal `EV_ACT_LED_OFF`. Al recibirla, desactiva la salida física (`led = 0`) y vuelve al estado de reposo.
