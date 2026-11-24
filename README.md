## Environment

The reference Conda environment is named `VDG2` and uses Python 3.10.20. Recreate it with:

```powershell
conda env create -f environment.yml
conda activate VDG2
```

CUDA 12.1-compatible PyTorch and PyG wheels are specified in `requirements.txt`. A supported NVIDIA GPU is strongly recommended for training and full evaluation.

## Data layout

The four dataset pickles and all per-protein inputs required by the two entry points are included:

- `Dataset/`: `Train_335.pkl` and the three evaluation datasets.
- `Feature/{pssm,hmm,dssp,resAF,distance_map_SC,psepos,SaProt-WslFoldseek}/`: features for the 706 unique proteins used across those datasets.Feature can download from: https://pan.baidu.com/s/1xAY58R1z4stsc426TLmh5g?pwd=6a3s
- `ppis_train335_seed2026_folds5.json`: the fixed five-fold split (seed 2026).
- `runs/base_models/`:Includes all trained weights. the six final base checkpoints and OOF meta features.

Paths are relative to the repository root, so run all commands from that directory.run main.py to get the prediction results.

## SaProt Feature Generation

Feature source and checkpoint

The residue-level SaProt embeddings were generated locally using the feature-extraction pipeline provided in this repository, rather than obtained from third-party precomputed embeddings. For each protein, the amino-acid sequence was read from the corresponding dataset pickle file, and the corresponding structural coordinates were loaded from Dataset/pdb/<protein_ID>.pdb.

The feature extractor loads the pretrained SaProt 650M AF2 checkpoint released by Westlake (westlake-repl/SaProt_650M_AF2). The corresponding SaProt tokenizer processes the amino-acid and 3Di structural tokens to generate residue-level representations.

The resulting embeddings are saved as individual .npy files in Feature/SaProt-WslFoldseek/. Each array contains one 1,280-dimensional embedding per residue, with the residue order consistent with the corresponding amino-acid sequence in the dataset pickle file.

Feature extraction procedure

SaProt inference is integrated into the model training pipeline and is automatically triggered by the corresponding training entry point. No separate manual invocation of SaProt is required.

The feature-extraction implementation is located in <feature_extraction_script.py>, and the corresponding function is called by <training_script.py>. To execute the pipeline, run the following command from the repository root:
