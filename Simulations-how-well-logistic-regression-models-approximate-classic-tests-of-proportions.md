# Introduction
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
on the conditional distribution of the data (e.g. for the Bernoulli's distribution in the logistic regression the E(Y|X=x) means is probability of success). Second, it depends on the used link function, where
- depending on the used transformation - differences may turn into ratios, and (say) probabilities turn into log-odds. No surprise that the interpretation of model coefficients will complicate (a bit). 

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

Now let's check how good is the approximation by doing some simulations. Nothing will give us better feeling of situaion than that.

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
- n_group = 20 (typical small data size; smaller samples don't make much sense in a reliable research),
- n_group = 50 (small-data but in a safer area)
- n_group = 100 (typical situation in clinical trials, where I work)
- n_group = 200 (above that, even small discrepancies between groups will be reported as statisticall significant, so testing at this data size is dangerous, especially without the practical significance defined!).

Actually, the type-1 error and power is totally off topic in this simjlation, we only check how well the methods follow each other.

---
Definition of a helper function for printing the figures.
I use: dplyr, tidyr, ggplot2, ggrepel, and patchwork

``` r
plot_differences_between_methods <- function(results, sign_level = 0.05) {
  
samples <- length(results$iter)
n_group <- first(results$n_group)

results %>% 
  mutate(diff = Test - Model,
         diff_sign = case_when(diff == 0 ~ "Test = Model", diff > 0 ~ "Test > Model", diff < 0 ~ "Test < Model"),
         ratio = Test / Model,
         discr = (Test <= sign_level & Model > sign_level) | 
           (Test > sign_level & Model <= sign_level),
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
      subtitle =  sprintf("N=%d samples, group size=%d observations; sign. levle=%.3f", samples, n_group, sign_level)) +
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
                  label=sprintf("Min=%.3f\nQ1=%.3f\nMed=%.3f\nQ3=%.3f\nMax=%.3f", Min, Q1, Med, Q3, Max))) +
   ylab("p-value Test - p-value Model")
)

(p_pval_vs <- results %>% 
  ggplot() +
  geom_point(aes(x=Test, y = Model)) +
  scale_y_continuous(trans='log10') +
  scale_x_continuous(trans='log10') +
  geom_abline(slope = 1, intercept = 0, col="green") +
  theme_bw() +
  labs(title = "P-values: Test vs Model",
       subtitle = sprintf("N=%d samples, group size=%d observations", samples, n_group)) +
  xlab("Test log(p-values)") +
  ylab("Model log(p-values)")
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

For the ordinal logistic regression I made a dataset consisting of patients' responses to question about the intensity of physical pain they feel, part of the ODI (Oswestry Disability Index). Patients are split into two groups: those taking a new investigated medicine vs. those taking active control (existing standard of care).
So the model is `ODIPain ~ Arm` (arm stands for the treatment arm).

The response was recorded on a 6-level Likert ordinal item (don't confuse with Likert scale, where responses to items are summed together giving a numerical outcome).
I programmed it so in one group dominate low responses and in the other group - high responses.

