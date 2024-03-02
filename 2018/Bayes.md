## Primer on Bayes theorem

Given a theory $t$ which is correct with some probability, and a new piece of evidence $e$, what is new probably of $t$ being correct?

$\mathsf{Pr}[t\mid e] = \ell * \mathsf{Pr}[t]$​

* $\mathsf{Pr}[t]$ is called the *prior* or *baseline* probability. It is the probability that $t$ is true before you consider the evidence. It is a common fallacy to not appreciate the significance that the prior has on the result. Given a very low prior (e.g., a super rare disease), even with strong evidence (a positive result from a very accurate test), the probability of $t$ (that you have the disease) might stay smaller than you would expect.[^1]

* $\mathsf{Pr}[t\mid e]$ is called the *posterior* probability. It is the “updated” value of $\mathsf{Pr}[t]$ after considering the evidence.

* $\ell$ is a scaling function that will increase, decrease, or not impact $\mathsf{Pr}[t]$ (with respect to $\mathsf{Pr}[t\mid e]$) depending on how supportive the evidence is for $t$.

The value $\ell$ is not a probability itself. We will define it below. For now think about how it works:

* If $\ell=0$, then $\mathsf{Pr}[t\mid e]$ will go to 0. It means that $e$ and $t$​ are mutually exclusive. 
* If $0<\ell<1$, the probability $\mathsf{Pr}[t\mid e]$ will decrease from $\mathsf{Pr}[t]$. It means that $e$ does not support $t$. 
* If $\ell=1$,  the probability $\mathsf{Pr}[t\mid e]$ will stay the same as $\mathsf{Pr}[t]$. It means that $e$ and $t$ are independent.
* If $1<\ell<\mathrm{max_\ell}$, the probability $\mathsf{Pr}[t\mid e]$ will decrease from $\mathsf{Pr}[t]$. It means that $e$ supports $t$. The biggest value $\ell$ can take on is not intuitive; call it $\mathrm{max_\ell}$ for now.
* If $\ell=\mathrm{max_\ell}$, then $\mathsf{Pr}[t\mid e]$ will go to 1. It means that $e$ proves $t$ is correct.

What is $\mathrm{max}_\ell$? It is $\frac{1}{\mathsf{Pr}[t]}$ which means that $\ell$ is "aware" of the value of $t$ and its probability $\mathsf{Pr}[t]$.

### Likelihood

So how do we compute $\ell$? It requires us to know something about the evidence $e$, its probability and how it is linked to $t$. There are a few formulations of $\ell$ that depend on what you know about the state of the world where $t$ is not true.

Variant 1: 

$$\ell = \frac{\mathsf{Pr}[e\mid t]}{\mathsf{Pr}[e]}$$



Variant 2:

$$\ell = \frac{\mathsf{Pr}[e\mid t]}{\mathsf{Pr}[e\mid t]\cdot Pr[t] + \mathsf{Pr}[e\mid !t]\cdot Pr[!t]}$$



These probabilities are as follows:

* Pr[e] - the probability of seeing the evidence (marginal probability)

* Pr[e|t] - assuming the theory is true, what is the probability this evidence will come to light (likelihood)?

* Pr[e|!t] - assuming the theory is not true, what is the probability this evidence will come to light?

Note: It is tempting to think $Pr[e|t] + Pr[e|!t] = Pr[e]$. This is not the case: $Pr[e|t]\cdot Pr[t] + Pr[e|!t]\cdot Pr[!t] = Pr[e]$



If you want to think in terms of errors:

* Pr[e|!t] is the probability of a false positive
* 1-Pr[e|!t] is the probability of a true negative
* Pr[e|t] is the probability of a true positives
* 1-Pr[e|t] is the probability of a false negative



[^1]: Psychologists like Kahneman and Tversky demonstrated humans are bad at this, which was a one of a set of results that netted Kahneman a Noble prize in economics (Tversky was no longer living or would have won as well).