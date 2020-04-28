
<p align="center">
<img width="150" align = "center" src="./Graphics/UR_Logo.jpg" > 


</p>


# Residential Real-Estate Spot-Market Pricing Model for Ames, IA.


## Executive Summary.


[Uncertainty Research](https://www.linkedin.com/company/uncertainty-research-llc/about/) (UR) outlines a preliminary capability to identify below-market opportunities to purchase residential properties. A hypothetical Real-Estate Investment Trust (REIT) seeks to enter residential real-estate rental market.  The business model is described in [R. Dizember, L. Kuzisto, *WSJ*, July 21, 2017](https://www.wsj.com/articles/meet-your-new-landlord-wall-street-1500647417), [A. Semuels, *The Atlantic*, February 13, 2019](https://www.theatlantic.com/technology/archive/2019/02/single-family-landlords-wall-street/582394/), and elsewhere.  

Two aspects of this operating model tend to deliver higher profitability than conventional landlords managing individual or small numbers of properties.  First, the funds purchase their properties using cash.  This gives them advantages over would-be owner/occupants dependendent on financing.  Second, they transfer costs to tenents that are conventionally borne by landlords. Tenants, for example, sometimes assume resonsibility for some maintenance costs.

<img width="600" align="left" src="./Graphics/20200116 CONOPS.svg.png" > 


The image to the left illustrates the overall *Concept for Operations*.  REITs' purchasers look for listings that are priced below the prevailing market. The spot-market pricing model provides estimates of the "should-cost" price.  When a below-market price is detected, buyers collect other information and apply it to a total-ownership cost model.  This feeds a decision framework as whether to invest.

This work addresses the initial component for the *Spot-Market Pricing Model* component.  The model considers 77  characteristics of each property.   The discussion below elaborates on technical details.  The prototype predictive model for spot-market pricing accounts for approximately 90% of the variation in sales price in the Ames, IA market.  Prices for *outlier* properties — exemplified by large-footprint properties in less-expensive neighborhoods — are estimated with less accuracy.


## Technical Approach.


<img width="600" align = "right" src="./Graphics/191227 CRISP-DM.svg.png" >

[Uncertainty Research's](https://www.linkedin.com/company/uncertainty-research-llc/about/) (UR's) delivery method is based on the [Cross-Industry Standard Process – Data Mining](http://4.bp.blogspot.com/-0iGdZDGnLks/VDA-7DKV_NI/AAAAAAAAAEI/IqYBNniTlZA/s1600/141004%2BFormal%2BMethods%2BComparison.png) (CRISP–DM).  The figure to the right provides an overview.  This method has provided the foundation for delivery methods used by many leading technology firms, including IBM.  

UR employs CRISP–DM because it contains more-direct coupling to the business context than some more data-centric methods.  It also provides more-explicit guidance on iterativity, an inescapable part of modeling.  The following subsections summarize its application in this work.

### Business Understanding


The figure in the executive summary above summarizes the concept of operations for a hypothetical REIT.  It seeks to find residential properties that are priced below the market.  Based on a total ownership cost model, REIT asset purchasers receive recommendations whether to purchase a given property.  The REIT then becomes an absentee landlord.  It rents the property out to qualified tenants.

Here, we use a prototypical data set from a [well-known kaggle challenge](https://www.kaggle.com/c/house-prices-advanced-regression-techniques).  An actual solution would obtain real-time updates from a site such as [Bright MLS Homes](https://www.brightmlshomes.com/), or possibly [Zillow](https://www.zillow.com/).  

The work summarized here represents the first step in the `Spot-market pricing model` component of the workflow. We prototype here the ability to estimate the *should-cost* price of individual residential properties.  We focus at this stage on point estimates.  We extend this in subseqent work to estimated probability distributions of expected prices. This allows us to specify a below-market-price-probability threshold, at which the REIT might elect to buy.

The `total ownership-cost model` estimates the [net-present-value](https://www.investopedia.com/terms/n/npv.asp) (NPV) of all projected rental income and costs except, for the acquisition costs. This is based on a [discounted cash-flow](https://www.investopedia.com/terms/d/dcf.asp) (DCF) analysis of *expected* costs such as taxes, maintenance, and insurance.  DCF is a foundational practice in financial accounting (e.g., [[Pratt, 2016]](https://amzn.to/2KkjH3c), [[Libby, *et al*, 2019]](https://amzn.to/2VpcqFD), [[Brealey, *et al*, 2020]](https://amzn.to/3ew0wkV)).  

Finally, an automated decision-making framework would provide REIT asset-portfolio managers with recommendations regarding whether to attempt a purchase of a given property. The decision model follows principles from the decision sciences (e.g., [[Kochenderfer, 2015]](https://amzn.to/34RA5BR), [[Skalna, *et al*, 2015]](https://www.springer.com/us/book/9783319264929), [[Howard, 2015]](https://amzn.to/2XS2aYi)).  

For each stage in the foregoing purchasing-decision framework, subsequent work will estimate probability distributions for the influential factors.  These are inferred from the data and fit to a distribution (e.g., [[Keelin, 2016]](https://pubsonline.informs.org/doi/10.1287/deca.2016.0338)).  In some cases they might be elicited from experts through a process resembling that described by [[Spetzler, 1975]](https://pubsonline.informs.org/doi/abs/10.1287/mnsc.22.3.340).  The overall framework is designed using a *probabilistic* approach [[Walsh, 2020]](https://hbr.org/2020/02/develop-a-probabilistic-approach-to-managing-uncertainty).  


### Data Understanding, Perparation.

Our prototypical data set comes from a [well-known kaggle challenge](https://www.kaggle.com/c/house-prices-advanced-regression-techniques).  The figure below summarizes statistics from the data dictionary, included as an appendix to the end of this report.  We begin with a flat table containing 2,051 records with 83 attributes each. 

<img width="1000" align="center" src="./Graphics/DataDictSummy.png" > 

Most-significantly, we begin with numerous incomplete records. Our data-completeness analysis looks across both observation and attribute dimensions.  We see a small nuber of of records for which many features are missing.  We also see some attributes for which pluralities of records lack values.  

Our missing-value handling for this exploratory stage is simple.  We discard the attributes for which large proportions of values are missing. Then we discard observations with null-valued attributes. A more-systematic missing-value-handling procedure awaits subsequent stages of this work.

The analysis also shows the different attribute categories in the data. We have continuous, discrete, and categorical attributes. The discrete attributes are either numeric measurements recorded at integer granularity, or ordinal variables. We do not distinguish for our purposes, here.  The continuous variable `SalePrice` is our response (target) variable.

<img width="350" align="right" src="./Graphics/Continuous Explanatories Correlation Heatmap.png" > 

This quantity of attributes is considerable.  We suffer from the risk of the *curse of dimensionality* [[Hastie, *et al*, 2009, §2.5]](https://web.stanford.edu/~hastie/Papers/ESLII.pdf). This becomes particularly acute considering our the number of categorical attributes.  When we *dummify* — e.g., [[pandas.get_dummies()]](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.get_dummies.html) — the data, the attribute dimensionality becomes multiples greater.  

We seek therefore to pare the attribute space. We pair-wise analyze each explanatory variable's relationship with the target variable `SalesPrice`.  This is accomplished via two methods. We consider the pair-wise correlations for continuous and discrete variables. The heat-map graphic to the right illustrates for continuous variables.  We retain continuous and discrete explanatory variables whose correlation with the response variable exceeds 0.45.  This gives us six continous and five discrete variables.

Our degree-of-influence analysis for categorical variables is somewhat less-direct. We assume that if a categorical variable is a good predictor of a continuous response variable, then the converse should also be true.  So, we construct univariate multinomial logistic-regression models for each categorical variable. We use our `SalesPrice` response variable as the explanatory variable for each such model. We retain categorical variables for which `SalesPrice` predicts their class with accuracy exceeding 0.55. This leaves us with 33 influential categorical variables. After dummifying, we end up with 177 explanatory variables, overall.

Finally, as is always recommended in high-dimensional scenarios, we perform dimensionality analysis of our explanatory-variable set. This provides multiple pieces of useful information.  First, multicollinearity presents difficulties for regression models, in particular (e.g., [[Dielman, 2005, §4.6]](https://amzn.to/2yycLN2), [[Fox, 2008, chap 13]](https://amzn.to/2zhcYot), [[Olive, 2017, §3.8]](https://www.springer.com/us/book/9783319552507)).  Regression models can become unstable in the presence of pronounced multicollinearity.   In general, knowing the effective dimensions explanatory-variable is very useful.

<img width="500" align="left" src="./Graphics/Dummified-Explanatory Sing-Value Spectrum.png" > 

Secondly, we get insight into the smallest number of explanatory variables a model with the lowest-achievable bias can potentially have. The figure to the left depicts results from dimensionality analysis of our 177 explanatory variables. This results from [*singular-value decomposition*](https://en.wikipedia.org/wiki/Singular_value_decomposition) of our explanatory-variable matrix. This tells us how much of the information in our explanatory variables is actually independent (e.g., [[Horn, 1985, §7.3.5]](https://amzn.to/3aoWdEP), [[Golub, 1989, §8.3]](https://amzn.to/2VHuzNT)).  


We find that our 177 explanatory variables are highly dimensionally-dominated. Most of the variance is contained in the first ten dimensions. In fact, only four dimensions contain 99% of the variance. Theoretically, we should be able to reduce our explanatory-variable dimensionality to just a handfull of dimensions and get the same result as using all of them.  We do not attempt this here, however.  

The vertical-axis plot limits truncate the values for approximately the ten smallest singular values. These have quantities on the order of <img src="https://render.githubusercontent.com/render/math?math=\sim10^{-10}">. The *hat matrix* — <img src="https://render.githubusercontent.com/render/math?math=\big(\boldsymbol{X}^T\boldsymbol{X}\big)^{-1}\boldsymbol{X}^T"> — for a regression model would be practically singular in this case.

### Modeling.

<img width="650" align="right" src="./Graphics/191212 Hastie-Tibshirani-Friedman M-L Caps and Lims.svg.png" > 

We consider a diverse variety of models. The figure to the right extends an important summary from [[Hastie, *et al*, 2009, Table 10.1, p. 351]](https://web.stanford.edu/~hastie/Papers/ESLII.pdf). This table groups the family of mainstream statistical-learning methods into five broad categories. The column headers represent the most-general form of each family of methods.  

For example, *Multi-Attribute Regression Splines* (MARS) represents the most-general form of regression modeling, according to this perspective. Ordinary Least-Squares (OLS) regression is arguably a special case of MARS.

The rows in the table contain points of view on the strengths and weaknesses associated with each of the methods. The original table in  [[Hastie, *et al*, 2009]](https://web.stanford.edu/~hastie/Papers/ESLII.pdf) considers nine such factors. The version here has been extended to contain three more. Model evaluation here focuses on predictive power, resistence to overfitting, and conditional probability. 

#### Approaches based on <img src="https://render.githubusercontent.com/render/math?math=\boldsymbol{\beta}^T\boldsymbol{x}_i%2B\beta_0">.

This work attempts to span the families in this table.  We however consider slightly different groupings.  The first group consists of methods that linearly transform explanatory-variable observations according to an operation resembling <img src="https://render.githubusercontent.com/render/math?math=\boldsymbol{\beta}^T\boldsymbol{x}_i%2B\beta_0">.  This class includes regression modeling, support-vector machines, artificial neural networks (ANNs), and kernel methods. 

The first two explicitly use <img src="https://render.githubusercontent.com/render/math?math=\boldsymbol{\beta}^T\boldsymbol{x}_i%2B\beta_0">-type operations in their formulations. ANNs use cascaded <img src="https://render.githubusercontent.com/render/math?math=\phi\big(\boldsymbol{\beta}^T\boldsymbol{x}_i%2B\beta_0\big)"> operations of our linear transform.  The 𝜙 functions are [rectifier linear unit (RELU) operators](https://machinelearningmastery.com/rectified-linear-activation-function-for-deep-learning-neural-networks/). 

The kernel methods — to which the kNN regressor belongs — fit less-well into this grouping.  They employ transforms of the form <img src="https://render.githubusercontent.com/render/math?math=\phi\big(\boldsymbol{\beta}^T\boldsymbol{x}_i\big)">.  The transform vector 𝝱 however is not uniform across all observations.  The kernel methods *scan* over the explanatory-variable space in a method resembling filtering and pooling in convolutional neural networks [[Haykin, 2009, §4.17]](https://amzn.to/3cvvO9J).

#### Approaches based on recursive binary partitions.

<img width="450" align="left" src="./Graphics/Tree Example.png" > 

*Tree*-based methods comprise our second class of methods.  These are distinct in that they do not involve <img src="https://render.githubusercontent.com/render/math?math=\boldsymbol{\beta}^T\boldsymbol{x}_i%2B\beta_0">-type operations.  Alternatively, tree-based methods divide the data using *recursive binary partitioning* ([[Brieman, *et al*, 1984]](https://amzn.to/3ankMSn) [[Hastie, *et al*, 2009, §9.2]](https://web.stanford.edu/~hastie/Papers/ESLII.pdf)). The figure to the left contains an example from [[Brieman, *et al*, 1984, Fig. 2.3]](https://amzn.to/3ankMSn). 

In a curve-fitting context — our case of interest — this produces approximations that are piecewise constant.  The partitioning is accomplished in a way that maximally distinguishes groups being separated according to some statistical score.  Usually, this is the mean-square error in curve-fitting contexts.

Our analysis here considers four variants of tree-based modeling. We first look for the best model for a basic regression tree.  The remaining variants are *ensemble methods*. The first is a *bagging tree*. This simply involves creating a bunch of trees from bootstrap samples of the data. The results of the trees are averaged together.

*Random Forests* extend this smoothing through also randomly selecting from among the feature set [[Hastie, *et al*, 2009, chap 15]](https://web.stanford.edu/~hastie/Papers/ESLII.pdf). Instead of simply bootstrapping our observation set, we also randomly select the features on which partitioning decisions are based. 

*Boosted Trees* "combines the outputs of many 'weak' estimators to produce a powerful 'committee'" [[Hastie, *et al*, 2009, chap 10]](https://web.stanford.edu/~hastie/Papers/ESLII.pdf). We "repeatedly grow shallow trees to the residuals, and hence build up an additive model consisting of the sum of trees" [[Efron, 2016, Chap 17]](https://amzn.to/2KmccsA).  At each stage, weights are applied to each of the training observations.  These weights apply emphasis to the observations for which the greatest error in the previous iterations occured. 

#### Why the distinction between  <img src="https://render.githubusercontent.com/render/math?math=\boldsymbol{\beta}^T\boldsymbol{x}_i%2B\beta_0">-based models and recursive binary partition?

<img width="450" align="right" src="./Graphics/Bayes-net illustration.png" > 

Recursive binary partitioning is paradigmatically distinct from  <img src="https://render.githubusercontent.com/render/math?math=\boldsymbol{\beta}^T\boldsymbol{x}_i%2B\beta_0"> modeling in an important aspect. Specifically, it provides an approach to handling conditional probability (e.g., [[Hamlett, 2019]](https://www.linkedin.com/pulse/statistical-evidence-public-policy-challenging-endeavor-neil-hamlett/)).  Conditional probability is a key emphasis of *Probabilistic Graphical Models* (PGMs) (e.g,  [[Pearl, 1988]](https://amzn.to/2VISAnH)   [[Studený, 2005]](https://www.springer.com/us/book/9781852338916) [[Koller, 2009]](https://amzn.to/3aom53p)). The illustration to the right comes from [[Darwiche, 2009, Fig 4.2]](https://amzn.to/3amjVBy).

Tree methods address conditional probability obliquely.  The recursive binary partitioning breaks observations into groups that are conspicuously distinct based on explanatory-variable values. This often proves a powerful approach to addressing *phenomenological heterogeneity*.  

Alternatively, approaches based on <img src="https://render.githubusercontent.com/render/math?math=\boldsymbol{\beta}^T\boldsymbol{x}_i%2B\beta_0"> implicitly assume *phenomnenological homogeniety*.  Response variables are related to numeric explanatory variables by the same set of slopes in the coefficient vector 𝝱. Distinct categories are handled by translating the intercept.  

Consider for example the instance in which an observation belongs to the <img src="https://render.githubusercontent.com/render/math?math=\nu^{th}"> category.  The indicator-function value for that attribute takes unity.  The effective intercept — all other variables being equal — becomes <img src="https://render.githubusercontent.com/render/math?math=\beta_0%2B\beta_\nu">, and our estimate <img src="https://render.githubusercontent.com/render/math?math=\boldsymbol{\beta}^T\boldsymbol{x}_i%2B\beta_0%2B\beta_\nu">.

*Why might this be problematic?*  Association with the <img src="https://render.githubusercontent.com/render/math?math=\nu^{th}"> category *might mean that we should have a coefficient vector different from* 𝝱. Our <img src="https://render.githubusercontent.com/render/math?math=\boldsymbol{\beta}^T\boldsymbol{x}_i%2B\beta_0">-based methods only however admit to possibility of a single coefficient vector for all observations across all categories. Phenomenological homogeniety is implicit.

Tree-based methods get around this by allowing for distinct partitions.  Our response-variable estimates <img src="https://render.githubusercontent.com/render/math?math=\hat{y}"> the piecewise-constant — the set-member average — for all members of a partition.  These estimates however can take on whatever value is appropriate to the partition-set members. It is unconstrained by values of some coefficient 𝝱, which must be shared by all other obsevations.  However, a piecewise-constant representation of the response curve is the cost of allowing for phenomenological heterogeneity. 

#### Mechanics of modeling.

<img width="750" align="right" src="./Graphics/respnse-variable KDE.png" > 


For each model approach approach we employ the [sklearn](https://scikit-learn.org/stable/) [GridSearchCV](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html) package to perform a search over a judiciously-selected hyperparameter space. For each hyper-parameter gridpoint we calculate five-fold cross-validation. The table in the [Model Evaluation](https://github.com/hamlett-neil-ur/housing_price_model#model-evaluation) section below lists the hyperprameters searched, as well as the values for the best models.

Also, we apply a logarithmic transform to the response variable.  The figure below highlights our motivation.  We see [kernel-density estimates](https://en.wikipedia.org/wiki/Kernel_density_estimation) of our response variable `SalePrice` as given, and of its logarithm.  Each plot contains skew statistics.

We see in the plots that the log-transformation produces a distribution that is both more concentrated and more symmetric. The un-transformed response variable is quite skewed, at 1.56.  It also appears to have a long upper tail.  The log-transformed reponse variable is much less skewed. It appears to have a long lower tale, but much less long than the un-transformed response variable.

Neither distribution passes statistical tests for normality.  Nonetheless, the log-transformed response variable appears to be much closer to normal.  This helps us in particular for linear modeling.  The more-concetrated distributions should also help us with nonparametric models, also.


<img width="750" align="left" src="./Graphics/EdwardsConundrum.png" > 



Some additional manual effort was applied to the linear-regression model.  Specifically, the model performance for that approach appeared to be driven by outliers. The figure to the left illustrates. Two particular observations appeared as conspiuous outliers from the rest, in terms of price and living space. These were in the Edwards neighborhood, for which the price distribution does not coincide with that of the overall market. Effort was applied to identify explanatory-variable attributes that drove the outlier estimates.


### Model Evaluation.

The table below contains summary statistics for the best model from each approach. It reports squared-error and coefficient-of-determination <img src="https://render.githubusercontent.com/render/math?math=R^2"> statistics for each approach.  These statistics are commonly used in analysis-of-variance (ANOVA) analysis for regression modelng (e.g., [[Sahai, 2004]](https://www.springer.com/us/book/9780817632304),  [[Dielman, 2005]](https://amzn.to/3eBjK8L)). It contains results for both training and test data.

<img width="1000" align="center" src="./Graphics/200426 Model ANOVAs.png" > 

Now, model-overfitting is the bain of any statistical modeler's existence. In an optimally-fit model, we look for results in which model scores for the training and test data sets are similar.  If, as often occurs, the model scores for the training data are higher than for test data, overfitting may have occurred. Alternatively, such disparities may represent evidence of heterogeniety in the data.

Two tree-based ensemble methods are highlighted in the table.  These achieved the best performance on the test data.  In particular, the *random-forest regressor* provided the best <img src="https://render.githubusercontent.com/render/math?math=R^2"> statistics. This achieved an <img src="https://render.githubusercontent.com/render/math?math=R^2"> of 0.870 against the training data.  The performance of the *bagging-tree regressor* was approximately equivalent.

Both methods appear to have yielded near-optimum fits.  The <img src="https://render.githubusercontent.com/render/math?math=R^2"> scores anfor the training and test data coincide very closely. The other ensemble method, the *ada-boosted tree regressor* provides performance that is not far behind.  None of these methods leads to significant overfitting. These methods appear to have *smoothed* out the variance. That is the motivation for ensemble methods.  

Somewhat surprisingly, the ridge-regression provided the strongest against the training data set <img src="https://render.githubusercontent.com/render/math?math=R^2\approx0.887">. This model is moderately overfit, yielding a trainnig score <img src="https://render.githubusercontent.com/render/math?math=R^2\approx0.855">.  It seems an optimally-fit linear model might yield results that coincide quite closely with the best of the tree-based ensemble methods, above.

Now, the table from [[Hastie, *et al*, 2009, Table 10.1, p. 351]](https://web.stanford.edu/~hastie/Papers/ESLII.pdf) in the [Modeling](https://github.com/hamlett-neil-ur/housing_price_model#modeling) introductory section above leads us to expect good results from Tree-based methods.  It also leads to expect strong from the ANN and the kNN models, also.  Results from the latter two are less-strong.  These disparities merit further subsequent investigation.

#### What do we believe is happening? 

<img width="750" align="left" src="./Graphics/Bagg-Tree Resp Resid Plots.png" > 

The figures to the left contain the residual and response plots for test and training estimates produced by the bagging-tree regressor. [[Olive, 2017]](https://www.springer.com/us/book/9783319552507) recommends this visualization.  The plots also contain [Locally-Weighted Scatterplot Smoothing](https://www.epa.gov/sites/production/files/2016-07/documents/loess-lowess.pdf) (LOWESS) curves, also recommended in [[Olive, 2017]](https://www.springer.com/us/book/9783319552507).

First, the LOWESS curves do not perfectly coincide with the red-colored <img src="https://render.githubusercontent.com/render/math?math=y_i=\hat{y_i}"> or the <img src="https://render.githubusercontent.com/render/math?math=\epsilon_i=0"> curves. This tells us that the model fails to capture some of the structure in the data. It contains some bias.

Specifically, our model overestimates the price of more-expensive properties.  Now, square-foot living space is an often-used basis for estimating house price. It is well-known among realtors however that price per square foot falls off for larger properties. Perhaps subsequent analysis will reveal that square footage is accorded too much influence.

<img width="750" align="left" src="./Graphics/Resp_resid_train_test.png" > 

Second, the some outliers are occurring.  These are evident when for example <img src="https://render.githubusercontent.com/render/math?math=\epsilon_i"> points are significantly off of the <img src="https://render.githubusercontent.com/render/math?math=\epsilon_i=0"> curve. These instances appear to drive our coefficient of determination <img src="https://render.githubusercontent.com/render/math?math=R^2"> scores. 

Now, our second set of response and residual plots depict results for the linear-regression model.  This is the default, against which we often compare other results. This model only performs marginally worse. Its outliers — <img src="https://render.githubusercontent.com/render/math?math=\epsilon_i"> points are significantly off of the <img src="https://render.githubusercontent.com/render/math?math=\epsilon_i=0"> curve — appear however to be marginally more acute. 

Our ensemble tree-based models do perform marginally better than the linear model.  This cursorily seems to support our hypothesis that the tree models handle conditional-probabilities — manifested here as outliers — a little bit better. Moreover, ensembling smooths out variance, which apparently mitigates overfitting. Further analysis is needed.

Finally, many of our models consistently perform better against training data than against test data.    Additional analysis is needed to understand other cases.  To the extent that the sufficient statistics (e.g., [[Cox, 1974]](https://amzn.to/34QsMKx)) for the training data are distinct from test data, model-score differences are attributable to heterogeneity.  We could compare the two data sets attribute-by-attribute using statistical tests such as the [Kolmogorov-Smirnov test](https://en.wikipedia.org/wiki/Kolmogorov%E2%80%93Smirnov_test) to ascertain whether this occurs. That our tree-based ensemble methods appear optimally fit however inclines us to suspect overfitting by some of the other model approaches. 


<img width="750" align="right" src="./Graphics/Bivariate price distributions.png" > 

***What about bias in our models?***  The figure to the right gives us some clues. Bias, again, occurs when our model fails to capture the phenomenology. In our <img src="https://render.githubusercontent.com/render/math?math=\boldsymbol{\beta}^T\boldsymbol{x}_i%2B\beta_0">-based models, this occurs when the relationships between an explanatory variable and the response variable are nonlinear. These plots show bivariate relationships between `GrLivArea` living space and `YearBuilt` — two of our explanatory variables with the strongest correlation with the response variable — and `SalePrice`.

These visualizations contain two representations superimposed upon each other.  The contour plots are bivariate kernel-density estimates [[Hastie, *et al*, 2009, §6.6]](https://web.stanford.edu/~hastie/Papers/ESLII.pdf). The superimposed curves are [Locally-Weighted Scatterplot Smoothing](https://www.statisticshowto.com/lowess-smoothing/) (LOWESS).  These are two-dimensional *slices* through the data.

Apparently, the relationship between size and price is approximately linear, but the relationship between age and price is not. The latter is a candidate source of bias. The size-versus price relationship is somewhat surprising.  Realtors advise would-be sellers against extrapolating price-per-square-foot from smaller properties in their markets. This might be less-precarious in Ames, IA than perhaps other markets.

We would expect that our tree-based methods would be more likely to overcome the age-versus-price nonlinearity than the <img src="https://render.githubusercontent.com/render/math?math=\boldsymbol{\beta}^T\boldsymbol{x}_i%2B\beta_0">-based models. Perhaps this is not a key driver of the bias we see in the response or scatter plots above.  It is difficult to tell whether when an where this drives our tree algorithms to split along this dimension.


## Further directions.  

This work is still in a preliminary stage. We demonstrate here the ability to establish a should-cost market price with a reasonable degree of accuracy. The opportunity appears to exist for improvement.


First, our original use case asks for distributions for response-variable estimates as opposed to point estimates we presently get.  This allows to assert with an estimated probability that a listed price falls below the market price by a predetermined threshold. Included among the approaches are metalog distribytions by [[Keelin, 2016]](https://pubsonline.informs.org/doi/10.1287/deca.2016.0338).  Some of the illustrative literature for [Multi-Attribute Regression Splines](https://github.com/scikit-learn-contrib/py-earth) (MARS) demonstrates its use for residual analysis.

Also, we can add additional data to the model.  Our protytpical data set spans the period from 2006 to 2010.  This of course includes the period including the runup to and aftermath from the late-2000s financial crisis. In general, prices change year-on-year. Things were occurring during that period with a high degree of volatility.

The U.S. [Federal Housing Finance Agency](https://www.fhfa.gov/) publishes a [house-price index](https://www.fhfa.gov/DataTools/Downloads/Pages/House-Price-Index.aspx). This shows trends in housing prices, both upward and downward.  Were we to scale our `SalePrice` response variable to this index, we could better account for this variability.

Other explanatory variables may also instructive. During the 2000s real-estate bubble, [*Economist*](https://www.economist.com/) magazine  periodically published its own [house-price index](https://infographics.economist.com/2017/HPI/index.html). This model was based on the premise that purchase prices are influenced by rent prices and household incomes within a geographic market.  This motivates the incorporation of these attributes into our model.  The U.S. [Bureau of Labor Statistics](https://www.bls.gov/) publishes these data. 




## Appendix — Data Dictionary.

The data dictionary is an operational component of the code. A template was produced using pandas.DataFrame methods.  The template was exported to a csv file.  This file was manually edited.  A "populated" data dictionary was read back into the python environment.  The contents of the "notes" and "disposition" columns were then used by the logic to handle and prepare the data for modeling.



|  attribute  |non_null_records| type  |distinct_values|                                                                      Notes                                                                       |                          Disposition                          | cat_vars  |
|-------------|---------------:|-------|--------------:|--------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------|-----------|
|1stFlrSF     |            2051|int64  |            915|First Floor square feet. Numeric at integer precision.                                                                                            |Use as provided                                                |Continuous |
|2ndFlrSF     |            2051|int64  |            504|Second floor square feet. Numeric at integer precision.                                                                                           |Use as provided                                                |Continuous |
|3SsnPorch    |            2051|int64  |             22|Three season porch area in square feet. Numeric.                                                                                                  |Use as provided                                                |Continuous |
|Alley        |            2051|object |              3|Alley access to property.  String-categorical.  Category “NA” misinterpreted as Null.                                                             |Use as provided                                                |Categorical|
|BedroomAbvGr |            2051|int64  |              8|Bedrooms above grade (does NOT include basement bedrooms). Discrete numeric.                                                                      |Use as provided                                                |Discrete   |
|BldgType     |            2051|object |              5|Type of dwelling. Categorical string.                                                                                                             |Use as provided                                                |Categorical|
|BsmtCond     |            2050|object |              7|Evaluates the general condition of the basement. String categorical. NA (⇒ “No Basement”) misinterpreted as NULL; empty values also present.      |Assign null values to “NA”                                     |Categorical|
|BsmtExposure |            2047|object |              6|Refers to walkout or garden level walls. String categorical. NA (⇒ “No Basement”) misinterpreted as NULL; empty values also present.              |Assign null values to “NA”                                     |Categorical|
|BsmtFullBath |            2049|float64|              6|Basement full bathrooms. Discrete numeric count. Missing values ≟ 0.                                                                              |Assign null values to 0                                        |Discrete   |
|BsmtHalfBath |            2049|float64|              5|Basement half bathrooms. Discrete numeric count.  Missing values ≟ 0.                                                                             |Assign null values to 0                                        |Discrete   |
|BsmtQual     |            2050|object |              7| Evaluates the height of the basement. String categorical. NA (⇒ “No Basement”) misinterpreted as NULL; empty values also present.                |Assign null values to “NA”                                     |Categorical|
|BsmtUnfSF    |            2050|float64|            968|Unfinished square feet of basement area. Numeric.                                                                                                 |Assign null values to 0                                        |Continuous |
|BsmtFinSF1   |            2050|float64|            822|Type 1 finished square feet. Numeric.                                                                                                             |Assign null values to 0                                        |Continuous |
|BsmtFinSF2   |            2050|float64|            206|Type 2 finished square feet. Numeric.                                                                                                             |Assign null values to 0                                        |Continuous |
|BsmtFinType1 |            2050|object |              8|Rating of basement finished area. String categorical.  NA (⇒ “No Basement”) misinterpreted as NULL; empty values also present.                    |Assign null values to “NA”                                     |Categorical|
|BsmtFinType2 |            2049|object |              8|Rating of basement finished area (if multiple types). String categorical.  NA (⇒ “No Basement”) misinterpreted as NULL; empty values also present.|Assign null values to “NA”                                     |Categorical|
|CentralAir   |            2051|object |              2|Central air conditioning. String binary categorical.                                                                                              |Use as provided                                                |Categorical|
|Condition1   |            2051|object |              9|Proximity to various conditions, transportation services, in particular. Nine categorical strings.                                                |Use as provided                                                |Categorical|
|Condition2   |            2051|object |              8|Proximity to various conditions (if more than one is present). Eight categorical strings.                                                         |Use as provided                                                |Categorical|
|Selldate     |            2051|Integer|             55|Date of sale.  Concatenation of Yr_sold, Mo_Sold.                                                                                                 |Use as provided                                                |Primary_key|
|Electrical   |            2051|object |              5|Electrical system. String categorical.                                                                                                            |Use as provided                                                |Categorical|
|EnclosedPorch|            2051|int64  |            159|Enclosed porch area in square feet. Numeric.                                                                                                      |Use as provided                                                |Continuous |
|ExterCond    |            2051|object |              5|Evaluates the present condition of the material on the exterior. String categorical.                                                              |Use as provided                                                |Categorical|
|ExterQual    |            2051|object |              4|Evaluates the quality of the material on the exterior. String categorical.                                                                        |Use as provided                                                |Categorical|
|Exterior1st  |            2051|object |             15|Exterior covering on house. String Categorical.                                                                                                   |Use as provided                                                |Categorical|
|Exterior2nd  |            2051|object |             15|Exterior covering on house (if more than one material). String Categorical.                                                                       |Use as provided                                                |Categorical|
|Fence        |            2051|object |              5|Fence quality. String categorical. NA (⇒ “No Fence”) misinterpreted as NULL.                                                                      |Use as provided                                                |Categorical|
|FireplaceQu  |            2051|object |              6|Fireplace quality. String categorical.  NA (⇒ “No Fireplace”) misinterpreted as NULL.                                                             |Use as provided                                                |Categorical|
|Fireplaces   |            2051|int64  |              5|Number of fireplace. Discrete numeric count.                                                                                                      |Use as provided                                                |Discrete   |
|Foundation   |            2051|object |              6|Type of foundation. String categorical.                                                                                                           |Use as provided                                                |Categorical|
|FullBath     |            2051|int64  |              5|Full bathrooms above grade. Discrete numeric count.                                                                                               |Use as provided                                                |Discrete   |
|Functional   |            2051|object |              8|Home functionality (Assume typical unless deductions are warranted). String categorical.                                                          |Use as provided                                                |Categorical|
|GarageArea   |            2050|float64|            515|Size of garage in square feet. Continuous numeric at integer precision. Empty for Garage_Type = “NA”.                                             |Assign null values to 0                                        |Continuous |
|GarageCars   |            2050|float64|              7|Size of garage in car capacity. Discrete numeric count. Missing values ≟ 0.                                                                       |Use as provided                                                |Discrete   |
|GarageCond   |            2050|object |              7|Garage condition. String categorical.  NA (⇒ “No Garage”) misinterpreted as NULL; Missing value for non-NA garage.                                |Discard records:  null-valued  for non-NA garage               |Categorical|
|GarageFinish |            2050|object |              5|Interior finish of the garage. String categorical.  NA (⇒ “No Garage”) misinterpreted as NULL.                                                    |Use as provided                                                |Categorical|
|GarageQual   |            2050|object |              7|Garage quality. String categorical.    NA (⇒ “No Garage”) misinterpreted as NULL; Missing value for non-NA garage.                                |Discard records:  null-valued records for non-NA garage        |Categorical|
|GarageType   |            2051|object |              7|Garage location. String categorical.   NA (⇒ “No Garage”) misinterpreted as NULL.                                                                 |Use as provided                                                |Categorical|
|GarageYrBlt  |            1937|float64|            214|Year garage was built. Numeric at discrete-year resolution. Empty for Garage_Type = “NA”.                                                         |Discard attribute. Highly correlated with Year_built attribute.|Discrete   |
|GrLivArea    |            2051|int64  |           1053|Above grade (ground) living area square feet. Numeric at integer precision.                                                                       |Use as provided                                                |Continuous |
|HalfBath     |            2051|int64  |              3|Half baths above grade. Discrete numeric count.                                                                                                   |Use as provided                                                |Discrete   |
|Heating      |            2051|object |              5|Type of heating. String categorical.                                                                                                              |Use as provided                                                |Categorical|
|HeatingQC    |            2051|object |              5|Type of heating. String categorical.                                                                                                              |Use as provided                                                |Categorical|
|HouseStyle   |            2051|object |              8|Style of dwelling. Categorical string                                                                                                             |Use as provided                                                |Categorical|
|Id           |            2051|int64  |           2051|Unique ID. Integer                                                                                                                                |Use as provided                                                |Primary_key|
|KitchenAbvGr |            2051|int64  |              4|Kitchens above grade. Discrete numeric count.                                                                                                     |Use as provided                                                |Discrete   |
|KitchenQual  |            2051|object |              4|Kitchen quality. String categorical.                                                                                                              |Use as provided                                                |Categorical|
|LandContour  |            2051|object |              4|Flatness of the property. Four string Categorical                                                                                                 |Use as provided                                                |Categorical|
|LandSlope    |            2051|object |              3|Slope of property. Three categorical strings.                                                                                                     |Use as provided                                                |Categorical|
|LotArea      |            2051|int64  |           1476|Lot size in square feet. Numeric continuous at integer precision.                                                                                 |Use as provided                                                |Continuous |
|LotConfig    |            2051|object |              5|Lot configuration. Five string categorical.                                                                                                       |Use as provided                                                |Categorical|
|LotFrontage  |            1721|float64|            448|Linear feet of street connected to property. Numeric continuous No obvious relationship to other attributes.                                      |Discard attribute.                                             |Continuous |
|LotShape     |            2051|object |              4|General shape of property. Four string Categorical.                                                                                               |Use as provided                                                |Categorical|
|LowQualFinSF |            2051|int64  |             31|Low quality finished square feet (all floors). Numeric at integer precision.                                                                      |Use as provided                                                |Continuous |
|MasVnrArea   |            2029|float64|            395|Masonry veneer area in square feet.  Some empty values. Numeric continuous.  Empty ≟ 0.                                                           |Discard records:  null-valued records.                         |Continuous |
|MasVnrType   |            2029|object |              5|Masonry veneer type. Five categorical values. Some empty values.  Empty ≟ “None”                                                                  |Discard records:  null-valued records.                         |Categorical|
|MiscFeature  |            2051|object |              6|Miscellaneous feature not covered in other categories. String categorical.  NA (⇒ “None”) misinterpreted as NULL.                                 |Use as provided                                                |Categorical|
|MiscVal      |            2051|int64  |             28|Value of miscellaneous feature. Numeric.                                                                                                          |Use as provided                                                |Continuous |
|MSSubClass   |            2051|int64  |             16|Identifies the type of dwelling involved in the sale. Numeric categorical (non-ordinal). Sixteen categories, all of which represented in data.    |Use as provided                                                |Categorical|
|MSZoning     |            2051|object |              7|Identifies the general zoning classification of the sale. String categorical. Seven of eight possible values represented.                         |Use as provided                                                |Categorical|
|Neighborhood |            2051|object |             28|Physical locations within Ames city limits. 28 categorical strings.                                                                               |Use as provided                                                |Categorical|
|OpenPorchSF  |            2051|int64  |            223|Open porch area in square feet. Numeric.                                                                                                          |Use as provided                                                |Continuous |
|OverallCond  |            2051|int64  |              9|Rates the overall condition of the house. Categorical Integer.                                                                                    |Use as provided                                                |Categorical|
|OverallQual  |            2051|int64  |             10|Rates the overall material and finish of the house. Categorical Integer.                                                                          |Use as provided                                                |Categorical|
|PavedDrive   |            2051|object |              3|Paved driveway. String categorical.                                                                                                               |Use as provided                                                |Categorical|
|PID          |            2051|int64  |           2051|Unique ID. Integer.                                                                                                                               |Use as provided                                                |Primary_key|
|PoolArea     |            2051|int64  |             10|Pool area in square feet. Numeric.                                                                                                                |Use as provided                                                |Continuous |
|PoolQC       |            2051|object |              5|Pool quality. String categorical.   NA (⇒ “No Pool”) misinterpreted as NULL; Missing value for non-NA garage.                                     |Use as provided                                                |Categorical|
|RoofMatl     |            2051|object |              6|Roof material. String Categorical.                                                                                                                |Use as provided                                                |Categorical|
|RoofStyle    |            2051|object |              6|Type of roof. String Categorical.                                                                                                                 |Use as provided                                                |Categorical|
|SalePrice    |            2051|object |              9|Type of sale. String categorical.                                                                                                                 |Use as provided                                                |Categorical|
|SaleType     |            2051|int64  |            828|Type of sale. Target Variable.                                                                                                                    |Use as provided                                                |           |
|ScreenPorch  |            2051|int64  |            100|Screen porch area in square feet. Numeric.                                                                                                        |Use as provided                                                |Continuous |
|Street       |            2051|object |              2|Type of road access to property. Two-string categorical                                                                                           |Use as provided                                                |Categorical|
|TotalBsmtSF  |            2050|float64|            893|Total square feet of basement area. Numeric                                                                                                       |Assign null values to 0                                        |Continuous |
|TotRmsAbvGrd |            2051|int64  |             14|Total rooms above grade (does not include bathrooms). Discrete numeric count.                                                                     |Use as provided                                                |Discrete   |
|Utilities    |            2051|object |              3|Type of utilities available. Four string categorical, of which three are present. No 'ELO' isntances                                              |Use as provided                                                |Categorical|
|WoodDeckSF   |            2051|int64  |            322|Wood deck area in square feet. Numeric.                                                                                                           |Use as provided                                                |Continuous |
|YearBuilt    |            2051|int64  |            113|Original construction date. Numeric measurement at integer precision.                                                                             |Use as provided                                                |Continuous |
|YearRemodAdd |            2051|int64  |             61|Remodel date (same as construction date if no remodeling or additions).  Numeric measurement at integer precision.                                |Use as provided                                                |Continuous |


___
