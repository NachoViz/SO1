## Planificación

▶ El objetivo de la planificación es repartir el tiempo del
procesador entre los diferentes procesos de una manera
que cumpla con los requerimientos del sistema, tales
como, tiempo de respuesta, productividad, o eficiencia en
el uso del procesador, etc.
**La funcion del planificador**
▶ Repartir el tiempo del procesador de manera justa entre los procesos
▶ Prevenir la `inanición` de los procesos
▶ Tener `poco overhead`
▶ Tener la opcion de `priorizar` algun proceso cuando sea necesario

## Planificación de Largo Plazo
▶ La planificación de largo plazo controla la creación de
procesos, es decir que controla el grado de
multiprogramación
El planificador debe tomar 2 decisiones: 
▶ `Cuándo` el SO puede crear nuevos procesos 
				- Depende del grado deseado de multiprogrogramacion
▶ `Cuál` de los trabajos a realizar se convertirá en el próximo proceso
				- La segunda decisión dijimos que involucra cual de los programas disponibles será convertido en un proceso
						▶ FIFO
						▶ Prioridades
						▶ Tiempo esperado de ejecución

## Planificación de Mediano Plazo
▶ Afecta las decisiones de suspensión de procesos o llevar procesos a memoria principal se toman en función de la necesidad de controlar también el grado de multiprogramación

## Planificación de Corto Plazo
Tambien llamado `scheduler`
Se ejecuta cada vez que ocurre algun evento, por ejemplo
	▶ Interrupciones de reloj
	▶ Interrupciones de E/S
	▶ Llamadas al sistema operativo
	▶ Señales, como semáforos por ejemplo
▶ La primera división con respecto al criterio puede ser entre `requerimientos de usuario` y `requerimientos del sistema`

### Orientados a usuario
#### Desempeño
▶ Tiempo de retorno, es el tiempo transcurrido entre la generación de un proceso y su finalización. Incluye todas las esperas
▶ Tiempo de respuesta, para un proceso interactivo es el tiempo entre que el usuario genera un pedido y comienza a recibir la respuesta
▶ Límites, en los casos en los que se puede definir un límite de tiempo el planificador debe maximizar el porcentaje de procesos que cumplen con los límites
#### Otros
▶ Consistencia, un mismo proceso debería volver a correr en aproximadamente la misma cantidad de tiempo independientemente de la carga del sistema

### Orientados al sistema
#### Desempeño
▶ Productividad, la política de planificación debe tratar de
maximizar la cantidad de trabajos completados por unidad
de tiempo
▶ Utilización, en algunos casos en los que el costo es
significativo entonces la carga del procesador es un
parámetro importante. Mantener el procesador ocupado
#### Otros
▶ Equitatividad, siendo todas las condiciones iguales todos
los procesos deberían ser tratados por igual y ningún
proceso debe sufrir inanición
▶ Prioridades, en caso de existir el planificador debe
respetar las prioridades
▶ Recursos, en general el planificador debe mantener
ocupados todos los recursos del sistema sin saturarlos

### Parametros
▶ `w` representa el tiempo que el proceso ha estado en el
sistema hasta el momento, esperando
▶ `e` representa el tiempo que el proceso ha estado
ejecutándose hasta el momento
▶ `s` representa el tiempo total de servicio requerido por el
proceso

El modo de decisión puede ser no preferente o preferente
▶ `Preferente` El proceso actualmente en ejecución puede ser interrumpido y llevado nuevamente al estado de listo
▶ `No preferente` En este caso una vez que un proceso se está ejecutando solo se detiene si termina o si se bloquea

### First-Come-First Served (FCFS)
	El primero en llegar es el primero en servir
	▶ Sistema de cola puro
	▶ No preferente
	▶ Favorece procesos intensivos en el uso del CPU. Un proceso corto debe esperar mucho en relación con su tiempo de ejecución

### Round Robin (RR) q=1 
▶ Preferente basado en un reloj que interrumpe la ejecución
▶ El proceso interrumpido es puesto de vuelta en la cola de listos y se ejecuta el próximo proceso en la cola

### Efecto del Tiempo entre Interrupciones

### Shortest Process Next (SPN) -> Primero el proceso mas corto
▶ No preferente
▶ Se elige para ejecutar el proceso con el tiempo de procesamiento esperado mas corto
▶ Puede haber inanición de los procesos mas largos
▶ Hay que estimar la duración de cada proceso

### Shortest Remaining Time (SRT) -> Primero Menor Tiempo Restante
▶ Versión preferente del anterior
▶ Hay que estimar el tiempo restante de ejecución de cada proceso

### Highest Response Ratio Next (HRRN) -> Primero el de Mayor Tasa de Respuesta
▶ Elige el proceso con mayor R : $ R = (w+s)/s $

### Realimentado
▶ Penaliza gradualmente aquellos procesos que hace más
tiempo que se están ejecutando
▶ Con la llegada de nuevos procesos puede producirse
inanición de procesos antiguos

### Planificacion Justa 

## Tiempo de Respuesta
 ...

