# Bayer-Groth Shuffles

I found it hard to get a concise description of the Bayer-Groth proof of shuffle. This is a straight-forward explanation that tries to minimize formalisms and does not cover all the generalizations.

## Preliminaries 

### Proof of Shuffles

Alice has a list of ciphertexts. Alice does not know the decryption key. Despite this, she is able to re-randomize the ciphertexts (so the re-randomized version is indistinguishable from the randomized version). And since it is just a list of ciphertexts, she can also reorder them. 

With a proof of shuffle, Alice proves she did nothing more to the list of ciphertexts than rerandomize them and shuffle them. In particular, she does not drop ciphertexts from the list, duplicate ciphertexts in the list, introduce new ciphertexts, etc. (all while maintaining the same number of ciphertexts, as the number of ciphertexts is evident). If Alice wants, she can opt to not rerandomize any or all of the ciphertexts (equivalent to rerandomzing with 0) or not shuffle (equivalent to shuffling with the identity permutation) --- and she can still produce a proof. The proof stops her from going beyond these two operations, but does not actually enforce that she injects non-trivial randomness. 

Some cryptographic protocols use shuffles for untraceability. Think about encrypted ballots in a ballot box on a voting protocol. Alice will shuffle and prove. Then Bob will shuffle and prove. Then Carol. At the end, as long as one is honest (and does not reveal their non-trivial rerandomizations and shuffle), the output order is randomized. 

### Encryption

The list of ciphertexts are from an additively homomorphic public key encryption scheme, such as exponential Elgamal. Additively homomorphic means:

* $\mathsf{Enc}(m_0+m_1)=\mathsf{Enc}(m_0)\cdot\mathsf{Enc}(m_1)$
* $\mathsf{Enc}(\alpha \cdot m_1)=\mathsf{Enc}(m_0)^\alpha$

The operations on the LHS inside the encryption are addition and multiplciation mod $q$ for 256-bit prime $q$. The operations on the RHS outside the encryption are akin to multiplication and exponentiation but will have a precise definition that depends on the encryption used (Elgamal are a pair of group elements while Paillier is a single group element) and the underlying group (integers are single integers while elliptic curve points are two coordinates).

Although not shown, $\mathsf{Enc}(m)$ is shorthand for $\mathsf{Enc}(m,r)$ where $r$ is a random factor used in encryption (so encrypting the same message twice results in different-looking ciphertexts). To re-randomize a ciphertext, one can encrypt a 0 (with a new random factor) and add it homomorphically to the original: $\mathsf{Enc}(m,\hat{r})=\mathsf{Enc}(m,r)\cdot\mathsf{Enc}(0,r')$. For Elgamal, random factors are also homomoprhic so $\hat{r}=r+r'$.

### Vector Commitments

Exponential Elgamal encryption is a commitment function with an extra property: decryption (or the ability to recover the message from the committed value with a decryption key). If we strip down exponential Elgamal to remove the ability to decrypt, we are left with a commitment function called a Pedersen commitment. It is just like exponential Elgamal (additively homomorphic, re-randomizable) but you cannot look at the commitment and get the message back. The only way to get the message back is for the person who created the commitment to reveal the message and random factor, and then everyone can check (this method also works for exponential Elgamal). 

So why do we need a commitment scheme if we have encryption already? The answer is efficiency. While a Pedersen commitment is half the size of an Elgamal ciphertext, this is not really a big deal. The main improvement happens when you have, say 1000, messages. To encrypt them all, you need 1000 ciphertexts. However to commit to them, you can pack a "fingerprint" of all 1000 messages into a single commitment value. 

This is called a vector commitment. It maintains its homomorphic properties:

* $\mathsf{Commit}(\langle m_{0}+\hat{m}_{0}, m_{1}+\hat{m}_{1}, m_{2}+\hat{m}_{2}\rangle)=\mathsf{Commit}(\langle m_{0}, m_{1}, m_{2} \rangle)\cdot\mathsf{Commit}(\langle \hat{m}_{0}, \hat{m}_{1}, \hat{m}_{2} \rangle)$
* $\mathsf{Commit}(\langle \alpha \cdot m_{0}, \alpha \cdot m_{1}, \alpha \cdot m_{2}\rangle)=\mathsf{Commit}(\langle m_{0}, m_{1}, m_{2} \rangle)^\alpha$



## Bayer-Groth Shuffle

We start with an input list of ciphertexts:

* $\langle \mathsf{Enc}(m_0), \mathsf{Enc}(m_1), \mathsf{Enc}(m_2), \mathsf{Enc}(m_3), \mathsf{Enc}(m_4) \rangle$

The output will be re-randomized (not shown) and shuffled (shown):

* $\langle \mathsf{Enc}(m_1), \mathsf{Enc}(m_0), \mathsf{Enc}(m_3), \mathsf{Enc}(m_2), \mathsf{Enc}(m_4) \rangle$

Alice will commit to the permutation $\pi$ she used:

* $\pi$: $\langle 1, 0, 3, 2, 4 \rangle$

* $\mathsf{Commit}(\langle 1, 0, 3, 2, 4 \rangle)$

The verifier will generate a random challenge $\alpha$ and send it to Alice. Alice will then commit to:

* $\mathsf{Commit}(\langle \alpha^1, \alpha^0, \alpha^3, \alpha^2, \alpha^4 \rangle)$

Alice will need to prove she did this correctly. Considering:

1. $\mathsf{Commit}(\langle 0, 1, 2, 3, 4 \rangle)$
2. $\mathsf{Commit}(\langle 1, 0, 3, 2, 4 \rangle)$
3. $\mathsf{Commit}(\langle \alpha^0, \alpha^1, \alpha^2, \alpha^3, \alpha^4 \rangle)$
4. $\mathsf{Commit}(\langle \alpha^1, \alpha^0, \alpha^3, \alpha^2, \alpha^4 \rangle)$

The verifier can create (1) and (3). The prover proves that they know $\pi$ a (undisclosed) permutation that turns (1) into (2), and this is the same as the permutation from (3) to (4). 

Toward showing this, the prover combines the values in (1) and (3), and does the same for (2) and (4). To combine, the verifier chooses a random $\beta$ and the prover computes:

5. $\mathsf{Commit}(\langle 0\beta+\alpha^0, 1\beta+\alpha^1, 2\beta+\alpha^2, 3\beta+\alpha^3, 4\beta+\alpha^4 \rangle)$
6. $\mathsf{Commit}(\langle 1\beta+\alpha^1, 0\beta+\alpha^0, 3\beta+\alpha^3, 2\beta+\alpha^2, 4\beta+\alpha^4 \rangle)$

For a random $\beta$, the same permutation $\pi$ turns (5) into (6). 

Next, the prover computes (7) which is the values of (5) programmed into the roots of a polynomial, and likewise (8) are the values of (6). These polynomials are the same if and only if the roots are the same:

7. $\mathsf{Commit}(P_1(\gamma))=\mathsf{Commit}(\prod (i \beta  + \alpha^i)-\gamma)$
8. $\mathsf{Commit}(P_2(\gamma))=\mathsf{Commit}(\prod (\pi(i)\beta  + \alpha^{\pi(i)})-\gamma)$

To check, the verifier selects a random $\gamma$ and the prover opens both polynomials. If the polynomials open to the same value, we can reason all the way back to (2) beings $\pi$ of (1) and (4) being $\pi$ of (3):

* If $P_1(\gamma)=P_2(\gamma)$ at a random point $\gamma$, the set of roots of the polynomials are the same.
* If the set of roots are the same, then the elements of (5) and (6) are permuted by some permutation value $\pi$.
* If the elements of (5) and (6) are random ($\beta$) linear combinations of (1,3) and (2,4), then (1) and (2) are permuted by $\pi$ and (3) and (4) are also permuted by $\pi$.

Using (3) and (4), we can now bring in the ciphertexts and compute:

9. $\langle \mathsf{Enc}(m_0\cdot\alpha^0), \mathsf{Enc}(m_1\cdot\alpha^1), \mathsf{Enc}(m_2\cdot\alpha^2), \mathsf{Enc}(m_3\cdot\alpha^3), \mathsf{Enc}(m_4\cdot\alpha^4) \rangle$

10. $\langle \mathsf{Enc}(m_1\cdot\alpha^1), \mathsf{Enc}(m_0\cdot\alpha^0), \mathsf{Enc}(m_3\cdot\alpha^3), \mathsf{Enc}(m_2\cdot\alpha^2), \mathsf{Enc}(m_4\cdot\alpha^4) \rangle$

The prover shows the values exponentiated in match (3) and (4). Finally we sum up (9) and (10) homomorphically can show they encrypt the same value:

11. $\mathsf{Enc}(m_0\cdot\alpha^0+m_1\cdot\alpha^1+m_2\cdot\alpha^2+m_3\cdot\alpha^3+m_4\cdot\alpha^4)=\mathsf{Enc}(m_1\cdot\alpha^1+m_0\cdot\alpha^0+m_3\cdot\alpha^3+m_2\cdot\alpha^2+m_4\cdot\alpha^4)$





