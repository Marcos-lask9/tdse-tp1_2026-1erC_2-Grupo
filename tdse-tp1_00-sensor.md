# Módulo Sensor: Diagrama de Estado y Modelado

## Eventos y Acciones 

Para el modelado del sensor (un solo botón) de la *Parking Ticket Dispenser Machine*, se utilizará la siguiente convención de identificadores y elementos, ejecutados cíclicamente cada 1mS:

### Eventos (*Triggers*)
* **`EV_BTN_XX_UP`**: Lectura física del pin de entrada indicando que el botón NO está presionado .
* **`EV_BTN_XX_DOWN`**: Lectura física del pin de entrada indicando que el botón SÍ está presionado.

### Acciones (*Effects*)
* **`tick = DEL_BTN_XX_MAX`**: Inicialización del temporizador de *debounce* (tiempo correspondiente para estabilizar la señal).
* **`tick--`**: Decremento del temporizador en cada ciclo de ejecución (cada 1mS).
* **`raise EV_SYS_XX_DOWN`**: Mensaje (signal) enviado al módulo `System` indicando que se validó correctamente la pulsación del botón.
* **`raise EV_SYS_XX_UP`**: Mensaje (signal) enviado al módulo `System` indicando que se validó correctamente la liberación del botón.




## Sensor Statechart - State Transition Table

A continuación se detalla la tabla de transición de estados para el filtro anti-rebote (debounce) del sensor, ejecutado cíclicamente cada 1mS:

| Current State | Event | [Guard] | Next State | Actions |
| :--- | :--- | :--- | :--- | :--- |
| **ST_BTN_XX_UP** | `EV_BTN_XX_UP` | | `ST_BTN_XX_UP` | |
| | `EV_BTN_XX_DOWN` | | `ST_BTN_XX_FALLING` | `tick = DEL_BTN_XX_MAX` |
| **ST_BTN_XX_FALLING** | `EV_BTN_XX_UP` | `[tick > 0]` | `ST_BTN_XX_FALLING` | `tick--` |
| | | `[tick == 0]` | `ST_BTN_XX_UP` | |
| | `EV_BTN_XX_DOWN`| `[tick > 0]` | `ST_BTN_XX_FALLING` | `tick--` |
| | | `[tick == 0]` | `ST_BTN_XX_DOWN` | `raise EV_SYS_XX_DOWN` |
| **ST_BTN_XX_DOWN** | `EV_BTN_XX_DOWN` | | `ST_BTN_XX_DOWN` | |
| | `EV_BTN_XX_UP` | | `ST_BTN_XX_RISING` | `tick = DEL_BTN_XX_MAX` |
| **ST_BTN_XX_RISING** | `EV_BTN_XX_DOWN`| `[tick > 0]` | `ST_BTN_XX_RISING` | `tick--` |
| | | `[tick == 0]` | `ST_BTN_XX_DOWN` | |
| | `EV_BTN_XX_UP` | `[tick > 0]` | `ST_BTN_XX_RISING` | `tick--` |
| | | `[tick == 0]` | `ST_BTN_XX_UP` | `raise EV_SYS_XX_UP` |
