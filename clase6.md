# Resumen: Sistemas Operativos I - Soportes de la Memoria Virtual

### 1. Fundamentos de la Memoria Virtual
* [cite_start]**Traducción de direcciones**: Las referencias a memoria son todas direcciones lógicas que el hardware traduce a direcciones físicas durante la ejecución[cite: 5].
* [cite_start]**Estructura no contigua**: El proceso es dividido en pedazos y estos no tienen que estar necesariamente en lugares continuos de memoria ya que gracias a las tablas de página o de segmento el hardware puede encontrar y resolver las referencias a memoria[cite: 6].
* [cite_start]**Conjunto residente**: Solo se carga una porción, conjunto residente y se va cargando el resto a medida que se necesita[cite: 11]. [cite_start]No es necesario que todo el proceso este cargado en la memoria principal para poder ejecutarlo[cite: 9, 10].
* [cite_start]**Ventajas principales**: Podemos tener cargados en memoria más procesos listos para ejecutar[cite: 13]. [cite_start]La imagen de un proceso puede ser más grande que la memoria principal[cite: 14].

### 2. El Fallo de Página (Paso a Paso)
[cite_start]Al iniciarse el proceso solo se cargan en memoria algunas páginas para que el proceso comience a ejecutarse[cite: 16]. Si se necesita una página que no está cargada, ocurre lo siguiente:
1. [cite_start]**Interrupción**: Si durante la ejecución se hace referencia a una página que no está en la memoria entonces el hardware genera una interrupción de fallo de memoria[cite: 17].
2. [cite_start]**Bloqueo**: El sistema operativo toma el control y pone el proceso en estado de bloqueado[cite: 18].
3. [cite_start]**Búsqueda**: El SO encarga al sistema de E/S que busque la página faltante en el disco y que la cargue en memoria[cite: 19].
4. [cite_start]**Despacho**: El SO despacha otro proceso de la cola de procesos listos mientras espera que la página se cargue en memoria[cite: 20].
5. [cite_start]**Reanudación**: Cuando la carga finaliza y el S/O se entera de esto por medio de una interrupción entonces actualiza la tabla de páginas del proceso y lo coloca nuevamente en el estado de listo[cite: 20].

### 3. Rendimiento: Hiperpaginación y Principio de Cercanía
* [cite_start]**Hiperpaginación**: Como siempre cuando el SO necesita traer una nueva página a memoria probablemente sea necesario liberar un marco ya ocupado[cite: 22, 23]. [cite_start]Existe el riesgo de eliminar una página que va a ser necesitada de manera inmediata[cite: 24]. [cite_start]Si esto sucede con mucha frecuencia entonces el sistema pasa mas tiempo cargando páginas a memoria que ejecutando las instrucciones de los procesos listos, esto se conoce como hiperpaginación[cite: 25].
* [cite_start]**Principio de Cercanía**: Observando la ejecución de los programas se ve que por lo general las referencias a los datos y a las instrucciones tienden a juntarse en grupos[cite: 27]. [cite_start]Durante períodos de tiempo relativamente cortos todas las referencias se concentran en unos pocos fragmentos del proceso[cite: 28]. [cite_start]En base a esto es posible hacer una predicción relativamente fuerte sobre cuales son los fragmentos que se necesitarán en el futuro cercano[cite: 29].

### 4. Tablas de Páginas
* [cite_start]**Control del SO**: El SO mantiene una tabla de páginas actualizada para cada proceso que se ha creado[cite: 46]. [cite_start]Cada entrada en la tabla de páginas indica en que marco se encuentra ubicada dicha página[cite: 47, 48].
* [cite_start]**Bits de estado**: Cada entrada en la tabla de páginas indica con un bit si la página se encuentra presente en la memoria o no[cite: 49, 50]. [cite_start]Otro bit indica además si la página ha sido modificada desde que fue cargada en memoria[cite: 51, 52].
* [cite_start]**Tablas multinivel**: Las páginas son relativamente pequeñas[cite: 81]. [cite_start]Como consecuencia las tablas de páginas pueden tener muchas entradas[cite: 82]. [cite_start]Esto lleva a que suele convenir paginar las propias tablas de páginas[cite: 83]. [cite_start]También se implementan tablas de más de un nivel de profundidad[cite: 84].

### 5. Buffer de Traducción Adelantada (TLB)
* [cite_start]**El problema del doble acceso**: Cada referencia a memoria puede generar dos accesos consecutivos a memoria[cite: 105]. [cite_start]El primero para obtener la entrada de la tabla de páginas, el segundo para obtener el dato que se necesita[cite: 106].
* [cite_start]**La solución (TLB)**: Una de las soluciones propuestas para este problema es utilizar un cache para almacenar las referencias a las páginas más utilizadas y de esa manera reducir las búsquedas a memoria[cite: 107].
* [cite_start]**Mecanismo**: Dada una dirección virtual el hardware busca la página referenciada en el TLB, en caso de encontrarla obtiene el número de marco directamente[cite: 108]. [cite_start]Si no la encuentra dentro del TLB entonces la busca en la tabla de páginas y luego de la memoria y actualiza el TLB[cite: 109].

### 6. Tamaño de Página y Segmentación
* [cite_start]**Compromisos del tamaño de página**: Reducir el tamaño de la página reduce la fragmentación interna[cite: 170]. [cite_start]Sin embargo, reducir el tamaño de la página incrementa el número de páginas por proceso, lo que a su vez incrementa el tamaño de las tablas de página[cite: 171]. [cite_start]Si las tablas son muy grandes es posible que parte de ellas no se encuentren siempre en memoria[cite: 172]. [cite_start]Esto puede generar casos de doble fallo de página para un solo acceso a memoria[cite: 173].
* [cite_start]**Segmentación**: La segmentación permite al programador ver la memoria dividida en múltiples espacios de direcciones o segmentos que pueden cambiar de tamaño de manera dinámica[cite: 177]. [cite_start]Simplifica la tarea de compartir funciones o datos al compartir segmentos entre programas y protegerlos[cite: 178, 179].
* [cite_start]**El enfoque combinado**: El espacio de direcciones es dividido en segmentos a conveniencia del programador y a su vez cada segmento es dividido en páginas[cite: 181, 182]. [cite_start]Si un segmento es menor que una página entonces ocupará una página completa[cite: 183]. [cite_start]El sistema combina ambos métodos de división para direccionar la memoria[cite: 184].

### 7. Segmentación y Paginación Combinadas

[cite_start]El sistema operativo puede combinar ambos métodos para direccionar la memoria[cite: 5].

* [cite_start]**Estructura combinada**: El espacio de direcciones se divide en segmentos según la conveniencia del programador, y luego cada segmento se subdivide en páginas[cite: 2, 3].

* [cite_start]**Fragmentación**: Si un segmento es más pequeño que una página, ocupará una página completa[cite: 4].

* [cite_start]**Direccionamiento y Protección**: La dirección virtual incluye número de segmento, número de página y un desplazamiento (offset)[cite: 8, 25, 29, 30]. [cite_start]Este modelo permite aplicar permisos específicos a nivel de segmento, como permitir o denegar referencias a datos o instrucciones de salto entre procesos[cite: 46, 49, 50, 53, 54].



### 8. Políticas de Lectura (¿Cuándo traer la página?)

[cite_start]Define el momento exacto en que una página nueva debe cargarse en la memoria principal[cite: 62].

* [cite_start]**A demanda**: La página se carga únicamente cuando se hace referencia a su dirección[cite: 63]. [cite_start]Al principio genera muchos fallos de página, pero gracias al principio de cercanía, estos disminuyen rápidamente[cite: 64, 65].

* [cite_start]**Paginación previa (Prepaging)**: Ante un fallo de página, el sistema no carga solo la página solicitada, sino también varias páginas consecutivas, aprovechando la eficiencia de los discos magnéticos para lecturas secuenciales[cite: 66, 67].



### 9. Políticas de Reemplazo (¿Qué página sacar?)

[cite_start]Cuando la memoria está llena y llega una nueva página, el sistema debe elegir cuál desalojar[cite: 71]. [cite_start]El objetivo es sacar la página con menor probabilidad de ser requerida en el futuro[cite: 73].

* [cite_start]**Óptimo**: Reemplaza la página que tardará más tiempo en volver a ser referenciada[cite: 82]. [cite_start]Es imposible de implementar en la vida real, pero se usa como estándar para evaluar otros algoritmos[cite: 81, 82].

* [cite_start]**LRU (Menos usada recientemente)**: Reemplaza la página que lleva más tiempo sin ser utilizada[cite: 84, 85, 86]. [cite_start]Es muy eficiente porque respeta el principio de cercanía, pero su implementación es costosa[cite: 87, 88].

* [cite_start]**FIFO (Primero en entrar, primero en salir)**: Funciona como un búfer circular que reemplaza la página más antigua en memoria[cite: 90, 91, 92]. [cite_start]Es fácil de programar, pero su rendimiento suele ser deficiente[cite: 93].

* [cite_start]**Reloj (Clock)**: Usa un "bit de uso" por cada marco, el cual se pone en '1' al cargar o referenciar la página[cite: 96, 97, 98]. [cite_start]Al buscar un reemplazo, el algoritmo busca el primer marco con bit '0'; mientras busca, cambia los bits '1' a '0'[cite: 99, 100, 101].

* [cite_start]**Almacenamiento intermedio (Page Buffering)**: Mantiene dos listas (páginas liberadas y modificadas)[cite: 369]. [cite_start]Esto permite recuperar rápidamente páginas descartadas por error antes de que sean sobrescritas y optimiza las escrituras en disco[cite: 372, 373].



### 10. Manejo del Conjunto Residente

[cite_start]El "conjunto residente" es la cantidad de páginas de un proceso que están cargadas en la memoria principal[cite: 376]. [cite_start]Mientras más pequeño sea, más procesos pueden ejecutarse a la vez, pero un tamaño demasiado pequeño aumenta drásticamente los fallos de página[cite: 377, 378].

* [cite_start]**Asignación Fija**: Se le asigna un número constante de marcos al proceso desde el inicio[cite: 381, 384]. [cite_start]Ante un fallo, el proceso debe reemplazar una de sus propias páginas[cite: 383].

* [cite_start]**Asignación Variable**: La cantidad de marcos crece o se reduce dinámicamente según la frecuencia de fallos de página que tenga el proceso[cite: 385]. [cite_start]Requiere llevar estadísticas detalladas, siendo más complejo[cite: 386].



### 11. Políticas de Vaciado (¿Cuándo escribir a disco?)

[cite_start]Define en qué momento las páginas que fueron modificadas en memoria deben guardarse en el disco duro[cite: 391, 392].

* [cite_start]**Vaciado a demanda**: Solo se escribe en disco cuando la página es elegida para ser reemplazada[cite: 393]. [cite_start]Puede ralentizar el sistema porque genera dos esperas (escribir la vieja y leer la nueva)[cite: 395].

* [cite_start]**Vaciado previo**: Se agrupan páginas modificadas y se escriben periódicamente antes de que necesiten ser reemplazadas[cite: 394]. [cite_start]Su desventaja es que puede escribir en disco páginas que luego volverán a ser modificadas[cite: 396].

* [cite_start]**Buffering**: Combina listas de páginas para asegurar que solo se reemplacen las páginas no modificadas (ya respaldadas), optimizando los tiempos[cite: 397, 398].



### 12. Control de Carga y Suspensión

[cite_start]Regula cuántos procesos residen en la memoria al mismo tiempo[cite: 400, 401]. [cite_start]Si hay demasiados, los conjuntos residentes se achican y se produce *hiperpaginación*[cite: 403, 404]. [cite_start]Para evitarlo, el SO suspende procesos basándose en criterios como[cite: 409, 410]:

1. [cite_start]**Prioridad**: Se suspende al de menor prioridad[cite: 411].

2. [cite_start]**Fallo de página**: Se suspende al que ya está bloqueado esperando una página[cite: 412].

3. [cite_start]**Tamaño**: Se suspende al de conjunto residente más pequeño (menos costoso de volver a cargar) o al más grande (libera más memoria de golpe)[cite: 413, 414].

4. [cite_start]**Ejecución**: Se suspende al que le queda mayor ventana de ejecución[cite: 415].
