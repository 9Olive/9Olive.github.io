---
title: "Test RMarkdown"
subtitle: "Testing a Markdown Document Rendered from RStudio using Knitr"
layout: post
tags: [R, RStudio, Knitr, RMD]
---

# Header!

The [test markdown] posted earlier seems to have work fine. The current issues remaining are asthestic, and would not have any affect on an actual post. In light of this success, it is time to turn my attention to building a workflow that starts with RStudio. I believe I'll need to tweak and tune the Knitr parameters to get what I want. I've also seen other rbloggers use custom scripts to Knit their post. 

[test markdown]: https://9olive.github.io/blog/2020/04/17/Test.html

# Example Coding

In the first markdown post, the code I post was executed independently of the post and the image was manually saved in a reference directory. The convenience of rendering straight from an [RMD document] lies within how code outputs are handled. Suppose you have 20 outputs from a single document. Would one need to manually execute each code chunk, save the output to reference directory, and then edit the markdown to include said reference? What if the output is just text, then would you need to edit the text such that it has the appearance of a coding output? Needless to say, the RMarkdown process aims to streamline this process.

[RMD document]: https://rmarkdown.rstudio.com/articles_intro.html

## Header 2!
### Header 3, for fun...

#### Okay some code now:

First, let's take a look at a code block that should not produce an output.


{% highlight r %}
library(faraway) 
library(tidyverse)
library(patchwork)
library(MASS)
library(knitr)
theme_set(theme_bw())
{% endhighlight %}

Look, no output. Normally R will warn the user of package conflicts, or confirm that a library is activated plus any default verbose messaging the package has.

Below we do a little linear modeling. The work below is looking a Salmonella dataset. 

> The data was collected in a salmonella reverse mutagenicity assay where the numbers of revertant colonies of TA98 Salmonella observed on each of three replicate plates for different doses of quinoline.

Below we fit two models. One that treats dosage of quinoline as a continuous variable, and another that treats it as a discrete factor level. The fit of each model is compared via ANOVA and then visualized. 


{% highlight r %}
data("salmonella")

# Fitting first model. Transforming Dose.
summary(salmonella.lm <- lm(colonies ~ log(dose + 1), salmonella))
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = colonies ~ log(dose + 1), data = salmonella)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -16.376  -6.882  -1.509   5.400  29.119 
## 
## Coefficients:
##               Estimate Std. Error t value Pr(>|t|)   
## (Intercept)     19.823      5.064   3.915  0.00123 **
## log(dose + 1)    2.396      1.128   2.125  0.04955 * 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 10.84 on 16 degrees of freedom
## Multiple R-squared:  0.2201,	Adjusted R-squared:  0.1713 
## F-statistic: 4.514 on 1 and 16 DF,  p-value: 0.04955
{% endhighlight %}



{% highlight r %}
fit1 <- ggplot() +
  geom_point(aes(x = salmonella.lm$model[,2], y = salmonella.lm$model[,1])) +
  geom_line(aes(x = salmonella.lm$model[,2], y = salmonella.lm$fitted.values)) +
  labs(title = 'Salmonella Fit', subtitle = c(salmonella.lm$call),
       x = 'Quinoline Dose (Transformed)', y = '# of Colonies') +
  theme(text = element_text(size = 14))

# Fitting a second model for comparison, treating Dose as factor, and still transforming it.
summary(salmonella.lm2 <- lm(colonies ~ as.factor(round(log(dose + 1), 4)), salmonella)) 
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = colonies ~ as.factor(round(log(dose + 1), 4)), data = salmonella)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -15.667  -3.917  -0.500   3.417  17.333 
## 
## Coefficients:
##                                          Estimate Std. Error t value Pr(>|t|)
## (Intercept)                                21.667      5.506   3.935  0.00198
## as.factor(round(log(dose + 1), 4))2.3979   -3.333      7.787  -0.428  0.67617
## as.factor(round(log(dose + 1), 4))3.5264    3.333      7.787   0.428  0.67617
## as.factor(round(log(dose + 1), 4))4.6151   21.000      7.787   2.697  0.01942
## as.factor(round(log(dose + 1), 4))5.8111   15.667      7.787   2.012  0.06722
## as.factor(round(log(dose + 1), 4))6.9088    8.000      7.787   1.027  0.32449
##                                            
## (Intercept)                              **
## as.factor(round(log(dose + 1), 4))2.3979   
## as.factor(round(log(dose + 1), 4))3.5264   
## as.factor(round(log(dose + 1), 4))4.6151 * 
## as.factor(round(log(dose + 1), 4))5.8111 . 
## as.factor(round(log(dose + 1), 4))6.9088   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 9.536 on 12 degrees of freedom
## Multiple R-squared:  0.5475,	Adjusted R-squared:  0.359 
## F-statistic: 2.904 on 5 and 12 DF,  p-value: 0.06047
{% endhighlight %}



{% highlight r %}
fit2 <- ggplot() +
  geom_point(aes(x = salmonella.lm2$model[,2], y = salmonella.lm2$model[,1])) +
  geom_point(aes(x = salmonella.lm2$model[,2], y = salmonella.lm2$fitted.values), col = 'red', shape = 3, size = 3) +
  labs(title = 'Salmonella Fit, Dose Treated as Factor', subtitle = c(salmonella.lm2$call),
       x = 'Quinoline Dose (Transformed)', y = '# of Colonies') +
  theme(text = element_text(size = 14))

# F-Test between the two models
anova(salmonella.lm, salmonella.lm2)
{% endhighlight %}



{% highlight text %}
## Analysis of Variance Table
## 
## Model 1: colonies ~ log(dose + 1)
## Model 2: colonies ~ as.factor(round(log(dose + 1), 4))
##   Res.Df    RSS Df Sum of Sq      F Pr(>F)
## 1     16 1881.1                           
## 2     12 1091.3  4    789.73 2.1709 0.1342
{% endhighlight %}

I print the visual fit below. 


{% highlight r %}
# Patchwork nomenclature
fit1 | fit2
{% endhighlight %}

![testing](/blog/addons/2020-04-20-TestRMD/unnamed-chunk-3-1.png)

We see that the factorized model has a better fit for each dosage level. The red pluses are simply the average for each level. Well that's all for this post. 
