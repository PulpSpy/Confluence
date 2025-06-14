# Proofs across groups



| Step                                       | Same Group $G_p$ (Chaum–Pedersen)                            | Different Groups $G_p, G_q$ (Π_dleq style)                   |
| ------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Public Inputs**<br>**(known to both)**   | $X_p = g_p^{x}h_p^{r_p}$<br>$Y_p = g_p^{x}h_p^{r_p'}$        | $X_p = g_p^{x}h_p^{r_p} \in G_p$<br>$X_q = g_q^{x}h_q^{r_q} \in G_q$ |
| **Secret Inputs**<br>**(known to prover)** | $(x, r_p, r_p')$                                             | $(x, r_p, r_q)$                                              |
| **1. Commitment**<br>(Prover → Verifier)   | Choose random:<br>$k, t_p, t_p' \in [0,p-1]$<br><br>Compute commitments:<br>$K_p = g_p^{k}h_p^{t_p}$<br>$L_p = g_p^{k}h_p^{t_p'}$ | Choose random small integers:<br>$0 \leq k < 2^{b_x+b_f}$<br>$t_p \in [0,p-1], t_q \in [0,q-1]$<br><br>Compute commitments:<br>$K_p = g_p^{k}h_p^{t_p}$<br>$K_q = g_q^{k}h_q^{t_q}$ |
| **2. Challenge**<br>(Verifier → Prover)    | Choose random:<br>$c \in [0,p-1]$                            | Choose small challenge:<br>$c \in [0,2^{b_c}-1]$             |
| **3. Response**<br>(Prover → Verifier)     | Compute mod $p$:<br>$z = k + c x \mod p$<br>$s_p = t_p + c r_p \mod p$<br>$s_p' = t_p' + c r_p' \mod p$<br><br>Send $(z,s_p,s_p')$ | Compute integer arithmetic (no mod):<br>$z = k + c x$<br>Abort & retry if:<br>$z \notin [2^{b_x+b_c},2^{b_x+b_c+b_f}-1]$<br><br>Compute:<br>$s_p = t_p + c r_p \mod p$<br>$s_q = t_q + c r_q \mod q$<br><br>Send $(z,s_p,s_q)$ |
| **4. Verification**<br>(Verifier)          | Check:<br>$g_p^{z}h_p^{s_p} \stackrel{?}{=} K_p X_p^{c}$<br>$g_p^{z}h_p^{s_p'} \stackrel{?}{=} L_p Y_p^{c}$<br>If both hold: accept. | Check:<br>$g_p^{z}h_p^{s_p}\stackrel{?}{=}K_p X_p^{c}$<br>$g_q^{z}h_q^{s_q}\stackrel{?}{=}K_q X_q^{c}$<br>Also verify range proof for $x$<br>If all hold: accept. |