# Basic Sequence Alignment Concepts

## Definition of Alignment

Given two sequences \(A\) and \(B\) of lengths \(n\) and \(m\) respectively, an **alignment** is a way to place these sequences one above the other, inserting gaps (`-`) where necessary, so that each column represents a pairing:

- If a column has (base from \(A\), base from \(B\)) = (C, C), that is a **match**.
- If they are different, that is a **mismatch** (or **replacement**).
- If one sequence has a gap (`-`) aligned to a character in the other, that indicates a **deletion** (from the perspective of one sequence) or an **insertion** (from the other perspective).

Formally:
- We transform \(A\) and \(B\) into new sequences \(U\) and \(V\) of the **same length** \(h\), by inserting the gap symbol (`-`). 
- \(A\) must be a subsequence of \(U\), and \(B\) must be a subsequence of \(V\).
- The length \(h\) ranges between \(\max(n,m)\) and \(n + m\).

## Alignment Example

Consider:
- Sequence \(A = \text{CCGATG}\) (length 6)
- Sequence \(B = \text{ACGGCTA}\) (length 7)

An alignment might look like:

```
A: C C G A - T G
B: A C G G C T A
```

- The `-` indicates a gap inserted in sequence \(A\) to match up a symbol in \(B\).
- Each column shows either a match/mismatch (substitution), an insertion, or a deletion.

## Cost of Alignment

We typically define a **cost function** \( c(x,y) \) (or a **scoring function** \( s(x,y) \) in a similarity context) for aligning any two "characters" \( x \) and \( y \). Commonly:

- **Match**: cost = 0 (or positive score)
- **Mismatch** (replacement): cost = 1 (or negative score)
- **Insertion/Deletion** (gap penalty): cost = 1 (or some negative score)

The **total alignment cost** is the sum of these individual pairwise costs across all columns. Minimizing total cost is equivalent to maximizing an alignment **score** if you flip the sign of the cost function.

## Types of Alignments

There are several types of sequence alignments:

### Global Alignment

Global alignment attempts to align every character in both sequences from beginning to end. This is useful when the sequences are of similar length and are expected to be similar throughout their entire length.

### Local Alignment

Local alignment identifies regions of high similarity within the sequences, without necessarily aligning them from end to end. This is useful when looking for conserved domains or motifs within otherwise dissimilar sequences.

### Semi-Global (Overlap) Alignment

Semi-global alignment allows free gaps at the beginning or end of one or both sequences. This is useful for finding overlaps between sequences, such as in genome assembly.

## Biological Context

In biological sequences:

- **DNA/RNA**: Alignments compare nucleotide sequences (A, C, G, T/U)
- **Proteins**: Alignments compare amino acid sequences (20 different amino acids)

The choice of cost/scoring function often reflects biological reality:

- Some substitutions are more likely than others (e.g., transitions vs. transversions in DNA)
- In proteins, substitutions between amino acids with similar properties are more common
- Gaps often represent evolutionary insertion/deletion events

## Visualization

Alignments can be visualized in several ways:

1. **Pairwise alignment**: Two sequences stacked with gaps
2. **Dot plot**: A matrix where dots indicate matches between sequences
3. **Multiple sequence alignment**: More than two sequences aligned together
4. **Alignment graphs**: Network representations of alignments

In the next sections, we'll explore algorithms for computing optimal alignments and measuring sequence similarity.
