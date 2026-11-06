# Buyer guide

Use the subscribed model package in your AWS account. The included
[sample notebook](sample-notebook.ipynb) provides synthetic ES/PT/EN examples,
real-time inference, batch transform and cleanup. Configure the subscribed
package ARN, execution role and an existing private S3 bucket in the same region.
No external dataset is required.

## Deployment

Use the qualified CPU instance `ml.c5.xlarge`.
Enable SageMaker network isolation. The container has all weights and does not
need outbound network access. Serverless inference is not supported.

The container listens on `SAGEMAKER_BIND_TO_PORT` (default 8080): `GET /ping`,
`POST /invocations`, and `GET /execution-parameters`. Readiness is reported only
after weights load and warmup completes. Batch configuration is `SingleRecord`,
`SplitType=Line`, up to 6 MiB; the server admits two inference calls concurrently.

## Request and result

`Content-Type: application/json`

```json
{"id":"T-1","subject":"Cobro duplicado","body":"Me cobraron dos veces. Revisen la factura hoy."}
```

Alternatively send `{"text":"..."}`. Explicit `text` takes precedence over
`subject` and `body`. JSON and JSON Lines preserve string ticket IDs.
Plain text is one ticket. CSV uses the `text` column, or the first column without
a header. JSON arrays and `{"instances":[...]}` return `{"predictions":[...]}`;
JSON Lines returns one prediction per line.

| Result field | Meaning |
| --- | --- |
| `category`, `intent` | Same predicted category, score, mode and ranked distribution |
| `top_k` | Top three category candidates (fewer for restricted label sets) |
| `urgency` | low, medium or high |
| `sentiment` | negative, neutral or positive |
| `language` | es, pt, en or unknown; uncalibrated Lingua language confidence |
| `needs_review`, `review_reasons` | Review heuristic for uncertain category, language, truncation or custom labels |
| `truncated` | Whether the tokenizer discarded tokens beyond its 128-token window |
| `model`, `version`, `model_revision` | Model identity and fingerprint for reproducible deployment checks |

Scores are **not calibrated probabilities**. A false review flag does not
mean the prediction is correct. Monitor and validate on your own ticket mix.

## Limits and errors

Requests are UTF-8, at most 6 MiB and 32 records. Each ticket is at most 100,000
characters; blank text is rejected. The encoder uses the first 128 tokenizer
tokens, including its prefix and special tokens. Important context after that
window may be lost. Regional slang, mixed languages, multiple intents and
unusual domains need additional validation.

| HTTP status | Action |
| --- | --- |
| 400 | Correct malformed input, blank text, labels or record limits |
| 408 | Retry a timed-out body upload |
| 413 | Reduce the payload below 6 MiB |
| 415 | Use a supported content type |
| 429 | Retry with exponential backoff and jitter; reduce concurrency |
| 500 | Check readiness and deployment health; contact support if persistent |

The server does not log ticket bodies or query strings. Buyers control AWS
access logs, data capture and downstream retention; review those settings.

## Custom labels (experimental)

```json
{"text":"The app crashes.","labels":{"intent":["wifi outage","picnic planning"]}}
```

Known subsets use `finetuned_subset`; novel labels use `zero_shot`. Default
labels use `finetuned`. Category overrides trigger review. Quality targets
apply only to the default label sets. Do not interpret restricted-set scores
as comparable with full-taxonomy scores.

## Costs, cleanup and support

AWS infrastructure charges are separate from software fees. An endpoint is
billed until deleted. The notebook deletes its endpoint, endpoint configuration
and models, and includes a separate cell for its two S3 objects. If execution
is interrupted, inspect the printed resource names in SageMaker.

Support: **info@softwaresushi.com**. Include version, model revision, instance
type, request ID and a description of the issue. Do not email ticket contents,
credentials or private customer information. No response-time SLA is advertised.
