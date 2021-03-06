---
title: "Making Packages in R"
teaching: 30
exercises: 0
questions:
- "How do I collect my code together so I can reuse it and share it?"
- "How do I make my own packages?"
objectives:
- "Describe the required structure of R packages."
- "Create the required structure of a minimal R package."
- "Write documentation comments that can be automatically compiled to R's native help and documentation format."

keypoints:
- "A package is the basic unit of reusability in R."
- "Every package must have a DESCRIPTION file and an R directory containing code."
---



Why should you make your own R packages?

**Reproducible research!**

An R package is the **basic unit of reusable code**.
If you want to reuse code later or want others to be able to use your code, you should put it in a package.

An R package requires two components:

*   a DESCRIPTION file with metadata about the package
*   an R directory with the code

*There are other optional components. Go [here][r-package-basics] for much more information.*

[r-package-basics]: http://adv-r.had.co.nz/Package-basics.html

### DESCRIPTION file

~~~
Package: Package name
Title: Brief package description
Description: Longer package description
Version: Version number(major.minor.patch)
Author: Name and email of package creator
Maintainer: Name and email of package maintainer (who to contact with issues)
License: Abbreviation for an open source license
~~~
{: .source}

The package name can only contain letters and numbers and has to start with a letter.

### .R files

Functions don't all have to be in one file or each in separate files.
How you organize them is up to you.
Suggestion: organize in a logical manner so that you know which file holds which functions.

### Making your first R package

Let's turn our temperature conversion functions into an R package.


~~~
fahrenheit_to_kelvin <- function(temp_F) {
    #Converts Fahrenheit to Kelvin
    temp_K <- ((temp_F - 32) * (5/9)) + 273.15
    temp_K
}
~~~
{: .r}


~~~
kelvin_to_celsius <- function(temp_K) {
  #Converts Kelvin to Celsius
  temp_C <- temp_K - 273.15
  temp_C
}
~~~
{: .r}


~~~
fahrenheit_to_celsius <- function(temp_F) {
  #Converts Fahrenheit to Celsius using fahrenheit_to_kelvin() and kelvin_to_celsius()
  temp_K <- fahrenheit_to_kelvin(temp_F)
	temp_C <- kelvin_to_celsius(temp_K)
  temp_C
}
~~~
{: .r}

We will use the `devtools` and `roxygen2` packages, which automate some of the steps in creating R packages.

~~~
install.packages(c("devtools", "roxygen2"))
library("devtools")
library("roxygen2")
~~~
{: .r}

Set your working directory, and then use the `create` function to start making your package.
Keep the name short and unique.
  - package_to_convert_temperatures_between_kelvin_fahrenheit_and_celsius (BAD)
  - tempConvert (GOOD)


~~~
setwd(parentDirectory)
create("tempConvert")
~~~
{: .r}

Add our functions to the R directory.
Place each function into a separate R script and add documentation like this:


~~~
#' Converts Fahrenheit to Kelvin
#'
#' This function converts input temperatures in Fahrenheit to Kelvin.
#' @param temp_F The temperature in Fahrenheit.
#' @return The temperature in Kelvin.
#' @export
#' @examples
#' fahrenheit_to_kelvin(32)

fahrenheit_to_kelvin <- function(temp_F) {
  temp_K <- ((temp_F - 32) * (5/9)) + 273.15
  temp_K
}
~~~
{: .r}

The `roxygen2` package reads lines that begin with `#'` as comments to create the documentation for your package.
Descriptive tags are preceded with the `@` symbol. For example, `@param` has information about the input parameters for the function. 

Note that if you don't put `@export` in the `roxygen2` comments, this function can only be used within the package itself. `@export` exports the function to the package NAMESPACE and makes it available when the package is loaded.

Now, we will use `roxygen2` to convert our documentation to the standard R format.


~~~
setwd("./tempConvert")
document()
~~~
{: .r}

Take a look at the package directory now.
The /man directory has a .Rd file for each .R file with properly formatted documentation.

Now, let's load the package and take a look at the documentation.


~~~
setwd("..")
install("tempConvert")

?fahrenheit_to_kelvin
~~~
{: .r}

Notice there is now a tempConvert environment that is the parent environment to the global environment.


~~~
search()
~~~
{: .r}

Now that our package is loaded, let's try out some of the functions.


~~~
fahrenheit_to_celsius(32)
~~~
{: .r}



~~~
[1] 0
~~~
{: .output}



~~~
fahrenheit_to_kelvin(212)
~~~
{: .r}



~~~
[1] 373.15
~~~
{: .output}



~~~
kelvin_to_celsius(273.15)
~~~
{: .r}



~~~
[1] 0
~~~
{: .output}

> ## Creating a Package for Distribution
>
> 1. Create some new functions for your tempConvert package to convert from Kelvin to Fahrenheit or from Celsius to Kelvin or Fahrenheit.
> 2. Create a package for our `analyze` function so that it can be reloaded or shared when more data arrives.
{: .challenge}
