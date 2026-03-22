#Notas clase 6
##Soportes de la Memoria Virtual

1. Si durante la ejecución se hace referencia a una página
que no está en la memoria entonces el hardware genera
una interrupción de fallo de memoria

##Buffer de Traducción Adelantada
1. Cada referencia a memoria puede generar dos accesos
consecutivos a memoria
▶ El primero para obtener la entrada de la tabla de páginas
▶ El segundo para obtener el dato que se necesita
2. Si no encuentra la direccion virtual en la memoria principal va a la secundaria

##Cual es el tamaño correcto de la página?
▶Reducir el tamaño de la página incrementa el número de
páginas por proceso, lo que a su vez incrementa el tamaño
de las tablas de página
▶ Si las tablas son muy grandes es posible que parte de
ellas no se encuentren siempre en memoria
	
	Arquitectura Min Max
        32-bit x86  4KiB 4MiB
        x86-64      4KiB 1GiB
	ARMv7       4KiB 16MiB

##Segmentación
▶ La segmentación permite al programador ver la memoria
dividida en múltiples espacios de direcciones o segmentos
▶ Los segmentos pueden cambiar de tamaño de manera
dinámica

##Segmentación y Paginación
▶ El espacio de direcciones es dividido en segmentos a
conveniencia del programador
▶ A su vez cada segmento es dividido en páginas
▶ Si un segmento es menor que una página entonces
ocupará una página completa
▶ El sistema combina ambos métodos de división para
direccionar la memoria

##Políticas de Ubicación y Reemplazo
▶ La política de reemplazo debe decidir que página
reemplazar cuando toda la memoria esta ocupada y hay
que cargar una nueva página
▶ Es importante considerar 2 conceptos distintos al analizar
esta política, el manejo del `conjunto residente` por un lado
y luego la `política de reemplazo` en sí
▶ El objetivo fundamental es lograr que la página elegida
para ser reemplazada sea aquella con la menor
probabilidad de ser requerida en el futuro
▶ Considerando el principio de cercanía se asume que
aquellas páginas que no han sido referenciadas desde
hace tiempo son aquellas con menor probabilidad de ser
referenciadas en el futuro

###Algoritmos mas comunes utilizados
1. Óptimo -> no funciona
2. Usado menos recientemente (LRU) -> costosa y dificil de impl.
3. FIFO  -> menos costosa y facil de implementar 
4. Reloj -> Se asocia un bit de uso

##Manejo del Conjunto Residente
▶ Con memoria virtual no es necesario tener todas las
páginas de un proceso en memoria simultáneamente. Tampoco es posible
` El conjunto de páginas de un proceso en memoria principal es el conjunto residente `
▶ Mientras menos memoria se le asigna a un proceso se
pueden mantener mas procesos en memoria simultáneamente
▶ Si un proceso tiene un conjunto residente muy pequeño
entonces la probabilidad de un fallo de página es mayor

###Dos opciones para definir la cantidad de marcos de cada proceso
▶ `Asignación Fija:` El número de marcos se define al
cargarse el proceso y luego no puede variar. La cantidad
exacta puede estar definida a nivel general o por el tipo de
proceso. Cuando hay un fallo de página se utiliza uno de
los marcos del propio proceso. El tamaño del conjunto
residente es constante
▶ `Asignación Variable:` El tamaño del conjunto residente es
variable, en principio variará en función de la frecuencia de
fallos de página del proceso. Es mas complejo por la
necesidad de llevar la estadística para cada proceso

1. Asignación Fija + Reemplazo Local (Fixed / Local)
Concepto: El proceso recibe una cantidad exacta e inamovible de marcos al iniciar. Si se queda sin espacio, obligatoriamente tiene que expulsar una de sus propias páginas para hacer lugar.

Ejemplo concreto: Imagina que ejecutas un proyecto en Java y el SO le asigna estrictamente 100 marcos. Si el programa necesita cargar la página 101, tiene que buscar entre sus propios 100 marcos y reemplazar la página que considere menos útil. No le puede robar memoria a otros programas.

2. Asignación Fija + Reemplazo Global (Fixed / Global)
Concepto: No es posible (Not possible).

¿Por qué?: Representa una contradicción lógica. Si la regla es que tu programa tiene exactamente 100 marcos (Fija), pero le permites robarle un marco a otro proceso de la memoria (Global), tu programa pasaría a tener 101 marcos. Eso rompería la regla de que su tamaño debe ser fijo.

3. Asignación Variable + Reemplazo Local (Variable / Local)
Concepto: El SO evalúa el rendimiento del proceso cada cierto tiempo. Si nota que el proceso necesita más memoria, le aumenta su límite total de marcos; si no la usa, se la reduce. Sin embargo, en el instante exacto en que ocurre un fallo de página, el proceso solo reemplaza páginas dentro de su propia asignación actual.

Ejemplo concreto: El SO nota que tu base de datos SQL está generando demasiados fallos de página, por lo que decide aumentarle su cuota de 50 marcos a 80 marcos. A partir de ese momento, si la base de datos se llena, el algoritmo de reemplazo elige a una "víctima" únicamente entre esas 80 páginas que le pertenecen.

4. Asignación Variable + Reemplazo Global (Variable / Global)
Concepto: Es la más dinámica y la que usan la mayoría de los sistemas operativos modernos. Cuando un proceso necesita cargar una página, el SO busca al mejor candidato para reemplazar en toda la memoria RAM, sin importar a qué programa le pertenezca.

Ejemplo concreto: Tienes abierto tu entorno de programación y un videojuego de fondo. El entorno necesita cargar un nuevo módulo. El SO busca en toda la RAM, detecta que hay páginas del videojuego que llevan mucho tiempo sin usarse, se las quita y se las da al entorno. El tamaño de tu entorno creció (asignación variable) a expensas de achicar el espacio del juego (reemplazo global).

##Políticas de Vaciado
En que momento escribir a disco páginas que han sido
modificadas
▶ Vaciado a demanda: La página modificada se escribe
cuando a sido elegida para ser reemplazada o
sobreescrita
▶ Vaciado previo: Las páginas modificadas se escriben en
grupos independientemente si han sido elegidas para ser
reemplazadas o no
Estas dos opciones tienen algunos problemas por ejemplo a
demanda puede generar dos esperas de página antes de
cargar la requerida. El vaciado previo puede escribir páginas
innecesariamente
▶ `Buffering:` Se arman dos listas una de páginas modificadas
y otra de páginas sin modificar. Solo serán reemplazadas
aquellas de la segunda lista

##Control de Carga
Se refiere al control de la cantidad de procesos residentes en
la memoria principal
▶ Cuando la cantidad de procesos es baja entonces la
probabilidad de que todos esten bloqueados es alta
▶ Cuando la cantidad de procesos es muy alta entonces los
conjuntos residentes de cada uno de los procesos es muy
pequeño
▶ Pero en este caso los conjuntos residentes pueden ser
demasiado chicos lo que genera hiperpaginación

##Suspencion de Procesos
▶ Proceso con la menor prioridad
▶ Proceso con fallo de página
▶ Proceso con el conjunto residente mas pequeño
▶ El proceso con mayor conjunto residente, libera la mayor
cantidad de páginas
▶ El proceso con la mayor ventana de ejecución restante
