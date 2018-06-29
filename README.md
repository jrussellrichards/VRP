# VRP
Vehicle routing problem with genetic algorithm

Javier Richards
Tópicos de inteligencia artificial
Universidad de Concepción.




Introducción
	En 1959 George Dantzig y John Ramser entregan la primera definición sobre lo que sería el VRP, en donde plantea una aproximación algorítmica y fue aplicado para entregas de gasolina. Pero, ¿En qué consiste este problema?. El VRP surge de la pregunta: Dado un número de “n” Clientes cada uno con una demanda “d” en diferentes ubicaciones y número “m” de vehículos, ¿Cuáles son las rutas que deben tomar los vehículos dado que estas minimicen el costo total de desplazamiento cumpliendo la restricción de satisfacer las demandas de todos los clientes?. Este problema es una generalización del más conocido problema TSP o más conocido como el problema del vendedor viajero por sus siglas en inglés. Existen muchas variaciones de este problema, una de las más conocidas y más aplicadas es el CVRP el cual añade capacidad a los vehículos. En muchas situaciones los clientes tendrán disponibilidad limitada en los cuales deberán ser servidos, a este tipo de problema se les llama VRPTW el cual incluye la restricción de una ventana de tiempo en los cuales los vehículos deben visitar a los clientes. En otros casos tendremos clientes los cuales tendrán diferentes demandas, algunos necesitarán entregar algún producto y otros por el contrario necesitarán que se les entregue algo. Es aquí donde nace otra variación de el VRP el cual incluye ida y recogidas, donde los vehículos pueden cumplir ambas funciones. 
Resolver este tipo de problemas suele tener un costo computacional muy alto, esto debido a que se caracterizan como problemas de optimización combinatoria de tipo NP-HARD o NP-DIFíCIL, los cuales tardan bastante tiempo en encontrar una solución ya que el tiempo no depende polinomialmente de la cantidad de clientes a servir. Es por esto que las implementaciones más usadas se basan en heurísticas, las cuales, generalmente no encuentran la solución exacta pero se comprometen en encontrar una solución muy cerca del óptimo.





Problema a atacar 

Se propone resolver un -Vehicle routing problem with capacity, time windows and multi-depot  en donde los vehículos tienen una capacidad limitada y se encuentran en diferentes posiciones y deben entregar a los clientes siempre en el mismo destino. Los clientes además tienen restricciones en el tiempo que se deben cumplir estrictamente, las cuales indican lo siguiente: Cada cliente debe ser retirado en un rango de hora, si un vehículo no cumple esa condición para ese cliente entonces el cliente no podrá ser servido por ese vehículo. Además cada cliente debe ser dejado en destino en un tiempo límite, así mismo como en la recogida de estos, si un vehículo no puede comprometerse a retirar un cliente y entregarlo en destino en el tiempo límite entonces no puede servirle. 
Se debe considerar como restricción final que los vehículos satisfagan la mayor cantidad de demanda posible. 
	
Método a utilizar
	Al ser un problema del tipo no polinomial se propone resolver este mediante heurística, más específicamente mediante el método de algoritmo genético.
Los Algoritmos Genéticos (AGs) son métodos adaptativos que pueden usarse para resolver problemas de búsqueda y optimización. Están basados en el proceso genético de los organismos vivos. A lo largo de las generaciones, las poblaciones evolucionan en la naturaleza de acorde con los principios de la selección natural y la supervivencia de los más fuertes, postulados por Darwin (1859). Por imitación de este proceso, los Algoritmos Genéticos son capaces de ir creando soluciones para problemas del mundo real. La evolución  de dichas soluciones hacia valores óptimos del problema depende en buena medida de una adecuada codificación de las mismas.


Modelo de la solución


Ruta
Para una instancia del problema se generará una ruta, la cual indicará la ruta a seguir por cada vehículo. Nos referiremos a la ruta de cada vehículo como “sub rutas” y a la ruta total como “ruta”, la cual está conformada por sub rutas dependiendo de la cantidad de vehículos que participen en esta instancia. Cada sub ruta será representada como una lista y estas estarán conformadas por los clientes a quienes deben servir. El total de sub rutas para una instancia del problema formará la ruta total.
Por ejemplo la ruta: [[1,2],[5,9],[12,3]] indica que los clientes 1,2,5,9,12 y 3 serán servidos por 3 vehículos, los cuales serán divididos en 1 y 2, 5 y 9, y, 12 y 3 respectivamente.
Gen
Nos referiremos a genes como cada cliente que se encuentre dentro de un individuo. Cada cliente será representado como un número entero, el cual, indica el número de cliente lo que nos permite a través de un diccionario encontrar la demanda para cada cliente en específico.

Individuos
Los individuos son la decodificación de las rutas en una sola lista, esto debido a que facilita la manipulación de datos para las futuras funciones que se explicarán más adelante. 
Por ejemplo la ruta mostrada en la parte I se representará como un individuo de la forma: [1,2,5,9,12,3]

Fitness
Cada ruta tiene su propio valor fitness que la distingue entre las demás y que permite diferenciar las mejores rutas de las que entregan una solución inferior. 

La función fitness está dada por: 

	Fitness= ∑sub route distance
En el caso de que la solución para ese individuo esté utilizando más vehículos de los disponibles se multiplica la solución por un factor muy grande para eliminar esta solución.
	Fitness= ∑sub route distance*999999
Reproducción
La reproducción es la encargada de asegurar la diversidad genética tomando las mejores soluciones encontradas hasta ahora.
Funciona de la siguiente manera:
Se realiza una competencia de torneo entre dos individuos al azar, de los cuales existe un ganador. El individuo ganador es escogido como el padre número 1, el cual será el primer encargado de crear la descendencia. Se repite el proceso para obtener al segundo padre.
Luego de tener ambos padres listos para la procreación se realiza el proceso de reproducción que consiste en lo siguiente:

Para el padre 1 se escogen dos genes aleatoriamente. Se extrae la sección   ubicada entre ambos genes y se une al individuo dos. Una vez  el individuo resultante es originado, se deben eliminar todos los genes repetidos partiendo de izquierda a derecha. Luego repetir el procedimiento pero esta vez con el Padre número 2.                                             
             


Mutación
La mutación nos asegura la diversidad genética, logrando solucionar posibles problemas cuando nos estamos acercando a un óptimo local que puede estar muy lejos de la solución real. 
La mutación funciona de la siguiente manera:
Se escogen dos genes aleatoriamente desde el individuo. Una vez escogido los genes se invierte el orden de esa sección del individuo. Para dejar más claro tomemos como ejemplo el individuo: [123456789] del cual aleatoriamente se escogen los genes 2 y 5, lo que da paso a una mutación que da como resultado el individuo: [154326789].
	La Mutación se aplica al momento de generar individuos nuevos de un proceso de 
	reproducción, en el cual la mutación tiene una probabilidad muy chica (0.05) de 
	que el individuo mute. 

Algoritmo genético

El algoritmo genético recibe como input

Problema genético, el cual a su vez contiene:
Un set de clientes, los cuales contienen individualmente:
Identificación
Posición
Ventana de tiempo para recogida
Ventana de tiempo de demora del servicio
Ventana de tiempo para llegada
Vehículos, los cuales contienen:
Identificación
Posición
Capacidad de los vehículos (capacidad homogénea) 
k: Cantidad de participantes en el torneo de selección por elitismo
Matriz de distancia entre ubicaciones, las cuales pueden representar comunas o ubicaciones predefinidas. 
opt: parámetro para maximizar o minimizar que en este caso será siempre minimizar ya que estamos evaluando en función de la fistancia
ngen: Número de generaciones
Size: Tamaño de la población
ratio_cross: Cantidad de la población que descenderá por cruza
Prob_mutate: Probabilidad de mutar


Primero se debe crear una población inicial, la cual es elegida randómicamente y que está dada por “size”. Esta será la primera generación y es la que dará origen a generaciones posteriores las cuales serán cada vez más aptas. 
Posteriormente a la creación de nuestra primera población, debemos crear nuestras posteriores generaciones. Estas se crearán de la siguiente manera:
En la nueva generación hay una cantidad de individuos que pasan directamente a la siguiente generación, estos descienden mediante selección por torneo en donde la cantidad de descendientes está dada por la cantidad total de la población menos la cantidad de individuos que resultaron mediante cruzas. Los individuos que pasan a la generación siguiente directamente lo hacen por elitismo, lo que quiere decir que son aquellos que son los mejores de su población -selección por ranking-.
Cruzas: El 85% de la población corresponde a hijos quienes nacieron mediante cruzas. Éste 85% se obtiene a partir de una selección por torneo de la siguiente manera: Se escogen dos individuos al azar a los cuales se les calcula su función fitness. El individuo más apto -mejor fitness- es quién será el padre número uno. Posteriormente se repite esto para obtener el padre número dos.
Mutación: Por cada hijo existe una probabilidad de que este mute. Esta es una manera para no caer en óptimos locales, dándole paso a soluciones que actualmente parecen no ser las mejores pasar a la siguiente generación.
Nueva generación: La nueva generación que es una generación de soluciones más óptimas está dada por:
Individuos seleccionados por elitismo cuya cantidad como se indicó anteriormente está dada por: Cantidad total menos cantidad de individuos que se obtendrán por cruza.
Individuos obtenido por cruzamiento
El algoritmo itera con este procedimiento hasta que el número de generaciones termina. Una vez se obtiene la generación final, el individuo con mayor fitness es el que se escoge como solución final obtenida. 

Prototipo iteración dos:
En esta iteración se modelo el algoritmo genético para un VRC, es decir un problema de ruteo de vehículos con capacidad, sin ventanas de tiempo ni diferentes posiciones.  
Métrica de evaluación
	Para evaluar el desempeño se calcula el RPD lo cual es una métrica para ver que tan bueno es nuestra solución a partir de una óptima conocida para la misma entrada. 
RPD = (output – óptimo) / óptimo *100 
Como se puede observar el RPD muestra el porcentaje de diferencia que el algoritmo varía de acuerdo a la solución exacta del problema. 
Se utilizará como prueba un problema de Augerat nombrado como An32k5 debido a que tiene 32 clientes y 5 vehículos con capacidad igual a 100. La solución óptima para este problema es de 787.
Se utilizarán los siguientes parámetros de entrada para el algoritmo:
2500 generaciones
Tamaño de la población igual a 800
Porcentaje de individuos que heredarán por cruza igual al 85%
0.05 la probabilidad de mutación
Resultados obtenidos.
	Luego de una cantidad moderada de iteraciones se obtuvieron los siguientes resultados:
Iteración 1: 820
Iteración 2: 844
Iteración 3: 838
Iteración 4: 807
Iteración 5: 841
Iteración 6: 833
Iteración 7: 796
Iteración 8: 816
Iteración 9: 812
Iteración 10: 835
Promedio: 824
Mejor solución: 796
Peor solución: 844
RPD Mejor solución: 1.1
RPD Peor solución: 7.2
RPD promedio: 4.7
Tiempo de ejecución: 175 segundos
Hardware de prueba
Intel core i5 4200u
8 gigas de memoria ram
Planificación de trabajo
Iteración tres: Implementación final, experimentación, documentación de la solución.
Conclusión
	Para esta iteración se obtuvieron resultados mucho mejor que los esperados en un tiempo aceptable pese a no tener un equipo con grandes capacidades. Se puede ver que la mejor solución está cerca del 1% de margen de error de acuerdo al óptimo y la peor solución solo tiene un 7% de diferencia con este. Se puede obtener mejores resultados pero se debe sacrificar el tiempo. Existen dos maneras -no excluyentes- para hacer esto: Aumentando el número de generaciones y aumentando el tamaño de la población. Hasta ahora algoritmos genéticos parecen ser una muy buena herramienta para estimar rutas de vehículos  soluciones muy buenas en tiempo de cómputos cortos.
Implementación:
	El software fue implementado en lenguaje Python 3.6 por lo que se debe instalar antes de correr este. Se debe tomar en consideración que el programa lee dos archivos externos ubicados en “datosVRP” así que se debe tener una correcta referencia a los archivos.

