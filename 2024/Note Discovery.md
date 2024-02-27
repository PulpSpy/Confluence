# Solution 1

This is a fun subject, I wanted to sketch an idea that could probably be massively improved. It is a slightly different threat model (in terms of a "trusted" or "untrusted" servers) than the proposed ones. It also requires very heavy computation for the servers (quadratic in the number of notes) but is lightweight for the client. It is adapted from these voting protocols: [JCJ](https://eprint.iacr.org/2002/165.pdf), [Selections](https://eprint.iacr.org/2011/166.pdf). 

Consider a set of 10 servers (trustees). The user is asked to trust that at least 1 is honest. The trustees generate a shared 10-out-of-10 public key for a threshold encryption scheme that is partially homomorphic. One candidate is Elgamal where public keys are the same format as signature schemes like ECDSA and Schnorr.

A big simplifying assumption (that I don't know works in practice) is that the set of note recipients are known at the beginning of an epoch and if a user is not in this set, they cannot receive notes until the next epoch.

For each epoch, the protocol is as follows:

* Each recipient pre-registers to receive notes by choosing a pseudonym (as a random value) and encrypting the nym under the trustees' public key. The encrypted nym is initially associated with the user it belongs to, as identified by their public key or address or any unique ID. So Alice with address $Addr_A$ posts $\langle Addr_A, \mathsf{Enc}(Nym_A)\rangle$. The protocol must ensure this value is actually from $Addr_A$ either through signatures or by being sent in a transactions from $Addr_A$.
* Assume 4 users register as follows: 

| $Addr_A$ | $\mathsf{Enc}(Nym_A)$ |
| -------- | --------------------- |
| $Addr_B$ | $\mathsf{Enc}(Nym_B)$ |
| $Addr_C$ | $\mathsf{Enc}(Nym_C)$ |
| $Addr_D$ | $\mathsf{Enc}(Nym_D)$ |

* The trustees each take a turn performing the following tasks
  * The first trustee takes the list and encrypts each element in the first column (in subsequent steps, they will instead rerandomize each already-encrypted values) and re-randomizes each value in the second column.
  * The trustee then shuffles the list row-wise
  * The trustee publishes the rerandomized and shuffled list
  * The trustee produces a ZKP that the output list is a permutation of the input list  (many proof schemes exist, including the [Neff shuffle](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=3d11b633abfa1a99391463345f7a1e1adb6a972b)) without revealing the permutation they used or the random factors they used
* After the last trustee has shuffled the list, each trustees will check each ZKP and sign the list if it is valid. Once all trustees have signed the list, the trustees will employ threshold decryption to decrypt the $Nym$ column of the list. The result is called the $\mathsf{Roster}$ and it is valid for one epoch and it might look as follows:

| $\mathsf{Enc}(Addr_A)$ | $Nym_A$ |
| ---------------------- | ------- |
| $\mathsf{Enc}(Addr_C)$ | $Nym_C$ |
| $\mathsf{Enc}(Addr_D)$ | $Nym_D$ |
| $\mathsf{Enc}(Addr_B)$ | $Nym_B$ |

* Note: as long as one trustee is honest, no one will know which user ends up in which row of the table (except the user themselves who can identify their nym).

To send a note from Alice to Bob, Alice encrypts Bob's identifier $\mathsf{Enc}(Addr_B)$ and includes this in the payload of the encrypted note. Call $\mathsf{Enc}(Addr_B)$ the $\mathsf{Hint}$.

* Note: Because our simple notation does not include random factors in $\mathsf{Enc}$, it is important to note that this value $\mathsf{Enc}(Addr_B)$ will not be bitwise identical to the corresponding value $\mathsf{Enc}(Addr_B)$ in the 4th row of the $\mathsf{Roster}$. Both represent an encryption under the same public key of the same message but will have different random factors and thus be different ciphertext values. In fact, for Elgamal, they will be computationally indistinguishable from an encryption of a different message (under standard cryptographic assumptions).

* Trustees will take the $\mathsf{Hint}$ and conduct a plaintext equality test (PET) between each encrypted key in the first column of the $\mathsf{Roster}$:

  | $\mathsf{PET}[\mathsf{Enc}(Addr_A),\mathsf{Hint}]\rightarrow False$ | $Nym_A$ |
  | ------------------------------------------------------------ | ------- |
  | $\mathsf{PET}[\mathsf{Enc}(Addr_C),\mathsf{Hint}]\rightarrow False$ | $Nym_C$ |
  | $\mathsf{PET}[\mathsf{Enc}(Addr_D),\mathsf{Hint}]\rightarrow False$ | $Nym_D$ |
  | $\mathsf{PET}[\mathsf{Enc}(Addr_B),\mathsf{Hint}]\rightarrow True$ | $Nym_B$ |

* Note: a PET is very close to a threshold decryption in cost, it just requires a pre-processing step before decryption. So doing a PET with every value in the table is basically trial decryption, it is just being done by the trustees instead of the user. 
* The trustees will then add $Nym_B$ to the encrypted note.
* Bob will look through all notes for anything marked $Nym_B$.

Additional notes and optimizations:

* The protocol is not perfectly private as notes received by the same recipient in an epoch can be linked together. 
* Users can reuse $Nym_A$ but this extends the linkage possible. 
* Users can supply a batch of $\langle\mathsf{Enc}(Nym_{A,E1}),\mathsf{Enc}(Nym_{A,E2}),\ldots\rangle)$ values for a set of future epochs $E1,E2,E3,\ldots$ at the start of the protocol.
* The threshold decryption can be m-out-of-n for different values of m and n. For example, if it were 7-of-10, then the protocol could proceed when 3 or less trustees were offline, however users would have to trust that at least 4 trustees are honest rather than 1. 



## Solution 2

Here is another solution that pushes the work onto the sender rather than the receiver.

Input: List of recipients per epoch, by public key. $\textsf{Roster:} \langle pk_A, pk_B, pk_C, pk_D, pk_E\rangle$

Sender Protocol: 

1) Sender creates a list to represent which recipient(s) she is selecting from the $\textsf{Roster}$. To select the fourth user David ($pk_D$), she would create: $\langle 0,0,0,1,0\rangle$
2) Encryption is exponential Elgamal (as in main post above). For notation, let $[[m]]_{_A}$ be an encryption of message $m$ under $pk_A$.
3) Sender encrypts each bit of $\langle 0,0,0,1,0\rangle$ with the public key that corresponds to $\langle pk_A, pk_B, pk_C, pk_D, pk_E\rangle$. Call this the $\mathsf{Hint:}\langle [[0]]_{_A},[[0]]_{_B},[[0]]_{_C},[[1]]_{_D},[[0]]_{_E}\rangle$
4) Sender sends encrypted note along with $\mathrm{Hint}$.

Receiver Protocol:

1. Receiver David fetches all $\mathrm{Hints}$ from within an epoch:

|                 | A            | B            | C            | D            | E            |
| --------------- | ------------ | ------------ | ------------ | ------------ | ------------ |
| Hint for Note 1 | $[[0]]_{_A}$ | $[[1]]_{_B}$ | $[[0]]_{_C}$ | $[[0]]_{_D}$ | $[[0]]_{_E}$ |
| Hint for Note 2 | $[[0]]_{_A}$ | $[[0]]_{_B}$ | $[[1]]_{_C}$ | $[[0]]_{_D}$ | $[[0]]_{_E}$ |
| Hint for Note 3 | $[[0]]_{_A}$ | $[[0]]_{_B}$ | $[[0]]_{_C}$ | $[[0]]_{_D}$ | $[[1]]_{_E}$ |
| Hint for Note 4 | $[[0]]_{_A}$ | $[[0]]_{_B}$ | $[[0]]_{_C}$ | $[[0]]_{_D}$ | $[[1]]_{_E}$ |
| Hint for Note 5 | $[[0]]_{_A}$ | $[[0]]_{_B}$ | $[[0]]_{_C}$ | $[[1]]_{_D}$ | $[[0]]_{_E}$ |
| Hint for Note 6 | $[[0]]_{_A}$ | $[[1]]_{_B}$ | $[[0]]_{_C}$ | $[[0]]_{_D}$ | $[[0]]_{_E}$ |

2. David discards all columns except D.
3. David takes the values in the D column and sums them homomorphically (one modular multiplication per homomorphic addition) and decrypts the resulting ciphertext. The integer will represent how many messages he received in the epoch. 
4. If zero, David is done for this round.
5. If non-zero, David uses binary search to locate the Hint(s) that belong to him.

Analysis:

1. Hints are large ($n$ ciphertexts for $n$ recipients) and probably kills this idea.
2. Computation for sender is expensive ($n$ encryption for $n$ recipients) however a trusted server could help by generating empty hints for senders to use: $\langle [[0]]_{_A},[[0]]_{_B},[[0]]_{_C},[[0]]_{_D},[[0]]_{_E}\rangle$. The sender then replaces $[[0]]_{_D}$ with $[[1]]_{_D}$ to send a note to David. Of course, the server can trace this Note. 
3. However the sender could ask for empty hints from $n$ semi-trusted (honest-but-curious) servers, add them together homomorphically (cheaper than an encryption), and then it is untraceable if 1 of the $n$ servers is honest. Even though a malicious server cannot trace the Note anymore, it could provide a Hint with values other than 0. Note that the recipient will still recieve the note, it will just create grief for non-recepients who think the note is for them. 
4. Requiring a ZKP from the server can side-step this, but now the sender is verifying ZKPs to sidestep encrypting and the ZKPs would need to be succinct for it to be a net savings in computation. Note that any discovered malicious behaviour is independently verifiable so we could also use incentives/slashing to enforce correct behaviour. 



## Solution 3

Here is another solution that allows an open set of recipients. However it trades off this assumption with a new assumption that might not work that great in practice:

* Assume that notes are stored in a sequence of slots and the sender can obtain the slot number for where they put the note.
* The setup is the same as the other two solutions: a group of non-colluding trustees utilizing threshold additively homomorphic encryption
* 

Sender Protocol:

* The sender obtains the recipients key: $pk_A$ and prepares the note
* The sender is given a "slot" in the list of notes, which are organized into blocks of 250 notes. 



Input: List of recipients per epoch, by public key. $\textsf{Roster:} \langle pk_A, pk_B, pk_C, pk_D, pk_E\rangle$

Sender Protocol: 

1) Sender creates a list to represent which recipient(s) she is selecting from the $\textsf{Roster}$. To select the fourth user David ($pk_D$), she would create: $\langle 0,0,0,1,0\rangle$
2) Encryption is exponential Elgamal (as in main post above). For notation, let $[[m]]_{_A}$ be an encryption of message $m$ under $pk_A$.
3) Sender encrypts each bit of $\langle 0,0,0,1,0\rangle$ with the public key that corresponds to $\langle pk_A, pk_B, pk_C, pk_D, pk_E\rangle$. Call this the $\mathsf{Hint:}\langle [[0]]_{_A},[[0]]_{_B},[[0]]_{_C},[[1]]_{_D},[[0]]_{_E}\rangle$
4) Sender sends encrypted note along with $\mathrm{Hint}$.

Receiver Protocol:

1. Receiver David fetches all $\mathrm{Hints}$ from within an epoch:

|                 | A            | B            | C            | D            | E            |
| --------------- | ------------ | ------------ | ------------ | ------------ | ------------ |
| Hint for Note 1 | $[[0]]_{_A}$ | $[[1]]_{_B}$ | $[[0]]_{_C}$ | $[[0]]_{_D}$ | $[[0]]_{_E}$ |
| Hint for Note 2 | $[[0]]_{_A}$ | $[[0]]_{_B}$ | $[[1]]_{_C}$ | $[[0]]_{_D}$ | $[[0]]_{_E}$ |
| Hint for Note 3 | $[[0]]_{_A}$ | $[[0]]_{_B}$ | $[[0]]_{_C}$ | $[[0]]_{_D}$ | $[[1]]_{_E}$ |
| Hint for Note 4 | $[[0]]_{_A}$ | $[[0]]_{_B}$ | $[[0]]_{_C}$ | $[[0]]_{_D}$ | $[[1]]_{_E}$ |
| Hint for Note 5 | $[[0]]_{_A}$ | $[[0]]_{_B}$ | $[[0]]_{_C}$ | $[[1]]_{_D}$ | $[[0]]_{_E}$ |
| Hint for Note 6 | $[[0]]_{_A}$ | $[[1]]_{_B}$ | $[[0]]_{_C}$ | $[[0]]_{_D}$ | $[[0]]_{_E}$ |

2. David discards all columns except D.
3. David takes the values in the D column and sums them homomorphically (one modular multiplication per homomorphic addition) and decrypts the resulting ciphertext. The integer will represent how many messages he received in the epoch. 
4. If zero, David is done for this round.
5. If non-zero, David uses binary search to locate the Hint(s) that belong to him.

Analysis:

1. Hints are large ($n$ ciphertexts for $n$ recipients) and probably kills this idea.
2. Computation for sender is expensive ($n$ encryption for $n$ recipients) however a trusted server could help by generating empty hints for senders to use: $\langle [[0]]_{_A},[[0]]_{_B},[[0]]_{_C},[[0]]_{_D},[[0]]_{_E}\rangle$. The sender then replaces $[[0]]_{_D}$ with $[[1]]_{_D}$ to send a note to David. Of course, the server can trace this Note. 
3. However the sender could ask for empty hints from $n$ semi-trusted (honest-but-curious) servers, add them together homomorphically (cheaper than an encryption), and then it is untraceable if 1 of the $n$ servers is honest. Even though a malicious server cannot trace the Note anymore, it could provide a Hint with values other than 0. Note that the recipient will still recieve the note, it will just create grief for non-recepients who think the note is for them. 
4. Requiring a ZKP from the server can side-step this, but now the sender is verifying ZKPs to sidestep encrypting and the ZKPs would need to be succinct for it to be a net savings in computation. Note that any discovered malicious behaviour is independently verifiable so we could also use incentives/slashing to enforce correct behaviour. 