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

### System Statechart - State Transition Table

A continuación, se detalla la tabla de transición de estados que describe el comportamiento del módulo de código C del tipo temporizado (período = 1mS) para la tarea de "procesar". Esta tabla representa la lógica central de la máquina expendedora de tickets, integrando los tres sensores (Bobina, Cámara y Botón):

| Current State | Event | [Guard] | Next State | Actions |
| :--- | :--- | :--- | :--- | :--- |
| `ST_SYS_IDLE` | `EV_SYS_COIL_DOWN` | | `ST_SYS_WAIT_CAM` | `timer = 0` |
| `ST_SYS_WAIT_CAM` | `EV_SYS_CAM_DOWN` | | `ST_SYS_WAIT_BTN` | `timer = 0` |
| `ST_SYS_WAIT_CAM` | `EV_SYS_COIL_UP` | | `ST_SYS_IDLE` | |
| `ST_SYS_WAIT_BTN` | `EV_SYS_BTN_DOWN` | | `ST_SYS_PRINTING` | `raise EV_ACT_PRINTER_ON`; `timer = 0` |
| `ST_SYS_WAIT_BTN` | `EV_SYS_COIL_UP` | | `ST_SYS_IDLE` | |
| `ST_SYS_PRINTING` | `tick` | `[timer >= DEL_SYS_PRINT_MAX]` | `ST_SYS_BARRIER_OPEN` | `raise EV_ACT_BARRIER_OPEN`; `timer = 0` |
| `ST_SYS_BARRIER_OPEN` | `EV_SYS_COIL_UP` | | `ST_SYS_IDLE` | `raise EV_ACT_BARRIER_CLOSE` |
| `ST_SYS_BARRIER_OPEN` | `tick` | `[timer >= DEL_SYS_TIMEOUT]` | `ST_SYS_IDLE` | `raise EV_ACT_BARRIER_CLOSE` |

**Explicación lógica de la secuencia:**
1. **Reposo (`ST_SYS_IDLE`):** La máquina espera. Si la bobina detecta un vehículo (`EV_SYS_COIL_DOWN`), el sistema se prepara para registrarlo.
2. **Esperando Cámara (`ST_SYS_WAIT_CAM`):** El sistema espera a que la cámara capture y valide la patente (`EV_SYS_CAM_DOWN`). Si el auto retrocede y se va antes de la foto (`EV_SYS_COIL_UP`), vuelve a reposo.
3. **Esperando Botón (`ST_SYS_WAIT_BTN`):** Con la patente ya leída, el sistema habilita al conductor a presionar el botón (`EV_SYS_BTN_DOWN`), lo que dispara la orden de imprimir (`raise EV_ACT_PRINTER_ON`). Nuevamente, si el auto se va sin pedir ticket, se aborta la operación.
4. **Imprimiendo (`ST_SYS_PRINTING`):** Se espera un tiempo prudencial (`DEL_SYS_PRINT_MAX`) para la emisión del papel. Al terminar, se ordena abrir la barrera (`raise EV_ACT_BARRIER_OPEN`).
5. **Barrera Abierta (`ST_SYS_BARRIER_OPEN`):** El sistema espera a que el vehículo avance y libere la bobina (`EV_SYS_COIL_UP`) para mandar a cerrar la barrera (`raise EV_ACT_BARRIER_CLOSE`) y volver al inicio. Por seguridad, si pasa mucho tiempo y el auto no avanza (`DEL_SYS_TIMEOUT`), la barrera también se cierra.
