Una *expresión regular* es una cadena de caracteres que describe un *lenguaje regular*. Se define una expresión regular inductivamente de la siguiente manera:

- Caso Base,
	1. $\emptyset$ es una *expresión regular* que define $\emptyset$
	2. $\varepsilon$ es una *expresión regular* que define $\{ \varepsilon \}$
	3. $a$ es una *expresión regular* que define $\{ a \}$
	
- Caso Inductivo,
	Sean $r_1, r_2$ *expresiones regulares* de forma que $L(r_1) = L_1$ y $L(r_2) = L_2$
	1. $(r_1 | r_2)$ es una *expresión regular* que define $L_1 \cup L_2$
	2. $(r_1 \cdot r_2)$ es una *expresión regular* que define $L_1 \cdot L_2$
	3. $(r_1^\ast)$ es una *expresión regular* que define $L_1^\ast$

## Lenguaje Regular
Un leguaje $L$ es regular si existe una [[Expresiones Regulares|Expresiones Regulares]] $r$ tal que $r$ define a $L$.

## Relaciones
### $R_L$
Sea $L \subseteq \Sigma^\ast,\; x,y \in \Sigma^\ast$, definimos la relación $R_L$ como:

$$x R_{L} y \iff \forall z \in \Sigma^\ast \; (xz \in L \land yz \in L) \lor (xz \notin L \land yz \notin L)$$

### $R_M$
Sea $M=\{Q, \Sigma, \delta, q_0, F\}$ un [[Autómata Finito Determinista|AFD]], $x, y \in \Sigma^\ast$, definimos la relación $R_M$ como:

$$xR_My \iff \hat{\delta}(q_0, x) = \hat{\delta}(q_0, y)$$

> [!info] Observación
> $R_M$ es una relación de equivalencia donde $|R_M|$ es igual a la cantidad de estados de $M$.
> 
> Además, para cualquier [[Expresiones Regulares|Lenguaje Regular]] $L$ se cumple que $xR_My \implies xR_Ly$. Por ende, se puede duducir que $|R_L| \leq |R_M|$.

>[!warning] ¡Cuidado!
>No necesariamente se cumple que $xR_Ly \implies xR_My$.

## Propiedades de Clausura
1. **Unión**: Sean $L_1$ y $L_2$ regulares $\implies L_1 \cup L_2$ regular.
2. **Intersección**: Sean $L_1$ y $L_2$ regulares $\implies L_1 \cap L_2$ regular.
3. **Concatenación**: Sean $L_1$ y $L_2$ regulares $\implies L_1 \cdot L_2$  regular.
4. **Diferencia**: Sean $L_1$ y $L_2$ regulares $\implies$ $L_1 - L_2$ regular
5. **Clausura**: Sea $L$ regular $\implies L^\ast$ regular.
6. **Complementario**: Sea $L$ regular $\implies L^c$ regular.
7. **Reflexión**: Sea $L$ regular $\implies L^r$[^1] regular.
8. **Cociente**: Sea $L_1$ regular y $L_2$ un lenguaje arbitrario $\implies$ $\frac{L_1}{L_2}$[^2]  regular.
9. **Sustitución**: Sea $L$ regular y $f$ una función de sustitución $\implies f(L)$ regular.
10. **Homomorfismo**: Sea $L$ regular y $h$ un homomorfismo[^3] $\implies$ $h(L)$ regular.
11. **Homomorfismo Inverso**: Sea $L$ regular y $h$ un homomorfismo $\implies$ $h^{-1}(L)$ regular.

[^1]: Se define el reflexión/reverso de un lenguaje $L$ como $L^r=\{x^r \in \Sigma^\ast: x \in L\}$. 
[^2]: Se define el cociente entre los lenguajes $L_1$ y $L_2$ como $\frac{L_1}{L_2}=\{x \in \Sigma^\ast : \exists y \in L_2 \land xy \in L_1 \}$.
[^3]: Un homomorfismo de cadenas es una función sobre cadenas que sustituye cada símbolo por una cadena determinada. A modo de ejemplo, la función $h$ definida por $h(0) = ab$ y $h(1) = \varepsilon$ es un homomorfismo. Dada cualquier cadena formada por $0s$ y $1s$, todos los $0s$ se reemplazan por la cadena $ab$ y todos los $1s$ se reemplazan por $\varepsilon$. Por ejemplo $h(011)=h(0) \cdot h(1) \cdot h(1) = ab$.
