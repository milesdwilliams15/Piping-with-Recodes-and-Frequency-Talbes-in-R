# Piping with Recodes and Frequency Talbes in R

## Recoding with recode(), mutate(), and '%>%'

The recode() function is a relatively straightforward way for recoding variables in R. But, not many
people are aware that the recode() command is compatible with functions from the dplyr package and
the piping command '%>%'.

To pipe recodes using the recode() function in the 'car' library, you can use the mutate() function:

    # Character Recode:
    mtcars <- mtcars %>% mutate(cylinders = recode(cyl, "4='4 cylinders';6='6 cylinders'; 8='8 cylinders'")) 
    # Numeric Recode:
    mtcars <- mtcars %>% mutate(cylindersNum2 = recode(cyl, "4=1;6=2; 8=3")) 

## Frequency Tables and the Shortcomings of the tabyl() Command

Also, the new tabyl() function in the janitor library is a simple method for obtaining a frequency
table.

    mtcars %>% tabyl(mpg) # percentage (really proportion) of cars with 'x' mpg.
    
     > mtcars %>% tabyl(mpg)
    mpg n percent
    1  10.4 2 0.06250
    2  13.3 1 0.03125
    3  14.3 1 0.03125
    4  14.7 1 0.03125
    5  15.0 1 0.03125
    6  15.2 2 0.06250
    7  15.5 1 0.03125
    8  15.8 1 0.03125
    9  16.4 1 0.03125
    10 17.3 1 0.03125
    11 17.8 1 0.03125
    12 18.1 1 0.03125
    ...

However, take note that there's no change in the results when you try to group the frequencies by another variable in the dataframe. 
    
    mtcars %>% group_by(cyl) %>% tabyl(mpg) # This yields the same results as the above example.
    
    > mtcars %>% group_by(cyl) %>% tabyl(mpg)
     mpg n percent
    1  10.4 2 0.06250
    2  13.3 1 0.03125
    3  14.3 1 0.03125
    4  14.7 1 0.03125
    5  15.0 1 0.03125
    6  15.2 2 0.06250
    7  15.5 1 0.03125
    8  15.8 1 0.03125
    9  16.4 1 0.03125
    10 17.3 1 0.03125
    11 17.8 1 0.03125
    12 18.1 1 0.03125
    ...

This is a problem if you want to understand how frequencies differ by certain categories (in the above example, how gas milage frequencies differ by the number of cyclinders a car has). 

This problem can be solved, however, by simply lifting the relevant count() and mutate() commands applied 
by the tabyl() function and by then using them directly (in the following order!):

    > mtcars %>% group_by(cyl) %>% count(mpg) %>% mutate(percent = n / sum(n, na.rm = TRUE))
    Source: local data frame [27 x 4]
    Groups: cyl [3]

         cyl   mpg     n    percent
        <dbl> <dbl> <int>      <dbl>
    1      4  21.4     1 0.09090909
    2      4  21.5     1 0.09090909
    3      4  22.8     2 0.18181818
    4      4  24.4     1 0.09090909
    5      4  26.0     1 0.09090909
    6      4  27.3     1 0.09090909
    7      4  30.4     2 0.18181818
    8      4  32.4     1 0.09090909
    9      4  33.9     1 0.09090909
    10     6  17.8     1 0.14285714
    # ... with 17 more rows

It works! And only at the cost of having to type just a little bit more code.
