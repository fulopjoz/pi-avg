# PI-AVG: Computational Genomics Algorithms (Algoritmy výpočetní genomiky)

This repository contains study materials for the PI-AVG course at Czech Technical University in Prague, focusing on computational genomics algorithms.

## Course Schedule

Lectures: Tuesday 9:15 at room TH:A-1247

- March 4, 2025: Multiple Sequence Alignment I
- March 18, 2025: Multiple Sequence Alignment II
- April 1, 2025: Read Alignment
- April 15, 2025: Genome Assembly
- April 29, 2025: Q&A
- May 13, 2025: Q&A

## Contents

- **Alignments**: Documentation on various sequence alignment algorithms
  - Basic alignments
  - Edit distance
  - Global and local alignments
  - Affine gap penalties
- **Algorithms**: General algorithm concepts and implementations
- **Data Structures**: Common data structures used in algorithms
- **Case Studies**: Real-world applications and examples
- **Code Examples**: Implementation examples in various languages

## Getting Started

This documentation is built with [MkDocs](https://www.mkdocs.org/), a fast and simple static site generator.

### Prerequisites

- Python 3.x
- pip (Python package installer)

### Installation

1. Clone this repository:
   ```
   git clone https://github.com/fulopjoz/pi-avg.git
   cd pi-avg
   ```

2. Install MkDocs and all required packages:
   ```
   pip install -r requirements.txt
   ```

3. Serve the documentation locally:
   ```
   mkdocs serve
   ```

4. Open your browser and navigate to `http://127.0.0.1:8000/`

### GitHub Pages Deployment

To deploy the documentation to GitHub Pages:

1. Build the site:
   ```
   mkdocs build
   ```

2. Deploy to GitHub Pages:
   ```
   mkdocs gh-deploy
   ```

This will build the site and push it to the `gh-pages` branch of your repository, making it available at `https://fulopjoz.github.io/pi-avg/`.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
