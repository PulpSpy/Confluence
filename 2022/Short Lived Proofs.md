**Short lived signatures and proofs: a gentle introduction**

Arasu Arun, Joseph Bonneau, Jeremy Clark

ASIACRYPT 2022

Usually a cryptographer works on two time-scales: most operations are designed to execute as fast as possible (e.g., signing a message, proving a statement in zero knowledge), while the rest are designed to be so slow that they cannot even execute within the lifetime of the universe (e.g., recovering the private key from a public key).

Time-based cryptography studies the in-between: functions that are designed to run in a moderate (e.g., hours, days, months) amount of time. [Verifiable delay functions](https://eprint.iacr.org/2018/601.pdf) (VDFs) are a standard primitive that serve a “stopwatch” for evaluation time. VDFs (or related constructions) can be combined with other primitives to yield [encryptions](https://people.eecs.berkeley.edu/~daw/papers/timelock.pdf) or [commitments](https://www.iacr.org/archive/crypto2000/18800237/18800237.pdf) of data that can be forced open after some period of time, or [signatures](https://eprint.iacr.org/2020/1563.pdf) that only show up after a delay. In each of these primitives, something is kept secret until enough time passes.

In our work, we look at whether we can maintain data integrity (specifically: a signature is unforgeable and a zero-knowledge proof is sound) until enough time passes, and then it can be forged by anyone. Why? This provides deniability in the future for data and knowledge. 

We do not want the signer, or anyone else, to have to take any future action to ensure this. With our primitive, anyone can sign a message or prove a statement and forget about it: its validity dissipates naturally over time without any further interaction. It dissipates because cryptography security actually breaks after a delay, allowing anyone to create forgeries. This is different from a [short-lived certificate](https://www.ieee-security.org/TC/W2SP/2012/papers/w2sp12-final9.pdf) where a short expiry date is chosen, but it is only by social convention that we decide not to trust the certificate anymore. 

### A simple approach & its fixes

While our constructions might seem complex to non-cryptographers (or even cryptographers working on other subareas of the field), the underlying concept can be understood very simply. Imagine if you signed messages using RSA signatures and a fresh (not seen before) 512-bit key. This key length is known to be far too weak by today’s standards, but recovering the key still takes a few hours and a few hundred dollars of cloud computing time. So a message signed a few seconds ago is probably not a forgery, but if you see a signature on a message that was sent yesterday, you have no ability to determine if it is real, or someone recovered the signing key and used it to sign a fraudulent message. 

That is exactly what we are aiming for with a short-lived signature. Unfortunately, this scheme does not quite work that well for a few nit-picky reasons:

1. Recovering an RSA key (via factorization) is parallelizable. So an attacker with more computing resources can recover the key (and forge signatures) faster. It’s hard to know if a signature is really good for 1 minute or 1 hour in the face of a well-resourced attacker.
2. The time to recover an RSA private key is variable: typically you use a blend of algorithms depending on how “lucky” the faster it is. 
3. Everytime you sign a message, you burn the key that you used to sign it.
4. When I see a signed message, I cannot tell when it was signed. Even if the signer includes a timestamp, forged messages can include incorrect timestamps trying to make me think a key is fresh when it is actually expired. 

Each of these problems are fixable:

1. Instead of using a weak RSA key, you can use a full-strength key for any signature scheme you want (e.g., ECDSA, Schnorr, BLS). You place the private key in a time-release encryption that will be revealed in the future. Time-lock encryption (like VDFs) is an inherently sequential computation, so parallelism doesn’t significantly help speed up the attack.
2. The number of steps needed to complete the computation is precisely chosen when the ciphertext is constructed. 
3. You can maintain a long-term key that you never reveal, and use it to sign a newly created sub-key for each message to prove that key belongs to you.
4. You can include a “random beacon” in the message you sign. A beacon value is something unpredictable before it is “fixed” and verifiable after the fact by looking it up and when it was produced. For example: lottery numbers, stock prices, block headers in a blockchain, trusted party like NIST. Each of these has some red-tape in how you use them that we are omitting. Note that beacons only timestamp in one direction: you cannot make a message appear older than the beacon value (but you can use any old beacon value with a new message). This is fine for us.

If you stitch all this together, you get a short-lived signature. However, signing a message requires two (normal) signatures—one on the message and one on the sub-key—and one (time-release) encryption. 

In our paper, we show you can build an even more efficient construction from scratch. We present a few flavors of short-lived signatures, but the main construction takes about the same time as computing one normal RSA signature. In this construction, we address the problems above as follows:

1. We use a VDF which, like time-release encryption, has only one sensible algorithm (that we know about) for solving it, and this algorithm cannot be sped up with additional resources (it is “inherently sequential”). 
2. It can only be sped up by building a faster processor. One day, we might have cheap and widely available VDF-specific chips that will be hard to improve on (you could maybe gain 5-20% by overclocking, or even 2x with a multimillion dollar custom chip, but it will be infesabile to solve VDFs 5x or 10x faster).
3. In our construction, we do not leak the signing key, so you can sign the message using your normal key. Instead, the adversary can eventually compute a trapdoor value that allows forgeries, despite not knowing the private key. 
4. We use a beacon to let the recipient determine how freshly generated a signature is. 

### Scoping zero-knowledge proofs with disjunction

Next we ask, can we build a similar construction for zero-knowledge proofs? The idea of a short-lived proof is to add a validity period of time to its soundness. If you pick up a proof that’s validity period has expired, it could have been a valid proof but you cannot be sure. It could also be a forgery of any incorrect statement that has been backdated (by using an old beacon value).

It is less obvious where to start with zero knowledge proofs—unlike signatures, there is no “key” that can be leaked to make it invalid. Instead we use a trick that has been around for a while: disjunction. Disjunction is just a fancy way of saying the claim being proven has an “or” condition in it. 

For example, say I hand you the public key of an Ethereum address that controls millions of ETH and I want to prove to you that I know the secret key. I could sign something with the key but I am scared you will sneak me a challenge message to sign that actually transfers my ETH. Instead, I can use a simple zero-knowledge proof of knowledge of the key. Let’s call what I am proving CLAIM. Technically CLAIM is: {sk | pk=KeyGen(sk, “256 bits”)}. 

With some clever disjunctions, I can put some limitations on the proof. For example, let’s say I only want you to be able to verify the proof. Instead of proving “CLAIM is true,” I will prove the following: “Either CLAIM is true *or* the prover knows your private key.” When you see this proof, you know it must be the case that CLAIM that is true because you protect your private key. But if you show this proof to your friend, your friend cannot decide which clause is true: it might be that CLAIM is true, or it might be that CLAIM is untrue but you know your own private key (which you do). These are called [designated verifier](https://link.springer.com/chapter/10.1007/3-540-68339-9_13) proofs. This disjunctive trick shows up elsewhere: [PoWorK](https://eprint.iacr.org/2015/1230) (email spam, merging blockchains based on proof of work and stake), [KeyForge](https://www.usenix.org/conference/usenixsecurity21/presentation/specter-keyforge) (deniable email). 

### Short-lived zero knowledge proofs

Disjunction gives us a starting point for a short-lived proof. Given some CLAIM I want to prove, can I make it short-lived by changing it to “either CLAIM is true or I solved a verifiable delay function”? This seems to work! If the VDF is configured to take one day, a beacon value from one minute ago is baked into the VDF, then you should be reasonably sure that CLAIM is true because there hasn’t been enough time to solve the VDF. However, if you don’t see my proof until a week from now, there has been enough time for someone to have solved the VDF and create a “proof” of some CLAIM that is not true. 

Again, for some nitpicky reasons, this almost works but not quite. Let’s consider one challenge: if CLAIM really is true, I still have to stick some alleged output of the VDF into the proof. The great thing about zero knowledge is that I don’t need to use a real output—the overall proof will still hold because the CLAIM side of the disjunction is true. The problem is, what if someone checks by solving the VDF and seeing if my made-up output is right or not? If it is not, then they know CLAIM must be true and it isn’t short-lived. So that means I need to stick a real VDF into the proof even when CLAIM is true, which means I need to spend time doing a VDF every time I want to prove something. The cost of computing the VDF is the same as the validity period of the proof, so a proof that is supposed to last a month will take me a month to produce. Worst, I can’t reuse a VDFs across multiple proofs without “tying them together” in terms of deniability. 

We have several ways of side-stepping this limitation in the paper, summarized below.

![img](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfK1HF65nbIKn0i0CilrZ_HdcPtFI1at1Mc9Q_OwgMt8EzZUpWCKhIDnZREYKsrDnQ5flpprDq1KhK8s6Je3qmteP7UzFPlV0H34u4MCmUFngqeUBPgMv6-lj8akiqboklxfgjsUPHc9HqkdV7iuk_1ZpQ?key=4u5A0UsV7E7wewKBqoNqDA)

Along this dimension, the best approach is using a zk-VDF (however there are other dimensions to consider that make the other constructions interesting still). With a zk-VDF, you do not reveal the input and output for the VDF, you simply prove in zero-knowledge that you know an input/output pair. This lets you fake the input/output of the VDF (simulate it) which is nearly instant (and independent of how long the delay parameter of the VDF is).

As a final note, the constructions above are for “classic” zero-knowledge proofs (called Sigma Protocols) but it is conceptually easy to make a zk-SNARK short-lived. You just add an “or” gate to the statement you are proving and add a sub-circuit that proves knowledge of a VDF solution. It is however a bit heavyweight. We did not spend time on optimization and for us, it adds about 2 million extra gates to the underlying proof “circuit”

### Applications

Short-lived signatures can be a drop-in replacement for signatures when deniability is needed. They are great for emails or instant messaging, especially when you don’t have a public key or any prior contact with the recipients. It doesn’t matter if you send to one person or a large group.

Short-lived proofs can be used in voting systems: you get a proof that your ballot was cast correctly for the right candidate, but the proof becomes invalid by the time you leave the polling place (for good measure, you could wait a bit and then get “fake” proofs for each of the other candidates you didn’t vote for).

Short-lived proofs can also be composed with other “disjunctive” proofs. For email, a better approach might be to start with the KeyForge system, which provides deniability if a server produces a certain value at a certain time, and then add a short-lived condition on top just in case the server is not timely. 

Perhaps could use more conclusion/open questions here?
