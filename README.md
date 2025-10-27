# 🧮 DMPK Immunogenicity Standards Interoperability

**Goal:** Demonstrate how ADA assay results — including bioanalytical method metadata (Standard vs PandA) — flow into **regulatory data standards**:

✅ CDISC ADaM-like format  
✅ FHIR interoperable clinical data objects  
✅ Automated validation checks

This repo bridges **bioanalysis → clinical pharmacology → regulatory informatics**.

---

## 🔗 Upstream Data Source

This project consumes outputs from:

📁 Repo: `ada-panda-mini`  
Artifacts needed:

| File | Source | Purpose |
|------|--------|---------|
| `tlgs.parquet` | /reports from ada-panda-mini | PK + ADA E/R summaries |
| `benchmarks.parquet` | /reports from ada-panda-mini | Drug tolerance PASS/ALERT flags |
| Figures | optional | QC visualization |

---

## 🧱 Repo Structure

```pqsql
dmpk-interop-immunogenicity-standards/
│
├─ notebooks/
│   ├─ 01_ingest_from_ada_panda_mini.ipynb
│   ├─ 02_build_cdisc_adimmun.ipynb
│   ├─ 03_export_fhir_json.ipynb
│   └─ 04_validate_standards.ipynb
│
├─ interoperability/
│   └─ fhir_examples/   ← JSON ADA + linked PK
│
├─ cdisc/
│   └─ adam_like/
│       ├─ adimmun.csv
│       └─ metadata.yaml
│
├─ intermediate/
│   └─ ada_harmonized.csv
│
├─ validations/
│   └─ summary.md
│
├─ config.yml
└─ README.md
```
👉 Enables machine-readable ADA risk flags for clinical systems.

---

## ✅ Validation Framework

Checks include:

- ✅ Controlled terminology mapping  
- ✅ Numeric ranges (0–1 recovery, ng/mL > 0)  
- ✅ No unclassified assay methods  
- ✅ PASS/ALERT logically linked to drug tolerance thresholds  

📤 Output: `validations/summary.md`

---

## ▶️ How to Run

```bash
git clone https://github.com/YOURNAME/dmpk-interop-immunogenicity-standards
cd dmpk-interop-immunogenicity-standards
mamba env create -f env.yml
mamba activate dmpk-interop

make all
```
---

## ✨ What’s Inside?

| Module | Output | Regulatory Relevance |
|--------|--------|--------------------|
| **CDISC ADaM-like export** | `adimmun.csv` | Supports ISS/ISE submission |
| **FHIR Observations** | JSON export | Enables EMR → regulatory interoperability |
| **Validation** | QC reports | Ensures standards compliance |

---

## 🧬 Key Variables in ADaM-like Output (`adimmun.csv`)

| Variable | Description |
|---------|-------------|
| `USUBJID` | Subject ID |
| `ADASTAT` | Detected ADA status (Standard/PandA) |
| `METHOD` | Bioanalytical assay method |
| `CONC_WK52_NGM` | Week 52 trough exposure |
| `FLAG_TOL_PASS` | Drug tolerance PASS/ALERT |
| `EFF_OBS` | On/off-drug response (binary) |

---

## 🌐 FHIR Interoperability

FHIR `Observation` fields include:

- `code` → Immunogenicity category (LOINC-mapped)
- `method` → Bridging vs PandA exposure
- `valueBoolean` → ADA detected Y/N
- `interpretation` → PASS/ALERT from validation

Example:

```json
{
  "resourceType": "Observation",
  "id": "ADA_S001_W30_PandA",
  "status": "final",
  "code": {
    "coding": [{
      "system": "http://loinc.org",
      "code": "94445-7",
      "display": "Anti-drug antibody qualitative"
    }]
  },
  "subject": {"reference": "Patient/S001"},
  "effectiveDateTime": "2025-01-15",
  "valueBoolean": true,
  "method": {"text": "PandA ADA Assay (PEG + Acid Dissociation)"},
  "interpretation": [{
    "coding": [{"system": "http://terminology.hl7.org/CodeSystem/v3-ObservationInterpretation",
               "code": "POS",
               "display": "Positive"}]
  }],
  "derivedFrom": [{
    "reference": "Observation/PK_S001_W30"
  }],
  "extension": [{
    "url": "http://example.org/fhir/StructureDefinition/drug-tolerance-flag",
    "valueString": "PASS"
  }]
}
```
Outputs will be written to:
```bash
cdisc/adam_like/
interoperability/fhir_examples/
validations/
```
---

## 📌 Why this matters

✅ Aligns **immunogenicity → exposure → regulatory reporting**  
✅ Standardizes communication between **BioA ↔ ClinPharm ↔ IT**  
✅ Enables **digital submissions** to FDA & EMA  
✅ **Future-proof**: FHIR/HL7 compatibility for direct **EHR ↔ regulatory** data exchange  

---

## 📚 References

- **CDISC ADaM Implementation Guide** — Immunogenicity Working Group  
- **HL7 FHIR Observation Resource** — immunogenicity profile examples  
- **FDA Immunogenicity Guidance (2019)** — bioanalytical method validation expectations  

---

## 📬 Contact
Carlos Montefusco
📧 cmontefusco@gmail.com
🔗 GitHub: /camontefusco
