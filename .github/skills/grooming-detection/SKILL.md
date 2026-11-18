---
name: grooming-detection
description: Project context, design decisions, and working rules for a college project that detects online grooming in chat conversations and explains WHY a conversation is flagged, so a parent can judge whether the danger is real. Use this skill whenever the user works on the PAN-12 dataset, the 57 coded XML chats (codes 200/600/900), stage classification, feature extraction, weak labeling, Viterbi/CRF smoothing, conversation-level risk scoring, SHAP/explainability, or evaluation for this project, even if they do not mention "grooming" explicitly.
---

# Grooming Detection Project (college course project)

## 1. Purpose

Build a **defensive, explainable** system that flags chat conversations for **parental review**. The system does not make final decisions: it surfaces evidence and the parent decides.

The key requirement from the user: the model must learn **why** a message or conversation is concerning (which grooming stage or tactic, at which point, with which evidence), not only output "bad / not bad".

Target explanation style (example of the intended output, not real data):

> Risk 0.91. Sexual talk began at message 38. A real-life meeting was proposed at message 61. The stage order matches the typical grooming progression.

The project is for a course. It does not need to be state of the art, but it must be done **properly**: sound methodology, honest evaluation, documented limitations.

## 2. Hard constraints (always respect)

1. **No hand labeling.** Hand-labeling messages must be avoided at all costs. Do not propose manual annotation or "hand-verify a sample". Use the existing codes, weak supervision, and indirect validation instead.
2. **Do not rush into code.** The user explicitly asked for discussion and planning before code. Propose the design or the next step, get confirmation, then write code. Never dump a full pipeline unprompted.
3. **Defensive purpose only.** Work on detection, explanation, and evaluation. Do not generate example grooming messages, scripts, or lists of manipulative phrases, and do not help craft text that evades detection. Describe patterns at the level of stage and function, not as ready-to-use lines. Use only data the user already has.
4. **Split by conversation, never by message.** Always group by chat/conversation ID in cross-validation (`GroupKFold`, `StratifiedGroupKFold`) to avoid leakage.
5. **Be honest about limits.** Report small-sample uncertainty, dataset artifacts, and false-positive cost.

## 3. Data

### 3.1 PAN-12 (sexual predator identification dataset)

- Large corpus of chat conversations (predator conversations from decoy-sting transcripts, negatives from ordinary chat).
can be found in pan12-sexual-predator-identification-training-corpus-2012-05-01.xml
- Provides a **list of user IDs who are predators** (conversation-level / author-level labels).
pan12-sexual-predator-identification-training-corpus-predators-2012-05-01.txt

- Provides **line-level predatory labels for the test set** (which lines inside conversations are predatory).
this is available for the test dataset, pan12-sexual-predator-identification-test-corpus-2012-05-17.xml

and the lines are in pan12-sexual-predator-identification-groundtruth-problem2.txt

and the predator list for test corpus is in: pan12-sexual-predator-identification-groundtruth-problem1.txt
- No stage/tactic labels.

### 3.2 The 57 coded conversations
- **57 separate conversations, each stored as an individual XML file.**
can be found in /encoded_chats_xml
- All are **definitely predatory**.
- **Every message is coded 200, 600, or 900:**
  - **200** = the predator is getting to know the victim (rapport / information gathering).
  - **600** = conversation moves into sexual talk.
  - **900** = the meeting-in-real-life stage.
- These codes ARE the stage labels. They remove any need for manual labeling.

### 3.3 Open questions (confirm with the user before depending on them)
- Exact XML schema (tags/attributes for author, timestamp, text, code). Ask for an anonymized snippet of structure before writing the parser.
- Whether both speakers' messages carry codes, or only the predator's. If victim messages are uncoded, decide on an extra "no code / victim" class.
- Whether the 57 chats overlap with PAN-12 (decoy-sting sources may be shared). Deduplicate before using PAN-12 as an external test.
- Whether timestamps exist in the XML (timing features are only valid if they do; never fabricate them).

## 4. Architecture: two stages

### Stage 1: message-level stage classifier
- **Trained on the 57 coded chats.** Target: 200 / 600 / 900, optionally plus a "benign / no code" class.
- **Context matters.** The same sentence can belong to different stages depending on neighbors, so include features or text from the previous 1 to 3 messages.
- **Model ladder (report each):**
  1. TF-IDF (word 1-3 grams and char_wb 2-5 grams) + class-weighted logistic regression (baseline, directly interpretable).
  2. Sentence embeddings (e.g. `all-MiniLM-L6-v2`) + logistic regression or LightGBM.
  3. Stretch: fine-tuned DistilBERT with token attributions (Captum/SHAP).
- **Sequence smoothing:** stages are ordered (200, then 600, then 900). Learn a 3x3 transition matrix from the 57 chats and apply **Viterbi decoding** (or a CRF) per conversation to remove isolated errors. This is also easy to explain.
- The user's earlier lexicons (common nouns, verbs, adjectives associated with predators) are **features / weak signals**, not the detector. Group them by stage or function and use per-category counts (lemmatized, normalized by message length).

### Stage 2: conversation-level risk model
- **Trained on PAN-12** conversations using the predator author list for labels; evaluated on the PAN-12 test set.
- Run Stage 1 over PAN-12 to get per-message stage probabilities, then aggregate:
  - fraction of messages per stage
  - position (relative and absolute) of first 600 and first 900
  - gap between stage onsets
  - whether stages occur in the expected order
  - slope of the 600 score over sliding windows
  - number of distinct stages reached
- Add **behavioral features**, computed per participant and as a ratio between participants (asymmetry is the signal):
  - message count and share, mean/variance of message length
  - who initiates, who restarts after gaps
  - response latency, duration, night-time share (only if timestamps exist)
  - double-texting rate, longest monologue
  - question ratio per participant
  - topic drift: cosine distance between embeddings of consecutive windows
- **Models:** class-weighted logistic regression baseline, then LightGBM/XGBoost with `shap.TreeExplainer` for exact per-feature explanations.

### Textual feature reference (message level)
| Group | Method |
|---|---|
| Word n-grams | TF-IDF, `min_df` 2-3, sublinear tf |
| Char n-grams | TF-IDF `char_wb` 2-5 (robust to slang/misspellings) |
| Lexicon counts | per-category count / length, plus binary hit; lemmatize first |
| Questions | has "?", question-word start, ratio in window |
| Pronouns | first-person vs second-person rate (spaCy POS) |
| Sentiment/emotion | VADER, NRC Emotion Lexicon |
| Style | length, caps ratio, emoji count, punctuation |
| Semantics | sentence-transformer embeddings |

Do not concatenate everything into one giant vector at first. Add feature groups incrementally so their contribution can be measured.

## 5. Validation without hand labeling

- **Internal:** grouped cross-validation on the 57 chats (grouped by chat). Report per-class precision, recall, F1, and macro-F1. Expect 900 to be rare: use class weights. With only 57 chats, use repeated grouped CV and report confidence intervals rather than a single split.
- **External:** run the Stage 1 model on PAN-12 test lines. Check that it fires more on the line-level predatory lines than on other lines. This validates Stage 1 using labels that already exist.
- **Ablation (the core research result):** text only vs. stage features vs. behavioral features vs. all, on the PAN-12 conversation task. This shows whether behavior adds signal beyond vocabulary.
- **Metrics for the conversation task:** PR-AUC, precision, recall, F-score at a threshold chosen from the precision-recall curve. Do not report accuracy alone (heavy class imbalance).
- **Explanations:** produce several worked example reports (stage timeline plus top contributing features) for the write-up.

## 6. Known pitfalls to check early

1. **Overlap/leakage** between the 57 chats and PAN-12.
2. **Source artifacts:** if negatives come from a different source than positives, the model may learn the source (formatting, usernames, topics) rather than behavior. Inspect top features for suspicious items. Compare results with and without a benign class.
3. **No negatives in the 57 chats:** the stage classifier only sees predatory conversations, so on its own it cannot separate predatory from benign text. Stage 2 and PAN-12 negatives handle that.
4. **Small sample (57 chats):** keep Stage 1 models regularized, use few hand-designed features rather than hundreds, and avoid complex sequence models that cannot be explained.
5. **Dataset-style bias:** PAN-12 reflects decoy-sting dynamics, not necessarily real-world chats. State this as a limitation.

## 7. Suggested order of work (confirm each step with the user)

1. Inspect the XML structure, write the parser, produce a flat table: `chat_id, idx, author, text, code, (timestamp)`.
2. Exploratory statistics: messages per chat, code distribution, stage order, who is coded.
3. Check overlap between the 57 chats and PAN-12.
4. Stage 1 baseline (TF-IDF + logistic regression) with grouped CV.
5. Add lexicon features and embeddings; compare.
6. Viterbi smoothing with the learned transition matrix.
7. External check of Stage 1 on PAN-12 test lines.
8. Stage 2 aggregated and behavioral features; train baseline and LightGBM on PAN-12.
9. SHAP explanations and worked example reports.
10. Ablation, PR curves, limitations section for the write-up.

## 8. How Copilot should behave in this project

- Start from this plan and the user's constraints; do not re-suggest hand labeling.
- Prefer small, reviewable steps. Explain the reasoning briefly, then ask before generating large amounts of code.
- Keep code reproducible: fixed random seeds, a single data-loading module, grouped splits everywhere, and results saved to files.
- Keep explanations central: every model choice should preserve the ability to say why a message or conversation was flagged.
- Never create or paste realistic grooming dialogue as test data. Use only the user's data, or neutral placeholder text for unit tests.

-ensure all code is in .ipynb type files.
-create seperate ipynb files for different parts of project unless they are related. If in doubt ask user.