# SNARKs: Reading Course

## Tools

Documenting what you are learning can be done one of three ways:

| Tools                    | Pros                                                         | Cons                                                     |
| ------------------------ | ------------------------------------------------------------ | -------------------------------------------------------- |
| Word, Google Docs, Pages | Easy to use                                                  | Hard to document math and code. Manual references.       |
| LaTeX                    | Easy to document math, easy to add citations and automatic reference management, standard for academic papers | Hard to learn, spend a lot of time on compilation errors |
| Markdown, Typora         | Middleground: Typora is as easy to use as Word, while math is the same as LaTeX (easy), some kinds of diagrams are easy to create as well (mermaid.js), integrates well with GitHub (although not all math), HackMD, GitBooks, etc. | References are still manual                              |

On balance, I suggest learning and working with Markdown. Specifically, I love Typora which is WYSIWYG markdown editor. I write all drafts of things (including this document) in it now. 



## Cryptography

Before turning to zero knowledge, spend some time familiarizing yourself with basic cryptography tools at a "black box" level. Black box means you don't care how they work, just focus on: what are the inputs, what are the outputs, what are the security properties?

These are not all directly related to zk-SNARKs but will help you situation zk-SNARKs in the broader cryptography landscape. The  basic four kinds of primitives are as follows:

|                      | *Message Confidentiality*:        | *Message Integrity*:     | *Both together:*                         |
| -------------------- | --------------------------------- | ------------------------ | ---------------------------------------- |
| ***Symmetric Key:*** | Encryption (e.g., AES)            | MACs (e.g., HMAC)        | Authenticated Encryption (e.g., AES GCM) |
| ***Public Key:***    | Public Key Encryption (e.g., RSA) | Signatures (e.g., ECDSA) | (Not really used)                        |

If Alice and Bob want to talk, they both need public keys and they both need to know the public key they have for the other person is the right one. Don't underestimate how hard it is to figure out keys, an area called public key infrastructre (PKI). Once you have keys, you can keep secret messages secret (public key encryption) and make sure messages are not changed after you sign them (digital signatures). However these primitives are slow compared to symmetric key cryptography, where we assume that Alice and Bob share a secret value that no one else knows. So generally we use public key cryptography to establish a symmetric key and once we have one, we switch to the faster symmetric key algorithms.

A simple protocol is:

1. Alice picks a symmetric key

2. Alice gets Bob's public encryption key

3. Alice sends the symmetric key to Bob using public key encryption
4. Alice and Bob exchange messages using the symmetric key and authenticated encryption

We can replace steps (1)--(3) with something called **key exchange**. It uses something similar to public key encryption but cheaper,  and it adds several security properties: Alice and Bob can both contribute to the randomness of the key; if Bob's private key gets stolen, the thief cannot figure out the symmetric key used between Alice and Bob; Alice and Bob know they are talking to each other; etc.

Finally, **hash functions** are an important building block. They show up in MACs and in digital signatures and are sometimes used by themselves.

Connections to **zero knoweldge proofs** (ZKPs): one way to do a digital signature is to do a zero knowledge proof with a message thrown in (e.g., DSA, Schnorr). Hash functions are used in ZKPs. Some key exchange protocols use ZKPs. 

Materials:

* Any intro to crypto material will be helpful
* My crypto course: https://www.pulpspy.com/courses/crypto/index.html
* Check in with me to go over



## Number Theory

It is important to learn at least one number theoretic setting where you can do cryptography. The one that is simple and versitile is doing multiplication and exponentiation of integers from $1$ to $p$ where $p$ is a large (e.g., 4096 bits) prime number. To keep ourselves inside this group, all multiplications and exponentiations are followed by a $\bmod p$ operation. 

Materials:

* Week 7 of my crypto course: https://www.pulpspy.com/courses/crypto/index.html
* Write Mathematica code to generate a table for all Zp:
  * Addition table
  * Multiplication table
  * Exponentiation table
    * g^(a mod q)^b=g^(ab)
    * g^(w^(x+y mod ord(a)))

  * Identify important structures: multiplicative subgroups, quadratic residues
  * Exponent groups: what is the order?

* Check in with me to go over
* Modify Mathematica code for Gq instead of Zp



## Commitment Functions

* Lectures 00-12 of my blockchain course: https://www.pulpspy.com/courses/blockchain/index.html
* Hash-based commitments
  * Are they hiding?
  * Are they binding?
  * Merkle trees
* Feldman commitments
  * Are they hiding?
  * Are they binding?

* Pedersen commitments
  * Are they hiding?
  * Are they binding?

* Multi-commitment (vector commitment)
  * $c=\mathsf{Comm}(m_0,m_1,m_2)=g_0^{m_0} g_1^{m_1} g_2^{m_2} h^{r}$
  * $c$ is constant-size -> "succinct"
  * "Proof"
    * Prover time
    * Verifier time <- sub-linear (fast) 
    * Proof size <- succinct (short)

  * Sigma protocols -> O(n) size, O(n) verifier
  * Bulletproofs -> O(1) size, O(log n)
  * Polynomials (Poly-IOP) -> O(1) size, O(1) verifier time, prover time O(n*log n), "universal trusted setup"




## Bilinear Pairings 

Materials:

* Check in with me to go over this topic



## Zero Knowledge (Basics/History)

Materials:

* https://zk-learning.org (Lectures: 1-3)



## Poly-IOP: KZG and Plonk

Materials:

* David Wong on Plonk (skip copy constraints): https://www.youtube.com/playlist?list=PLBJMt6zV1c7Gh9Utg-Vng2V6EYVidTFCC
* Dan Boneh on Plonk: https://zk-learning.org (Lecture 5: Plonk Interactive Oracle Proofs)
* Ariel Gabion on https://web.archive.org/web/20220702133937/https://hackmd.io/@arielg/ByFgSDA7D
* Check in with me and I'll explain copy constraints in more detail
* Checkpoint: draft a Plonk explainer: written and/or video 



## Advanced Primitives

* Proof carrying data (PCD) is a generalization of incremental verifiable computation (IVC). Sometimes known as recursive SNARKs.
  * $\mathsf{state}_i=f(x_i,\mathsf{state}_{i-1})$ and proof $\pi_i$ that $f$ is correctly applied and $\pi_{i-1}$ is correct
  * Example: Halo, Halo2, Halo Infinite
  * Use-case: constant size blockchain (Mina)
  * Red tape: 
* Folding schemes
  * Assume a subcomponent of your circuit is repeated across your circuit in $n$ different places. Is there some optimization you can do? There are some repeated elements: the selectors are the same, the wiring is the same. The difference is the values on the wires.
  * Visually, a folding scheme stacks the circuit components on top of each other, and for each stack of wires, it merges them to a single value using a mathematical formula. Putting these merged values through the circuit should result in an output matching the merger of the output wires. So essentially you prove all $n$ subcomponents at once.
  * From an efficiency perspective, this can reduce prover effort from $n$ to 1 for the subcomponent, however they also have to prove the merge (folding) was done correctly. This will be $n$ for each wire and so over-all, it does not save. However if the folding can be done as pre-computation, then it is worth doing to save time later.
  * Example: Nova 
