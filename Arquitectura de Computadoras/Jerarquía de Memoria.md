La *jerarquía de memoria* establece un orden en función de la capacidad y velocidad de las memorias. La mayor jerarquía corresponde a la memoria más rápida. Mientras que la menor corresponde a la memoria más lenta.

## Principio de Localidad
El principio de localidad establece que los programas acceden a una porción relativamente reducida del address space en un determinado lapso de tiempo.
- Localidad Temporal: Si un elemento es referenciado en determinado momento, es común que vuelva a ser referenciado poco tiempo después.
- Localidad Espacial: Si un elemento referenciado en determinado momento, es común que los elementos con direcciones “cercanas” también sean accedidos poco tiempo después.

Surgen los siguientes términos:
- Hit: Se dice que ocurre un *hit* cuando un elemento se encuentra en el lugar de la jerarquía donde se lo está buscando.
- Miss: Se dice que ocurre un *miss* cuando un elemento no es encontrado en el lugar de la jerarquía donde se lo está buscando. En este caso es necesario ir a buscar el elemento a un nivel de jerarquía inferior.
- Hit-Rate: Es la tasa de acierto de encontrar un elemento en el lugar de la jerarquía en que se lo busca.
- Miss-Rate: Es la tasa de fallos de encontrar un elemento en el lugar buscado. Observar que se calcula como 1 - *Hit Rate*.
- Hit-Time: Tiempo de acceso promedio en el nivel de jerarquía considerado (donde se da el *hit*).
- Miss-Penalty: Tiempo de acceso promedio adicional requerido para acceder al elemento de información en el nivel de jerarquía inferior. *Típicamente ocurre que __Hit Time__ $<$ __Miss Penalty__.*
- Average Access Time: Se calcula como *__Hit Time__ $+$ __Miss Rate__ $\cdot$ __Miss Penalty__*

Respecto al rendimiento de la memoria:
- Access Time: Tiempo que transcurre entre que la dirección se presenta estable y los datos pueden ser manipulados en forma confiable. *Puede variar dependiendo si es un read o write.*
- Cycle Time: Tiempo mínimo que debe pasar entre un acceso y el siguiente (las memorias requieren de un cierto tiempo de recuperación antes de poder iniciar un nuevo ciclo de acceso).
- Transfer Rate: Velocidad de transferencia de los datos.

## Memoria Cache
La lógica de funcionamiento de la memoria cache es la que se esquematiza en el siguiente diagrama de flujo:

```mermaid
stateDiagram-v2
state "Se recibe una solicitud de lectura de una palabra." as s0
state "Buscar palabra en cache" as s1
state if_in_cache <<choice>>
state "Se retorna la palabra" as in_cache
state "Se lee el bloque de memoria que contiene la palabra" as not_in_cache
state "Se selecciona una entrada de la cache para alojar el bloque" as s2
state "Se carga el bloque en cache" as s3

[*] --> s0
s0 --> s1: + Cache Access Time
s1 --> if_in_cache
if_in_cache --> in_cache: Hit
if_in_cache --> not_in_cache: Miss
not_in_cache --> s2: + Memory Access Time
s2 --> s3
s3 --> in_cache

in_cache --> [*]
```

Al momento de diseñar una memoria cache se tienen en cuenta los siguientes criterios:
- Tamaño de Memoria Cache
- Función de Correspondencia
- Algoritmo de Sustitución
- Política de Escritura
- Tamaño de Bloque

## Funciones de Correspondencia

### Direct Mapping
Una *memory address* cuya función de correspondencia implementa *direct mapping* se representa como sigue,

$$\overset{TAG}{\overbrace{b_{t+l+w-1} \dots b_{l+w}}} \cdot \overset{LINE}{\overbrace{b_{l+w-1} \dots b_{w}}} \cdot \overset{WORD}{\overbrace{b_{w-1} \dots b_{0}}}$$

Siendo $t, l, w$ los bits reservados para __TAG__, __LINE__, __WORD__ respectivamente.
- __TAG__: Identifica que bloque esta en una línea dada.
- __LINE__: Identifica el índice de la entrada de la cache donde se encuentra el bloque.
- __WORD__: Representa el *offset* dentro del bloque identificado.

>[!warning] 
>Un bloque es identificado por su __TAG__ y un __LINE__, es decir, hay $2^m$ bloques que comparten la misma entrada en la cache, donde $m$ la cantidad de bits asociados a __TAG__. Por lo tanto, *direct mapping* no permiten que bloques con misma línea  esten cargados simultaneamente en la memoria cache.

![[Drawing 2023-07-11 15.55.39.excalidraw|center]]
- Cargar un bloque de memoria:
	1. Se determina la línea según los bits de __LINEA__ de la dirección del bloque de memoria.
	2. El bloque es cargado en la línea identificada y el __TAG__ de la dirección se almacena en su campo correspondiente.
- Buscar un bloque de memoria:
	1. Se determina la línea según los bits de __LINEA__ de la dirección.
	2. Se determina el __TAG__ a partir de los bits de __TAG__ de la dirección y se compara con el valor del campo __TAG__ correspondiente a la línea identificada.
	3. Si los valores coinciden, se produce un *hit*, en otro caso, se produce un *miss*.
	4. Se utiliza el *offset* __(WORD)__ respecto a la dirección para acceder a un byte especifico.

### Fully Associative
Una *memory address* cuya función de correspondencia implementa *fully associative* se representa como sigue,

$$\overset{TAG}{\overbrace{b_{w+t-1} \dots b_{w}}} \cdot \overset{WORD}{\overbrace{b_{w-1} \dots b_{0}}}$$

Siendo $t, w$ los bits reservados para __TAG__, __WORD__ respectivamente.
- __TAG__: Identifica el bloque deseado.
- __WORD__: Representa el *offset* dentro del bloque identificado.

- Cargar un bloque de memoria:
	1. Se selecciona una línea de la cache cuyo *valid bit* sea 0. En caso de no encontrar dicha línea, se selecciona una línea mediante una *política de reemplazo*
	2. La línea seleccionada es "desalojada" y el bloque de memoria es "alojado" en dicha línea. Actualizando el valor __TAG__ de dicha entrada.
- Buscar un bloque de memoria:
	1. Se compara el __TAG__ asociado a la dirección con todos los campos __TAG__ de la memoria cache. Si coincide alguna comparación, se produce un *hit* y se accede el bloque que coincidió en la comparación. En otro caso, se produce un *miss*.
	2. Se utiliza el *offset* __(WORD)__ respecto a la dirección para acceder a un byte especifico.

### N-Way Set Associative
Una *memory address* cuya función de correspondencia implementa *n-way set associative* se representa como sigue,

$$\overset{TAG}{\overbrace{b_{t+l+w-1} \dots b_{l+w}}} \cdot \overset{SET}{\overbrace{b_{l+w-1} \dots b_{w}}} \cdot \overset{WORD}{\overbrace{b_{w-1} \dots b_{0}}}$$

Siendo $t, s, w$ los bits reservados para __TAG__, __SET__, __WORD__ respectivamente.
- __TAG__: Identifica que bloque esta en una línea dada.
- __SET__: Identifica el *set* donde se ubica el bloque.
- __WORD__: Representa el *offset* dentro del bloque identificado.

- Cargar un bloque de memoria:
	1. Se determina el *set* según los bits de __SET__ de la dirección del bloque de memoria.
	2. Se selecciona una entrada disponible del *set* identificado. En caso de estar todo el *set* ocupado, se utiliza una *política de reemplazo*.
	3. La entrada seleccionada es "desalojada" y el bloque de memoria es "alojado" en dicha línea. Actualizando el valor __TAG__ de dicha entrada.
- Buscar un bloque de memoria:
	1. Se determina el *set* según los bits de __SET__ de la dirección.
	2. Se comparan los campos __TAG__ de el *set* identificado.
	3. En caso de que alguna comparación coincida, se produce un *hit*. En otro caso, se produce un *miss*.
	4. Se utiliza el *offset* __(WORD)__ respecto a la dirección para acceder a un byte especifico.
## Algoritmos de Reemplazo
- LRU (Least Recently Used): Selecciona la línea que maximice el tiempo de último acceso.
- FIFO (First In, First Out): Selecciona la línea que contenga el bloque con mayor "antigüedad".
- LFU (Least Frequently Used): Selecciona la línea que tenga la menor cantidad de accesos.
- Random: Selecciona una línea de forma aleatoria.

## Coherencia de Cache
El problema de la coherencia de la cache es el que se genera por la necesidad de que el contenido de la cache y el de la memoria principal debe estar sincronizado en todo momento.

- Write-Through: En un esquema *wirte-though*, la escrituras siempre actualizan el cache y el próximo nivel inferior de la jerarquía de memoria, asegurando que la información siempre sea consistente entre ambos niveles.
- Write-Back: En un esquema *write-back*, las escrituras solo actualizan la cache, luego, cuando se reemplace este bloque, se actualiza el próximo nivel inferior de la jerarquía. *Se agrega un dirty-bit, para indicar si el bloque fue modificado mientras se encontraba en cache o no*.
- [[Sistemas#Multiprocessor System|Multiprocessor System]]: Se resuelve de igual forma que *write-through* y *write-back*. Sin embargo, en el caso de *write-back*, puede surgir la situación donde varios [[CPU]] tengan de forma simultanea el mismo bloque en cache, de esta forma, si uno de estos actualiza este bloque surge inconsistencia sobre este bloque. Por ende, es necesario tener un sistema de monitoreo cruzado entre todos los sub-sistemas de memoria cache de todos los [[CPU|CPU'S]].
