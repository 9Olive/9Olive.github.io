---
title: Testing the ropes
subtile: Post workflow needs to be polished
layout: post
tags: [beuatiful-jekyll, data-science, rstats]
bigimg: /blog/addons/2020-04-17-Test/AdobeStock_163816180.jpeg
image: /img/Rplot02.png
---

# From Boiler Room to Light

I'm slowly constructing my workflow from analysis in Rstudio all the way to posting to the web. 

## Code Chunking

It's important for me to be able to showcase my code. There are several ways to highlight code in Markdown documents. Let's try a couple:

```r
x <- rnorm(10)
y <- (x * 10) + runif(10, -10, 10)

plot(y ~ x)
lines(x, lm(y ~ x)$fitted)
```

![rtest1](/blog/addons/2020-04-17-Test/Rplot01.png)

How does that look?

**Another one**
<center>
<img src="/blog/addons/2020-04-17-Test/maxresdefault.jpg" alt="DJ Khaled letting you know there is, another one." width="400">
</center>

Code chunk 2

{% highlight r %}
x <- rnorm(10)
y <- (x * 10) + runif(10, -10, 10)

plot(y ~ x)
lines(x, lm(y ~ x)$fitted)
{% endhighlight %}

Same image, but different code to render it.

![rtest2](/blog/addons/2020-04-17-Test/Rplot01.png){: .center-block :}