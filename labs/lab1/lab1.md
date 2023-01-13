# **One-dimensional function minimization**

And Moses stretched out his hand over the sea, and the LORD drove the sea with a strong east wind all night, and made the sea dry land, and the waters parted. And the children of Israel went in the midst of the sea on dry land: the waters were a wall to them on the right and on the left side. 

The waters parted with Moses hand, and for his companions from the shore, the waves was described by the following function:

$y= x^2e^{sin(x)}$

So function $y=x^2e^{sinx}$ is our $optimality \ criteria$.

```py
def func(x):
    return x ** 2 * np.e ** np.sin(x)
```

![](https://github.com/Lopa10ko/ITMO-appliedmath-2023/blob/main/labs/lab1/data/001_raw_function.png)

### **Dichotomic minimization**

```py
eps = 10 ** -6
delta = eps / 2
left, right = -8, 9
```

```py
def minimize_dichotomic(function, left, right, eps):
    steps = 0
    calls = 0
    history = []
    while abs(left - right) > eps:
        calls += 2
        center = (left + right) / 2
        steps += 1
        history.append([left, right, center])
        if function(center + eps) < function(center - eps):
            left, right = center, right
        elif function(center + eps) > function(center - eps):
            left, right = left, center
        else:
            left, right = center + eps, center - eps
    return (left + right) / 2, steps, history, calls
```

```
Dichotomic minimization completed within 24 steps
Computed optimal value: 4.218436777591705
Optimal function value: 7.377709994478169
Total function calls: 48
```

![](https://github.com/Lopa10ko/ITMO-appliedmath-2023/blob/main/labs/lab1/data/002_dichotomic_test.png)

![](https://github.com/Lopa10ko/ITMO-appliedmath-2023/blob/main/labs/lab1/data/003_dichotomic_iterative_test.png)

### **The golden ratio minimization**

```py
def minimize_goldenratio(function, left, right, eps):
    steps, calls, history = 0, 0, []
    alpha = (3 - math.sqrt(5)) / 2
    subleft = left + (right - left) * alpha
    subright = right - (right - left) * alpha
    value_left, value_right = function(subleft), function(subright)
    calls += 2
    while abs(left - right) > eps:
        steps += 1
        history.append([left, right, subleft, subright])
        if value_left >= value_right:
            left = subleft
            subleft = subright
            subright = right - (right - left) * alpha
            value_left, value_right = value_right, function(subright)
            calls += 1
        else:
            right = subright
            subright = subleft
            subleft = left + (right - left) * alpha
            value_left, value_right = function(subleft), value_left
            calls += 1

    return (left + right) / 2, steps, history, calls
```

```
Golden Ratio minimization completed within 35 steps
Computed optimal value: -1.4237677837006615e-07
Optimal function value: 2.0271144132898533e-14
Total function calls: 37
```

![](https://github.com/Lopa10ko/ITMO-appliedmath-2023/blob/main/labs/lab1/data/004_goldenratio_test.png)

### **Fibonacci minimization**

```py
def get_fibonacci(n):
    f1, f2 = 0, 1
    for _ in range(n):
        f1, f2 = f2, f1 + f2
    return f1

def get_closest_fibonacci_num(N):
    f1, f2, cnt = 0, 1, 0
    while f1 < N:
        cnt += 1
        f1, f2 = f2, f1 + f2
    return cnt
```

```py
def minimize_fibonacci(function, left, right, eps):
    steps, calls, history = 0, 0, []
    start_index = get_closest_fibonacci_num(abs(left - right) / eps)
    start_fib, prepre_fib = get_fibonacci(start_index), get_fibonacci(start_index - 2)
    alpha = prepre_fib / start_fib
    subleft = left + (right - left) * alpha
    subright = right - (right - left) * alpha
    value_left, value_right = function(subleft), function(subright)
    calls += 2
    while steps != start_index - 2:
        steps += 1
        history.append([left, right, subleft, subright])
        if value_left >= value_right:
            left = subleft
            subleft = subright
            subright = right - (right - left) * get_fibonacci(start_index - steps - 2) / get_fibonacci(start_index - steps)
            value_left, value_right = value_right, function(subright)
            calls += 1
        else:
            right = subright
            subright = subleft
            subleft = left + (right - left) * get_fibonacci(start_index - steps - 2) / get_fibonacci(start_index - steps)
            value_left, value_right = function(subleft), value_left
            calls += 1
    return (left + right) / 2, steps, history, calls
```

```
Fibonacci minimization completed within 35 steps
Computed optimal value: -6.830087336719668e-07
Optimal function value: 4.665006116480829e-13
Total function calls: 37
```

![](https://github.com/Lopa10ko/ITMO-appliedmath-2023/blob/main/labs/lab1/data/005_fibonacci_test.png)

### **Parabolic minimization**

```math
\left\{\begin{matrix} ax_1^2+bx_1+c=f(x_1) \\ ax_2^2+bx_2+c=f(x_2)\\ax_3^2+bx_3+c=f(x_3) \end{matrix}\right. 
```

```math
\left\{\begin{matrix} a(x_1^2-x_2^2)+b(x_1-x_2)=f(x_1)-f(x_2) \\ a(x_2^2-x_3^2)+b(x_2-x_3)=f(x_2)-f(x_3) \end{matrix}\right.
```

```math
\begin{pmatrix}x_1^2-x_2^2 & x_1-x_2 \\ x_2^2-x_3^2 & x_2-x_3 \\ \end{pmatrix} \cdot \begin{pmatrix} a \\ b \\ \end{pmatrix} = \begin{pmatrix} f(x_1)-f(x_2)  \\f(x_2)-f(x_3)\\ \end{pmatrix}
```

```math
\begin{pmatrix} a \\ b \\ \end{pmatrix} = \begin{pmatrix}x_1^2-x_2^2 & x_1-x_2 \\ x_2^2-x_3^2 & x_2-x_3 \\ \end{pmatrix}^{-1} \cdot \begin{pmatrix} f(x_1)-f(x_2)  \\ f(x_2)-f(x_3)\\ \end{pmatrix} = \begin{pmatrix}x_2-x_3 & x_2-x_1 \\ x_3^2-x_2^2 & x_1^2-x_2^2 \\ \end{pmatrix} \cdot \begin{pmatrix} f(x_1)-f(x_2)  \\ f(x_2)-f(x_3)\\ \end{pmatrix}
```

```math
\begin{pmatrix} a \\ b \\ \end{pmatrix} = \begin{pmatrix} (f(x_1)-f(x_2))(x_2-x_3) + (f(x_2)-f(x_3))(x_2-x_1) \\ (f(x_1)-f(x_2))(x_3^2-x_2^2) + (f(x_2)-f(x_3))(x_1^2-x_2^2)\end{pmatrix}
```
```math
x_{min}=\frac{-b}{2a} = - \frac{1}{2} \cdot \frac{(f(x_1)-f(x_2))(x_3^2-x_2^2) + (f(x_2)-f(x_3))(x_1^2-x_2^2)}{(f(x_1)-f(x_2))(x_2-x_3) + (f(x_2)-f(x_3))(x_2-x_1)}
```

```py
def get_xmin(x1, f1, x2, f2, x3, f3):
    f12, f23 = f1 - f2, f2 - f3
    x21, x23 = x2 - x1, x2 - x3
    xsq32, xsq12 = x3 ** 2 - x2 ** 2, x1 ** 2 - x2 ** 2
    return -0.5 * (f12 * xsq32 + f23 * xsq12) / (f12 * x23 + f23 * x21)

def minimize_parabolic(function, left, right, eps):
    steps, calls, history = 0, 0, []
    center = (left + right) / 2
    value_left, value_center, value_right = function(left), function(center), function(right)
    calls += 3
    while value_left < value_center or value_right < value_center:
        if value_center > value_left:
            right = center
            value_right = value_center
            center = (left + right) / 2
            value_center = function(center)
            calls += 1
        elif value_center > value_right:
            left = center
            value_left = value_center
            center = (left + right) / 2
            value_center = function(center)
            calls += 1
        else:
            break
    # x_min in [left, right]
    # f(center) < f(left), f(right)
    x_min = get_xmin(left, value_left, center, value_center, right, value_right)
    while True:
        steps += 1
        history.append([left, right, center, x_min])
        value_x_min = function(x_min)
        calls += 1
        if left < x_min < center and value_x_min > value_center:
            left, value_left = x_min, value_x_min
        elif left < x_min < center and value_x_min < value_center:
            right, value_right = center, value_center
            center, value_center = x_min, value_x_min
        elif center < x_min < right and value_x_min > value_center:
            right, value_right = x_min, value_x_min
        elif center < x_min < right and value_x_min < value_center:
            left, value_left = center, value_center
            center, value_center = x_min, value_x_min
        x_min_new = get_xmin(left, value_left, center, value_center, right, value_right)
        if abs(x_min - x_min_new) <= eps:
            return x_min, steps, history, calls
        x_min = x_min_new
```

```
Parabolic minimization completed within 23 steps
Computed optimal value: -1.2301318779860001e-06
Optimal function value: 1.5132225757728896e-12
Total function calls: 26
```

![](https://github.com/Lopa10ko/ITMO-appliedmath-2023/blob/main/labs/lab1/data/006_parabolic_test.png)

### **Brent combined minimization**

```py
def minimize_brent(function, left, right, eps):
    def get_sign(a, b):
        return 1 if a >= b else -1

    def get_parabola_xmin(x1, f1, x2, f2, x3, f3):
        f12, f23 = f1 - f2, f2 - f3
        x21, x23 = x2 - x1, x2 - x3
        xsq32, xsq12 = x3 ** 2 - x2 ** 2, x1 ** 2 - x2 ** 2
        assert f12 * x23 + f23 * x21 != 0, "Computed float values not accurate enough\nTry changing convergence parameter (eps)"
        return -0.5 * (f12 * xsq32 + f23 * xsq12) / (f12 * x23 + f23 * x21)

    def check_inequality(a, b, c):
        return a != b and a != c and b != c

    steps, calls, history = 0, 0, []
    alpha = (3 - math.sqrt(5)) / 2
    k = 0.1
    center = (left + right) / 2
    value_center = function(center)
    calls += 1

    min1 = min2 = min3 = center
    value_min1 = value_min2 = value_min3 = value_center
    current_len = prev_len = math.fabs(left - right)
    while math.fabs(right - left) > eps and current_len > eps:
        steps += 1
        preprev_len, prev_len = prev_len, current_len
        x_min = None

        if x_min is not None and min(math.fabs(x_min - right), math.fabs(x_min - left)) <= eps * 10:
            print("Minimization interrupted!")
            break

        if check_inequality(min1, min2, min3) and check_inequality(value_min1, value_min2, value_min3):
            x_min = get_parabola_xmin(min1, value_min1, min2, value_min2, min3, value_min3)
        if x_min is not None and left + eps <= x_min <= right - eps and math.fabs(x_min - min1) < preprev_len / 2:
            current_len = math.fabs(x_min - min1)
        else:
            if min1 < (right - left) / 2:
                # golden ration [min1, right]
                current_len = right - min1
                x_min = min1 + current_len * alpha
            else:
                # golden ration [left, min1]
                current_len = min1 - left
                x_min = min1 - current_len * alpha
        history.append([left, right, min1, min2, min3, x_min])
        if x_min is not None:
            # if math.fabs(x_min - min1) < eps:
            #     x_min = min1 + get_sign(x_min, min1) * eps
            value_x_min = function(x_min)
            calls += 1
            if value_x_min <= value_min1:
                if x_min >= min1:
                    left = min1
                else:
                    right = min1
                min3, min2, min1 = min2, min1, x_min
                value_min3, value_min2, value_min1 = value_min2, value_min1, value_x_min                
            else:
                if x_min >= min1:
                    right = x_min
                else:
                    left = x_min
                if value_x_min <= value_min2 or min2 == min1:
                    min3, min2 = min2, x_min
                    value_min3, value_min2 = value_min2, value_x_min
                elif value_x_min <= value_min3 or min3 == min2 or min3 == min1:
                    min3 = x_min
                    value_min3 = value_x_min
    return x_min, steps, history, calls
```

```
Brent minimization completed within 10 steps
Computed optimal value: 5.5667878187159446e-09
Optimal function value: 3.098912679111412e-17
Total function calls: 11
```

![](https://github.com/Lopa10ko/ITMO-appliedmath-2023/blob/main/labs/lab1/data/007_brent_test.png)

# **Benchmark of minimization methods:**

```py
optima_dichotomic, steps_dichotomic, history_dichotomic, calls_dichotomic = minimize_dichotomic(func, left, right, eps)
optima_goldenratio, steps_goldenratio, history_goldenratio, calls_goldenratio = minimize_goldenratio(func, left, right, eps)
optima_fib, steps_fib, history_fib, calls_fib = minimize_fibonacci(func, left, right, eps)
optima_parabolic, steps_parabolic, history_parabolic, calls_parabolic = minimize_parabolic(func, left, right, eps)
optima_brent, steps_brent, history_brent, calls_brent = minimize_brent(func, left, right, eps)
```

![](https://github.com/Lopa10ko/ITMO-appliedmath-2023/blob/main/labs/lab1/data/008_minimization_benchmark.png)






