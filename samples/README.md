# Synthetic input and actual output samples

Each intent has an input JSON Lines file containing one Spanish, Portuguese and
English example, and a matching output file in the same order. Use SingleRecord
batch transform or send one input object to a real-time endpoint. These are
original Software Sushi synthetic examples, licensed Apache-2.0. No customer or
external dataset text is included.

Outputs are actual responses from the qualified SageMaker image, rather than
gold answers. The first original regression example for each language and gold
intent was selected without filtering for successful predictions. Predictions
may disagree with the intended category. See provenance.json for that comparison,
model identity and source-evidence checksum. Scores are uncalibrated.
