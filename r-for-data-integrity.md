% R for Data Integrity

I'm going to talk about things
------------------------------

1.  R: what it is, what it isn't.
2.  R >> SAS. Data integrity examples given.
3.  R is not perfect.


R is
----

A programming language.


R is not an interpreter
-----------------------

although you'll need one.


R is not a REPL
---------------


R is not an IDE
---------------


Reading a CSV file in SAS
-------------------------



Make sure all of the field lengths are right!


Reading a CSV file in R
-----------------------



Getting some variable names in SAS
----------------------------------

Don't want to read the entire dataset!

proc contents

SQL where


Getting some variable names in R
--------------------------------

```r
names(df) %setdiff% c(var1, var2)
```


Flow control based on data in SAS
---------------------------------

proc sql, into, where

%if n>1 %then %do;

%end;

Flow control based on data in R
---------------------------------

```r
num = sum(df$var == 'thang')
if (num > 1) {
    ...
}
```

(why is this so hard in SAS?)
-----------------------------

Multiple reasons, but mainly:

*   SAS has two variable types: datasets & macro strings.
*   Not only that, but macro strings are different to strings within a
    dataset, so you have to do
    ```sas
    %sysfunc(countw(&str.))
    ```
    instead of just
    ```sas
    countw(&str.)
    ```
*   Translating between the two is painful!


(why is this so hard in SAS?)
-----------------------------

R has

*   R has boolean, int, double, strings, dates, complex
*   Vectors of all of them
*   Lists
*   Dataframes---made up of vectors, so can reuse code:
    ```r
    # Which entries of string vector are equal to 'R >> SAS'?
    vec == 'R >> SAS'
    # Which entries of a dataframe string column are equal to 'R >> SAS'?
    df$col == 'R >> SAS'
    ```
*   Arbitrary, user-defined datatypes

Much more expressive!


Histogram in SAS
----------------




Histogram in R
--------------

```r
ggplot(df, aes()) + geom_histogram()
```


Let's use a flag variable to split the histogram SAS
------------------------------------------------


Let's use a flag variable to split the histogram in R
------------------------------------------------

```r
ggplot(df, aes()) + geom_histogram() + facet_wrap(~ var)
```


And another variable to colour in the bars in SAS
-------------------------------------------------

```sas
/* nope */
```


And another variable to colour in the bars in R
-----------------------------------------------

```r
ggplot(df, aes(, fill=var)) + geom_histogram(somethingelse?) + facet_wrap(~ var)
```


What about analysing datesets on network drive?
---------------------------------------------

*   SAS rarely caches datasets: almost always have to read the dataset off the
    network.

*   R will load datasets into RAM *once* and then process them from there

*   Speed up (measure work RAM bandwidths vs. network bandwidths)

It's not all peaches & cream, however...


No RAM, no R
------------

*   32 bit R has a limit of 2 GB, even supposing a 64 bit executable,
    won't be able to crunch datasets bigger than 4 GB (if that) on most machines.


Core R is... weird
------------------

*   **3** different object-oriented programming systems

*   API is all over the shop
*   3 datetime classes: `POSIXct`, `POSIClt` and both are subclasses of
    `POSIXt`. (Example of manipulations?)
*   `plot` isn't exactly friendly
*   packages are arcane


Use packages!
-------------

*   `dplyr` for basic data manipulation (subsetting rows & columns,
    aggregation, column transformations)
*   `ggplot` for plotting
*   `lubridate` for date twiddling
*   `stringr` for string twiddling
*   `devtools` for writing packages
*   and more...

Don't bother learning the equivalent core R functions until you
absolutely need to.


Alternatives
------------