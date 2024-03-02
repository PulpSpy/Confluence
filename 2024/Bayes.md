Given a theory t and pieces of evidence e1, e2, and e3, what is new probably of t being correct?



Pr[ t | e ] = ell * Pr[t]



Pr[t] is called the prior or baseline probability. It is the probability that t is true before you consider the evidence. It is a common fallacy to not appreciate the significance the prior has on the result. Given a very low prior (e.g., super rare disease), even with strong evidence (a positive result from an accurate test), the probability of t (that you have the disease) might stay small.



Pr[t|e] is called the posterior probability. It is the “updated” value of Pr[t] after considering the evidence.



ell is a scaling function that will increase, decrease, or not impact Pr[t] depending on how supportive the evidence is for t. 



ell is not a probability itself. If ell=0, then the probability goes to zero and t and e are mutually exclusive. If ell is between 0 and 1, the probability of t decreases (thus e does not support t). If ell is exactly 1, then t and e are independent. If ell is between 1 and a max value we’ll call max for now, then the probability of t increases (thus e supports t). If ell is max, then the probability goes to 1 (certainty) and e proves t. 



What is max? Well, it is a bit ugly because Pr[t|e] is a probability and thus can only go to one. So it is going to be some ratio between Pr[t|e] and Pr[t]. Specifically it is 1/Pr[t]. 



== Liklihood



So how do we compute ell? It requires us to know something about the evidence e, its probability and how it is linked to t. There are a few formulations of ell (obviously equivalent) that depend on what you want to estimate to compute it.



Variant 1: ell( Pr[e|t], Pr[e]) = Pr[e|t]/Pr[e]

Variant 2: ell( Pr[e|t], Pr[e|!t] ) = Pr[e|t]/(Pr[e|t]*Pr[t] + Pr[e|!t]*Pr[!t])



Pr[e] - the probability of seeing the evidence (marginal probability)

Pr[e|t] - assuming the theory is true, what is the probability this evidence will come to light (likelihood)

Pr[e|!t] - assuming the theory is not true, what is the probability this evidence will come to light



In terms of a filter, Pr[e|!t] is the probability of a false positive and 1-Pr[e|!t] is the true negatives. Pr[e|t] is the true positives and 1-Pr[e|t] is the probability of a false negative. 



Example: probability email is spam because spam filter labels it spam. Key influences (variant 2):

1. the probability of spam in the first place: if spam is super rare, the probability this is spam is low
2. the probability of spam being labeled spam (accuracy or true positive)
3. the probability of non-spam being mislabeled as spam (false positive)



Note: It is tempting to think Pr[e|t] + Pr[e|!t] = Pr[e]. This is not the case:

Pr[e|t]*Pr[t] + Pr[e|!t]*Pr[!t] = Pr[e]



Example: probability the coin will flip heads given it is raining. Say the probability it is raining is 30%. Then the probability it is raining given the coin flips heads is the same as if it flips tails. So Pr[e|t] = Pr[e|!t]. We can consider these two values as a ratio (odds or likelihood ratio): o=Pr[e|t]/Pr[e|!t]. If o=1, e and t are independent. If o>1, then it e supports t. If o<1, then it does not support t. 