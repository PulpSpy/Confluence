# Blockchain Voting Projects

## ZKVoting

### Links

Website: 

Talk: https://www.youtube.com/live/FDj-hoF4xpA?si=UT53aRB1cMDrR1Eh

Paper: https://eprint.iacr.org/2024/1003.pdf

### Use

* National Election Commission of South Korea, used in public sector elections for local elections, referendums, & party nominations
* Challenge in using non-standardized modern cryptography, cannot be certified

### Protocol

#### Nullifiable commitments

Generate system-wide parameters:

- Pick secret: $\rho \in \mathbb{Z}_q$ and generators: $g_1, g_2, g_3\in \mathbb{G}$ and set $g_4 = g_1^\rho$ and $g_5=g_2/g_4$

- Master public key: $\text{mpk} = (g_1, g_2, g_3, g_4,g_5)$
- Master secret key: $\text{msk} = \rho$

Alice asks for a real ($b=1$) key or fake ($b=0$) key:

* Pick random $h_1$ and set $h_2= h_1^\rho \cdot g_3^b$
* Send Alice her specific $\mathsf{commKey} = ((g_1, h_1), (g_2, h_2))$

Alice commits to message $m$ using $\mathsf{commKey}$:

* Pick random $r$
* $\mathsf{comm} = \langle C_1, C_2\rangle = \langle g_1^r \cdot h_1^m, g_2^r \cdot h_2^m \rangle$

Alice opens commitment:

* Reveal $\langle \mathsf{commKey},m,r \rangle$ and anyone can check

Manager processes commitment:

*  Compute $\frac{C_2}{C_1^\rho}$
  * $=\frac{g_2^r \cdot (h_2)^m}{g_1^{r\rho} \cdot h_1^{m\rho}}$ 
  * $=\frac{g_2^r \cdot (h_1^\rho \cdot g_3^b)^m}{g_1^{r\rho} \cdot h_1^{m\rho}}$
  * $=\frac{g_2^r \cdot h_1^{m\rho} \cdot g_3^{mb}}{g_1^{r\rho} \cdot h_1^{m\rho}}$
  * $=\frac{g_2^r \cdot g_3^{mb}}{g_1^{r\rho}}$
  * $=\frac{g_2^r}{g_1^{r\rho}}\cdot g_3^{mb}$
  * $=(\frac{g_2}{g_1^{\rho}})^r\cdot g_3^{mb}$
  * $=(\frac{g_2}{g_4})^r\cdot g_3^{mb}$
  * $=g_5^r\cdot g_3^{mb}$
  * Think of this as a commitment to $m\cdot b$ where $b$ defines if it is real or fake

### Voting

Recap:

* Encryption-based
* Voter has a key pair and is issued a "casting key"
  * In practice, EA issues keys for usability
  * Corrupt EA could trace votes
  * Voting is split -> unclear details
* Ballot is encrypted vote, some "serial number" to prevent double voting, and ZKP (ballot is ok, candidate is authorized)
  * $\mathsf{ballot}=\langle \mathsf{Enc}(v), \mathsf{serial}, \pi \rangle$
* Splitting process into ballot share 1 and share 2
  * $\mathsf{ballot_1}=\langle \mathsf{Enc}(v), \mathsf{serial}', \pi_1 \rangle$
  * $\mathsf{ballot_2}=\langle \mathsf{Enc}(v)', \mathsf{serial},\pi_2\rangle$
  * $\mathsf{ballot_3}=\langle \pi_3\rangle$
  * First two are posted over an anonymous channel (so they are not linked to each other) and then they are "connected" with transaction 3????

### Non-standard assumptions:

* Strong anonymous channel (Tor not good enough, mix network)



## Opn.vote

Website: https://www.opn.vote

Whitepaper: https://www.opn.vote/wp-content/uploads/2024/09/opn.vote_short_paper_LNCS_style-e-vote-ID-2024-Track-4.pdf

Recap:

* Blind-signature based like FOO
* Users have a pseudonymous account
* Uses meta-transactions so users do not need to have a wallet
* Running tally is protected with encryption (EA's public key) and the key is posted after voting closes

Non-standard assumptions:

* Strong anonymous channel (Tor not good enough, mix network)

Use:

* ABSTIMMUNG21 referendum in Germany (non-governmental?) with ~250K eligible voters



