# Logistic-regression-is-regression
Despite the popular yet wrong claim that logistic regression "is not a regression", it's one of the key regression and hypothesis testing tools in epxerimental research (like clinical trials). I will share information from my field to break the bizzare situation, when people from Machine Learning tell me that "what we do every day cannot be done".

# Logistic regression has been a regression since its birth - and is used this way every day

Well, it's kinda... embarrassing for me to write about something that is (should be) obvious to anyone working with statistics but in the last decade has been distorted by hundreds of thousands of members of the #machinelearning community, so today lie replaced the truth... 

I remember the first time, when, during some discussion, I said that
\"I\'ve been using logistic regression for long years on daily basis for
regression and testing hypotheses, but I\'ve never used it for
classification\" and a Data Scientist (with PhD degree) told me, that I
must have been mistaken, because \"*despite its name logistic regression
not a regression algorithm*\". I asked him \"*then tell me, please, what
do I do every day at work?*😉\" he replied \"*I have no idea, but this
sounds a pure nonsense, because logistic regression predicts only two
binary outcomes so you understand it cannot be a regression*\" 🤦

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/d10d14a0-6972-405a-85d9-f0704613b65c"/>
    <p align="center"><em><sup>In such moments I wish there existed a triple facepalm too...</sup></em></p>
</p>

Earlier, people (mostly researchers, statisticians) already were
reporting that a similar situation happened to them during interviews
and internet discussions. I did small research, which results knocked me
off my feet. I \"googled\" for terms like \"*logistic regression is not
(a) regression*\", \"*logistic regression is a misnomer*\" or
\"*logistic regression, despite its name*\". The number of findings was
huge - they occurred everywhere: in articles (Medium, Quora), tutorials
(also issued by companies offering paid courses), blogs, courses, books
(including bestsellers in ML written by people holding PhD), YouTube
videos. I also repeated the search on LinkedIn and found endless flood
of posts repeating this nonsense just copy-pasted from other posts.

Not only that! I asked Chat GPT 3 (then 3.5) and got identical results.
No surprise! It was \"fed\" by garbage, so it learned garbage, and today
it helps spreading garbage to learners, who usually don\'t even suspect
something is wrong, so they trust AI and repeat the nonsense further and
further and\...

\... there is no single week on LinkedIn without someone repeating it,
earning hundreds of 👍 proving that hundreds of people liked (so tens of
thousands saw it) it and will likely repeat the same.

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/5a709edf-bba7-4c0c-bd4e-9669156a4686"/>
</p>

Finally I decided to write a few words about this \"issue\". I write
from the perspective of a clinical biostatistician, working in clinical
trials - part of the pharmaceutical industry responsible for both
existing and new therapies (drugs, procedures, devices) evaluation and
approval. Here the logistic regression is the key regression algorithm,
used to answer questions about treatment efficacy and safety based on
the data from clinical trials with binary endpoints (success/failure).
And yes, that\'s true - I have never used it for classification during
the whole time of my professional work.

# Birth of the logistic regression and the\... Nobel Prize

The origins of the logistic function can be traced back to the 19th
century ([[free
PDF]](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=360300)),
where it was employed in a \"*model of population growth*\". Early
attempts (1930s) to model binary data in the regression manner resulted
in ***probit regression*** model, which constituted a standard for the
next few decades. Researchers found the outcome not much intuitive, so
they searched for a regression model, which coefficients would be easier
to interpret. In already 1944 **Joseph Berkson** started working on the
alternative to the probit model, and the \"logit\" (by analogy to
\"probit\") model was born. Unfortunately, the logit model was rejected
by many as *inferior to the probit model*. It took long years, until the
logit model gained similar \"trust\" (1960-1970), finally refined by
**Sir David Cox** (\"Some procedures connected with the logistic
qualitative response curve\", 1966 and \"[*[The regression analysis of
binary sequences]*](https://www.jstor.org/stable/2983890)\",
1968).

/ BTW, check also other titles of this Great Mind of Statistics!
[[https://www.nuff.ox.ac.uk/Users/Cox/Publications.html]](https://www.nuff.ox.ac.uk/Users/Cox/Publications.html)
/

Almost in parallel with the *multinomial logit model* (Cox, Theil),
which, finally, in 1973, allowed **Daniel McFadden**, a famous
econometrician, to piece together existing puzzles, including the
**Duncan Luce**\'s *choice axiom*, into a whole, which resulted in a
theoretical foundation for the ***logistic regression***. At that time,
McFadden was deeply involved in pioneering work in developing the
theoretical basis for *discrete choice* where he applied the logistic
regression for empirical analysis. His work, making a profound impact on
the analysis of discrete choice problems in economics and other fields,
gave him the Nobel Prize in 2000.

I think we can fairly say that Daniel McFadden\'s work on the logistic
(ordinary and multinomial) regression model and the discrete choice
analysis was truly groundbreaking. It played a significant role in
establishing logistic regression as a solid tool in statistical
analysis, not only in econometrics!

Remember the rejection of the logit model, found inferior to the probit
one? Now the situation reversed, and logistic regression today is the
default approach.

1970s were truly fruitful to logistic regression! In 1972, **Sir John
Nelder** and **Robert Weddeburn** in their seminal work ([[free
PDF]](http://www.medicine.mcgill.ca/epidemiology/hanley/bios601/Likelihood/NelderWedderburn1972.pdf))
introduced the idea of a unified framework: the ***Generalized Linear
Model (GLM)***, enabling regression models to cope with response
variables of any type (counts, categories, continuous), relaxing also
the assumption of normal distribution of errors.

/ Logistic regression is a special case of the GLM. You can spot it
easily when working with R statistical package, when you call the glm()
and specify the family of conditional distribution of the response, here
\"binomial\" with appropriate link, here \"logit\": glm(family =
binomial(link = \"logit\")) /

Just a decade later, two other big names you know for sure, **Trevor
Hastie** and **Robert Tibshirani** extended the Generalized Linear Model
(logistic regression is a special case of it) to the **Generalized
Additive Model**. In their articles (e.g. \"Generalized Additive Models
for Medical Research\",
[[https://doi.org/10.1177/096228029500400]](https://doi.org/10.1177/096228029500400)
) they mention the role of logistic regression in identification and
adjustment for prognostic factors in clinical trials and observational
studies.

/ Did you know that Professor Hastie authored the glm() command in the
S-PLUS statistical suite (the father of R, origin of the R syntax),
estimating the coefficients of the regression models and performing the
inference? /

Additional extensions for handling repeated observations were made by
**Kung-Yee Liang** and **Scott L. Zeger** in 1986 via *Generalized
Estimating Equations* (GEE) and **Breslow, Clayton and others** around
1993, when the theory of *Generalized Linear Mixed Models* (GLMM) was
born.

**I can only imagine McFadden\'s and others\' reaction to the nonsense
\"logistic regression is not a regression\"\...**

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/073f5592-6707-4c78-82f6-4304204656d1"/>
</p>

# Conditional expectation - the key to understand the GLM

Every regression describes a relationship between the predictor and some
function of the conditional response. It can be a quantile,
Qith(Y\|x=x), as in the quantile regression. Or some trimmed estimator
of the expected value, like in the robust regression. Or - the expected
value of the conditional response (=*conditional expectation*) itself,
like in the classic linear regression: **E(Y\|X=x)**.

/ so often confused with one of the estimation algorithms \--\> \"OLS
regression\" - don\'t repeat that. /

Now, it\'s all about the conditional distribution. If it\'s Gaussian
(normal distribution), you obtain the linear regression. But the GLM
allows you to use also other distributions: Bernoulli (or binomial),
gamma, Poisson, negative binomial, etc. The problem is that then the
conditional expectations are not linearly related with the predictor,
which is something we really want. That\'s why we have the **link
function**, linking the conditional expectation and the predictor for a
given conditional distribution: **g(E(Y\|X=x)) = Xb** (sometimes you
will see this formula reversed: E(Y\|X=x) = g-1(Xb). It\'s equivalent
formulation).

Now, the expected values are \"linearized\" with respect to the
predictor. For the ordinary linear regression you don\'t need that, so
the g() is just I() (identity function, which we omit) - the expected
values lay on a straight line, plane, or hyperplane (depending on how
many predictors you have).

/ The name, *conditional expectation*, is also perfectly visible when
you do ANOVA. That\'s just 1:1, perfect example: the levels of
categorical predictor(s) \"form\" sub-distributions, and mean is
calculated in each. Now you also understand what it means: \"**expected
value CONDITIONAL to the predictor**\"! /

Below we can observe various conditional distributions and their means.
The means lay on a straight line transformed by the g() function, **the
link**.

/ OK, I know, the illustration isn\'t perfect, simplifications are made,
but let\'s agree on its imperfection, as long as it shows the main idea,
huh? /

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/a49edd95-5aaf-457d-ae94-d4621dca45a0"/>
    <p align="center"><em><sup>beta regression isn\'t strictly a GLM, but an extension of it</sup></em></p>
</p>

BTW: This is well explained in the book I recommend you to read:

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/180dd744-20cc-45bb-bb78-06d48b3ef458"/>
    <p align="center"><em><sup>Peter H. Westfall, Andrea L. Arias, Understanding Regression Analysis A Conditional Distribution Approach</sup></em></p>
</p>

Now, let\'s answer a few questions:

1.  **Is expected value numerical or categorical?** Of course it\'s
    numerical. It\'s just \"average\". So you instantly see that
    logistic regression CANNOT predict categorical (binary) outcome
    itself. Whatever you\'ve been told - it cannot, and it does not.
    Period.

2.  **What kind of conditional distribution does the logistic regression
    use?** It uses the Bernoulli\'s distribution of a single-trial
    random variable, taking value 1 (for success) with probability p and
    the value 0 (for failure) with probability 1−p.

3.  **What is the expected value for the Bernoulli\'s distribution?**
    It\'s \"p\" - the probability of success.

4.  **So the E(Y\|X=x) is numerical?** Yes, probabilities are numerical.

5.  **Why \"Bernoulli\" if statistical manuals say \"binomial\"?**
    Bernoulli is binomial with k=1. Just a general term.

**I hope you can see from this, that logistic regression, as any other
regression, predicts a numerical outcome, NOT categorical.**

# How is logistic regression turned into a classifier?

The outcome from the logistic regression, the conditional probability
(therefore logistic regression is called also a \"*direct probability
estimator*\") subjected to a conditional rule IF-THEN-ELSE , which
compares it against some threshold (usually 0.5, but this shouldn\'t be
taken as granted!) and returns the category:

**IF (p \< 0.5) THEN A ELSE B**

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/64479263-ebb4-4cb6-b55f-03ed63b4a296"/>
</p>

- *Wait, but this is NOT a regression! This USES the regression prediction instead!*

**Glad you spotted it!**

Too often people do not and just repeat that \"logistic regression
predicts binary outcome\". And when I tell them \"*but what about the
regression term in it, which means that it should predict a numerical
value?*\", they respond \"***Oh! It\'s a misnomer! Despite its name,
logistic regression isn\'t a regression because it it doesn\'t predict
numerical outcome!*\".**

In other words, they do something like this:

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/2dcb4fe2-20a6-40e3-9a07-b8e9b9626292"/>
</p>

\... making a direct jump from binary input to binary output:

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/7ed86276-2b81-410d-9cbe-6782c4b427f5"/>
</p>

But notice, they did NOT CHANGE THE NAME, accordingly. Instead of
calling it \"Logistic Classifier\", the ML community left the name
\"Logistic Regression\". We could say they \"appropriated the logistic
regression\".

Consequently, **they have problems with justifying the existing name**,
so they try:

-   „*Oh! This name is a „misnomer*"

-   „*It\'s called regression because the equation has similar form to
    linear regression*"

-   \"*It must be said that, despite its name, it\'s not a regression*"

Isn\'t this just crazy?

1.  Statisticians invent a regression algorithm (that is, to solve
    regression problems) and use this way for more than half a century

2.  ML specialists find it useful for classification

3.  ML specialists treat it as a classifier

4.  ML specialists do NOT assign appropriate name to the classifier,
    leaving the \"regression\" in it

5.  ML specialists deny that logistic regression is a regression

6.  ML specialists correct statisticians that \"LR is a misnomer and
    despite its name LR is not a regression\"

Despite numerous regression-related problems, where the logistic
regression is used every day, the situation looks like below:

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/c44cf4a7-23df-4def-8ab8-25f221ca1289"/>
</p>

So once in a lifetime, let\'s recall what is the difference between
**logistic regression** and **logistic classifier**:

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/9a5ba5fd-14a2-43ee-8c2d-ede271571571"/>
</p>

# Maybe... But everyone uses logistic regression for classification!

Ah, argumentum ad populum ;\]

OK then:

- First thing: not \"**_everyone_**\*. I understand that ML is a hot topic today (and is here to stay) but it does NOT mean everything revolves around and _nothing else matters_. There are other areas of science and data analysis too. Yeah, really.

-   The fact, that gazillions of people use logistic regression for
    classification purposes doesn\'t remove it\'s regression nature.
    It\'s just one application. It\'s like using a hammer for cooling
    your forehead - you can call it a \"forehead cooler\", but it
    doesn\'t change the fact it\'s still a hammer.

-   You should add \"\... in Machine Learning\". Outside ML, the
    applications are much richer.

-   Believing that there\'s nothing beyond Machine Learning in this
    world doesn\'t change\... the world. And the reality is that
    **experimental research** (including clinical research, physical and
    chemical experiments, sociological and psychological studies,
    quality assessments), where the regression tools are applied to
    binary (and n-ary) endpoints on daily basis, is still the essential
    part of science. So no, it\'s not true that \"everyone uses logistic
    regression for classification\". You should be more specific and
    add: \"*Everyone in Machine Learning*\".

So while I can understand someone saying that \"*in ML, logistic
regression is a classification algorithm*\", I cannot agree that
\"*logistic regression is not a regression*\". A single specific
application, employing also additional steps, and producing a different
(categorized) output does not invalidate the \"core\" engine.

The fact that a tomato can be used to cook a soup (involving many steps)
does not mean that \"tomato is not a fruit - it is a misnomer, because
tomato is a soup ingredient\". It\'s that simple.

# Regression-related applications of the logistic regression (and its friends)

Multiple times I mentioned that logistic regression is used by me and
other statisticians to non-classification, regression tasks. Believe me,
there is NO difference from any other regression!

-   **Assessmen**t = direction and magnitude **of the impact of
    predictors on the response** expressed as: log-odds, odds-ratios, or
    probability (via estimated marginal means or marginal effects \--\>
    for non-identity links)

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/21b4016c-b49f-4414-bd9d-8d3ffa4e9559"/>
</p>

-   For categorical predictors: **inference about the main effects**
    (=ANOVA), optionally adjusted for numerical covariates (=ANCOVA);
    exploration of their **interactions**

-   For categorical predictors: **inference about the simple effects of
    interest**, analysed via planned or ad hoc contrasts; optionally
    adjusted for numerical covariates

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/effa8140-6ab9-4be3-942b-6bbc2c8c5ffd"/>
</p>

-   For numerical or ordinal categorical predictors: **testing for
    trends** (linear, quadratic, cubic, higher) in proportions.
    **Comparisons of trends** between groups.

-   **Replicating the classic statistical tests**: of proportions,
    odd-ratios and stochastic superiority (Wald\'s and Rao z test, chi2,
    Cochran-Armitage, Breslow-Day, Cochran-Mantel-Haenszel, McNemar,
    Cochran Q, Friedman, Mann-Whitney (-Wilcoxon))

-   **Extending the above tests** for multiple variables and their
    interactions, and numerical covariates. Just check the illustration
    below and then [[visit my GitHub for several
    examples]](https://github.com/adrianolszewski/Logistic-regression-is-regression/blob/main/Testing%20hypotheses%20about%20proportions%20using%20logistic%20regression.md):

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/8b0bc028-7a77-44a2-ac78-c5fb4e1127a0"/>
    <p align="center"><em><sup>logistic regression and friends can replicate lots of classic tests!</sup></em></p>
</p>

-   Bonus: the model-based approach ([[check my GitHub for some thoughts
    and
    notes]](https://github.com/adrianolszewski/model-based-testing-hypotheses/blob/main/README.md))
    allows one to employ advanced parametric adjustment for multiple
    comparisons via multivariate t distribution, adjust numerical
    covariates, employ time-varying covariates, account for repeated and
    clustered observations and more!

-   Direct probability estimator used to implement the **inverse
    probability weighting** (IPW) and **propensity score matching**
    algorithms

-   Logistic regression is very useful in the **assessment of the
    Missing-Completely-At-Random (MCAR) pattern** when exploring the
    missing observations!

In my field, clinical trials, I use the logistic regression for:

-   the **assessment of between-arm treatment effect** via comparison of
    the log-odds or the % of clinical success at certain timepoints

-   **non-inferiority, equivalence or superiority testing** *(employs
    clinical significance)* at selected timepoints via appropriately
    defined confidence intervals of difference between %s (average
    marginal effect)

-   the **assessment of the impact of predictors on the clinical
    success** + provide covariate-adjusted EM-means for their main
    effects, interactions and finally their appropriate contrasts

-   the **exploration of interactions** (simple effects), making the
    essential part of my daily work

-   **analysing the over-time within-arm trends of % of successes**,
    e.g. to assess the treatment or some practice persistence.

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/1bc6cc95-deb4-4e2a-9fde-cc0fd6643638"/>
</p>

# Friends of the logistic regression

Logistic regression has many friends that were invented to address
various problems related to regression. Let us enumerate them and
briefly describe:

-   **Binary Logistic Regression -** that\'s our binomial regression
    with logit link, a special case of the Generalized Linear Model,
    modelling the % of successes.

-   **Multinomial Logistic Regression** (MLR) - helpful, when we deal
    with a response consisting of multiple unordered classes (e.g.
    colours).

-   **Nested MLR** - will help us when the classes are \"organized\" in
    groups, related in a hierarchy - thus nested. Imagine that a person
    chooses a mean of transport between **air** {plane} and **road**
    {car, train, bus}. When the road transportation is chosen, then the
    further decision is made only between the three alternatives. It\'s
    similar to multi-level models, where the error terms may present
    some correlation within the same nest, whereas uncorrelated between
    nests. Thank you, **McFadden**, also for this one! [[Read more
    here]](https://cran.r-project.org/web/packages/nestedLogit/vignettes/nestedLogit.html)
    and [[here (Applied Microeconometrix with
    R)]](https://discdown.org/microeconometrics/index.html)
    (or just \"google\" for more).

-   **Ordinal LR** (aka Proportional Odds Model) - allows you to deal
    with 2+ ordered classes, {horrible \< poor \< average \< good \<
    excellent} or {slow \< average \< fast}, {small \< medium \< big}
    and so on. This is the default method of **analysing responses from
    pools and questionnaires** (including Likert items), e.g. . Did you
    know, that the OLR is related with the *Mann-Whitney (-Wilcoxon)
    test*? Use it, if you need a flexible non-parametric test, that: a)
    handles multiple categorical variables, b) adjusts for numerical
    covariates (like ANCOVA). Don\'t hesitate to use it with NUMERICAL
    variables! Yes, you can always do this, the same way you employ
    rank-based methods (e.g. *Conover\'s AN\[C\]OVA*). Read also the
    articles by **Prof. Harrell**, namely: [[Resources for Ordinal
    Regression Models]](https://www.fharrell.com/post/rpo/),
    [[Equivalence of Wilcoxon Statistic and Proportional Odds
    Model]](https://www.fharrell.com/post/powilcoxon/), [[If
    You Like the Wilcoxon Test You Must Like the Proportional Odds
    Model]](https://www.fharrell.com/post/wpo/), and more.

-   **Generalized OLR** - aka Partial Proportional Odds Model is used
    when the proportionality of odds doesn\'t hold. (PS: read
    [[Violation of Proportional Odds is Not
    Fatal]](https://www.fharrell.com/post/po/))

-   **Logistic Quantile Regression** - application similar to the
    above - performs logistic quantile regression for bounded responses,
    like percentages (0-1), school grades, visual analog scales and so
    on. Check [[this
    article]](https://www.amarun.org/images/amarun/materiales/articulos/Galaraza_11_2020.pdf)
    and manuals for
    [[Stata]](https://journals.sagepub.com/doi/pdf/10.1177/1536867X1101100301)
    and [[R
    (lqr)]](https://cran.r-project.org/web/packages/lqr/).

-   **Conditional Logistic Regression - helpful** when we deal with
    stratification and matching groups of data, e.g. in observational
    studies without randomization, to match subjects by some
    characteristics and create homogenous \"baseline\". It can be used
    to reproduce the *Cochran-Mantel-Haenszel test* (via
    *clogit(\...strata)* in R)

-   The **binary logistic regression and its multinomial LR and ordinal
    LR friends can account for dependent responses** (repeated
    measurements, clustered observations) through the *Generalized
    Estimating Equations* (GEE) semi-parametric estimation and the
    *Generalized Linear Mixed Models* (GLMM). No surprise that logistic
    regression is one of the core regression models for longitudinal
    clinical trials with binary endpoints. And no, we do NOT classify
    there anything ;\]

-   **Alternating Logistic Regression** - it\'s a quite rare (and
    forgotten) model, suitable if we deal with correlated observations,
    e.g. when we analyse repeated or clustered data. I mentioned already
    two methods: the mixed-effect LR, and GEE LR. The Alternating LR is
    the 3rd option, which models the dependency between pairs of
    observations by using log odds ratios instead of correlations (which
    is done by GEE). It handles ordinal responses too. There were some
    past implementations in R, but now they are removed from CRAN. [[SAS
    supports it as part of PROC
    GEE]](https://documentation.sas.com/doc/en/pgmsascdc/9.4_3.4/statug/statug_gee_details06.htm).

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/79f802b9-9571-45f2-b69f-fd208ebc0b17"/>
        <p align="center"><em><sup>Logistic regression has many friends</sup></em></p>
</p>

# Literature

I will populate this chapter with textual references later. For now,
find the \"collage\" of covers. And believe, neither of these books will
say that \"logistic regression is not a regression\" :)

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/c0fb2bf5-089a-4a2f-8a98-fb569c7a3589"/>
        <p align="center"><em><sup>NEITHER of these great books will give you nonsenses like "...is not a regression"</sup></em></p>
</p>

\+ recently found an excellent one:

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/3d639108-382c-48aa-b383-e798c1afed07"/>
        <p align="center"><em><sup>Norman Matloff, Statistical Regression and Classification From Linear Models to Machine Learning</sup></em></p>
</p>

Other authors also prove it can be done properly:

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/76b8b6e1-11c7-4299-beb3-5271c52a62b8"/>
        <p align="center"><em><sup>Brett Lantz, Machine Learning with R: Learn How to Use R to Apply Powerful Machine Learning Methods and Gain and Insight into Real-world Applications</sup></em></p>
</p>

## ad hoc comments from my readers

-   *Q: \"Adrian, but in their book, Hastie and Tibshirani put the
    logistic regression in the »classification« chapter!\"*

A: Of course they did! It\'s a book about machine learning, so this kind
of *application* is of interest and highly expectable. **BUT they've
never said it\'s not a regression model. ** They both wrote also a
series of articles on the application of the proportional hazard models
and the logistic regression in biostatistical (they worked in the
division of biostatistics) applications in the regression manner
(assessment of the prognostic factors, assessment of the treatment
effect) and call it a regression model.

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/1b6ce8e0-e8b4-4562-89fe-03ba30d04c75"/>
</p>

Also in the book you mention, on page 121-122 + the following examples
they say: \"*Logistic regression models are used mostly as a data
analysis and inference tool, where the goal is to understand the role of
the input variables in explaining the outcome. Typically many models are
fit in a search for a parsimonious model involving a subset of the
variables, possibly with some interactions terms.*\"

-   *Q: You said that Prof. Hastie authored the glm() function in S. Any
    source?*

*A:*
Here (just for instance):

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/a289534b-6b52-45b6-92bf-1e4955185728"/>
</p>

-   *Q: ChatGPT 3.5 says that logistic regression is not a regression!*

*A:* **ChatGPT will repeat what was trained on**. Don\'t rely on it
strictly when you are learning a new topic, because what you will be
told strongly depends on how you will ask. It was trained on mixed good
and bad resources, so sometimes the *valid one* is \"allowed to speak\"
but just a few questions later it may be messing again. This pertains to
ANY kind of topic, not only in statistics. **DO ALWAYS verify the
responses from any AI-based system if you are going to learn from it,
pass your exams or an interview, or do your job.**

PS: I was told that the newest version of ChatGPT is much better, so
give it a try.

<p align="center">
<img src="https://github.com/adrianolszewski/Logistic-regression-is-regression/assets/95669100/84538581-5251-471b-a979-8256cd854f0a"/>
            <p align="center"><em><sup>ChatGPT 3.5 in action</sup></em></p>
</p>
