Practical: Psychological therapies
================
Anna Dzieciol
October 23, 2017

We will be analysing data from *Improving Access to Psychological Therapies*, an NHS programme that provides mental health support for people with anxiety and depression. The data on service use is published on NHS Digital. We will use Google Trends to see how search interest in psychological therapies is changing, and whether this is associated with changes in NHS service use.

R basics
--------

### Command prompt

The console window on the bottom of R Studio shows the command prompt. &gt; tells us that R is ready for our commands.

### Help

To access R documentation for a fuction, type ? and a function name.

``` r
?read.csv
```

There are also many websites with help and tips, for example Stack Overflow: <https://stackoverflow.com/>

### Functions

We perform analysis by running functions on data. A lot of functions are included in core R, some are in extension packages, and we can define new functions as we analyse data. To call a function, we first type function name, and then a set of brackets which may contain some arguments.

**Example functions:**

``` r
# List files in current directory
list.files()
```

    ##  [1] "google_trends_data" "iapt.csv"           "iapt_data"         
    ##  [4] "iapt.R"             "iapt_reference"     "psych-ds.Rproj"    
    ##  [7] "trends.csv"         "tutorial_files"     "tutorial.md"       
    ## [10] "tutorial.Rmd"

``` r
# Create a vector with three elements - a series of three numbers.
c(1,2,3)
```

    ## [1] 1 2 3

We can nest functions inside other functions.

``` r
print(c(1,2,3))
```

    ## [1] 1 2 3

### Assignment operator

R auto-prints output to the console.

``` r
2+2
```

    ## [1] 4

To prevent this we can use `<-`, an assignement operator. Instead of prining the result, R will save it under name `a`.

``` r
a <- 10/5
```

To view the result, we need to type `a`

``` r
a
```

    ## [1] 2

Another example:

``` r
x <- 10
y <- 2
z <- x*y
```

We can see a list of objects we created in the Environment tab in R studio.

### The data

We are ready to look at some data. First, let's open the NHS dataset.

``` r
iapt <- read.csv("iapt.csv", stringsAsFactors = FALSE)
```

`iapt` is a data frame - where a row represents one observation, and a column one variable. Each column can be of different data type, for example text columns can sit alongside numeric columns.

``` r
class(iapt)
```

    ## [1] "data.frame"

What are the column names?

``` r
names(iapt)
```

    ##  [1] "GroupType"                "Month"                   
    ##  [3] "ReferralsReceived"        "SelfReferrals"           
    ##  [5] "Appointments"             "NotCaseness"             
    ##  [7] "Recovery"                 "Improvement"             
    ##  [9] "Deterioration"            "NoReliableChange"        
    ## [11] "ReliableRecovery"         "GuideSelfHelpBookApts"   
    ## [13] "NonGuideSelfHelpBookApts" "GuideSelfHelpCompApts"   
    ## [15] "NonGuideSelfHelpCompApts" "BehavActLIApts"          
    ## [17] "StructPhysActApts"        "AntePostNatalCounselApts"
    ## [19] "PsychoEducPeerSuppApts"   "OtherLIApts"             
    ## [21] "EmploySuppLIApts"         "AppRelaxApts"            
    ## [23] "BehavActHIApts"           "CoupleTherapyDepApts"    
    ## [25] "CollabCareApts"           "CounselDepApts"          
    ## [27] "BPDApts"                  "EyeMoveDesenReproApts"   
    ## [29] "MindfulApts"              "OtherHIApts"             
    ## [31] "EmploySuppHIApts"         "CBTApts"                 
    ## [33] "IPTApts"

Let's look at the data. `head()` shows the first 6 lines, `View()` opens the dataset in a new tab. Although the data looks like a spreadsheet, we can't click into it to change it - we can only modify the data by writing code.

``` r
head(iapt)
View(iapt)
```

To find out a little bit more about the data, we can use `str()`. It shows the dimensions of the dataset (number of rows and columns), the column names, and variable classes: that variables are numeric (`int`) and character (`char`). It also gives us the first few data values.

``` r
str(iapt)
```

`summary()` will show minimum, maximum and average values of each numeric variables in the dataset, as well as how many missing values `NA's` there are.

``` r
summary(iapt)
```

### Indexing

We refer to the dataset by name `iapt`. To rename the dataset, we can simply assign a new name to it.

``` r
data <- iapt
```

What if we wanted to look at only a part of the data? We refer to a datapoint by using square brackets: \[row number, column number\]

``` r
# Third row and first column
iapt[3,1]
```

    ## [1] "England"

``` r
# First and second element of 12th column
iapt[c(1, 2), 12]
```

    ## [1] NA NA

``` r
# Entire tenth row
iapt[10, ]
```

``` r
# 5th to 12th element of 7th row
iapt[c(5:12), 7]
```

    ## [1] 17480 19625 20974 17281 19552 19160 18733 17512

``` r
# Same as above.
# iapt[c(5,6,7,8,9,10,11,12), 7]
```

We can also refer to columns by column names.

``` r
iapt$ReferralsReceived
```

    ##  [1] 112683 111609 123474 103897  99893 114126 115727  96761 115373 121906
    ## [11] 123774 100088 119470 123179 118989 112610 107128 113918 105202 106462
    ## [21] 116572 116325 126126  92260 126297 113707 130073 102535 127243 119346

Combining column names and row numbers.

``` r
# First three elements of SelfReferrals column
iapt$SelfReferrals[1:3]
```

    ## [1] 50017 47527 53501

We can also use logical statements for indexing.

``` r
# Select all Months where the numbers of appointments is more than 0.5 million, 
# and number of self referrals is  greater than 70 thousand
iapt[iapt$Appointments > 500000 & iapt$SelfReferrals > 70000, 2]
```

    ## [1] "2016-11-01" "2017-01-01" "2017-03-01" "2017-05-01" "2017-06-01"

### Data types

We saw that the `iapt` dataset contains some numeric and categorical data. Having correct data types is important, as some functions can only be performed on certain data types. Let's change the `Month` variable to a date type.

``` r
iapt$Month <- as.Date(iapt$Month, format = "%Y-%m-%d")
class(iapt$Month)
```

    ## [1] "Date"

Let's also create a year variable. We will take the first four characters of the Month variable using substr command.

``` r
iapt$year <- substr(iapt$Month, 1, 4)
```

iapt dataset
------------

Now that we learned some basics of R, we are ready to analyse the data.

**1. How many referrals did this NHS programme receive in 2016? What is this as a proportion of England's population?**
Hint: England's population is 55 million

``` r
referrals2016 <- iapt$ReferralsReceived[iapt$year == 2016]

# Calculate sum using sum() function.

# Use R like a calculator to get a proportion.
```

**1. Did the number of referrals to this NHS programme increase over time?**

Was the total number of referrals in 2016 greater than in 2015?

``` r
# We already calculated the numbers of referrals in 2016. Now, repeat this for 2015.

# Subtract one value from another.
```

We can also plot the data.

``` r
plot(iapt$Month, iapt$ReferralsReceived)
```

![](tutorial_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-27-1.png)

To customise the plot a little bit.

``` r
plot(iapt$Month, iapt$ReferralsReceived, 
     xlab = "Month", ylab = "Number of referrals", 
     ylim = c(0, 130000), col = "red", type = "l")
```

![](tutorial_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-28-1.png)

**2. Did the number of appointments change over time? Were there more appointments in 2016, compared to 2015?**

``` r
# Repeat the steps above for a new variable.
```

**3. What types of therapies are offered by the NHS?**

``` r
# First, let's summarise the data a little. Total number of self help appointments
iapt$total_self_help <- iapt$GuideSelfHelpBookApts + 
  iapt$NonGuideSelfHelpBookApts + 
  iapt$GuideSelfHelpCompApts + 
  iapt$NonGuideSelfHelpCompApts

# What proportion of overall appointments is each therapy type, for each month.
iapt$total_self_help_prop <- iapt$total_self_help / iapt$Appointments

# Continue making variables for a few other therapies or therapy types.

# Calculate average of monthly proportions, for each variable.
summary(iapt$total_self_help_prop)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
    ##    0.15    0.15    0.16    0.16    0.17    0.17       6

``` r
# or..
mean(iapt$total_self_help_prop, na.rm = TRUE)
```

    ## [1] 0.16

``` r
# or, to get means of all columns in one step. Note: you need to change the column index.
colMeans(iapt[, 3:5], na.rm = TRUE)
```

    ## ReferralsReceived     SelfReferrals      Appointments 
    ##            113892             60211            494735

Google Trends dataset
---------------------

We are ready to combine the `iapt` dataset with data from Google Trends <https://trends.google.co.uk/trends/> .

``` r
trends <- read.csv("trends.csv")
trends$week <- as.Date(trends$week, format = "%Y-%m-%d")
```

We will merge the two datasets by a key - an id column that is identical in the two datasets. The key we will use will be the month column.

``` r
# Run each line one by one to see intermediate output. Otherwise, the variable name gets overwritten.

# Erase the last three characters - day, from the date
trends$month <- substr(trends$week, 0, 7)
# Replace the day with "-01", to match the date format in iapt dataset.
trends$month <- paste0(trends$month, "-01")
# Change the class of variable to a date, to match the iapt dataset.
trends$month <- as.Date(trends$month, format = "%Y-%m-%d")
```

But, in the trends dataset one row represents one week. In iapt data, one row is one month. To combine these datasets, we first need to transform the weekly data into monthly data. We will calculate average weekly search volume for each month.

R has many extension packages. We will use package dplyr to aggregate data. Each package has a different syntax.

``` r
# Load package
library(dplyr)

monthly_trends <- trends %>% 
  group_by(month) %>% 
  summarise(cbt = mean(cognitive_behavioural_therapy, na.rm = TRUE), 
            counselling = mean(counselling_psychology, na.rm = TRUE), 
            antidepressant = mean(antidepressant, na.rm = TRUE),
            depression = mean(depression, na.rm = TRUE),
            anxiety = mean(anxiety, na.rm = TRUE))
```

We are now ready to combine the two datasets.

``` r
dat <- merge(iapt, monthly_trends, by.x = "Month", by.y = "month")
```

**4. Is search interest in psychotherapies and mental health related to the number of referrals to the NHS IAPT services?**

``` r
plot(dat$ReferralsReceived, dat$counselling)
```

![](tutorial_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-35-1.png)

Let's do a correlation test to see if this effect is statistically significant.

``` r
cor.test(dat$ReferralsReceived, dat$counselling)
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  dat$ReferralsReceived and dat$counselling
    ## t = 4, df = 20, p-value = 0.0005
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.330 0.827
    ## sample estimates:
    ##   cor 
    ## 0.641

**5. Try plotting and correlating different sets of variables**
