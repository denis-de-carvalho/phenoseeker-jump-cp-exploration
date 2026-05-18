# Notes on the Phenoseeker pipeline

*These are my working notes, not a finished summary.*

## What Phenoseeker does

Phenoseeker is a Python toolkit for **phenotype-based compound preselection**. Starting from Cell Painting microscopy images of cells treated with chemical compounds, it produces, for each compound, a numerical "phenotypic signature" (an embedding vector). Compounds with similar phenotypic signatures are likely to have similar biological activity, even when their chemical structures are very different.

## Inputs and outputs

**Inputs:**
- cell Painting microscopy images (typically from the JUMP-CP dataset, 112,480 compounds)
- associated metadata (plate, well, compound identifier, DMSO control flag, etc.)
- configured via YAML files in `configs/`

**Outputs:**
- well-level normalized embeddings (one vector per well)
- compound-level aggregated embedings (one vector per compound)
- used downstream for similarity ranking, activity cliff detection, and pre-screening

## The pipeline

1. **Image-level feature extraction** : each image goes through DINOv2 (used training-free); output: one embedding per image.
2. **Aggregation to well level** : for each well, the embeddings of all its images are averaged ("well mean"); output: one embedding per well.
3. **Sphering on DMSO controls** : a whitening transform is computed using only DMSO control wells, then applied to all well-level embeddings; effect: removes technical-only directions of variance (batch effects).
4. **Inverse normal transform (INT)** : each dimension of the embedding is rank-transformed to follow a standard normal distribution; effect: stabilizes statistics across dimensions.
5. **Compound-level aggregation (optional)** : for each compound, its wells' normalized embeddings are aggregated to give a single per-compound vector.
6. **Evaluation / downstream use** : embeddings used for retrieval (mAP scoring), activity cliff detection, pathway analysis, etc.

## Key technical choices

- **Backbone for embedding extraction :** DINOv2 (frozen, training-free transfer learning).
- **Normalization recipe (default) :** well mean -> sphering on DMSO -> inverse normal transform.
- **Dataset :** JUMP-CP, 112,480 compounds.
- **Framework :** Python 3.11, PyTorch 2.9, dependency management via Poetry.

## Where to find each component in the repo

| What | Where |
|------|-------|
| Pipeline configuration | `configs/*.yaml` |
| Feature extraction | `scripts/extract_features.py` |
| Profile creation (full pipeline) | `scripts/create_profiles.py` |
| Normalization grid search | `scripts/test_normalisations.py` |
| ChEMBL label integration | `scripts/get_chembl_activities.py` |
| Lit-PCBA mapping | `scripts/explore_lit_PCBA.py` |
| Pathway analysis | `scripts/pathways_max.py` |
| Core library (`EmbeddingManager`, `BioproxyEvaluator`) | `src/phenoseeker/` |
| How-to guides | `docs/embedding_manager.md`, `docs/bioproxy_evaluator.md` |
| Reproducible figures | `notebooks/` (e.g., `fig_1_umap.ipynb`) |


## questions for later

- choose JUMP-CP subset to download first to test the pipeline
- How is the sphering matrix computed in practice (covariance of DMSO embeddings, then inverse square root)?
- What does the `BioproxyEvaluator` actually score? mAP against what reference?



## Glossary : technical terms encountered while reading

- **Cell Painting** : a standardized fluorescence microscopy assay that stains 8 cellular components (nucleus, mitochondria, Golgi, ER, RNA, actin/Golgi, etc.) using 6 dyes captured in 5 image channels. Produces rich morphological "fingerprints" of treated cells.
- **JUMP-CP** : Joint Undertaking for Morphological Profiling, Cell Painting. The largest public Cell Painting dataset, jointly produced by the Broad Institute and pharma partners. ~116,000 compounds, ~85 TB raw.
- **Embedding** : a vector of numbers (often of length 512 or 1024) that summarizes a complex object (here, an image). Two visually similar images → embeddings close together in vector space.
- **DINOv2** : a self-supervised vision foundation model from Meta AI, pre-trained on large generic image collections. Used as a "feature extractor" without retraining (transfer learning).
- **Transfer learning** : using a model trained on one task/domain as the starting point for a different task. In Phenoseeker, DINOv2 was trained on generic images, but is reused as-is on microscopy images.
- **Training-free** : variant of transfer learning where the pretrained model is used *without any fine-tuning* on the target domain. Phenoseeker uses DINOv2 training-free.
- **Backbone** : the neural network used to compute embeddings. Here DINOv2.
- **Well** : One well = one experimental condition (e.g., one compound at one concentration).
- **Well mean** : averaging the embeddings of all images taken within a single well, to produce one embedding per well.
- **DMSO** : dimethyl sulfoxide, the solvent in which compounds are dissolved. Wells containing only DMSO (no compound) serve as **negative controls** in the screen.
- **Sphering (or whitening)** : a linear transformation that, computed from the covariance of a reference group (here DMSO controls), rescales the embedding space so that the controls become isotropic (identity covariance). Removes directions of technical variance.
- **Inverse normal transform (INT)** : a rank-based transformation that forces each dimension of the embedding to follow a standard normal distribution. Stabilizes statistics across dimensions.
- **Batch effect** : non-biological variability between experimental batches (plates, days, labs). The principal enemy of any large-scale image-based screen.
- **Activity cliff** : a pair of structurally very similar compounds with very different biological activities. Hard to detect from chemical structure alone; phenotypic embeddings catch them well.
- **mAP (mean Average Precision)** : a retrieval evaluation metric. Higher = better. Used here to score how well embedding similarity recovers known biological relationships.
- **ChEMBL** : a large public database of bioactive molecules and their measured activities. Used as ground truth for evaluation.
- **Lit-PCBA** : a curated benchmark from the literature derived from PubChem BioAssay, with per-target lists of active and inactive compounds. Used for retrieval evaluation.
- **BindingDB** : public database of measured binding affinities between small molecules and protein targets. Used for pathway analysis here.
- **Poetry** : a Python tool for managing project dependencies (alternative to pip + requirements.txt).
- **EmbeddingManager** : the central class in `src/phenoseeker/` that handles loading, normalization, aggregation, and visualization of embeddings.
- **BioproxyEvaluator** : the class in `src/phenoseeker/` that evaluates phenotypic embeddings against biological reference data (e.g., ChEMBL bioactivities, Lit-PCBA targets) to compute retrieval scores.
- **UMAP** : Uniform Manifold Approximation and Projection. A dimensionality reduction technique used to visualize high-dimensional embeddings in 2D plots.
