# Complejidad computacional del algoritmo de intersección PRCG ∩ regular

Nota de trabajo (no es parte de la tesis). Análisis de complejidad del
algoritmo que, dada una PRCG $G$ *top-down non-erasing* y un AFD $A$,
construye una PRCG $G'$ con $L(G') = L(G) \cap L(A)$.

## Parámetros

Por cada cláusula $c \in P$:

- $|Q|$ — número de estados del autómata $A$.
- $n_c$ — número de variables **distintas** en $c$.
- $K_c$ — suma de las aridades de **todos** los predicados de $c$ (cabeza + cuerpo).
- $\ell_c$ — longitud total de los argumentos de $c$.

Y los máximos sobre las cláusulas: $n_{\max}$, $K_{\max}$, $\ell_{\max}$;
$a_{\max}$ es la aridad máxima de un predicado.

## Tamaño de la salida y tiempo del algoritmo

$$
O\!\left(\sum_{c\in P} \ell_c \cdot |Q|^{\,2 n_c + K_c}\right)
\;=\;
O\!\left(|P|\cdot \ell_{\max}\cdot |Q|^{\,2 n_{\max} + K_{\max}}\right).
$$

De dónde sale el exponente:

- $|Q|^{2 n_c}$ — por enumerar las asignaciones $\Phi$ (un par de estados por cada una de las $n_c$ variables; cada par tiene $|Q|^2$ opciones).
- $|Q|^{K_c}$ — por enumerar los pares inducidos sobre los $K_c$ argumentos (cada argumento aporta a lo sumo $|Q|$ pares inducidos).

## Cota de no-terminales

$$
|N'| \;\le\; |N|\cdot |Q|^{\,2 a_{\max}} + 1.
$$

## Las dos lecturas

1. **Con la gramática $G$ fija** ($n_{\max}, K_{\max}$ constantes): el algoritmo es
   **polinomial en el tamaño del autómata** $|Q|$, de grado $2 n_{\max} + K_{\max}$.
   Es el mismo comportamiento que la construcción de Bar-Hillel para CFG
   (grado 2) y que la de Bertsch-Nederhof para sRCG.

2. **Con $G$ como parte de la entrada**: la dependencia es **exponencial** en la
   aridad de los predicados y en el número de variables (porque $K_{\max}$ y
   $n_{\max}$ están en el exponente). No se puede evitar en general: el problema
   subyacente del vacío de la intersección es NP-completo cuando el lenguaje
   regular es finito (Bertsch-Nederhof, IWPT 2001), así que el blow-up
   exponencial en esos parámetros es inherente, no un defecto del algoritmo.

## Nota sobre el borrador

El borrador `Capitulo3.tex` da la cota $\sum_c |Q|^{2 n_c}$ (sin el factor
$|Q|^{K_c}$), que **subcuenta**. Contraejemplo: la cláusula terminal
$\mathrm{Par}(\varepsilon) \to \varepsilon$ tiene $n_c = 0$, por lo que
$|Q|^{2 \cdot 0} = 1$, pero la construcción genera $|Q|$ cláusulas (una por
estado). La cota correcta es $|Q|^{\,2 n_c + K_c}$.
