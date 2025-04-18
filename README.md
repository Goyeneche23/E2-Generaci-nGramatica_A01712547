# E2-GeneraciónGramatica_A01712547
### By Juan José Goyeneche Sánchez
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
S -> N S'  
S'-> Conj S | ε
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
RestAux -> Neg Adv Adj | ε
Adv -> 'immer' | 'sehr' | 'nie' | 'oft' | 'hier' | 'dort' | 'heute' | ε
Adj -> 'schön' | 'laut'  | 'klein' | 'intelligent' | 'dumm' | 'freundlich' | 'traurig'
Neg -> 'nicht' | ε
 ```
