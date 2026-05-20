# Phenotypic Profiling on JUMP-CP : a Phenoseeker-Inspired Pipeline

A personal exploration of phenotypic compound profiling on the JUMP-CP Cell Painting dataset, inspired by the Phenoseeker pipeline from Sanchez et al. (*Communications Biology*, 2026).

The goal is not to fork or reuse the Phenoseeker code, but to re-implement a simplified version of its core ideas from scratch, in order to learn the mechanics of phenotypic profiling and develop a working environment for further experimentation.

## Goals

1. Re-implement a simplified version of the Phenoseeker pipeline (DINOv2 embedding extraction, DMSO-based sphering, inverse normal transform) on a small subset of JUMP-CP.
2. Explore variants of the pipeline: different normalization strategies (per-feature z-score vs Phenoseeker-style sphering), alternative backbones (DINOv2 vits14 vs ResNet50 ImageNet), and visualization methods (PCA, UMAP).
3. Evaluate the pipeline quantitatively by KMeans clustering and Adjusted Rand Index against the known DMSO / compound labels.
4. Document each step clearly enough that any reader can re-run the analyses in Google Colab.

## Status

Project initiated May 2026, ongoing. See `ROADMAP.md` for current state and planned next steps.

The pipeline is currently implemented end to end on a small subset (3 plates of source 3, batch CP59, around 10 wells processed). All notebooks run in Google Colab without local installation.

## References

- **Phenoseeker**: Sanchez et al. (2026). *Large Scale Compound Selection Guided by Cell Painting Reveals Activity Cliffs and Functional Relationships.* Communications Biology. [Paper](https://doi.org/10.1038/s42003-025-09500-y) · [Original code](https://github.com/mxfly14/2025_sanchez_phenoseeker) · [Web tool](https://www.phenoseeker.bio.ens.psl.eu/)
- **JUMP-CP**: Joint Undertaking for Morphological Profiling (Broad Institute + pharma partners).
- **JUMP downloader**: Watkinson et al., ISBI 2024. [Code](https://github.com/gwatkinson/jump_download).

## Author

Denis De Carvalho, engineering student in Bioinformatics & Pharmacology (EPISEN, Paris).
