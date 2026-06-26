El problema de la satisfacibilidad booleana (SAT) se estudia en este trabajo
desde la teorı́a de lenguajes formales. En la lı́nea de investigación en la que se
inscribe, la satisfacibilidad de una fórmula se reduce al vacı́o de la intersección
de un lenguaje regular de interpretaciones con una gramática que impone la
consistencia entre las ocurrencias de cada variable booleana.
Con gramáticas menos expresivas esa reducción se limita a fórmulas con
cierto orden de las ocurrencias: con una gramática libre del contexto no se ad-
miten cruces entre ellas, y con una gramática de concatenación de rango simple
(sRCG) la aridad crece con la fórmula. En un intento de resolver más fórmulas
en tiempo polinomial se ataca el problema con gramáticas de concatenación de
rango (RCG): con su no linealidad se modela la consistencia de más fórmulas
que con una sRCG y se mantiene mı́nima la aridad. Esto se muestra con una
RCG que reconoce la consistencia de cualquier fórmula de SAT con aridad uno,
uno de los resultados de este trabajo; pero por esa misma no linealidad se pierde
la garantı́a de que el vacı́o sea polinomial.
Se da un algoritmo de fuerza bruta que decide el vacı́o de la intersección de
una RCG con un lenguaje regular finito, en el que se enumeran las cadenas del
autómata y cada una se reconoce en tiempo polinomial con la RCG. Su costo
es exponencial cuando esas cadenas son exponencialmente muchas, ası́ que la
enumeración no es una vı́a factible, pero sirve para entender por qué es necesario
estudiar el problema sobre un único objeto gramatical. Se construye ese objeto,
la gramática producto, y se caracterizan su tamaño y el costo de construirla;
sobre él se puede abordar el vacı́o: hallar un método polinomial para todas las
fórmulas, probar que no existe, o hallarlo solo al restringir la modelación a una
subclase.
Para decidir el vacı́o en tiempo polinomial sobre la gramática producto ha-
ce falta eliminar antes sus predicados y cláusulas no productivos. Aunque su
tamaño no es polinomial, la gramática producto es el objeto sobre el que conti-
nuar el estudio: esa limpieza y el análisis del costo de decidir el vacı́o sobre la
gramática resultante se proponen como trabajo futuro.
Palabras clave: satisfacibilidad booleana, gramáticas de concatenación de ran-
go, vacı́o de la intersección, lenguaje regular finito, no linealidad.