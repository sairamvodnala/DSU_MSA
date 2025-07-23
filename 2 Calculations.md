Calculations
========================================================
autosize: true
font-family: 'Garamond'
date: June 5 2023

## Statistical Programming

<style type="text/css">
  body{ /* Normal  */
  font-size: 24pt;
  font-family: Garamond;
}

th, td { 
      font-size: 16px;
      border-collapse: collapse;
      border-width:3px
    }
    
h1.title {
  font-size: 36pt;
  color: DarkRed;
  font-family: Arial;
}
h1 { /* Header 1 */
  font-size: 32pt;
  color: DarkBlue;
  font-family: Arial;
}
h2 { /* Header 2 */
    font-size: 24pt;
  color: DarkBlue;
}
h3 { /* Header 3 */
  font-size: 24pt;
  font-family: "Times New Roman", Times, serif;
  color: DarkBlue;
}
code.r{ /* Code block */
    font-size: 12px;
}
pre { /* Code block - determines code spacing between lines */
    font-size: 14px;
}
.tiny-code pre code {
  font-size: 1.02em;
}
.small-code pre code {
  font-size: 1.2em;
}
.medium-code pre code {
  font-size: 1.4em;
}
</style>

Overview
========================================================
Consider reported values for total calories per recipe from Wansink Table 1. This table shows an F value of 402.3, with an associated p-value of $< 0.0001$. The F ratio, however, only tests that there is a significant difference between at least two pairs treatment means. It does not tell us which means are different from each other.

Overview
========================================================

Remember the common statement of the null hypothesis for the F ratio. Let $\mu_{1936}$ be the mean calories per recipe for the 1936 edition, and let$\mu_{1946}$ be the mean calories per recipe for 1946, etc, Then our null hypothesis can be stated as 

$$
H_0:\mu_{1936} =\mu_{1946}=\dots =\mu_{2006}
$$

Overview
========================================================

Do we need an F-test to interpret these results? Consider the plot from `1 Geting Started R.` Total calories is relatively unchanged from 1936 to 1997. Thus, the only pair that we might find significant is between 1936 and 2006. Any F-test among all treatment means will be dominated by the difference between 1936 and 2006. Thus, we can test a simpler hypothesis:

$$
H_0:\mu_{1936} =\mu_{2006}
$$
or, alternatively

$$
H_0:\mu_{1936} - \mu_{2006} = 0
$$

Overview
========================================================

This hypothesis can be tested using a couple simple test statistics. For sufficiently large $n$, we can use something like Wald's tests, where we write

$$
W = \frac{\widehat{\mu_i}-\widehat{\mu_j}}{\widehat{se}}
$$

where 

$$
\widehat{se}=\sqrt{\frac{\widehat{\sigma}^2_{i}}{n} + \frac{\widehat{\sigma}^2_{j}}{n}}
$$

Overview in R
========================================================
class: small-code 

If assume calories per recipe are normally distributed ($\sim \mathcal{N}(0,1)$), we can go ahead and compute a p-value using the standard normal distribution. The first line calculates the Wald statistic, the second duplicates the Wald statistic, then calculates a p-value from the normal distribution.


``` r
(3051.9-2123.8)/sqrt(((1050.0^2)/18)+((1496.2^2)/18))
```

```
[1] 2.154197
```

``` r
2*(1-pnorm(abs((3051.9-2123.8)/sqrt(((1050.0^2)/18)+((1496.2^2)/18)))))
```

```
[1] 0.03122475
```
(Note - we want a two-tailed hypothesis test, so we compute $2\mathbb{P}(|W|>Z)=2(1-\mathbb{P}(Z<|W|))$)

Overview in Python
========================================================
class: small-code 

Using Python, we need to import both the `math` and the `scipy.stats` libraries. Otherwise, the mathematical form is similar to R, with the exception of the exponentiation operator.


``` python
import math
import scipy.stats
(3051.9-2123.8)/math.sqrt(((1050.0**2)/18)+((1496.2**2)/18))
```

```
2.154196666658047
```

``` python
2*(1-scipy.stats.norm.cdf(abs((3051.9-2123.8)/math.sqrt(((1050.0**2)/18)+((1496.2**2)/18)))))
```

```
0.03122475216914955
```

Overview in Python
========================================================

In addition to `sqrt` function, we see a difference in the cumulative density function for the normal distribution (`pnorm` in R), In Python, this is a property of the `norm` class, which is a member of `scipy.stats`; we access this property by calling the `cdf` function of the `norm` class.

Overview in Python
========================================================
class: small-code 

It's common convention to abbreviate commonly used libraries (e.g. `np` for `numpy` and `plt` for `matplotlib`). I tend to defy convention here and use the fully qualified name. But you are free to use standard abbreviations in your code, for example


``` python
import math
import scipy.stats as sp
(3051.9-2123.8)/math.sqrt(((1050.0**2)/18)+((1496.2**2)/18))
```

```
2.154196666658047
```

``` python
2*(1-sp.norm.cdf(abs((3051.9-2123.8)/math.sqrt(((1050.0**2)/18)+((1496.2**2)/18)))))
```

```
0.03122475216914955
```


Overview in Python
========================================================

We can also save ourselves some typing using `import` statements of the form
```
from scipy import stats
...
2*(1-stats.norm.cdf(abs((3051.9-2123.8)/math.sqrt(((1050.0**2)/18)+((1496.2**2)/18)))))
```
or

```
from scipy.stats import norm
...
2*(1-norm.cdf(abs((3051.9-2123.8)/math.sqrt(((1050.0**2)/18)+((1496.2**2)/18)))))
```

Overview in Julia
========================================================
class: small-code 
The initial steps in calculations in Julia a very similar to R and Python


``` julia
using Distributions
(3051.9-2123.8)/sqrt(((1050.0^2)/18)+((1496.2^2)/18))
```

```
2.154196666658047
```

``` julia
2*(1-cdf(Normal(0,1), (abs((3051.9-2123.8)/sqrt(((1050.0^2)/18)+((1496.2^2)/18))))))
```

```
0.03122475216914955
```
Note that, as with Python, statistical distribution functions have to be imported. 

Overview in Julia
========================================================

We can also see here key difference in the languages with regard to statistical functions. R has simple function for quantiles, distributions, etc. while in Python, the `cdf` is a method of the `normal` class. 

In Julia we have `cdf`, which is generic function that is dispatched to a class specific function based on the type of the first argument, in this case an instance of the `Normal` class with mean 0 and standard deviation of 1.

Overview in Julia
========================================================

We should also note how we `print` values; in R, this is commonly done automatically for each value in RMarkdown, while in Python we commonly call `print` explicitly. Julia is a mix. We will frequently have values printed from a function unless we suppress printing by appending `;` at the end of each statement. Julia also has two `print` functions. The ordinary `print` will print results in a single line; if we want each result on its own line we use `println`.

Overview
========================================================
class: small-code 

## t-test

We can use the same calculations for a Student's $t$ test, and write

$$
t = \frac{\widehat{\mu_i}-\widehat{\mu_j}}{\widehat{se}}
$$

Note - for simplicity we are assuming homogeneity of variances; non-homogeneous variances require a Satterthwaite correction for degrees of freedom.

Overview in R
========================================================
But in this case, we make a different assumption about the distribution of possible $t$ values, and use Student's $t$ distribution to calculate a $p$-value. 


``` r
2*(1-pt(abs((3051.9-2123.8)/sqrt(((1050.0^2)/18)+((1496.2^2)/18))),
        (2*(18-1))))
```

```
[1] 0.03839995
```

Overview in Python and Julia
========================================================
class: small-code 
In Python, this is

``` python
print(2*(1-scipy.stats.t.cdf(abs(3051.9-2123.8)/math.sqrt(((1050.0**2)/18)+((1496.2**2)/18)), 
                             (2*(18-1)))))
```

```
0.03839995456203238
```

and in Julia, we write


``` julia
println(2*(1-cdf(TDist((2*(18-1))), (abs((3051.9-2123.8)/sqrt(((1050.0^2)/18)+((1496.2^2)/18)))))))
```

```
0.03839995456203238
```

We see that the t-distribution requires a parameter, degrees of freedom, which for these data is `2*(18-1)`

Overview in R
========================================================
class: small-code 

While it's convenient to simply type in values for complex formula, this can also be error prone, particularly, if we want to reuse values. We commonly store values in variables. This can also simplify our formula, particularly if we break complex calculations into smaller pieces, storing the intermediate steps in variables.


``` r
m_2006 <- 3051.9
m_1936 <- 2123.8
s_1936 <- 1050.0
s_2006 <- 1496.2
n_1936 <- n_2006 <- 18
```

Overview in R
========================================================
class: small-code 


``` r
hat.se <- sqrt(((s_1936^2)/n_1936)+((s_2006^2)/n_2006))
W <- (m_2006-m_1936)/hat.se
t <- (m_2006-m_1936)/hat.se
2*(1-pnorm(abs(W)))
```

```
[1] 0.03122475
```

``` r
2*(1-pt(abs(t),(n_1936-1 + n_2006-1)))
```

```
[1] 0.03839995
```
 R I commonly use the underscore operator for variables that might be denoted mathematically as subscripts in $LaTeX$ notation (i.e. $m_{2006}$), while I use the `.` character to separate words. It's not strictly required by the language, it more a personal preference.

Overview in Python and Julia
========================================================
class: small-code 
Python and Julia use the `=` operator to assign values to variable names. The `=` operator can also be used in R, but I generally prefer to use `<-` operator for assigning values to variables,  Additionally, the `.` operator has special meaning in both Python and Julia (typically, to denote namespace membership), but has no special meaning in R, other than to denote decimal numbers.

Thus, in Python, we define variables as


``` python
m_2006 = 3051.9
m_1936 = 2123.8
s_1936 = 1050.0
s_2006 = 1496.2
n_1936 = n_2006 = 18
```

Overview in Python
========================================================
class: small-code 
and execute calculations as 

``` python
hat_se  = math.sqrt(((s_1936**2)/n_1936)+((s_2006**2)/n_2006))
W = (m_2006-m_1936)/hat_se
t = (m_2006-m_1936)/hat_se
2*(1-scipy.stats.norm.cdf(abs(W)))
```

```
0.03122475216914955
```

``` python
2*(1-scipy.stats.t.cdf(abs(t),(n_1936-1 + n_2006-1)))
```

```
0.03839995456203238
```

Overview in Julia
========================================================
class: small-code 

``` julia
m_2006 = 3051.9;
m_1936 = 2123.8;
s_1936 = 1050.0;
s_2006 = 1496.2;
n_1936 = n_2006 = 18;
```

Overview and Julia
========================================================
class: small-code 

Julia will frequently echo operations to standard output (the screen or code chunks). We can suppress this by adding the `;` character.


``` julia
hat_se  = sqrt(((s_1936^2)/n_1936)+((s_2006^2)/n_2006));
W = (m_2006-m_1936)/hat_se;
t = (m_2006-m_1936)/hat_se;
2*(1-cdf(Normal(), (abs(W))))
```

```
0.03122475216914955
```

``` julia
2*(1-cdf(TDist(n_1936-1 + n_2006-1),abs(t)))
```

```
0.03839995456203238
```

Overview
========================================================

While this test does suggest a significant difference between calories per recipe from 1936 to 2006, this larger p-value than reported by Wansink.

Discussion Question: Why might our simple Wald's hypothesis test be less statistically significant than the F-test reported by Wansink?

Concepts
========================================================

- Statements
- Variables and Constants
- Variable declaration
- Variable assignment
- Built-in functions
- Mathematical operators
- Relational operators

Statements in R
========================================================

Statements are the fundamental executable units of a language.

The statements we've seen in R are lines of text, terminated by a newline/carriage return (invisible) character. This character is typed by pressing the enter key and initiates the Evaluate step of the REPL. Multiple executable statements may be combined in a single line using a semicolon:


``` r
s_1936 <- 1050.0; s_2006 <- 1496.2
```

Statements in Python and Julia
========================================================

Statements are similar in Python and Julia, and the `;` operator allows multiple statements to appear on a single line. 

### Python

``` python
s_1936 = 1050.0; s_2006 = 1496.2
```

The `;` has a different additional meanings in Julia; while it can be used to separate statements, it also suppresses output of assignments. It's also used in function and array definitions.

### Julia

``` julia
s_1936 = 1050.0; s_2006 = 1496.2
```

```
1496.2
```


Command line calculations in R
========================================================

All three languages allow statements to be entered and interpreted in the command line (REPL); so we can just use the command line as a calculator:


``` r
2*(1-pnorm(abs((3051.9-2123.8)/sqrt(((1050.0^2)/18)+((1496.2^2)/18)))))
```

```
[1] 0.03122475
```

Variables and Constants
========================================================
We will typically use variables to store values, to be referenced later. 

In statically-typed, compiled languages like C/C++/FORTRAN, etc. variables are identifiers associated with specific locations in computer memory and will almost always have a type associated with them.

Variables and Constants
========================================================
In dynamically-typed languages, like R, Python or Julia, a variable is associated most commonly with an object, and the object itself carries with it a type. Each of these languages supports many different kinds of types. 

In Python, the common class type is `object`, in Julia this is `Any`. In R, we don't see a single common type (unless we consider S-expressions as a type), and there are competing object systems in R. You will mostly see S3-type objects in this course.

Variables and Constants in R
========================================================

In R, the statement

``` r
s_1936 <- 1050.0
```
assigns a numeric constant value to the variable named `s_1936`. We slightly different assignment statements in Python and Julia.

Variables and Constants in R
========================================================
The constant `1050.0` always evaluates to itself:

``` r
1050.0
```

```
[1] 1050
```
while a variable evaluates to its most recently assigned value


``` r
s_1936
```

```
[1] 1050
```

Variables and Constants in R
========================================================
... unless it hasn't been assigned.

```
s_k
> s_k
Error: object 's_k' not found
```

Variables in REPL
========================================================
In many cases, REPL languages will automatically print the results of the statement. However, this will not always be true, so we sometimes need to call `print` explicitly. 

The behavior of `print` differs among the languages, particularly Julia. In R and Python, `print` automatically adds a new line to output, while `print` in Julia does not. If we don't want our output to run together in a single line, in Julia we use the `println` command.

Printing Variables in Python and Julia
========================================================

Note that in R, the assignment operator (`<-`) is a function that returns a value, so we can write something like

``` r
print(s_1936 <- 1050.0)
```

```
[1] 1050
```

will print a result. 

Printing Variables in Python and Julia
========================================================
However, this is not true in Python or Julia
#### Python
```
>>> print(s_1936 = 1050.0)
TypeError: 's_1936' is an invalid keyword argument for print()
```

#### Julia
```
julia> print(s_1936 = 1050.0)
ERROR: MethodError: no method matching print(; s_1936=1050.0)
```
  
Printing Variables in Python and Julia
========================================================

`=` can also be used for assignments in R, but won't return a value that can be used by the `print` function.
```
> print(s_1936 = 1050.0)
Error in print.default(s_1936 = 1050) : 
  argument "x" is missing, with no default
```

Value Types
========================================================

Most of the values we use in statistical programming are

- Numeric
- Text
- Boolean
- Null

Types 
========================================================
To illustrate types, we can examine the types of elements by calling *type* functions; this will be `typeof` in R and Julia, and `type` in Python.

Types in R
========================================================

``` r
typeof(2)
```

```
[1] "double"
```

``` r
typeof(2.0)
```

```
[1] "double"
```

<small>Numbers default to decimal or floating point values in R, but we can force to integer using `L`. We will rarely use integer values explicitly in R.</small>


``` r
typeof(2L)
```

```
[1] "integer"
```


Types in Python
========================================================


``` python
type(2)
```

```
<class 'int'>
```

``` python
type(2.0)
```

```
<class 'float'>
```
Python does make a distinction between integer and decimal numbers. This, we will occasionally need to be aware of.

Types in Julia
========================================================

``` julia
typeof(2)
```

```
Int64
```

``` julia
typeof(2.0)
```

```
Float64
```

Julia has an expansive type system. This is partly due to the fact that Julia is just-in-time compiled - the type system helps make the compiled code more efficient.

Text data.
========================================================
We'll cover working with text data in a later section, but here we'll just note that all three languages support text data. 

R text
========================================================
In many languages, text as a string of characters is delimited by double quotes (`"`), as opposed to single characters, which are usually delimited with single quotes (i.e. `'a'`). However, R does not have a special type for characters, so `"` and `'` can be used interchangeably.


``` r
typeof("some text")
```

```
[1] "character"
```

``` r
typeof('a')
```

```
[1] "character"
```

Python text.
========================================================
Similar to R, Python allows `"` and `'` to be used interchangeably.


``` python
type("some text")
```

```
<class 'str'>
```

``` python
type('a')
```

```
<class 'str'>
```

Julia text.
========================================================

Julia does make distinctions between strings of characters and single characters.

``` julia
typeof("some text")
```

```
String
```

``` julia
typeof('a')
```

```
Char
```

Boolean
========================================================
Many languages represent boolean (true/false) values as distinct types. However, in our languages, we see different spellings of the values. Boolean types can frequently be converted to integer values, and we can do arithmetic on true/false values. This is sometimes useful in determining the number of, say, data rows, meet a certain condition.

Boolean Values in R
========================================================

``` r
typeof(FALSE)
```

```
[1] "logical"
```

``` r
FALSE + TRUE
```

```
[1] 1
```

Boolean in Python
========================================================

``` python
type(False)
```

```
<class 'bool'>
```

``` python
False + True
```

```
1
```

Boolean in Julia
========================================================

``` julia
typeof(false)
```

```
Bool
```

``` julia
false + true
```

```
1
```

Null values in R
========================================================
Sometimes, a data set will be incomplete - some elements of the data will be missing. 
Languages for data analysis will typically represent missing values with a special data type,
which consists of only one value. 

In R, this is `NA`, and we can check for this using the built-in function 

``` r
tmp <- NA
is.na(tmp)
```

```
[1] TRUE
```

Null values in Python
========================================================
In Python, there is the special object `None`. We don't test for this using a function, but we can use the syntax 

``` python
tmp = None
print(tmp is None)
```

```
True
```

Note that `is` is an identity operator, which is sometimes different that the equality operator `==`.

Null values in Julia
========================================================
Julia defines the constant `nothing` to represent a null value. Julia also defines the type 
`Union{}`, which is the exact opposite of `Any` - every object in Julia can be of type `Any`; 
nothing can be of type `Union{}`, so sometimes `Union{}` is used in cases where an empty or missing value
is needed.

Special Types and Constants
========================================================
class: small-code
Certain common values, like $\pi$, $\epsilon$ or the largest possible numeric value (in computer terms), are available as named constants:

``` r
pi
```

```
[1] 3.141593
```

``` r
.Machine$double.eps
```

```
[1] 2.220446e-16
```

``` r
.Machine$double.xmax
```

```
[1] 1.797693e+308
```

($\epsilon$ is the smallest possible values, such that $1+\epsilon > 1$)

Special Types and Constants in R
========================================================

$\infty$ may also be a special constant, as in R:


``` r
1/0
```

```
[1] Inf
```

Special Types and Constants in Julia
========================================================

We get similar results in Julia


``` julia
1/0
```

```
Inf
```

Special Types and Constants in Python
========================================================

while Python throws an exception

``` python
1/0
```

```
ZeroDivisionError: division by zero
```

Special Types and Constants in R
========================================================

R also supports the constant 'Not a Number':

``` r
sqrt(-1)
```

```
[1] NaN
```

Special Types and Constants in Julia
========================================================

Julia considers this an error


``` julia
sqrt(-1)
```

```
sqrt(-1)
ERROR: DomainError with -1.0:
sqrt will only return a complex result if called with a complex argument. Try sqrt(Complex(x)).
```

Special Types and Constants in Python
========================================================

Python similarly throws an error


``` python
math.sqrt(-1)
```

```
ValueError: math domain error
```

Factors or Class Values
========================================================

R allows for discrete or categorical data (as opposed to continuous numeric data). We'll just note this now; we won't do much with discrete data until we start working with data tables. Python and Julia have categorical types, but they are not part of the base language, these types are supported by different library types (`pandas` in Python, and `DataFrames` in Julia)

Mathematical Operators
========================================================
Mathematical operators are like functions, in that they accept one or two
numeric values as parameters and return numeric values. 

What differentiates them from functions is that binary operators tend to use **infix**
notation - the operator is between the two parameters. They also don't
require parenthesis to denote a parameter list.

Mathematical Operators
========================================================

Consider, in contrast, LISP, where all functions, even operators, are **prefix** notation. The minus operator returns a negation of the input when there is a single value, and sequentially subtracts the remainder of the input from the first value, if there are more than one parameter.

```
(- 3)
(- 3 2)
(- 5 2 1)
```

Mathematical Operators
========================================================

You might used **postfix** notation if you've used a Hewlett-Packard RPN
calculator, that is,

```
3 2 +
```

Some mathematical notation is postfix. For example, matrix transpose is represented as $\mathbf{X}^t$, but would be entered in R as `t(X)`


R Mathematical Operators 
========================================================

Why is this relevant? In R, it is possible to define new operators, and it helps to understand that operators are functions. We can, for example, invoke the addition function by

``` r
'+'(1,2)
```

```
[1] 3
```
There are some popular libraries, particularly in the 'tidyverse' that provide a large number of operators.

Python Mathematical Operators 
========================================================
Python also allows programmers to override mathematical operators, though in this case, the mathematical operators are commonly implemented in terms of member functions in defined classes. That is, the `+` operator is implemented as the `__add__` 'magic' function.

We won't be writing classes for this course, so we won't go into much detail into Python's class system. In general, the standard mathematical operators will work just fine for us.


``` python
n1=1; n2=2
n1.__add__(n2)
```

```
3
```

Julia Mathematical Operators 
========================================================

Julia also allows us to override operators; as with R these can be defined as functions. Again, we won't need to override mathematical operators for this course, but we can see how they work:


``` julia
(+)(1,2)
```

```
3
```


Common mathematical operators
========================================================

Operation | R | Python | Julia
----------|---|--------|-------
$x + y$   | `x+y` | `x+y` | `x+y`
$x - y$   | `x-y` | `x-y` | `x-y`
$x \times y$ | `x*y` | `x*y` | `x*y`
$x \div y$ | `x/y` | `x/y` | `x/y`
$x^y$ | `x^y` | `x**y` | `x^y`
$x \backslash y$ | `x%/%y` | `x//y` | `x÷y` `(x \div <tab> y)`
$x \text{ mod } y$ | `x%%y`  | `x%y` | `x%y`
negation | `-x` | `-x`| `-x`
grouping | `()` | `()`| `()`

Note there some differences, particularly with exponentiation and integer division.



Order of Operations
========================================================
For the most part, mathematical operators follow the standard PEDMAS order of operations:

- parenthesis
- exponentiation 
- multiplication or division
- addition or subtraction
- operations of equal precedence performed left to right

If you're not sure of the order, enclose the operation in parenthesis. It's frequently good practice to use parenthesis liberally when writing computations; it prevents order of operation error.

Order of Operations
========================================================

For example, in the $t$-test, we write

```
(m_2006-m_1936)/hat_se
```

because 
```
m_2006-m_1936/hat_se
```
only applies division to the last term This type of error is a common source of bugs.

Variables
========================================================
We use variable names store and access values in computer memory.
There are typically 3 phases in working with variables.

- Declaration
- Assignment
- Access

Variable declaration
========================================================
User declared variables are associated with locations for storing data. In general, variable names cannot be reserved words - that is, those words that are defined as part of the language.

Rules for declaring identifiers include

Variable declaration in R
========================================================
- can be a mix of letters, digits, underscore `_` or
period `.`
- must start with letter or period. If starting with a period, the
second character cannot be a digit.

Variable declaration in Python
========================================================
- must start with a letter or the underscore character.
- cannot start with a number.
- can only contain alpha-numeric characters and underscores (A-z, 0-9, and _ )
- are case-sensitive (age, Age and AGE are three different variables)

Variable declaration in Julia
========================================================
- must start with an underscore, a letter(A-Z or a-z) or a Unicode character greater than `U+00A0`(NBSP - no break space).
- can also contain digits(0-9) or !, but must not begin with these.
- operators like (+, ^, etc.) can also be used to name a variable.
- can also be written as words separated by underscore, but that is not a good practice and must be avoided unless necessary.

Variable assignment
========================================================
Variable assignment occurs when a variable is assigned a value by the user. In the examples we've seen, variable assignment has followed directly from variable declaration. 

Our languages are considered late binding language; this means that variable types aren't determined until they are evaluated. This is in contrast to static typed languages, where the type variable must be known at compile time. In a statically typed language, variables are frequently be declared (with an associated type) before they are assigned.

Variable assignment
========================================================

In late binding languages, variables are usually assigned when they are declared. When I want to have a variable name available in my code, but won't know the value (or even type) until later in code execution, I'll frequently use code of the form
```
to_be_determined <- NULL
```

Variable assignment in R
========================================================

For example, a common paradigm I use in analysis is
```
result <- NULL
...
result <- <some function call that may fail to produce a result>
if(!is.null(result)) {
   <process the result>
} else {
   <report the result could not be computed>
}
```


Assignment Operators
========================================================
Python and Julia have only one assignment operator : `=`

R has three different assignment operators : `=`, `<-`
and `<<-`, not counting the rightward assignment operators
`->` and `->>`. In most cases these are equivalent;
we'll cover the differences when we discuss **scoping** rules. These
all can be thought of special cases of the `assign` function,
with different **environments** as parameters.

Assignment Operators
========================================================

For now, it is worthwhile to note that in R, the assignment operator `<-` is also
a function that returns a value, so we can do things like


``` r
print(n_1936 <- (n_2006 <- 18))
```

```
[1] 18
```

instead of


``` r
n_2006 <- 18
n_1936 <- n_2006
print(n_1936)
```

```
[1] 18
```

Assignment Operators
========================================================
We can chain assignments using the `=` operator in all three languages. That is, the code
```
n_1936 = n_2006 = 18
```
will assign the value 18 to both `n_1936` and `n_2006` in all three languages.


Accessing variables
========================================================

Typically, all we need to do to access a variable is to invoke its name.

Mostly, we do this to use the value reference in the variable, to assign that variables value to another variable, or to examine the contents of the variable. In R, this looks like:


``` r
n_2006 <- 18
n_1936 <- n_2006 + 1
print(n_1936)
```

```
[1] 19
```

Library Functions
========================================================

We've made use of some functions provided by our languages that are not considered to be a part of the *base* language itself. Instead, these are sometimes functions written in the *base* language, and made available in *libraries* or *packages*.

When the R executable is first run, it loads some of the more common libraries, including `base` and `stats`. This gives us access to common mathematical and statistical functions in R without extra effort.

In contrast, for most of the mathematical or statistical functions we'll need in this course, we'll need to load specific libraries in Python or Julia.

Library Functions
========================================================
To load external code in Python, we use the `import` command

``` python
scipy.stats
```

```
<module 'scipy.stats' from 'C:\\Users\\sairam\\DOCUME~1\\VIRTUA~1\\R-RETI~1\\lib\\site-packages\\scipy\\stats\\__init__.py'>
```

 while Julia uses the syntax `using`

``` julia
using Distributions
```


Some mathematical functions
========================================================

Function | R | Python | Julia 
---------|---|--------|----------
$| x |$ | `abs(x)` | `abs(x)` | `abs(x)`
$\sqrt{x}$ | `sqrt(x)` | `math.sqrt(x)` | `sqrt(x)`
$\left[ x \right]$ | `round(x)`  | `round(x)` | `round(x)`
$\lfloor x \rfloor$ | `floor(x)` | `math.floor(x)` | `floor(x)`
$\lceil x \rceil$ | `ceiling(x)` | `math.ceil(x)` | `ceil(x)`
$e^x$ | `exp(x)` | `math.exp(x)` | `exp(x)`
$\sin$ | `sin(x)` | `math.sin(x)` | `sin(x)`
$\Gamma (x)$ | `gamma(x)` | `math.gamma(x)` | `SpecialFunctions gamma(x)`

Statistical Functions
========================================================

Function | R | Python | Julia 
---------|---|--------|----------
$Normal(0,1)$ pdf(x) | `dnorm(x,0,1)` | `scipy.stats.norm.pdf(x,0,2)` | `Distributions pdf(Normal(0,1),x)`
$Normal(0,1)$ cdf(x) | `pnorm(x,0,1)` | `scipy.stats.norm.cdf(x,0,1)` | `Distributions cdf(Normal(0,1),x)`
$Normal(0,1)$ quantile(x) | `qnorm(x,0,1)` | `scipy.stats.norm.ppf(x,0,1)` | `Distributions quantile(Normal(0,1),x)`
random(x) $Normal(0,1)$ | `dnorm(x,0,1)` | `scipy.stats.norm.rvs(x,0,1)` | `Distributions rand(Normal(0,1),x)`

Relational operators
========================================================
Relational operators are binary operators that accept two values and return
a Boolean (TRUE/FALSE) value. 

The values will typically be numeric, but relational operators may apply to any ordered type, such as text. Factors can be defined as ordered, as well.


Common relational operators
========================================================

Relation  |  R | Python | Julia 
----------|----|--------|----------
$x < y$ | `x < y` | `x < y` | `x < y`
$x > y$ | `x > y` | `x > y` | `x < y`
$x \leqslant y$ | `x <= y` | `x <= y` | `x <= y` or `x ≤ y` (`\le[tab]`)
$x \geqslant y$ | `x >= y` | `x >= y` | `x >= y` or `x ≥ y` (`\ge[tab]`)
$x = y$ | `x == y` | `x == y` | `x == y`
$x \neq y$ | `x != y` | `x != y`| `x != y` or `x ≠ y` (`\ne[tab]`)

Using Operators
========================================================

Operators should be thought of as taking two values (generally 
of the same type) and producing a single value. However, the produced
value may of a different type than the original values.

Using Operators
========================================================

Thus, we can chain some operators


``` r
1 + 2 + 3
```

```
[1] 6
```

but not others. This produces an error in R:


``` r
1 > 2 > 3
```

```
Error: unexpected '>' in "1 > 2 >"
```

Using Operators
========================================================

but works fine in Python and Julia

### Python

``` python
1 > 2 > 3
```

```
False
```

### Julia

``` julia
1 > 2 > 3
```

```
false
```

Using Operators
========================================================

We can make our intent more clear by grouping operations with parenthesis.

``` r
1 > (2 > 3)
```

```
[1] TRUE
```

``` r
(1 > 2) > 3
```

```
[1] FALSE
```

List comprehensions in Python and Julia
========================================================

In R, we don't really have scalar values - single values are actually arrays of length 1. This implies that R operators work pairwise on array arguments:

``` r
c(1,2,3) < 2
```

```
[1]  TRUE FALSE FALSE
```

``` r
c(1,2,3) < c(3,1,2)
```

```
[1]  TRUE FALSE FALSE
```

List comprehensions in Python and Julia
========================================================
This is, a operator (and this is true for many functions) can take two vectors of matching length (or two with lengths that are multiples, one of the other) and produce a vector of values of the same length as the longest argument.

List comprehensions in Python and Julia
========================================================
In Python and Julia, operators and other functions don't default to pairwise operations.

### Python

``` python
# error [1,2,3] < 2
[1,2,3] < [3,1,2]
```

```
True
```

### Julia

``` julia
# error [1,2,3] < 2
 [1,2,3] < [3,1,2]
```

```
true
```

List comprehensions in Python and Julia
========================================================
Instead, we rely, in Python and Julia, a syntax commonly referred to **list comprehension**. These generally involve syntax of the form

`newlist = [<expression> for <item> in <iterable>]`

List comprehension in Python
========================================================
Thus,


``` python
[x < 2 for x in [1,2,3]]
```

```
[True, False, False]
```

``` python
[x < y for x,y in zip([1,2,3],[3,1,2])]
```

```
[True, False, False]
```

List comprehension in Python
========================================================
Julia has a similar syntax, but different output:


``` julia
[x < 2 for x in [1,2,3]]
```

```
3-element Vector{Bool}:
 1
 0
 0
```

``` julia
[x < y for (x,y) in zip([1,2,3],[3,1,2])]
```

```
3-element Vector{Bool}:
 1
 0
 0
```

List comprehensions
========================================================
List comprehensions work for any valid `<expression>`, including function calls. I'm introducing the concept now, to highlight that in R most operations are implicitly vectorized, while Python and Julia most vector operations must be made explicit. This we'll cover in more detail in later lectures.

Broadcasting in Julia
========================================================
Julia also allows the use of the `.` operator to broadcast other operators or functions over lists, for example:

``` julia
2 .<[1,2,3]
```

```
3-element BitVector:
 0
 0
 1
```

We won't need broadcasting for the first couple sets of exercises, but it's a good thing to know about operators in Julia.

Discussion Questions
========================================================

- Why do 

``` r
1 > (2 > 3)
(1 > 2) > 3
```


give different values? 

- Where did we see $\infty$ in R? Is there an equivalent in Python or Julia?

========================================================


Give examples in R, Python or Julia, please, for the $t$ distribution:

- probability density function
- cumulative distribution function
- quantile function
- random number generator




