# OMF Auto Loans - Open-Source Dataset Research for Image Quality and Tampering Detection

Date: 2026-06-05
Scope: Open-source or publicly reachable datasets aligned to
- Text/value manipulation (edited amounts, overwritten fields)
- Image tampering (cut-paste regions, overlays, inconsistent fonts)
- Preference for English/US-style financial and verification documents

## 1) Executive Summary

Best immediate fit for your objectives:
1. DocTamper (document text tampering, region-level localization, strong baseline ecosystem)
2. OSTF + TFR/TextShield (open-set tampered text, generative-AI era focus)
3. FantasyID (ID-document manipulation benchmark with commercial-use claim in paper abstract)
4. AIForge-Doc and AIForge-Doc v2 (latest AI-forged financial/form tampering benchmark papers; very relevant to edited amounts)
5. RVL-CDIP + FUNSD + CORD (not forgery datasets by default, but strong authentic document corpora for negative class and synthetic fraud generation)

Most practical OMF strategy:
- Use one tampering-focused dataset for detector pretraining (DocTamper/OSTF/FantasyID)
- Use one financial/form corpus for domain adaptation (RVL-CDIP + receipts/forms)
- Add OMF-specific synthetic tampering generation on pay stubs, bank statements, and titles (critical for US auto-loan relevance)

## 2) Dataset Shortlist (Prioritized)

## Tier A - Directly aligned to tampered documents

| Dataset | Alignment to OMF objectives | English/US fit | Access and license notes | Why it matters |
|---|---|---|---|---|
| DocTamper (CVPR 2023) | Excellent for tampered text localization in document images | Mixed global docs, not US-specific | Repo says non-commercial and academic-request flow; Kaggle mirror exists | Strong baseline for edited values and local text manipulation |
| OSTF (AAAI 2025) | Strong for open-set tampered scene/document text in generative AI era | Mixed | Public links listed; repository license CC-BY-NC-4.0 | Helps robustness against unseen edit tools |
| TFR / TextShield benchmark (AAAI 2026 repo) | Focuses on reasoning about real vs generated vs tampered text | Mixed | Benchmark distributed via request flow; repo under CC-BY-NC-4.0 | Useful if you want VLM-based forensic reasoning |
| FantasyID (IJCB 2025 paper) | Directly targets ID-document digital manipulations | Good for KYC-like pipelines; not auto-loan-specific docs | Paper says publicly available including commercial use (verify on project page/download terms) | Very relevant for identity-doc checks in loan onboarding |
| AIForge-Doc (2026) | Financial/form docs forged with AI inpainting, pixel masks | Includes receipt/form docs across languages | Paper claims release; verify repository and terms | Closely matches edited amount/value attack pattern |
| AIForge-Doc v2 (2026) | GPT-Image-2 forged documents with pixel-precise masks | Financial receipt/form framing | Paper states release of dataset/pipeline | Captures latest AI-generated tampering risk |
| DOCFORGE-BENCH (2026) | Zero-shot benchmark across 8 document forgery datasets | Mixed | Benchmark paper; check code/data links for each component dataset | Useful for realistic out-of-box benchmarking and threshold calibration |

## Tier B - Useful for broad image tampering pretraining

| Dataset | Alignment | English/US fit | Access notes | Caveat |
|---|---|---|---|---|
| CASIA v1/v2 (common mirrors on Kaggle) | Classic copy-move/splicing tasks | Not document-specific | Easily downloadable mirrors exist | Kaggle mirrors often have unknown/inconsistent licensing metadata |
| Columbia Image Splicing | Canonical splicing detection | Not document-specific | Publicly mirrored in multiple places | Small and old; use only as auxiliary |
| CocoGlide (linked from TruFor) | Tampering localization with masks | Not document-specific | Linked from TruFor repo | Good for robustness, weaker domain fit |

## Tier C - Authentic document corpora to create OMF-style negatives and synthetic positives

| Dataset | Primary use in OMF pipeline | English/US fit | Notes |
|---|---|---|---|
| RVL-CDIP (400k docs, 16 classes) | Authentic document source, layout diversity, hard negatives | Strong English coverage | Derived from IIT-CDIP/Legacy Tobacco library; verify downstream commercial constraints |
| FUNSD | Form understanding and key-value region grounding | English forms | Small but useful for field-level manipulation simulation |
| CORD v2 | Receipt field/value structures for amount-tampering tests | Mostly receipts, not US-only | Useful for receipt amount manipulation experiments |
| WildReceipt / SROIE / XFUND | Additional receipt/form variety | Mixed multilingual | Strong for stress-testing OCR/value consistency logic |

## 3) Loan-Verification Relevance Assessment

No major open dataset is a perfect US auto-loan fraud benchmark. The current best path is a hybrid:
1. Start with document-forgery datasets for localization/model priors.
2. Add financial/form corpora for value-field realism.
3. Generate OMF-specific synthetic attacks for pay stubs, bank statements, proof-of-address, vehicle title/registration.

Recommended synthetic attack catalog for OMF:
- Amount overwrite (gross pay, net pay, balance)
- Date edits (pay period, statement cycle)
- Employer/payor name substitution
- Region copy-paste from same doc and cross-doc
- Overlay patch insertion (logos, signature blocks, account numbers)
- Font-family/kerning mismatch injection
- Recompression and screenshot-of-document artifacts

## 4) Dataset Access Risk Register (Important)

Before production use, confirm for each dataset:
1. Commercial-use allowance (especially for DocTamper/OSTF/TFR which are often non-commercial).
2. Redistribution rights (model weights trained on restricted data).
3. PII/legal constraints (ID-like datasets and financial documents).
4. Geographic/data sovereignty requirements.

A practical approach:
- R&D benchmark phase can use non-commercial datasets.
- Production training should migrate to: internally generated/synthetic data + partner-cleared datasets + licensed data streams.

## 5) Recommended Starter Pack for OMF (60-90 day research track)

Use this bundle first:
1. DocTamper (or FantasyID if access and licensing clears faster)
2. RVL-CDIP (authentic negatives and document diversity)
3. FUNSD/CORD subset for field-level manipulations
4. Internal synthetic OMF set (pay stubs, statements, title docs)

Target split:
- 40% public tampering datasets
- 20% public authentic financial/form docs
- 40% OMF synthetic scenario data

## 6) Sources (deep web research)

Core dataset and benchmark sources:
- https://github.com/qcf-568/DocTamper
- https://www.kaggle.com/datasets/dinmkeljiame/doctamper
- https://arxiv.org/abs/2407.21422
- https://github.com/qcf-568/OSTF
- https://github.com/ant-research/TextShield
- https://arxiv.org/abs/2507.20808
- https://www.idiap.ch/paper/fantasyid
- https://arxiv.org/abs/2602.20569
- https://arxiv.org/abs/2604.25213
- https://arxiv.org/abs/2603.01433
- https://arxiv.org/abs/2508.11021

General image-forensics benchmarks/code:
- https://github.com/grip-unina/TruFor
- https://github.com/scu-zjz/IMDLBenCo
- https://arxiv.org/abs/2505.11003

Document corpora:
- https://huggingface.co/datasets/aharley/rvl_cdip
- https://www.cs.cmu.edu/~aharley/rvl-cdip/
- https://huggingface.co/datasets/nielsr/funsd
- https://huggingface.co/datasets/naver-clova-ix/cord-v2

Industry practice references:
- https://www.veriff.com/fraud/learn/document-fraud-adapting-to-evolving-fraud-tactics
- https://www.entrust.com/blog/2024/identity-fraud-report

Notes:
- Some pages had anti-bot/cookie gates; where needed, claims were taken from paper abstracts/repository READMEs directly.
- For any dataset without explicit downloadable terms in this pass, treat as provisional until legal/data-governance review is complete.
