Who is Satoshi Nakamoto?
Stub of the article by Jeremy Clark (Concordia University)
Open to collaboration

The cryptocurrency Bitcoin was created by an individual, or collection of individuals, using the name Satoshi Nakamoto. In October 2018, Satoshi released a whitepaper describing the Bitcoin protocol. Later he released a software package for running the protocol, and spent around two years maintaining the software, communicating with users over email, and promoting the currency in online forums.

Many have been captivated by the question of who Satoshi really is, including the media. At least a dozen guesses, with varying degrees of journalistic evidence, have been made in print by reputable journalists working for the New York Times, Newsweek, The New Yorker, Wired, Forbes, Fast Company, and others. 

Despite the important consequences an answer to this question might hold, the topic of Satoshi’s identity is considered to be in the realm of speculation, gossip, and circumstantial evidence. We challenge this notion by attempting to say something scientific about the subject of Satoshi’s identity. It seems inevitable that the media will continue positing new Satoshi’s, while academics will throw their arms in the air and say, “probably not but who can really know?”

We believe that we owe the media and others a better response. In this article, we encourage interested parties to follow the framework of Baysian reasoning when thinking through the question of who Satoshi is, evaluating the likelihood of a particular candidate, and determining what kind of evidence is sufficient for such a claim. 
Bayes Primer
Bayes theorem is a mathematical relationship between probabilities that most people who have finished high school mathematics have seen but long forgotten. Think of it like a box sitting on a table. 

  at least glanced at and maybe committed to memory  usually glanced at appreciated 
En vogue, lots exist, keep it short


Non-Bayesian Approaches
Checklist
Lord, Liar, Lunatic, Legend
Occam’s Razor

COPYRIGHT AVOIDANCE


“Kaminsky ticked off the skills Nakamoto would need to pull it off. “He’s a world-class programmer, with a deep understanding of the C++ programming language,” he said. “He understands economics, cryptography, and peer-to-peer networking. Either there’s a team of people who worked on this,” Kaminsky said, “or this guy is a genius.” Source
A Checklist Approach

Suitable
Crypto
Baseline knowledge
Restraint
Developer
C++ Application Dev
P2P and Network Protocol


Economics
Smart contracts
Consensus
Philosophy
Cypherpunk?
Crypto mailing list / white paper
Active on mailing list

“The idiosyncratic construction of Bitcoin’s code also suggests that Satoshi had an academic background. It has been described as “brilliant but sloppy”, eschewing conventional software development practices like unit testing but exhibiting cutting-edge security architecture and an expert understanding of academic cryptography and economics.” Source

Circumstantial: 
Timezone
Clusters
British slang
Double space after period
Japanese name
Crypto mailing list 

Flawed scientific
Flow approach - Shamir et al - Dustin Trammell
Stylometry - Patents 

A Bayesian Approach: existing evidence

Establishing a prior probability
Base rate fallacy
Drunkard and lost keys
Example: Linus Torvalds - Finnish grad student, must have been a team, guess other high profile OS people: Ken Thompson, Dennis Ritchie (disregards why they would rival their own) or most colourful: Richard Stallman

Demographics
Craig Wright 
Andy Greenberg and Gwern Branwen, Wired 
Sam Biddle and Andy Cush, Gizmodo
Andrew O’Hagan, London Review of Books
Nick Szabo
Nathaniel Popper, New York Times 
Michael Clear
Joshua Davis, New Yorker
Dorian Nakamoto
Leah McGrath Goodman, Newsweek
Hal Finney
Andy Greenberg, Forbes

Evidence
At Crypto, 

A Bayesian Approach: chosen evidence

Sytlometry
Signed with keys



Techniques and critique
Antedote: critique with my bio, chaum, double space, canada // drunkard (linux)
Sytlometry: not understanding FP // rarest disease on earth // drunkard
Signing: only thing real
Personal interview: not reliable
IP and emails: reliable. CBC says 68.165.57.219 (I think this is wrong?) can points it to LA (Passionate Eye); also says IP of first transaction, that can’t be right. Other sources say 68.164.57.219
Location: Cali, Hal, Dorian -> correlation/causation -> third party
Bayes and Lewis thing: liar, lunatic, legend, lord -> Craig Wright
Cohesion in single person
Counterfuge: this we need to think about. Should be British, because bloody and times of london. BUT IF YOU WRE SATOSHI, wouldn’t you exactly want to do that to throw people off? Wouldn’t you want to converse with yourself (Hal)? Best way for Hal to hide being Satoshi is to go wholehog into Bitcoin (as oppose to never mention it ever?)
Useful links:

Did Kavanaugh Do It?. Math Helps Us Decide | by Eric Saund | Medium

Who Wrote The <em>Death Note</em> Script? · Gwern.net

Model Comparison and Occam's RazorCSW

Craig Wright is not Satoshi Nakamoto



08 Oct 07 - DAK & CSW book

?? Dec 08 - DAK & CSW conf paper

03 Jan 09 - Genesis block

21 Jan 09 - DAK mail list

25 Mar 10 - DAK mail list (last)

30 Apr 13 - DAK obituary

07 Feb 14 - MT Gox suspends 

?? Feb 14 - CSW contacts DAK family

12 Feb 14 - DAK father post

08 Sep 14 - P2P account hack warning

?? Nov 15 - Tips to Gizmodo





JVP @haq4good - story about conf, backtracked on it being CSW (BitcoinBelle)

Iang @iang_fc 

@dr_craig_wright -> iang & JVP & XS

Jon Mateos 



https://medium.com/hackernoon/bitcoin-belles-ccme-the-woman-who-brought-you-craig-satoshi-wright-strikes-again-f74e4ef129a4





=============

Bayes Thereom

=============



== 



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