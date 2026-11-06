# Support Ticket Classifier

**Release candidate; publication qualification is required.** The repository's
readiness report records the current result. Prepared marketing assets do not
imply Marketplace approval or production validation.

## Purpose and outputs

Assist support teams with ES/PT/EN ticket triage inside their AWS account.
Default intent labels: billing, technical_issue, account_access, cancellation,
shipping_delivery, refund, complaint, sales_inquiry, other. Urgency: low, medium,
high. Sentiment: negative, neutral, positive. Language: es, pt, en, unknown.

Intent, urgency and sentiment include a label, uncalibrated score, mode and ranked
distribution. Language includes a label and uncalibrated confidence.
`category` aliases `intent`; `top_k` contains its first three candidates. Optional
IDs are preserved. Review flags identify selected uncertain or unsupported
conditions, without guaranteeing correctness. See [usage](usage.md).

## Architecture and provenance

Frozen [multilingual-e5-small](https://huggingface.co/intfloat/multilingual-e5-small),
revision `614241f622f53c4eeff9890bdc4f31cfecc418b3`, MIT license. Its published
ONNX encoder uses masked mean pooling and L2 normalization. Tickets use `query: `;
experimental custom-label prompts use `passage: `. The encoder is not fine-tuned.
Maximum sequence length is 128 tokens including prefix and special tokens.

Linear softmax heads use frozen embeddings and thirteen lexical rates: four
urgency/sentiment cues and nine taxonomy cues. These features carry learned
weights; matching a word does not override a predicted label. Per-task
normalization and regularization are selected using the minimum source/language
development macro-F1. Intent also selects optional language-specific heads using
development cohorts; uncertain language detection uses the shared head.
The exporter records dataset pins, normalization and selection in heads.json.

Training combines original synthetic ES/PT/EN examples, Bitext English intent
examples (CDLA-Sharing-1.0) and SID synthetic multilingual intent examples (MIT).
An additional 1,080 AI-authored examples provide training and 270 separately
authored examples provide development coverage, balanced across ES/PT/EN. None
have human-reviewed labels. The frozen 2,000-case evaluation is excluded from
training and hyperparameter selection.
External datasets supply intent only; urgency and sentiment labels are never
inferred from their intent annotations. Raw external datasets are excluded from
the image. See [DATA.md](data.md) for splits, hashes, attribution and limitations.

Language detection uses the bundled Apache-2.0 Lingua 2.2.0 statistical language
models. ES/PT/EN are supported; French, German and Italian are comparison languages
for rejecting common unsupported input. The first 4,096 characters are examined;
ambiguous results become unknown. Other unsupported and mixed-language tickets
can still be misidentified and require review. Language confidence is uncalibrated.

The serving image uses pinned Alpine 3.24/musl packages, CPU ONNX Runtime 1.24.4,
NumPy 2.4.6 and hash-pinned tokenizers/Lingua wheels. A signed, hash-pinned SQLite
3.54.1 library backport fixes the older stable package's known issues. The full
package database, source commits and license inventory remain in the image.

## Evaluation and limitations

The release regression set has 81 synthetic tickets: 27 per language, with each
intent and each urgency/sentiment label present in every language. Translations
share scenarios, so they are not 81 independent real-world cases. The set was
frozen before candidate training; repeated candidate measurements make it a
regression check, not a blind final estimate. The earlier 36-ticket pilot remains
unchanged for historical comparison. External intent reports have a different
scope and must be reported separately.

Publication gates: intent macro-F1 >=0.85 separately in ES/PT/EN; urgency >=0.75;
sentiment >=0.85; language accuracy >=0.98; full label coverage; actual SageMaker
ml.c5.xlarge p95 <=150 ms on at least 100 samples for the same candidate and image.
Local Docker timings do not satisfy the AWS gate.

The expanded diagnostic contains 1,800 core cases and 200 challenge cases.
All received separate AI label annotation; none are human-reviewed. Five
ambiguous Portuguese core sentiment labels are excluded only from sentiment
scoring. Earlier failures informed model improvement, so repeated results must
not be described as blind or representative customer accuracy.

Review signals include short input, explicit different requested actions and
different intent predictions from up to four topic-bearing clauses.
The latter uses the same local encoder and selected heads, with no external
call. The signals are conservative heuristics and cannot detect every ambiguity;
absence of a flag does not make a result safe for unattended routing. Additional
clause scoring changes runtime cost and must be included in AWS qualification.

This evidence is insufficient to promise accuracy on a customer's traffic.
Test regional wording, ambiguity, negation, short and long tickets, multi-intent
messages and domain shifts. Scores are not calibrated probabilities. Do not use
this model for high-impact autonomous decisions. It does not redact personal data.

## Runtime, privacy and support

Weights are baked into the CPU container. No external service or download is
needed during inference. No product telemetry client is included. Enable
SageMaker network isolation. Buyers own their AWS logging, data capture, access
control and downstream storage settings. The server omits bodies and query
strings from its own logs, limits requests to 32 records/6 MiB, and signals busy
capacity with 429.

Code and original heads: Apache-2.0; encoder: MIT. Bundled notices are under
`licenses/`. Software rates in model.yaml remain a commercial proposal, separate
from AWS charges; trial and pricing must be confirmed in the final offer.
Support: info@softwaresushi.com; no response-time SLA is promised.
