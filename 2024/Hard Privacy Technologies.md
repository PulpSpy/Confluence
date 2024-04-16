# Hard Privacy Technologies

* Summary of technology that enables hard privacy. 

* Extracted from Auer, Bohme, Clark, Demirag [ABCD24]; to appear.

  

## Zero-knowledge proofs

### SNARKs, STARKs, $\Sigma$-protocols


A software client based on ZKP produces a sequence of numbers that can be shown to others to convince them that asserted features about their private data are true. For example, for a payee to know that a payment will be executed, it suffices to know that the payer’s account balance exceeds the value of the payment amount. ZKPs are often used in combination with other approaches described below.

Limitations:

* Very high computation cost -- depending on the implementation by a factor 100--1000 compared to unencrypted text (plaintext) for general features.
* The security of ZKPs often depends on mathematical assumptions that are not yet time-tested. 
* The computationally more efficient types of ZKPs often have vulnerabilities when it comes to collusion during the initial system setup.



## Homomorphic encryption (HE)

### Partially, fully (FHE), commitments


A payment processor can perform computations on encrypted data without having access to the plaintext. For example, a payment amount can be subtracted from an account balance without learning either the payment amount or the balance.

Limitations:

* Extremely high computational costs. 
* On consumer grade computers, only a small set of predefined computations can be performed. For example, while subtraction or addition may work on a consumer grade computer, multiplication or division is well out of bounds. 
* No existing payments architecture offers a convincing solution for handling access to decryption keys.



## Secret sharing

### Two/multi-party computation, threshold signatures, multi-sig

Private data is stored simultaneously with multiple data holders in such a way that no individual (or subset below a definable threshold, for example 4 out of 7) can reconstruct the information. By extension, not only can data be stored, but also pre-defined computations can be performed on the data with the same confidentiality. Some examples include multi-sig, key recovery, or distributed auctions. 

Limitations:

* Hypothetically applicable to CBDCs, but there do not exist workable technological design proposals.
* Computation requires substantial synchronous communication between the data holders. 
* Data holders always need to be online, even if they can be trusted to never deviate from the protocol.
  

## Anonymity-enhanced signatures

### Ring/blind signatures, anonymous credentials

A user can present a message signed by a payment processor in such a way that no one can link it back to the user who asked for the signature (blind signatures).  This is applicable to verifying attributes (e.g., age, unspent coin) of a user's identity (anonymous credentials).  Anonymous e-cash can also be built such that showing the same attribute twice (i.e., double-spending a coin) revokes the user's anonymity.  A final variation (ring signature) allows a user to sign a message in such a way that no one can tell who exactly signed the message, only that it was one person within a group of people chosen by the user. 

Limitations:

* Best examples do not easily interoperate (cryptographically) with other primitives. 
* Revocation of signed data is hard. 
* CBDCs built with this often require interactive spending protocols and double-spending is detectable (but not directly preventable). 
* Ring signatures become computationally inefficient as the group grows large, which limits anonymity.
  

## Tamper-resistant hardware

### Hardware security modules (HSMs), smart cards,  physically uncloneable functions (PUFs)

A manufacturer designs very efficient, special-purpose hardware robust against tampering. It can be provisioned to a processor (e.g., HSM) to handle payments while preventing insiders from accessing or changing customer data. Alternatively, it can be given to users and merchants (e.g., smart cards) to execute payment protocols while protecting PINs, balances, and other data. PUFs are a more futuristic building block for keeping pieces of hardware unique, assuming certain physical properties hold. Can be used in combination with cryptography to secure end points and keys.

Limitations:

* Resistance to tampering or cloning is assumed based on the current state of art and science, and reasonable limits on the adversary’s budget. 
* Attacks get better and cheaper over time, requiring regular replacements and costly certification processes. 
* Untested at scale for fully offline payments with large (e.g., over \$1000) account balances. 
* Manufacturer and supply chain of the specialized hardware must be fully trusted, akin to banknotes.



## Trusted computing

### TPMs, TEEs, Intel SGX, ARM TrustZone


Trusted computing uses hardware-level cryptography to provide a special mode for general purpose devices (e.g., a computer or phone) that is secure against software attacks (e.g., malware) and consumer-grade modification (e.g., jailbreaking on phones). Payment processors can use trusted computing to deploy services on untrusted cloud services and/or untrusted user devices. 

Limitations:

* Security appears to be lower than dedicated tamper-resistant hardware---many attacks are known and discovered each year that work in laboratory conditions. 
* Currently, most operating system and platform manufactures take exclusive control over these features.
* Manufacturer and supply chain of general purpose devices must be fully trusted.



## Privacy metrics

### $k$-anonymity, differential privacy

One approach to privacy is to remove information before data is shared. This primarily protects individuals in the data set. Privacy metrics guide the selection and amount of information to remove. Common forms include understandable heuristics (e.g., k-anonymity) for making records more similar (e.g., distinct birth years, 1972 and 1978, are replaced by the attribute 1970s); and mathematically principled frameworks that add random noise (e.g.,  differential privacy), resembling randomized response techniques known from population surveys. This approach is often combined with cryptography to blind data instead of fully removing it.

Limitations:

* The entity applying the metric needs to have a copy of the raw data and must be trusted. 
* These metrics do not tell users how to remove information; they only specify when to stop. 
* Applications are best-suited for generating statistics on payments (secondary use of the data) rather than protecting the primary data. 
* Naturally, heuristic metrics are based on non-trivial assumptions about the data distribution, and, if inaccurate, privacy may be compromised. Specifically, if data is added over time (in live systems), it becomes harder and harder to continually assure the assumptions are met. 
* Strict mathematical frameworks are very conservative, protecting the most extreme cases (i.e., the most exposed individual in the worst case). This can result in adding so much noise that the data becomes useless. 
* Claims regarding the use of differential privacy in industry often prioritize the utility of the data over privacy hence being more to the effect of marketing purposes rather than protecting users. 
  