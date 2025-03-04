# Global, Local, and Overlap Alignment

While edit distance focuses on the minimum number of operations to transform one sequence into another, sequence alignment algorithms typically use scoring schemes to find the optimal alignment between sequences. There are three main types of alignment approaches: global, local, and overlap alignment.

## Global Alignment (Needleman-Wunsch)

**Global alignment** attempts to align two sequences from start to end. This approach is most useful when:
- The sequences are of similar length
- The sequences are expected to be similar throughout their entire length
- You want to find the best overall alignment

### Needleman-Wunsch Algorithm

The **Needleman-Wunsch algorithm** is the standard dynamic programming approach for global alignment. It uses a scoring scheme rather than a cost function:

- Match: positive score (e.g., +1)
- Mismatch: negative score (e.g., -1)
- Gap: negative score (e.g., -2)

#### Algorithm Steps

1. **Initialization**: Create a scoring matrix \(S\) of size \((n+1) \times (m+1)\)
   - \(S(0,0) = 0\)
   - \(S(i,0) = -i \times \text{gap\_penalty}\) for all \(i > 0\)
   - \(S(0,j) = -j \times \text{gap\_penalty}\) for all \(j > 0\)

2. **Fill the matrix**: For each cell \(S(i,j)\), compute:
   $$
   S(i,j) = \max \begin{cases}
   S(i-1,j-1) + s(A[i], B[j]) & \text{(match/mismatch)} \\
   S(i-1,j) - \text{gap\_penalty} & \text{(deletion)} \\
   S(i,j-1) - \text{gap\_penalty} & \text{(insertion)}
   \end{cases}
   $$
   Where \(s(A[i], B[j])\) is the score for aligning characters \(A[i]\) and \(B[j]\).

3. **Traceback**: Start from \(S(n,m)\) and follow the path that led to each cell's score until reaching \(S(0,0)\).

#### Example

Consider aligning sequences \(A = \text{"ACGT"}\) and \(B = \text{"ATGC"}\) with:
- Match score: +1
- Mismatch score: -1
- Gap penalty: -2

The scoring matrix would be:

```
    |    | A  | C  | G  | T
----|----|----|----|----|----
    |  0 | -2 | -4 | -6 | -8
----|----|----|----|----|----
  A | -2 |  1 | -1 | -3 | -5
----|----|----|----|----|----
  T | -4 | -1 |  0 | -2 | -2
----|----|----|----|----|----
  G | -6 | -3 | -2 |  1 | -1
----|----|----|----|----|----
  C | -8 | -5 | -1 | -1 |  0
```

The optimal global alignment has a score of 0 and might be:

```
A C G T
A T G C
```

## Local Alignment (Smith-Waterman)

**Local alignment** aims to find the most similar **substrings** within two sequences. This approach is most useful when:
- The sequences may only share regions of similarity
- You want to identify conserved domains or motifs
- The sequences differ significantly in length or content

### Smith-Waterman Algorithm

The **Smith-Waterman algorithm** modifies the Needleman-Wunsch approach to focus on local regions of similarity:

#### Algorithm Steps

1. **Initialization**: Create a scoring matrix \(L\) of size \((n+1) \times (m+1)\)
   - \(L(i,0) = 0\) for all \(i\)
   - \(L(0,j) = 0\) for all \(j\)

2. **Fill the matrix**: For each cell \(L(i,j)\), compute:
   $$
   L(i,j) = \max \begin{cases}
   0 & \text{(start new alignment)} \\
   L(i-1,j-1) + s(A[i], B[j]) & \text{(match/mismatch)} \\
   L(i-1,j) - \text{gap\_penalty} & \text{(deletion)} \\
   L(i,j-1) - \text{gap\_penalty} & \text{(insertion)}
   \end{cases}
   $$

3. **Traceback**: Start from the cell with the **highest score** in the entire matrix and follow the path until reaching a cell with score 0.

The key difference from global alignment is that:
- Scores are never allowed to go below zero (we reset to zero instead)
- We start the traceback from the highest-scoring cell (not necessarily the bottom-right)
- The traceback ends when we reach a zero (not necessarily at the top-left)

#### Example

Using the same sequences and scoring scheme as before, the local alignment matrix might look like:

```
    |    | A  | C  | G  | T
----|----|----|----|----|----
    |  0 |  0 |  0 |  0 |  0
----|----|----|----|----|----
  A |  0 |  1 |  0 |  0 |  0
----|----|----|----|----|----
  T |  0 |  0 |  0 |  0 |  1
----|----|----|----|----|----
  G |  0 |  0 |  0 |  1 |  0
----|----|----|----|----|----
  C |  0 |  0 |  1 |  0 |  0
```

The optimal local alignment might be just a single matching character or a longer substring, depending on the sequences and scoring parameters.

## Overlap Alignment

**Overlap alignment** (or semi-global alignment) is used when we want to find the best overlap between the end of one sequence and the beginning of another. This is particularly useful in:
- Genome assembly, where we need to merge overlapping sequence fragments
- Identifying potential fusion points between sequences

### Algorithm Approach

Overlap alignment modifies the global alignment algorithm by:
- Not penalizing gaps at the beginning of one sequence and/or the end of the other
- This is implemented by modifying the initialization and/or the final score selection

For example, to find the best suffix of \(A\) that matches a prefix of \(B\):
1. Initialize the first row normally: \(S(0,j) = -j \times \text{gap\_penalty}\)
2. Initialize the first column with zeros: \(S(i,0) = 0\) (no penalty for gaps at the start of \(B\))
3. Fill the matrix as in global alignment
4. For the final score, find the maximum value in the last row (not necessarily \(S(n,m)\))

## Choosing the Right Alignment Approach

The choice between global, local, and overlap alignment depends on the biological context:

| Alignment Type | When to Use |
|----------------|-------------|
| Global | When sequences are similar throughout (e.g., homologous genes) |
| Local | When looking for conserved domains or motifs in otherwise dissimilar sequences |
| Overlap | When assembling fragments or finding potential fusion points |

## Similarity Scoring in Biological Sequences

In biological sequence alignment, the scoring scheme is crucial and often reflects evolutionary or biochemical properties:

### DNA/RNA Scoring

- **Match**: Typically +1 or +5
- **Mismatch**: Often -1 to -4, but may vary based on transition vs. transversion
- **Gap**: Usually a larger penalty, e.g., -2 to -10

### Protein Scoring

For proteins, substitution matrices like PAM (Point Accepted Mutation) or BLOSUM (BLOcks SUbstitution Matrix) are used:
- These matrices assign different scores to different amino acid substitutions based on observed frequencies in related proteins
- For example, substituting similar amino acids (e.g., leucine for isoleucine) gets a higher score than dissimilar ones

In the next section, we'll explore affine gap penalties, which provide a more realistic model for biological insertions and deletions.
