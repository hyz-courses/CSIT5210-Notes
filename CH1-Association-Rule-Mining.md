
# Chapter 1. Association Rule Mining

**List of Notations**

| Notations | Description | Meaning |
| :--- | :--- |:---|
| $A$ | Captial Letter | A single item |
| $r$ | Lowercase Letter | An association rule |
| $\mathbf{X}$ | Bold-face capital letter | An item set |
| $\mathbb{S}$ | Blackboard-bold captital letter | A rule set |

# 1. Basic Concepts

| TID | A | B | C | D | E |
|----|---|---|---|---|---|
| t1 | 1 | 0 | 0 | 1 | 0 |
| t2 | 1 | 1 | 0 | 1 | 1 |
| t3 | 0 | 1 | 1 | 0 | 0 |
| t4 | 1 | 1 | 1 | 1 | 1 |
| t5 | 0 | 1 | 1 | 0 | 1 |

## 1.1 Items and Item Set

**`def` Items and Item Set**

- Single Items: $A, B, C, D, E$
- Item Sets: $\{B, C\}$, $\{A, B, C\}$, $\{A\}$

**`def` Support of an item set** 

The support of an item set refers to the number of records (i.e., frequency) where all the item in the the item set are contained.

$$\text{supp}(\{A, C\}) = \text{Number of records contianing both} \ A \ \text{and} \ C$$

> *e.g.* 
>
> Since there are three records that contain both $B$ and $C$, the item set $\{B, C\}$ is called a 3-frequent item set.

## 1.2 Association Rules

**`def` Association Rule**

An implication relation mapping from a set of items to another set of items. An example is as follows:

$$\{B, C\} \rightarrow \{ E \}$$

It is a claim that, a record that contains $B$ and $C$ would also contains $E$.

> Note that the association rule is a claim, not a fact. It may be true for certain records, while it's possible that it's not true for some other records.

**`def` Support of an Association Rule**

The support of an association rule refers to the support of the item set that contains all the referred items in the rule.

$$\text{supp}(B \rightarrow C) = \text{supp}(\{B, C\})$$

**`def` Confidence**

The confidence of an association rule refers to how likely the rule holds. It is measured by the portion of records that fulfilled the rule over the records that fulfilled the antecedent.

$$\text{conf}(B \rightarrow C) = \frac{\text{supp}(\{B, C\})}{\text{supp}(\{B\})}$$

# 2. Problem Definition

## 2.0 Special Definitions

We temporarily define some terms to illustrate the problem.

**`def` Large Itemset**

Item sets with a support greater than a threshold.

**`def` Large Association Rules**

Association rules with a support greater than a threshold.

**`def` Interesting Association Rules**

Association rules with a confidence greater than a threshold.

## 2.1 Problem Description

**Given:** A matrix of transaction info.

| A | B | C |
|---|---|---|
| 1 | 1 | 1 |
| 0 | 1 | 0 |
| 1 | 1 | 1 |
| 0 | 1 | 1 |

**Goal:** Find all association rules that are large and interesting.

- i.e., find all association rules with $\text{supp} \geq 3$ and $\text{conf} \geq 50\%$

## 2.2 Problem Solutions

### 2.2.1 Method 1: Naive / Straightforward Method:

**Step 1.** Enumerate all possible rules (find all concepts).

| Rules |
|-------|
| $\{A\} \rightarrow \{B\}$ |
| $\{A\} \rightarrow \{C\}$ |
| $\{B\} \rightarrow \{A\}$ |
| $\{B\} \rightarrow \{C\}$ |
| $\{C\} \rightarrow \{A\}$ |
| $\{C\} \rightarrow \{B\}$ |
| $\{A, B\} \rightarrow \{C\}$ |
| ... |

**Step 2.** Compute support and confidence of each rule.

| Rules | Supp | Conf |
|-------|------|------|
| $\{A\} \rightarrow \{B\}$ | 2 | $\frac{2}{2}=1.0$ |
| $\{A\} \rightarrow \{C\}$ | 2 | $\frac{2}{2}=1.0$ |
| $\{B\} \rightarrow \{A\}$ | 2 | $\frac{2}{4}=0.5$ |
| $\{B\} \rightarrow \{C\}$ | 3 | $\frac{3}{4}=0.75$ |
| $\{C\} \rightarrow \{A\}$ | 2 | $\frac{2}{2}=1.0$ |
| $\{C\} \rightarrow \{B\}$ | 3 | $\frac{2}{2}=1.0$ |
| $\{A, B\} \rightarrow \{C\}$ | 2 | $\frac{2}{2}=1.0$ |
| ... |


**Step 3.** Select a set of rules $R$ with $\text{supp}(r) \geq 3$ and $\text{conf}(r) \geq 50\%$, for all $r\in R$.

**Output:** $\mathbb{S}_{0}$, the required set of association rules.

### 2.2.2 Method 2: Two-step method

**Step 1.** Generate all large item sets (supp >= 3).

(Find all concepts, certifying one condition)

*Step 1.1* Find all item sets.

*Step 1.2* Compute all the supports.

| Item Sets | Supp | Large? |
|-----------|------|--------|
| $\{A\}$ | 2 | NO |
| $\{B\}$ | 4 | YES |
| $\{C\}$ | 3 | YES |
| $\{A, B\}$ | 2 | NO |
| $\{A, C\}$ | 2 | NO |
| $\{B, C\}$ | 3 | YES |
| $\{A, B, C\}$ | 2 | NO |

*Step 1.3* Filter out the non-"large" ones (< 3).

| Item Sets | Supp | Large? |
|-----------|------|--------|
| $\{B\}$ | 4 | YES |
| $\{C\}$ | 3 | YES |
| $\{B, C\}$ | 3 | YES |

**Output:** $\mathbb{S}_{1}$

**Step 2.** Generate a set of rules with conf >= 50%.

*Step 2.1* For any two item sets in $\mathbb{S}_{1}$, being $\mathbf{X}$ and $\mathbf{Y}$, where $\mathbf{X} \subseteq \mathbf{Y} $:

If $\frac{\text{supp}(\mathbf{Y})}{\text{supp}(\mathbf{X})} \geq 50\%$ then:

Generate $\mathbf{X} \rightarrow \mathbf{Y} - \mathbf{X}$

> Why subset? 
>
> Remark: $\text{conf}(\{B\} \rightarrow \{C\}) = \frac{\text{supp}(\{B, C\})}{\text{supp}(\{B\})}$. Here, $\mathbf{X}$ is $\{C\}$ while $\mathbf{Y}$ is $\{B, C\}$.
> 
> Larger set, more specifications, less occurances.

**Output:** $\mathbb{S}_{2}$.

## 2.3 Equivalence of the two methods

**`claim`** The two methods generates the same set of outputs:

$$\mathbb{S}_{2} = \mathbb{S}_{0}$$

---

**`proof`** $\mathbb{S}_{2}=\mathbb{S}_{0}$

Proving $\mathbb{S}_{2}=\mathbb{S}_{0}$ is equivalent to proving:

$$\mathbb{S}_{2} \subset \mathbb{S}_{0} \land \mathbb{S}_{0} \subset \mathbb{S}_{2}$$

Which is further equivalent to proving:

$$(r \in \mathbb{S}_{2} \implies r \in \mathbb{S}_{0}) \land (r \in \mathbb{S}_{0} \implies r \in \mathbb{S}_{2})$$

Note that two sets are rule sets.

---

**Sub-proof 1:** $(r \in \mathbb{S}_{2} \implies r \in \mathbb{S}_{0})$

Suppose that $r = (\mathbf{I}_{1} \rightarrow \mathbf{I}_{2})$ is a rule in $\mathbb{S}_{2}$, where $\mathbf{I}_{1}, \mathbf{I}_{2}$ are item sets.

$r \in \mathbb{S}_{2}$

$\implies (\text{conf}(r) \geq 50\%) \land (\mathbf{I}_{1} \cup \mathbf{I}_{2}, \mathbf{I}_{1} \in \mathbb{S}_{1})$

---

*Sub-proof 1 - Branch 1:*

$\mathbf{I}_{1} \cup \mathbf{I}_{2}, \mathbf{I}_{1} \in \mathbb{S}_{1} \implies \text{supp}(\mathbf{I}_{1} \cup \mathbf{I}_{2}) \geq 3 \land \text{supp}(\mathbf{I}_{2}) \geq 3$

Since $\text{supp}(\mathbf{I}_{1} \cup \mathbf{I}_{2}) \geq 3$ and $\text{supp}(\mathbf{I}_{1} \rightarrow \mathbf{I}_{2}) = \text{supp}(\mathbf{I}_{1} \cup \mathbf{I}_{2})$, we have:

$$ \text{supp}(\mathbf{I}_{1} \rightarrow \mathbf{I}_{2}) \geq 3$$

namely,

$$\text{supp}(r) \geq 3$$

The rule is large.

---

*Sub-proof 1 - Branch 2:*

For $\text{conf}(r) \geq 50\%$ and $\text{supp}(r) \geq 3$, the rule $r$ is both large and interesting. Therefore, $r \in \mathbb{S}_{0}$.

---

**Sub-proof 2:** $(r \in \mathbb{S}_{0} \implies r \in \mathbb{S}_{2})$

Suppose that $r = (\mathbf{I}_{1} \rightarrow \mathbf{I}_{2})$ is a rule in $\mathbb{S}_{2}$, where $\mathbf{I}_{1}, \mathbf{I}_{2}$ are item sets.

$r \in \mathbb{S}_{0}$

$\implies (\text{supp}(r) \geq 3) \land (\text{conf}(r) \geq 50\%)$

$\implies (\text{supp}(\mathbf{I}_{1} \rightarrow \mathbf{I}_{2}) \geq 3) \land (\text{conf}(\mathbf{I}_{1} \rightarrow \mathbf{I}_{2}) \geq 50\%)$

$\implies (\text{supp}(\mathbf{I}_{1} \cup \mathbf{I}_{2}) \geq 3) \land (\frac{\text{supp}(\mathbf{I}_{1} \cup \mathbf{I}_{2})}{\text{supp}(\mathbf{I}_{1})} \geq 50\%)$

---

*Sub-proof 2 - Branch 1:*

There's a trivial property:

$$\text{supp}(\mathbf{I}_{1}) \geq \text{supp}(\mathbf{I}_{1} \cup \mathbf{I}_{2})$$

Having $\text{supp}(\mathbf{I}_{1} \cup \mathbf{I}_{2}) \geq 3$, we can conclude that:

$$\text{supp}(\mathbf{I}_{1}) \geq \text{supp}(\mathbf{I}_{1} \cup \mathbf{I}_{2}) \geq 3$$

Generally, both $\mathbf{I}_{1}$ and $\mathbf{I}_{1} \cup \mathbf{I}_{2}$ are large. Therefore:

$$\mathbf{I}_{1}, \mathbf{I}_{1} \cup \mathbf{I}_{2} \in \mathbb{S}_{1}$$

---

*Sub-proof 2 - Branch 2:*

With $\mathbf{I}_{1}, \mathbf{I}_{1} \cup \mathbf{I}_{2} \in \mathbb{S}_{1}$, **Step 2** must consider $\mathbf{I}_{1}$ and $\mathbf{I}_{1} \cup \mathbf{I}_{2}$.

Since $\text{conf}(\mathbf{I}_{1} \rightarrow \mathbf{I}_{2}) \geq 50\%$, **Step 2** must generate $\mathbf{I}_{1} \rightarrow \mathbf{I}_{2}$. Namely, **Step 2** must generate $r$.

Therefore, $r \in \mathbb{S}_{2}$.

---

**Proof Summary**

By **Sub-proof 1**, we have $r \in \mathbb{S}_{2} \implies r \in \mathbb{S}_{0}$, namely, $\mathbb{S}_{2} \subset \mathbb{S}_{0}$.

By **Sub-proof 2**, we have $r \in \mathbb{S}_{0} \implies r \in \mathbb{S}_{2}$, namely, $\mathbb{S}_{0} \subset \mathbb{S}_{2}$.

To sum up, $\mathbb{S}_{2} \subset \mathbb{S}_{0} \land \mathbb{S}_{0} \subset \mathbb{S}_{2}$. By definition, $\mathbb{S}_{2}=\mathbb{S}_{0}$.

**Qed.**

---







<!-- Known: $\text{conf}(B \rightarrow C) \geq 50\%$

Show: $\text{supp}(B \rightarrow C) \geq 3\%$

i.e., show $\text{supp}(\{B, C\}) \geq 3\%$

$B \rightarrow C \in \mathbb{S}_{2} \implies \frac{\text{supp}(\{B, C\})}{\text{supp}(B)} \geq 50\%$

Another line:

$B \rightarrow C \in \mathbb{S}_{2} \implies B, C \in \mathbb{S}_{1}$

$\implies \text{supp}(B) \geq 3$

Claim 2: $\mathbb{S}_{0} \subset \mathbb{S}_{2}$

F
$B \rightarrow C \in \mathbb{S}_{0}$

$\implies \text{conf}(B\rightarrow C) \geq 50\% \land \text{supp}(B\rightarrow C) \geq 3$

$\Leftrightarrow \frac{\text{supp}(\{B, C\})}{\text{supp}(B)} \geq 50\% \land \text{supp}(\{B, C\}) \geq 3$

$\implies \frac{\text{supp}(\{B, C\})}{\text{supp}(B)} \geq 50\% \land \text{supp}(B) \geq 3$

$\implies \frac{\text{supp}(\{B, C\})}{\text{supp}(B)} \geq 50\% \land B \in \mathbb{S}_{1}$

Step 2 must consider $B$, and $C$ together and must generate $B \rightarrow C$.

Therefore, $\mathbb{S}_{0} \in \mathbb{S}_{2}$. -->