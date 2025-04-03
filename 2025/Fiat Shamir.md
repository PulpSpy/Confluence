# Fiat-Shamir Attacks

## Schnorr $\Sigma$-Protocol

### Review

Recall Schnorr's proof of knowledge of a discrete logarithm protocol, implemented in a group $G$ of prime order $q$, with generator $g$. The discrete logarithm problem is hard in $G$.

The statement being proven is knowledge of a secret $x \in \mathbb{Z}_q$ such that $y = g^x$. In Camenisch–Stadler notation:
$$
\text{PK}\{(x) : y = g^x\}
$$
We consider the public parameters describing the group as $\mathsf{pp}=\langle G,q,g \rangle$ and the statement as $\mathsf{stmt}=\langle y \rangle$. 

The (correct) Schnorr prover's protocol, made non-interactive with strong Fiat-Shamir is as follows.

1. Choose secret random $a \in \mathbb{Z}_q$ and output  $b = g^a$
2. Compute and output challenge $c=\mathcal{H}(\mathsf{pp},\mathsf{stmt},b)$ for hash function $\mathcal{H}$
3. Compute and output $d = a + c \cdot x \mod q$

The verifier checks:

1. $g^d \stackrel{?}{=} b \cdot y^c$ 

### Forgeries

Notice that if the prover can predict the challenge value $c$ before running the prover protocol, the prover can forge a valid proof for any $y$ without needing (or knowing) the witness value $x$. The hash function in Fiat Shamir prevents this, but consider the interactive format of Schnorr where the verifier chooses $c$ at random and someone the prover can predict which $c$ the verifier is going to pick.

The trick to compute the values out-of-order, starting with $d$ and then computing $b$. This is not an "attack" per se, because real executions of the protocol will enforce that values are computed in the correct order.

1. Malicious prover learns value $c$
2. Malicious prover choses a random value $d$
3. Malicious prover computes $b=g^d y^{-c}$ (which is the verifier's check rearranged)

The key property to knowing a proof is real (or sound) and not forged is knowing that $b$ was generated before the prover knew the value of $c$.  Note also that the prover does not know $a$ in a forged proof (it computes $b$ directly).

The fact that forgeries can be computed efficiently without knowledge of the witness indirectly proves the proof transcript itself cannot leak knowledge of the witness. In this case, the malicious prover is called a simulator and proving things out of order is permitted.

### Attack 1: Exhaustive Search

The easiest attack on Fiat-Shamir is exhaustive search. The prover will guess what the value of $c$ is, create a forgery, and then once it has $b$ it will check if it just so happens that its guessed value of $c$ satisfies $c\stackrel{?}{=}\mathcal{H}(\mathsf{pp},\mathsf{stmt},b)$. If so, it is done. If not, it tries again with a new random $c$ and/or $d$. Since only one value of $c$ satisfies the equation, the exhaustive search is over all acceptable values of $c$. Protecting against this attack is simple: ensure $c$ is large enough that it cannot be brute-forced. 

Where this attack can work is when the challenge space is small, say in a system that only requires 10 or 20-bit challenge values. This might not be a zero-knowledge proof or argument, but a similar kind of protocol like "cut and choose."

### Attack 2: Weak Fiat-Shamir

This attack arrises when the challenge value $c=\mathcal{H}(\mathsf{pp},\mathsf{stmt},b)$ from step 2 includes $b$ but does not include $\mathsf{stmt}$ which recall is $y$. Because the proof is not stapled to a specific value of $y$, a malicious prover can use an attack to find an accepting transcript for some $y$ (that will be output post hoc by the technique). This will not be a general purpose attack because the adversary cannot prove knowledge of $x$ for a chosen value of $y$. 

1.	Pick arbitrary $b \in \mathbb{Z}_q$ and compute $c=\mathcal{H}(b)$

2. Pick arbitrary $d \in \mathbb{Z}_q$

 	3.	Compute $y = \left( g^d \cdot b^{-1} \right)^{1/c}$

We can check that this satisfies the verifier's equation but substituting in $y$:

* $g^d \stackrel{?}{=} b \cdot y^c$ 
* $g^d \stackrel{?}{=} b \cdot [\left( g^d \cdot b^{-1} \right)^{1/c}]^c$ 
* $g^d \stackrel{?}{=} b \cdot g^d \cdot b^{-1}$ 
* $g^d \stackrel{\checkmark}{=} g^d $ 

### Attack 3: Sequential Composition

Consider a proof of knowledge of $x$ such that $y = g^x$, where instead of using one Schnorr protocol, we perform $t$ sequential repetitions to reduce soundness error from $1/q$ to $(1/q)^t$ in the interactive case.

In each repetition $i$:

1. The prover chooses random $a_i \in \mathbb{Z}_q$ and computes $b_i = g^{a_i}$.
2. The verifier sends a challenge $c_i \in \mathbb{Z}_q$.
3. The prover responds with $d_i = a_i + c_i \cdot x \mod q$.
4. The verifier checks $g^{d_i} \stackrel{?}{=} b_i \cdot y^{c_i}$.

This sequential repetition is sound in the interactive setting: the prover does not know $c_i$ ahead of time, and each challenge cuts their forgery probability by a factor of $1/q$.

To eliminate interaction using Fiat-Shamir, we define:

$$
c_i = \mathcal{H}(\mathsf{pp}, \mathsf{stmt}, b_1, \ldots, b_t, i)
$$

and the final proof is:

$$
\pi = \left\{ (b_i, d_i) \right\}_{i=1}^t
$$

Verifier recomputes $c_i = \mathcal{H}(\mathsf{pp}, \mathsf{stmt}, b_1, \ldots, b_t, i)$ and verifies each round as before.

The issue is that the prover chooses all $b_i$ values before the challenges $c_i$ are fixed, because the challenges are derived from the full transcript. Thus:

1. The prover samples many different sets of $a_i$ (and thus $b_i$) values.
2. For each full transcript $(b_1, \ldots, b_t)$:
   - Computes challenges $c_i = \mathcal{H}(\cdots)$.
   - Attempts to compute fake responses $d_i$ such that
     $$
     d_i = a_i + c_i \cdot x_i \mod q
     $$
     for some guessed or simulated $x_i$ values
3. Repeats until a full proof passes verification

Since there are $t$ challenges, and the prover can search over all $2^t$ possible transcripts, the number of effective attempts increases exponentially in $t$. As a result, instead of $k \cdot t$ bits of soundness (with $k$-bit challenges), the non-interactive version has only approximately $k \cdot t - t$ bits of soundness. That is, about one bit of security is lost per round.
