Cleaned PAMNet QM9 experiment runner
====================================

Replace your current main_qm9.py and models.py with the files in this folder.
The rest of the project structure remains the same.

Variants:
  original : original PAMNet/PAMNet_s architecture
  ws       : weight sharing only
  br       : basis reduction only
  wsbr     : weight sharing + basis reduction

Default dataset split is QM9-5k:
  train=4000, val=500, test=500

Default target behavior keeps the original project mapping:
  --target 7 maps to actual QM9 column 12 (U0_atom)
To disable this behavior and use the raw QM9 column 7 (U0), add:
  --no_atom_mapping

Example commands:

1) Original PAMNet:
python -u main_qm9.py --dataset QM9 --model PAMNet --variant original --target 7 --epochs 10 --batch_size 32 --dim 128 --n_layer 6 --lr 1e-4 --run_name pamnet_original_t7

2) Weight sharing only:
python -u main_qm9.py --dataset QM9 --model PAMNet --variant ws --target 7 --epochs 10 --batch_size 32 --dim 128 --n_layer 6 --lr 1e-4 --run_name pamnet_ws_t7

3) Basis reduction only:
python -u main_qm9.py --dataset QM9 --model PAMNet --variant br --target 7 --epochs 10 --batch_size 32 --dim 128 --n_layer 6 --lr 1e-4 --run_name pamnet_br_t7

4) Proposed model: WS + BR:
python -u main_qm9.py --dataset QM9 --model PAMNet --variant wsbr --target 7 --epochs 10 --batch_size 32 --dim 128 --n_layer 6 --lr 1e-4 --run_name pamnet_wsbr_t7

5) PAMNet_s baseline:
python -u main_qm9.py --dataset QM9 --model PAMNet_s --variant original --target 7 --epochs 10 --batch_size 32 --dim 128 --n_layer 6 --lr 1e-4 --run_name pamnets_original_t7

Output files:
  results/<run_name>_epochs.csv   per-epoch logs
  results/summary.csv             one summary row per run; use this for comparison tables
  results/<run_name>_config.json   exact configuration of the run

The script also reports:
  - number of trainable parameters
  - best validation MAE
  - test MAE at best validation
  - total training time
  - average epoch time
  - test inference time
  - peak CUDA memory allocated/reserved
