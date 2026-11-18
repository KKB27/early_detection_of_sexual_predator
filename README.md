# Explainable Chat Risk Review Project

This folder contains a research/course project that uses coded conversations and PAN-12 data to study:

1. **Stage 1:** message-level classification into the coded stages `200`, `600`, and `900`.
2. **Stage 2:** conversation-level PAN-12 risk scoring, with numeric and text feature comparisons.
3. **Review explanations:** SHAP reports and a notebook for scoring a user-provided message or conversation.

The models are research tools for **human review only**. They do not determine whether a person is dangerous or whether a conversation is safe. In particular, Stage 1 was trained only on coded messages from predatory conversations, so its `200`/`600`/`900` labels are stage estimates, not safe/unsafe classifications. PAN-12 reflects a specific dataset and decoy-sting setting; results are not guarantees of real-world performance.

## 1. Extract the ZIP and open the project root

Extract the ZIP while preserving its folder structure. In Jupyter, set the working directory to the extracted `NLP Project Final` folder (the folder containing this README). The notebooks use paths relative to that root, such as `models/`, `reports/`, and `data/processed/`.

Do not open a notebook from a different working directory and assume its relative paths will work.

## 2. Software prerequisites

- Python 3.11 or 3.12 is recommended for a fresh environment.
- Jupyter Notebook or JupyterLab.
- Internet access is needed the first time the sentence-transformer model is downloaded in notebook 06 or 14, unless the model is already cached.
- A GPU is optional. TF-IDF and logistic regression work on CPU; batching is used where large text inputs could otherwise use substantial memory.

### Windows PowerShell setup

Open PowerShell in the extracted project root:

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install jupyterlab ipykernel numpy pandas scipy scikit-learn joblib matplotlib lightgbm shap sentence-transformers
python -m ipykernel install --user --name nlp-project --display-name "Python (NLP Project)"
jupyter lab
```

In Jupyter, choose the **Python (NLP Project)** kernel.

If PowerShell prevents environment activation, either follow your organization's policy for local scripts or run the venv's Python directly:

```powershell
.\.venv\Scripts\python.exe -m pip install jupyterlab ipykernel numpy pandas scipy scikit-learn joblib matplotlib lightgbm shap sentence-transformers
.\.venv\Scripts\python.exe -m jupyter lab
```

### macOS/Linux setup

```bash
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install jupyterlab ipykernel numpy pandas scipy scikit-learn joblib matplotlib lightgbm shap sentence-transformers
python -m ipykernel install --user --name nlp-project --display-name "Python (NLP Project)"
jupyter lab
```

The saved `joblib` models were created with particular library versions. Loading them with a different scikit-learn version may show an `InconsistentVersionWarning` or fail. For reproducible results, use the same Python environment that created the supplied models when available. Otherwise, install compatible dependencies and rerun the model-training notebooks locally before using the scorer; do not ignore a model-loading error.

## 3. Quick start: score the included sample

The ZIP includes trained models and reports under `models/` and `reports/`, including the thresholds and Stage 1 transition matrix required by the scorer. If these files are present, there is no need to retrain just to try the sample.

1. Start Jupyter from the project root.
2. Open `14_user_conversation_scoring.ipynb`.
3. Run its cells from top to bottom.
4. The default configuration reads `user_conversation.txt`. It contains a neutral sample with one message per line and speaker labels.
5. To score another conversation, edit `INPUT_FILE` in the input cell to point to a UTF-8 text file.
6. To enter a single message interactively, set `INPUT_MODE = "sentence"` instead of `"text_file"`.

For text files, use **one message per line**. Speaker labels are optional but recommended, for example:

```text
Speaker A: [message text]
Speaker B: [message text]
Speaker A: [message text]
```

The input parser treats text before the first colon as a speaker label. Avoid timestamps; this notebook does not parse timestamps. Without distinct speaker labels, conversation behavior features are weak approximations. A single sentence is especially outside the validated conversation-level use case.

### Red/green result

The scorer produces a binary review indicator from the two Stage 2 models:

- **Red:** either model score reaches that model's saved validation cutoff.
- **Green:** both model scores are below their respective cutoffs.

Green means only **“not flagged by these models”**; it is not evidence that a conversation is safe. The model cutoffs were selected on a PAN-12 validation split and are not calibrated for real-world conversations. Stage 1 stage predictions do not determine the red/green result.

The notebook displays scores and stage predictions in its output. It does not write the supplied conversation text or score tables to files. Do not save or share notebook outputs containing private conversations.

## 4. Rebuild and run the notebooks

To reproduce the pipeline from data preparation through the final scorer, run the notebooks in this order, always from the project root:

| Order | Notebook | Purpose |
|---|---|---|
| 1 | `01_coded_chat_data_loading.ipynb` | Parse the coded XML chats; create the message-level Stage 1 data. |
| 2 | `02_coded_chat_exploration.ipynb` | Explore coded message counts and stage sequences. |
| 3 | `03_overlap_leakage_check.ipynb` | Check exact text overlap between coded chats and PAN-12. |
| 4 | `04_stage1_baseline.ipynb` | Train/evaluate the grouped-CV TF-IDF Stage 1 classifier and save its model. |
| 5 | `05_stage1_lexicon_features.ipynb` | Compare the lexicon-augmented Stage 1 model. |
| 6 | `06_stage1_embeddings.ipynb` | Compare sentence embeddings. The first run may download `all-MiniLM-L6-v2`. |
| 7 | `07_stage1_viterbi_smoothing.ipynb` | Learn stage transitions and evaluate Viterbi sequence smoothing. |
| 8 | `08_stage1_pan12_external_validation.ipynb` | Indirectly evaluate Stage 1 against supplied PAN-12 test-line labels. |
| 9 | `09_pan12_stage2_features.ipynb` | Parse PAN-12, generate Stage 1 scores, and create conversation-level features. This is a large-data step and may take time/memory. |
| 10 | `10_stage2_baseline_ablation.ipynb` | Compare text-only, stage-only, behavior-only, and combined logistic-regression models. |
| 11 | `11_stage2_lightgbm.ipynb` | Train/compare LightGBM models. |
| 12 | `12_stage2_shap_explanations.ipynb` | Generate SHAP feature importance and worked examples. |
| 13 | `13_stage2_final_evaluation.ipynb` | Consolidate Stage 2 evaluation, bootstrap intervals, and precision-recall plots. |
| 14 | `14_user_conversation_scoring.ipynb` | Score a user-provided sentence or conversation text file. |

Use **Run All** or execute cells from top to bottom. If you rerun a notebook from the middle, make sure all prerequisite cells and notebooks have run in the current environment. Training notebooks may overwrite model/report artifacts with newly generated versions.

Notebook outputs are included and should be retained when rerunning or editing the notebooks; do not clear them unless you explicitly want to remove the recorded execution results.

## 5. Data and generated files

### Inputs expected for a full rebuild

- `encoded_chats_xml/`: individual coded XML chat files.
- PAN-12 XML and label files at the project root:
  - `pan12-sexual-predator-identification-training-corpus-2012-05-01.xml`
  - `pan12-sexual-predator-identification-training-corpus-predators-2012-05-01.txt`
  - `pan12-sexual-predator-identification-test-corpus-2012-05-17.xml`
  - `pan12-sexual-predator-identification-groundtruth-problem1.txt`
  - `pan12-sexual-predator-identification-groundtruth-problem2.txt`
- `lexicons/`: project lexicon text files.

If any source dataset is missing from your ZIP, obtain it from its authorized source and place it at the path expected above. Check the dataset's license/terms before copying or redistributing it. The full PAN-12 XML files may be large.

### Outputs

- `data/processed/`: parsed messages, conversation feature tables, Stage 1 predictions, and embeddings generated during execution.
- `models/`: fitted Stage 1 and Stage 2 model files.
- `reports/`: evaluation CSV/JSON files, transition matrix, overlap checks, and SHAP results.

The first notebook creates `data/processed/` as needed. If training is not run, that folder may be empty; the quick-start scorer instead needs the model and report files listed in its artifact-loading cell.

## 6. Method and limitations

- The coded-chat corpus contains 56 available files in the current project inventory (although an earlier plan anticipated 57).
- Stage 1 uses only coded predator messages with labels `200`, `600`, and `900`; uncoded victim messages are not treated as an invented class.
- Stage 1 evaluation groups by conversation to avoid splitting messages from the same chat across train/test folds.
- PAN-12 supplies conversation/author labels and test-line labels, but no grooming-stage labels. Stage 1 evaluation on PAN-12 is therefore indirect.
- Stage 2 uses PAN-12-derived labels and features. Dataset artifacts, author/source effects, class imbalance, and the decoy-sting setting limit generalization.
- Model explanations are learned associations, not causes or proof. Scores should support careful human review and must not be used as automatic determinations about people.

## 7. Common issues

- **`FileNotFoundError`:** start Jupyter with the extracted project root as the working directory; check the required files and folder names.
- **Missing model/threshold/transition file in notebook 14:** run the corresponding training notebook(s), or restore the supplied `models/` and `reports/` artifacts from the ZIP.
- **`ModuleNotFoundError`:** install the named package in the active notebook kernel, then restart the kernel.
- **Sentence-transformer download errors:** check internet access and try again; model weights are required for the embedding Stage 1 model.
- **Model deserialization/version errors:** use a compatible Python/scikit-learn environment or retrain locally in the active environment.
- **Memory pressure in notebook 09:** close other memory-heavy applications and run the notebook on a machine with sufficient RAM. GPU memory does not replace system RAM for its TF-IDF processing.
- **Notebook 14 missing speaker warning:** ensure the text file has at least two speaker labels used consistently, such as `Speaker A:` and `Speaker B:`.

For a concise work log, see `things_done.txt`.
