# pymer4

Love mixed-modeling using lme4 in R but prefer to work in the scientific Python ecosystem? Then this package has got you covered! It's a small convenience package wrapping the basic functionality of [lme4](https://github.com/lme4/lme4)\*.  
\* *Currently this only includes linear and logit models*

It's main purpose is to provide a clean interface that hides the back-and-forth code required when moving between R and Python. In other words a user can work completely in Python, never having to deal with R, but get all of lme4 goodness. Behind the scenes this package simply uses [rpy2](https://rpy2.readthedocs.io/en/version_2.8.x/) to pass objects between languages, compute what's needed, parse everything, and convert to native Python types (e.g. numpy arrays, pandas dataframes, etc).

Additionally this package has some extra goodies to make life easier when fitting models (see below).

### Requirements:
You need *both* Python and R on your system to use this package. In addition to the following Python and R packages:
```
pandas>=0.19.1
numpy>=1.12.0
rpy2>=2.8.5
lme4>=1.1.12
lmerTest>=2.0.33
```

Example usage:
```
import pandas as pd
from pymer4.models import Lmer

df = pandas.read_csv('mydata.csv')

#Initialize a model object using plain old R-style formulae
#Because this is just running R behind the scenes, any model specification that lme4 can handle will work here
model = Lmer('DV ~ IV1 (IV1 | Subject)',data=df)

#Fit it and voila! R-style summary statistics
model.fit(method = 'Wald')
```

![](/misc/output.png)


### Model attributes
A pymer4 object contains multiple attributes (accessed via self.attributeName) that try to store the most useful (and most likely to be inspected) information from a lmer call in R. If all you desire is the traditional summary table from R, it lives in model.coefs.  

The full list includes:
- ngrps = number of groups based on random effects structure
- AIC = Akaike information criterion of model fit
- logLike = log-likelihood of model fit
- warnings = fit warning from lme4 or lmerTest
- ranef_var = variance and stdev of random effects
- ranef_corr = correlations between random effects
- fixef = group/cluster/subject level estimates for each fixed effect
- ranef = group/cluster/subject level estimate deviations from the overall model fixed effects
- coefs = fixed effect estimates and inference statistics
- resid = model residuals for each observation
- fits = model predictions for each observation
- data = copy of input data with residuals and fits added as new columns

### Convenience features
- Fitted models include p-value calculations using Satterthwaite approximation via the [lmerTest](https://cran.r-project.org/web/packages/lmerTest/index.html) package.
- All model fits include *confidence intervals* in addition to the typical stuff from R's summary() call
- The model.data attribute contains a copy of the input dataframe but with additional columns for the model *residuals* and model *fits*
- Logit models will automatically have fixed effects conversions for *odds-ratios*, *probabilities*, as well as confidence intervals for both. These will also be saved in the model.coefs attribute

### Coming soon
- Specification of R-style contrast coding schemes on factors
- Corrected pairwise post-hoct tests computed via the lsMeans package
