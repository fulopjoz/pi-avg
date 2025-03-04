Sequence Alignment and Related Concepts

---

## Table of Contents
1. [Overview and Motivation](#overview-and-motivation)
2. [Basics of Sequence Alignment](#basics-of-sequence-alignment)
   - [Definition of Alignment](#definition-of-alignment)
   - [Alignment Example](#alignment-example)
   - [Cost of Alignment](#cost-of-alignment)
3. [Edit Distance and Dynamic Programming](#edit-distance-and-dynamic-programming)
   - [Edit Operations](#edit-operations)
   - [Edit Distance Algorithms](#edit-distance-algorithms)
   - [Complexity Considerations](#complexity-considerations)
4. [Longest Common Subsequence (LCS)](#longest-common-subsequence-lcs)
5. [Approximate String Matching](#approximate-string-matching)
6. [Similarity Scoring in Biological Alignment](#similarity-scoring-in-biological-alignment)
7. [Global, Local, and Overlap Alignment](#global-local-and-overlap-alignment)
   - [Global Alignment (Needleman-Wunsch)](#global-alignment-needleman-wunsch)
   - [Local Alignment (Smith-Waterman)](#local-alignment-smith-waterman)
   - [Overlap Alignment](#overlap-alignment)
8. [Affine Gap Penalties](#affine-gap-penalties)
9. [Key Takeaways and Biological Relevance](#key-takeaways-and-biological-relevance)
10. [References and Further Reading](#references-and-further-reading)

---

## 1. Overview and Motivation

In bioinformatics and computational genomics, **sequence alignment** is fundamental to analyzing DNA, RNA, and protein sequences. By measuring how well two (or more) sequences can be lined up to reveal matches, mismatches, insertions, or deletions, we gain insight into:

- **Evolutionary relationships** among organisms or genes.
- **Functional similarities** between sequences (e.g., similar motifs indicate similar biological roles).
- **Practical applications** such as error correction in sequencing data, variant detection, read mapping, genome assembly, and more.

This lecture series addresses:
- **Basic alignment definitions** and cost models,
- **Edit distance**, dynamic programming, and approximate matching,
- **Global vs. local alignment** algorithms,
- **Affine gap penalties**,
- **Implications in broader contexts** like multiple alignment, pattern matching, and computational genomics.

---

## 2. Basics of Sequence Alignment

### Definition of Alignment
Given two sequences \(A\) and \(B\) of lengths \(n\) and \(m\) respectively, an **alignment** is a way to place these sequences one above the other, inserting gaps (`-`) where necessary, so that each column represents a pairing:

- If a column has (base from \(A\), base from \(B\)) = (C, C), that is a **match**.
- If they are different, that is a **mismatch** (or **replacement**).
- If one sequence has a gap (`-`) aligned to a character in the other, that indicates a **deletion** (from the perspective of one sequence) or an **insertion** (from the other perspective).

Formally:
- We transform \(A\) and \(B\) into new sequences \(U\) and \(V\) of the **same length** \(h\), by inserting the gap symbol (`-`). 
- \(A\) must be a subsequence of \(U\), and \(B\) must be a subsequence of \(V\).
- The length \(h\) ranges between \(\max(n,m)\) and \(n + m\).

### Alignment Example
Consider:
- Sequence \(A = \text{CCGA TG}\) (length 7)
- Sequence \(B = \text{ACGG CTA}\) (length 7)

An alignment might look like:

C C G A - T G A C G G C T A

- The `-` indicates a gap inserted in sequence \(A\) to match up a symbol in \(B\).
- Each column shows either a match/mismatch (substitution), an insertion, or a deletion.

### Cost of Alignment
We typically define a **cost function** \( c(x,y) \) (or a **scoring function** \( s(x,y) \) in a similarity context) for aligning any two “characters” \( x \) and \( y \). Commonly:
- **Match**: cost = 0 (or positive score)
- **Mismatch** (replacement): cost = 1 (or negative score)
- **Insertion/Deletion** (gap penalty): cost = 1 (or some negative score)

The **total alignment cost** is the sum of these individual pairwise costs across all columns. Minimizing total cost is equivalent to maximizing an alignment **score** if you flip the sign of the cost function.

---

## 3. Edit Distance and Dynamic Programming

### Edit Operations
When discussing **edit distance**, we usually allow:
1. **Substitution (Replacement)**: Change one character into another.
2. **Insertion**: Insert a new character into a sequence.
3. **Deletion**: Delete a character from a sequence.
4. (Sometimes) **Match** is treated as a “zero-cost” or “free” operation.

**Edit distance** is the minimum number of edit operations needed to transform one sequence into another.

### Edit Distance Algorithms
We compute edit distance using **dynamic programming**. Define a matrix \( D \) of size \((n+1)\times(m+1)\) where:
- Rows correspond to prefixes of \(A\),
- Columns correspond to prefixes of \(B\),
- \(D(i,j)\) is the minimal edit distance between \(A[1..i]\) and \(B[1..j]\).

The recurrence is:
\[
D(i,j) \;=\; \min \Bigl\{
\;D(i-1,j-1) + \text{cost(substitution)},\;
D(i-1,j) + \text{cost(deletion)},\;
D(i,j-1) + \text{cost(insertion)}
\Bigr\}.
\]

Boundary conditions:
- \(D(0,0)=0\),
- \(D(i,0) = i \times \text{cost(deletion)}\),
- \(D(0,j) = j \times \text{cost(insertion)}\).

In the **unit cost** model:
- Substitution cost = 1 (if characters differ), 0 if they match,
- Insertion cost = 1,
- Deletion cost = 1.

Hence, if \(\text{cost(substitution)} = 1\) or \(0\), the recurrence becomes:
\[
D(i,j) = \min \bigl\{
D(i-1,j-1) + [A_i \neq B_j],\;
D(i-1,j) + 1,\;
D(i,j-1) + 1
\bigr\}.
\]
Here, \([A_i \neq B_j]\) is the Iverson bracket that evaluates to 1 if \(A_i\neq B_j\), and 0 otherwise (i.e., if they match).

### Complexity Considerations
- **Time complexity**: \(O(n \times m)\), since we fill an \(n \times m\) matrix with constant-time updates.
- **Space complexity**: \(O(n \times m)\) if we store the entire matrix, but can be **reduced to** \(O(\min(n,m))\) if we only need the final distance or a partial traceback (storing only one column or row at a time).

---

## 4. Longest Common Subsequence (LCS)

The **Longest Common Subsequence (LCS)** problem measures a different notion of similarity: the longest sequence \(C\) that can be obtained by deleting (but not reordering) characters from both \(A\) and \(B\). For example, if
- \( A = \text{tervetuloa} \),
- \( B = \text{teretulemast} \),

the LCS could be `teretula`. We can relate LCS length to **edit distance** in the **Indel distance** model (insertions and deletions but no substitutions). In particular:
\[
\lvert \text{LCS}(A,B) \rvert
\;=\;\frac{\lvert A\rvert + \lvert B\rvert - \text{D}_{\mathrm{InDel}}(A,B)}{2}.
\]

A standard dynamic programming solution for LCS also runs in \(O(n \times m)\), though with different recurrences. LCS is crucial for partial similarity detection, especially in contexts where only inserts/deletions matter.

---

## 5. Approximate String Matching

**Approximate string matching** generalizes the idea of edit distance to searching a pattern \(P\) of length \(m\) inside a text \(T\) of length \(n\), allowing up to \(k\) mismatches (substitutions) or up to \(k\) “errors” in the sense of Levenshtein distance. We then look for **substrings** \(T[i..(i+m-1)]\) such that \(\text{edit\_distance}(P,\, T[i..i+m-1]) \le k\).

A well-known approach:
- Construct a dynamic programming matrix allowing partial matches,
- Initialize the first row to zeros (to allow matches to start at any position),
- Fill in row by row or column by column,
- Check at each possible alignment if \(\le k\) edits are required.

Time complexity is still \(O(n \times m)\) with straightforward DP, and can be optimized further with advanced bit-parallel or other specialized methods.

---

## 6. Similarity Scoring in Biological Alignment

In biological contexts, we typically **score alignments** rather than just count costs. A scoring scheme might include:
- **Substitution matrix** for matching and mismatching letters: e.g., match = +1, mismatch = -1, or more sophisticated matrices (like PAM or BLOSUM in protein alignment).
- **Gap penalty**: negative score for inserting a gap. Sometimes a **linear** penalty (e.g., -\(\delta\) per gap symbol), sometimes **affine** (large penalty for opening a gap, smaller penalty for extending it).
- **Match** may have a positive score (e.g., +1), or in protein alignments, +5 or +10 depending on the amino acids that match.

### Global Alignment as Maximum Similarity
Minimizing cost aligns well with maximizing negative cost. Alternatively, we define a similarity function \( s(x, y) \), such that the alignment’s **total similarity** is
\[
\sum_{i=1}^{h} s(U[i],\, V[i]).
\]
We want to find the alignment that **maximizes** the total similarity. Techniques are essentially symmetrical to edit distance, only flipping the min to a max and substituting the cost function with a scoring function.

---

## 7. Global, Local, and Overlap Alignment

### Global Alignment (Needleman-Wunsch)
**Global alignment** aligns two sequences from start to end, used especially when the two sequences are of comparable length and are assumed to be similar overall. 

The **Needleman-Wunsch algorithm** uses dynamic programming identical in spirit to edit distance, but with a scoring scheme:
\[
S(i,j) \;=\; \max\{
\;S(i-1, j-1) + s(a_i,b_j),\;
S(i-1, j) - \delta,\;
S(i, j-1) - \delta
\}.
\]
We initialize:
- \(S(0,0) = 0\),
- \(S(i,0) = -i\,\delta\),
- \(S(0,j) = -j\,\delta\).

### Local Alignment (Smith-Waterman)
**Local alignment** aims to find the most similar **substrings** of two sequences. This is used when two long sequences share only a short region of high similarity, often crucial in identifying shared motifs or functional domains.

The **Smith-Waterman algorithm** modifies the DP by allowing alignment scores to drop to zero:
\[
L(i,j) \;=\; \max\{
0,\;
L(i-1, j-1) + s(a_i,b_j),\;
L(i-1, j) - \delta,\;
L(i, j-1) - \delta
\}.
\]
The optimal local alignment is found by tracing back from the cell with the maximum value in the matrix.

### Overlap Alignment
**Overlap alignment** is used when we only want to align a **suffix** of one sequence with a **prefix** of another, which often arises in sequence assembly. It ensures we focus on how the end of one sequence may match the beginning of another.

---

## 8. Affine Gap Penalties

In many biological scenarios, opening a gap (starting an insertion/deletion run) has a higher penalty than **extending** that gap. The **affine gap penalty** model sets:
- Gap opening penalty = \(\alpha\),
- Gap extension penalty = \(\beta\) per additional gap character.

Hence a gap of length \(k\) might incur cost:
\[
\alpha + (k-1)\,\beta,
\]
with \(\alpha > \beta\). This more closely models real biological insertions/deletions. The dynamic programming approach is more elaborate, typically maintaining separate DP arrays for “match/mismatch” continuation vs. gap extension.

---

## 9. Key Takeaways and Biological Relevance

- **Edit distance** and **alignment** are two sides of the same coin, differing by sign (cost vs. score).
- **Dynamic programming** is the foundation for computing global, local, and overlap alignments.
- **Approximate string matching** extends alignment ideas to large-scale text searches.
- **Affine gap penalties** reflect the reality of biological insertions/deletions.
- **Longest Common Subsequence** is a special but relevant case (no replacements).
- These methods are at the heart of **read alignment**, **variant calling**, **homology searches**, and more in modern genomics pipelines.

---

