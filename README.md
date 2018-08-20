# `umatrix` - A matrix library for MicroPython

## Features

`umatrix` was written mainly with speed and ease-of-use in mind. It aims to be a simple solution to matrix arithmetic needs in Micropython. It implements basic matrix operations (addition, subtraction, multiplication) as well as determinant (shortened to `det`), `inverse`, `trace`, `transpose`, `copy`, and other functions. The matrix class supports `int`, `float`, and `complex` coefficients, as well as `numpy`-like matrix slicing.

## Examples

### Creating and viewing a matrix
```
>>> from umatrix import *
>>> A = matrix([1, 2, 3], [4, 5, 6], [7, 8, 9])
>>> A
matrix( [1,     2,      3],
        [4,     5,      6],
        [7,     8,      9] )
>>> M = matrix([12, 23, 31], [40, 50, 60], [71, 87, 98])
>>> print(M)
[12,    23,     31,
 40,    50,     60,
 71,    87,     98]
>>> N = matrix([12.516j, 6.345, 7+.171j], are_rows=False)
>>> N
matrix( [   12.516j],
        [     6.345],
        [(7+0.171j)] )
```

### Matrix properties
```
>>> A.order
3
>>> A.is_square
True
>>> N.is_square
False
>>> N.size
(3, 1)
>>> N.rows
[[12.516j], [6.345], [(7+0.171j)]]
>>> N.cols
[[12.516j, 6.345, (7+0.171j)]]
```

### Adding, subtracting, multiplying, raising to the power of n
```
>>> A+M
matrix( [ 13,    25,     34],
        [ 44,    55,     66],
        [ 78,    95,    107] )
>>> M-A
matrix( [11,    21,     28],
        [36,    45,     54],
        [64,    79,     89] )
>>> A*M
matrix( [ 305,   384,    445],
        [ 674,   864,   1012],
        [1043,  1344,   1579] )
>>> M*A
matrix( [ 321,   387,    453],
        [ 660,   810,    960],
        [1105,  1361,   1617] )
>>> A**2
matrix( [ 30,    36,     42],
        [ 66,    81,     96],
        [102,   126,    150] )
>>> M*2
matrix( [ 24,    46,     62],
        [ 80,   100,    120],
        [142,   174,    196] )
>>> 2*M
matrix( [ 24,    46,     62],
        [ 80,   100,    120],
        [142,   174,    196] )
```

### Determinant / `det`
Supports <= 4x4 matrices.
```
>>> A.det
0
>>> M.det
1810
>>> abs(M)
1810
>>> N.det
[Traceback]
AssertionError: Matrix must be square.
```

### Inverse.
Supports <= 4x4 matrices.
```
>>> A.inverse
[Traceback]
AssertionError: Matrix is singular.
>>> M.inverse
matrix( [ -0.1767956,     0.2447514,    -0.09392265],
        [  0.1878453,    -0.5662984,      0.2872928],
        [-0.03867403,     0.3254144,     -0.1767956] )
```

### Rounding a matrix.
Note that calling `round` on a matrix will not work as Micropython has not implemented the `__round__` magic method, so rounding a matrix requires you call its defined `round` method with the accustomed decimal places argument. The `round` method supports `complex` coefficients. The boolean argument `inplace` set to `False` by default makes the changes "in place" when set to `True`.
```
>>> (M*M.inverse).round(5)
matrix( [ 1.0,   0.0,   -0.0],
        [ 0.0,   1.0,    0.0],
        [ 0.0,  -0.0,    1.0] )
>>> N
matrix( [12.516j],
        [6.345],
        [(7+0.171j)] )
>>> N.round(2, True)
>>> N
matrix( [12.52j],
        [6.34],
        [(7+0.17j)] )
```

### Copying a matrix
```
>>> B = M.copy()
>>> M
matrix( [12,    23,     31],
        [40,    50,     60],
        [71,    87,     98] )
>>> B[0] = [2222,2222,2222]
>>> B
matrix( [2222,  2222,   2222],
        [  40,    50,     60],
        [  71,    87,     98] )
>>> M
matrix( [12,    23,     31],
        [40,    50,     60],
        [71,    87,     98] )
>>> M.det == B.det
False
```

### Transpose
```
>>> A
matrix( [1,     2,      3],
        [4,     5,      6],
        [7,     8,      9] )
>>> A.transpose
matrix( [1,     4,      7],
        [2,     5,      8],
        [3,     6,      9] )
>>> N
matrix( [12.516j],
        [6.345],
        [(7+0.171j)] )
>>> N.transpose
matrix( [12.516j,       6.345,  (7+0.171j)] )
```

### Trace
```
>>> A
matrix( [1,     2,      3],
        [4,     5,      6],
        [7,     8,      9] )
>>> A.trace
15
>>> M
matrix( [12,    23,     31],
        [40,    50,     60],
        [71,    87,     98] )
>>> M.trace
160
```

### Eigenvalue and eigenvector checking
`is_eigenvalue` takes the value to check as its single argument. `is_eigenvector` takes the vector and value to check respectively: the vector can be given in tuple/list form or in matrix form. 
```
>>> C = matrix([1, 2], [2, 1])
>>> C
matrix( [1,     2],
        [2,     1] )
>>> C.is_eigenvalue(2.431)
False
>>> C.is_eigenvalue(3)
True
>>> C.is_eigenvalue(-1)
True
>>> C.is_eigenvector((1, -1), -1)
True
>>> C.is_eigenvector(matrix([1, 1], are_rows=False), 3)
True
```

### Equality tests
```
>>> A == M
False
>>> N != A
True
>>> A == A.copy()
True
```

### `apply`
This method takes a function and the boolean `inplace` as its arguments. It applies that function to the matrix's coefficients and either returns a new matrix with the return values or overwrites the initial matrix's coefficients. By default, `inplace = False`.
```
>>> A
matrix( [1,     2,      3],
        [4,     5,      6],
        [7,     8,      9] )
>>> from math import log
>>> A.apply(log).round(2)
matrix( [ 0.0,  0.69,    1.1],
        [1.39,  1.61,   1.79],
        [1.95,  2.08,    2.2] )
>>> A.apply(lambda x: x if x > 5 else 0, inplace=True)
>>> A
matrix( [0,     0,      0],
        [0,     0,      6],
        [7,     8,      9] )
```

### `numpy`-like matrix slicing

Referencing a matrix with a `tuple` of either two `slice`s or a `slice` and an `int` returns a new matrix.
You can also modify matrix coefficients by assigning values to a matrix `slice`.
Note that for changing the value of a specific coefficient, you should use `matrix[idx1][idx2] = new_val` and not a `slice` assignment.
```
>>> Z = matrix([1,2,3,4],[5,6,7,8],[8,9,0,1],[2,3,4,5],[6,7,8,9])
>>> Z
matrix( [1,     2,      3,      4],
        [5,     6,      7,      8],
        [8,     9,      0,      1],
        [2,     3,      4,      5],
        [6,     7,      8,      9] )
>>> Z[:, 3]
matrix( [4],
        [8],
        [1],
        [5],
        [9] )
>>> Z[0, ::2]
matrix( [1,     3] )
>>> Z[1:4, 1:]
matrix( [6,     7,      8],
        [9,     0,      1],
        [3,     4,      5] )
>>> Z[::2, 2]
matrix( [3],
        [0],
        [8] )
>>> Z[::2, 2] = 1111, 1111, 1111
>>> Z
matrix( [   1,     2,   1111,      4],
        [   5,     6,      7,      8],
        [   8,     9,   1111,      1],
        [   2,     3,      4,      5],
        [   6,     7,   1111,      9] )
>>> Z[:, 2:]
matrix( [1111,     4],
        [   7,     8],
        [1111,     1],
        [   4,     5],
        [1111,     9] )
>>> Z[:, 2:] = [[0]*2]*5
>>> Z
matrix( [1,     2,      0,      0],
        [5,     6,      0,      0],
        [8,     9,      0,      0],
        [2,     3,      0,      0],
        [6,     7,      0,      0] )
>>> Z[1, ::2]
matrix( [5,     0] )
>>> Z[1, ::2] = [5555, 5555]
>>> Z
matrix( [   1,     2,      0,      0],
        [5555,     6,   5555,      0],
        [   8,     9,      0,      0],
        [   2,     3,      0,      0],
        [   6,     7,      0,      0] )
>>> Z[4][1] = 9999
>>> Z
matrix( [   1,     2,      0,      0],
        [5555,     6,   5555,      0],
        [   8,     9,      0,      0],
        [   2,     3,      0,      0],
        [   6,  9999,      0,      0] )
```

## Useful functions: `eye`, `fill`, `zeros`, `ones`

Note that for `fill`, `zeros`, and `ones`, if the number of columns is not supplied as a final argument a square matrix is returned.
`eye` always returns a square matrix.

- `eye`: returns the identity matrix.
``````
>>> eye(4)
matrix( [1,     0,      0,      0],
        [0,     1,      0,      0],
        [0,     0,      1,      0],
        [0,     0,      0,      1] )
``````

- `fill`: returns a matrix filled with the coefficient of your choice.
``````
>>> fill(25, 3, 6)
matrix( [25,    25,     25,     25,     25,     25],
        [25,    25,     25,     25,     25,     25],
        [25,    25,     25,     25,     25,     25] )
>>> fill(3.14, A.order)
matrix( [3.14,  3.14,   3.14],
        [3.14,  3.14,   3.14],
        [3.14,  3.14,   3.14] )
``````

- `zeros`: returns a matrix filled with zeros.
``````
>>> zeros(5,4)
matrix( [0,     0,      0,      0],
        [0,     0,      0,      0],
        [0,     0,      0,      0],
        [0,     0,      0,      0],
        [0,     0,      0,      0] )
``````

- `ones`: returns a matrix filled with ones.
``````
>>> ones(4,5)
matrix( [1,     1,      1,      1,      1],
        [1,     1,      1,      1,      1],
        [1,     1,      1,      1,      1],
        [1,     1,      1,      1,      1] )
``````

## Testing Inversion Speed

### Environment

For both the Pyboard v1.1 and Pyboard v1.0 lite tests, no SD card was used.
For each flavour's test, there were only 3 files onboard the flash storage: `boot.py` (factory state), `main.py` (as described above), and `umatrix.py`.

`main.py` consisted of the following lines:
```
from umatrix import *
from utime import ticks_us

def t(func, n):
        # returns the average time for n executions of func in milliseconds
        start = ticks_us()
        for i in range(n):
                _ = func()
        end = ticks_us()
        return ((end-start)/1000)/n

T = matrix([12, 62], [98, 21])  # 2x2
U = T**3        # 2x2 large coefficients

V = matrix([57, 45, 67], [77, 40, 93], [12, 76, 34])    # 3x3
W = V**3        #3x3 large coefficients

X = matrix([10, 49, 36, 54], [88, 61, 53, 20], [31, 42, 53, 64], [9, 75, 60, 75])       # 4x4
Y = X**3        # 4x4 large coefficients
```

Visualisation:
```
>>> T
matrix( [12,    62],
        [98,    21] )

>>> U
matrix( [275148,        428606],
        [677474,        337365] )

>>> V
matrix( [57,    45,     67],
        [77,    40,     93],
        [12,    76,     34] )

>>> W
matrix( [1280099,       1498022,        1732795],
        [1568078,       1786761,        2112958],
        [ 978772,       1245168,        1345452] )

>>> X
matrix( [10,    49,     36,     54],
        [88,    61,     53,     20],
        [31,    42,     53,     64],
        [ 9,    75,     60,     75] )

>>> Y
matrix( [1177869,       1777147,        1597682,        1614846],
        [1535988,       2364798,        2117353,        2152054],
        [1445741,       2205124,        1984654,        2000664],
        [1724826,       2616789,        2351580,        2386851] )
```

### Execution

The timing function `t` was called in the REPL with `n = 5000`. The reported result is the slowest of 3 tests i.e. 3 executions of `t(func, 5000)`.

### Results

A reminder that the results are in milliseconds.

#### Pyboard v1.1

- `umatrix` `v1.1`

| Matrix Size | Small Coefficients | Large Coefficients |
|:-----------:|:------------------:|:------------------:|
| 2x2         | 0.6740602          | 0.7213964          |
| 3x3         | 1.072958           | 1.573              |
| 4x4         | 1.734226           | 4.559215           |



#### Pyboard v1.0 lite

- `umatrix` `v1.1`

| Matrix Size | Small Coefficients | Large Coefficients |
|:-----------:|:------------------:|:------------------:|
| 2x2         | 1.137326           | 1.217149           |
| 3x3         | 1.831495           | 2.671786           |
| 4x4         | 2.979418           | 7.701244           |

There is a clear increase in execution time for either tests as the matrix size and coefficients get larger.

The performance difference between the two flavours is present, but negligeable depending on your application's mercy.