Before finally looking at the Monte Carlo method itself (next lesson), we need to introduce the important concept of Estimator. But let's first start with a quick refresher on the things we learned so far.

## A Quick Review

In the last chapters, we have introduced the concept of the population parameter. We can use a simple average to compute the mean of any population parameter. This mean is denoted \(\mu\). However, when for practical reasons it is impossible to measure this mean, we can resort to using sampling to estimate it. The idea is to make a series of observations that take the form of a series of random variables noted \(X_i\) and average their value. This is called a sample mean:

$$\bar X = \dfrac{\sum_{i=1}^n X_i}{n}.$$

Both the variance of the population and the of the sample mean can be computed using the equations:

$$\sigma^2 = { {\sum_{i=1}^N {(x_i - \mu)^2}}\over N} = {{\sum_{i=1}^N x_i}\over N} - \mu^2,$$

and

$$S_n^2 = { {\sum_{i=1}^n (x_i - \bar X)^2 } \over n} = {{\sum_{i=1}^n x_i}\over n} - \bar X^2.$$

respectively. We will speak again about the variance of the sample mean in this chapter. Because the sample mean is a random variable itself (it is an average of random variables and hence is random itself), we can measure its mean and its variance using the same method as the one we used for the sample mean. The mean of the sample mean is just a simple average of all the sample means. As for the variance of the sample mean we found and proved in the last chapter that it relates to the variance of the population through the equation:

$$\bar \sigma_n^2 = { \sigma^2 \over n }.$$

Where \(\sigma^2 \) is the population variance and \(n\) is the sample size. We also showed in the last chapter, that by the Law of Large Numbers, the sample mean \(\bar X \) approaches in probability and almost surely the population mean \(\mu\):

$$\bar X \xrightarrow{p} \mu \quad \text{ for } n \rightarrow \infty.$$

The superscript \(p\) over the right arrow means "converge in probability".

Finally, we also learned a few things about the distribution of the sample mean itself. We know the distribution of a statistic is called a sampling distribution, that its expected value (expected value of the distribution of sample means) is the population parameter's mean \(\mu\) and that \(\bar X\) converges in distribution to a normal distribution (of mean \(\mu\) and variance \(\sigma^2 / n\)). We also know its rate of convergence is proportional to \(1 / \sqrt{n}\). To summarize we have:

![](/images/monte-carlo-methods/summary.png?)

- The population mean \(\color{\red}{\mu}\) and variance \(\color{\red}{\sigma^2}\).
- The sample mean \(\color{\red}{\bar X_n}\) and its variance \(\color{\red}{S_n}\).
- The expected value of the distribution of means \(\color{\red}{\mu_{\bar X}}\) and the variance of the distribution of means \(\color{\red}{ \sigma_{\bar X}^2 }\).

## Estimate and Estimator

The concept of an estimator is simple and is just a generalization in a way of the concept of the sample mean. Obviously, in statistics the terminology used when it comes to estimators is different than what we have been using so far. A parameter of a population will now be given the greek letter \(\theta\) (theta) instead of \(\mu\). As usual, our goal is to use some statistical method to estimate the value of \(\theta \) which is unknown (for example \(\theta\) can be the height of the adult population living in the Bahamas). What is used to do before, in the previous chapter was to estimate this value by sampling the population and averaging the results. The result is called the sample mean and can be written as:

$$\bar X = \color{red}{\dfrac{1}{n}} (\color{green}{X_1} \color{red}{+} ... \color{red}{+} \color{\green}{X_n}) = {\sum_{i=1}^n \dfrac{X_i}{n}}.$$

Where \(n\) is the sample size and \(X_i\) are random variables or if you prefer, some observable data. What needs to be noticed here, is that the sample mean is just some sort of **function** (a sum and average) **of a collection of observable data**. In other words, if you look at the equation of the sample mean above, the terms in green are the observable data, and the terms and mathematical operator in red form manipulate this data to produce a result which is an estimation of the population's parameter \(\theta \). All these terms and operators form a function of which the data is an argument. We can formalize this idea by writing:

$$\delta(x_1, ... x_n),$$

where the greek letter \(\delta\) (lower case delta) denotes a (real-valued) function taking as argument a collection of observable data \(x_1, ... x_n\).

!!!
This function \(\delta\) is what we call an **estimator** of the parameter \(\theta\) and the result of \(\delta(x_1, ..., x_n)\) is called an **estimate** of \(\theta\).
!!!

The sample mean is just an example of such an estimator but we will learn in future lessons that other estimators exist. It is important to realize that an estimator is a function of data, and consequently:

!!!
that because an estimator \(\delta(X1, . . . , Xn)\) is a function of the random variables \(X1, . . . , Xn\), the estimator itself is a random variable (which by the way is what we call a statistics),
!!!

which we have been repeating many times during this lesson, but we have now formalized this idea. Try to see the difference between an estimator and an estimate (even though subtle): an estimate is a **specific value** \(\delta(x_1,...,x_n)\) of the estimator which we can determine by using observable values \(x_1, ..., x_n\). The estimator is a **function** \(\delta(X)\) of the random vector \(X\) while again, an estimate is a just specific value \(\delta(x)\).

In the chapter on expected value, we mentioned that random variables can be manipulated [algebraically pointwise](http://en.wikipedia.org/wiki/Algebra_of_random_variables). This is important because it justifies the fact that an estimator can be considered a random variable itself. The result of adding together some random variables is a random variable. It also suggests that there is no restriction on the type of function you can use as an estimator and as mentioned before, different estimators will be studied in the next lessons. The "attractiveness" of an estimator (compared to others) depends on its properties such as its mean square error (RSE which we briefly talked about in the previous chapter), its consistency, and its asymptotic distribution. We briefly touched on these concepts in the previous chapter. We will review them in detail in a future lesson on estimators (the topic is important enough in rendering to have its lesson). An additional property we haven't talked about yet which is important is **unbiasedness**; we will look at this concept now.

## Unbiased and Biased Estimator

The concept of biasedness and unbiasedness is important in rendering. If you are interested in computer graphics and rendering you are likely to have come across articles or posts in which the authors talked about a bias or unbias path tracing. The question of what that means is also very often asked on forums. The term is not related to the field of rendering but to the field of statistics. The term "unbiased path tracing" was coined to designate a certain type of algorithm used in rendering based on "unbiased statistical methods". First, we will explain what this means, and then for fun and get back to the field of rendering we will look at the definition of unbiased rendering given by Wikipedia, and show you that now, with all the information you have been given in this lesson, you can understand every single word of this definition.

The concept is pretty simple. Earlier on in this chapter, we introduced the concept of estimator \(\delta(X)\). The sample mean is a form of an estimator, but in the general sense, an estimator is a function operating on observable data and returning an estimate of the population's parameter value \(\theta\) (we will be using \(\theta\) in this chapter to denote the unknown parameter we want to estimate). In the chapter on expected value, we showed that the sample means converges in probability to the population mean as the sample size approaches infinity:

$$\bar X_n \xrightarrow{p} \theta \quad \text{ for } n \rightarrow \infty.$$

We could also express this relationship in terms of the expected value of the sample mean:

$$E[\bar X_n] - \theta = 0.$$

But since the method by which we compute a sample mean is an estimator itself, we can substitute \(\bar X_n\) for \(\delta(X)\):

$$E[\delta_{unbiased}(X)] - \theta = 0.$$

This is an important result. It says that the difference between the expected value of the estimator and the population parameter is 0\. But if this is true in the particular context where the estimator is a simple average of random variables you can perfectly design an estimator which has some interesting properties but whose expected value is different than the parameter \(\theta\). In other such an estimator would produce the following result:

$$E[\delta_{biased}(X)] - \theta \neq 0.$$

The difference between the expected value of the estimator and the parameter is what we call bias. In other words, we can right the above relationship as:

$$E[\delta_{biases}(X)] - \theta = \text{ bias }.$$

And of course, you have already guessed that if the bias is 0, then we say that the estimator is **unbiased** and logically when this is not true (when the bias is different than 0) we say that the estimator is a **biased estimator**.

!!!
To be perfectly complete, we should add to this definition that to be an unbiased estimator, the estimator has to be unbiased for any possible value that \(\theta\) can take on. **A sample from a normal distribution with unknown mean \({ \theta }\), \({ \bar X_n } \) is an unbiased estimator of \({ \theta}\) because \({ E[\bar X_n] = \theta } \) for \({ -\infty < \theta < \infty }\).**
!!!

You may ask, wouldn't that be a significant problem for an estimator to have an expected value different than the parameter we try to estimate (in other words you may think of the result as being wrong)? In the general case this can be considered as an undesirable behavior indeed, but consider an estimator whose rate of convergence is much better than that of other estimators. Even if its expected value is just slightly off from the real value of \(\theta\) but close enough to be considered a valid estimate, the fact that you get an acceptable estimation much "quicker" than with other estimators even though biased, can be very advantageous (imagine a system in which speed is more important than visual accuracy or fidelity). Wherever you consider the result you get from using that "biased" estimator acceptable compared to the result you would get from using an "unbiased" estimator is completely left to your appreciation. Keep in mind that one of the main goals of computer graphics, is to be able to **generate high-quality antialiased images** (we will see what this means soon)**, with the smallest possible number of samples**. For this reason, estimators having a fast rate of convergence are often preferred to others even if they produce biased results.

## Properties of Estimators

The **unbiasedness** (or biasedness) of an estimator is a property we have already talked about.

Variance is another property: it relates to estimators' rate of convergence (how quickly you get to the true mean as you increase the sample size). For unbiased estimators, this variance is measured as \(E[(\bar X_n - \theta)^2]\) (which you can also write as \(E[(\delta(X) - \theta)^2]\)) which as briefly mentioned in the previous chapter is also called the estimator's **Mean Square Error** (or M.S.E.). As a general rule, the smaller the estimator's variance the better (it converges faster to the result).

**Consistency** is the last property we will review in this chapter. You will see this term being used often. An estimator is said to be consistent if the probability of the estimator getting closer to the population parameter \(\theta\) increases as the sample size \(n\) increases. We know from using the Law of Large Numbers that the sample mean \(\bar X\) converges in probability to \(\theta \) as \(n \rightarrow \infty\) hence consistency is verified in this case.

As a general rule, a good estimator is both unbiased and has the lowest variance or M.S.E. Often though biased estimators have a variance lower than that of unbiased estimators (which we shall see in our study of various estimators).

As a final exercise, take the time to read the following Wikipedia definition of an unbiased renderer. To your great satisfaction, you should be pretty amazed by the fact that every single idea that this definition refers to is now fully making sense to you. We broke it down to insert comments:

> In computer graphics, unbiased rendering refers to a rendering technique that does not introduce any systematic error, or bias, into the radiance approximation.

You know that goal of a renderer is to compute a quantity which is called radiance. We talked about radiance extensively in the lesson Introduction to Shading. The method this line refers to is the estimator that we will be using to evaluate this radiance (using sampling) and if this estimator is unbiased then we can say that our renderer is unbiased as well.

> Because of this, it is often used to generate the reference image to which other rendering techniques are compared.

Because the estimator is unbiased we know that if the sample size is high enough it will converge eventually to the true radiance. Thus images produced by unbiased renderers can be used as reference images (i.e. compared to images produced by biased renderers for instance).

> Mathematically speaking, the expected value of the unbiased estimator will always be the population mean, for any number of observations. [...]

We proved in the last chapters that the sample mean \(\bar X_n\) gets closer to the parameter \(\theta\) for \(n \rightarrow \infty\).

> Variance is reduced by and standard deviation by for data points, meaning that four times as many data points are needed to halve the standard deviation of the error.

We explained in the last chapter that the variance or M.S.E of the distribution of mean \(\sigma_{\bar X}^2 \) is equal to the population's variance divided by the sample size \(n\). Since the standard deviation is just the square root of variance, we also showed that we needed four times more samples to half the standard deviation (which you can see as a measure of the error).

> This makes unbiased rendering techniques less attractive for real-time or interactive rate applications.

We also explained that unbiased estimators were generally preferred to biased ones, however, biased estimators can converge more quickly to \(\theta\) making them more attractive than unbiased estimators particularly since speed is preferred to accuracy (or when accuracy is not essential).

> Conversely, an image produced by an unbiased renderer that appears smooth and noiseless is probabilistically correct. A biased rendering method is not necessarily wrong, and it can still converge to the correct answer if the estimator is consistent. It does, however, introduce a certain bias error.

The property of being biased is not as important as the property of being consistent in the evaluation of an estimator. Convergence is important regardless of whether or not the estimate is biased or not (assuming the number of samples is large enough to produce an image with very small variance, we at least know that the only difference between the unbiased and biased image is due to bias and not any other error that would have been introduced by the biased estimator). In other words, bias is generally acceptable, but the error is not. To see how all these concepts are used in practice, check the lesson entitled "Introduction to Light Transport".

## Wrapping Up!

Congratulation if you read all the chapters so far! This will conclude (momentarily) our journey in the world of statistics and probability. We will regularly come back to issues related to statistics in the next lessons. No matter how painful and difficult you found that journey, we promise that it will pay off! We are now finally ready to look at the Monte Carlo method itself but considering everything you know about statistics this should present no difficulty at all.