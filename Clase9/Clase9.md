## Planificación de Disco en Linux
### Planificador del ascensor
▶ Tiene una cola única para escritura y lectura
▶ Se le agregan las siguientes condiciones,
	1. Si un pedido es en el mismo sector o uno adyacente a un pedido que ya se encuentra en la cola entonces estos se funden en uno solo
	2. Si un pedido es lo suficientemente viejo entonces un pedido nuevo será agregado al final de la cola
	3. Si hay un lugar adecuado se inserta el pedido en el lugar correcto de la cola 
	4. Si no hay un lugar adecuado entonces se coloca el pedido al final de la cola

### Planificador de deadline
▶ Con el ascensor un pedido en una pista lejana puede
tener que esperar mucho si llegan muchos pedidos
cercanos a la posición actual
▶ Otro problema es la diferencia de requerimientos entre
lecturas y escrituras

