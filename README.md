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

Feature source and checkpoint(The preprocessed feature files are available for download via the following cloud storage link: https://pan.baidu.com/s/1xAY58R1z4stsc426TLmh5g?pwd=6a3s)

The residue-level feature files were generated locally using our feature-extraction script, rather than downloaded as precomputed embeddings. The input amino-acid sequences were read from the dataset pickle files, and the corresponding coordinate files were provided as Dataset/pdb/<protein_ID>.pdb. The resulting per-protein embeddings were saved as .npy files in Feature/SaProt-WslFoldseek; each array contains one 1,280-dimensional embedding per residue.
We used the SaProt 650M AF2 checkpoint released by Westlake (westlake-repl/SaProt_650M_AF2). SaProt is invoked automatically as part of the model training pipeline, without requiring separate script execution or additional commands.
For UBtest_31-6, a matching PDB file was available for each of the 25 protein chains. We ran Foldseek’s structureto3didescriptor command on each structure through WSL to obtain its 3Di sequence. The Foldseek amino-acid sequence was aligned to the dataset sequence; positions without an aligned 3Di token were represented by #. Each amino acid was then paired with its corresponding 3Di token and supplied to the SaProt tokenizer. The resulting residue embeddings were saved in the same .npy format.
