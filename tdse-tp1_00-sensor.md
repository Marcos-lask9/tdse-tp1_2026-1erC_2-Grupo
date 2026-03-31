# Módulo Sensor: Diagrama de Estado y Modelado

## 1. Eventos y Acciones 

Para el modelado del sensor (un solo botón) de la *Parking Ticket Dispenser Machine*, se utilizará la siguiente convención de identificadores y elementos, ejecutados cíclicamente cada 1mS:

### Eventos (*Triggers*)
* **`EV_BTN_XX_UP`**: Lectura física del pin de entrada indicando que el botón NO está presionado .
* **`EV_BTN_XX_DOWN`**: Lectura física del pin de entrada indicando que el botón SÍ está presionado.

### Acciones (*Effects*)
* **`tick = DEL_BTN_XX_MAX`**: Inicialización del temporizador de *debounce* (tiempo correspondiente para estabilizar la señal).
* **`tick--`**: Decremento del temporizador en cada ciclo de ejecución (cada 1mS).
* **`raise EV_SYS_XX_DOWN`**: Mensaje (signal) enviado al módulo `System` indicando que se validó correctamente la pulsación del botón.
* **`raise EV_SYS_XX_UP`**: Mensaje (signal) enviado al módulo `System` indicando que se validó correctamente la liberación del botón.
