Paper [Here](https://www.google.com)

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
    - Perhaps during the low-degree growth step...

 - Can we impose a weaker/different definition of high-degree?
    - Technically for $k = \omega(\log n)$, the presence of $O(d \log n / k)$ outgoing edges is sufficient.


#### Low-Degree

##### Five types (each assuming preceding types are unsatisfied):
 - 0: $\rho(A) < 1$
    - Here we can just remove all edges
    - Then the sum of edges leaving a type-0 component, given all type-0 components are disjoint according to this procedure, is $\sum_{A_0}|\partial_H A_0| < \sum_{A_0}|A|/k < n/k$.
 - 1: $d_H(A) \le 5|\partial_H A|$
    - Set $q = C\cdot \frac{k}{\sqrt{d(A)|A|}}$
    - Then total probability an edge of $v$ sampled is $O(k/\sqrt{d_H(v)})$ since $|A|$ at least doubles after every successful growth.
    - Sample edges among all vertices with probability $q$. then
        $$
            \text{cost}(A)/|A| \\ 
            \le \frac{\rho(A)}{k} \exp(-q \rho(A) |A|/k) \\
            = \frac{\rho(A)}{k}\exp(-C(\frac{\rho(A)}{k})k\sqrt{
        |A|/d})
            \\ \le \frac{\rho(A)}{k} \exp(-C(\frac{k}{5})^{1/2} \rho(A)^{1/2})
            \\ = O(1/k^2) = O(1/(k\log n)),
        $$
        where the last inequality follows from that the expression on the penultimate line, letting $x=\rho(A)$, is maximized at $x_0 = (2/C)^25/k$.
    - Thus each vertex incurs a total cost of at most $n\log n * O(1/(k\log n)) = O(n / k)$
 - 2: $A$ is the first low-degree component of level $\le \ell(A)$ containing $v(A)$
    - ...
 - 3: $A$ is not the first low-degree component of level $\le \ell(A)$ of $v(A)$
    - ...

##### Notes:
 - Can the above types for low-degree components can be removed after adding the little extra insight, which is that during a low-d $v$ in low-degree component $A$, if $A$ grows, the ratio $d/|A|$ is halved? Might require some complicated bounding.



### Questions:
---

- Does one really need to "trim" edges? What if we  make the standard conversion of $G$ to a directed graph, and use their technique to bound the number of directed edges removed to $O(n / k)$? Then an edge is included in the result iff at least one direction remains. The eliminated inter-component edges are at most one-half the directed edges so that it suffices to bound the number of elimated directed edges as $O(n / k)$. This seems like a clearer argument to make.
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
