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

* Somehow choose a random $a$ and $b$ and compute $\langle\boxed{a},\boxed{b},\boxed{ab}\rangle$
  * This called a multiplication triple or Beaver triple
  * Generally expensive and might resort to FHE circuits
  * Pre-computation step and can be done before knowing $\boxed{x}$ or $\boxed{y}$ which is why it is not used to directly multiply $\boxed{x\cdot y}$ (once you have this step done, computing $\boxed{x\cdot y}$ can be done with only decryption/addition/scalar multiplication)

Multiplication step

* Consumes one triple $\langle\boxed{a},\boxed{b},\boxed{ab}\rangle$
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




## COMPARISON (tk)

Due to Schoenmakers and Tuyls

Problem: Given [x] and [y], return: [-1] if x<y, [0] if x=y, and [1] if x>y

Requires binary representation so [x] and [y], and uses MULT (CONDITIONAL)

Protocol:

* To add



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

* This is only statistically hiding (of the difference between $x$ and $y$) and has a lot of red tape around parameterizing the values (maximum size of $x$ and $y$ and $r$​'s).

  

## MEMBERSHIP

Due to Boneh, Goh, Nissam

Problem: given $\boxed{x}$, determine if $x \in \{\mathsf{val}_0, \mathsf{val}_1, \mathsf{val}_2, \ldots\}$ for a set of values

Protocol

* Compute $\boxed{z}=(\boxed{x}-\mathsf{val}_0)\cdot(\boxed{x}-\mathsf{val}_1)\cdot(\boxed{x}-\mathsf{val}_2)\ldots$​​
  * Requires $\mathtt{MULT}$ to compute
  * Specifically it requires n-1 $\mathtt{MULT}$ operations for n values in the set
  * For 1 value, no multiplication and reduces to $\mathtt{EQUAL}$​ above
  * For 2 values, one multiplication is required and BGN can be used
  * For 3+ values, many multiplications are required and fully homomorphic encryption or MPC is required
* Recover $z$ from $\boxed{z}$
* If $z=0$, then $x \in \{\mathsf{val}_0, \mathsf{val}_1, \mathsf{val}_2, \ldots\}$; otherwise it is not.
  * It is ok if the trustees cannot fully recover $z$ as long as they can determine if it is 0 or not 0 (i.e., protocol works with exponential Elgamal, BGN, etc. which will let you recover $g^z$​​; of course, these might not work as you also need to do multiplications as above.)
  * Important: if $x$ is not in set of values, $z$ will leak partial information about $x$. For protocols where $x$ may or may not be in the set, insert a round of blinding (see $\mathtt{EQUAL}$ above for how) to prevent this. For protocols where $x$ must be in the set and this ZKT is enforcing honesty, it probably does not hurt to leak information about badly constructed $x$ values.



# ZK Proofs

There are **uncommon** proofs of small things that are not direct applications of $\Sigma$​-protocols or zk-SNARKs. Many are based around cut-and-choose (CNC) protocols. 



## MEMBERSHIP (CNC)

Problem: given $\boxed{x}$, prove that $x \in \{\mathsf{val}_0, \mathsf{val}_1, \mathsf{val}_2, \ldots\}$​ for a set of values

**Protocol 1:**

* Use a $\Sigma$​-protocol

**Protocol 2:**

* Due to Benaloh
* Prover: encrypt and shuffle the list of values: $V=\{\boxed{\mathsf{val}_2}, \boxed{\mathsf{val}_n},\ldots , \boxed{\mathsf{val}_3}\}$​ and send it to verifier
* Verifier: flip a coin
* Prover: if heads, open all values in $V$ to show it is properly constructed
* Prover: if tails, compute $\boxed{\delta}= \boxed{x}-\boxed{\mathsf{val}_j}$ for the value in $V$ that matches $x$ and recover $\delta$
* Verifer: check that $\delta=0$
  * Alternative: do a ZK proof of shuffle, send the index of the matching item, and send $\delta$ with a ZK proof of decryption



## EQUAL (CNC)

Problem: given $\boxed{x_0}$ and $\boxed{x_1}$, prove that $x_0=x_1$.

**Protocol 1:**

* Use a $\Sigma$​-protocol

**Protocol 2:**

* Prover sends $\boxed{x_0}$ and $\boxed{x_1}$ to verifier
* Verifier flips a coin $b=\{0,1\}$ and picks a random number $r$
* Verifier computes $\boxed{z}=r*\boxed{x_b}$  and sends $\boxed{z}$
* Prover recovers $z$ and replies with $r$
  * If messages are not the same, it gets two possible values for $r$ ($r_0=z/x_0$ and $r_1=z/x_1$​ ) and must guess which is correct



## RANGE (Binary) 

This approach addresses the inefficiency of a subset membership proof by performing a smaller subset membership multiple times. The smallest (non-trival) subset membership proof is between two values, such as $\langle 0,1\rangle$. The idea here is provide a "bit decomposition" of $[[x]]$ which means convert $x$ into a binary form and give individual commitments to each bit $\langle [[b_0]], [[b_1]], [[b_2]], \ldots \rangle$. To use this as a range proof, the value of $\mathsf{max}$ is taken to be (near) a perfect power of two, $2^{d}$-1, and we prove $x$ is between $0$ and $2^{d}-1$ (If $\mathsf{max}$​ needs to be something more specific, we can accommodate that but set that detail aside for now).

The proofs consists of three ideas:

1) If only $d$ bits are given, the number cannot be larger than $2^{d}-1$. Since anyone can count the number of commitments, anyone can know there are only $d$ commitments.
2) If $d$ commitments are given, we need to verify they are actually bits and not commitments to larger numbers. For this, we can do $d$ subset membership proofs between $[[b_i]]$ and $\langle 0,1 \rangle$. Notice that the set is size $2^d$ but we are getting a proof for with only $d$ (as opposed to $2^d$) cases (this is sometimes called divide-and-conquer).
3) To issue a range proof for $[[x]]$ (the entire number), we have to show that $\langle [[b_0]], [[b_1]], [[b_2]], \ldots \rangle$ is the proper bit decomposition of $x$. Anyone can check this, starting with the committed bits and building them back into $[[x]]$, if the commitment scheme is additively homomorphic: $[[x]]=\sum_{i=0}^{d-1} [[b_i]]*2^i$  where the values of $2^i$ are publicly known (i.e., they are just the powers of two: $1, 2, 4, 8,16 \ldots$).

To adjust the range from $[0,2^{d-1}]$ to the range $[\mathsf{min},\mathsf{max}]$​ for any values, one can tk

### MEMBERSHIP for $\langle 0,1 \rangle$

Bit decomposition requires a subset membership for $\langle 0,1 \rangle$. 

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



## RANGE (Sum of squares)

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

      

## RANGE (Cards)

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



## INEQUAL

Problem: given $\boxed{a}$ and $\boxed{b}$, show they do not encrypt the same value.

Protocol:

* Compute $\boxed{\delta}=\boxed{a}-\boxed{b}$​ 
* Compute $\delta^{-1}$ and send $\boxed{\delta^{-1}}$​
  * If $a=b$ then $\delta=0$ and $\delta^{-1}$ does not exist
* Prove $\langle g, \boxed{\delta}, \boxed{\delta^{-1}}, g \rangle$ is a DDH-tuple with a $\Sigma$​-protocol like Chaum-Pedersen



## INEQUAL (CNC)

Problem: given $\boxed{x_0}$ and $\boxed{x_1}$, show they do not encrypt the same value.

Protocol:

* Verifier flips a coin $b=\{0,1\}$ and rerandomizes $\boxed{x_b}$​ 
* Verifier sends $\boxed{x_b}$
* Prover recovers $x_b$ and matches it to either $x_0$ or $x_1$
* Prover asserts value of $b$
  * If they are equal, prover does not know which and guesses (50% probability of being right)