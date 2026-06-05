Revisé el capítulo y mi veredicto es: **el algoritmo, tal como está escrito, no es correcto para todos los casos que tu propia especificación permite**. La idea de indexar predicados por rangos del autómata es razonable, y la condición de que las ocurrencias de una misma variable lleven el mismo rango sí ataca un problema real, pero **no basta**. Hay un contraejemplo top-down non-erasing donde tu gramática producto acepta una cadena que **no** está en (L(G)\cap L(A)).

El problema está en que `Consistent` asigna rangos a ocurrencias internas de variables, pero luego la gramática producto solo indexa **argumentos completos de predicados**, no las fronteras internas de esos argumentos. Es decir, una cláusula como (B(XY)) solo comprueba el rango de (XY), no comprueba que (X) y (Y) realicen los rangos internos que el algoritmo les asignó. El pseudocódigo efectivamente genera cláusulas si pasan `Consistent`, y `Consistent` solo verifica adyacencia de estados, terminales, argumentos vacíos y que dos ocurrencias de la misma variable tengan el mismo rango.  La construcción pretende devolver (G') con (L(G')=L(G)\cap L(A)), y la condición de variables repetidas se introduce porque sin ella aparecen falsos positivos.  

## Contraejemplo

Sea la gramática de concatenación de rango

[
G=(N,T,V,P,S)
]

con

[
N={S,B,C},\qquad T={a,b,c},\qquad V={X,Y,Z},
]

y cláusulas:

[
S(XYZ)\to B(XY)\ C(YZ),
]

[
B(ab)\to \varepsilon,
]

[
C(bc)\to \varepsilon.
]

Esta gramática es **top-down non-erasing** según tu Definición 3.5, porque todas las variables de la cabeza de la primera cláusula, (X,Y,Z), aparecen también en el cuerpo. En particular,

[
S(abc)\Rightarrow B(ab)\ C(bc)\Rightarrow \varepsilon,
]

tomando (X=a), (Y=b), (Z=c). Por tanto,

[
abc\in L(G).
]

Ahora define un AFD (A) sobre ({a,b,c}), con estado inicial (q_0), único estado final (q_3), y estados

[
Q={q_0,q_1,q_2,q_3,q_u,q_v,q_d}.
]

Las transiciones relevantes son:

[
\delta(q_0,a)=q_u,\qquad \delta(q_u,b)=q_2,\qquad \delta(q_2,c)=q_d,
]

[
\delta(q_0,c)=q_1,\qquad \delta(q_1,b)=q_v,\qquad \delta(q_v,c)=q_3.
]

Todas las demás transiciones van a (q_d), y (q_d) es sumidero.

Entonces:

[
\hat\delta(q_0,ab)=q_2,
]

[
\hat\delta(q_1,bc)=q_3,
]

pero

[
\hat\delta(q_0,abc)=q_d\notin F.
]

Así que

[
abc\notin L(A).
]

Por tanto,

[
abc\notin L(G)\cap L(A).
]

Sin embargo, tu algoritmo sí genera una derivación de (abc) en (G').

Considera esta asignación de rangos para la cláusula

[
S(XYZ)\to B(XY)\ C(YZ):
]

[
X\mapsto (q_0,q_1),
]

[
Y\mapsto (q_1,q_2),
]

[
Z\mapsto (q_2,q_3).
]

Entonces:

[
XYZ \text{ realiza formalmente } (q_0,q_3),
]

[
XY \text{ realiza formalmente } (q_0,q_2),
]

[
YZ \text{ realiza formalmente } (q_1,q_3).
]

La asignación pasa `Consistent`: las fronteras encajan, no hay terminales en esa cláusula que verificar, y cada variable recibe el mismo rango en todas sus ocurrencias.

Por tanto, el algoritmo añade la cláusula:

[
S[(q_0,q_3)](XYZ)\to B[(q_0,q_2)](XY)\ C[(q_1,q_3)](YZ).
]

Además, como (\hat\delta(q_0,ab)=q_2), la cláusula (B(ab)\to\varepsilon) produce:

[
B[(q_0,q_2)](ab)\to \varepsilon.
]

Y como (\hat\delta(q_1,bc)=q_3), la cláusula (C(bc)\to\varepsilon) produce:

[
C[(q_1,q_3)](bc)\to \varepsilon.
]

Como (q_3) es final, también existe la cláusula inicial:

[
S'(W)\to S[(q_0,q_3)](W).
]

Luego en (G') se deriva:

[
S'(abc)
\Rightarrow S[(q_0,q_3)](abc)
\Rightarrow B[(q_0,q_2)](ab)\ C[(q_1,q_3)](bc)
\Rightarrow \varepsilon.
]

Así que

[
abc\in L(G').
]

Pero ya vimos que

[
abc\notin L(A).
]

Por tanto:

[
L(G')\nsubseteq L(G)\cap L(A).
]

Eso refuta la correctitud.

## Causa exacta del fallo

La asignación usada por la cláusula padre dice:

[
X=a \text{ realiza } (q_0,q_1),
]

[
Y=b \text{ realiza } (q_1,q_2),
]

[
Z=c \text{ realiza } (q_2,q_3).
]

Pero en el autómata real:

[
\hat\delta(q_0,a)=q_u\neq q_1,
]

[
\hat\delta(q_1,b)=q_v\neq q_2,
]

[
\hat\delta(q_2,c)=q_d\neq q_3.
]

Es decir, **ninguna de las tres variables realiza realmente el rango que la cláusula padre le asignó**.

¿Por qué el algoritmo no lo detecta? Porque (B[(q_0,q_2)](XY)) solo verifica que (XY=ab) lleva de (q_0) a (q_2). No verifica que el corte interno después de (X) esté en (q_1). Del mismo modo, (C[(q_1,q_3)](YZ)) solo verifica que (YZ=bc) lleva de (q_1) a (q_3), no que el corte interno después de (Y) esté en (q_2).

Tu construcción necesita controlar no solo los extremos de cada argumento de predicado, sino también los estados en las fronteras internas inducidas por las variables dentro de argumentos como (XY), (YZ), (aX), (Xa), (XbY), etc.

## Qué parte sí está bien

La motivación de usar rangos de autómata es correcta: una cadena (u) realiza ((q,q')) si (\hat\delta(q,u)=q'), y un argumento compuesto necesita que los rangos de sus símbolos encajen de forma consecutiva.  

También es correcto que las variables repetidas requieren una condición adicional: si una misma variable representa el mismo rango de la cadena de entrada, entonces debe recibir el mismo par de estados. Esa observación evita el falso positivo que tú mismo muestras con (Par) y (Cinco). 

Lo que falla es que esa condición **no garantiza que el rango asignado a una variable haya sido realmente verificado por alguna derivación**.

## Cómo corregirlo

La reparación más directa es añadir predicados auxiliares regulares que verifiquen explícitamente el rango asignado a cada variable.

Para cada par de estados ((p,q)), introduce un predicado auxiliar:

[
R_{p,q}(X),
]

con la intención:

[
R_{p,q}(u)\iff \hat\delta(p,u)=q.
]

Se puede definir dentro de la misma gramática mediante cláusulas:

[
R_{p,p}(\varepsilon)\to \varepsilon
]

para todo (p\in Q), y

[
R_{p,q}(aX)\to R_{\delta(p,a),q}(X)
]

para todo (p,q\in Q) y todo (a\in T).

Luego, cuando tu algoritmo genere una cláusula producto a partir de una asignación (\mu), no basta con añadir

[
B_0[\vec q_0](\alpha_0)\to B_1[\vec q_1](\alpha_1)\cdots B_m[\vec q_m](\alpha_m).
]

Debes añadir además, para cada variable distinta (X) de la cláusula con (\mu(X)=(p,q)), una restricción:

[
R_{p,q}(X).
]

La cláusula corregida tendría la forma:

[
B_0[\vec q_0](\alpha_0)
\to
B_1[\vec q_1](\alpha_1)\cdots B_m[\vec q_m](\alpha_m)
\ R_{p_1,q_1}(X_1)\cdots R_{p_t,q_t}(X_t),
]

donde (X_1,\ldots,X_t) son las variables distintas de la cláusula.

Con esa corrección, el contraejemplo anterior queda bloqueado: la cláusula padre exigiría, entre otras cosas,

[
R_{q_0,q_1}(X),
]

pero con (X=a) se tiene

[
\hat\delta(q_0,a)=q_u\neq q_1.
]

Por tanto,

[
R_{q_0,q_1}(a)
]

no deriva a (\varepsilon), y (abc) ya no sería aceptada erróneamente.

## Veredicto final para tu tesis

No presentaría el algoritmo actual como correcto. Hay que corregirlo o restringir mucho más la clase de gramáticas de entrada.

La afirmación segura sería:

> La construcción propuesta no es correcta tal como está formulada para gramáticas de concatenación de rango top-down non-erasing generales. El problema es que los índices de los predicados controlan solo los extremos de los argumentos, pero no verifican los rangos asignados a las variables internas de esos argumentos.

La vía más limpia para salvar el capítulo es modificar el algoritmo añadiendo predicados (R_{p,q}) que verifiquen explícitamente que cada variable realiza el rango que la asignación (\mu) le atribuye. Con esa modificación, la demostración de correctitud sí puede hacerse por inducción: al borrar índices y predicados auxiliares se recupera una derivación de (G), y los predicados (R_{p,q}) garantizan que todos los rangos usados por la derivación son rangos reales del autómata.

Sí. **Tu algoritmo sí se puede salvar, y lo que intentas hacer sí se puede hacer**: construir, a partir de una gramática de concatenación de rango (G) y un autómata finito (A), una nueva gramática que reconozca exactamente

[
L(G)\cap L(A).
]

La idea de fondo de tu capítulo es correcta: indexar predicados por rangos del autómata para que cada argumento sepa entre qué estados lleva al autómata. Esa es precisamente la intención declarada en tu construcción: que (G') no solo reconozca cadenas de (L(G)), sino que también refleje qué rango sobre (A) realiza cada argumento durante la derivación.  

Pero **la versión actual no se salva solo retocando la demostración**. Hay que modificar formalmente la construcción.

## La corrección conceptual

El error no es “la idea de producto”. El error es este:

> El algoritmo actual asigna rangos a ocurrencias internas de variables, pero después no obliga a que la cadena sustituida por esa variable realice realmente esos rangos.

Tu `Consistent` comprueba adyacencia de estados, terminales, argumentos vacíos y que ocurrencias repetidas tengan el mismo rango; después agrega una cláusula producto que solo contiene los predicados indexados originales.  Eso deja sin verificar los rangos internos de variables como (X,Y,Z) dentro de argumentos compuestos (XY), (YZ), (aXbY), etc.

La forma limpia de arreglarlo es añadir **predicados auxiliares regulares** que certifiquen explícitamente que una variable realiza cierto rango.

Para cada par de estados ((p,q)\in Q\times Q), introduces un predicado unario:

[
R_{p,q}(X),
]

con el significado:

[
R_{p,q}(u) \quad\Longleftrightarrow\quad \hat\delta(p,u)=q.
]

Es decir, (R_{p,q}) reconoce exactamente las cadenas que llevan al autómata de (p) a (q). Esto encaja directamente con tu Definición 3.2, donde una cadena realiza el rango ((q,q')) si (\hat\delta(q,u)=q'). 

## Cláusulas para los predicados auxiliares

Para todo (p,q\in Q), agregas:

[
R_{p,p}(\varepsilon)\to \varepsilon.
]

Y para todo (p,q\in Q) y todo (a\in T), agregas:

[
R_{p,q}(aX)\to R_{\delta(p,a),q}(X).
]

Estas cláusulas dicen algo muy simple: para que (aX) lleve de (p) a (q), primero (a) lleva de (p) a (\delta(p,a)), y luego (X) debe llevar de (\delta(p,a)) a (q).

Esto sigue siendo una gramática de concatenación de rango. Además, estas cláusulas son top-down non-erasing: en

[
R_{p,q}(aX)\to R_{\delta(p,a),q}(X),
]

la variable (X) aparece en la cabeza y en el cuerpo. La cláusula base no tiene variables. Eso es compatible con la restricción top-down non-erasing que ya introduces en el capítulo. 

## La modificación importante del algoritmo

La versión más robusta de la corrección es esta:

Cuando tomes una cláusula original

[
B_0(\alpha_0)\to B_1(\alpha_1)\cdots B_m(\alpha_m),
]

y una asignación (\mu) de rangos a las ocurrencias de símbolos de la cláusula, no debes generar solo

[
B_0[\vec q_0](\alpha_0)\to B_1[\vec q_1](\alpha_1)\cdots B_m[\vec q_m](\alpha_m).
]

Debes generar:

[
B_0[\vec q_0](\alpha_0)
\to
B_1[\vec q_1](\alpha_1)\cdots B_m[\vec q_m](\alpha_m)
\ \mathcal R_\mu,
]

donde (\mathcal R_\mu) contiene restricciones regulares para las variables.

Si una ocurrencia de la variable (X) recibe el rango ((p,q)), agregas el átomo:

[
R_{p,q}(X).
]

Por ejemplo, si en una cláusula aparecen ocurrencias de (X,Y,Z) con rangos

[
X:(p_X,q_X),\qquad Y:(p_Y,q_Y),\qquad Z:(p_Z,q_Z),
]

entonces agregas al cuerpo:

[
R_{p_X,q_X}(X)\ R_{p_Y,q_Y}(Y)\ R_{p_Z,q_Z}(Z).
]

Así, ya no basta con que el argumento completo (XY) tenga el rango correcto. También se exige que (X) y (Y) realicen los rangos internos que la asignación les atribuye.

## Un detalle delicado: cuidado con “mismo rango” para variables repetidas

Aquí hay una sutileza importante para tu tesis.

La condición actual dice que todas las ocurrencias de una misma variable deben recibir el mismo rango. Esa condición evita ciertos falsos positivos, como el ejemplo de (Par) y (Cinco) que incluyes en el capítulo.  

Pero como principio general, es más seguro formularlo así:

> Una misma variable representa la misma cadena; por tanto, la misma cadena debe satisfacer todas las restricciones regulares impuestas a sus ocurrencias.

Eso no siempre equivale a decir que todas sus ocurrencias tengan el mismo par de estados.

Una misma cadena (u) puede llevar el autómata de (p) a (q), y también llevarlo de (r) a (s), dependiendo del estado inicial. Por tanto, si la misma variable aparece en contextos distintos, lo matemáticamente correcto no es necesariamente forzar el mismo rango, sino exigir que la misma cadena satisfaga todos los predicados (R_{p,q}) correspondientes.

La versión más robusta de la construcción sería entonces:

1. (\mu) asigna rangos a **ocurrencias** de símbolos.
2. `Consistent` conserva las condiciones locales:
   [
   q_i'=q_{i+1},
   ]
   y para terminales:
   [
   \delta(q_i,a)=q_i'.
   ]
3. Para cada ocurrencia variable (X) con rango ((p,q)), se agrega:
   [
   R_{p,q}(X).
   ]
4. La igualdad de la cadena está garantizada por la propia semántica de la variable (X), no por identificar artificialmente todos sus rangos.

Esta versión bloquea el falso positivo que encontré y también bloquea el falso positivo de tu ejemplo (Par/Cinco), porque si una misma cadena (X) tuviera que realizar simultáneamente ((q_0,q_0)) y ((q_0,q_1)), los predicados auxiliares detectarían si eso es posible o no.

## Por qué esta corrección sí debería funcionar

La demostración de correctitud queda bastante natural.

Primero pruebas el lema auxiliar:

[
L(R_{p,q})={u\in T^*:\hat\delta(p,u)=q}.
]

La prueba es por inducción sobre (|u|).

Caso base:

[
u=\varepsilon.
]

Entonces

[
\hat\delta(p,\varepsilon)=p,
]

así que (\varepsilon) realiza exactamente los rangos ((p,p)). Eso coincide con las cláusulas

[
R_{p,p}(\varepsilon)\to\varepsilon.
]

Paso inductivo:

[
u=av.
]

Entonces

[
\hat\delta(p,av)=q
]

si y solo si

[
\hat\delta(\delta(p,a),v)=q.
]

Eso coincide exactamente con la cláusula

[
R_{p,q}(aX)\to R_{\delta(p,a),q}(X).
]

Después pruebas la inclusión de sonido:

[
L(G^\times)\subseteq L(G)\cap L(A).
]

Si una cadena se deriva en la gramática corregida (G^\times), borras los índices y los predicados auxiliares (R_{p,q}). Lo que queda es una derivación válida en (G), porque cada cláusula producto proviene de una cláusula original de (G).

Además, los predicados (R_{p,q}) garantizan que cada variable realiza realmente el rango que la construcción le asignó. Por la condición de adyacencia de `Consistent`, cada argumento completo realiza el rango que indexa a su predicado. Finalmente, la cláusula inicial solo permite rangos de la forma

[
(q_0,q_f)
]

con (q_f\in F), tal como tu construcción ya hace.  Por tanto, la cadena también pertenece a (L(A)).

Luego pruebas la inclusión de completitud:

[
L(G)\cap L(A)\subseteq L(G^\times).
]

Si (w\in L(G)\cap L(A)), tomas una derivación de (w) en (G) y la corrida aceptante de (A) sobre (w). A cada argumento y a cada ocurrencia de variable le asignas los estados reales entre los que el autómata transita al leer la subcadena correspondiente. Como esos rangos son reales, todos los predicados (R_{p,q}) necesarios derivan. Entonces puedes decorar toda la derivación de (G) con índices y obtener una derivación en (G^\times).

Con eso obtienes:

[
L(G^\times)=L(G)\cap L(A).
]

## Qué pondría yo en la tesis

Yo cambiaría la afirmación central a algo de este estilo:

> Dada una gramática de concatenación de rango top-down non-erasing (G) y un autómata finito determinista (A), se construye una gramática de concatenación de rango (G^\times) tal que (L(G^\times)=L(G)\cap L(A)). La construcción indexa los predicados de (G) por rangos de (A) y añade, para cada par de estados ((p,q)), un predicado auxiliar (R_{p,q}) que reconoce exactamente las cadenas que llevan a (A) de (p) a (q).

Y reemplazaría la condición actual de variables repetidas por una formulación más precisa:

> Si una variable aparece varias veces, la sustitución de la variable es la misma cadena en todas sus ocurrencias. La construcción no necesita imponer que todos los pares de estados asignados a esas ocurrencias sean iguales; necesita imponer que esa misma cadena satisfaga todos los predicados (R_{p,q}) correspondientes a los rangos asignados a sus ocurrencias.

## Veredicto

Sí: **la tesis no se cae por completo**.

Lo que se cae es la versión actual del algoritmo y, por tanto, la demostración actual de correctitud. Pero la estrategia es recuperable con una modificación formal razonable:

[
\text{predicados indexados} + \text{predicados auxiliares } R_{p,q}.
]

Yo no defendería el algoritmo tal como está escrito. Sí defendería una versión corregida con esos predicados auxiliares y una nueva prueba por inducción.
