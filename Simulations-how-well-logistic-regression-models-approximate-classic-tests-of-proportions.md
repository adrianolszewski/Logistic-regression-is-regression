![obraz](https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/35f70dac-488a-4ffa-a29a-3d8a57d257c4)# Introduction
[In this document](https://github.com/adrianolszewski/Logistic-regression-is-regression/blob/main/Testing%20hypotheses%20about%20proportions%20using%20logistic%20regression.md) I showed how the model-based approach, namely
the logistic regression (with extensions) can replicate numerous classic tests of proportions: 
Wald's and Rao z test for 2+ proportions (+ ANOVA-like), Cochran-Mantel-Haenszel (CMH), Breslow-Day, Cochran-Armitage, McNemar, Cochran Q, Friedman, Mann-Whitney (-Wilcoxon), Kruskal-Wallis

For some models the equivalence is strict, I mean - it could be shown by formulas (I was shown it in the past yet  I don't take the challenge to replicate :-) ).
For others it's a close approximation, coming from different approaches (thus, different set of calculations), testing different (yet related!) hypotheses and sometimes even rounding.
When you take a simple model like `response ~ categorical_predictor`, then, using the _treatment coding_ (default in R) the interpretation comes out of the box:
- value of the intercept is the (conditional) mean, or median, or any other function of data in the group rendered by  the reference level of your predictor (e.g. non-smokers). Note, that the mean can be either "raw"
or transformed via link function in case of the GLM (Generalized Linear Model).
- value of the 2nd model coefficient -assigned to the 2nd level of the predictor- is the difference between the 2nd group and the intercept (1st) group. 
- ... this extends to multiple predictors with >2 levels and their interactions. The calculations complicate quickly (especially in presence of interactions), but the idea remains the same.

Whether it is directly and easily interpretable depends on the formulation of your model. 

For the general linear model (say, just the linear regression) the interpretation will be easy: intercept stands for the mean in the reference group,
and the value of the "beta" (assigned to the 2nd level of predictor) represents the difference in means between both groups.
Similarly for the quantile regression yet now you get differences in medians, first, third or any other quantile you specified.

The situation complicates if you applied transformation of the dependent variable (response) or in case of the Generalized Linear Models (GLM) transforming -in contrast- the E(Y|X=x), 
like the logistic, ordinal logistic, beta and gamma regressions (+ a few more). First, the interpretation of the "conditional mean" depends
on the conditional distribution of the data (e.g. for the Bernoulli's distribution in the logistic regression the E(Y|X=x) means is probability of success). Second, it depends on the used link function, where - depending on the used transformation - differences may turn into ratios, and (say) probabilities turn into log-odds. No surprise that the interpretation of model coefficients will complicate (a bit). 

And this poses another issue. Let's take the logistic regression. While all these terms: probabilities, odds, log-odds, odds-ratios, testing hypotheses are mutually related through the well-known formulas, performing inference
on different scales may yield a little bit different results. Well, at the end of the day, you test *different* hypotheses (though related, so the results cannot be wildly discrepant!) and you will operate on different scales.
And this will complicate EVEN MORE if you will ignore some terms (average over them), because it DOES matter whether you average on the original (logit) or the transformed (probability) scale.
Change on the scale of the log-odds is not the same as change on the probability scale.
[Why is the p value by average marginal effects different than the p value of the coefficients?](https://stats.stackexchange.com/questions/464115/why-is-the-p-value-by-average-marginal-effects-different-than-the-p-value-of-the)

With the logistic regression you can compare outcomes on both logit and probability scales. The former comes out of the box, the latter requires either calculating the AME (average marginal effect) or employing the LS-means on appropriate scale.
[Transformations and link functions in emmeans](https://cran.r-project.org/web/packages/emmeans/vignettes/transformations.html)

/ If you're curious about the AME, especially in terms of the logistic regression, visit: [Usage Note 22604: Marginal effect estimation for predictors in logistic and probit models](http://support.sas.com/kb/22/604.html) | [A Beginner’s Guide to Marginal Effects](https://library.virginia.edu/data/articles/a-beginners-guide-to-marginal-effects) |
[How can I use the margins command to understand multiple interactions in logistic regression? Stata FAQ](https://stats.oarc.ucla.edu/stata/faq/how-can-i-use-the-margins-command-to-understand-multiple-interactions-in-logistic-regression-stata/) |
[Week 13: Interpreting Model Results: Marginal Effects and the margins Command](https://clas.ucdenver.edu/marcelo-perraillon/sites/default/files/attached-files/week_13_margins.pdf) | [Stata 18 - Help for margins](https://www.stata.com/help.cgi?margins) |
[An introduction to margins in R](https://cran.r-project.org/web/packages/margins/vignettes/Introduction.html)

If you are curious about the LS-means (EM-means), that's another story and just use Google.
Ideally add "SAS" to your queries, as commercial packages (SAS, Stata, SPSS, NCSS) have the best documentation I've ever seen (sorry R).
[ Getting started with emmeans ](https://aosmith.rbind.io/2019/03/25/getting-started-with-emmeans/)
[Shared Concepts and Topics - LSMEANS Statement](https://documentation.sas.com/doc/en/pgmsascdc/9.4_3.4/statug/statug_introcom_sect040.htm)
[Least-Squares Means: The R Package lsmeans](https://www.jstatsoft.org/article/view/v069i01) /

Now let's back on the track.
**By applying Wald's, LR (Likelihood Ratio), or Rao testing procedure to an appropriate model you will be (mostly) able to reproduce the same (or pretty equivalent) hypotheses as the ordinary statistical tests do.**

## But why? It's SLOW!
Let's be honest - model-based testing is **MUCH SLOWER** than running a plain good-old test. Just try. When I performed these simulations, at these sample sizes
tests completed calculations immediately, while for the model it took 2-3 seconds. Now add to this calculation of the AME, emmeans, some adjustments for degrees of freedom and you'll get about 5-10 seconds at just few dozens of observations. Now, imagine you run these procedures over a multiply imputed (MICE) dataset.
For just 20 imputed datasets x 5-10 seconds it's **100-200 seconds**. At work, on larger data, with more complex models, not rarely it took **5-10 minutes** to complete. In contrast, ordinary testing in this setting may take **no more than just few seconds**.

## OK, but try doing THIS with classic tests!
Classic tests are "simple" (often - yes), FAST and... did I say simple?
Simple test = simple scenarios.

A more advanced inferential analysis often goes FAR beyond that these tests can do.

- ordinary tests won't allow you to control for covariates. Goodbye more advanced analyses.
- most classic tests cannot handle more complex m x n-way designs. Want to test some outcome across multiple groups rendered by `sex * visit * treatment_arm`? Forget! You will likely need to run multiple simple tests and they won't be able to detect inter-variable relationships. 
- most of the classic tests won't be able to test interactions between multiple factors (a few modern ones, like ATS (ANOVA-Type Statistic) or WTS (Wald-Type Statistic) can do this, but only in a limited scope (1-level interaction between just 2 factors).
- classic tests won't allow you to test simple effects via contrasts, e.g.: "Visit: 3, Arm: Treatment | Male vs Female effect" vs. "Visit 3, Arm: Control | Male vs Female effect". **For the model-based testing it's a piece of cake**.
- you may simply NOT KNOW which test to use! Believe me or not, there are 850+ (EIGHT HUNDRED FIFTY!) statistical tests and counting. A colleague of mine has been counting them for years (with my little support). With a model - you don't care about all these "version", just provide the formula, set some parameters and test the hypotheses you need. Need a trest for trend? Just user ordinal factor for your time variable.
- and you will obtain standard errors and confidence intervals for these comparisons!
- Want to test some hypotheses jointly? Forget with classic tests!
- Want to effectively adjust for multiple testing using parametric exact method employing the estimated effects and covariaces through the multivariate t distribution ("MVT")? This is far better than Bonferroni :-) But forget with simple tests!

Fair enough?
  
## Conclusions
So you can see with your own eyes, that model-based testing has LOTS of advantages. But sometimes you will need just a simple, classic test that runs FAST. Especially, if you have lots of tests to do under multiple imputation conditions, with lengthy data, and you are approaching a deadline :-)

So the choice depends really on your needs.  I only want to show you that this is doable and how well.

Now let's check how good is the approximation by performing some simulations. Nothing will give us better feeling of situaion than that.

---
# Methodoloy
For each test I set up a loop, in which data were sampled (according to some patterns) and the p-values from both ordinary test and its model counterpart were collected.

Then I displayed these p-values in a series of graphs:
1. Test vs. Model to observe how well they follow each other and observe discrepancies
2. Ratios of p-values - the closer to 1 the better (this works well regardless of the magnitude of p-values)
3. Raw differences in p-values. These differences do depend on the magnitude of p-values, and that's exactly what I wanted to see. At very small p-values (p<0.001) I would not care
about differences like p=0.00032 vs. p=0.00017 because both results are far from the typical signfiicance levels (0.1, 0.01, 0.05, 0.01 and also 0.001).
For practical purposes I would care if they were discrepant near 0.05.
4. Comparison of % of cases where Test > Model vs. Test < Model, with additional descriptie statistics of the p-values. Because raw differences depend on the magnitude, it's difficult to observe them on a common scale. Log transformation doesn't help there (0, negative values).
So this approach gives me some feelings about the situation
5. How many times both methods disagreed on the rejection of the null hypothesis and what were the actual p-values.

I repeated the simulations sample sizes:
- n_group = 20 - typical small data size; smaller samples don't make much sense in a reliable research,
- n_group = 30 - the "magical number" so many statisticians find "magical". Of course it's not, but this constitutes a reasonable "lower limit of data"
- n_group = 50 - still small data but in a safer area
- n_group = 100 - typical situation in clinical trials, where I work
- n_group = 200 - above that, even small discrepancies between groups will be reported as statisticall significant, so testing at this data size is dangerous, especially without the practical significance defined!).

Actually, the type-1 error and power is totally off topic in this simjlation, we only check how well the methods follow each other.

---
Definition of a helper function for printing the figures.
I use: dplyr, tidyr, ggplot2, ggrepel, and patchwork

``` r
plot_differences_between_methods <- function(results, sign_level = 0.05, log_axes=FALSE) {
  
  properties <- attr(results, "properties")
  samples <- properties$samples
  n_group <- properties$n_group

results %>% 
  mutate(diff = Test - Model,
         diff_sign = case_when(diff == 0 ~ "Test = Model", diff > 0 ~ "Test > Model", diff < 0 ~ "Test < Model"),
         ratio = Test / Model,
         discr = (Test <= sign_level & Model > sign_level) | (Test > sign_level & Model <= sign_level),
         discr_descr = ifelse(discr, sprintf("T: %.3f ; M :%.3f", Test, Model), NA)) -> results 

(p_rej_discrep <- results %>%
  ggplot(aes(x=iter, y = discr, label=ifelse(discr, discr_descr, NA), col=discr)) +
  geom_point(show.legend = FALSE) +
  ggrepel::geom_label_repel(min.segment.length = 0, seed = 100,
                           box.padding = .4,  segment.linetype = 6,   
                           direction = "both",
                           nudge_y = .1,
                           max.overlaps = 50, size=2.5, show.legend = FALSE) +
  scale_color_manual(values=c("TRUE"="red2", "FALSE"="green2")) +
  scale_y_discrete(labels = c('In agreement','Discrepant')) +
  theme_bw() +
  labs(title = "Discrepancy in rejection: Test vs Model",
      subtitle =  sprintf("N=%d samples, group size=%d observations; sign. level=%.3f", samples, n_group, sign_level)) +
  ylab("p-value Test - p-value Model")
)

(p_pval_ratios <- results %>% 
   ggplot() +
   geom_point(aes(x=iter, y = ratio)) +
   geom_hline(yintercept = 1, col="green") +
   theme_bw() +
   labs(title = "Ratio of p-values: Test vs Model",
        subtitle =  sprintf("N=%d samples, group size=%d observations", samples, n_group)) +
   ylab("p-value Test / p-value Model")
) 

(p_pval_diffs <- results %>% 
  ggplot() +
  geom_point(aes(x=iter, y = diff)) +
  geom_hline(yintercept = 0, col="green") +
  theme_bw() +
  labs(title = "Raw differences in p-values: Test vs Model",
      subtitle =  sprintf("N=%d samples, group size=%d observations", samples, n_group)) +
  ylab("p-value Test - p-value Model")
) 

(p_pval_rel <- results %>% 
   group_by(diff_sign) %>% 
   summarize(n=n(),
             q=list(setNames(quantile(diff), nm=c("Min", "Q1", "Med", "Q3", "Max"))),
             .groups = "drop_last") %>% 
   mutate(p=n/sum(n)) %>% 
   unnest_wider(q) %>% 
   
   ggplot() +
   geom_bar(aes(x = diff_sign, y=p), stat="identity") +
   scale_y_continuous(labels=scales::percent) +
   theme_bw() +
   xlab("Relationship") +
   labs(title = "Relationship between p-values: Test vs Model",
        subtitle =  sprintf("N=%d samples, group size=%d observations", samples, n_group)) +
   geom_label(aes(x = diff_sign, y=.5,
                  label=sprintf("Quartiles of differences\nMin=%.3f\nQ1=%.3f\nMed=%.3f\nQ3=%.3f\nMax=%.3f", Min, Q1, Med, Q3, Max)),
              size=3.5) +
   ylab(NULL)
)

(p_pval_vs <- results %>% 
  ggplot() +
  geom_point(aes(x=Test, y = Model)) +
    
  geom_abline(slope = 1, intercept = 0, col="green") +

  { if(properties$under_null)
       list(geom_hline(yintercept = sign_level, linetype="dashed", color="red"),
            geom_vline(xintercept = sign_level, linetype="dashed", color="red"),
            geom_label(aes(x=0.75, y=0.25, 
                           label = sprintf("Rejections of H0 at α=%.3f\nModel: %d\nTest: %d",
                                           sign_level,
                                           sum(Model <= sign_level),
                                           sum(Test <= sign_level)))))
  } +
  
  { if(log_axes)
      list(scale_y_continuous(trans='log10'),
           scale_x_continuous(trans='log10'),
           xlab("Test log(p-values)"),
           ylab("Model log(p-values)"))
    else
      list(xlab("Test p-values"),
           ylab("Model p-values"))
  } +
    
  theme_bw() +
  labs(title = "P-values: Test vs Model",
       subtitle = sprintf("N=%d samples, group size=%d obs., %s",
                          samples, n_group, 
                          ifelse(properties$under_null, "Under H0", "Under H1")))
)

((p_rej_discrep + p_pval_ratios +
  p_pval_diffs + p_pval_rel +
  patchwork::plot_layout(ncol = 2, nrow = 2)) | p_pval_vs) + 
  plot_layout(widths = c(2, 1))
}
```
---
# Simulations
## Mann-Whitney vs. Proportional-Odds Model (Ordinal Logistic Regression)
### The data
For the ordinal logistic regression I made a dataset consisting of patients' responses to question about the intensity of physical pain they feel, part of the ODI (Oswestry Disability Index). Patients are split into two groups: those taking a new investigated medicine vs. those taking active control (existing standard of care).
So the model is `ODIPain ~ Arm` (arm stands for the treatment arm).

The response was recorded on a 6-level Likert ordinal item (don't confuse with Likert scale, where responses to items are summed together giving a numerical outcome).
I programmed it so in one group dominate low responses and in the other group - high responses.
Example:
```r
set.seed(1000)
lapply(1:100, function(i) {
  stack(
    data.frame(arm1 = sample(0:5, size=100, replace=TRUE, prob = c(20, 10, 5, 2, 2, 2)),
               arm2 = sample(0:5, size=100, replace=TRUE, prob = c(2, 2, 2, 5, 10, 20)))) %>% 
    mutate(values_ord = ordered(values), ind = factor(ind))
}) -> data
```
Although the data size will vary during the simulations, I will visualize only this one case to save space. The stacked bar chart clearly shows that in one group dominate "dark" elements, while in the other group - "bright" ones. The density plots confirm the opposite nature of responses across both arms.

Our comparisons will get more power at increasing sample size, which is an ideal situation as we want to observe both high and low p-values.

``` r
lapply(1:100, function(i) {
  print(i)
  data[[i]] %>% 
    ggplot() +
    geom_bar(aes(x=ind, group=values, fill=values), position = "fill", show.legend = FALSE) +
    theme_void()
}) -> plots1

lapply(1:100, function(i) {
  print(i)
  data[[i]] %>% 
    ggplot() +
    geom_density(aes(x=values, group=ind, fill=ind, col=ind), show.legend = FALSE, alpha=0.6, adjust = 1.5)+
    xlab(NULL) +ylab(NULL) +
    theme_void()
}) -> plots2

(patchwork::wrap_plots(plots1, ncol = 10, nrow = 10) |
    patchwork::wrap_plots(plots2, ncol = 10, nrow = 10)) +
  patchwork::plot_annotation(title = "Patient-reported ODI pain scores across both treatment arms")
```
![obraz](https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/a75158cb-c47e-44d8-8e33-acb98c6534a2)

### Comparison
``` r

simulate_wilcox_olr <- function(samples, n_group, set, arm_1_prob, arm_2_prob) {
  set.seed(1000)
  data.frame( 
    do.call( 
      rbind, 
      lapply(1:samples, function(i) {
        print(i)
        stack(
          data.frame(arm1 = sample(set, size=n_group, replace=TRUE, prob = arm_1_prob),
                     arm2 = sample(set, size=n_group, replace=TRUE, prob = arm_2_prob))) %>% 
          mutate(values_ord = ordered(values), ind = factor(ind)) -> data
        
        c(iter = i,
          n_group = n_group,
          Test  = tidy(wilcox.test(values~ind, data=data, exact = FALSE, adjust = FALSE))$p.value,
          Model = joint_tests(MASS::polr(values_ord ~ ind , data = data, Hess=TRUE))$p.value)
      }))) -> result
  
  attr(result, "properties") <- list(arm_1_prob = arm_1_prob, 
                                     arm_2_prob = arm_2_prob, 
                                     under_null = identical(arm_1_prob, arm_2_prob),
                                     samples = samples,
                                     n_group = n_group)

  return(result)
}
```
### Results
#### 20 observations per group
##### Under H0 - same probabilities of obtating each score across both groups = same ordering of observations
``` r
simulate_wilcox_olr(samples = 100, n_group = 20, set = 0:5, 
                    arm_1_prob =  c(20, 10, 5, 2, 2, 2),
                    arm_2_prob =  c(20, 10, 5, 2, 2, 2)) %>%  
plot_differences_between_methods()
```
![obraz](https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/9cb896a2-4ad7-49e8-a35e-9956a2b5b572)

OK, let's make some notes:
1. look at the most right plot. The p-values from both methods are well aligned to the line of slope = 1, though some bias is visible towards Test p-values.
2. It means, that test was more conservative than model.
3. The fact, that practically all p-values coming a test were larger from the p-values obtained from the model is confirmed also by the bar plot (central-bottom).
4. When we look at the area below the 0.05 significance level, we notice 9 obervations. Remembering we "work under the null" it means, that both methods exceeded the 5% significance level, reaching 9% in this simulation. At this sample size it's not bad for exploratory purposes.
5. What's nice, the tests did not contradict each other. 

##### Under H1 - in one group the probabilities of obtating each score are reversed (as in the figure explaining the data above)
``` r
simulate_wilcox_olr(samples = 100, n_group = 20, set = 0:5, 
                    arm_1_prob =  rev(c(20, 10, 5, 2, 2, 2)),
                    arm_2_prob =  c(20, 10, 5, 2, 2, 2)) %>%  
plot_differences_between_methods()
```
![obraz](https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/9488e056-373a-44b4-a14e-03dc7164e439)

Well well weel! Let's make some notes:
1. Now we went to small and very small p-values, so I log-transformed both axes.
1. There was a clear relationship between the methods, but it's far from a perfect agreement!
2. This time model was giving noticeably larger p-values
3. The fact, that practically all p-values coming a test were larger from the p-values obtained from the model is confirmed also by the bar plot (central-bottom).
4. When we look at the area below the 0.05 significance level, we notice 9 obervations. Remembering we "work under the null" it means, that both methods exceeded the 5% significance level, reaching 9% in this simulation. At this sample size it's not bad for exploratory purposes.
5. What's nice, the tests did not contradict each other. 


