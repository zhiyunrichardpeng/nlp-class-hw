# Advanced NLP: Homework 2 — BERT Chunker

This directory contains the starter for HW2-P: BERT Finetuning for Robust
Phrasal Chunking. Read the [full assignment on the course website](https://zhiyunrichardpeng.github.io/nlp-class/hw2.html)
for the task, experiments, deadlines, and grading table.

Copy the contents of this directory directly into `hw2/` in your group's
repository. Run the shell commands below from that `hw2/` directory (or from
`nlp-class-hw/bertchunker/` when checking the starter), unless stated otherwise.

## Installation

Use Python 3.10 or later. On Linux/macOS:

    python3.10 -m venv venv
    source venv/bin/activate
    python -m pip install -r requirements.txt

On Windows PowerShell, you can use the environment directly without activation:

    py -3 -m venv venv
    .\venv\Scripts\python.exe --version
    .\venv\Scripts\python.exe -m pip install -r requirements.txt

Confirm the interpreter is Python 3.10 or later.

You may copy and modify `requirements.txt` as `answer/requirements.txt` to
document your dependencies. Do not use external data or external libraries
that implement the fine-tuning model improvements for you.

## Model and training requirements

Use `distilbert-base-uncased` as the pretrained encoder. Keep its architecture
and pretrained weights fixed: all encoder parameters must remain frozen
throughout training. Train the classification head and any new components
you add above the encoder; do not unfreeze the encoder's top Transformer layers.

You may change training hyperparameters, including learning rate, number of
epochs, and batch size. Trainable model parameters (the weights of the head
and added components) are distinct from these training hyperparameters.

The corrected `answer/default.py` freezes the encoder and optimizes only the
classification head. If you add trainable components, include their parameters
in your optimizer while keeping the encoder frozen. Keep all existing command
line options; you may add options but must not remove the existing ones.

## Required files and report

Create all of the following:

* `answer/bertchunker.py` — your implementation.
* `answer/bertchunker.ipynb` — your report, including method, experiments,
  results, and discussion. Include it in `source.zip`; no separate `report.pdf`
  is required for HW2-P.
* `answer/README.username` — a short contribution statement from each group
  member, using that member's SFU username.

Compare the default baseline, an MLP head, a CRF head, and any other methods
you attempt. You may use MLP or CRF as your chosen improvement. Describe which
approach worked best and illustrate errors with examples. Report the training
settings and dev FB1 for each comparison. Develop your own improvement ideas;
trying one or two ideas does not remove the required baseline/MLP/CRF comparisons.

The default notebook imports `default.py`. After copying it for your report,
update imports and experiment cells to run your `bertchunker.py` and other
methods, and replace the demonstration with your own analysis and results.

## Default solution

On Linux/macOS, copy the provided files:

    cp answer/default.py answer/bertchunker.py
    cp answer/default.ipynb answer/bertchunker.ipynb

On Windows PowerShell:

    Copy-Item answer/default.py answer/bertchunker.py
    Copy-Item answer/default.ipynb answer/bertchunker.ipynb

Train a fresh frozen-encoder baseline and evaluate it (Linux/macOS):

    python answer/default.py -f > baseline-dev.out
    python conlleval.py -o baseline-dev.out

On Windows, train without shell redirection and collect predictions using
the supplied scripts, avoiding older PowerShell's different `>` encoding:

    .\venv\Scripts\python.exe answer/default.py -f
    .\venv\Scripts\python.exe zipout.py -r default.py -x .\venv\Scripts\python.exe -o baseline-output -z baseline-output
    .\venv\Scripts\python.exe check.py -z baseline-output.zip

Check `baseline-output/dev.ret` and `baseline-output/test.ret` for `0`.

The `-f` option forces training and replaces `data/chunker.pt` and any matching
epoch checkpoints. Preserve previous experiment checkpoints under a different
name if you need them. Without `-f`, training starts only if the model file is
missing. Training time depends on your hardware and settings.

After changing your solution or training settings, retrain it with
`python answer/bertchunker.py -f` (on Windows,
`.\venv\Scripts\python.exe answer/bertchunker.py -f`) before creating your
submission outputs. Otherwise an existing checkpoint is reused. Preserve
baseline and method results separately for the required comparisons.

Submitting the default solution without modification earns zero marks.

## Checkpoint compatibility

Earlier starter code fine-tuned the whole encoder. Its checkpoints, including
the [historical downloadable chunker.pt](https://drive.google.com/file/d/1OY_NKo0vxS2ndttdfpqGyMo0PubbhIK3/view),
are not verified frozen-encoder checkpoints and must not be used as the starting
point for the corrected model. The corrected loader rejects legacy checkpoints
without the frozen-encoder metadata. Train a new model from the original
`distilbert-base-uncased` encoder using `-f`; do not just add metadata to an old
checkpoint. Historical scores and notebook outputs do not establish the
performance of the corrected frozen baseline.

## Create output.zip

Once your implementation and chosen model are ready, run on Linux/macOS:

    python zipout.py
    python check.py

On Windows PowerShell, specify the interpreter used by `zipout.py`:

    .\venv\Scripts\python.exe zipout.py -x .\venv\Scripts\python.exe
    .\venv\Scripts\python.exe check.py

Check `output/dev.ret` and `output/test.ret` for return code `0`, and inspect
the corresponding `.err` files if either run fails. `zipout.py` can create an
archive even when a child program fails, so the existence of `output.zip` alone
does not establish successful prediction.

For more options, use `python zipout.py -h` and `python check.py -h`. To save
evaluation details, use `python check.py -l log`. On Windows, use the environment's
Python executable in place of `python` for these commands.

Evaluation uses chunk-level FB1 (F1), rather than token accuracy. Test-set
performance is hidden and will be evaluated after the submission deadline.

## Create source.zip and submit

Run:

    python zipsrc.py

On Windows, use `.\venv\Scripts\python.exe zipsrc.py`. For options, use `-h`.
Check that `source.zip` contains `bertchunker.py`, `bertchunker.ipynb`, and one
`README.username` for each group member, plus all additional source files your
solution requires. Files from `answer/` appear at the archive root.

Upload `output.zip` and `source.zip` to HW2-P on Coursys as a group submission,
and update your group's GitHub repository with the same final source and report.
Do not commit large model checkpoints to GitHub or place them in `answer/`.

## Data files

* `data/train.txt.gz` — clean CoNLL 2000 training data, with word, POS, and chunk
  label columns.
* `data/input/dev.txt` and `data/input/test.txt` — noisy word/POS input, with no
  chunk label column.
* `data/reference/dev.out` — reference chunk labels for the dev input.

Use the supplied data only. Do not use test data to tune or select your model.
