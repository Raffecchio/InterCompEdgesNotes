Paper [Here](https://arxiv.org/pdf/1909.11147)
For an arbitrary graph $G$ , define a procedure which yields a number of inter-component edges $X_P$ such that $\mathbb E[X_P] = O(n / k)$ implies the number of edges from expected $k$-out $X_k$ has $\mathbb E[X_k] = O(n / k)$.

Authors track a sampling pool $H$ of edges and define a procedure which starting from an empty graph selects the smallest component $A$ such that at least one edge in $H$ leaves $A$ and:
 - Tries to grow $A$ by sampling edges with some probability. If the component is not grown:
 - Removes all outgoing from the sampling pool. Since $A$ was the smallest, all outgoing edges will forever be inter-component.

Then it remains to bound the expected number of edges removed from $H$ throughout the entire process.

Authors split components into two types, _high-degree_ and _low-degree_.

Assume $k \ge c\log n$.

#### High-degree : $\exists v\in A: d_H(v) > 2|A|$

__Author's argument:__ 
> Resample edges with probability $1/d$ until an outgoing edge is sampled. This succeeds w.p. $\ge \frac{1}{4}$, so the event that less than $32\log n$ total rounds are participated in by any vertex is extremely likely ($p \ge 1 - n^{-30}$). Denote this event $S$. Thus to prove that it suffices just to prove the bound given $S$. Under $S$, the total probability a vertex is sampled in a high-degree growth step is $O(\log n / d)$ since it participated in $O( \log n)$ of these sampling rounds.


##### Notes:
 - The author's argument does not make it plain why $\mathbb E[X_P \mid S] = O(n/k)$ implies $\mathbb E[X_k] = O(n/k)$. Suppose we made another procedure $\mathcal Q$ in which we instead take all edges from a fixed random expected $k$-out graph. Assuming the right low-degree procedure, it is clear that $X_{\mathcal Q} \succeq X_k$. The probability of failure in a HD growth step in $\mathcal Q$ is then at most $(1 - k/d)^{d / 2} < \exp(-k/2) < n^{-2}$ for $c > 4$, and the expected edge eliminations at most $dn^{-2} < n^{-1}$, totaling to $O(1)$ after summing over all vertices. For $k = o(\log n)$, this cannot work, since the  probability of sampling exactly zero outgoing edges is "too high".
 - There might be $O(\log n)$ high-degree growth steps for a single vertex, but there are at most $2n$ growth steps in total. How can we use this?
    - Perhaps there is a procedure where we can sample edges from at most 2n vertices in total to make progress towards $k = O(1)$.

 - Can we impose a weaker/different definition of high-degree?
    - Technically for $k = \omega(\log n)$, the presence of $O(d \log n / k)$ outgoing edges is sufficient.


#### Low-Degree

 - First, it is easy to prove IC edges are $O(n\log n / k)$ , as follows: Sample each edge with probability $q = C \cdot \frac{k}{|A|}$. Then the cost of growing $A$ is at most $|\partial_H A|(1 - C\cdot \frac{k}{|A|})^{|\partial_H A|} < |\partial_H A|\exp(-Ck|\partial_H A|/|A|) < \frac{|A|}{Ck}$. Thus for all low-deg growth steps the cost is at most $\frac{\sum_i |A_i|}{Ck} = O(n\log n/k)$.

##### Five types (each assuming preceding types are unsatisfied):
 - 0: $\rho(A) < 1$
    - Here we can just remove all edges
    - Then the sum of edges leaving a type-0 component, given all type-0 components are disjoint according to this procedure, is $\sum_{A_0}|\partial_H A_0| < \sum_{A_0}|A|/k < n/k$.
 - 1: $d_H(A) \le 5|\partial_H A|$
    - Set $q = C\cdot \frac{k}{\sqrt{d(A)|A|}}$
    - Then total probability an edge of $v$ sampled is $O(k/d_H(v))$ since $|A|$ at least doubles after every successful growth, and $d \le 2|A|$.
    - Sample edges among all vertices with probability $q$. Then
        $$ \begin{align}
            \text{cost}(A)/|A| \nonumber\\ 
            \le \frac{\rho(A)}{k} \exp(-q \rho(A) |A|/k) \nonumber\\
            = \frac{\rho(A)}{k}\exp(-C(\frac{\rho(A)}{k})k\sqrt{
        |A|/d}) \nonumber
            \\ \le \frac{\rho(A)}{k} \exp(-C(\frac{k}{5})^{1/2} \rho(A)^{1/2}) \tag{a}
            \\ = O(1/k^2) = O(1/(k\log n)) \nonumber,
            \end{align} $$
        where the last inequality follows from that $(1)$, letting $x=\rho(A)$, is maximized at $x_0 = (2/C)^25/k$.
    - Thus each vertex incurs a total cost of at most $n\log n * O(1/(k\log n)) = O(n / k)$.
    - __Notes__:
        - What's the strict condition to get $\text{cost}(A)/|A| \le O(1/(k\log n))$?
          - $\frac{|\partial_H A|}{|A|}\exp(-\sum q_i d^+_i) \le C'/(k\log n)$
          - $\log |A| + \sum q_i d^+_i \ge \log(|\partial_H A|k\log n) - C''$
        - One idea to show that we cannot prove this case to work for $k \le \log n$ is to construct some sequence of graphs of increasing size for which it is clear we cannot get $O(n / k)$ inter-component edges.
        - The intuition I currently have is that if $k$ is "large enough", the exponential factor in the cost is small enough such that we can perform the 'rough' approxmation of summing costs.
        - Using the bound $d \le 2|A|$, we can get the above condition to:
        $$
        k\frac{|\partial_H A|}{2|A|} + C'' \ge \log(k\frac{|\partial_H A|}{|A|}\log n)
        \\\implies \frac 1 2 \rho(A) - \log(\rho(A)) + C'' \ge \log\log n
        \\\implies \rho(A) \ge 4\log\log n
        $$
        - For the right choice of $C''$. So if we assume that the graph is a $4\log\log n$-expander, or more specifically if there is no subset $B \subset V$ with $1 < k\frac{|\partial_H B|}{|B|} < 4\log\log n$, the proof is easy.
 - 2: $A$ is the first low-degree component of level $\le \ell(A)$ containing $v(A)$
    - Then the same is true for at least half the neighbors of $v(A)$.
      - __Pr__: Assume not, then at least $d_H(A)/2$ vertices in $A$ are contained in components $B_1,\ldots, B_s$ of level at most $\ell(A)$. Then the number of neighbors of $v$ is at most $\sum_i|\partial_H B_i| \le \sum_i 2^{\ell(B_i) + 1} |B_i|/k \le 2^{\ell(A) + 1} \sum_i |B_i| / k \le 2^{\ell(A) + 1} |A|/k \le 2|\partial_H A|$. Thus $d_H(v) \le 2(2|\partial_H A|) + |\partial_H A| = 5|\partial_H A|$, a contradiction.
    - So there are, up to a constant factor, $d(A)$ vertices whose level got reduced by $A$. Then can charge each of these (again up to constant factor) 
    $$
    \frac{\text{cost}(A)}{d} \le \frac{2^{\ell(A)}}{k} (|A|/d)\exp(-C 2^{\ell(A)}(|A|/d)^{1/2}) \le \frac{2^{\ell(A)}}{k}(\frac{2}{C2^{\ell(A)}e})^2 \le \frac{2^{-\ell(A)}}{k}
    $$
    And it follows that a single vertex will accumulate cost $O(1/k)$ over all types 2 growth steps.

 - 3: $A$ is not the first low-degree component of level $\le \ell(A)$ of $v(A)$
    - Then $v(A)$ was in non-type-2 component $B$ which had maximum degree $d' \ge d_H(A)$ at the time of growing.
    - Then: <!-- $\rho(B) \le 2\rho(A)$ -->
    $$
    |A|\text{cost}(A) \le 2^{\ell(A)}/k |A|^2 \exp(-C2^{\ell(A)}(|A|/d)^{1/2}) \le 2^{\ell(B)}/k |B|^2 \exp(-C 2^{\ell(B)}(|B|/d)^{1/2})\\
    \le |B|\text{cost}(B)
    $$
    This means that we can offload the cost of $A$ to $B$, so that the cost of type-3 growth steps accrued by $B$ is at most $\sum_{i \ge 1} 2^{-i} \text{cost}(B) < \text{cost}(B)$.

##### Notes:
 - Can the above types for low-degree components can be removed after adding the little extra insight, which is that during a low-d $v$ in low-degree component $A$, if $A$ grows, the ratio $d/|A|$ is halved? Might require some complicated bounding.
    - We can almost get there: 
    $$ \begin{align*}
    \frac{\text{cost}(A)}{|A|} &\le \rho'(A)\exp(-Ck \rho'(A)\sqrt{\frac{|A|}{d_H(A)}})\\
    &\le \frac{\sqrt{d_H(A)}}{k\sqrt{|A|}}\\
    \end{align*} $$
    - But this does not work since $d_H(A)$ is the *maximum* degree of $v\in A$, which might be very close to $2|A|$ at every step.
    - I don't think the $d_H(A)$ can be replaced with $d_H(v)$, since the  above bound is existentially "tight" for the case that all boundary edges are adjacent to the same vertex.
        <!-- - Is it? Say $d_H(A) \ge \frac{1}{2}|\partial_H A|$. -->
 - More generally, since there are $O(2n)$ total growth steps, it suffices that the cost of each step is $O(1/k)$. Given a probability $q_i$ and outgoing edge count $d^+_i$ for each vertex, it thus suffices that:
 $$
 |\partial_H A| \exp(-\sum_i q_i d^+_i) \le C/k\\
 \iff \log C + \sum_i q_i d^+_i \ge \log(k|\partial_H A|) $$
 - I.e., we can expect to sample at least $\log (k|\partial_H A|)$ edges from the boundary.
 - Alternatively, we can get each step to $O(|A|/(k\log n))$
   - Results in $\log(C|A|) + \sum_i q_i d^+_i \ge \log(k|\partial_H A|\log n)$
        - So if $k$ is a constant, this seems like quite a difficult condition to enforce.

<!-- - By the density condition we have $|A| > k|\partial_H A|$, so then we 'need' $\sum_i q_i d^+_i \ge \log n$
            - So, this direction seems difficult for 
-->

 - The Type 0 condition, however, doesn't guarantee either of these. An intuition that the analysis applied to types 1+ cannot be applied here is that for a type 0 component, with $q = \frac{Ck}{|A|}$, the probability of failure is lower bounded by a constant, so the upper bound used implicitly when simply summing costs for all steps is too rough.
    - Similarly, if enough vertices have degrees higher than the boundary (similar to type 2/3), then we also cannot use this analysis.

    - Suggestion: Find a nice condition for which the proof becomes easier in some way. Make a log of assumptions that make the proof easier. Is there a simple natural condition to simplify the proof?
        - Expander decomposition


<!-- For the low-degree case, if we sample with probabilities $q_i = \frac{Ck}{|A|}$, then it suffices to get each step's cost to $O(1/k)$, or alternatively $O(|A|/(k\log n))$. -->


<!--
- But the above is not sufficient, and it is instead sufficient to get
$$ \begin{align*}
    \text{cost}(A) &< O(\sum_{v\in A} \frac{k \sqrt{d_H(v)}}{\sqrt{|A|}})\\
    \iff |\partial_HA|\exp(-C (\sum_{v\in A} q_v d^+_v)) &< \cdots\\
    \leftarrow &< 5
\end{align*}$$
-->


### Notes:
---

- Can we use the fact that when $N$ components of similar density combine, the sum of densities of the containing component across vertices decreases by a factor of at least $N$?
    - It seems the type 2 proof is a sort of way to make vertices "pay" for components according to the history of the densities of components containing them, such that due to the above property, each vertex ends up paying $O(1/k)$ in total.
    - The author's proof uses the $d > 5|\partial_H A|$ condition to enforce that there are enough vertices to cover the cost


### Questions:
---

- Does one really need to "trim" edges? What if we make the standard conversion of $G$ to a directed graph, and use their technique to bound the number of directed edges removed to $O(n / k)$? Then an edge is included in the result iff at least one direction remains. The eliminated inter-component edges are at most one-half the directed edges so that it suffices to bound the number of elimated directed edges as $O(n / k)$. This seems like a clearer argument to make.
O
- ...

Definitions:
- $v(A) = \text{argmax}_{v\in A} d_H(v)$
- $d_H(A) = d_H(v(A))$

<!--
Bulleted list:
- Here is one item
- Here is another item
- I don't think that control should be the  keymap for autocompletion...
  - Sub-item
    - Sub-sub item
        - sub-sub-sub item
            - sub-sub-sub-sub item
- Ideally, we'll show $O(n / k)$ inter-component edges.
-->
