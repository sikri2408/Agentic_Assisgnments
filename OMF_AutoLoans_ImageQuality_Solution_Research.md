# OMF Auto Loans - Image Quality and Tampering Detection Solution Research

Date: 2026-06-05
Focus: Buildable solution options for
- Text/value manipulation (edited amounts, overwritten fields)
- Image tampering (cut-paste, overlays, inconsistent fonts)
- Practical choices across Python libraries, DL models, and VLM-based systems

## 1) What latest research says (2025-2026 trend)

## Key findings from recent papers

1. AI-forged document edits are now hard for both humans and classic detectors.
- AIForge-Doc and AIForge-Doc v2 show strong performance drop for older detectors on AI inpainting-based edits.
- Reported examples include near-chance behavior in some VLM judge settings on forged financial/form docs.

2. Zero-shot deployment is mostly a calibration problem, not only a model problem.
- DOCFORGE-BENCH reports moderate ranking performance (AUC) but poor fixed-threshold F1 due to tiny tampered regions.
- Small domain threshold calibration sets can recover substantial performance without full retraining.

3. Open-set robustness matters more than in-distribution SOTA claims.
- OSTF work emphasizes unseen forgery types and proposes training that improves open-set performance.

Implication for OMF:
- Do not rely on a single detector or one fixed threshold.
- Use a multi-signal scoring pipeline with calibration per document type.

## 2) Industry patterns (how teams are handling fraud in practice)

Common production pattern (from KYC/fraud providers and recent practice writeups):
1. Aggregate many weak signals instead of a single binary model.
2. Combine visual forensics + OCR consistency + metadata + cross-document business rules.
3. Keep human-in-the-loop for ambiguous/high-risk cases.
4. Continuously retrain/recalibrate using newly observed fraud patterns.

Example signal families seen in production discussion:
- Template geometry and border consistency
- Font and character consistency
- Barcode/MRZ/serial consistency checks
- Signature/portrait/region anomaly embedding matches
- Cross-session and cross-user pattern linking

## 3) OMF-target architecture (recommended)

## A. Input quality gate (reject bad captures early)
- Blur and sharpness: variance of Laplacian
- Resolution and effective DPI check
- Skew and perspective distortion
- Lighting and glare detection
- Compression quality and screenshot recapture detection

Python stack:
- OpenCV, Pillow, scikit-image

## B. Tamper candidate generation (cheap, broad)
- ELA-like residual map (JPEG only as a heuristic)
- Noiseprint/residual inconsistency maps
- Copy-move candidate blocks
- Edge and text-line anomaly segmentation

Python stack:
- OpenCV, NumPy, scikit-image, PyWavelets

## C. OCR and field extraction
- Detect key fields by doc type: amount, date, employer, account number, VIN, address
- Extract with confidence and bounding boxes

Python stack:
- PaddleOCR or Tesseract + layout parsers
- Optional: docTR, LayoutLM family for structured parsing

## D. Value integrity checks (high ROI for lending)
- Arithmetic checks: gross - deductions = net
- Date consistency checks across fields
- Amount consistency across repeated mentions
- Name/address consistency across submitted docs
- Currency symbol and decimal formatting sanity checks

## E. Learned tamper localization model
- Start with DocTamper/OSTF-style pretrained models
- Fine-tune on OMF synthetic attacks
- Output: tamper mask + confidence + uncertainty map

Candidate model families:
- TruFor-style fusion (RGB + forensic traces)
- Document-specific tamper detectors (DocTamper/OSTF ecosystem)
- Unified benchmark integrations via IMDLBenCo / ForensicHub-style frameworks

## F. VLM forensic reasoner (secondary judge, not sole judge)
- Ask model to explain suspicious region and inconsistency
- Use for triage and analyst evidence, not final autonomous approval
- Prompt with extracted OCR snippets + bounding boxes + image crops

Candidate models:
- Qwen2.5-VL class models, GPT-4o class APIs, Gemini multimodal APIs
- Keep deterministic and audited prompting templates

## G. Decision layer and calibration
- Ensemble score = weighted sum of quality, visual tamper, OCR confidence, rule consistency, model uncertainty
- Thresholds per doc type (pay stub vs bank statement vs title)
- Separate auto-approve, auto-reject, manual-review zones

## 4) Three implementable solution tracks

## Track 1 - Python-first baseline (fastest)
Goal: 2-4 weeks MVP
- Quality gate + OCR + rules + classical forensic heuristics
- No heavy GPU requirement

Pros:
- Fast to build and explain
- Easy governance

Cons:
- Lower recall on modern AI edits

## Track 2 - Hybrid DL for localization (recommended default)
Goal: 6-10 weeks
- Track 1 plus a tamper localization model fine-tuned on synthetic OMF edits
- Add per-document threshold calibration workflow

Pros:
- Better fraud recall with controlled false positives
- Good balance of cost and accuracy

Cons:
- Needs training pipeline and MLOps support

## Track 3 - Hybrid + VLM forensic co-pilot (advanced)
Goal: 10-14 weeks
- Track 2 plus VLM reasoner for hard cases and analyst workflow
- Produce human-readable evidence traces

Pros:
- Better handling of novel manipulations
- Improves analyst productivity and explainability

Cons:
- Prompt/security hardening required
- VLM hallucination risk if not constrained

## 5) Metrics and evaluation protocol (must-have)

Use document-level and pixel-level metrics:
- Image-level: AUC, PR-AUC, recall at fixed FPR
- Localization: IoU, pixel-F1, calibrated IoU-F1
- Business impact: fraud capture rate, false decline rate, manual review rate

Calibration recommendations:
1. Maintain small calibration sets per document type (10-50 images minimum per threshold refresh cycle).
2. Tune thresholds separately for synthetic and real-world fraud queues.
3. Monitor drift weekly (OCR confidence drift, score distribution drift, capture-device drift).

## 6) OMF-specific threat scenarios to include in testing

1. Paystub amount inflation and YTD tampering
2. Bank statement running-balance edits
3. Utility bill address edits (apartment/unit changes)
4. Vehicle title VIN character swaps
5. Overlay patches hiding adverse transactions
6. AI inpainting of digits while preserving surrounding texture
7. Re-encoded screenshots of edited PDFs

## 7) Suggested technical stack (Python + VLM)

Core Python:
- opencv-python
- pillow
- numpy
- scikit-image
- pandas
- pydantic

OCR/layout:
- paddleocr (or tesseract fallback)
- layoutparser / docTR (optional)

Modeling:
- torch
- segmentation-models-pytorch (optional)
- timm

VLM integration:
- provider SDK (OpenAI/Gemini/others)
- strict JSON schema outputs for forensic reasoning

MLOps:
- mlflow or weights-and-biases
- great-expectations for data checks
- evidential logging for each decision

## 8) 12-week execution blueprint

Week 1-2:
- Data inventory and legal/license screening
- Build quality-gate and OCR-rule baseline

Week 3-4:
- Synthetic tampering pipeline for OMF doc templates
- Baseline metrics and risk thresholds

Week 5-8:
- Train/fine-tune localization model
- Add uncertainty and calibration module

Week 9-10:
- Add VLM-assisted forensic explanation service
- Human-review console fields and evidence views

Week 11-12:
- UAT with fraud ops
- Threshold hardening, drift monitors, rollout plan

## 9) Research sources used

Recent papers and benchmarks:
- https://arxiv.org/abs/2602.20569
- https://arxiv.org/abs/2604.25213
- https://arxiv.org/abs/2603.01433
- https://arxiv.org/abs/2508.11021
- https://arxiv.org/abs/2407.21422
- https://arxiv.org/abs/2505.11003
- https://arxiv.org/abs/2402.13545

Codebases and dataset hubs:
- https://github.com/qcf-568/DocTamper
- https://github.com/qcf-568/OSTF
- https://github.com/ant-research/TextShield
- https://github.com/grip-unina/TruFor
- https://github.com/scu-zjz/IMDLBenCo

Industry references:
- https://www.veriff.com/fraud/learn/document-fraud-adapting-to-evolving-fraud-tactics
- https://www.entrust.com/blog/2024/identity-fraud-report

## 10) Final recommendation for OMF

Adopt Track 2 as primary path, with Track 3 features phased in:
1. Build a robust rule-plus-forensics baseline quickly.
2. Add tamper localization model trained on OMF-like synthetic edits.
3. Use VLM as analyst-assist and explanation layer, not sole decision maker.
4. Institutionalize per-doc-type threshold calibration and weekly drift monitoring.

This gives the best trade-off between speed, governance, and fraud-capture performance for auto-loan document verification.
