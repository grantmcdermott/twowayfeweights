
# twowayfeweights
Estimates the weights attached to the two-way fixed effects regressions studied in de Chaisemartin &amp; D'Haultfoeuille (2020a), as well as summary measures of these regressions' robustness to heterogeneous treatment effects.

## Setup 
```s
ssc install twowayfeweights, replace
```

or from GitHub

```s
net install twowayfeweights, from("https://raw.githubusercontent.com/chaisemartinPackages/twowayfeweights/main") replace
```
## Syntax

```s
twowayfeweights Y G T D, type(string)
  [summary_measures test_random_weights(varlist)
  controls(varlist) other_treatments(varlist) weight(varlist) path(string)]
```
## Description
+ **Y** is the dependent variable in the regression. **Y** is the level of the outcome if one wants to estimate the weights attached to the fixed-effects regression, and **Y** is the first difference of the outcome if one wants to estimate the weights attached to the first-difference regression.
+ **G** is a variable identifying each group.
+ **T** is a variable identifying each period.
+ **D** is the treatment variable in the regression. **D** is the level of the treatment if one wants to estimate the weights attached to the fixed-effects regression, and **D** is the first difference of the treatment if one wants to estimate the weights attached to the first-difference regression.
  
### Options 

+ **type** is a required option that can take four values: *feTR*, *feS*, *fdTR*, *fdS*. If *feTR* is specified, the command estimates the weights and sensitivity measures attached to the fixed-effects regression under the common trends assumption. With *feS*, it estimates the weights and sensitivity measures attached to the fixed-effects regression under common trends and the assumption that groups' treatment effect does not change over time. With *fdTR*, it estimates the weights and sensitivity measures attached to the first-difference regression under the common trends assumption. Finally, with *fdS* it estimates the weights and sensitivity measures attached to the first-difference regression under common trends and the assumption that groups' treatment effect does not change over time.
+ **summary_measures** displays complementary results from the computation of the weights. Specifically, the option outputs: (*i*) the point estimate of the coefficient on the **D** variable from a TWFE regression, (*ii*) the minimum value of the standard deviation of the ATEs compatible with the coefficient from the TWFE regression and ATE across all treated (g,t) cells being equal to zero, (*iii*) the minimum value of the standard deviation of the ATEs compatible with the coefficient from the TWFE regression and ATE across all treated (g,t) cells having different signs (this is computed only if the sum of negative weights is different from 0). See the FAQ section for other details.
+ **test_random_weights** when this option is specified, the command estimates the correlation between each variable in *varlist* and the weights. Testing if those correlations significantly differ
from zero is a way to assess whether the weights are as good as randomly assigned to groups and time periods. When **other_treatments** is specified, the command only reports the correlation between each variable and the weights attached to the main treatment, not the correlations between each variable and the contamination weights attached to the other treatments.
+ **controls** is a list of control variables that are included in the regression. Controls should not vary within each group x period cell, because the results in in de Chaisemartin & D'Haultfoeuille (2020a)
apply to two-way fixed effects regressions with group x period level controls. If a control does vary within a group x period cell, the command will replace it by its average value within each group x period cell.
+ **other_treatments** is a list of other treatment variables that are included in the regression. While the results in de Chaisemartin & D'Haultfoeuille (2020a) do not cover two-way fixed effects regressions with several treatments, those in de Chaisemartin & D'Haultfoeuille (2020b) do, so the command follows results from that second paper when **other_treatments** is specified. This option can only be used when **type(*feTR*)** is specified. When it is specified, the command reports the number and sum of positive and negative weights attached to the treatment, but it does not report the summary measures of the regression's robustness to heterogeneous treatment effects, as these summary measures are no longer applicable when the regression has several treatment variables. The command also reports the weights attached to the other treatments. The weights reported by the command are those in Corollary 1 in de Chaisemartin & D'Haultfoeuille (2020b). See de Chaisemartin & D'Haultfoeuille (2020b) for further details.
+ **weight**: if the regression is weighted, the weight variable can be specified in **weight**. If **type(*fdTR*)** is specified, then the weight variable should be non-missing at the first period when a group appears in the data (e.g. at t=1 for the groups that are in the data from the beginning), and for all observations for which the first-difference of the group-level mean outcome and treatment are non missing.
+ **path** allows the user to specify a path (e.g D:\FolderName\project.dta) where a .dta file containing 3 variables (Group, Time, Weight) will be saved. This option allows the user to see the weight attached to each group x time cell. If the **other_treatments** option is specified, the weights attached to the other treatments are also saved in the .dta file.

## FAQ


#### *How can one interpret the summary measures of the regression's robustness to heterogeneous treatment effects?*
When the two-way fixed effects regression has only one treatment variable, the command reports two summary measures of the robustness of the treatment coefficient beta to treatment heterogeneity across groups and over time. The first one is defined in point (i) of Corollary 1 in de Chaisemartin & D'Haultfoeuille (2020a). It corresponds to the minimal value of the standard deviation of the treatment effect across the treated groups and time periods under which beta and the average treatment effect on the treated (ATT) could be of opposite signs. When that number is large, this means that beta and the ATT can only be of opposite signs if there is a lot of treatment effect heterogeneity across groups and time periods. When that number is low, this means that beta and the ATT can be of opposite signs even if there is not a lot of treatment effect heterogeneity across groups and time periods. The second summary measure is defined in point (ii) of Corollary 1 in de Chaisemartin & D'Haultfoeuille (2020a). It corresponds to the minimal value of the standard deviation of the treatment effect across the treated groups and time periods under which beta could be of a different sign than the treatment effect in all the treated group and time periods.

#### *How can I tell if the first summary measure is high or low?*
Assume that the first summary measure is equal to x. How can you tell if x is a low or a high amount of treatment effect heterogeneity? This is not an easy question to answer, but here is one possibility.  Let us assume that the treatment effects of (g,t) cells are drawn from a uniform distribution. Then, to have that the mean of that distribution is 0 while its standard deviation is x, the treatment effects should be uniformly distributed on the [-sqrt(3)x,sqrt(3)x] interval. Then, you can ask yourself: is it reasonable to assume that some (g,t) cells have a treatment effect as large as sqrt(3)x, while other cells have a treatment effect as low as -sqrt(3)x? If the answer is negative, that is, you think that it is not reasonable to assume that the treatment effect will exceed the +/-sqrt(3)x bounds for some (g,t) cells, this means that the uniform distribution of treatment effects compatible with an ATT of 0 and a standard deviation of x seems implausible to you. Then, you can consider that the command's first summary measure is high, and that it is unlikely that beta and the ATT are of a different sign. Conversely, if the answer is positive, that is, you believe that the treatment effect might exceed the bounds for some (g,t) cells, it may not be unlikely that beta and the ATT are of a different sign. 

The previous sensitivity exercise assumes that treatment effects follow a uniform distribution. You may find it more reasonable to assume that they are, say, normally distributed. Then you can conduct the following, similar exercise. If the treatment effects of (g,t) cells are drawn from a normal distribution with mean 0 and standard deviation x normal distribution, 95% of them will fall within the [-1.96x,1.96x] interval, and 5% will fall outside of that interval. Then, you can ask yourself: is it reasonable to assume that 2.5% of (g,t) cells have a treatment effect larger than 1.96x, while 2.5% have a treatment effect lower than -1.96? If the answer is negative, that is, you are willing to assume that at most 2.5% of the treatment effects fall out of the [-1.96x,1.96x] interval at each side, this means that the normal distribution of treatment effects compatible with an ATT of 0 and a standard deviation of x seems implausible to you. Then, you can consider that the command's first summary measure is high, and that it is unlikely that beta and the ATT are of a different sign.

#### *How can I tell if the second summary measure is high or low?*
Assume that the second summary measure is equal to x. To fix ideas, let us assume that beta>0. Let us assume that the treatment effects of (g,t) cells are drawn from a uniform distribution.  Then, one could have that those effects are all negative, with a standard deviation equal to x, for instance if they are uniformly drawn from the [-2sqrt(3)x,0]. Then, you can ask yourself: is it reasonable to assume that some (g,t) cells have a treatment effect as low as -2sqrt(3)x? If the answer is negative, that is, you are not willing to assume that some (g,t) cells have a treatment effect lower than -2sqrt(3)x, this means that the uniform distribution of treatment effects compatible with sign reversal and a standard deviation of x seems implausible to you. Then, you can consider that the command's second summary measure is high, and that sign reversal is unlikely. If the treatment effects of (g,t) cells are all negative, they cannot follow a normal distribution, so we do not discuss that possibility here.


## References

+ de Chaisemartin, C and D'Haultfoeuille, X (2020a). American Economic Review, vol. 110, no. 9. [Two-Way Fixed Effects Estimators with Heterogeneous Treatment Effects](https://www.aeaweb.org/articles?id=10.1257/aer.20181169).
+ de Chaisemartin, C and D'Haultfoeuille, X (2020b). [Two-way fixed effects regressions with several treatments](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3751060).

## Authors

+ Clément de Chaisemartin, University of California at Santa Barbara, Santa Barbara, California, USA.
+ Xavier D'Haultfoeuille, CREST, Palaiseau, France.
+ Antoine Deeb, University of California at Santa Barbara, Santa Barbara, California, USA.

## Contact

[chaisemartin.packages@gmail.com](mailto:chaisemartin.packages@gmail.com)
