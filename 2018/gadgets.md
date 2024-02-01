Gadgets 

♾️ ♾️ ♾️

Tests, proofs, operations



=== MULT (MPC)

MPC

Due to Cramer, Damgard, Neilson (CDN)

Given [x], [y], find [xy]

Each party chooses [ri]

Compute x’ = [x] + [r1] + [r2] +… and DECRYPT

Each party broadcasts: [y]^(-ri)=[-y*ri]

Compute: [xy] = [y]^(x’)+[-y*r1]+[-y*r2]+…

Note: Committed scalar protocol below can prove each party does it correctly

Note: Requires decryption of arbitrarily large ciphertexts



=== MULT (Precomp)

Trustees

Beaver’s Trick

Given [x], [y], find [xy]

Precompute (somehow) [a], [b], [ab] for random a,b

Compute:

x’ = [x]-[a] and DECRYPT

y’ = [y]-[b] and DECRYPT

[xy] 	= x’y’+ x’[b] + y’[a] + [ab]

​	= [x-a][y-b]+[xb-ab]+[ya-ab]+[ab]

​	= [xy - xb - ya + ab + xb - ab + ya - ab + ab]

​	= [xy]

Note: Requires decryption of arbitrarily large ciphertexts



=== MULT (BGN-like lattice)

Trustees

Original idea

Uses BGN-like encryption with full decryption (no Elgamal, BGN)

Given [x], [y], find [xy]

Coin toss [a] and [b]

Compute [ab] with pairing

Apply Beaver’s method above: only works if you can decrypt arbitrary ciphertexts



=== MULT (CONDITIONAL)

Trustee

Schoenmakers and Tuyls

Given [x] and “bit” [b] from {-1,1}, return [x] if b=1; else [-x]

Each party selects r from {-1,1} and successively blinds both [x] and [b]:

[x’]=[x*r1*r2*…]

b’=[b*r1*r2*…] and DECRYPT

Check that b’ is {-1,1}

Compute [xb]=[x’]b’



=== MULT (BITWISE)

Trustees

Schoenmakers and Tuyls

Given [x] in binary [x1],[x2],[x3],… and [y], find [xy]

Map [xi] from {0,1} to {-1,1} with [xi] = 2[xi] - 1

Compute [x1][y], [x2][y], [x3][y], … using MULT (CONDITIONAL)

Map [xi*y] from {-y,y} to {0,y} with [xi*y] = ([xi*y] + [y])/2

Compute [xy] = [x1*y] + [x2*y]2 + [x3*y]4 + …



=== COMMITTED SCALAR

Given [x], compute [x]^a=[xa] and ReRand, proving a matches [a]

Intuition: publishing [a] lets you prove properties about it

For exponential Elgamal, prove: [g] [g^x] [g^a] [g^(xa)] for a and publicly generated [g]



=== LUT

Mix and Match



=== n-AND

Compute:

[b1]+[b2]+[b3]+…+[bn] = [b]



LUT:

[b]	Out

[0]	[0]

[1]	[0]

[2]	[0]

…

[n]	[1]



Cheaper than LUT for whole operation (order n instead of 2^n) 



=== n-OR

Compute:

[b1]+[b2]+[b3]+…+[bn] = [b]



LUT:

[b]	Out

[0]	[0]

[1]	[1]

[2]	[1]

…

[n]	[1]



Cheaper than LUT for whole operation (order n instead of 2^n). If you are ready to reveal, you can just PET with [0] once and decide if the output should be [0] (PET->true) or [1] (PET->false)



=== XOR

2PC

Schoenmakers and Tuyls

Given [x] and [y], compute [x + y mod 2]=[x]+[y]-2[x][y] using any general MULT above or the following

Map [y] from {0,1} to {-1,1}: [y’]=2[y]-1

Run CONDITIONAL on [x] and [y’] to obtain [xy’]

Compute xor [x+y mod 2]=[y]-[xy’]



xy	y’	xy’	y-xy’

00 	-1	0	0

01	1	0	1

10	-1	-1	1

11	1	1	0



=== NAND/XOR/2 Gate

Danezis, Fournet, Groth, Kohlweiss

Trustees

BGN encryption

Given [a] and [b]

Shuffle [c=0] and [c=1]

For NAND, compute square span program with first [ci]

[z] = [a]+[b]-2[ci]

Compute [z]([z]-1), blind, and decrypt

Alternatively, for Elgamal, do a PET with 0 and a PET with 1

If non-zero (not a match), use [ci] as output

If zero, use other [ci] as output



=== NAND

2PC

Represent 0 as [0][1] and 1 as [1][0]

Given [a][a] and [b][b]

T1: Shuffle [a][a]

T2: Open aa

T2: If aa=0: output [0][1][b][b]

T2: If aa=1: output [b][b][0][1]

T1: If shuffled, take [0][1] otherwise [b][b]: Result is AND

T1&2: Swap cards: NOT

Cases:

- a=0, shuffle=0: [1][0]
- a=0, shuffle=1: [1][0]
- a=1, shuffle=0: [b][b]
- a=1, shuffle=1: [b][b]



=== SELECT

Trustees

Adida and Wikstrom

Given [x] and bit [b], return [x] if b=1; else [0]

Compute (with BGN or any MULT mechanism above): [x]*[b]

Also: can do with add-homomorphic with nested encryptions: explore more



=== SELECT CONDITIONAL

Toft

C <- {A,B} | b

If b=0, C is A. If b=1, C is B.

[C]=[A]+[b]([B]-[A])



=== SELECT CONDITIONAL 

Chou & Orlandi

1-of-2 obvious transfer

Alice sends A=g^a and receives B=g^b when bit is 0 and B=A*g^b when bit is 1

Alice encrypts m1 under k1=B^a and m2 under k2=(B/A)^a





=== ZERO SHARING

Hao, Ryan, Zielinski

Improves Kiayais and Yung

A set of people generate shares that sum to 0 (or multiply to 1)

Round one: each party posts g^x and PoK(x) in a queue

Round two: each party computes share=(g^z)^x=g^xz

g^z = (g^x)(g^x)(g^x)…(g^-x)(g^-x)(g^-x)

Where the original terms are the people in front of you and the inverted terms are the people after you

The product of all g^xz terms is 1 (i.e., g^0)

Zero shares are used to blind messages (that are not otherwise encrypted) and will cancel out if the messages are summed (in the exponent)



============

TESTS

============



=== MEMBERSHIP

Trustees

Generalization of BGN paper

Given [x], want to show x is in {a,b,c,d,…} without revealing x

Compute polynomial with roots equal to members

[p]=([x]-a)([x]-b)([x]-c)…

Decrypt p and see if it is 0

The number of multiplications you can do is equivalent to the size of the set

To show x is {0,1}, normal BGN suffices, otherwise you’ll need somewhat-homomorphic encryption



=== EQUALITY / INEQUALITY

Trustees

From Jacobson and Juels

Do [x] and [y] encrypt the same thing?

Compute [d]=[x]-[y]

Blind d’=[d]b and DECRYPT

Return true upon d=0; otherwise false



=== RANGE

Adapted for the Trustees

Given [x], is x in [0,3r]?

Let r be prime and (p-1)=a*r

Compute shares [si] such that [s1+…+st]=[x]

Give one share to each trustee (proxy-reencryption) to decrypt

Each trustee decrypts [si] and asserts [si] and [ui] = [si mod r]

Above requires a proof, probably possible by showing some [fi] such that [ui]*[fi]=[si] and doing some range proofs on [u] and [fi]???

Perform 5 PETS

[u1+…+ut] =? [x]

[u1+…+ut] =? [x+q]

[u1+…+ut] =? [x+(p mod q)]

[u1+…+ut] =? [x+a]

[u1+…+ut] =? [x+(p mod q)+q]

Shuffle PET output before final decryption

In Range: One PET is true

Out of Range: All PETs are false (knowing which one leaks info about x)

From Peng, Boyd, Dawson, Okamoto



=== PARITY

Given [x], return 0 if odd and 1 if even

OPEN ?? (Useful -> could do range tests:

2[x]:	Even		x: {0,q}

​	Odd		x: {q,p}

4[x]:	Even		x: {0,q/2} or {q,3q/4}

​	Odd		x: {q/2,q} or {3q/4,q}

Given [x], return [0] if odd and [1] if even

OPEN ?? (Useful -> could do binary decomposition under encryption)



=== COMPARISON

Given [x] and [y], return: [-1] if x<y, [0] if x=y, and [1] if x>y

Can be simplified to binary cases (e.g., x>y or x=y)

From Schoenmakers and Tuyls

Requires binary representation so [x] and [y], and uses MULT (CONDITIONAL)



=== COMPARISON

Given [x] and [y], return: true if x<y

Compute [x-y] and blind with bounded b: b[x-y]

Decrypt and determine if b(x-y) is negative or positive

The value b does not perfectly blind x-y but can be shown to be good enough





============

PROOFS

============



=== Membership Proof

Interactive

Benaloh’s capsule

Given [x], want to show x is in {a,b,c,d,…} without revealing x

Alice permutes {[d],[b],[a],…}

Bob flips coin

Upon heads, Alice shows permutation is well-formed

Upon tails, Alice chooses value of x from list (say x=d) and computes: [x]-[d]=0 and decrypts to show it is zero



=== Equality Proof

Interactive

Cut-and-Choose

Requires homomorphic encryption

Prover sends [[x]] and [[y]]

Verifier picks one at random ([[z]]=[[x]] or [[z]]=[[y]]) and multiplies in a: [[z]]^a = [[z*a]] 

Prover decrypts and determines a 

(If x!=y, Prover finds two possible a values and must guess with 1/2 probability)



=== Range Proof

Non-interactive

Number has n-d leading zeros

Due to Mao (Chaum et al?)

Is [x] less than 2^d ?

Requires binary encryption of x: [x1],[x2],[x3],…[xd]

Show there are no more than d [xi’s]

Prove [xi] is 0 or 1 for each [x1]

Compute: [x] = [x1]+[x2]2+[x3]4+[x4]8…+…[xd]2^(d-1)



=== Range Proof

Non-interactive

Golle’s poker

Given [x], want to show x is in {0,51}

Publicly encrypt the following pairs and shuffle:

[0],[1]

[0],[2]

[0],[3]

[0],[6]

[0],[13]

[0],[26]

Choose one number from each pair, sum them up, and show, with a plaintext equality test, this sum is equal to [x].

Why 1,2,3,6,13? Every number in {0,51} can be written as a combination of 1 of these numbers or zero (a weird non-binary decomposition)

x is [x1]+[x2]2+[x3]3+[x4]6+[x5]13+[x6]26



=== Range Proof

Non-interactive

Paillier Setting

Adapted from Szepieniec & Preneel

Define Lift(c) as embedding ciphertext c from Zn^2 to Zn^3

Given Enc(a) and range R

Compute: [delta]=R-[a]

Compute: [z]=(Lift[R]-Lift([a]) - Lift([delta))

Decrypt [z]

If z=0, a<R

Otherwise z will be some multiple of n and a>R

Trick: Decrypt [z] requires knowing the private key in Zn^3 given that you know the private key in Zn^2; requires some algebraic group structure



=== Range Proof

Interactive

Brickell/Chaum/Damgard/Van de Graaf

Adpated from Boudot (note error in Boudot concerning ranges leads to ambiguity, think following is correct)

Given [x], show [x] is in range {0,100}

Alice choses random [a] from {0,100} and computes [b] = 1-a

Bob flips coin

Upon heads, Alice shows [a] and [b] are well-formed

Upon tails, Alice computes x’=[x+a] and x’’=[x+b], decrypts, and shows they are in {-100,200}





=== Polynomial Commitments

Setup: random s, compute g, g^s, g^(s^2), g^(s^3), …, destroy s

Polynomial: P() = c0 + c1*x + c2*x^2 + c3*x^3 +…

Commit to P() == Commit to P(s): g^P(s) = [(g)^c0] * [(g^s)^c1] * [(g^(s^2))^c2] + …

To open at point w, commit to P(w): Compute a new polynomial Pw() = P() - P(w) / (x-w)

Note that Pw(s)*(x-w) + P(w) = P(s) 

Then commit to Pw() by committing to Pw(s) and produce {w,z=P(w),Pw(s)} as proof

Verify {w,z} by computing: e(g^P(s),g) =? e(g^Pw(s), (g^s)/(g^w)) * e(g,g)^z

e(g,g)^P(s) =? e(g^Pw(s), g^(s-w)) * e(g,g)^z

e(g,g)^P(s) =? e(g,g)^Pw(s)(s-w) * e(g,g)^z

e(g,g)^P(s) =? e(g,g)^(Pw(s)(s-w)+P(w))

P(s)=?Pw(s)(s-w)+P(w)





=== Inequality via Non-Zero Proof

Brands

Given com=g^m*h^r=Commit[m,r], prove that m is not zero

Note that m=0 is simple by proving knowledge of r such that h^r=c (since g disappears)

Choose random r and t, compute b=(c)^r * h^-t

Compute chal = H(c1)

Compute d1 = s + chal * m^(-1) 

Compute d2 = t + chal * m^(-1) * r

Output {b, chal, d1, d2}

Verify b =? comm^d1 * (g^(-chal) * h^(d2))

b*g^chal =? comm^d1 * h^d2



=== Inequality via Non-Zero Proof

Prove [[a]] is not commitment to b

Compute [[a-b]] = [[d]]

Problem is now: prove [[d]] not a commitment to 0

Compute d^-1 and show [[d^-1]] as a witness

If d=0, there is no inverse

Prove < g, [[d]], [[d^-1]], g> is a DDH-tuple 



=== Inequality Proof

Given [[x]] and [[y]], prove x!=y

Publicly compute [[d]] = [[y]]*[[-x]] = [[y-x]]

Privately compute e such that ed=1 (mod q)

Compute [[ed]] = [[d]]^e and prove knowledge of e

Example: [[1]], [[d]], [[1]]^e, [[d]]^e is a DH tuple

Prove [[ed]] = [[1]]

Note: if x and y are equal, d=0 and no such e exists



=== Inequality Proof

Interactive

Cut-and-Choose

Requires re-randomizable encryption

Prover sends [[x]] and [[y]]

Verifier picks one at random, rerandomizes it and sends back

Prover decrypts and asserts whether it matches [[x]] or [[y]]

(If x==y, Prover must guess with 1/2 probability)