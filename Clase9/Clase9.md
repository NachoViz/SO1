## Planificación de Disco en Linux
### Planificador del ascensor
▶ Tiene una cola única para escritura y lectura, siempre ordenada.
▶ Es decir que solo si hay un pedido de R/W en el disco, interviene este planificador.
▶ Los pedidos se hacen por sector (SECTOR 500 , SECTOR 502, SECTOR 2000)
▶ Si el cabezal esta pasado por el SECTOR 2000 y justo llega un pedido del SECTOR 2200, este lo agarra 'de paso' y lo pone el lista.
▶ Se le agregan las siguientes condiciones,
	1. Si un pedido es en el mismo sector o uno adyacente a un pedido que ya se encuentra en la cola entonces estos se funden en uno solo
	2. Si un pedido es lo suficientemente viejo entonces un pedido nuevo será agregado al final de la cola
	3. Si hay un lugar adecuado se inserta el pedido en el lugar correcto de la cola 
	4. Si no hay un lugar adecuado entonces se coloca el pedido al final de la cola
#### Si queres hacer una prueba y ver que pedidos en realtime tiene un PC podes hacer:
 ´sudo apt install iotop´ 
Para luego 
´sudo iotop -o´ 
En Linux podés ver y cambiar el algoritmo de planificación (el "ascensor") en tiempo real.
´cat /sys/block/TU_DISCO/queue/scheduler´
...Para ver tu disco podes tirar el comando ´lsblk´

### Planificador de deadline
▶ Con el ascensor un pedido en una pista lejana puede
tener que esperar mucho si llegan muchos pedidos
cercanos a la posición actual
▶ Otro problema es la diferencia de requerimientos entre
lecturas y escrituras
▶ Con la escritura el proceso no se bloquea pero con la **lectura SI**, ya que no puede continuar sin el dato requerido
▶ El planificador tiene tres colas funcionando al mismo tiempo
	Cola de Ascensor: Ordena los pedidos por posición física (para no gastar el disco).
	FIFO de Lectura: Ordena las lecturas por tiempo de llegada.
	FIFO de Escritura: Ordena las escrituras por tiempo de llegada.
	Pedidos: 	
		▶ Pedidos de Lectura: Suelen tener un límite de 500 ms
		▶ Pedidos de Escritura: Suelen tener un límite de 5 segundos.
#### EN MI DEBIAN
Se pueden ver estos tiempos haciendo 
´cat /sys/block/sda/queue/iosched/write_expire´
### Planificador con anticipacion
▶ Hasta ahora todos los planificadores solo buscan la velocidad de vaciado de las colas, en el caso de la **memoria secundaria** no es la mejor opcion
▶ Suele suceder con pedidos de lectura que la aplicación
genera un pedido nuevo de lectura inmediatamente
despues de recibir los datos del pedido previo
▶ A pesar de esto hay un retardo para el sistema entre que
se satisface una lectura y se recibe el nuevo pedido
▶ En ese tiempo el scheduler comienza a satisfacer otro
pedido en la cola
▶ El planificador espera el tiempo prefijado, si llega un nuevo
pedido de lectura a un sector cercano al actual entonces
se lo sirve inmediatamente

### Planificador completamente justo (CFQ)
▶ Mantiene una cola de E/S para cada proceso
▶ El planificador atiende las colas en round-robin
▶ Los pedidos de cada cola se ordenan
▶ Si una cola queda vacía antes de tiempo espera un poco
antes de pasar a la siguiente (anticipación)

**Round-Robin** es el algoritmo de reparto equitativo por excelencia. En sistemas operativos, es la forma de asegurar que nadie se "quede con el control" del recurso por demasiado tiempo.
Imaginalo como una ronda de mate (o de cualquier bebida en grupo):

### Dispositivos de Estado Sólido
	▶ Ventajas,
		▶ Tamaño
		▶ Peso
		▶ Consumo
		▶ Resistencia
		▶ Velocidad
		▶ Silencio
	▶ Desventajas,
		▶ Precio
		▶ Capacidad
		▶ **Vida útil**

▶ Una consideración es que **las celdas flash** solo pueden escribirse un número limitado de veces antes de fallar
▶ Para superar este inconveniente el controlador del disco mantiene una estadística del uso de las diferentes celdas para que todas se consuman de manera pareja
▶ De esta forma se logran resultados más que aceptables

### Planificador no-op
▶ Mantiene una cola de E/S para cada proceso
▶ El planificador atiende las colas en round-robin
▶ Los pedidos no se ordenan, la cola se comporta como FIFO
▶ Reduce el overhead de CPU

Imagina que tenés que ir a 5 lugares distintos en Córdoba:
	**Con mucho overhead (Planificador del Ascensor/CFQ)**: Te sentás con un mapa, calculás las distancias, ordenás las paradas para gastar menos nafta y decidís el orden óptimo. Gastar ese tiempo pensando es el "overhead".

	**Con poco overhead (No-op**): Agarrás las 5 direcciones y vas a la primera que te dieron, después a la segunda, y así. No perdiste ni un segundo pensando (cero overhead), aunque quizás manejaste más kilómetros.
Se usa principalmente en SSDs y máquinas virtuales

### RAID = {R}edundant {A}rray of {I}ndependent/{I}nexpensive {D}isks
▶ Es un conjunto de discos conectados al sistema que son considerados por este como un solo disco lógico
▶ La información se distribuye entre los diferentes discos físicos
▶ Parte de la capacidad se utiliza para guardar información redundante para lograr resistencia frente a fallos de los discos físicos
▶ El otro objetivo es mejorar la performance del almacenamiento secundario por medio de paralelismo
▶ RAID mejora la velocidad asi como también incrementa la confiabilidad

	RAID 0 (non-redundant) -> Reparto equitativamente la informcion en los discos, pero si pierdo 1 de los 4 discos perderia 1/4 de la informacion
			       -> Utilizando el paralelismo puedo leer los 4 discos al mismo tiempo lo que aumenta la velocidad (usa solo 1 tiempo de busqueda para agarrar los 4)
			       -> Datos se dividen en BANDAS y se distribuyen en las diferentes unidades
			       -> La CAPACIDAD DE ARREGLO es igual a la suma de las capacidades de los discos

	RAID 1 (mirrored)0      -> Usa redundancia, duplicando los datos en **DOS** DISCOS DISTINTOS
				-> Nivel mas rapido, ya que no calcula paridad (Escritura rapida tmb)
				-> CAPACIDAD DE ARREGLO es igual a la MITAD DE LA CAPACIDAD DE TODOS LOS DISCOS
				-> DUPLICA el costo de bit almacenado

	RAID 2 (redundancy through Hamming[^1] code) (NO SE UTILIZA EN LA ACTUALIDAD)
				-> Provee redundancia pero utilizando un **CODIGO HAMMING** a nivel de bit 
[^1]: Código Hamming es un sistema "mágico" que no solo sirve para saber si un dato se rompió (detectar el error), sino que también sirve para arreglarlo si>
Es como si mandaras un mensaje por WhatsApp y, aunque llegue con letras cambiadas, el celular sea lo suficientemente inteligente para saber cuáles están mal>
				-> La información se distribuye a nivel de bit entre los discos y lo mismo con la paridad
				-> Usando un código H(7,4) la CAPACIDAD DE ARREGLO es de 4/7 la suma de las capacidades de las unidades del arreglo
				-> Puede corregir errores de UN DISCO y detectar errores de DOS DISCOS
				-> Necesita sincronización de cabezales entre discos
#### Ejemplo concreto 
 En el RAID 2 de tu imagen Mirá los cilindros del dibujo:
Tienes 4 discos de datos ($b_0, b_1, b_2, b_3$): Aquí guardás tu información bit por bit.
Tienes 3 discos de paridad ($f_0, f_1, f_2$): Aquí guardás los bits de Hamming.
Si el disco $b_1$ se prende fuego o falla, el sistema operativo mira los otros 6 discos, hace la cuenta de Hamming y reconstruye en tiempo real el bit que falta.

	RAID 3 (bit-interleaved parity)
				-> Utiliza solo paridad a nivel de byte [QUE CHOTA ES ESTO??]
Esto significa que el sistema divide tu información en trozos de apenas 1 byte (8 bits) para repartirlos entre los discos.
Para entenderlo, comparémoslo con cómo guardás archivos normalmente:
	1. La distribución (Striping)
Imaginá que querés guardar la palabra "HOLA", que ocupa exactamente 4 bytes. 
En RAID 3, el sistema hace esto:
El byte de la 'H' va al Disco 0.
El byte de la 'O' va al Disco 1.
El byte de la 'L' va al Disco 2.
El byte de la 'A' va al Disco 3.
	2. El cálculo de la Paridad ($XOR$)
Al mismo tiempo, el sistema operativo realiza una operación lógica llamada OR exclusivo ($\oplus$) con esos 4 bytes:
$$H \oplus O \oplus L \oplus A = \text{Byte de Paridad}$$
Ese resultado se guarda en el disco $P(b)$.
	3. ¿Por qué es importante que sea "a nivel de byte"?
Como los trozos son tan chiquitos (un misero byte), para leer o escribir cualquier archivo, todos los discos tienen que trabajar al mismo tiempo.
Sincronización: Los motores de los discos deben estar perfectamente sincronizados (como bailarines de ballet).
 Si un disco gira un milímetro más lento, el byte llega tarde y la palabra "HOLA" se rompe.
Velocidad: Es muy rápido para transferir archivos grandes (porque usás el ancho de banda de todos los discos a la vez), pero es malísimo para archivos chiquitos porque siempre tenés que mover todos los cabezales de todos los discos.
				-> La información se escribe secuencialmente en los diferentes discos para mejorar la velocidad de lectura y escritura
				-> La paridad se almacena en un solo disco (se calcula con un OR exclusivo)
				-> Tolera el fallo de una unidad
				-> Puede continuar funcionando luego del fallo de una unidad en modo de reconstrucción en línea

	RAID 4 (block-level parity)
				-> En esta categoría las bandas son mayores y los accesos son independientes entre discos
				-> Cuando se escribe una banda en un disco hay que escribir la correspondiente en el disco de paridad
				-> Frente a cargas substanciales de escritura el disco de paridad se convierte en un cuello de botella para el desempeño del sistema	
# RAID 4: Optimización de Paridad (El Truco del XOR)

En RAID 4, el objetivo es actualizar el **disco de paridad** de forma eficiente cuando solo cambia un dato en **un solo disco**, sin tener que leer todos los demás discos del arreglo.

### 1. El Problema: El Cálculo Lento
Para un arreglo de 4 discos de datos ($X_0, X_1, X_2, X_3$), la paridad original ($X_4$) es:
$$X_4 = X_0 \oplus X_1 \oplus X_2 \oplus X_3$$

Si queremos cambiar el dato $X_1$ por un nuevo valor $X_1'$, la lógica básica nos obligaría a leer $X_0, X_2$ y $X_3$ para recalcular todo. 
> **Inconveniente:** Si el RAID tiene 100 discos, tendrías que leer 99 para actualizar solo uno. Es inviable.

---

### 2. La Solución: Propiedad de Cancelación
Usamos la propiedad del XOR donde un valor operado consigo mismo se anula: $A \oplus A = 0$.

Para obtener la **nueva paridad ($X_4'$)**, el sistema aplica esta lógica:
1. **Partimos de la paridad vieja:** $X_4$ (que ya contiene la info de todos los discos).
2. **"Restamos" el dato viejo:** Aplicamos XOR con el dato que vamos a borrar ($X_1$). Esto lo vuelve cero dentro de la ecuación original.
3. **"Sumamos" el dato nuevo:** Aplicamos XOR con el nuevo valor ($X_1'$).

**Fórmula final:**
$$X_4' = X_4 \oplus X_1 \oplus X_1'$$

---

### 3. Impacto en el Rendimiento (E/S)
Gracias a este truco, no importa cuántos discos tenga el RAID, siempre realizaremos solo **4 operaciones de I/O**:

* **Lectura (2):** Leemos el dato viejo ($X_1$) y la paridad vieja ($X_4$).
* **Escritura

	RAID 5 (block-level distributed parity)
				-> A diferencia de RAID 4 distribuye la paridad de manera uniforme entre los diferentes discos
				-> El objetivo de esto es reducir el cuello de botella de RAID 4 debido al disco dedicado de paridad
				-> Ha sido utilizado mucho en el pasado reciente debido a su buena performance y su capacidad para seguir funcionando aún cuando una unidad falla
				-> Cuando aparece un fallo y se reemplaza la unidad el arreglo RAID reconstruye los datos sobre la nueva unidad
				-> Luego del fallo de una unidad la reconstrucción del arreglo puede demorar mucho tiempo debido a la gran capacidad de las unidades
				-> A medida que crecen estos discos, son mas propensos a FALLAR
				-> Además es muy común que se utilizen unidades del mismo fabricante y del mismo lote
				-> Todo esto eleva la probabilidad de que suceda un nuevo fallo antes de que la reconstrucción termine
				-> Esto deja el arreglo **inutilizado**
# RAID 5: Paridad Distribuida y el Riesgo de los Discos Grandes

RAID 5 es la evolución de RAID 4. Su principal cambio es que elimina el "disco de paridad dedicado" para repartir esa carga entre todos los discos del arreglo.

### 1. Funcionamiento: Paridad Distribuida
A diferencia de RAID 4, donde un solo disco se encargaba de la paridad, en RAID 5 **la paridad se distribuye uniformemente** entre todas las unidades.

* **Estructura:** Si tenés 5 discos, el bloque de paridad del primer conjunto de datos va al Disco 4, el del segundo al Disco 3, y así sucesivamente (va "saltando").
* **Objetivo:** Eliminar el **cuello de botella** del disco de paridad. Ahora, múltiples escrituras pueden ocurrir en paralelo si afectan a bloques de paridad que están en discos físicos distintos.
* **Tolerancia:** Soporta el fallo de **una (1) unidad**. El sistema puede seguir operando en "modo degradado" y reconstruir los datos en tiempo real.

---

### 2. El Gran Problema: La Reconstrucción Crítica
Aunque RAID 5 fue el estándar por años, las diapositivas advierten sobre un peligro moderno fatal:

1. **Capacidad vs. Velocidad:** Los discos crecieron muchísimo en capacidad (ej. 12TB), pero su velocidad de lectura no aumentó al mismo ritmo.
2. **Tiempo de Reconstrucción:** Cuando un disco falla y se reemplaza, el sistema debe leer **todos** los otros discos para regenerar el dato perdido. En discos grandes, este proceso puede tardar **días**.
3. **Estrés y Lote de Fabricación:** Durante la reconstrucción, los discos sanos trabajan al 100% de su capacidad. Como suelen ser del **mismo fabricante y mismo lote**, tienen altas chances de fallar casi al mismo tiempo.
4. **El "Game Over":** Si un **segundo disco falla** mientras el primero todavía se está reconstruyendo, el arreglo queda **inutilizado** y se pierde toda la información.

---

### 3. Resumen para el Parcial
* **Nivel de paridad:** A nivel de bloque (como RAID 4) pero distribuida.
* **Costo de escritura:** Sigue usando el truco del XOR ($2$ lecturas y $2$ escrituras), pero repartido.
* **Capacidad útil:** $(n-1) \times \text{capacidad del disco más chico}$.
* **Punto débil:** Vulnerabilidad extrema durante la fase de reconstrucción en discos de gran capacidad.


	RAID 6 (dual redundancy)
# RAID 6: Doble Paridad y Máxima Seguridad

RAID 6 es una extensión de RAID 5 que añade un nivel extra de protección. Su objetivo principal es sobrevivir a escenarios donde fallan múltiples unidades al mismo tiempo.

### 1. El Concepto de Doble Paridad
A diferencia de RAID 5, que utiliza un solo cálculo de paridad (XOR), RAID 6 utiliza **dos cálculos de paridad independientes** (comúnmente llamados **P** y **Q**).

* **Distribución:** Ambos bloques de paridad se almacenan en discos diferentes y se distribuyen por todo el arreglo (igual que en RAID 5).
* **Costo de Almacenamiento:** Requiere el espacio equivalente a **dos discos extra** para almacenar estas paridades. Por ejemplo, si tenés 6 discos, solo usás el espacio de 4 para datos.
* **Tolerancia extrema:** Permite que fallen **hasta dos discos simultáneamente** sin que se pierda ni un solo bit de información.

---

### 2. ¿Por qué se creó? (Solución a RAID 5)
Como vimos en la clase anterior, el punto débil de RAID 5 es el largo tiempo de reconstrucción con discos grandes, donde un segundo fallo significa la pérdida total de datos.

* **El Salvador del Rebuild:** RAID 6 fue implementado específicamente para combatir este riesgo. Si un disco falla y, durante la pesada tarea de reconstrucción, **falla un segundo disco**, el arreglo RAID 6 puede seguir funcionando y reconstruyendo ambos.
* **Discos Grandes:** Es la configuración recomendada hoy en día cuando se utilizan discos mecánicos de gran capacidad (SATA/SAS de varios Terabytes).

---

### 3. Rendimiento y Comparativa
* **Lectura:** Muy rápida, similar a RAID 5.
* **Escritura:** Un poco más lenta que RAID 5, ya que el sistema debe calcular y escribir **dos** bloques de paridad distintos por cada escritura de datos.
* **Capacidad útil:** $(n-2) \times \text{capacidad del disco más chico}$.

---

### 4. Resumen para el Parcial
* **Diferencia clave con RAID 5:** Doble paridad independiente (P y Q).
* **Tolerancia a fallos:** Hasta **2 unidades** al mismo tiempo.
* **Uso ideal:** Servidores con muchos discos o discos de gran capacidad donde la seguridad de los datos es crítica.
				
# Resumen Comparativo de Niveles RAID

| Categoría | Nivel | Descripción | I/O Request Rate (Read/Write) | Data Transfer Rate (Read/Write) | Aplicación Típica |
| :--- | :---: | :--- | :--- | :--- | :--- |
| **Striping** | 0 | No redundante | **Exc.** (Large strips) | **Exc.** (Small strips) | Aplicaciones de alto rendimiento para datos no críticos. |
| **Mirroring** | 1 | Espejado (Mirrored) | Bueno / Regular | Regular / Regular | Unidades de sistema; archivos críticos. |
| **Parallel Access** | 2 | Redundante vía código Hamming | Pobre | **Excelente** | (Poco usado actualmente) |
| **Parallel Access** | 3 | Paridad intercalada a nivel de bit | Pobre | **Excelente** | Apps con grandes pedidos de I/O (Imágenes, CAD). |
| **Independent Access** | 4 | Paridad intercalada a nivel de bloque | **Exc.** / Regular | Regular / Pobre | (Poco usado, superado por RAID 5) |
| **Independent Access** | 5 | Paridad distribuida a nivel de bloque | **Exc.** / Regular | Regular / Pobre | Alta tasa de pedidos, lectura intensiva, búsqueda de datos. |
| **Independent Access** | 6 | Doble paridad distribuida a nivel de bloque | **Exc.** / Pobre | Regular / Pobre | Aplicaciones que requieren disponibilidad extremadamente alta. |
