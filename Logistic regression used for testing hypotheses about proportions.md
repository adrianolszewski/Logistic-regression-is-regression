Despite the widespread and nonsensical claim, that "logistic regression is not a regression", it constitutes one of the key regression and hypothesis testing tools used in the experimental research (like clinical trials).

Let me show you how the logistic regression (with a few extensions) can be used to test hypotheses about fractions (%) of successes, repacling the classic "test for proportions".
Namely, it can replicate the results of:

1. the Wald's (normal approximation) **z test for 2 proportions with non-pooled standard errors** (common in clinical trials) via LS-means on the prediction scale or AME (average marginal effect)
2. the Rao's score (normal appr.) **z test for 2 proportions with pooled standard errors** (just what the `prop.test()` does in R)
3. the **z test for multiple (2+) proportions**
4. **ANOVA-like** (joint) test for multiple caterogical predictors (n-way ANOVA). Also (n-way) ANCOVA if you employ numerical covariates.
5. the **Cochran-Mantel-Haenszel (CMH) for stratified/matched data** via _conditional logistic regression_
6. the **Breslow-Day test for odds ratios** through Rao's ANOVA --> the interaction term
7. the Cochran-Armitage test for trend in ordered proportions
8. the **McNemar and Cochran Q** test of paired proportions via GEE estimation (Generalized Estimating Equations with compound symmetry)
9. the **Friedman test** - as above
10. the **Mann-Whitney-Wilcoxon and Kruskal-Wallis** via Ordinal Logistic Regression (and paired Wilcoxon via GEE)

---

We will use 3 data sets (defined at the bottom of this file):
* unpaired 2-group data

``` r
> head(unpaired_data)
     sex response    trt
1 female        0 active
2 female        0 active
3 female        0 active
4 female        0 active
5 female        0 active
6 female        0 active
> tail(unpaired_data)
     sex response     trt
101 male        1 placebo
102 male        1 placebo
103 male        1 placebo
104 male        1 placebo
105 male        1 placebo
106 male        1 placebo
```

* paired 2-group data

``` r
> head(paired_data)
  ID Time Treatment Response
1  1  Pre   placebo        0
2  1 Post   placebo        1
3  2  Pre   placebo        0
4  2 Post   placebo        0
5  3  Pre   placebo        0
6  3 Post   placebo        0
> 
> tail(paired_data)
   ID Time Treatment Response
35 18  Pre    active        0
36 18 Post    active        1
37 19  Pre    active        0
38 19 Post    active        0
39 20  Pre    active        0
40 20 Post    active        0
```

* ordered data
``` r
> head(ordered_paired_data)
  ID Time Response TimeUnord
1  1   T1        0        T1
2  2   T1        0        T1
3  3   T1        0        T1
4  4   T1        0        T1
5  5   T1        0        T1
6  6   T1        0        T1
> tail(ordered_paired_data)
   ID Time Response TimeUnord
25  5   T3        1        T3
26  6   T3        1        T3
27  7   T3        1        T3
28  8   T3        0        T3
29  9   T3        1        T3
30 10   T3        1        T3
```

---
Loading necessary packages
```{r}
library(emmeans)
library(broom)
library(survival)
library(marginaleffects)
library(geepack)
```

Defining auxiliary function (to validate the results)
``` r
wald_z_test <- function(table) {
  p1 <- prop.table(table, 1)[1, 1]
  p2 <- prop.table(table, 1)[2, 1]
  n1 <- rowSums(table)[1]
  n2 <- rowSums(table)[2]
  se_p1 <- sqrt(p1 * (1 - p1) / n1)
  se_p2 <- sqrt(p2 * (1 - p2) / n2)
  se_diff <- sqrt(se_p1^2 + se_p2^2)
  z <- (p1 - p2) / se_diff
  p <- 2 * (1 - pnorm(abs(z)))
  return(data.frame(estimate = p1 - p2, z = z, se = se_diff, p.value = p, row.names = NULL))
}
```

---
# Wald's z test for 2 proportions (non-pooled SE)

We want to reproduce this result:
``` r
> wald_z_test(xtabs(~ trt + response,data = unpaired_data))
   estimate        z         se     p.value
1 0.2737968 3.047457 0.08984435 0.002307865
```

We will use this logistic regression (LR) model:
``` r
> summary(lr_model <- glm(response ~ trt , data = unpaired_data, family = binomial(link = "logit")))

Call:
glm(formula = response ~ trt, family = binomial(link = "logit"), 
    data = unpaired_data)

Deviance Residuals: 
    Min       1Q   Median       3Q      Max  
-1.7011  -1.1620   0.7325   1.0778   1.1929  

Coefficients:
            Estimate Std. Error z value Pr(>|z|)   
(Intercept) -0.03637    0.26972  -0.135  0.89274   
trtplacebo   1.21502    0.42629   2.850  0.00437 **
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 140.50  on 105  degrees of freedom
Residual deviance: 131.88  on 104  degrees of freedom
AIC: 135.88

Number of Fisher Scoring iterations: 4
```

## Wald's z test via LS-means on re-grided scale (probability scale)
``` r
> pairs(emmeans(lr_model, regrid="response", specs = ~ trt))
 contrast         estimate     SE  df z.ratio p.value
 active - placebo   -0.274 0.0898 Inf  -3.047  0.0023
 ```
Let's look closer at the results:
| Outcome   | LS-means | raw z test | comment |
|-----------|----------|------------|---------|
| estimate  | -0.2737968 | 0.2737968| 👍; swap factor levels to change the sign or ignore |
| SE        | 0.08984432 | 0.08984435 | agreement by 7 dec. digits 👍 |
| statistic | -3.047458 | 3.047457 | sign - as above; agreement by 5 dec. digits 👍 |
| p-value   | 0.002307857 | 0.002307865 | aggrement by 7 dec. digits 👍 |

Excellent agreement!

## Wald's z test via AME (average marginal effect)
``` r
> marginaleffects::avg_slopes(lr_model)

 Term         Contrast Estimate Std. Error    z Pr(>|z|)   S  2.5 % 97.5 %
  trt placebo - active    0.274     0.0898 3.05  0.00231 8.8 0.0977   0.45

Columns: term, contrast, estimate, std.error, statistic, p.value, s.value, conf.low, conf.high 
 ```
Let's look closer at the results:
| Outcome   | AME | raw z test | comment |
|-----------|----------|------------|------|
| estimate  | 0.2737968 | 0.2737968 | | 👍 |
| SE        | 0.08984433 | 0.08984435 | 👍 |
| statistic | 3.047458 | 3.047457 | agreement by 5 dec. digits 👍 |
| p-value   | 0.002307859 | 0.002307865 | agreement by 6 dec. digits 👍 |

Perfect agreement!

---
# Rao score z test for 2 proportions (pooled SE)

We want to reproduce this result:
``` r
> prop.test(xtabs(~ trt + response,data=unpaired_data), correct = FALSE)

	2-sample test for equality of proportions without continuity correction

data:  xtabs(~trt + response, data = unpaired_data)
X-squared = 8.4429, df = 1, p-value = 0.003665
alternative hypothesis: two.sided
95 percent confidence interval:
 0.09770511 0.44988848
sample estimates:
   prop 1    prop 2 
0.5090909 0.2352941 
```

We will use the same logistic regression (LR) model as previously

## Rao score z test via ANOVA with Rao test
``` r
> anova(glm(response ~ trt , data = unpaired_data, family = binomial(link = "logit")), test = "Rao")
Analysis of Deviance Table

Model: binomial, link: logit

Response: response

Terms added sequentially (first to last)

     Df Deviance Resid. Df Resid. Dev    Rao Pr(>Chi)   
NULL                   105     140.50                   
trt   1   8.6257       104     131.88 8.4429 0.003665 **
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
Let's look closer at the results:
| Outcome   | ANOVA + Rao test | prop.test() | comment |
|-----------|----------|------------|---------|
| statistic | 8.442898 | 8.442897 | agreement by 5 dec. digits 👍 |
| p-value   | 0.003664718 | 0.003664719 | agreement by 8 dec. digits 👍 |

Perfect agreement!

---
# Breslow-Day test for odds ratios via ANOVA with Rao test

We want to reproduce this result for treatment and sex:
``` r
> BreslowDayTest(xtabs(~ trt +response + sex, data=unpaired_data), correct = TRUE)

	Breslow-Day Test on Homogeneity of Odds Ratios (with Tarone correction)

data:  xtabs(~trt + response + sex, data = unpaired_data)
X-squared = 1.4905, df = 1, p-value = 0.2221
```
This time add sex to the model and will look at the interaction term

``` r
> as.data.frame(anova(glm(response ~ trt * sex , data = unpaired_data, family = binomial(link = "logit")), test="Rao")[4, ])
        Df Deviance Resid. Df Resid. Dev      Rao  Pr(>Chi)
trt:sex  1 1.498573       102   130.0512 1.496552 0.2212027
```
Let's look closer at the results:
| Outcome   | ANOVA + Rao test | Breslow-Day | comment |
|-----------|----------|------------|---------|
| statistic | 1.496552 |  1.490537  | agreement by 2 dec. digits 👍 |
| p-value   | 0.2212027 | 0.2221331 | agreement bt 2 dec. digits 👍 |

Good agreement!

---
# (Cochrane-) Mantel-Haenszel via conditional logistic regression

We want to reproduce this result for sex strata:
``` r
> mantelhaen.test(unpaired_data$response, unpaired_data$trt, unpaired_data$sex, exact = F, correct = F)

	Mantel-Haenszel chi-squared test without continuity correction

data:  unpaired_data$response and unpaired_data$trt and unpaired_data$sex
Mantel-Haenszel X-squared = 8.3052, df = 1, p-value = 0.003953
alternative hypothesis: true common odds ratio is not equal to 1
95 percent confidence interval:
 1.445613 7.593375
sample estimates:
common odds ratio 
         3.313168 
```
And through the model:
``` r
> summary(clogit(response~trt + strata(sex),data=unpaired_data))$sctest
      test         df     pvalue 
8.30516934 1.00000000 0.00395324 
```
Let's look closer at the results:
| Outcome   | Cond. LR | CMH | comment |
|-----------|----------|------------|---------|
| statistic | 8.30516934 | 8.305169 | 👍 |
| p-value   | 0.00395324 | 0.00395324 | 👍 |

Ideal agreement!

---
# McNemar's, Cochran Q, Friedman tests via GEE estimated LR
We want to reproduce this result for sex strata:
``` r
> mcnemar.test(x=paired_data[paired_data$Time == "Pre", "Response"], y=paired_data[paired_data$Time == "Post", "Response"], correct = F)

	McNemar's Chi-squared test

data:  paired_data[paired_data$Time == "Pre", "Response"] and paired_data[paired_data$Time == "Post", "Response"]
McNemar's chi-squared = 10.286, df = 1, p-value = 0.001341

# or this one

> paired_data %>% rstatix::friedman_test(Response ~ Time |ID)
# A tibble: 1 × 6
  .y.          n statistic    df       p method       
* <chr>    <int>     <dbl> <dbl>   <dbl> <chr>        
1 Response    20      10.3     1 0.00134 Friedman test

# or this one

> RVAideMemoire::cochran.qtest(Response ~ Time | ID,data=paired_data)

	Cochran's Q test

data:  Response by Time, block = ID 
Q = 10.2857, df = 1, p-value = 0.001341
alternative hypothesis: true difference in probabilities is not equal to 0 
sample estimates:
proba in group Post  proba in group Pre 
                0.7                 0.1 
```

Through the GEE-estimated model:
``` r
> summary(geepack::geeglm(Response ~ Time, id = ID,data=paired_data, family = binomial(), corstr = "exchangeable"))

Call:
geepack::geeglm(formula = Response ~ Time, family = binomial(), 
    data = paired_data, id = ID, corstr = "exchangeable")

 Coefficients:
            Estimate Std.err   Wald Pr(>|W|)   
(Intercept)   0.8473  0.4880  3.015  0.08249 . 
TimePre      -3.0445  0.9484 10.305  0.00133 **
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Correlation structure = exchangeable 
Estimated Scale Parameters:

            Estimate Std.err
(Intercept)        1  0.7215
  Link = identity 

Estimated Correlation Parameters:
      Estimate Std.err
alpha  -0.1455  0.2819
Number of clusters:   20  Maximum cluster size: 2 

# or in a more compact form:
> coef(summary(geepack::geeglm(Response ~ Time, id = ID,data=paired_data, family = binomial(), corstr = "exchangeable")))[2,]
        Estimate Std.err  Wald Pr(>|W|)
TimePre   -3.045  0.9484 10.31 0.001327
```

Let's look closer at the results:
| Outcome   | GEE LR | Tests | comment |
|-----------|----------|------------|---------|
| statistic | 10.31 | 10.2857 | agreement by 1 deci. digits 👍 |
| p-value   | 0.001327 | 0.001341 | agreement by 4 dec. digits 👍 |
 
Acceptable agreement!

---
# Cochrane-Armitage test for trend via GLM + ANOVA LRT (Likelihood Ratio Test)
We want to reproduce this result for sex strata:
``` r
> DescTools::CochranArmitageTest(xtabs(~Response + Time,data=ordered_paired_data))

	Cochran-Armitage test for trend

data:  xtabs(~Response + Time, data = ordered_paired_data)
Z = -3.6, dim = 3, p-value = 0.0003
alternative hypothesis: two.sided

# or this one

> rstatix::prop_trend_test(xtabs(~Response + Time,data=ordered_paired_data))
# A tibble: 1 × 6
      n statistic        p p.signif    df method               
* <int>     <dbl>    <dbl> <chr>    <dbl> <chr>                
1    30      12.9 0.000336 ***          1 Chi-square trend test
```

Through the GLM model:
``` r
> as.data.frame(anova(glm(Response ~ Time,data=ordered_paired_data, family = binomial()), test="LRT"))[2,]
     Df Deviance Resid. Df Resid. Dev  Pr(>Chi)
Time  2    14.99        27      26.46 0.0005553
```

Let's look closer at the results:
| Outcome   | GLM + LRT ANOVA | Test | comment |
|-----------|----------|------------|---------|
| statistic | 12.86 | 14.99 | same order of magnitude |
| p-value   | 0.0005553 | 0.000336 | agreement by 3 dec. digits 👍 |
 
Reasonable agreement. (Maybe I'll find a better one).

---
* _EM-means_ (estimated marginal means) is another name of the well-known in experimental research _LS-means_ (least-square means)
It's a model-based predicted (estimated) mean. If you remember the definition of regression (NO, not the Machine Learning one...)
then you know that regresion gives you a some function of the data conditional to the predictor.
For the linear regression it's E(Y|X=x), for the GLM it is link(E(Y|X=x)), for quantile regression it's median(Y|X=x).
And since the predictor exclusively consists of categorical variables, they form sub-groups in which the (conditional) 
means are calculated. If we include also numerical covariates into the model, the predictions will account for it, giving us so-called "covariate-adjusted means".
