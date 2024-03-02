**Proof of Solvency**

Things Provisions does not solve well:

* Succinct O(1) verifier for PoL and PoA
* Incremental updates to PoL and PoA
* Dispute resolution for PoL
* Hide changes in size day-to-day (leaks commercial data)

- Clash attacks
- PoA requires a hot wallet, can we cert chain to an ephemeral key?
  - Risky for lending assets between insolvent exchanges
- Exchange profiles users who check and who do not
  - Offer PoL checks as ciphertexts, users fetch via (trivial or no trivial) PIR
  - Third party host
- Augment with complete knowledge to prevent encumbering keys to share them?
- Does offering incentives for checking work
  - Arguably no: users are already financially incentivized to check
  - Psychologically though, an explicit incentive rather than implicit one might work better