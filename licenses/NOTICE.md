# Third-party notices

- Software Sushi code, lexical features, original synthetic examples and learned
  heads are distributed under Apache-2.0 (`Apache-2.0.txt`).
- The frozen multilingual-e5-small encoder and tokenizer are from Microsoft /
  intfloat, revision `614241f622f53c4eeff9890bdc4f31cfecc418b3`.
  The pinned model card declares MIT. `MIT-unilm.txt` preserves Microsoft's
  upstream license; `multilingual-e5-small-README.md` preserves the model card.
  Source: https://huggingface.co/intfloat/multilingual-e5-small
- Bitext customer-support v11 data, by Bitext, is used for intent training and
  evaluation at revision `430d1a89bd93bd1fa23c16f29dd53e73f0087443`.
  License: CDLA-Sharing-1.0 (`CDLA-Sharing-1.0.txt`). No raw dataset is included
  in the image. Section 3.5 addresses results of computational use; do not apply
  the code license to the upstream dataset or redistribute it without its own
  notices and license obligations.
  Source: https://huggingface.co/datasets/bitext/Bitext-customer-support-llm-chatbot-training-dataset
- SID (Synthetic Intent Dataset), Luigicfilho (2026), revision
  `43fad0f7d60bd1dfb0402c8632deb2cbc13f4731`, is used for intent training and
  evaluation. Its pinned dataset card declares MIT (`SID-dataset-card.md`).
  Raw SID data is not included in the image. Its author describes template
  generation, typo augmentation and entity resampling; do not call these
  customer-authored production tickets.
  Source: https://huggingface.co/datasets/luigicfilho/sid

- Lingua language identification, version 2.2.0, is bundled under Apache-2.0.
  Its upstream license is preserved in `Lingua-LICENSE` and the installed wheel.
  Source: https://github.com/pemistahl/lingua-py

Alpine system packages retain their own licenses, package checksums and source
commits in `/opt/program/runtime-inventory.json` and the APK database. The
SQLite library uses Alpine's signed 3.54.1 backport (source commit
`f8081b25590214e2695a4faa77c7892bd902a4e8`); other packages use the stable repository.
Corresponding packaging/source recipes are available in Alpine's aports repository
at each recorded commit. Apache-2.0 for our code does not relicense OS packages.

Python package distributions retain their own licenses and notices in the
container's installed `.dist-info` directories. The runtime locks record exact
package versions and distribution hashes. This file does not relicense
third-party software or data.
