# Linear Codes Cheatsheet



## Notation

A linear code takes a message $m_i$ and encodes it into a codeword $c_i=\mathsf{Encode}(m_i)$. The purpose is to add error detection/correction when $m_i$ might be transmitted over a channel with errors or noise. For this reason, $c_i$ is necessarily longer than $m_i$. (Note: technically the definition will allow the size of $m_i$ and $c_i$ to be the same but it results in a linear code that is deficient is some sense: e.g., provides no error detection/correction, cannot encode every possible message, etc.). 

If you have heard of checksums (which appends error correction bits to the end of a message), note that linear codes are different. They do not necessarily just append error checking to the end of a copy of the original message, they might transform the original message into something new.

Now lets examine the formal definition for a linear code $\mathcal{C}$, which is a scheme for coding (rather than a codeword). We say $\mathcal{C} \subseteq \mathbb{F}_p^n$ is a $[n, k, l]_p$ linear code. There are 4 parameters:

* $p$ is a parameter that impacts the size of the message and the size of the codeword. Because linear codes are "linear", we will be able to do some algebra on the codes and the purpose of $p$ is basically to allow this. Specifically, codewords will be taken from the finite field $\mathbb{F}_p^n$. 
* $n$ is the length of the codeword. Think of it as a $n$-length vector where each element of the vector is from $\mathbb{F}_p$. 
* $k$ is the length of the original message. Again, generally $k<n$. 
* $l$ is a parameter that impacts the quality of the encoding scheme in terms of its ability to detect and correct errors. Generally speaking, there is a trade-off between performance and quality. So low quality codes (small $l$) will be faster and high quality codes (large $l$) will be slower but better at detecting/correcting errors. There is also an upper limit on $l$ once you fix $n$ and $k$. Codes that use the maximum $l$ possible are called MDS codes (maximum distance separable) and Reed Solomon is a prominent example.

We will focus on MDS linear codes only so we will drop $l$ from the definition and simply refer to them as $[n, k]_p$ MDS linear codes. That said, $l$ is still a relevant "feature" of an MDS linear code, it just is not a "free parameter" that we can choose. Later we will show that $l=n-k+1$ for MDS codes (and in general $l\leq n-k+1$, called the Singleton bound). 



## Encoding

You begin with a message to encode. We'll assume the message lives in $\mathbb{Z}_p^k$, though linear codes can be constructed over more general structures. In simpler terms, the message is an array of $k$ integers, where each integer is in the range $[0\dots p-1]$, inclusive. A codeword is an array of $n$ integers (with $n > k$), where each element is again in $[0\dots p-1]$. 

We often think of $n$ and $k$ as a ratio $\rho=k/n$ which is called the encoding rate. A standard rate might be 0.5 which means codewords are twice as big as the messages.

Although a codeword is just a vector in $\mathbb{Z}_p^n$, not all such vectors are valid codewords. The valid ones form a special subset. This subset is defined to have three key properties:

1. **Error detection**: If a codeword is corrupted in a few positions, the result will likely no longer be a valid codeword.
2. **Error correction**: If a codeword is corrupted slightly, we may be able to identify the nearest valid codeword and recover the original message.
3. **Linearity**: Codewords follow algebraic rules. If two messages $m_0$ and $m_1$ add to form $m_2 = m_0 + m_1 \mod p$, then their codewords also add the same way:  
   $$
   \mathsf{Encode}(m_2) = \mathsf{Encode}(m_0) + \mathsf{Encode}(m_1)
   $$

Messages are drawn from a space we will denote $\mathbb{Z}^k_p$ and when encoded, the codeword is in $\mathbb{Z}^n_p$. Encoding is bijective: it maps a unique message to a unique codeword and a decoding method will reverse this. But since the space of codewords is bigger than the space of messages, there are a bunch (in fact, most) of "words" in $\mathbb{Z}^n_p$ that are not valid codewords. 

So we can split $\mathbb{Z}^n_p$ up into a few segments:

1. $\mathcal{C}$ are the words that are valid codewords
2. Some words are invalid codewords but they are "close" enough to a codeword to be uniquely decoded. We say they are $\delta$-close to $\mathcal{C}$.
3. The rest **(and most**) words are invalid and not uniquely decodable. We say they are $\delta$-far from $\mathcal{C}$.

Importantly note that errors in transmission tend to start with a code word and modify it, rather than generating a completely random word, so the probability that we can detect and correct an error is higher than the above might lead you to believe. 

## Example: Reed-Solomon



## Fact: Singleton Bound

To support error detection and correction, we want our codewords to have as few zero entries as possible. Among all non-zero codewords, we look for the one with the fewest non-zero digits (this is the Hamming weight), and we call that number $l$. This is the minimum distance or minimum weight of the code.

The higher the value of $l$, the better the code is at detecting and correcting errors. But increasing $l$ comes at a cost: fewer codewords can satisfy the requirement, which limits how much information the code can carry. A fundamental constraint on these parameters is:

$$
l \leq n - k + 1
$$
This is known as the Singleton bound. Generally we want to maximize $l$ (better error correction) but also minimize $n$ (smaller codewords). One optimization is ensuring equality in the bound: $l = n - k + 1$. Codes like this are called maximum distance separable (MDS), of which Reed Solomon is a prominent example.



## Fact: Minimum Distance

A key fact about linear codes is that the **relative distance** between any two codewords is never too small. For all distinct codewords $u, v \in \mathcal{C}$, we have:

$$
\Delta(u, v) \geq \mu(\mathcal{C}) = \frac{l}{n}
$$

This says that the **relative Hamming distance** between any two codewords is at least $\frac{l}{n}$. That’s the same as saying their difference has at least $l$ non-zero entries.

Why is that?

Because:
- $u - v$ is **also a codeword** (since $\mathcal{C}$ is linear),
- And $u \ne v$ means $u - v \ne 0$,
- So $u - v$ is a **non-zero** codeword,
- But we assumed **every non-zero codeword has weight at least $l$** (by definition of the code),
- Therefore: $|u - v|_0 \ge l$, and so $\Delta(u, v) = \frac{1}{n} |u - v|_0 \ge \frac{l}{n}$

This fact tells us something powerful:  
> Any two distinct codewords are guaranteed to differ in **at least $l$ positions**, which makes the code reliable for detecting and correcting errors.

In practical terms, this gives us a **built-in minimum separation** between valid codewords, which is the foundation for how and why linear codes can detect and correct transmission errors.





## Fact: Johnson Bound

So far we've seen that a code's minimum distance $l$ (or relative distance $\mu = \frac{l}{n}$) controls how well it can detect and correct errors. But what if we flip the question?

**Suppose you receive a corrupted word — how many codewords could be "close enough" to be confused with it?**  
This is where the **Johnson bound** comes in.

The Johnson bound puts a limit on how many codewords can lie **within a certain Hamming distance** of a received (possibly corrupted) word. In other words, it says:

> Even if someone tries to confuse the decoder by flipping some digits, there are only so many codewords that could be mistaken for the original.

The bound says that if a code $\mathcal{C} \subseteq \mathbb{F}_p^n$ has relative distance $\mu = \frac{l}{n}$, then for any received word, the number of codewords within relative distance $\delta < 1 - \sqrt{1 - \mu}$ is **at most polynomial** in $n$.

This is helpful in decoding:

- It tells us that **list decoding** (finding *all* codewords within a certain radius) is computationally feasible **as long as the error rate $\delta$ is below this threshold**.
- If we go beyond that radius, the number of possible codewords could grow exponentially — making decoding ambiguous or impossible.

This gives a kind of **safe zone**: if your noise level is under the Johnson radius, you're still in a good position to recover the original message — possibly even if you're beyond the unique decoding radius.

### Summary

- The **Singleton bound** limits how good a code *can* be: $l \le n - k + 1$
- The **Johnson bound** tells us how many codewords might live *within a certain error radius*
- Together, they define the sweet spot for codes that are both efficient and error-resilient