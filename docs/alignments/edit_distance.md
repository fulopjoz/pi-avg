# Edit Distance and Dynamic Programming

## Edit Operations

When discussing **edit distance**, we typically allow the following operations:

1. **Substitution (Replacement)**: Change one character into another.
2. **Insertion**: Insert a new character into a sequence.
3. **Deletion**: Delete a character from a sequence.
4. **Match**: Often treated as a "zero-cost" or "free" operation (a special case of substitution).

The **edit distance** between two sequences is defined as the minimum number of edit operations needed to transform one sequence into another.

## Levenshtein Distance

The most common form of edit distance is the **Levenshtein distance**, which allows:
- Substitutions (cost = 1)
- Insertions (cost = 1)
- Deletions (cost = 1)
- Matches (cost = 0)

For example, the Levenshtein distance between "kitten" and "sitting" is 3:
1. **k**itten → **s**itten (substitution of 'k' with 's')
2. sitt**e**n → sitt**i**n (substitution of 'e' with 'i')
3. sittin → sittin**g** (insertion of 'g' at the end)

## Dynamic Programming Solution

We compute edit distance using **dynamic programming**. Define a matrix \( D \) of size \((n+1)\times(m+1)\) where:
- Rows correspond to prefixes of sequence \(A\) (length \(n\))
- Columns correspond to prefixes of sequence \(B\) (length \(m\))
- \(D(i,j)\) is the minimal edit distance between \(A[1..i]\) and \(B[1..j]\)

### Recurrence Relation

The key recurrence relation is:

$$
D(i,j) = \min \begin{cases}
D(i-1,j-1) + \text{cost}(A[i], B[j]) & \text{(substitution/match)} \\
D(i-1,j) + \text{cost}(\text{deletion}) & \text{(deletion)} \\
D(i,j-1) + \text{cost}(\text{insertion}) & \text{(insertion)}
\end{cases}
$$

Where:
- \(\text{cost}(A[i], B[j])\) is 0 if \(A[i] = B[j]\) (match), and 1 otherwise (substitution)
- \(\text{cost}(\text{deletion})\) is typically 1
- \(\text{cost}(\text{insertion})\) is typically 1

### Boundary Conditions

We initialize the matrix with:
- \(D(0,0) = 0\) (empty string to empty string requires no operations)
- \(D(i,0) = i\) (transforming a string of length i to an empty string requires i deletions)
- \(D(0,j) = j\) (transforming an empty string to a string of length j requires j insertions)

### Example

Let's compute the edit distance between \(A = \text{"ACGT"}\) and \(B = \text{"ATGC"}\):

```
    |   | A | C | G | T
----|---|---|---|---|---
    | 0 | 1 | 2 | 3 | 4
----|---|---|---|---|---
  A | 1 | 0 | 1 | 2 | 3
----|---|---|---|---|---
  T | 2 | 1 | 1 | 2 | 2
----|---|---|---|---|---
  G | 3 | 2 | 2 | 1 | 2
----|---|---|---|---|---
  C | 4 | 3 | 2 | 2 | 2
```

The edit distance is \(D(4,4) = 2\), meaning we need at least 2 operations to transform "ACGT" to "ATGC".

## Traceback

To find the actual sequence of edit operations, we perform a **traceback** through the matrix:

1. Start at cell \(D(n,m)\)
2. At each step, move to the cell that led to the current value (diagonal for substitution/match, up for deletion, left for insertion)
3. Continue until reaching \(D(0,0)\)

The path through the matrix reveals the optimal sequence of edit operations.

## Variations of Edit Distance

### Hamming Distance

**Hamming distance** only allows substitutions, not insertions or deletions. It can only be computed between sequences of equal length and counts the number of positions where the characters differ.

### Longest Common Subsequence (LCS)

The **Longest Common Subsequence** problem is closely related to edit distance. It finds the longest sequence that appears in the same order (but not necessarily consecutively) in both input sequences.

The length of the LCS relates to edit distance when only insertions and deletions are allowed (no substitutions):

$$
\text{EditDistance}_{\text{InDel}}(A,B) = |A| + |B| - 2 \times |\text{LCS}(A,B)|
$$

## Complexity Considerations

- **Time complexity**: \(O(n \times m)\), since we fill an \(n \times m\) matrix with constant-time updates.
- **Space complexity**: \(O(n \times m)\) if we store the entire matrix.

### Space Optimization

If we only need the final distance value (not the alignment), we can optimize space to \(O(\min(n,m))\) by storing only two rows or columns at a time, since each cell only depends on the previous row and column.

## Applications in Genomics

Edit distance has numerous applications in computational genomics:

- **Sequence comparison**: Measuring similarity between DNA or protein sequences
- **Error correction**: Identifying and correcting sequencing errors
- **Read mapping**: Aligning sequencing reads to a reference genome
- **Variant calling**: Identifying mutations by comparing sequences
- **Phylogenetic analysis**: Determining evolutionary relationships

In the next section, we'll explore global and local alignment algorithms, which build upon these edit distance concepts.
