## Introduction

The chapter contains more advanced mathematical concepts than the others. It would typically be part of the advanced section. You can safely skip it if you are not interested. However, we hope beginners can find an excellent introduction to a few powerful mathematical tools and techniques you will often see used in computer graphics.

## Taylor Series

Remember from chapter 1 that the Bézier curve can be seen as a sum of polynomials. We can generalize this idea and write the following formula (equation 1):

$$f(x) = a_0 + a_1x + a_2x^2 + a_3x^3 + a_4x^4 + ... = \sum_{n=0}^\infty c_n x^n$$

Where n defines the degree of the function. In the case of cubic Bézier curves, n = 3. However, let's not try to show how this relates to the Bézier example too much, and let's keep this demonstration as generic as possible. In mathematics, this sum is called a **power series** in one variable, x.

The power series expansion for f(x) can be differentiated term by term, and the resulting series is a valid representation of f′( x) (equation 2):

$$f'(x) = a_1 + 2a_2x + 3a_3x^2+ 4a_4x^3 + ... = \sum_{n=1}^\infty nc_n x^{n-1}$$

<details>
A quick refresher regarding the terms we will use in this chapter. The notation n! means the factorial of n, and \(f^{(n)}(x)\) denotes the nth derivative of f evaluated at the point x. The derivative of order zero f is defined as f itself, and \((x − a)^0\) and 0! are both determined to be 1.
</details>

If we differentiate again, we get (equation 3):

$$f''(x) = 2a_2 + 6a_3x+ 12a_4x^2 + ... = \sum_{n=2}^\infty n(n-1)c_n x^{n-2}$$

And so on. Now let's imagine that we substitute x with 0 in f(x), f'(x) and f''(x):

$$\begin{array}{l}x=0 \text{ in equation 1 yields } a_0 = f(0) \\x=0 \text{ in equation 2 yields } a_1 = f'(0)\\x=0 \text{ in equation 3 yields } a_2 = {f''(0) \over 2}\end{array}$$

And in general, substituting x = 0 in the power series expansion for the nth derivative of f yields:

$$c_n = {{f^{(n)}(0)} \over {n!}}$$

These are called the **Taylor coefficients** of f and the resulting power series:

$$\sum_{n=0}^\infty {{f^{(n)}(0)} \over {n!}}x^n,$$

is called the **Taylor series** of the function f. How can this help us in any way? This Taylor series has a remarkable property for some functions. For these functions, it converges to the original function f(x). That is:

$$f(x) = \sum_{n=0}^\infty {{f^{(n)}(0)} \over {n!}}x^n$$

If this equality is true for all x in some neighborhood of (interval around) 0, then the function f is said to be analytic (at 0). More generally, if you form the Taylor series of f about a point x = x0:

$$\sum_{n=0}^\infty {{f^{(n)}(x_0)} \over {n!}}(x-x_0)^n$$

And if this series converges to f(x) for all x in some neighborhood of x0, then f is said to be analytic at x0.

## Moving Forward

This chapter is called fast forward differencing. So where is the forward differencing term coming from, and what relation does it have with the Taylor series? Without going into too many details here, let's recall that the derivative of a function at x can be computed by taking two points on the plot of this function, one at x and one at x + h, and tracing a line passing through these two points. This is just an illustrated way of looking at this mathematical concept called a **forward difference approximation**, which can be generalized with the following formula (equation 4):

$$f'(x) = \lim_{h \to 0}{ { F(x + h) - F(x) } \over h }$$

The value h (usually a small value) represents the difference in x between the two sampled points (in our example from figure 1).

![Figure 1: you can think about the derivative as the slope of a tangent line. Using forward difference, we can approximate this derivative. When h decreases, the estimation of this derivative becomes more accurate. In this example, the black line is the actual tangent an f(x), and the blue line is the approximated tangent using forward differencing for two values of h.](/images/bezier/derivative.gif?)

What does it mean exactly? As shown in figure 1, the line we obtain using this method approximates the expected derivative at x. The error of this approximation depends on h: as h decreases (towards a limit which is 0), the approximation of this derivative gets closer to the expected solution (Figure 1). But before we say more about the magnitude of the difference between this approximation and the expected solution, let's mention that we can compute the next position f(x + h) on the curve by rearranging the term of the previous equation:

$$f(x + h) \approx f(x) + hf'(x)$$

This is a crucial equation that says that a point on the curve at x + h can be approximated by taking the sum of the function at x and the first derivative of f at x multiplied by h.

Now let's get back to this approximation problem. Equation 4 can be written as:

$$f'(x) = { { f(x + h) - f(x) } \over h } + \text{ term which goes to 0 as } h \rightarrow 0$$

In other words, the expected solution to the derivative of the function f can be seen as an approximation plus a value which can be seen as the error of this approximation. As h goes to 0, the approximation converges to the expected solution, and the error term vanishes to 0. Therefore, we can establish a direct link between the magnitude of h and the magnitude of this error. We can write:

$$O(h) \equiv \text{ terms of the form } h \times \text{ something. }$$

Where \(O(h)\) is the notation representing the approximation's error. So:

$$f'(x) = { { f(x + h) - f(x) } \over h } + O(h)$$

If we multiply this equation by h, we get:

$$hf'(x) = { f(x + h) - f(x) }+ O(h^2)$$

And by rearranging the terms, we get the following:

$$f(x+h) = f(x) + hf'(x) + O(h^2)$$

// explain why the sign of O(h) doesn't change

Which is a Taylor expansion of order 2. Note than when \(h\) is small (e.g. \(h = 0.1\)), then \(h^2\) is even smaller (\(h^2 = 0.01\)). This expansion can be done with as many terms as we like (equation 5):

$$f(a + h) = f(a) + f'(a)h + f''(a) { {h^2} \over {2!} } + ... + f^{ (n) }(a){ {h^n} \over {n!}} + O(h^{n+1})$$

Where \(f^{(k)}\) is the k-th derivative of f. When a = 0, this expansion is also called the **Maclaurin's expansion** (the value n + 1 defines the order of this Taylor series). The more terms we use, the smaller the error.

<details>
For completeness, let's mention two additional ways of computing this approximation: the backward and centered approximations (we will only use the forward difference approximation in this chapter). Respectively they are:

$$
\begin{array}{l} 
f'(x) = \dfrac{F(x) - F(x - h)}{h} + O(h) \\
f'(x) = \dfrac{F(x + h) - F(x - h)}{2h} + O(h^2)
\end{array}
$$
</details>

Hopefully, by now, you will understand where we are heading and how these techniques can be used to compute positions along the Bézier curve. Equation 5 says that we can approximate the value of a function f at a + h using a Taylor series (we will use this idea to evaluate positions along the Bézier curve for increasing value of the parametric value t), and the error of this approximation depends of the number of terms we use in this series. The error decreases as the number of terms used increases. The error increases as h increases. That is for the theory. Next, we will show that the equation used to compute positions along Bézier curves belongs to a class of functions for which the Taylor series converges to the function f. In other words (we are using a Maclaurin's expansion here, a = 0):

$$f(x) = \sum_{n=0}^\infty {{f^{(n)}(0)} \over {n!}}x^n,$$

holds for the polynomials used in the computation of Bézier curves. Furthermore, we will show that the Taylor series used to compute these polynomials have a finite number of terms (the term \(O(h^n) = 0\)). Practically, it means that using the Taylor series to compute positions along the Bézier curve does not return an approximation but the expected solution.

## Bézier curve as a Taylor series

As we mentioned above, polynomials, such as those used to compute Bézier curves and surfaces, are analytic everywhere (this is the condition to use a Taylor series in place of the function f). We could use a Taylor series to compute the position of points along a Bézier curve rather than directly computing the Bernstein polynomials. Let's recall the formula used to calculate these positions:

$$B(x) = P_0(1-x)^3 + P_13(1-x)^2x + P_23(1-x)x^2 + P_3x^3$$

Which we can generalize (equation 6):

$$B(x) = P_0 * B_0(x) + P_1 * B_1(x) + P_2 * B_2(x) + P_3 * B_3(x)$$

where \(P_0, P_1, P_2, P_3\) are the control points of the curve and \(B_0, B_1, B_2, B_3\) are Bernstein polynomials:

$$B_{i,n}(x) = \left( \begin{array}{c}n \\ i \end{array} \right)x^i(1-x)^{n-i}, \; i=0,...,n.$$

In the case of cubic Bézier curves, n = 3, and the four coefficients are:

$$\begin{array}{l} B_{0,3}=(1-x)^3,\\ B_{1,3}=3x(1-x)^2,\\ B_{2,3}=3x^2(1-x)x,\\ B_{3,3}= x^3\end{array}$$

If we try to re-write equation 6 in a form similar to equation 1, we have:

$$\begin{split}B(x) = &a_0 B_{0,3}(x) + a_1 B_{1,3}(x) + a_2 B_{2,3}(x) + a_3 B_{3,3}(x) = \\& \sum_{i=0}^{n=3}a_i B_{i,n}\end{split}$$

Therefore the equation used for Bézier curves can be seen as a power series and since the terms \(B_{i,3}\) are polynomials, we can compute it using a Taylor series:

$$B(x) \rightarrow \sum_{i=0}^{n=3} { {a_iB^ {(i)} (0) } \over {i!} }(x)^i$$

Taylor series are based on the function's f(x) (here \(f(x)=B_{i,3}(x)\)) first, second, ... derivatives, therefore to use this method, we now need to learn about computing the derivatives of Bernstein polynomials.

## Derivatives of Bernstein Polynomials

Derivatives of the n-th degree Bernstein polynomials are polynomials of degree n − 1. This derivative can be written as a linear combination of Bernstein polynomials. In particular (equation 7):

$${ d \over dx } B_{i,n}= n(B_{i-1, n-1}(x) - B_{i, n-1}(x))dx$$

You can read as the derivative of a Bernstein polynomial can be expressed as the degree of the polynomial (n) multiplied by the difference of two Bernstein polynomials of degree n−1.

<details>
The derivative of the function \((1-x)^n\) is \(-n(1-x)^{n-1}\) and the Bernstein polynomial (we have omitted the binomial coefficient for now) \(B_{i,n}=x^i(1-x)^{n-i}\). The latter can be seen as the product of two functions: \(x^i\) and \((1-x)^{n-1}\). To find the derivatives of the products of two (or more) functions, we need to use the product rule. It says that:

$$(f.g)' = f'.g + f.g'$$

Therefore the derivative of the Bernstein polynomial can be written as:

$${B'}_{i,n} = (x^i . (1-x)^{n-i})'=ix^{i-1}.(1-x)^{n-i} + (-n(1-x)^{n-i-1}x^i)$$

We can put the Binomial coefficient back in:

$${B'}_{i,n} = \left(\begin{array}{c}n\\i \end{array}\right) (ix^{i-1}.(1-x)^{n-i} - n(1-x)^{n-i-1}x^i)$$

Developing this formula and re-arranging the term leads to equation 7.
</details>

Using equation 7 to compute these derivatives is trivial. To make this task easier, let's write the first few Bernstein basis polynomials again:

$$
\begin{array}{l} B_{0,0}(x) = 1, \\
B_{0,1}(x) = 1 - x, &amp; B_{1,1}(x) = x \\
B_{0,2}(x) = (1 - x)^2, &amp; B_{1,2}(x) = 2x(1 - x), &amp; B_{2,2}(x) = x^2 \\
B_{0,3}(x) = (1 - x)^3, &amp; B_{1,3}(x) = 3x(1 - x)^2, &amp; \rightarrow \\ \rightarrow B_{2,3}(x) = 3x^2(1 - x), &amp; B_{3,3}(x) = x^3  \\
\end{array}
$$

For instance the derivative of \(B_{i=0,n=3}\) is:

$${B'}_{i=0,n=3} = 3({B_{-1,2}} - B_{0, 2}) = 3(0 - (1-x)^2) = 3(1-x)^2$$

We now have all the mathematical tools to apply these techniques to compute Bézier curves.

## Application to Bézier Surfaces

We can now apply these techniques to evaluate Bézier curves or surfaces. Remember from chapter 1 that these curves can be computed using the following formula:

$$B(x) = P_0 (1-x)^3 + P_1 3 (1-x)^2x + P_2 3(1-x)x^2 + P_3 x^3$$

Where \(P_0, P_1, P_2, P_3\) are the curve's control points, where the other terms are Bernstein polynomials. We can re-write these equations as:

$$B(x) = P_0 B_{0,3} + P_1 B_{1,3} + P_2 B_{2,3} + P_3 B_{3,3}$$

And we showed at the beginning of this chapter that it could also be expressed as a Taylor series:

$$\begin{split}B(x) \rightarrow &\sum_{i=0}^{n=3} { {B^ {(i)} (0) } \over {i!}}(x)^i = \\&B'(0) + B''(0)x + {B'''(0) \over 2}x^2 + {B'''(0) \over 6}x^3 \end{split}$$

To compute B(x) using the Taylor series, we need to compute the first, second, and third derivatives of B(x). Hopefully, because we are computing a cubic curve, the fourth derivative (and all subsequent ones) is 0 (if f(x)=x^3, f'(x)=3x^2, f''(x)=6x, f'''(x)=6, and f''''(x) = 0). We are, therefore, only interested in the first three order derivatives of this function: B'(0), B''(0), and B'''(0), which are used in the Taylor series. Remember that further up in this chapter, we have mentioned that using the Taylor series to evaluate positions along the Bézier curve doesn't give an approximation but the exact solution. Why? Because, as we have just said, the Taylor series can be expanded to a finite number of terms B'(0), B''(0), and P'''(0), which reduces the error term \(O(h^n)\) in the series to zero. This is an essential detail. Using what we have learned on computing the Bernstein polynomials derivatives, we can therefore write:

$$
\begin{array}{l} B(0) = P_0\\B'(0) = 3P_1 - 3P_0 \\ B''(0) = 6P_0 - 12P_1 + 6P_2 \\ B'''(0) = -6P_0 + 18P_1 - 18P_2 + 6P_3\end{array}
$$

<details>
if \(B(x) = a_0 B_{0,3} +  a_1 B_{1,3} +  a_2 B_{2,3} +  a_3 B_{3,3}\) then (using what we have learned on Berstein polynomial derivatives):

$$\begin{split} B'(x) &amp;=  a_0 3(-(1-x)^2) + a_1 3((1-x)^2 - 2x(1-x)) +\\&amp;a_2 3(2x(1-x) - x^2) + a_33(x^2)\end{split}$$

and

$$B'(0) = a_0 -3 + a_1 3 + 0 + 0 = 3 a_1 -3 a_0$$

We can easily find B''(x) from B'(x) (remember that (f.g)' = f'g + g'f therefore if \(g(x)= 2x(1-x)\) then \(g'(x)=2(1-x) + 2x*-1\):

$$\begin{split} B''(x) &amp;=  a_0 3(- -2(1-x)) + \\&amp;a_1 3(-2(1-x) - (2(1-x) + 2x*-1)) +\\&amp;a_2 3 ((2(1-x) + 2x*-1) - 2x) + a_33(2x)\end{split}$$

and

$$\begin{split}B''(0) &amp;= a_0 3(2) + a_13(-2 -2) + a_2 3(2) + a_3 3(0) = \\&amp;6 a_0 - 12 a_1 + 6 a_2\end{split}$$

Finally, deriving B'''(x) from B''(x):

$$\begin{split}  B'''(x) &amp;= \\&amp;a_0 3(2*-1) + a_13(2 - (-2 - 2)) + a_23((-2 - 2) -2) + a_3 3(2) \\&amp;= -6a_0 + 18a_1 - 18a_2 + 6 a_3\end{split}$$
</details>

All there is left to do is to substitute these derivatives in the Taylor series formula (equation 8):

$$B(x) = B(0) + B'(0) x + {{B''(0) x^2} \over 2} + {{B'''(0) x^3} \over 6}$$

Where x, in that case, is the parametric value t which we have been using to compute positions along the curve. We can implement this formula in the following pseudocode:

```
point P[4] = { ... }; // control points
point b0 = P[0]; // initialize p0, pd0, pdd0 and pddd0
point bd0 = 3 * P[1] - 3 * P[0]; 
point bdd0 = 6 * P[0] - 12 * P[1] + 6 * P[2];
point bddd0 = -6 * P[0] + 18 * P[1] - 18 * P[2] + 6 * P[3];
int nsegs = 10; // number of points we want to compute on the curve
float x = 0;
float dx = 1 / nsegs;
for (int i = 0; i &lt;= nsegs; ++i) {
   P_on_curve = b0 + bd0 * x + bdd0 * x * x / 2 + bddd0 * x * x * x / 6;
   x += dx;
}
```

You may still wonder why we are computing B(x) with equation 8, as it seems a more complicated solution than when we compute the Bernstein polynomial directly. Equation 8, though, has an interesting quality from a computational point of view, as shown in the pseudocode above: once P(0), P'(0), P''(0), and P'''(0) are computed (lines 2-5 in the code), these values can be re-used to calculate as many points as we want on the curve for values of t increasing from 0 to 1 (line 10). If we compute many points at once, this technique can be more efficient than computing the Bernstein polynomials directly.

However, the equation in its current form still requires a multiplication by x. We could rewrite equation 8 to hide this multiplication (equation 9):

$$B(x) = B(0) + Bd + { Bdd \over 2 } + { Bddd \over 6 },$$

where

$$
\left\{
\begin{array}{l}
Bd  &=& B'(0)x,\\
Bdd &=& {{B''(0) x^2} },\\
Bdd &=& {{B'''(0) x^3} }
\end{array}
\right.
$$

This is a trick, but if we could compute B(x) using additions and divisions only, the code would run faster. How could we make that trick work? First, it is essential to understand that the previous technique (the pseudocode above) computes positions on the curve for any values of x (or \(t\) to stick with the term used in the previous chapter) on the interval [0,1]. Values on the curve are computed using the result of the function and the result of the function's first, second, and third derivatives at x = 0 (remember that we are using a Maclaurin series). Intuitively, you can see this as computing points about f(0) for values of x greater than 0. With equation 8, we don't necessarily have to compute the points on the curve in sequential order. We can pick any random value for x in the interval [0,1], and it would work. As for equation 9, a solution exists, but on the condition that t varies uniformly from 0 to 1 (to phrase things differently, to the contrary of equation 8, we won't be able to use equation 9 to compute points on the curve for nonsequential and non regularly spaced values of x). This constraint is the price to pay to get a solution faster to compute than a straightforward implementation of equation 8. This time we will use the general form of the Taylor series (equation 10):

$$f(x + h) = f(x) + f'(x)h + {{f''(x)h^2} \over 2} + {{f'''(x)h^3} \over 6} $$

The difference with the Maclaurin series is that f(x+h) computation now depends on f(x). We can compute a position on the curve at f(0); for example, increment x by \(\Delta x\) and compute f(0+\(\Delta x\)) as f(0) plus the other terms of the Taylor expansion. We can repeat this process in a loop to compute a sequence of points for monotonically increasing values of x:

```
f(x) = f(0);
h = 1 / niter; // in this example, x goes from 0 to 1, but it doesn't have to
for (int i = 0; i &lt; niter; ++i) {
    // compute a point in the proximity of f(x)
    f(x + h) = f(x) + f'(x) * h + f''(x) * h^2 / 2 + f'''(x) * h^3 / 6;
    // new point becomes f(x)
    f(x) = f(x + h);
    x += h; // update current value of x
}
```

To make the rest of the demonstration easier to understand, let's imagine that you want to compute a series of values for the function f(x) = 2x + 1 using the Taylor series method:

$$f(x + h) = f(x) + f'(x)h = f(x) + 2 * h$$

We can implement this function with the following pseudocode:

```
float h = 1 / niter;
float fd = 2;
for (int i = 0; i &lt;= niter; ++i) {
    f(x + h) = f(x) + fd * h;
    f(x) = f(x + h);
    x += h;
}
```

However, computing f(x + h) involves an addition and a multiplication (fd * h). To get rid of this multiplication, we could store the result of 2h in a variable and add this variable to f(x):

```
float h = 1 / niter;
float fdh = 2 * h;
f(x) = 0; // first value
for (int i = 1; i &lt;= niter; ++i) {
    f(x + h) = f(x) + fdh;
    f(x) = f(x + h);
    x += h;
}
```

However, this technique only works because h is constant; in other words, because x is incremented using a regular step size. If this limitation is not a problem, then computing f(x+h) that way is quicker than the method involving multiplication.

Let's try to apply these techniques to the Bézier curve. We want to compute equation 10 in a loop, to calculate a sequence of points along the Bezier curve, where x (or the parametric value t) goes from 0 to 1 in constant step increment. Here is equation 10 again.

$$f(x + h) = f(x) + f'(x)h + {{f''(x)h^2} \over 2} + {{f'''(x)h^3} \over 6}$$

The size of this step is determined by the total number of points needed on the curve (which also defines the number of times we iterate through the loop):

$$h = { 1 \over npoints} $$

Let's first look at the first derivative of the function f in equation 10:

$$f(x + h) = f(x) + \color{ \red }{ f'(x)h } + {{f''(x)h^2} \over 2} + {{f'''(x)h^3} \over 6} $$

It is essential to remember that as we iterate through the loop, x increases. Therefore, the value of f'(x) changes as x increases, and each time we iterate through the loop, we need to compute the next point x + h for f'. What works for f can also work for f' since they are both polynomials. Therefore we can also use a Taylor series (as we did for f) to compute f' (equation 11):

$$\color{\red}{f'(x + h) = f'(x) } + \color{\green}{f''(x)h + {f''(x) h^2\over 2}}$$

What you need to see in this formula is that f'(x + h) only relies on f'(x) (text in red). Of course, we have f'' and f''' in the formula (text in green), which are both multiplied with h but let's ignore them for the time being. If we find an initial value for f' at x = 0, computing the next point in the loop f'(x=0 + h) can be done by adding f'(0) plus some other terms. And the next point in the loop can be computed as f'(x=h + h) = f'(x=h) plus some other terms, etc. Notice that computing f'(x+h) only requires additions. The final obstacle is the multiplication of f'(x) by h in equation 10. But note that if:

$$f'(x + h) = f'(x) + ...$$

Then similarly:

$$f'(x + h) h = f'(x) h + ( ... ) h$$

Multiplying the terms by h on both sides doesn't change the equality. Therefore we can compute f'(x+h)h in a loop as the sum of f'(x)h. Let's write some pseudocode to illustrate this idea:

```
float h = 1 / niter;
// compute the initial value for f' * h
fph = fp(0) * h; // f'(0) multiplied by h 
for (int i = 1; i &lt;= niter; ++i) {
    // compute  f'(x + h)h = f'(x)h + ( ... )h
    fph = fph + ( ... ) * h;
}
```

If we add what we have learned on computing f'(x)h in equation 10, we can now compute f(x + h) and f'(x + h)h in a loop using additions only (if we ignore the terms denoted by ...):

```
float h = 1 / niter;
// compute the initial value for f
f = f(0);
// compute the initial value for f' * h
fph = fp(0) * h; // f'(0) multiplied by h 
for (int i = 1; i &lt;= niter; ++i) {
    // compute f(x + h) = f(x) + f'(x)h + ...
    f = f + fph + ...;
 
    // and now compute  f'(x + h)h = f'(x)h + ( ... )h
    fph = fph + ( ... ) * h;
}
```

We can apply the same method to compute f'' in the Taylor series of f and f'. We can compute f''(x + h) using another Taylor series (equation 12):

$$f''(x + h) = f''(x) + f'''(x) h$$

In equation 10, f''(x) is multiplied by \(h^2\) but if:

$$f''(x + h) = f''(x) + f'''(x) h$$

then

$$f''(x + h) h^2 = f''(x) h^2 + ( f'''(x) h ) h^2$$

Remember that f'' is used to compute f (equation 10) and f' (equation 11). In equation 10, f'' is divided by two. The pseudocode with the addition of f'' gives:

```
float h = 1 / niter;
// compute the initial value for f
f = f(0);
// compute the initial value for f' * h
fph = fp(0) * h; // f'(0) multiplied by h 
// compute initial value for f'' * h * h
fpphh = fpp(0) * h * h;
for (int i = 1; i &lt;= niter; ++i) {
    // compute f(x + h) = f(x) + f'(x)h + f''(x)h^2 / 2 + ...
    f = f + fph + fpphh / 2 + ...;
 
    // compute  f'(x + h)h = f'(x)h + (f''(x)h + ... )h
    fph = fph + fpphh + ...;
 
    // compute f''(x + h)h^2 = f''(x)h^2 + ( f'''(x)h ) h^2
    fpphh = fpphh + ...;
}
```

The last term to update in the loop is f''' (multiplied by h^3 in equation 10) which appears in the Taylor series of f, f', and f''. But it is a constant (the third derivative of a cubic function is a constant term); therefore, its value doesn't change as x increases. F''' is divided by 6 in equation 10 (f), 2 in equation 11 (f''), and also used in equation 12 (f''). Here is the final version of our pseudocode:

```
float h = 1 / niter;
// compute the initial value for f
f = f(0);
// compute the initial value for f' * h
fph = fp(0) * h; // f'(0) multiplied by h 
// compute initial value for f'' * h * h
fpphh = fpp(0) * h * h;
// compute initial value for f''' * h * h * h
fppphhh = fppp(0) * h * h * h;
for (int i = 1; i &lt;= niter; ++i) {
    // compute f(x + h) = f(x) + f'(x)h + f''(x)h^2 / 2 + f'''(x)h^3 / 6;
    f = f + fph + fpphh / 2 + fppphhh / 6;
 
    // compute  f'(x + h)h = f'(x)h + (f''(x)h/2 + ... )h
    fph = fph + fpphh + fppphhh / 2;
 
    // compute f''(x + h)h^2 = f''(x)h^2 + (f'''(x)h/2)h^2;
    fpphh = fpphh + fppphhh;
}
```

If we apply this technique to the Bézier curve, then we need to compute the initial values for f, fph, fpphh and fppphhh using the values of \(B(0)\), \(B'(0)\), \(B''(0)\) and \(B'''(0)\) and multiply \(B'(0)\) by \(h\), \(B''(0)\) by \(h^2\) and \(B'''(0)\) by \(h^3\):

```
float h = 1 / nsegs; // constant step increment
point P[4] = { ... }; // control points
// compute initial values for f, fph, fpphh and fppphhh
point f = P[0]; // B(0)
point fph = (3 * P[1] - 3 * P[0]) * h; // B'(0)h
point fpphh = (6 * P[0] - 12 * P[1] + 6 * P[2]) * h * h; // B''(0)h^2
point fppphhh = (-6 * P[0] + 18 * P[1] - 18 * P[2] + 6 * P[3]) * h * h * h; // B'''(0)h^3
int nsegs = 10; // number of points we want to compute on the curve
point b = b0; // first position on the curve
for (int i = 1; i &lt;= nsegs; ++i) {
    // compute f(x+h), the next point at x = h * i
    f = f + fph + fpphh / 2 + fppphhh / 6;
 
    // compute f'(x+h) and f''(x+h)
    fph = fph + fpphh + fppphhh / 2;
    fpphh = fpphh + fppphhh;
}
```

In conclusion, we have just shown that starting from a set of initial values, we can compute points along a Bézier curve using additions and divisions only (equation 9). This code can further be optimized if needed by removing the divisions in the loop at the expense of making the code longer (this is left as an exercise).

## Source Code

We now give the C++ implementation of the fast-forward difference algorithm to compute Bézier curves and surfaces. In the main function (line 28), we first compute a series of points in the V direction (lines 32-35) to create the auxiliary curves along the U direction from which we can compute a series of points lying on the Bézier surface. The points along the curve are calculated using the algorithm described in this chapter (lines 1-15). For reference, you can also implement Maclaurin's method (lines 17-24). PS: this code was written for clarity, not efficiency.

```
void evalBezierCurveFFD(const uint32_t &divs, const Vec3f &P0, const Vec3f &P1, const Vec3f &P2, const Vec3f &P3, Vec3f *B) 
{ 
#if 1 
    float h = 1.f / divs; 
    Vec3f b0 = P0; 
    Vec3f fph = 3 * (P1 - P0) * h; 
    Vec3f fpphh = (6 * P0 - 12 * P1 + 6 * P2) * h * h; 
    Vec3f fppphhh = (-6 * P0 + 18 * P1 - 18 * P2 + 6 * P3) * h * h * h; 
    B[0] = b0; 
    for (uint32_t i = 1; i <= divs; ++i) { 
        B[i] = B[i - 1] + fph + fpphh / 2 + fppphhh / 6; 
        // update bd, bdd
        fph = fph + fpphh + fppphhh / 2; 
        fpphh = fpphh + fppphhh; 
    } 
#else 
    Vec3f b0 = P0; 
    Vec3f bd0 = 3 * (P1 - P0); 
    Vec3f bdd0 = (6 * P0 - 12 * P1 + 6 * P2); 
    Vec3f bddd0 = (-6 * P0 + 18 * P1 - 18 * P2 + 6 * P3); 
    for (uint32_t i = 0; i <= divs; ++i) { 
        float x = i / (float)divs; 
        B[i] = b0 + bd0 * x + bdd0 * x * x / 2 + bddd0 * x * x * x / 6; 
    } 
#endif 
} 
 
void evalBezierPatchFFD(const uint32_t &divs, const Vec3f *controlPoints, Vec3f *&P) 
{ 
    // generate grid
    Vec3f controlPointsV[4][divs + 1]; 
    for (uint16_t i = 0; i < 4; ++i) { 
        evalBezierCurveFFD(divs, controlPoints[i], controlPoints[4 + i], 
            controlPoints[8 + i], controlPoints[12 + i], controlPointsV[i]); 
    } 
    for (uint16_t i = 0; i <= divs; ++i) { 
        evalBezierCurveFFD(divs, controlPointsV[0][i], controlPointsV[1][i], 
            controlPointsV[2][i], controlPointsV[3][i], P + i * (divs + 1)); 
    } 
}
```

![Figure 2: a render of Newell's teapot (each of the 32 Bézier patches has a different color).](/images/bezier/teapotpatches.png?)

The source of the updated ray tracer is available in the download section. You will also need the files from lesson 8. To compile the program, follow the instructions from lesson 8.

The bad news, though, is that on modern processors and compilers, there is almost no difference (in terms of execution time) between this code and the code we used in chapter 2. In the early days of computer graphics, though, anything that could save some CPU cycles was considered worth it hence the success of the fast-forward differencing technique. It is not so much the case anymore today, but having a chance to use it here in this chapter to convert Bézier patches to poly grids, is a perfect opportunity to learn about it by showing how the technique applies to a practical case, as well as learn about Taylor series which are used in many other standard computer graphics algorithms.