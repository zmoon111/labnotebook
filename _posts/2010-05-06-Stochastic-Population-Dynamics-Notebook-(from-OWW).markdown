---
layout: post
tags: [warning-signals, code-tricks]
categories: [ecology]
---





 





Goals
-----

-   Model choice exercise with sdes
-   general likelihood calculation from simulation

Test Case
---------

*d**X*~*t*~ = α~*t*~(θ − *X*~*t*~)*d**t* + σ*d**t*

*d*α~*t*~ = − β

\

Approach
--------

-   The linear approximation to the warning signals dynamics can be
    captured by the OU process, look for changing α
-   SDE models will also provide some of the coarser approximations for
    the structured population dynamics. Formulations of these still to
    do.

Conditional probability
-----------------------

Solution to the time dependent OU process, see Gardiner 4th ed. pg 111,
eq. (4.5.109).

Solve using ![ f(X\_t) = X\_t \\exp\\left( - \\int\_0\^t \\alpha(s) ds
\\right)
](http://openwetware.org/images/math/2/6/1/261158506a921c58c07cce2014f09198.png)
and apply [Ito
formula](http://en.wikipedia.org/wiki/It%C5%8D%E2%80%93Doeblin%27s_formula "http://en.wikipedia.org/wiki/It%C5%8D%E2%80%93Doeblin%27s_formula"),
giving

![ X\_t = X\_0 \\exp\\left( - \\int\_0\^t \\alpha(s) ds \\right)
+\\theta\\left( 1- \\exp\\left( - \\int\_0\^t \\alpha(s) ds \\right)
\\right) + \\int\_0\^t \\sigma \\exp\\left( - \\int\_0\^t \\alpha(s) ds
\\right) dW\_t
](http://openwetware.org/images/math/8/b/2/8b2d9515dd6a89360e9c97a122103193.png)

And the moments are:

\

![ \\begin{align} E(X\_T) &= X\_0 \\exp\\left( - \\int\_0\^T \\alpha(s)
ds \\right) +\\theta\\left( 1- \\exp\\left( - \\int\_0\^T \\alpha(s) ds
\\right) \\right) \\\\ \\operatorname{Var}(X\_T) &= \\sigma\^2
\\int\_0\^T \\exp\\left( -2 \\int\_0\^t \\alpha(s) ds \\right) d t
\\end{align}
](http://openwetware.org/images/math/e/5/a/e5ab85ca602fb12998c2eb985474c7e6.png)

-   Using the exact solution I can write down the conditional density
    function. We can evaluate both integrals for this linear increase
    model:

![ \\int\_0\^t (\\beta t +\\alpha\_0 ) dt = \\beta t\^2/2 + \\alpha\_0 t
](http://openwetware.org/images/math/e/8/f/e8f494fb0fc3fffba3ebbf121a70100a.png)

![ \\int\_0\^t e\^{ - \\beta s\^2 - 2\\alpha\_0 s} ds =
\\frac{\\sqrt{\\pi}e\^{\\alpha\_0\^2/\\beta} }{2\\sqrt{\\beta}} \\left(
\\operatorname{erf} \\left( \\frac{ \\alpha\_0+\\beta t }{
\\sqrt{\\beta} } \\right) - \\operatorname{erf} \\left( \\frac{
\\alpha\_0 }{ \\sqrt{\\beta} } \\right) \\right)
](http://openwetware.org/images/math/7/a/2/7a2b755c9174107368e4480c60e685b4.png)

\

-   Next, implement the conditional probability density in R:

~~~~ {.de1}
numeric_V <- function(Dt, pars){
    vint <- function(x){
        exp(-pars$beta*x^2 -2* pars$alpha_0*x)
    }
    int <- integrate(vint, 0, Dt)
    int$value*pars$sigma^2
}
 
analytic_V <- function(Dt, pars){
    if( erf(pars$alpha/sqrt(pars$beta) ) != 1){
 
        Log_Vx <- log(pars$sigma^2 *  sqrt(pi) ) + pars$alpha_0^2/(pars$beta)  + log( ( erf( (pars$alpha_0+pars$beta*Dt)/(sqrt(pars$beta) ) ) - erf(pars$alpha_0/(sqrt(pars$beta) ) ))/(2*sqrt(pars$beta) ))
        class(Log_Vx) = "numeric"
        Vx <- exp(Log_Vx)
    } else {
        warning("beta near zero, using approximation")
        Vx <- pars$sigma^2*(1-exp(-2*pars$alpha*Dt))/(2*pars$alpha)
 
    }
        Vx
}
 
# Parametrization dXt = alpha(theta - Xt)dt + sigma*dWt, alpha(t) = beta*t+alpha_0
warning_model <- function(Dt, Xo, pars){
    int <- pars$beta*Dt^2/2 + pars$alpha_0*Dt
    Ex <- Xo * exp(-int) + pars$theta * (1 - exp(-int) )
    Vx <- analytic_V(Dt, pars)
 
    return(list(Ex=Ex,Vx=Vx))
}
 
dcWarning <- function(x, Dt, x0, pars, log = FALSE){
  P <- warning_model(Dt, x0, pars)
  dnorm(x, mean=P$Ex, sd=sqrt(P$Vx), log=log)
}
 
 
warning.lik <- function(alpha_0, theta, sigma, beta){
  n <- length(X)
  dt <- deltat(X)
  pars = list(alpha_0=alpha_0, theta=theta, sigma=sigma, beta=beta)
  -sum( dcWarning(X[2:n], dt, X[1:(n-1)], pars, log=TRUE) )
}
 
 
require(stats4)
require(sde)
require(NORMT3)
 
set.seed(123)
theta <- 3
alpha <- 1
sigma <- 2
alpha_0 <- 1
beta <- .01
pars = list(alpha_0=alpha, theta=theta, sigma=sigma, beta=beta)
 
warning_model(1, 1, pars)
setOU(1, 1, theta=c(theta*alpha, alpha, sigma) )
 
numeric_V(Dt, pars)
analytic_V(Dt, pars)
~~~~

\

#### Note on Numerics

-   Unfortunately the analytic solution does not have nice numerical
    properties for small β, which is the interesting limiting case
    (no/very slow change in stability). The error functions both
    approach unity, making the difference tiny, while the exponential
    term diverges. The result is that variances for too small beta
    suddenly become zero, instead of the appropriate limit. The
    numerical integration is more robust to this. Analytic
    implementation is modified to just provide the limit case when erf
    functions both evaluate at exactly 1.

-   This linear test case model is quite restrictive. If the eigenvalue
    is decreasing, doing so in a linear way need not out perform a model
    in which it is not decreasing at all. It would be interesting to
    test if this is more sensitive than change point estimation. Iacus
    (2008) implements a least-squares based change point estimation in
    the R sde package.

\

Misc Notes
----------

-   Upgraded to Ubuntu 10.04. Went fine on the experimental partition
    with a reformat to extension 4 and a fresh install. Fast boot times,
    spiffy system. Full upgrade on main (ext3) partition failed
    miserably though, can't even boot up successfully following the
    upgrade.

-   Adding additional packages when fine, mostly copied configuration
    files over. A couple extra steps to get dual monitors working again,
    as the resolution wasn't properly detected. [Solution
    here](http://ubuntuforums.org/showthread.php?t=1112186 "http://ubuntuforums.org/showthread.php?t=1112186"),
    essentially:

~~~~ {.de1}
 #!/bin/bash         
 xrandr --newmode "1920x1200_60.00" 193.16 1920 2048 2256 2592 1200 1201 1204 1242 -HSync +Vsync
 xrandr --newmode "1280x1040_60.00"  110.63  1280 1360 1496 1712  1040 1041 1044 1077  -HSync +Vsync
 xrandr --addmode VGA1 1920x1200_60.00
 xrandr --addmode VGA1 1280x1040_60.00
~~~~

\

-   vim-full is deprecated, vim-latexsuite needs additional
    installation, read: /usr/share/doc/vim-latexsuite/README.Debian
    after installing. Also vim's "+y copy to clipboard doesn't work with
    pure vim, so installing xclip vim-common and vim-gnome to fixed
    this.

\

