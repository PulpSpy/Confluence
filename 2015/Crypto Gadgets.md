# Crypto Gadgets

A collection of gadgets for computing and proving.

# Two Party Computation (2PC)

Generally these protocols:

* denote encryption/commitment/shares of $x$ as $\boxed{x}$​
* assume addition (mod q) is free
* are honest-but-curious only
* follow the paradigm of: 
  * Alice blinds the data
  * Bob unblinds the data, does something with it, and reblinds it
  * Alice does something with it and unblinds it
  * If Alice sees what Bob sees, or vice-versa, privacy is broken.



## NAND

Represent every 0 as 01 and 1 as 10. Thus:

* $\mathsf{in}_l=\langle \mathsf{in}_{l,0},\mathsf{in}_{l,1} \rangle$

* $\mathsf{in}_r=\langle \mathsf{in}_{r,0},\mathsf{in}_{r,1} \rangle$​

Do the following:

* Trustee 1: shuffle the bits within $\mathsf{in}_l$ and remember
* Trustee 2: Open $\mathsf{in}_l$
  * If $\langle0,1\rangle$, output $\langle0,1,\mathsf{in}_{r,0},\mathsf{in}_{r,1}\rangle$ to T1
  * if $\langle1,0\rangle$, output $\langle\mathsf{in}_{r,0},\mathsf{in}_{r,1},0,1\rangle$ to T1
* Trustee 1:
  * If did not shuffle, output first two numbers
  * If shuffled, output last two numbers


Full logic table:

| in   | in   | shuffle | inter | out  |
| ---- | ---- | ------- | ----- | ---- |
| 0    | 0    | 0       | 0101  | 01   |
| 0    | 1    | 0       | 0110  | 01   |
| 1    | 0    | 0       | 0101  | 01   |
| 1    | 1    | 0       | 1010  | 10   |
| 0    | 0    | 1       | 0101  | 01   |
| 0    | 1    | 1       | 1001  | 01   |
| 1    | 0    | 1       | 0101  | 01   |
| 1    | 1    | 1       | 0110  | 10   |



## MULT

This is due to Cramer, Damgard, Neilson (CDN). 

Problem: compute $\boxed{x\cdot y}$  given $\boxed{x}$ and $\boxed{y}$

* Alice chooses $r_a$ and sends $\boxed{r_a}$
* Bob choose $r_b$ and sends $\boxed{r_b}$
* Both compute $\boxed{\tilde{x}}=\boxed{x}+\boxed{r_a}+\boxed{r_b}$ and recover $\tilde{x}$​
  * This requires recovery of $z$​ from an arbitrarily large message space
  * Exponential Elgamal, BGN, Pedersen, and similar cannot be used
  * Paillier and Shamir secret sharing can
  * If encryption is used, decryption key is shared 2-out-of-2 between Alice and Bob
* Alice computes and sends $\boxed{y_a}=r_a\cdot\boxed{y}$​
* Bob computes and sends $\boxed{y_b}=r_b\cdot\boxed{y}$​
* Both compute $\boxed{x\cdot y}=\tilde{x}\cdot\boxed{y}+\boxed{y_a}+\boxed{y_b}$​
  * $\tilde{x}\cdot y-y_a-y_b=(x+r_a+r_b)\cdot y-r_ay - r_by=xy$

The protocol is honest-but-curious but can be made malicious with a ZKP that covers the construction of $y_a$ (and conversely $y_b$). This is the same as showing that $\langle\boxed{1},\boxed{y},r_a\cdot\boxed{1},r_a\cdot\boxed{y}\rangle$​ is a DDH tuple (but you are probably not in the DL setting).

## SELECT

Due to Chou & Orlandi

Specific to Elgamal style encryption and presented as oblivious transfer.

Problem: Alice has $x_0$ and $x_1$. Bob has bit $b$. Output to Bob is $x_b$.

* Alice chooses $r_a$ and sends $y_a=g^{r_a}$
* Bob chooses $r_b$ and sends $y_b={y_a}^b g^{r_b}=\begin{cases}g^{r_b} & b=0 \\ y_ag^{r_b} & b=1 \end{cases}$
* Alice computes $k_0={y_b}^{r_a}$ and sends $c_0=\mathsf{Enc}_{k_0}(x_0)$​​
* Alice computes $k_1=(\frac{y_b}{y_a})^{r_a}$ and sends $c_1=\mathsf{Enc}_{k_1}(x_1)$
* Bob sets key to $k_b={y_a}^b=g^{r_ar_b}$ and decrypts $c_b$ 

Remarks

* Honest but curious in terms of constructing the values correctly
* In both cases, Bob gets an extra ciphertext encrypted with a junk key
  * When $b=0$, $c_1$ is encrypted with $k=g^{r_a(r_b-r_a)}$​
  * When $b=1$, $c_0$ is encrypted with $k=g^{r_a(r_b+r_a)}$​
  * Computing $g^{{r_a}^2}$ from $g^{r_a}$ is computationally infeasible



## Conditional

Due to Schoenmakers and Tuyls

Problem: given $\boxed{x}$ and bit $\boxed{b}$, return $\boxed{c}=\begin{cases}\boxed{-x}&b=0\\\boxed{x}&b=1\end{cases}$​

Protocol

* Map $\boxed{b}$ from $\{0,1\}$ to $\{-1,1\}$ : $\boxed{\hat{b}}=2\cdot\boxed{b}-1$​

* Alice chooses $r_a\in\{-1,1\}$ and multiplies it into $\boxed{x}$ and $\boxed{\hat{b}}$

* Bob chooses $r_b\in\{-1,1\}$ and multiplies it after Alice

* Result is $\boxed{x'}=r_a \cdot r_b \cdot \boxed{x}$ 

* Result is  $\boxed{b'}=r_a \cdot r_b \cdot \boxed{\hat{b}}$

* Recover $b'$ from $\boxed{b'}$​ and assert $b'\in\{-1,1\}$​

* Compute $\boxed{c}=b' \cdot \boxed{x'}$

  

## XOR

Due to Schoenmakers and Tuyls

Problem: given bits $\boxed{x}$ and $\boxed{y}$, compute $\boxed{z}=\boxed{x\oplus y}$​​

Protocol:

* Run $\mathtt{Conditional}(\boxed{x},\boxed{y})$ to obtain $\boxed{c}=\begin{cases}\boxed{-x}&y=0\\\boxed{x}&y=1\end{cases}$
* Compute $\boxed{z}=\boxed{y}-\boxed{c}$​ 

Full Logic Table:

| x    | y    | c    | y-c  |      |
| ---- | ---- | ---- | ---- | ---- |
| 0    | 0    | 0    | 0    |      |
| 0    | 1    | 0    | 1    |      |
| 1    | 0    | -1   | 1    |      |
| 1    | 1    | 1    | 0    |      |



## MULT (Binary)

Due to Schoenmakers and Tuyls

Problem: compute $\boxed{x\cdot y}$  given $\boxed{x}$ and $\boxed{y}$ and binary decomposition of $\boxed{x}: \boxed{x_0},\boxed{x_1},\ldots$

* Red tape: need decomposition of $x$ at start of protocol. You get $(x\cdot y)$ only as a whole integer at end of protocol. Thus you can only do this once.

Protocol:

* For each bit $\boxed{x_i}$, run $\mathtt{Conditional}(\boxed{y},\boxed{x_i})$ to obtain $\boxed{c_i}=\begin{cases}\boxed{-y}&x_i=0\\\boxed{y}&x_i=1\end{cases}$
* Map $\boxed{c_i}$ from $\{-y,y\}$ to $\{0,y\}$ by computing $\boxed{\hat{c_i}}=(\boxed{c_i}+\boxed{y})/2$
* Compute $\boxed{x\cdot y}=(2^0)\boxed{c_0}+(2^1)\boxed{c_1}+(2^2)\boxed{c_2}\ldots$





# Multiparty Computation (MPC) 

Generally these protocols:

* denote encryption/commitment/shares of $x$ as $\boxed{x}$​
* assume addition (mod q) is free
* are malicious or, more strongly, publicly verifiable
  * malicious means misbehaviour is detectable by participants in the protocol given a transcript
  * publicly verifiable means misbehaviour is detectable by anyone given a transcript



## NAND (LUT)

This is due to Jakobsson and Juels (Mix and Match)

Problem: given a lookup table (LUT) that implements function $f$, compute: $\mathsf{out}=f(\mathsf{in_l},\mathsf{in_r})$​

* Illustrated with a NAND, but works for any lookup table (multiple inputs, multiple outputs), however efficiency is proportional to size of table.

Protocols

* The trustees implement distributed/threshold encryption
* Leader trustee starts with the lookup table in plaintext:

| $\mathsf{In_l}$ | $\mathsf{In_r}$ | $\mathsf{Out}$ |
| --------------- | --------------- | -------------- |
| 0               | 0               | 1              |
| 0               | 1               | 1              |
| 1               | 0               | 1              |
| 1               | 1               | 0              |

* Each bit is encrypted and each trustee randomly shuffles the rows of the table (pre-computation, along with a Neff proof of shuffle)
* This is repeated for each LUT needed (once a table is used once, it is discarded)
* The final table might look as follows:

| $\mathsf{In_l}$ | $\mathsf{In_r}$ | $\mathsf{Out}$ |
| --------------- | --------------- | -------------- |
| $[[1]]$         | $[[1]]$         | $[[0]]$        |
| $[[0]]$         | $[[0]]$         | $[[1]]$        |
| $[[1]]$         | $[[0]]$         | $[[1]]$        |
| $[[0]]$         | $[[1]]$         | $[[1]]$        |

* When the trustees have two input values $\boxed{x}$ and $\boxed{y}$, they do a plaintext equality test (EQUAL under ZKTs below) between $\boxed{x}$ and each value in the first column of the shuffled lookup table. Then they take $\boxed{y}$ and do a plaintext equality with each value in the second column. For the row that evaluates true/true (second row in the example assuming $x=0$ and $y=0$), they output the encrypted value in the third column:

| $\mathsf{In_l}$ | $\mathsf{In_2}$ | $\mathsf{Out}$ |
| --------------- | --------------- | -------------- |
| False           | False           | $[[0]]$        |
| True            | True            | $[[0]]$ ✅      |
| False           | True            | $[[1]]$        |
| True            | False           | $[[1]]$        |



## NAND (LUT+SSP)

Adapted from Danezis, Fournet, Groth, Kohlweiss

Works in the trustees model

Illustrated for NAND gates but works for any binary gate with a change of equation.

Square spanning program (SSP): $\omega=\mathsf{in}_l+\mathsf{in}_r+2\cdot\mathsf{out}-2$. If and only if $\omega=\{0,1\}$, then $\mathtt{NAND}(\mathsf{in}_l,\mathsf{in}_r)=\mathsf{out}$. For proof systems, $\omega=\{0,1\}$ is a witness that the NAND gate was computed correctly. For MPC systems, we can compute two candidates for $\mathsf{out}$ called $\lambda_0$ and $\lambda_1$ as below. The (only) one in $\{0,1\}$ is the correct value of $\mathsf{out}$.

$\mathsf{out}=\frac{\{0,1\}+2-\mathsf{in}_l-\mathsf{in}_r}{2}$​

$\lambda_0=\frac{2-\mathsf{in}_l-\mathsf{in}_r}{2}$​

$\lambda_1=\frac{3-\mathsf{in}_l-\mathsf{in}_r}{2}$

Full logic table:

| $\mathsf{in}_l$ | $\mathsf{in}_r$ | $\mathsf{out}$ | $\omega$ | $\lambda_0$   | $\lambda_1$   |
| --------------- | --------------- | -------------- | -------- | ------------- | ------------- |
| 0               | 0               | 1              | 0        | 1             | $\frac{3}{2}$ |
| 0               | 1               | 1              | 1        | $\frac{1}{2}$ | 1             |
| 1               | 0               | 1              | 1        | $\frac{1}{2}$ | 1             |
| 1               | 1               | 0              | 0        | 0             | $\frac{1}{2}$ |

A lookup table for NAND is 4 rows. A lookup table for $\lambda_0$ is 3 rows. The drawback is the LUT cannot be constructed until $\lambda_0$ and $\lambda_1$ are known values, as they are outputs in the table, ruling out pre-computation as is done in Mix and Match. Still, this is 3 PETS worst case, 1.5 PETS average case. 

|   $\lambda_0$ | $\mathsf{out}$ |
| ------------: | -------------: |
|             0 |    $\lambda_0$ |
| $\frac{1}{2}$ |    $\lambda_1$ |
|             1 |    $\lambda_0$ |

This drawback (no pre-computation of LUTs) can be sidestepped with the following protocol. Encrypt a helper pair of bits 0 and 1 and shuffle them during precomputation. Thus $\chi=\langle\chi_0,\chi_1\rangle=\langle0,1\rangle$ or $\langle1,0\rangle$. Compute $z=\mathsf{in}_l+\mathsf{in}_r-2\cdot\chi_0$​.

Full logic table:

| $\mathsf{in}_l$ | $\mathsf{in}_r$ | $\chi_0$ | $\chi_1$ | $z$  | $\mathsf{out}$ |
| --------------- | --------------- | -------- | -------- | ---- | -------------- |
| 0               | 0               | 0        | 1        | 0    | $\chi_0$       |
| 0               | 1               | 0        | 1        | 1    | $\chi_0$       |
| 1               | 0               | 0        | 1        | 1    | $\chi_0$       |
| 1               | 1               | 0        | 1        | 2    | $\chi_1$       |
| 0               | 0               | 1        | 0        | -2   | $\chi_1$       |
| 0               | 1               | 1        | 0        | -1   | $\chi_1$       |
| 1               | 0               | 1        | 0        | -1   | $\chi_1$       |
| 1               | 1               | 1        | 0        | 0    | $\chi_0$       |

Create a NAND-SSP-LUT with just an input (no output) of 0 and 1. Compute $z$ and run through LUT. If it matches, output $\chi_0$. If it bounces out of the LUT without matching, output $\chi_1$. Note that this revealed bit (matches LUT or does not match LUT?) does not leak any information about inputs as each input combination matches exactly once and does not match exactly once. 

A NAND-LUT is 4 PETS worst case, 2 average case. NAND-SSP-LUT is 2 PETS worst case, 1.75 average case. 



## n-AND & n-OR (LUT)

Problem: compute an AND or OR across $n$ values: $b_0,b_1,b_2,\ldots,b_n$

* Compute $\boxed{b}=\boxed{b_0}+\boxed{b_1}+\boxed{b_2}+\ldots+\boxed{b_n}$​
* Use LUT operation (see above) on the following n-AND table

| Input: $\boxed{b}$ | Out (n-AND) |
| ------------------ | ----------- |
| $\boxed{0}$        | $\boxed{0}$ |
| $\boxed{1}$        | $\boxed{0}$ |
| $\boxed{2}$        | $\boxed{0}$ |
| ...                | ...         |
| $\boxed{n}$        | $\boxed{1}$ |

* Use LUT operation (see above) on the following n-OR table

| Input: $\boxed{b}$ | Out (n-OR)  |
| ------------------ | ----------- |
| $\boxed{0}$        | $\boxed{0}$ |
| $\boxed{1}$        | $\boxed{1}$ |
| $\boxed{2}$        | $\boxed{1}$ |
| ...                | ...         |
| $\boxed{n}$        | $\boxed{1}$ |

Operation is O(n) rather than doing n LUTs at O(2^n).

## MULT (CDN)

The Cramer, Damgard, Neilson (CDN) protocol described above under 2PC can be extended to MPC.



## MULT (Beaver)

Problem: compute $\boxed{x\cdot y}$  given $\boxed{x}$ and $\boxed{y}$

Pre-computation step:

* Somehow choose a random $a$ and $b$
* Compute $\langle\boxed{a},\boxed{b},\boxed{ab}\rangle$
  * This called a multiplication triple or Beaver triple
  * Generally expensive and might resort to FHE circuits

Multiplication step

* Consume $\langle\boxed{a},\boxed{b},\boxed{ab}\rangle$
  * Can only be used for one multiplication and then discarded
* Compute $\boxed{\tilde{x}}=\boxed{x}-\boxed{a}$ and $\boxed{\tilde{y}}=\boxed{y}-\boxed{b}$​
* Recover $\tilde{x}$ and $\tilde{y}$
  * This requires recovery from an arbitrarily large message space
  * Exponential Elgamal, BGN, Pedersen, and similar cannot be used
  * Paillier and Shamir secret sharing can
  * If encryption is used, decryption key is shared 2-out-of-2 between Alice and Bob
* Compute $\boxed{xy}=\tilde{x}\cdot\tilde{y}+\tilde{x}\cdot\boxed{b}+\tilde{y}\cdot\boxed{a}+\boxed{ab}$​
  * $\tilde{x}\tilde{y}+\tilde{x}b+\tilde{y}a+ab=(x-a)(y-b)+(x-a)b+(y-b)a+ab=xy-ya-xb+ab+xb+-ab+ya-ab+ab=xy$



## MULT (BGN-esque)

Problem: compute $\boxed{x\cdot y}$  given $\boxed{x}$ and $\boxed{y}$

Assume encryption allows arbitrary additions and one multiplication (like BGN), however assume also that you can decrypt arbitrarily large messages (unlike BGN). Assume distributed/threshold key generation and decryption. One candidate might be "A Simple BGN-type Cryptosystem from LWE."

Non-solution:

* Use the multiplication operation to compute $\boxed{xy}$
  * This can only be done once, we would like to do it for arbitrary multiplications

Protocol:

* Coin toss a random ciphertext $\boxed{a}$
* Coin toss a random ciphertext $\boxed{b}$
* Use the multiplication operation to compute $\boxed{ab}$
* Generate $\boxed{1}$ and use multiplication to compute: $\langle\boxed{x\cdot 1},\boxed{y\cdot 1},\boxed{a\cdot 1},\boxed{b\cdot 1}\rangle$​ 
  * This maps all the values in to the post-multiplication group (which is often different than pre-multiplication)
  * We will abuse notation and continue to call the outputs $\boxed{x},\boxed{y},\boxed{a},\boxed{b}$​
* Compute $\boxed{\tilde{x}}=\boxed{x}-\boxed{a}$ and $\boxed{\tilde{y}}=\boxed{y}-\boxed{b}$​
* Recover $\tilde{x}$ and $\tilde{y}$​
  * This requires recovery from an arbitrarily large message space
* Compute $\boxed{xy}=\tilde{x}\cdot\tilde{y}+\tilde{x}\cdot\boxed{b}+\tilde{y}\cdot\boxed{a}+\boxed{ab}$



## SELECT

**Protocol 1:** Due to Toft

Problem: Provided with a pair of ciphertexts $\boxed{x_0},\boxed{x_1}$ and a selector bit $\boxed{b}\in\{\boxed{0},\boxed{1}\}$, output $\boxed{x_b}$

* $\boxed{x_b}=\boxed{x_0}-\boxed{b}\cdot\boxed{x_0}+\boxed{b}\cdot\boxed{x_1}$
  * Requires a MULT operation (see above)



**Protocol 2:** due to Jacobsson & Juels

Do a LUT operation (see above) for the following lookup table:

| Input: $\boxed{b}$ | Output        |
| ------------------ | ------------- |
| $\boxed{0}$        | $\boxed{x_0}$ |
| $\boxed{1}$        | $\boxed{x_1}$ |



## ZERO Sharing

Due to Hao, Ryan, Zielinski and improves upon Kiayais and Yung

Problem: a set of $n$ people generate a set of shares $g^{x_i}$ such that $\Sigma x_i = 0$

Protocol

* Each party posts $X_i=g^{x_i}$
* Each party computes $Z_i=X_0X_1 \ldots X_{n-2}^{-1}X_{n-1}^{-1}=g^{z_i}=g^{x_0}g^{x_1}g^{x_2}\ldots g^{-x_{n-2}}g^{-x_{n-1}}$
  * The positive values are the people in front of you
  * The negative values are the people behind you
  * You do not include your own exponent (yet)
  * You do not actually learn $z_i$ , it just for notation
* Each party computes their final share as: $Z_i^{x_i}=g^{x_i \cdot z_i}$​

Logic table for four parties:

| Party | Initial   | w/ Alice     | w/ Bob        | w/ Carol      | w/ David      | Final          |
| ----- | --------- | ------------ | ------------- | ------------- | ------------- | -------------- |
| Alice | $g^{x_0}$ | –            | $g^{-x_0x_1}$ | $g^{-x_0x_2}$ | $g^{-x_0x_3}$ | Product of Row |
| Bob   | $g^{x_1}$ | $g^{x_0x_1}$ | –             | $g^{-x_1x_2}$ | $g^{-x_1x_3}$ | Product of Row |
| Carol | $g^{x_2}$ | $g^{x_0x_2}$ | $g^{x_1x_2}$  | –             | $g^{-x_2x_3}$ | Product of Row |
| David | $g^{x_3}$ | $g^{x_0x_3}$ | $g^{x_1x_3}$  | $g^{x_2x_3}$  | –             | Product of Row |



# Zero-Knowledge Test (ZKTs)

Zero knowledge tests are technically a special case of a 2PC or MPC. A 2PC/MPC usually is about computing an output from a set of inputs. A ZKT is about determining a fact about an input without learning it fully or computing with it. The fact is usually a conditional (it is or is not equal to another value; it is or is not from a certain range; etc.). 

* Unlike a zero-knowledge proof, the person who encrypted/committed/shared the input value is no longer around to prove the fact that everyone is interested in learning. 

* Typically the trustees performing the test have the power to fully recover the value, they choose to recover the fact about the value instead of the value.

* This is enforced with an honest majority (non-collusion) assumption on the trustees

  

## EQUAL

Due to Jakobsson and Juels

Trustee model with additively homomorphic encryption and distributed/threshold key generation and decryption

Problem: given $\boxed{x}$ and $\boxed{y}$, determine if (and only if) $x=y$ 

Protocol:

* Compute $\boxed{\delta}=\boxed{x}-\boxed{y}$
  * Note $\delta=0$ iff $x=y$; otherwise $\delta$ is the difference
  * Recovering $\delta$ would reveal the difference between $x$ and $y$ even if they are different
* Each trustee choose random $r_i$ and computes $\boxed{\hat{\delta_i}}=r_i\cdot\boxed{\hat{\delta}_{i-1}}$​
  * Initial value $\boxed{\hat{\delta}_{-1}}=\boxed{\delta}$

* Trustees recover $\hat{\delta}_n$ from final value $\boxed{\hat{\delta}_n}$​
  * It is ok if the trustees cannot fully recover $\hat{\delta}_n$ as long as they can determine if it is 0 or not 0 (i.e., protocol works with exponential Elgamal, BGN, etc. which will let you recover $g^{\hat{\delta}_n}$)

* If $\hat{\delta}_n=0$, the original values are equal: $x=y$; else, they are different.
  * As long as one trustee maintains secrecy over $r_i$, the difference between $x$ and $y$, in the case they are not equal to start with, is maintained.
  * Each step of the protocol can be covered with a simple $\Sigma$​-protocol to provide public verifiability against a malicious trustee.




### === MEMBERSHIP

Trustees

Generalization of BGN paper

Given [x], want to show x is in {a,b,c,d,…} without revealing x

Compute polynomial with roots equal to members

[p]=([x]-a)([x]-b)([x]-c)…

Decrypt p and see if it is 0

The number of multiplications you can do is equivalent to the size of the set

To show x is {0,1}, normal BGN suffices, otherwise you’ll need somewhat-homomorphic encryption







### === RANGE

Adapted for the Trustees

Given [x], is x in [0,3r]?

Let r be prime and (p-1)=a*r

Compute shares [si] such that [s1+…+st]=[x]

Give one share to each trustee (proxy-reencryption) to decrypt

Each trustee decrypts [si] and asserts [si] and [ui] = [si mod r]

Above requires a proof, probably possible by showing some [fi] such that [ui]*[fi]=[si] and doing some range proofs on [u] and [fi]???

Perform 5 PETS

[u1+…+ut] =? [x]

[u1+…+ut] =? [x+q]

[u1+…+ut] =? [x+(p mod q)]

[u1+…+ut] =? [x+a]

[u1+…+ut] =? [x+(p mod q)+q]

Shuffle PET output before final decryption

In Range: One PET is true

Out of Range: All PETs are false (knowing which one leaks info about x)

From Peng, Boyd, Dawson, Okamoto



### === PARITY

Given [x], return 0 if odd and 1 if even

OPEN ?? (Useful -> could do range tests:

2[x]:	Even		x: {0,q}

​	Odd		x: {q,p}

4[x]:	Even		x: {0,q/2} or {q,3q/4}

​	Odd		x: {q/2,q} or {3q/4,q}

Given [x], return [0] if odd and [1] if even

OPEN ?? (Useful -> could do binary decomposition under encryption)



### === COMPARISON 1

Due to Schoenmakers and Tuyls

Problem: Given [x] and [y], return: [-1] if x<y, [0] if x=y, and [1] if x>y

Requires binary representation so [x] and [y], and uses MULT (CONDITIONAL)

To add



## COMPARISON (Statistical)

Due to ???

Problem: Given $\boxed{x}$ and $\boxed{y}$, determine if $x>y$ or $x=y$ or $x<y$

* This differs from $\mathtt{COMPARISON1}$​ in that the result is returned in plaintext and not ciphertext (making it simpler)
* Compare this to $\mathtt{EQUAL}$ above for some omitted details

Protocol:

* Compute $\boxed{\delta}=\boxed{x}-\boxed{y}$
* Each trustee choose a **small** random $r_i$ and computes $\boxed{\hat{\delta_i}}=r_i\cdot\boxed{\hat{\delta}_{i-1}}$​
  * Initial value $\boxed{\hat{\delta}_{-1}}=\boxed{\delta}$​
  * $r_i$ is chosen such that the multiplication of each $r_i$ with $\delta$ will never exceed $q$ (group size) and trigger a modular reduction
* Trustees recover $\hat{\delta}_n$ from final value $\boxed{\hat{\delta}_n}$​
  * If $\hat{\delta}_n=0$ then  $x=y$
  * If $\hat{\delta}_n<0$ then  $x<y$​
  * Else $\hat{\delta}_n>0$ then  $x>y$

Remarks: 

* This is only statistically hiding (of the difference between $x$ and $y$) and has a lot of red tape around parameterizing the values (maximum size of $x$ and $y$ and $r$'s).







# ZK Proofs





### === Membership Proof

Interactive

Benaloh’s capsule

Given [x], want to show x is in {a,b,c,d,…} without revealing x

Alice permutes {[d],[b],[a],…}

Bob flips coin

Upon heads, Alice shows permutation is well-formed

Upon tails, Alice chooses value of x from list (say x=d) and computes: [x]-[d]=0 and decrypts to show it is zero



### === Equality Proof

Interactive

Cut-and-Choose

Requires homomorphic encryption

Prover sends [[x]] and [[y]]

Verifier picks one at random ([[z]]=[[x]] or [[z]]=[[y]]) and multiplies in a: [[z]]^a = [[z*a]] 

Prover decrypts and determines a 

(If x!=y, Prover finds two possible a values and must guess with 1/2 probability)



### === Range Proof

Non-interactive

Number has n-d leading zeros

Due to Mao (Chaum et al?)

Is [x] less than 2^d ?

Requires binary encryption of x: [x1],[x2],[x3],…[xd]

Show there are no more than d [xi’s]

Prove [xi] is 0 or 1 for each [x1]

Compute: [x] = [x1]+[x2]2+[x3]4+[x4]8…+…[xd]2^(d-1)



### === Range Proof

Non-interactive

Golle’s poker

Given [x], want to show x is in {0,51}

Publicly encrypt the following pairs and shuffle:

[0],[1]

[0],[2]

[0],[3]

[0],[6]

[0],[13]

[0],[26]

Choose one number from each pair, sum them up, and show, with a plaintext equality test, this sum is equal to [x].

Why 1,2,3,6,13? Every number in {0,51} can be written as a combination of 1 of these numbers or zero (a weird non-binary decomposition)

x is [x1]+[x2]2+[x3]3+[x4]6+[x5]13+[x6]26



### === Range Proof

Non-interactive

Paillier Setting

Adapted from Szepieniec & Preneel

Define Lift(c) as embedding ciphertext c from Zn^2 to Zn^3

Given Enc(a) and range R

Compute: [delta]=R-[a]

Compute: [z]=(Lift[R]-Lift([a]) - Lift([delta))

Decrypt [z]

If z=0, a<R

Otherwise z will be some multiple of n and a>R

Trick: Decrypt [z] requires knowing the private key in Zn^3 given that you know the private key in Zn^2; requires some algebraic group structure



### === Range Proof

Interactive

Brickell/Chaum/Damgard/Van de Graaf

Adpated from Boudot (note error in Boudot concerning ranges leads to ambiguity, think following is correct)

Given [x], show [x] is in range {0,100}

Alice choses random [a] from {0,100} and computes [b] = 1-a

Bob flips coin

Upon heads, Alice shows [a] and [b] are well-formed

Upon tails, Alice computes x’=[x+a] and x’’=[x+b], decrypts, and shows they are in {-100,200}





### === Polynomial Commitments

Setup: random s, compute g, g^s, g^(s^2), g^(s^3), …, destroy s

Polynomial: P() = c0 + c1*x + c2*x^2 + c3*x^3 +…

Commit to P() == Commit to P(s): g^P(s) = [(g)^c0] * [(g^s)^c1] * [(g^(s^2))^c2] + …

To open at point w, commit to P(w): Compute a new polynomial Pw() = P() - P(w) / (x-w)

Note that Pw(s)*(x-w) + P(w) = P(s) 

Then commit to Pw() by committing to Pw(s) and produce {w,z=P(w),Pw(s)} as proof

Verify {w,z} by computing: e(g^P(s),g) =? e(g^Pw(s), (g^s)/(g^w)) * e(g,g)^z

e(g,g)^P(s) =? e(g^Pw(s), g^(s-w)) * e(g,g)^z

e(g,g)^P(s) =? e(g,g)^Pw(s)(s-w) * e(g,g)^z

e(g,g)^P(s) =? e(g,g)^(Pw(s)(s-w)+P(w))

P(s)=?Pw(s)(s-w)+P(w)





### === Inequality via Non-Zero Proof

Brands

Given com=g^m*h^r=Commit[m,r], prove that m is not zero

Note that m=0 is simple by proving knowledge of r such that h^r=c (since g disappears)

Choose random r and t, compute b=(c)^r * h^-t

Compute chal = H(c1)

Compute d1 = s + chal * m^(-1) 

Compute d2 = t + chal * m^(-1) * r

Output {b, chal, d1, d2}

Verify b =? comm^d1 * (g^(-chal) * h^(d2))

b*g^chal =? comm^d1 * h^d2



### === Inequality via Non-Zero Proof

Prove [[a]] is not commitment to b

Compute [[a-b]] = [[d]]

Problem is now: prove [[d]] not a commitment to 0

Compute d^-1 and show [[d^-1]] as a witness

If d=0, there is no inverse

Prove < g, [[d]], [[d^-1]], g> is a DDH-tuple 



### === Inequality Proof

Given [[x]] and [[y]], prove x!=y

Publicly compute [[d]] = [[y]]*[[-x]] = [[y-x]]

Privately compute e such that ed=1 (mod q)

Compute [[ed]] = [[d]]^e and prove knowledge of e

Example: [[1]], [[d]], [[1]]^e, [[d]]^e is a DH tuple

Prove [[ed]] = [[1]]

Note: if x and y are equal, d=0 and no such e exists



### === Inequality Proof

Interactive

Cut-and-Choose

Requires re-randomizable encryption

Prover sends [[x]] and [[y]]

Verifier picks one at random, rerandomizes it and sends back

Prover decrypts and asserts whether it matches [[x]] or [[y]]

(If x==y, Prover must guess with 1/2 probability)