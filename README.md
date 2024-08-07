
# Rapp <img src="man/figures/logo.png" align="right" height="138" alt="" />

<!-- badges: start -->

[![R-CMD-check](https://github.com/r-lib/Rapp/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/r-lib/Rapp/actions/workflows/R-CMD-check.yaml)
<!-- badges: end -->

Rapp (short for "R application") makes it fun to write and share command
line applications in R.

It is an alternative front end to R, a drop-in replacement for `Rscript`
that does automatic handling of command line arguments. It converts a
simple R script into a command line application with a rich and robust
support for command line arguments.

It aims to provides a seamless transition from interactive repl-driven
development at the R console to non-interactive execution at the command
line.

Here is a simple example Rapp:

``` r
#!/usr/bin/env Rapp
#| name: flip-coin
#| description: Flip a coin.

#| description: Number of coin flips
n <- 1L

cat(sample(c("heads", "tails"), n, TRUE), fill = TRUE)
```

Then you can invoke it from the command line:

``` bash
$ flip-coin
tails

$ flip-coin --n=3
tails heads tails

$ flip-coin --help
Flip a coin.

Usage: flip-coin [options]

Options:
  --n <value>  (Default: 1, Type: integer)
      Number of coin flips

$ flip-coin --help --yaml
name: flip-coin
description: Flip a coin.
options:
  'n':
    default: 1
    val_type: integer
    arg_type: option
    description: Number of coin flips
arguments: {}
```

Application options and arguments work like this:

### Options

Simple assignments of scalar literals at the top level of the R script
are automatically treated as command line *options*.

``` r
n <- 1
```

becomes an option at the command line:

``` bash
flip-coin --n 1
```

Option values passed from the command line are parsed as yaml/json, and
then coerced to the original R type. The following option value types
are supported: int, float, string, and bool. Values can be supplied
after the option, or as part of the option with `=`. The following two
usages are the same:

``` bash
flip-coin --n=1
flip-coin --n 1
```

Bool options, (that is, assignments of `TRUE` or `FALSE` in an R app)
are a little different. They support usage as switches at the command
line. For example in an R script:

``` r
echo <- TRUE
```

means that at the command line the following are supported:

``` r
my-app --echo       # TRUE
my-app --echo=yes   # TRUE
my-app --echo=true  # TRUE
my-app --echo=1     # TRUE

my-app --no-echo     # FALSE
my-app --echo=no     # FALSE
my-app --echo=false  # FALSE
my-app --echo=0      # FALSE
```

### Positional Arguments

Simple assignments of length-0 objects at the top level of an R script
become positional arguments. If the R symbol has a `...` suffix or
prefix, it becomes a collector for a variable number of positional
arguments. Positional arguments always come into the R app as character
strings.

``` r
args... <- c()
```

or

``` r
first_arg      <- c()
...middle_args <- c()
last_arg       <- c()
```

## Shipping an Rapp as part of an R package

You can easily share your R app command line executable as part of an R
package.

-   Add {Rapp} as a dependency in your DESCRIPTION
-   Place your app in the `exec` folder in your package, e.g:
    `exec/myapp`. Apps are automatically installed as executable.
-   Instruct your users to add executables from Rapp and your package to
    their `PATH`. On Linux and macOS, add the following to .bashrc or
    .zshrc (or equivalent)

    ``` bash
    export PATH=$(Rscript -e 'cat(system.file("exec", package = "Rapp"))'):$PATH
    export PATH=$(Rscript -e 'cat(system.file("exec", package = "my.package.name"))'):$PATH
    ```
-   If [`rig`](https://github.com/r-lib/rig) is already on the `PATH`,
    you can also use `rig` to run a script in a packages `exec` directory:

    ``` bash
    rig run <pkg>::<script>
    ```

# Windows

Rapp works on Windows. However, because there is no native support for
`!#` shebang executable on Windows, you must invoke Rapp directly.

``` cmd
Rapp flip-coin --n 3
```

## More examples

See the `inst/examples` folder for more example R apps.


## Other Approaches

This package is just one set of ideas for how to build command line apps in R.
Some other packages in this space:

- [littler](https://github.com/eddelbuettel/littler) (typically paired with one of the below)
- [docopt](https://github.com/docopt/docopt.R)
- [optparse](https://github.com/trevorld/r-optparse)
- [argparse](https://github.com/trevorld/r-argparse)
- [argparser](https://CRAN.R-project.org/package=argparser)

Also, some interesting examples of other approaches to exporting cli interfaces from R packages:

- [renv](https://github.com/rstudio/renv/blob/main/inst/bin/renv)
- [bspm](https://github.com/cran4linux/bspm/blob/master/R/scripts.R)
