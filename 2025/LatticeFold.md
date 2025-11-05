LatticeFold



SIS problem

Warm-up problem: Given a random matrix A with n rows, m columns, and elements that are in Zq, find solutions for z that solve the equation Az=0. z is a m-length vector and 0 is an n-length vector (of all zeros). z is not allowed to be 0. The mathematical terms for z is a kernal. 

How many solutions to z are there?

1. Case: every column in A is linearly independent: rank(A)=min(n,m)
   1. Tall matrix (n>m): only solution is z=0
   2. Square matrix (n=m): one solution
   3. Wider by 1 matrix (m=n+1): q solutions
   4. Wider by 2 matrix (m=n+2): q^2 solutions
   5. Wide matrix (m>n): q^(m-n) solutions
2. Case: not all columns are independent rank(A)<min(n,m) -> start over with a new A



SIS problem: find a solution z where every value in z is between [-B,B] for some bound B that is much smaller than q (B<<q/2), in other words, z is "short"

If there are q^(m-n) solutions, probably some of these are going to be short if we let m get bigger and bigger than n. However we can actually prove there MUST be a solution, it is not just probabilistic. 

Think of a function $f(z_i)=A*z_i$ that maps possible solutions $z_i$ to output $f(z_i)$ which may or may not be the 0 vector. Consider the following exhaustive search algorithm:

* Choose a $z_i$ (randomly or with a counter) but keep $z_i$ "super-short" where super-short means between [-B/2, B/2]. How many super-short $z_i$'s are there? Each element of $z_i$ is in [-B/2,B/2] so there are B+1 possible values (B/2 negative values, B/2 positive values and 0). The vector is $m$ elements long so there are $(B+1)^m$ possible super-short values to brute-force over. How many outputs are there? An output is a vector of length $n$ where each element is in Zq so $q^n$.
  1. Case: $f(z_1)=0$. You are done, $z_1$ solves the SIS problem.
  2. Case: $f(z_1)=f(z_2)$ where $z_1\neq z_2$. There is a collision where some input $z_1$ produces the same output as a different $z_2$. The output is not the zero vector (otherwise we'd be in case 1). This means that $A*z_1=A*z_2$. Consider the vector $\hat{z}=z_1-z_2$, what is $f(\hat{z})$? 
     * $f(\hat{z})=A*\hat{z}=A*(z_1-z_2)=A*z_1-A*z_2=A*z_1-A*z_1=0$ when  $A*z_1=A*z_2$
     * So $\hat{z}$ is a solution to the SIS problem if it is short and non-zero. 
       * $\hat{z}\neq 0$ since $z_1\neq z_2$ therefore it is non-zero
       * Is $\hat{z}$ short given that $z_1$ and $z_2$ are super short? In other words, if you subtract two super-short vectors, do you get a result that is at least short? This is exactly the case, if $z_1$ and $z_2$ are in [-B/2,B/2] then $\hat{z}$ will be in [-B,B]. For example the biggest possible element after subtraction could be $B/2 - (-B/2)=B$, and symmetrically for the smallest.
       * Thus $\hat{z}$ is a solution to the SIS problem

Now back to the original question, is an SIS solution guaranteed to exist? The answer will be yes if at least one collision exists between  supershort vectors $z_1$ and $z_2$. If the number of supershort vectors (inputs to $f$) are larger than the number of outputs, then (by the pigeonhole principle) there must be at least one collision. When you find the collision, you subtract the values and the result is an SIS solution, therefore there must be at least one SIS solution. When are the number of inputs greater than the outputs?

* $(B+1)^m>q^n$ which we can realize because we control $m$ and $n$ which are the dimensions of the matrix. So essentially we make the matrix wider and wider until this bound holds, then an SIS solution must exist.
* Rewritten: $m>n\cdot log(q) / log(B+1)$

Trivia:

* If $\hat{z}$ is an SIS solution then $-\hat{z}$ is also an SIS solution
* If we setup SIS problem to find a specific output vector $b$ instead of 0 ($Az=b$), the problem (called ISIS) is exactly the same. In other words, $b=0$ is just a convenient value to choose, it is not essential to the hardness of the problem that SIS output is zero. 
* A can be in normal form: [A'||I] where A' is a square matrix and I is the identity matrix and the hardness stays the same. 

