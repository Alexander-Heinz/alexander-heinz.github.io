How To Bootstrap Lasso Coefficients
================

In this tutorial and code snippet, I’ll show you how to gain more
confidence in your parameter estimates from a lasso regression.

Lasso regression is known for a rather low Variable Selection Precision
(VSP).

See [Here](https://web.stanford.edu/~hastie/Papers/elasticnet.pdf) or
[Here](https://www.duo.uio.no/bitstream/handle/10852/38874/KineVeronicaLund_MasterThesis.pdf?sequence=1)
for more details about this, but the essence is that:

-   Lasso variable selection is poor when the number of feature is
    higher than the number of observations.

-   Lasso variable selection is unstable when you have groups of
    features that are highly correlated.

To get an estimate of how stable predictors are in the selection
process, the following approach has a simple logic:

-   Run the `glmnet` model 1000 times, including a 10-fold [Cross
    Validation](https://machinelearningmastery.com/k-fold-cross-validation/)
    every run to select a `lambda` value.

-   Extract the 25th and 975th value to get a [bootstrap confidence
    interval](https://acclab.github.io/bootstrap-confidence-intervals.html)
    (optionally, you could also take the minimum and maximum value)

Let us start by preparing the `mtcars` dataset for a lasso regresion in
`glmnet`. 

As an example, we will predict the miles per gallon from different cars in the `cars` dataset that is included in `R`.


``` r
if (!require("pacman")) install.packages("pacman") # to install required packages
```

    ## Loading required package: pacman

``` r
# LOAD PACKAGES AND INSTALL IF NOT THERE
pacman::p_load(glmnet,
               tidyverse,
               kableExtra
)
n_folds = 10 # the number of folds for cross validation
var_imp_avg = tibble() # prepare the output dataframe / tibble
iterations = 1000 # number of bootstrap samples
# prepare dataset in a glmnet format:
outcome <- mtcars$mpg %>% data.matrix() 
predictors <- mtcars %>% select(-mpg) %>%  data.matrix()
```

We set the number of folds for cross validation using `n_folds`. We also prepared a dataframe for the output and set the number of bootstrap samples to 1000. As predictors, we select all variables except for `mpg`, which should be our outcome variable.

Now let’s start the bootstrapping process!

``` r
for (i in 1:iterations) {
  # predictive model:
  cv_model = glmnet::cv.glmnet(predictors, outcome, # the formula
                               alpha = 1, # for lasso
                               family = "gaussian", # for linear regression
                               type.measure = "mse", # your error measure, here mean squared error
                               nfolds = n_folds, # the number of folds in cross validation
                               standardize = TRUE, # standardize coefficients
                               intercept = FALSE) # calculate an intercept y/n
  
  # extract the coefficients
  var_imp <- coef(cv_model, s = cv_model$lambda.1se) %>% 
    as.matrix() %>% 
    as.data.frame() %>% 
    rownames_to_column("Factor") %>% 
    rename("Importance" = "s1")
  
  # extract the RMSE and lambda bootstrapped measurements
  RMSE <- cv_model$cvm[cv_model$lambda == cv_model$lambda.1se] %>% sqrt() %>% round(2)
  lambda <- cv_model$lambda.1se %>% round(2)
  
  if(i == 1){
    lambda_values <- lambda
    RMSE_values <- RMSE
    var_imp_avg <- var_imp
  }
  else{
    var_imp_avg <- left_join(var_imp_avg, var_imp, by = "Factor")
    lambda_values <- lambda_values %>% append(lambda)
    RMSE_values <- RMSE_values %>% append(RMSE)
  }
}

upper = c()
lower = c()
for (row in seq_len(nrow(var_imp_avg))){
  upper[row] = sort(var_imp_avg[row,])[975]
  lower[row] = sort(var_imp_avg[row,])[25]
}
# get the first column as rownames
var_imp_avg <- column_to_rownames(var_imp_avg, var = "Factor")
# now create a mean per row:
var_imp_avg <- rowMeans(var_imp_avg)
# format as dataframe
var_imp_avg <- as.data.frame(var_imp_avg)
# get rownames back as column
var_imp_avg <- rownames_to_column(var_imp_avg, var = "Factor")
# rename the Importance column
var_imp_avg <- var_imp_avg %>%
  rename(Importance = var_imp_avg)
# round the confidence intervals
upper <- unlist(upper) %>% round(2)
lower <- unlist(lower) %>% round(2)
# create CI column
var_imp_avg$"95% CI" <- paste("[", lower, ", ", upper, "]", sep = "")

# filter out predictors with Variable importance greater than zero
var_imp_avg <- var_imp_avg %>%  
  filter(abs(Importance) > 0)
```

As you can see, I ran the cross-validated model 1000 times and stored the results in the output dataframe. I selected the 25th and 975th to get the values which can be seen as a bootstrapped confidence interval of our values. They provide a measurement of variation of our coefficients.

Now that we have the coefficients, we can print them (here, I'm using `kableExtra` for R markdown files):

``` r
var_imp_avg %>% 
  #mutate(Importance = round(Importance, 2)) %>% 
  kableExtra::kbl(booktabs = T,
                  col.names = c("Factor", "mean coeff.", "95% CI"),
                  escape = TRUE,
                  format = "markdown")
```

| Factor | mean coeff. | 95% CI           |
|:-------|------------:|:-----------------|
| hp     |  -0.0000020 | \[0, 0\]         |
| drat   |   2.8611797 | \[2.46, 3.29\]   |
| wt     |  -2.3544672 | \[-1.93, -2.58\] |
| qsec   |   0.9550230 | \[0.8, 1.07\]    |
| am     |   0.6647145 | \[0, 1.4\]       |
| carb   |  -0.0038989 | \[-0.01, 0\]     |

It looks like we have a variable that sometimes gets dropped out the
model: the 95% CI of `am` (automatic / manual gear) includes zero. Thus, we are rather uncertain about the influence of manual vs. automatic gear on the miles per gallon of a car. 
Also, the `carb` (carbon emissions) variable has a tiny CI, and the one of `hp` (horsepower). As these are standardized coefficients, we can followingly say that the automatic / manual gear, horse power, and the carbon emissions don't really have an influence on the miles per gallon a car uses, but, e.g., the weight of it has!

I hope you understood the essence of this by now: if we did not use bootstrapping here, we might have had a positive coefficient of `am` just by chance, and overestimated its influence. 