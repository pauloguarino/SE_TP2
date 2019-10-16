# TP2 de Sistemas Embebidos

## statecharts_bare_metal

El programa comienza de forma similar a los analizados en el TP1. Se hace la inicialización general con boardConfig(), se configura
el tickrate a 1 por milisegundo, y con tickCallbackSet se setea la función de interrupción por tick, myTickHook, que lo único que
hace es setear el flag SysTick_Time_Flag como verdadero. La variable statechart es de tipo Prefix, la estructura de datos que usa
la máquina de estados, y está definida en el archivo "Prefix.h", generado a partir de "prefix.sgen". La función prefix_init
inicializa la máquina de estados asociada a la variable statechart asignándole a todos los elementos del atributo stateConfVector
el valor Prefix_last_state, uno de los 3 estados posibles según el siguiente enum definido en "Prefix.h":
```
typedef enum
{
	Prefix_last_state,
	Prefix_main_region_APAGADO,
	Prefix_main_region_ENCENDIDO
} PrefixStates;
```
Además deja los estados como si no hubiese habido ningún evento.

El programa consiste en un ciclo infinito en el que con cada interrupción se llama a prefixIface_raise_evTick(&statechart) que
activa el evento evTick, y después se ejecuta el ciclo de estados al llamar a prefix_runCycle(&statechart). Esta función recorre
el vector stateConfVector, un atributo de la estructura prefix, de la variable statechart, y según el estado en que se encuentra cada
elemento, se llama a la función main_region_APAGADO_react(handle, bool_true) o main_region_ENCENDIDO_react(handle, bool_true).
Yendo más profundo se llega a la función prefixIface_opLED que es la que, dependiendo de los argumentos, prende o apaga el LED. Esta
función está definida en el main y llama a gpioWrite para cambiar el estado del registro del LED seleccionado para toggearlo.