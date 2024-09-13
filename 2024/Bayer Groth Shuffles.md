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





## Neff Shuffle

Before explaining the Bayer-Groth shuffle, we will explain a building block: the Neff shuffle. 





## Bayer-Groth Shuffle

We start with an input list of ciphertexts:

* $\langle \mathsf{Enc}(m_0), \mathsf{Enc}(m_1), \mathsf{Enc}(m_2), \mathsf{Enc}(m_3), \mathsf{Enc}(m_4) \rangle$

The output will be re-randomized (not shown) and shuffled (shown):

* $\langle \mathsf{Enc}(m_1), \mathsf{Enc}(m_0), \mathsf{Enc}(m_3), \mathsf{Enc}(m_2), \mathsf{Enc}(m_4) \rangle$

Alice will commit to the permutation she used:

* Permutation: $\langle 1, 0, 3, 2, 4 \rangle$

* $\mathsf{Commit}(\langle 1, 0, 3, 2, 4 \rangle)$

The verifier will generate a random challenge $\alpha$ and send it to Alice. Alice will then commit to:

* $\mathsf{Commit}(\langle \alpha^1, \alpha^0, \alpha^3, \alpha^2, \alpha^4 \rangle)$

Alice will need to prove she did this correctly, which we will return to. 