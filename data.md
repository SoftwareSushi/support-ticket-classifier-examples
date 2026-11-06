# Data and artifact provenance

Data provenance for Support Ticket Classifier 1.0.0.
No private customer ticket collection is used. External sources describe synthetic
or hybrid synthetic examples. Do not market them as representative production traffic.

## Current training and development

The exporter trains frozen-encoder softmax heads. It selects regularization and
normalization on development data. Intent uses the minimum macro-F1 across each
source/language cohort (over that cohort's supported labels); the expanded
candidate uses this criterion for all tasks. Optional language-specific intent
heads are selected on the corresponding development cohorts. Training weights balance
source × language × class. This prevents a large, easy source from hiding a weak
cohort. No release-test labels enter optimization or hyperparameter selection.

| Source | Train rows | Development rows | Labels used |
| --- | ---: | ---: | --- |
| Original `export/corpus_v2.py` | 4,374 | 486 | intent, urgency, sentiment |
| Bitext v11 | 2,640 | 440 | intent only; English, eight mapped classes |
| SID | 5,880 | 1,435 | intent only; ES/PT/EN, nine mapped classes |
| Natural AI-authored supplement | 1,080 | 270 | intent, urgency, sentiment; ES/PT/EN |
| Total | 13,974 | 2,631 | external datasets still supply intent only |

The supplement contains 360 training and 90 development cases per language,
authored by `gpt-6.1-sol`, with `human_reviewed=false`. Texts and scenario-family
identifiers are disjoint across the two splits. Import checks compared them with
the frozen evaluation, prior regression data and all 218,037 cached external
rows, finding zero normalized text overlaps. This is lexical evidence, not proof
of semantic independence. The source receipts and exact hashes remain in
`artifacts/natural-training-v4/data-manifest.json` and `provenance/`.

The 2,000-case expanded evaluation remains frozen at SHA-256
`212087768d1bd53d965ac769f450a0f3bcf2df9f17cafd679ab4c96c9fadc677`.
Its original failures informed remediation but no evaluation text or label is
included in training or hyperparameter selection. Repeated scoring is diagnostic.

`export/train_release.py` provides the expanded-data recipe. It compares the
original four lexical features with all thirteen for each task, then selects
optional language-specific intent heads using development data. It requires
the separate supplement and frozen evaluation exclusion file, and refuses to
overwrite the packaged heads or an existing experiment output. Record its
outputs before running a new qualification; retraining is not part of image builds.

Original examples combine 81 training topic strings (27 per language) with
urgency/sentiment modifiers and sentence orders. Development uses 27 different
topic strings and separate modifier variants. Combination count is not the
number of independent support scenarios. Corpus and export code are Apache-2.0.

Bitext source: [customer-support LLM dataset](https://huggingface.co/datasets/bitext/Bitext-customer-support-llm-chatbot-training-dataset),
revision `430d1a89bd93bd1fa23c16f29dd53e73f0087443`, file
`Bitext_Sample_Customer_Support_Training_Dataset_27K_responses-v11.csv`, SHA-256
`6f81102b0100b97b8468eb04368033a23206bf1fde9d53500d5806ec1001a434`.
License: CDLA-Sharing-1.0. Only instruction text and mapped intent labels are used.
Whitespace/case-normalized text hashes assign groups to train/dev/test (80/10/10)
before hash-sorted per-intent caps of 120/20/20. Conflicting mapped labels are
excluded. Ambiguous intents are omitted; this source has no technical_issue class.

SID source: [Synthetic Intent Dataset](https://huggingface.co/datasets/luigicfilho/sid),
Luigicfilho (2026), revision `43fad0f7d60bd1dfb0402c8632deb2cbc13f4731`, declared
MIT license. Preserve upstream train/validation/test; normalized exact duplicate
groups prefer test, then development, then training. Conflicting mapped labels
are excluded. Use ES/PT/EN, explicit taxonomy-compatible intents, at most 40
training and 10 evaluation examples per upstream intent/language. Standalone
emotions and politeness are not mapped to complaint or other. Augmented variants
may share underlying templates across splits, so exact deduplication does not
establish independent scenario families. The original model selection did not use `validation_real`. A later diagnostic
evaluation of that split identified weaknesses in the original heuristic language
detector, which was replaced with bundled Lingua models. This source is now a
diagnostic regression dataset, not an untouched final estimate.

```text
e78b6c331e4c254d5504b9de671284f88cb7e1d0dce8a4502c7686e9d59cc1eb  SID train.csv
12790954dc9fe9153181fd476c8811301a97761d30117987f6cef6a79b290ce7  SID val.csv
ca5b100fd9828b1aed7de032d997b1203bc7e07e8f6594dab3498763b4a4a157  SID test.csv
```

The current selected train and development texts have zero normalized exact
overlaps. External test reporting removes exact overlaps with *all* training and
development sources. Raw external data and feature caches remain under ignored
`.foundry/` directories and are excluded from the image. Downloaders verify
pinned SHA-256 values. Never invent missing urgency/sentiment labels.
See `licenses/NOTICE.md` for attribution and upstream licensing scope.

## Frozen release regression

`tests/eval/release-v2.jsonl` contains 81 synthetic tickets, 27 per language,
with three examples per intent and all urgency/sentiment labels in each language.
The same 27 scenarios are translated across languages; this is not 81 independent
customer cases. It was frozen before training, has zero normalized exact overlap
with current train/dev, and is now a repeatedly measured regression set. A future
production accuracy claim needs a new blind, representative evaluation.

```text
b4040ef2356690c41196b1a1d806c77afdeefc26e3a91ee39accc3aab19e0b77  tests/eval/release-v2.jsonl
```

Keep its checksum and labels unchanged. Investigate model changes on development
data; do not tune thresholds or rewrite test labels to obtain a pass.
`benchmark/evaluate_external.py` separately measures held-out mapped intent
splits. Those results do not validate urgency or sentiment. SID remains synthetic,
and Bitext's mapped taxonomy omits technical_issue.

## Additional public-source diagnostic

`benchmark/evaluate_fresh.py` evaluates the pinned SID `val_real.csv`, SHA-256
`a1d4d950ace27c2669f414ee24d4e89f756951044f5c4b3e783ef146ec41d5c8`.
The upstream card calls it human-verified real-world evaluation; that claim was
not independently audited. Keep only mapped ES/PT/EN intents; remove exact
train/dev overlap, previous evaluation overlap, duplicate texts and contradictory
intent/language annotations. Raw text stays in ignored local cache; reports
contain aggregates only. It has no urgency or sentiment ground truth.

The first run exposed weak word-list language identification. Comparing the
replacement against the same corpus is diagnostic improvement evidence, not a
new blind production estimate. No intent heads or frozen test labels were changed
to obtain this result. Intent performance in Portuguese remains weaker; keep
human review and validate on representative buyer traffic before automation.

## Encoder and reproducibility

Frozen `intfloat/multilingual-e5-small`, revision
`614241f622f53c4eeff9890bdc4f31cfecc418b3`; upstream model card declares MIT.
The downloader and Docker build verify these published LFS SHA-256 values:

```text
ca456c06b3a9505ddfd9131408916dd79290368331e7d76bb621f1cba6bc8665  model/encoder.onnx
0b44a9d7b51c3c62626640cda0e2c2f70fdacdc25bbbd68038369d14ebdf4c39  model/tokenizer.json
```

`--download-only` preserves heads. Retraining deliberately replaces heads.json;
review separately. Install `export/requirements.txt` for the SciPy optimizer.
Heads record source pins, normalization, selection and corpus checksum. A runtime
`model_revision` fingerprints all Python runtime sources, metadata and heads.
The ECR image digest additionally identifies the complete deployed image.

The serving image uses `runtime-apk.lock` for the complete Alpine 3.24 package
closure and `runtime-wheels.lock` for the Rust tokenizer and Lingua wheels.
The latter are installed without unused Hugging Face Hub/network clients.
SQLite 3.54.1 is a signed, SHA-256-pinned Alpine library backport; the rest of
the OS graph remains pinned to stable. The Docker base is pinned by digest.
`runtime/verify.py` checks native versions, the SQLite source ID/FTS5 regression,
package closure and CPU provider, then records source commits and license terms.
Native development/export keeps separate Python 3.12 requirements/runtime locks.

## Publication gates

`make qualify MODEL=support-ticket-classifier` checks intent macro-F1 >=0.85
**each** ES/PT/EN, urgency >=0.75, sentiment >=0.85, language accuracy >=0.98,
and every label in every language. The release workflow and `publish --submit`
with `Intent=APPLY` honor the result. Development `test` is not certification.

An actual SageMaker ml.c5.xlarge report is mandatory: at least 100 measured
requests, p95 <=150 ms, the same model version/revision, image digest and network
isolation. Run `benchmark/sagemaker_endpoint.py` against an explicitly selected,
already provisioned endpoint; it never provisions resources. Local timings do
not qualify. A report is evidence to review, not a signed AWS attestation.

Before submission, pin `FOUNDRY_IMAGE_URI` to the tested ECR `@sha256:...` digest.
The qualifier compares that digest with the report, and submission verifies that
the completed, Marketplace-certified SageMaker package uses the same image.

## Historical pilot (retained, no longer the release dataset)

Original `export/corpus.py`: 114 original tickets. Original
`tests/eval/dataset.jsonl`: 36 separately written synthetic tickets with incomplete
PT high-urgency / EN positive-sentiment coverage. Historical artifact hashes:

```text
fc604d6e3ad0f9717d2efc88c4ce8ad7127730c8c5da2579173011571c0302fd  export/corpus.py
901090778c0c60429a303fc22fc88a55f44064aa1e05e8be92dffdd8c00f3ec7  tests/eval/dataset.jsonl
d261e5379a4363c6f1c2abbac9306b33a9e39a9c679a087e0a4aabb65b29b20a  historical model/heads.json
```
