Security Proofs

Adversaries, etc



See also: composition



Security proofs are broken down along the following lines



1) Adversary:



Passive - honest-but-curious

Active - fully malicious



2) Corruptions:



Static - non-adaptive corruptions

Dynamic - adaptive corruptions



3) Composition:



Stand-alone - a single run in isolation is secure

Composable - multiple runs composed in certain ways are secure



Further types of composition:

​	- Self composition

 		- Sequential

 		- Parallel

 		- Concurrent

​	- General composition

​		- Universal composition



===

Types

===



Game-based: no composition, non-adaptive adversaries

Simulation: choose (?) 

UC: General composition (but not necessarily adaptive adversaries?)



===

History

===



GM84 -> security reductions

GGM86 - hybrid argument -> sequence of transitions based on indistinguishability (similar to games)

BG89 - early (but not necessarily first) game-based proof -> games make transitions based on both indistinguishability and failure events



GMW86 - Simulation

GMW87 - Indistinguishability

Bea92 - Real/Ideal

MR92 - Real/Ideal

Can01 - UC



===

Primitives

===



Encryption -> no non-interactive, non-committing encryption scheme exists in the plain model or non-programmable RO (NPRO) model [Nie02]