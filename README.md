# Obtención de un plano a partir de datos obtenidos en un levantamiento planimétrico mediante un programa en python

Felipe de Jesús Duran Luna, Sanchez Cabellos Arturo

 Universidad de Colima, 28050, fduran@ucol.mx. Universidad de Colima, 28983, [arturo\_sanchez@ucol.mx](mailto:arturo_sanchez@ucol.mx)

Resumen

El problema que se requirió resolver fue la creación de un programa en lenguaje Python que a partir de datos de campo de un levantamiento planimétrico con teodolito y estación total de una poligonal cerrada realice todos los procedimientos hechos en gabinete para la obtención del cuadro de construcción de dicho polígono y la representación gráfica en archivo dxf (Drawing Exchange Format) que puede ser visto y modificado en programas de diseño como es AutoCAD o en sistemas de información geográfica.

Los datos de campo pueden ser por el método de ángulos internos y por el método de conservación de azimut, también puede definirse si es un levantamiento directo o por radiaciones. Estos datos deben estar en formato csv o delimitado por comas para que el programa pueda leerlos y en un orden definido.

Lo que se obtiene es el cuadro de construcción del polígono en un archivo csv y la representación gráfica del polígono en un archivo dxf. Para lograrlo se requirió de las funciones de otros módulos (Numpy, Pandas, y dxfwrite) para facilitar el proceso de la información. En pocas palabras, numpy se utilizó para funciones matemáticas como son las funciones trigonométricas, pandas se utilizó para el manejo de información (por medio de tablas de datos) y la lectura y escritura de archivos csv y dxfwrite para la escritura de archivos dxf que contiene la representación gráfica del polígono.

Abstract

The problem that was required to solve was the creation of a program in Python language that from field data of a planimetric survey with theodolite and total station of a closed polygon perform all the procedures done in the cabinet to obtain the construction box of said polygon and the graphical representation in file dxf (Drawing Exchange Format) that can be seen and modified in design programs such as AutoCAD or in geographic information systems.

1. The field data can be by the internal angle method and by the azimuth conservation method, it can also be defined if it is a direct or radiation survey. This data must be in csv format or de-limited by commas so that the program can read them and in a defined order.
2. What you get is the construction box of the polygon in a csv file and the graphical representation of the polygon in a dxf file. To achieve this, the functions of other modules (Numpy, Pandas, and dxfwrite) were required to facilitate the process of information. In short, numpy was used for mathematical functions such as trigonometrical functions, pandas was used for information management (by means of data tables) and reading and writing csv and dxfwrite files for writing dxf files which contains the graphic representation of the polygon.

1. Introducción

La programación es una herramienta que en la actualidad es aprovechada en cualquier área de estudio, ya sea, en la elaboración de investigaciones, creando procesos que automaticen el análisis de información, elaborando simulaciones de algún fenómeno y para cualquier otro fin en el que se requiera el uso de la tecnología para facilitar un trabajo.

Desde sus inicios la programación junto con la tecnología ha evolucionado, diseñándose lenguajes más similares al lenguaje humano (lenguajes de alto nivel). &quot;En la actualidad los lenguajes más utilizados son: Java, C, C++, Python y Rubí según, y cada uno tiene sus ventajas y desventajas&quot;.

Este proyecto consistió en crear un programa en lenguaje Python con el fin de automatizar el proceso de elaboración del plano de escrituración de un polígono. La información que recibe puede ser de un levantamiento directo o por radiaciones y, el polígono o poligonal de apoyo (si es por radiaciones) pueden ser por el método de ángulos internos y por el método de conservación de azimut.

Como resultado se obtiene el cuadro de construcción del polígono en un archivo csv, el dibujo del polígono más la base del plano, la escala numérica utilizada para la elaboración del plano, el error lineal, la precisión del levantamiento y si los datos son por el método de ángulos internos también se obtiene el error angular.

El programa se realizó en el programa Spyder, que es un editor y compilador especializado al lenguaje Python, es decir, que traduce a lenguaje máquina, las instrucciones escritas en el editor para que el ordenador las ejecute, así se pudieron detectar y corregir errores durante su elaboración.

2. Desarrollo

El propósito de este proyecto fue elaborar un programa en lenguaje Python que realizara los procedimientos matemáticos vistos en clase de topografía general I para procesar la información obtenida en campo con el fin de automatizar los procedimientos hechos en gabinete de un levantamiento topográfico de una poligonal cerrada.

Debido a que en la actualidad los programas están encaminados a procesar información de equipos y levantamientos más sofisticados (coordenadas obtenidas por gps y lidar o, levantamientos indirectos con el uso de imágenes aéreas) se deja de lado los métodos tradicionales, sin embargo, estos métodos siguen siendo utilizados y toma mucho tiempo en calcular coordenadas, áreas, volúmenes o elaborar el plano del levantamiento. Es por esa razón la elaboración de este proyecto puede ser de utilidad para el trabajo en gabinete de un topógrafo.

2.1. Manejo de datos

En el manejo de datos se emplea el proceso de elaboración del programa donde se define la función de cada sección y el código.

La primera parte consiste en importar los módulos necesarios para la elaboración del programa. Importamos numpy que contiene las funciones matemáticas; pandas que tiene herramientas para el manejo de la información, además de leer y crear archivos; dxfwrite para la elaboración de archivos dxf. Se abrevia cada módulo para que el código del programa sea más simple.

Definimos una función que defina si un ángulo es mayor a 360 grados. Si es así, regresa el número menos los 360 grados, de lo contrario regresa el mismo número.

Creamos una función que calcule la proyección en X y en Y a partir de una lista de distancias (d) y una lista de azimuts (az).

Definimos una función que obtenga la condición angular para después compararla con la suma de los ángulos internos del polígono y obtener el error angular. Después se realiza la compensación angular y regresa dos resultados: Los ángulos internos compensados y el error angular.

El archivo csv donde se encuentran los datos de campo debe de tener un orden específico en sus columnas como se muestra en el siguiente ejemplo:

Para las radiaciones, el orden y nombre de las columnas, es el siguiente:

Elaboramos dos funciones que obtengan el rumbo a partir de cierta información. La primera función obtiene el rumbo a parir de un azimut donde define el cuadrante en el que se encuentra y transforma los grados decimales, en grados, minutos y segundos.

La segunda función define el rumbo a partir de dos coordenadas. Esto consiste en obtener el desplazamiento en X y en Y que van del primer punto al segundo, posteriormente, se divide el desplazamiento en X sobre el desplazamiento en Y, calculamos la tangente inversa del valor obtenido y tenemos el ángulo.  Dependiendo del signo del desplazamiento se define el rumbo norte, sur, este u oeste.

Para la elaboración del plano se definió una función donde recibe como parámetros las coordenadas de la poligonal de apoyo y opcionalmente las coordenadas de las radiaciones. En la primera parte se crea el archivo y las capas (las que pertenecen a la poligonal de apoyo y las que pertenecen a las radiaciones). Todos los archivos dxf tienen una capa predeterminada, esta será utilizada para elaborar el resto del plano. La segunda parte consiste en definir si el levantamiento es por radiaciones o es directo, en ambos casos definimos el centro del polígono para poder colocar el cuadro que encierra el polígono y el cuadro que contiene la información del polígono. Es necesario saber las dimensiones del cuadro, así que se definió un cuadro base que después va ser elevado a una escala.

Esta función define la escala a partir de un número, que es el cociente de la distancia que existe entre el centro a un extremo del polígono sobre la distancia que existe entre el centro del polígono a un extremo del cuadro, de esa forma se obtiene un número que es el número de veces que el cuadro que encierra el poligono debe aumentar. La condición que se debe cumplir es que el número tiene que ser menor a una lista de números o escalas.

Creamos una función que obtenga el cuadro de construcción. Recibe como parámetros la localización en el disco del archivo csv con los datos de campo, el tipo de levantamiento (ángulos internos o conservación de azimut) y la coordenada inicial.

En la primera parte se definen dos variables: la primera variable (cd) será la que contendrá los datos finales para el cuadro de construcción, mientras que, la segunda variable (df) es la que contiene la información de campo. &quot;read\_csv&quot; es una función del módulo pandas para leer archivos csv en el disco y extraer su información.

La segunda parte de la función se refiere al tipo de método que se realizó en el levantamiento. Si fue por el método de ángulos internos (tipo 1) entonces se requiere de compensar el error angular y obtener el azimut de cada vértice.

Si la información es por el método de conservación de azimut solo se necesita transformar los grados, minutos y segundos grados decimales.

Después de obtener el azimut de cada vértice, sigue obtener el rumbo de cada uno. Utilizando la función que definimos anteriormente para calcular los rumbos podremos obtenerlos. Usando la función para obtener las proyecciones obtenemos la proyección en X y en Y de cada lado del polígono. Antes de calcular las coordenadas se requiere corregir las proyecciones, primero suman todas las proyecciones en X y Y  para obtener el error de cada uno, también se realiza la sumatoria de los lados de polígono para obtener el perímetro y utilizando el método de compensación corregimos cada proyección.

Teniendo las proyecciones corregidas, el siguiente paso es calcular la coordenada de cada vértice sumándole a la proyección del vértice la coordenada anterior a esta. Después obtenemos el error lineal del levantamiento y finalmente la precisión del levantamiento.

Por último, se elabora el cuadro de construcción de la poligonal real (o de apoyo si tiene radiaciones) juntando lo so los datos de estación, punto visado, distancia, rumbo y coordenadas. La función regresa como resultado el cuadro de construcción, el error angular, la precisión del levantamiento, el azimut de cada línea en grados decimales y las coordenadas de cada vértice, estos dos últimos valores se colocaron a parte, ya que, es más practico extráelos directamente que del cuadro de construcción.

Para que el programa pudiera conocer la localización del archivo, el tipo de levantamiento y la coordenada inicial, se elaboró una interfaz simple donde se puede colocar los datos. Para la localización del archivo se debe colocar la dirección y el nombre del archivo incluyendo su terminación .csv, El tipo de método se define con el uno para ángulos internos y el dos para conservación de azimut. Va seguido de la dirección del archivo y separado por una coma. Para las radiaciones solo se define si se van a usar o no. Si se van a utilizar radiaciones, para definir la localización del archivo será igual a la anterior.

Se lee el archivo que contiene las radiaciones con la función &quot;read\_csv&quot;, la información es guardada en la variable &quot;rd&quot;. Convertimos los grados minutos y segundos en grados decimales que serán guardados en una nueva columna, también se crearon otras dos columnas donde se guardaran las coordenadas finales.

Después se elaboró un algoritmo para definir cuales radiaciones correspondían al vértice de la poligonal de apoyo parar sumar el ángulo de la radiación con el azimut del respectivo vértice y obtener el azimut de cada radiación. Teniendo el azimut de cada radiación se calculan las proyecciones que, a su vez, se suman al vértice correspondiente y obtenemos las coordenadas del punto de cada radiación.

Las coordenadas de las radiaciones se guardan también en una lista que será utilizada en la elaboración del plano. Para obtener la distancia y el rumbo que había entre dos puntos se tuvo que iterar por cada reglón y realizar las operaciones matemáticas correspondientes. En el caso de la distancia, se le restaron a las coordenadas de un punto las coordenadas del punto anterior para obtener la proyección de cada eje, con el teorema de Pitágoras se calculó la distancia. Usando la función de calcular el rumbo a partir de dos puntos obtenemos el rumbo de cada línea. La información es almacenada en una tabla donde también contiene las coordenadas de las radiaciones y finalmente se guarda en un archivo csv.

Si no van a usarse radiaciones simplemente se guarda el cuadro de construcción de la poligonal y se elabora el plano.

2.2. Resultados obtenidos

Como resultado se obtuvo una parte de lo que contiene el plano de escrituración, los cuales son: el dibujo del polígono ya sea de forma directa o por radiaciones, el dibujo de la poligonal de apoyo (si es por radiaciones), el cuadro que encierra el dibujo del polígono y el cuadro donde se muestra la información del polígono. Cada uno se tiene una capa definida para tener un mejor control en el manejo de las propiedades de cada uno.

También se obtuvo el cuadro de construcción del polígono, guardado como csv, el cual contiene el rumbo y distancia que existe entre un vértice y otro, así como, las coordenadas de cada vértice.

En cuanto a la interfaz del programa, resulto ser práctica, ya que, es muy simple y sencillo de colocar la dirección de los archivos y definir los parámetros para la elaboración del plano, estos son: el tipo de levantamiento, la coordenada inicial, el azimut inicial si es por el método de ángulos internos, si el levantamiento es directo o contiene radiaciones. En la misma interfaz, después de definir todos los parámetros te muestra el error de cierre angular y lineal, la escala utilizada y la precisión del levantamiento. El plano y el cuadro de escrituración se guardarán en la carpeta de usuarios con el nombre de &quot;plano.dxf&quot; y &quot;cuadro.csv&quot; respectivamente.

2.3. Conclusión

En conclusión, el proyecto fue un éxito, ya que, se logró los objetivos planteados inicialmente. Se logró obtener el plano del levantamiento de un polígono con datos de campo medidos con un tránsito o con una estación total (ángulo y distancia), el polígono del plano coincide exactamente a la forma original, por lo que se deduce que el procedimiento del programa es correcto.

Fue posible que se pudiera definir en el programa, si se utilizó el método de radiaciones o se utilizó el método de conservación de azimut, además también fue posible definir en el programa si fue un levantamiento directo o por radiaciones.

Se logró obtener el cuadro de construcción, por un levantamiento directo o por radiaciones, además de realizar una compensación angular (solo para el método de ángulos internos) y lineal. Finalmente se pudo obtener el error angular y la precisión del levantamiento. En cuanto al formato y diseño del polígono, no fue posible incluir el cuadro de construcción en él, así que solo es posible tenerlo en un archivo csv, para después pasarlo manualmente al plano utilizando programas como AutoCAD.

3. Referencias web

http://docs.python.org.ar/tutorial/pdfs/TutorialPython2.pdf

https://www.campusmvp.es/recursos/post/los-diez-lenguajes-de-programacion-de-2017.aspx

https://lolap.wordpress.com/2015/01/14/que-es-un-csv-como-se-hace-y-para-que-sirve/

Poster

http://www.ictea.com/cs/knowledgebase.php?action =displayarticle&amp;id=8817
