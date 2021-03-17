# R

View the full guide in [moodle](https://e-learning.hdm-stuttgart.de/moodle/pluginfile.php/222982/mod_resource/content/6/R_Basics.html).



## Operators

The standard Operators are available: `+, -, *, /, ^`.
Also, the standard comparative operators apply: `==, !=, <, >, <=, >=`.
The logic operators are: `&, |, !`, for `AND, OR, NOT`.

To declare variables, use the `<-` operator (altough you can also use `=`).

```r
a <- 6
b <- 7
```

To generate a sequence of numbers, use the `:` operator.



## Functions

Functions have an opening and a closing bracket `()`. Within these brackets are the parameters which are passed to the function.

```r
sqrt(1000)
round(sqrt(1000), digits=2)
```

Some often used functions are `sum(), min(), max(), mean(), sort()`.



## Vectors

Vectors contain values of the same data type.
They are initiated with `c()`

```r
odd_numbers = c(1, 3, 5, 7)
colors = c('blue', 'red', 'yellow')
```



## Packages

Packages expand the features of R. To install a package, use `install.packages('name')`.
To enable a package, use `library(package)`.



## Data Types

### Scalar Data Types

```r
integer = 5
numberic = 5.3
string = 'This is a string'
logical = TRUE
```

To get the type of a variable, use `class(variable)`.

Data objects can also be converted to diffetent data types:

```r
a = as.integer(24.3)
b = as.character(a)
```



### List

There are the data types `list, vector, factor, matrix, dataframe`.

A list can contain different data types and also different variables.

```r
a = list('hello', 2.5, 7)
a = list(b = 'hello', c = 2.5, d = 7)
```

You can determine the length of a list with `length(variable)`.



### Vector

A vector is a list, with the __same__ data types. It is initiated with `c()`.

```r
a = c(1, 5, 4, 9)
```



### Factor

A factor is a vector with non-numeric values. It is created by defining a vector, then assigning it to a factor.

```r
sex = factor(c('male', 'female', 'female', 'male'))
levels(sex)     # returns 'male' 'female'
nlevels(sex)    # returns 2
```



### Matrix

A matrix is a two-dimensional table containing of values.

```r
a = matrix(1:12, nrow=3, ncol=4)

# this will create:
      [,1] [,2] [,3] [,4]
[1,]    1    4    7   10
[2,]    2    5    8   11
[3,]    3    6    9   12
```



### DataFrame

A DataFrame is basically a table. It contains of multiple veectors.

```r
sex = c('male', 'female')
age = c(25, 24)
dataframe = data.frame(sex, age)

# this will create a table like this:
     sex age
1   male  25
2 female  24
```

To get the dimensions of a DataFrame, use `dim(df)`.
To get a basic overview over a dataframe, use `str(sf)`. This will return all column names and the first 5 values.
To get all columns, use `names(df)`.

To get specific values from a DataFrame, use `df[row, column]`. One of these values can also be empty, then the whole row or column gets returned.



## Manage Data Objects in R Studio

To list all current data objects, use `objects()`. To remove objects, use `rm()`.