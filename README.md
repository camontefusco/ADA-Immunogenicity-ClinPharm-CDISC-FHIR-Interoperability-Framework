# DMPK Standards & Interoperability Demo

A **standards-aware demo** showing how ADA assay results and method metadata flow into **CDISC-like** and **FHIR** artifacts.  
Consumes outputs from [`ada-panda-mini`](https://github.com/camontefusco/ada-panda-mini).

## 🎯 Purpose
- **Audience:** data standards groups, QA, interoperability experts.
- **Inputs:**  
  - `ada-panda-mini/reports/tlgs.parquet` (metrics: incidence, bias, attainment)  
  - `ada-panda-mini/reports/benchmarks.parquet` (PASS/ALERT)  
- **Outputs:**  
  - `cdisc/adam_like/adimmun.csv` (ADaM-ish immunogenicity dataset)  
  - `interoperability/fhir_examples/*.json` (FHIR Observation resources)  
  - `validations/summary.md` (checks)

## 📂 Structure

```bash
dmpk-standards-interoperability/
├─ notebooks/
│  ├─ 03_ingest_ada_from_panda_mini.ipynb
│  └─ validations/checks.ipynb
├─ cdisc/adam_like/
├─ interoperability/fhir_examples/
├─ config.yaml
├─ env.yml
├─ Makefile
└─ README.md
```
ADaM-like dataset spec (minimal)

cdisc/adam_like/adimmun.csv columns:

USUBJID – subject id

VISITNUM, VISIT – visit number/label

ADADTC – ADA collection date (derive from day; use ISO date placeholder)

PARAMCD – e.g., ADAFL (ADA positive flag), ADATITR (titer, if simulated)

AVAL – numeric value (e.g., titer); AVALC – categorical (e.g., “POS”/“NEG”)

METHOD – Standard or PandA

TOLFLAG – PASS/ALERT from benchmarks (merge by METHOD)

COMMENT – optional free text (“Recovery ≥80% up to 200 µg/mL (sim)”)

## FHIR Observation examples

Create one JSON per method & visit with method metadata. Minimal, valid structure:

interoperability/fhir_examples/Observation_ADA_PandA_Week36.json
```json
{
  "resourceType": "Observation",
  "status": "final",
  "code": { "text": "Anti-drug antibody (ADA) result" },
  "effectiveDateTime": "2025-01-01",
  "valueCodeableConcept": { "text": "ADA incidence 40%" },
  "method": { "text": "PandA (PEG precipitation + Acid dissociation)" },
  "interpretation": [{ "text": "Drug tolerance PASS (≥80% to 1000 µg/mL)" }],
  "note": [{ "text": "Derived from ada-panda-mini simulated cohort; literature anchored." }]
}
```
