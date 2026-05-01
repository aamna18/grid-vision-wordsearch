# grid-vision-wordsearch
 
A computer vision and OCR pipeline that automatically solves word search puzzles from PDF or image input — no manual transcription required.
 
---
 
## Overview
 
Given a scanned or digital word search puzzle and a list of target words, the system handles the full solving process end-to-end:
 
1. Detects and isolates the puzzle grid from the input
2. Segments the grid into individual cells
3. Recognizes characters using per-cell OCR
4. Cleans and normalizes the extracted grid
5. Searches for all target words across 8 directions
6. Outputs a color-coded visual solution
The pipeline is designed to handle real-world inputs — including noise, imperfect scan alignment, and OCR inaccuracies.
 
---
 
## Pipeline
 
```
Input (PDF / Image)
       |
       v
 Grid Detection
       |
       v
 Cell Segmentation
       |
       v
  OCR (per cell)
       |
       v
  Grid Cleaning
       |
       v
 Word Search Solver
       |
       v
 Visualization Output
```
 
---
 
## Technical Components
 
### Grid Detection
 
Adaptive thresholding is applied to the input image, followed by morphological operations to isolate horizontal and vertical grid lines. The largest detected contour is treated as the puzzle boundary and cropped for downstream processing.
 
### Cell Segmentation
 
Row and column boundaries are inferred from detected line positions. Cells are extracted dynamically — no fixed grid size is assumed — making the system adaptable to puzzles of varying dimensions.
 
### OCR
 
Each cell is processed individually using EasyOCR. The preprocessing chain applied to each cell before recognition includes:
 
- Border removal to eliminate grid-line interference
- Upscaling for improved model performance
- CLAHE contrast enhancement for local contrast normalization
- Otsu binarization for clean black-and-white separation
Per-cell OCR significantly outperforms full-image OCR for grid-based layouts.
 
### Grid Cleaning
 
Raw OCR output is normalized to correct common character confusions:
 
| OCR Output | Corrected To |
|------------|--------------|
| `0`        | `O`          |
| `1`, `l`   | `I`          |
| `5`        | `S`          |
| `8`        | `B`          |
 
Cells that remain unrecognized are marked and treated as wildcards during the search phase.
 
### Solver
 
The solver searches for each target word in all 8 directions:
 
- Horizontal: left, right
- Vertical: up, down
- Diagonal: all four diagonals
Wildcard cells provide tolerance against residual OCR errors without compromising overall correctness.
 
### Visualization
 
The solution is rendered as a saved image with distinct colors assigned per word, highlighted cell paths, and a clean grid overlay.
 
---
 
## Installation
 
**Python dependencies:**
 
```bash
pip install pdf2image opencv-python pillow matplotlib easyocr
```
 
**System packages (required for PDF support):**
 
```bash
apt-get install poppler-utils
```
 
---
 
## Usage
 
Set the input file path and word list, then run the notebook:
 
```python
FILE_PATH = "puzzle.pdf"
 
WORDS = [
    "MONSTER", "GAME", "TRAIN", ...
]
```
 
Output includes console results with word coordinates and a saved image with the fully highlighted solution.
 
---
 
## Challenges and Solutions
 
**OCR noise**
Character-level errors are common in grid layouts. Addressed through per-cell OCR, aggressive preprocessing, and post-processing normalization rather than relying on a single pass over the full image.
 
**Grid detection variability**
Puzzle layouts differ in line thickness, spacing, and alignment. Adaptive thresholding and morphological line extraction handle this without hardcoding layout assumptions.
 
**Segmentation accuracy**
Inaccurate cell boundaries degrade OCR quality. Line-based grid reconstruction ensures boundaries align with actual grid lines rather than estimated positions.
 
---
 
## Limitations
 
- Accuracy is dependent on input image resolution and scan quality
- Severely distorted or handwritten puzzles may reduce recognition accuracy
- The target word list must be provided manually

