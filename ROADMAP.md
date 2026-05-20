# Roadmap


- [x] Read Phenoseeker README + scripts overview
- [x] Write `notes/phenoseeker_overview.md`
- [x] First Colab notebook: verify environment
- [x] Explore JUMP-CP metadata in Colab
- [x] Identify DMSO controls programmatically and select a working subset
- [x] Locate the image files on S3 and understand the path structure
- [x] Download a minimal subset (one source, one batch)
- [x] Visualize sample Cell Painting images (one well, 5 channels + RGB composite)
- [x] Run pre-trained DINOv2 inference on the subset (Colab GPU)
- [x] Apply a Phenoseeker-style normalization (sphering on DMSO + INT)
- [x] Compare two normalization strategies (per-feature z-score vs Phenoseeker-style)
- [x] Compare two visualization methods (PCA vs UMAP)
- [x] Quantitative evaluation by KMeans clustering (ARI against DMSO/compound labels)
- [x] Compare backbones (DINOv2 vits14 vs ResNet50 ImageNet)
- [ ] Scale up the subset (more DMSO wells per plate) for a more stable sphering matrix
- [ ] Reproduce a specific figure or analysis from Sanchez et al. on the subset
- [ ] Final writeup and project README revision
