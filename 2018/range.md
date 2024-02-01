**Range Proofs**

〚 🔢 〛



Bit Decomposition

- Assume a subset membership proof {0,1, …}
  - Given commitment to x: [[x]] 
  - Given range: [0, … , 2^(d)-1]
  - Given a subset membership proof {0, 1, … }
- Naive approach
  - Use subset membership proof to prove [[x]] is in {0, 1, … , 2^(d)-1}
- Divide and conquer
  - Decompose x into bits: [[b0]] [[b1]] … [[bd-1]]
  - Count commitments and ensure no more than d (verifiable by inspection) 
  - Prove each bit is {0,1} with d subset membership proofs
  - Prove [[x]] matches [[b0]] [[b1]] … [[bd-1]]
    - Done with additive homomorphism
    - ∑ [bi]*2^(i)



Square Decomposition

- 4 squares theorem
  - Every zero/positive integer can be written as the sum of 4 squares
  - Find {x1, x2, x3, x4} such that x = x1^2 + x2^2 + x3^2 + x4^2
  - xi can be 0
- Prove [[x]] is between [a,b]
  - Generate [[x-a]] and show this is non-negative (just [[x]] for a=0)
  - Generate [[b-x]] and show this is non-negative when b is a natural integer (Z) rather than a modular amount. 
  - Requires a special commitment scheme that shows [[x]] is a commitment over the integers Z and not just Zp
    - RSA setting typically but some complex later works might work
    - Patch with cross-group commitments???



Inner Product

- Subset membership for {0,1}
- Prove [[x]] is {0,1}
  - Choose y
    - If x=0, set y=1
    - If x=1, set y=0
  - Produce [[y]]
  - Prove [[x]]*[[y]] = 0
  - Prove [[x]]+[[y]] = 1
  - Theorem is [[x]] is any value other than 0 or 1, there is no single value of y that satisfies both equations simultaneously



Polynomials

- Given P() on some domain
- Compute Z() = P()(1-P()) and show it is vanishing polynomial on domain