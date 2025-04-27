# E2-GeneraciónGramatica_A01712547
### By Juan José Goyeneche Sánchez | A01712547
---------------------------------------------
### Descripción
--------------------------
La gramatica en la implementación de lenguajes en metodos computacionales, esta delimita las reglas y estructura de estos lenguajes, creando una syntaxis que debera seguirse. Esta gramatica funciona a traves de estados los cuales terminan llegando a un punto terminal, estos estados sirven para delimitarlas estructuras que pueden llegar a formar los lenguajes con los puntos terminales. Para este proyecto tendremos que hacer uso de esto para crear la gramatica o almenos una parte del idioma Aleman.

Un Ejemplo simple:
```
S -> Sujeto
Sujeto -> Nombre | Pronombre N
N -> carro | casa | hombre | ε
Nombre -> paul
Pronombre -> El | La 
```
Este ejemplo nos muestra como a traves de los estados se establezcan reglas.  En este caso lo aceptado seria: 
  * La Casa
  * El Carro
  * El
  * Paul
    
Pero no se reconoceran los siguientes como sujeto por su error gramatical:
  * El Paul
  * Carro 

#### Parser LL
 Para generar nuestra gramatica usaremos un parser LL(1), este es un analizador descendente de las gramaticas libres de contexto. Este analizador procesa las entradas de izquierda a derecha y genera derivaciones por la izquierda de una oración o enunciado. Para esto se deberan seguir las siguientes dos reglas: 
  * Sin ambiguedad
  * Sin recursion izquierda
##### Ambiguedad 
Una gramática libre de contexto es ambigua si existe al menos una cadena terminal que puede ser generada por dos o más árboles de derivación diferentes. 
Ejemplo: 
```
S -> P | L | R
P -> O K
L -> hola mundo
O -> hola
K -> mundo
```
En este ejemplo hay diferentes maneras de llegar a la cadena "hola mundo", esto ocasionara que el parser no sepa que estructura elegir y hay un problema de sintaxis.

##### Recursion Izquierda
La recursión izquierda ocurre en una gramática cuando una regla de producción tiene una llamada recursiva a sí misma como primer elemento del lado derecho.
Ejemplo: 
```
A → A c | ε
```
Esta recursion provoca que el parser empiece nuevamente A sin terminar la tarea de a, por lo cual entrara en un bucle, lo que se busca evitar.

------------------------------
Primero veamos el punto de inflección de cuando se termino la gramatica y justo antes de que se empezara a modificar para evitar ambiguedad y una recursion Izquierda. Mientras estuve realizando esto tambien fui pensando en evitar la ambiguedad, esto facilita el siguiente paso de modificar para evitar ambiguedad y recursion izquierda, porque como se vera son pocas las cosas a cambiar, pero de igual manera limita la creación de la gramatica. Hubieron muchas decisiones que se tomaron pensando en esto, que limitaron el lenguaje, como al final quitar la separacion de articulos dependiendo el caso o genero, O el delimitar verbos en base a su conjugación temporal. Esto hubiera generado de manera potencial ambiguedad en la gramatica, y aunque podria llegar a hacerse, siendo especialmente el Aleman un idioma tan rico y donde la estructura de la oracion cambia mucho dependiendo de las conjugaciones, nos hubiera quedado una gramatica MUCHISIMO mas grande. a continuacion unos ejemplos de donde hubiera existido un problema con la ambiguedad, y como se podria haber encontrado una solucion:

Problema Ambiguedad: 

 ```
 !-----------Lenguaje Aleman--------------------

! ------------------------Inicio -----------------------
Sent -> StandSent Sent| QuestSent Sent| SubSent Sent | ε
StandSent -> StandSentAux FinS
StandSentAux ->  S StSaux
StSaux -> P | ε
QuestSent -> QuestSaux FinInt
QuestSAux -> (QSiNo | QResP | QPrep)
QSiNo -> QStand
QResP -> ProInt QStand
QPrep -> PrepInt QStand
QStand -> V S Obj Rest
Conj -> 'und' | 'oder' | 'aber' | ','
FinS -> '.'
FinInt -> '?'

PrepInt -> 'mit wem' | 'an wen' | 'worüber' | 'auf wen'
ProInt -> 'wer' | 'was' | 'wann' | 'wo' | 'warum' | 'wie' | 'wohin' | 'woher'

SubSent -> StandSentAux ',' SubClause FinS | SubSentAux ',' StandSentAux FinS
SubSentAux -> S Obj Rest V
SubClause -> ConjSub SubSentAux
ConjSub -> 'dass' | 'weil' | 'wenn' | 'obwohl'

! -------------------Sujeto------------------------------
S -> S Conj N
A -> 'der' | 'die' | 'das' | 'dem' | 'den' | 'des'
Aind -> 'ein' | 'eine' | 'keine' | 'kein' | 'einem' | 'keinem' | 'keiner' | 'einen' | 
N -> A Noun | Name | Pron
Name -> Nom | Title Nom
Noun -> 'Katze' | 'Hund' | 'Team' | 'Mann' | 'Schwester' | 'Elephant' | 'Fußball' | 'Film' | 'Freizeit' | 'Haus' | 'Auto' | 'Buch' | 'Stadt' | 'Schule' | 'Liebe' | 'Zeit' | 'Problem' | 'Park' | 'Wasser' | 'Brot' | 'Kaffee' | 'Essen' |
Nom -> 'Mark' | 'Clinton' | 'Juan' | 'Jose' | 'Carla' | 'Carlos' | 'Sofia' | 'Paul'
Title -> 'Herr' | 'Fräulein' | 'Frau'
Pron -> 'ich' | 'du' | 'er' | 'sie' | 'es' | 'wir' | 'ihr' | 'Sie'

! -------------- Predicado ----------------------------------

P -> V Obj Rest | 
V -> 'spielt' | 'schwimmt' | 'ist' | 'sieht' | 'hat' | 'weiß' | 'denkt' | 'sieht' | 'hört' | 'liest' | 'schreibt' 
Obj -> Oaux Obj' | ε
Obj' -> Conj Obj | ε
Oaux -> JustObj | PrepObj
JustObj -> A Noun | Aind Noun | Noun
PrepObj -> Prep N
Prep -> 'mit' | 'ohne' | 'auf' | 'an' | 'in' | 'für' | 'über' | 'unter'
Rest ->  RestAux | RestAux Conj Rest
RestAux -> Neg Adv Adj | Adj | Adv Adj | Neg | ε
Adv -> 'immer' | 'sehr' | 'nie' | 'oft' | 'hier' | 'dort' | 'heute' | ε
Adj -> 'schön' | 'laut'  | 'klein' | 'intelligent' | 'dumm' | 'freundlich' | 'traurig'
Neg -> 'nicht' | ε
 ```
-----------------------------------
###### Problema Recursividad Izq
Encontramos un problema de recursividad izquierda en el avance anterior, esto se debe a que se tiene una llamada recursiva a sí misma como primer elemento del lado derecho.
 ```
S -> S Conj N
 ```
En este caso cuando se llega a S se buscara alcanzar a escribir un n (noun), el problema aqui es que S se llamara a si misma sin saber si se llego a N o no. 

![image](https://github.com/user-attachments/assets/ccf08d43-4f28-4ba6-b10c-f5b9d4243dd2)

Para arreglar esto debemos de pasar S hasta despues de cualquier tarea, osea a la derecha del todo, pero claro, tambien tenemos el tema de agregar un 'Conj' entre N y N, lo mejor en este caso es crear un estado auxiliar al cual llamar, si se quiere regresar a S.
```
S -> N S'
S' -> Conj S | ε
```
Aca la diferencia es que se vuelve a llamar a S, pero esto cuando ya se haya se encuentre N en el stack y el remaining input sea otro, en caso de ser otro N, la funcion auxiliar realizara 'Conj' y tras eso regresara a S, ya sin ninguna tarea pendiente. En caso de querer terminar, en el auxiliar se tomara epsilon y se ira a la siguiente tarea.

###### Problema Ambiguedad
El siguiente problema de ambiguedad mas estados, en la gramatica del mostrada anteriormente, ocurre en varias situaciones, especialmente entre distintos tipos de oraciones. Incluso la principal razón para no separar verbos, pronombres, etc entre conjugaciones, tiempo y generos y  ponerlos todos en un mismo estado fue que esto generaria casos donde se compartan terminales entre estados y la vuelvan ambigua. El siguiente ejemplo es un poco mas claro pero aun complejo y la respuesta no es tan facil como agregar algo que diferencia las terminales.
```
RestAux -> Neg Adv Adj | Adj | Adv Adj | Neg | ε
Adv -> 'immer' | 'sehr' | 'nie' | 'oft' | 'hier' | 'dort' | 'heute' | ε
Adj -> 'schön' | 'laut'  | 'klein' | 'intelligent' | 'dumm' | 'freundlich' | 'traurig'
Neg -> 'nicht' | ε
```
Primero expliquemos que nos encontramos con lo que va despues del verbo, su negacion, adverbio o adjetivo. Existen muchas posibilidades en el lenguaje para usarlas.
- Spielt sehr gut (jueg@ muy bien)
- Spielt nicht (no jueg@)
- Spielt gut (jueg@ bien)
- Spielt nicht gut (no jueg@ bien)
Entendiendo los posibles casos, nos encontramos que en caso de usar Rest, usaremos siempre Adj o Neg.
Ahora veamos el error de ambiguedad, esta existe porque podemos crear de muchas maneras un terminal. Ejemplo, al crear 'schön' (bello).
- Opcion 1: RestAux -> Adj -> 'schön'
- Opcion 2: RestAux-> Adv Adj -> Adv -> ε -> Adj -> 'schön'
- Opcion 3: RestAux-> Neg Adv Adj -> Neg -> ε -> Adv -> ε -> Adj -> 'schön'
Asi como usamos este ejemplo realemente encontramos mucha ambiguedad con otras terminaciones en esta parte de la gramatica, para cambiarlo hay muchas maneras en realidad. La que se usa aqui no es la manera mas elegante, pero al identificar que la mayoria de problemas venian desde los epsilon en las terminales, se decidio quitar esos epsilon. Dar a RestAux unas opciones que no puedan coencidir entre ellas mismas, y que el epsilon se encuentre antes de llamar a RestAux.
```
Rest ::= RestAux Rest1
Rest ::= ''
Rest1 ::= Conj RestAux 
Rest1 ::= ''
RestAux ::= Neg Adj
RestAux ::= Adv Adj
Neg ::= ''
Neg ::= nicht
```


#### Tipo de Gramatica
Basandonos en 'The Extended Chomsky Hierarchy´ encontramos que esta gramatica basada en el idioma aleman que hicimos, es una gramatica libre de contexto. Esto pasa porque no existe dependencia, osease no existe contexto.  Esto se debe a que en todas las reglas, el lado izquierdo contiene únicamente variables y no terminales, lo que impide que se clasifique como una gramática que dependa de un contexto. Por esto que de igual manera esta relacionado a lo que se comento arriba sobre la ambiguedad, es que esta gramatica pertenece a un lenguaje de tipo dos.

![image](https://github.com/user-attachments/assets/84603bf9-4776-4837-a3ce-d35d35d116be)

--------------------------------------
#### Complejidad
La complejidad de una gramatica como esta no depende realmente de la gramatica en si, si no del parser que se usara para analizar esta. En este caso usaremos un parser LL(1), la gramatica aqui el rol que juega es que en caso de tener recursividad izquierda o ambiguedad, necesitariamos usar otro parser que tendria una complejidad diferente, aunque probablemente sea mas "potente". El Parser LL(1) tiene muchos aspectos que vuelven la complejidad muy simple en comparación a otros parsers.
 * Busqueda Decendente de izquierda a derecha.
 * Construye derivaciónes por la izquierda.
 * Un Lookahead.
   
Como funciona esto es que el parser hara uso de las tablas generadas para buscar la ruta al destino deseado basandose en el tocken del stack en el que se encuentre, pero hay que tomar en cuenta que va de uno en uno. Esto lo vuelve menos practico o potente lo cual exige mas dificultad al crear gramaticas, pero lo volvera menos complejo. 
* O(n)
![image](https://github.com/user-attachments/assets/a1ce8dba-a48e-4db5-8018-7dbf1398abf6)

Esta es la complejidad de nuestro parser LL(1), donde n dependera de la cadena que mandemos a analizar. De igual manera el peor caso y el mejor caso posible seran O(n), el mejor caso sera O(1).

#### Referencias
GeeksforGeeks (06 Feb 2025). Construction of LL(1) Parsing Table. https://www.geeksforgeeks.org/construction-of-ll1-parsing-table/


Kumari, S. (17 Mar 2025). Chomsky Hierarchy. Naukri Code 360. https://www.naukri.com/code360/library/chomsky-hierarchy


Moreno, M. (December 2, 2004) Elimination of left recursion https://www.csd.uwo.ca/~mmorenom/CS447/Lectures/Syntax.html/node8.html


Straub, J. (7 Oct 2024). How to form basic German sentences. Lingoda. https://www.lingoda.com/blog/en/how-to-form-basic-german-sentences/


Princeton (n.d.). LL(1) Parser Visualization. https://www.cs.princeton.edu/courses/archive/spring20/cos320/LL1/
