# Range Proofs

#### Subset Membership

A subset membership proof takes a committed value $[[x]]$ and a publicly known set of values like $\langle 5,67,302,145,678 \rangle$ and demonstrates $x$ is one of these values without revealing which one.

A range proof is a special case of a subset membership proof where the set is $\langle 0,1,2,3,\ldots,\mathsf{max} \rangle$ up to a maximum number $\mathsf{max}$. It however is generally not efficient and is linear in the size of the set.

#### Bit Decomposition

This approach addresses the inefficiency of a subset membership proof by performing a smaller subset membership multiple times. The smallest (non-trival) subset membership proof is between two values, such as $\langle 0,1\rangle$. The idea here is provide a "bit decomposition" of $[[x]]$ which means convert $x$ into a binary form and give individual commitments to each bit $\langle [[b_0]], [[b_1]], [[b_2]], \ldots \rangle$. To use this as a range proof, the value of $\mathsf{max}$ is taken to be (near) a perfect power of two, $2^{d}$-1, and we prove $x$ is between $0$ and $2^{d}-1$ (If $\mathsf{max}$​ needs to be something more specific, we can accommodate that but set that detail aside for now).

The proofs consists of three ideas:

1) If only $d$ bits are given, the number cannot be larger than $2^{d}-1$. Since anyone can count the number of commitments, anyone can know there are only $d$ commitments.
2) If $d$ commitments are given, we need to verify they are actually bits and not commitments to larger numbers. For this, we can do $d$ subset membership proofs between $[[b_i]]$ and $\langle 0,1 \rangle$. Notice that the set is size $2^d$ but we are getting a proof for with only $d$ (as opposed to $2^d$) cases (this is sometimes called divide-and-conquer).
3) To issue a range proof for $[[x]]$ (the entire number), we have to show that $\langle [[b_0]], [[b_1]], [[b_2]], \ldots \rangle$ is the proper bit decomposition of $x$. Anyone can check this, starting with the committed bits and building them back into $[[x]]$, if the commitment scheme is additively homomorphic: $[[x]]=\sum_{i=0}^{d-1} [[b_i]]*2^i$  where the values of $2^i$ are publicly known (i.e., they are just the powers of two: $1, 2, 4, 8,16 \ldots$).

To adjust the range from $[0,2^{d-1}]$ to the range $[\mathsf{min},\mathsf{max}]$​ for any values, one can tk



#### Subset Membership of {0,1}

**Disjunction**

* Prove x=0 or x=1 using an "or" or "disjunctive" proof
* Possible with $\Sigma$-protocols

**Inner Product**

* Choose y
  - If x=0, set y=1
  - If x=1, set y=0
* Produce [[y]]
* Prove [[x]]*[[y]] = 0
* Prove [[x]]+[[y]] = 1
* Theorem is [[x]] is any value other than 0 or 1, there is no single value of y that satisfies both equations simultaneously

**Multiplication**

* Compute $[[z]]=([[x]]-0)\cdot([[x]]-1)$​ 
* Open $[[z]]$ which will be 0 when $x=0$ or when $x=1$, and will not be 0 for any other value. 

**Polynomials**

* Compute Z() = P()(1-P()) and show it is vanishing polynomial on domain



#### Square Decomposition

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



#### Inner Product

- Subset membership for {0,1}
- Prove [[x]] is {0,1}
  - 



#### Polynomials

- Given P() on some domain
- 