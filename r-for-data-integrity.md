% R for Data Integrity

Let's talk about R and SAS
--------------------------

1.  R: what it is, what it isn't.
2.  R >> SAS. Data integrity examples given.
3.  R is not perfect.


R is
----

a programming language, defined by a specification:


R is not an interpreter
-----------------------

although you'll need one.


R is not a Read Evaluate Print Loop (REPL)
------------------------------------------


R is not an Integrated Development Environment (IDE)
----------------------------------------------------


Reading a CSV file in SAS
-------------------------

```sas
data ds;
    %let _EFIERR_ = 0; /* set the ERROR detection macro variable */
    infile "C:\path\to\file.csv" delimiter = ',' MISSOVER DSD lrecl=32767 firstobs=2;
       informat ID $10.;
       informat DOB yymmdd10.;
       informat Gender $1.;
       informat Age best32.;
       informat Address $127.;
       informat Postcode $4.;
       informat Height best32.;
       format ID $10.;
       format DOB yymmdd10.;
       format Gender $1.;
       format Age best32.;
       format Address $127.;
       format Postcode $4.;
       format Height best32.;
    input
        ID $
        DOB
        Gender $
        Age
        Address $
        Postcode $
        Height
        ;
    if _ERROR_ then call symputx('_EFIERR_',1);  /* set ERROR detection macro variable */
run;
```

Make sure all of the field lengths are right! What happens -> more columns, resupply, merge multiple files?


Reading a CSV file in R
-----------------------

```r
df = read.csv("C:\\path\\to\\file.csv", header=TRUE, stringsAsFactors=FALSE)
```

Bonus: when reading a dataset split between multiple files, don't need to worry about the same column having different field lengths!

![](gifs/ecstatic.gif)


Something else that's painful in SAS...
---------------------------------------

```SAS
proc sort data=ds_1; by var; run;
```


Something else that's painful in SAS...
---------------------------------------

```SAS
proc sort data=ds_1; by var; run;
proc sort data=ds_2; by var; run;
```


Something else that's painful in SAS...
---------------------------------------

```SAS
proc sort data=ds_1; by var; run;
proc sort data=ds_2; by var; run;

data merged;
    merge ds_1 (in=a) ds_2 (in=b);
    by var;
    if a=1 and b=0 then output;
run;
```


... but easy in R
-----------------

```R
merged = anti_join(ds_1, ds_2, by="var")
```

. . .

(we cheated a <small>little</small> bit: `left_join` is in `dplyr`, not core R)


Flow control based on data in SAS
---------------------------------

Let's print a message if a column `var` takes on the value `'foo'` at least once.

. . .

Should be easy, right?

. . .

```SAS
proc sql;
    select count(*) into :num
    from df
    where var='foo'
    ;
quit;

%if &num > 0 %then %put 'foo' present;
```


Flow control based on data in R
-------------------------------

```r
num = sum(df$var == 'foo')
if (num > 0) print("'foo' present")
```


(why is this so hard in SAS?)
-----------------------------

Many reasons, but mainly:

*   SAS has two variable types: datasets & macro strings.
*   Only macro strings are involved in control flow.
*   Not only that, but macro strings are different to strings within a
    dataset, so you have to do
    ```sas
    %sysfunc(countw(&str.))
    ```
    instead of just
    ```sas
    countw(&str.)
    ```
*   Translating between datasets and macro variables is painful!


(why is this so hard in SAS?)
-----------------------------

R has

*   boolean, int, double, strings, dates, complex types
*   Vectors of all of them
*   Lists
*   Dataframes, which are made up of vectors, so can reuse code:
    ```r
    # Which entries of a vector of strings are equal to 'foo'?
    vec == 'foo'
    # Which entries of a dataframe string column are equal to 'foo'?
    df$col == 'foo'
    ```
*   Arbitrary, user-defined datatypes
*   All of these can be used in control flow statements

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


What about analysing datesets stored on a network drive?
--------------------------------------------------------

*   SAS rarely caches datasets: almost always have to read the dataset off the
    network.

*   R will load datasets into RAM *once* and then process them from there

*   Speed up ~20 GB/s vs ~100MB/s bandwidths

. . .

It's not all peaches & cream, however...


No RAM, no R
------------

*   If it doesn't fit into memory, you're going to have to get creative.
    For our current machines, this means datasets bigger than 5 GB (if that).


Core R is... weird
------------------

*   **3** different object-oriented programming systems

*   API is all over the shop:
    *   3 datetime classes: `POSIXct`, `POSIClt` and both are subclasses of
        `POSIXt`. (Example of manipulations?)
    *   `sys` vs `Sys` vs `System`
    *   bad default behaviour for many core functions

*   Although The documentation is verbose and long-winded to everyone except


Use (Hadley Wickham's) packages!
--------------------------------

*   `dplyr` for basic data manipulation (subsetting rows & columns,
    aggregation, variable transformations)
*   `ggplot` for plotting
*   `lubridate` for date munging
*   `stringr` for string munging
*   `devtools` for writing packages
*   and more...

Don't bother learning the equivalent core R functions until you
absolutely need to.
