# Resumen


## Propagacion de error

$\Delta f(x) \approx |f'(\~x)| \cdot \Delta \~x $


$\Delta f(x_1,x_2\;...\;x_n)\approx\sum_{i=1}^{n}\frac{\partial f}{\partial x_i}\cdot\Delta x_i$

## IEEE 754

- **Simple precisión (32 bits)**:
  - 1 bit para el signo
  - 8 bits para el exponente
  - 23 bits para la mantisa

- **Doble precisión (64 bits)**:
  - 1 bit para el signo
  - 11 bits para el exponente
  - 52 bits para la mantisa

#### Formula normalizada

$(-1)^S × (1.M) × 2^{(E - bias)}$

- **Bias**: Es un valor que se resta del exponente para permitir la representación de exponentes negativos. Equivale a $2^k-1$.
  - Para 32 bits, el bias es 127.
  - Para 64 bits, el bias es 1023.

#### Casos

- **Números desnormalizados**: Si todo el exponente es 0, se usa $(-1)^S × (0.M) × 2^{(1 - bias)}$
- **Cero**: Exponente y mantisa 0
- **Infinito**: Todo el exponente en 1 y toda la mantisa en 0 (el bit de signo diferencia $+\infin$ y $-\infin$)
- **NaN (Not a Number)**: Si todo el exponente es 1 pero toda la mantisa no es 0.
- **Normalizados**: Si el exponente no es todo 0 ni todo 1 se utiliza el caso normal.

### Metodos cerrados
#### Teorema de Bolzano

Si f es una funcion continua en un intervalo cerrado [a, b] y cumple las siguientes condiciones:

1. $f(a)$ y $f(b)$ tienen signos opuestos, es decir: $f(a) \cdot f(b) < 0$

Entonces, existe al menos un punto c en el intervalo (a, b) tal que:
$f(c) = 0$

En base a esto se utiliza el metodo de biseccion que funciona como una busqueda binaria para halar este $c$.

### Metodos abiertos

#### Metodo de newton
Para una función $f(x)$ y una aproximación inicial $x_0$, la fórmula iterativa se define como:

$x_{n+1} = x_n - \frac{f(x_n)}{f'(x_n)}$


#### Metodo de newton de varias variables
Similarmente:

$\mathbf{x}_{n+1} = \mathbf{x}_n - J(\mathbf{x}_n)^{-1} \mathbf{F}(\mathbf{x}_n)$


### Metodos Iterativos Matriciales

$A = D-L-U$


#### Metodo de Jacobi
Se despeja cada variable en cada ecuacion y se redefine iterativamente.
A partir de 

$$D\,\mathbf{x}_{(k+1)} = (L+U)\,\mathbf{x} _{(k)}+b$$


Se deduce la matriz de iteracion

$$\bf{x}_{(k+1)} = D^{-1}\,(L+U)\,\bf{x} _{(k)}+ D^{-1}\,b$$


#### Metodo de Gauss Seidel
Se despeja cada variable en cada ecuacion y se redefine iterativamente tomando en cuenta las variables ya calculadas.
A partir de 

$(D-L)\,\bf{x}_ {(k+1)} = U\,\bf{x}_{(k)}+b$

Se deduce la matriz de iteracion

$\mathbf{x}_{(k+1)} = (D-L)^{-1}\,U\,\mathbf{x}_{(k)}+ (D-L)^{-1}\,b$



## Funciones útiles de Matlab

#### Norma infinita
si se tiene un vector columna x, se puede usar norm() para hallar el elemento de maxima magnitud:

```norm(x,'inf')```

$\|x\|_{\infty} = \max (|x_1|, |x_2|, ..., |x_n|)$

Se usa en formulas como el error relativo en metodos iterativos matriciales

```matlab
% Devuelve el error relativo
rel_err = norm(x1 - x0,'inf')/norm(x1,'inf');
```

#### Derivadas y el Jacobiano
- Para implementar el método Newton-Raphson, se necesita calcular el **Jacobiano**.
- En MATLAB, puedes usar `jacobian()` para derivar funciones simbólicas y `matlabFunction()` para convertir expresiones simbólicas a funciones numéricas.

  ```matlab
  syms x y
  dJ = jacobian(F(x, y), [x, y]);
  dJ = matlabFunction(dJ); % Convertir Jacobiano simbólico a función numérica
  ```


#### Operadores Elemento a Elemento
Algunos operadores llevan un punto (`.`) para indicar que se trata de una operación **elemento a elemento** en lugar de una operación matricial estandar.
- `.*`, `./`, `.^`: Se utilizan para multiplicar, dividir, o elevar al cuadrado cada elemento de un vector o matriz de forma individual.
  - Ejemplo:
    ```matlab
    A = [1, 2, 3];
    B = [4, 5, 6];
    C = A .* B; % Resultado: [4, 10, 18]
    ```
- Los operadores sin punto (`*`, `/`, `^`) se utilizan para operaciones **matriciales** estándar.
    - Ejemplo:
      ```matlab
      A = [1, 2; 3, 4];
      B = [5, 6; 7, 8];
      C = A * B; % Multiplicación de matrices estándar

#### Comandos Menores Útiles
- `[m, n] = size(A)`: Devuelve el número de filas (`m`) y columnas (`n`) de la matriz `A`.
  ```matlab
  A = [1, 2, 3; 4, 5, 6];
  [m, n] = size(A); % m = 2, n = 3
  ```
- `triu(A)`: Devuelve la parte triangular superior de la matriz `A` (incluyendo la diagonal).
  ```matlab
  A = [1, 2, 3; 4, 5, 6; 7, 8, 9];
  U = triu(A);
  % U =
  % [1, 2, 3]
  % [0, 5, 6]
  % [0, 0, 9]
  ```
- `tril(A)`: Devuelve la parte triangular inferior de la matriz `A` (incluyendo la diagonal).
  ```matlab
  L = tril(A);
  % L =
  % [1, 0, 0]
  % [4, 5, 0]
  % [7, 8, 9]
  ```
  - `L = -tril(A, -1)`: Devuelve la parte triangular inferior de la matriz `A`, excluyendo la diagonal, y cambia el signo de todos sus elementos.
  ```matlab
  A = [1, 2, 3; 4, 5, 6; 7, 8, 9];
  L = -tril(A, -1);
  % L =
  % [ 0,  0,  0]
  % [-4,  0,  0]
  % [-7, -8,  0]
  ```
- `U = -triu(A, 1)`: Devuelve la parte triangular superior de la matriz `A`, excluyendo la diagonal, y cambia el signo de todos sus elementos.
  ```matlab
  U = -triu(A, 1);
  % U =
  % [ 0, -2, -3]
  % [ 0,  0, -6]
  % [ 0,  0,  0]
  ```
