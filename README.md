# Support Ticket Classifier

Review-assisted support-ticket triage in English, Spanish and Portuguese, running in your AWS account.

The CPU container returns intent, urgency, sentiment, language, ranked alternatives and review flags. It supports SageMaker real-time endpoints and batch transform. Weights are bundled and inference requires no external API or runtime download.

## Get started

1. Subscribe to the Software Sushi product in AWS Marketplace.
2. Open [the sample notebook](sample-notebook.ipynb) and supply your subscribed model package, SageMaker execution role and private S3 bucket.
3. Run the synthetic examples, then validate representative tickets from your own workflow.
4. Delete test endpoints when finished; the notebook includes cleanup steps.

Supported instance: `ml.c5.xlarge`. Software price: USD 0.70 per instance-hour, with a 14-day software trial. AWS infrastructure charges are separate, including during the trial.

## Documentation and demonstration

- [Buyer guide](usage.md)
- [Model card](model-card.md)
- [Data and licensing](data.md)
- [Measured quality and performance](quality-summary.json)
- [Examples by intent](samples/)
- [Demo with English narration](https://cdn.softwaresushi.com/marketplace/support-ticket-classifier/1.0.0/)

The demo inbox illustrates a possible integration; it is not a hosted ticketing application included with the model.

## Evaluation limits

The expanded evaluation contains 2,000 synthetic AI-labeled cases, with separate AI annotation and no human review. Its earlier failures informed improvements with independent training/development data; repeated results are diagnostic, not a blind estimate of customer accuracy.

Scores are uncalibrated. Review flags miss some ambiguous and short/noisy requests, so an unflagged prediction can still be wrong. Keep human review and validate your own traffic before automating routing. The model does not redact personal data.

These client examples contain no model weights, training data, credentials or private customer tickets. Code/examples are Apache-2.0; bundled notices describe third-party components. Support: info@softwaresushi.com.
