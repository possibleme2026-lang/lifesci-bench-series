---
configs:
- config_name: default
  data_files:
  - split: train
    path: lifesci-harbor-bench.tar.gz
dataset_info:
  features:
  - name: task_id
    dtype: string
  - name: domain
    dtype: string
  - name: instruction
    dtype: string
  splits:
  - name: train
    num_bytes: 1887436
    num_examples: 100
  download_size: 1887436
  dataset_size: 1887436
viewer: false
---

# LifeSci-Harbor-Bench: 100 Open Research Tasks for Agentic Life Science

**100 ultra-long-horizon, open-ended research tasks** evaluating coding agents on
**real, computationally verifiable (dry-lab) research in the life sciences**, with an
emphasis on **aging / longevity / healthspan**. Every task is self-contained, derived
from **real scientific literature, clinical trials, and public databases**, and scored
through a **6-layer deterministic verifier + optional LLM judge** (rubric + claim
verification + hallucination detection + cognitive traps).

**No fixed answers.** Tasks evaluate research quality, methodological rigor, and
reproducibility — not whether a specific number is correct.

## Key Numbers

| Metric | Value |
|--------|-------|
| Total tasks | **100** |
| Domains | **14** |
| Real papers cited (DOI/PMID) | **514+** |
| Real clinical trials (NCT) | **139+** |
| Real database references | **489+** |
| Average estimated effort | **130 hours** (range 80–200) |
| Verification layers | **6 deterministic + 1 LLM judge** |
| Cognitive traps per task | **2–4** (unit inconsistency, outliers, confounders, multiple testing) |

## Why These Tasks

Existing scientific agent benchmarks either (a) ask for a single analysis on real data
with fuzzy ground truth, or (b) use synthetic data with known answers that reward
hacking. LifeSci-Harbor-Bench takes a third path: **open research tasks on real data**
where the verifier scores *process quality* — methodological rigor, citation integrity,
reproducibility, trap detection — rather than a single numerical answer. This makes the
benchmark **resistant to reward hacking** while remaining **fully deterministic and
reproducible**.

Every task is grounded in:
- **Real papers** (verified DOI/PMID from Nature, Cell, Science, Nature Aging, etc.)
- **Real clinical trials** (verified NCT numbers from ClinicalTrials.gov)
- **Real databases** (GEO, UK Biobank, GTEx, GWAS Catalog, ENCODE, Human Cell Atlas, etc.)

## Task Structure (Harbor Format)

```
tasks/life-sciences/{domain}/{task_id}/
├── instruction.md              # Task instructions (the only text the agent sees)
├── environment/
│   └── data/
│       └── DATA_SOURCES.md     # Real data download instructions (URLs + accessions)
├── solution/
│   └── pipeline.py             # Research framework skeleton (NOT a solution)
└── tests/
    ├── verify.py               # 6-layer verifier + LLM judge integration
    ├── llm_judge.py            # LLM judge module (OpenAI-compatible API)
    ├── expert_guidance.json    # Task-specific expert evaluation criteria
    └── fixtures/
        └── visible_truth.json  # Task metadata + real source traceability
```

## Verification Methodology (7-Layer)

| Layer | Weight | What It Checks |
|-------|--------|----------------|
| 1. Deterministic | 30% | Deliverables exist, code syntax, manuscript structure, citations, data outputs |
| 2. Fine-grained rubric | 25% | 10 mandatory (w4) + 8 optional (w2) + 3 negative (w-4) criteria, ternary scoring |
| 3. Claim verification | 15% | Factual claims have nearby citation support |
| 4. Hallucination detection | 15% | Fabricated data, fake refs, "data not shown">3, suspicious p-values, results without code → red flag |
| 5. Cognitive traps | 10% | Agent identifies and corrects embedded traps (unit inconsistency, outliers, confounders, multiple testing) |
| 6. RSI iteration | 5% | Version management, research logs, self-awareness of limitations |
| 7. LLM Judge | (blend) | Expert-level evaluation on 5 dimensions (data integrity, analytical rigor, relevance, execution precision, format) — enhances layers 2-5 when API key available |

**Critical failure rule**: incomplete deliverables (<0.5), no executable code (0), or
detected hallucination (<0.4) → total score capped at 0.3.

## Domain Coverage (14 domains)

| Domain | Count | Description |
|--------|-------|-------------|
| `biological-clock` | 7 | Epigenetic clocks, transcriptomic clocks, proteomic clocks, metabolomic clocks, multi-omic fusion |
| `cardiovascular` | 8 | Heart failure, vascular aging, HFpEF, arterial stiffness, cardiac senescence, SGLT2 inhibitors |
| `cellular` | 8 | Cellular senescence, SASP, proteostasis, autophagy, stem cell exhaustion, extracellular vesicles |
| `circadian-rhythm` | 6 | Shift work, clock genes, chronotherapy, sleep and dementia, circadian disruption |
| `epigenetics` | 8 | DNA methylation drift, epigenetic clocks, histone marks, chromatin accessibility, reprogramming |
| `genetics` | 7 | GWAS, Mendelian randomization, telomere biology, APOE, somatic mutations, mtDNA haplogroups |
| `immunology` | 7 | Inflammaging, vaccine response, immune cell atlas, TCR repertoire, trained immunity, CMV |
| `metabolism` | 8 | Mitochondrial dysfunction, NAD+ metabolism, caloric restriction, rapamycin, BCAA, bile acids |
| `microbiome` | 8 | Gut microbiome, centenarian microbiota, FMT, microbial metabolites, gut-brain axis |
| `neuroscience` | 8 | Alzheimer's, Parkinson's, neuroinflammation, BBB, synaptic loss, glymphatic system |
| `pharmacology-senolytics` | 7 | D+Q, fisetin, navitoclax, drug repurposing, senolytic clinical trials, cardiac toxicity |
| `reproductive-aging` | 6 | Ovarian aging, menopause, oocyte quality, AMH, fertility preservation |
| `stem-cell-regenerative` | 7 | HSC clonal hematopoiesis, muscle stem cells, partial reprogramming, neural stem cells |
| `systems` | 5 | Multi-omic biological age, hallmark networks, physiological reserve, polypharmacy |

## Complete Task Matrix

### biological-clock (7 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `clock-acceleration-discordance-tissues` | Epigenetic Age Acceleration Discordance Across Tissues: Multi-Tissue M... | 140 | 5 | 1 | 5 |
| 2 | `clock-exercise-intervention-rct` | Epigenetic Clock Response to Exercise Intervention: Reanalysis of Rand... | 130 | 5 | 2 | 5 |
| 3 | `epigenetic-clock-benchmarking-cohorts` | Systematic Benchmarking of Epigenetic Aging Clocks Across 10+ Independ... | 160 | 5 | 2 | 5 |
| 4 | `metabolomic-clock-biological-age` | Metabolomic Aging Clock Development and Biological Age Estimation Usin... | 140 | 5 | 1 | 5 |
| 5 | `multi-omic-clock-fusion-integration` | Multi-Omic Biological Age Clock Fusion: Integrating DNA Methylation, T... | 180 | 5 | 1 | 5 |
| 6 | `proteomic-clock-mortality-ukbiobank` | Proteomic Aging Clock Development and Mortality Risk Prediction Using ... | 150 | 5 | 1 | 5 |
| 7 | `transcriptomic-aging-clock-gtex-tissues` | Development and Benchmarking of Tissue-Specific Transcriptomic Aging C... | 150 | 5 | 1 | 5 |

### cardiovascular (8 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `arterial-stiffness-dementia-causal` | Arterial Stiffness as a Causal Risk Factor for Dementia: MR and Mediat... | 130 | 5 | 1 | 5 |
| 2 | `biological-age-heart-failure` | Biological Age Acceleration and Incident Heart Failure | 140 | 5 | 1 | 5 |
| 3 | `cardiac-senescence-snrnaseq` | Cardiac Senescence Burden in Human Heart Failure: snRNA-seq Reanalysis | 150 | 5 | 1 | 5 |
| 4 | `exercise-vascular-epigenetic-aging` | Exercise Training Reverses Vascular Epigenetic Aging | 140 | 5 | 3 | 5 |
| 5 | `hfpef-multiomics-subtypes` | HFpEF Multi-Omics Subtype Discovery and External Validation | 160 | 5 | 2 | 5 |
| 6 | `prs-early-onset-mi` | Polygenic Risk Scores for Early-Onset Myocardial Infarction | 130 | 5 | 1 | 5 |
| 7 | `sglt2-cardioprotection-mechanisms` | SGLT2 Inhibitor Cardioprotection Mechanisms Beyond Glycemia | 140 | 5 | 3 | 5 |
| 8 | `vascular-aging-cognitive-decline-mr` | Vascular Aging and Cognitive Decline: Bidirectional Mendelian Randomiz... | 150 | 5 | 1 | 5 |

### cellular (8 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `autophagy-flux-human-aging-omics` | Autophagy Flux Measurement in Human Aging: Transcriptomic and Proteomi... | 120 | 5 | 1 | 5 |
| 2 | `cellular-senescence-single-cell` | Single-Cell Transcriptomic Atlas of Cellular Senescence in Aging | 130 | 5 | 1 | 5 |
| 3 | `extracellular-vesicle-aging-proteomics-rnaseq` | Extracellular Vesicle Signaling in Aging: Proteomic and Small RNA-Seq ... | 125 | 5 | 1 | 5 |
| 4 | `lipofuscin-senescence-imaging-transcriptomics` | Lipofuscin Accumulation and Cellular Senescence: Imaging-Transcriptomi... | 115 | 5 | 1 | 5 |
| 5 | `mitochondrial-derived-vesicles-quality-control` | Mitochondrial-Derived Vesicles and Mitochondrial Quality Control in Hu... | 130 | 5 | 1 | 5 |
| 6 | `proteostasis-neurodegeneration` | Proteostasis Collapse in Neurodegeneration: Mechanisms and Therapeutic... | 140 | 5 | 1 | 5 |
| 7 | `sasp-regulatory-network` | Regulatory Network Architecture of the Senescence-Associated Secretory... | 120 | 5 | 1 | 5 |
| 8 | `stem-cell-exhaustion-modeling` | Computational Modeling of Hematopoietic Stem Cell Exhaustion During Ag... | 140 | 5 | 1 | 5 |

### circadian-rhythm (6 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `chronotherapy-cancer-elderly-outcomes` | Chronotherapy Optimization for Cancer Treatment in Elderly Patients | 110 | 5 | 3 | 5 |
| 2 | `circadian-disruption-metabolic-causal-inference` | Circadian Disruption and Metabolic Syndrome: Multi-Cohort Causal Infer... | 110 | 6 | 2 | 5 |
| 3 | `circadian-gene-expression-gtex-aging` | Age-Associated Circadian Gene Expression Remodeling Across Human Tissu... | 100 | 6 | 1 | 5 |
| 4 | `per2-cry1-variants-healthspan-gwas` | PER2/CRY1 Genetic Variants, Circadian Phenotypes, and Healthspan | 100 | 6 | 1 | 5 |
| 5 | `shift-work-epigenetic-aging-cohort` | Shift Work Exposure and Epigenetic Aging in Occupational Cohorts | 120 | 6 | 1 | 5 |
| 6 | `sleep-duration-dementia-bidirectional-mr` | Bidirectional Causal Inference of Sleep Duration and Dementia Risk | 100 | 6 | 1 | 5 |

### epigenetics (8 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `chromatin-accessibility-immune-aging` | Chromatin Accessibility Remodeling and Immune Cell Dysfunction in Huma... | 110 | 5 | 1 | 5 |
| 2 | `dna-methylation-drift-causality` | Causal Inference of DNA Methylation Drift in Aging | 120 | 5 | 1 | 5 |
| 3 | `epigenetic-clock-cross-species` | Cross-Species Conservation of Epigenetic Aging Clocks | 100 | 5 | 1 | 5 |
| 4 | `epigenetic-reprogramming-safety` | Safety and Efficacy of Partial Epigenetic Reprogramming for Aging Reve... | 150 | 5 | 1 | 5 |
| 5 | `histone-mark-landscape-aging` | Histone Modification Landscape Remodeling During Aging | 110 | 5 | 1 | 5 |
| 6 | `pollution-epigenetic-age-acceleration` | Environmental Pollution Exposure, Epigenetic Age Acceleration, and Lon... | 130 | 5 | 1 | 5 |
| 7 | `transposable-element-reactivation-aging` | Transposable Element Reactivation and Genomic Instability in Human Agi... | 120 | 5 | 1 | 5 |
| 8 | `x-inactivation-skewing-aging-females` | X-Chromosome Inactivation Skewing, Epigenetic Drift, and Aging Phenoty... | 100 | 5 | 1 | 5 |

### genetics (7 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `aging-metabolite-gwas` | Genome-Wide Association Study of Aging-Related Metabolites and Causal ... | 130 | 5 | 1 | 5 |
| 2 | `apoe-alzheimer-causal-mechanism` | APOE Genotype and Alzheimer's Disease: Causal Mechanisms Beyond Risk A... | 150 | 5 | 1 | 5 |
| 3 | `gene-environment-longevity-interaction` | Gene-Environment Interaction in Human Longevity: GWAS and Lifestyle Fa... | 130 | 5 | 1 | 5 |
| 4 | `longevity-gene-conservation` | Evolutionary Conservation of Longevity Genes Across Model Organisms an... | 120 | 5 | 1 | 5 |
| 5 | `mtdna-haplogroup-healthspan` | Mitochondrial DNA Haplogroups and Healthspan: Cohort Analysis of Mitoc... | 120 | 5 | 1 | 5 |
| 6 | `somatic-mutation-aging-tissues` | Somatic Mutation Burden in Aging Tissues: Whole-Genome Sequencing Coho... | 140 | 5 | 1 | 5 |
| 7 | `telomere-mr-all-cause` | Mendelian Randomization of Telomere Length and All-Cause Mortality | 110 | 5 | 1 | 5 |

### immunology (7 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `aging-vaccine-design` | Rational Vaccine Design for Aging Immune Systems | 130 | 5 | 1 | 5 |
| 2 | `autoimmunity-immunosenescence-gwas` | Autoimmunity Risk in Immunosenescence: GWAS and Electronic Health Reco... | 130 | 5 | 1 | 5 |
| 3 | `cmv-immune-aging-driver` | Cytomegalovirus as a Driver of Immune Aging: Serology and Immune Profi... | 120 | 5 | 1 | 4 |
| 4 | `inflammaging-immune-atlas` | Single-Cell Immune Atlas of Inflammaging Across the Human Lifespan | 130 | 5 | 1 | 5 |
| 5 | `senolytic-discovery-screen` | Computational Drug Discovery and Repurposing Screen for Novel Senolyti... | 140 | 5 | 1 | 5 |
| 6 | `tcr-repertoire-diversity-aging` | T Cell Receptor Repertoire Diversity Decline in Aging: Multi-Cohort TC... | 120 | 5 | 1 | 4 |
| 7 | `trained-immunity-aging-epigenetic` | Trained Immunity and Innate Immune Memory in Aging: Transcriptomic and... | 140 | 5 | 1 | 4 |

### metabolism (8 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `ages-vascular-aging-proteomics` | Advanced Glycation End Products (AGEs) and Vascular Aging: Cohort Prot... | 110 | 5 | 1 | 5 |
| 2 | `bcaa-insulin-resistance-causal-mr` | Branched-Chain Amino Acids as Causal Drivers of Insulin Resistance: Me... | 120 | 5 | 1 | 5 |
| 3 | `bile-acid-signaling-healthspan-microbiome` | Bile Acid Signaling, Gut Microbiome, and Healthspan in Human Aging: Mu... | 125 | 5 | 1 | 5 |
| 4 | `caloric-restriction-mechanism` | Mechanistic Basis of Caloric Restriction-Induced Longevity | 130 | 5 | 1 | 5 |
| 5 | `ketogenic-diet-biological-aging-rct` | Ketogenic Diet Effects on Biological Aging: Multi-Omic Reanalysis of R... | 115 | 5 | 1 | 5 |
| 6 | `mitochondrial-dysfunction-causality` | Causal Role of Mitochondrial Dysfunction in Aging | 120 | 5 | 1 | 5 |
| 7 | `nad-metabolism-longevity-meta` | NAD+ Metabolism and Longevity: Meta-Analysis of Preclinical and Clinic... | 110 | 5 | 1 | 5 |
| 8 | `rapamycin-dose-response` | Dose-Response and Sex-Specific Effects of Rapamycin on Aging and Healt... | 120 | 5 | 1 | 5 |

### microbiome (8 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `antibiotic-aging-accelerant` | Antibiotic Exposure as an Accelerant of Biological Aging: Pharmacoepid... | 150 | 5 | 1 | 4 |
| 2 | `centenarian-microbiome-meta-analysis` | Meta-Analysis of Gut Microbiome Signatures in Centenarians vs. Elderly... | 120 | 7 | 1 | 5 |
| 3 | `fmt-aging-systematic-review` | Fecal Microbiota Transplantation Efficacy in Aging-Related Conditions:... | 110 | 5 | 3 | 5 |
| 4 | `gut-brain-cognitive-decline` | Gut Microbiome Composition Predicts Cognitive Decline: Longitudinal Co... | 140 | 5 | 2 | 4 |
| 5 | `microbial-metabolite-cv-aging` | Microbial Metabolites (TMAO, Butyrate, Indole) as Predictors of Cardio... | 130 | 5 | 1 | 5 |
| 6 | `microbiome-frailty-causal-mr` | Causal Role of Gut Microbiome in Frailty: Two-Sample Mendelian Randomi... | 100 | 5 | 3 | 4 |
| 7 | `personalized-microbiome-healthspan` | Personalized Microbiome Modulation for Healthspan: Design of Adaptive ... | 160 | 5 | 3 | 5 |
| 8 | `uremic-toxins-kidney-aging` | Microbiome-Derived Uremic Toxins and Kidney Aging: Multi-Omics Integra... | 130 | 5 | 1 | 5 |

### neuroscience (8 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `bbb-permeability-aging` | Blood-Brain Barrier Permeability in Normal Aging: Imaging and Transcri... | 130 | 5 | 1 | 5 |
| 2 | `blood-epigenetic-clock-ad` | Blood-Based Epigenetic Clocks for Alzheimer's Disease Prediction: Long... | 120 | 5 | 2 | 4 |
| 3 | `glymphatic-aging-neurodegeneration` | Glymphatic System Function in Aging and Neurodegeneration: Real Imagin... | 120 | 5 | 1 | 5 |
| 4 | `lifestyle-brain-aging-rct` | Lifestyle Intervention Effects on Brain Aging: Reanalysis of Real RCT ... | 150 | 5 | 2 | 5 |
| 5 | `neuroinflammation-scrna-cognitive` | Neuroinflammation as Driver of Cognitive Decline: Single-Cell RNA-Seq ... | 140 | 5 | 1 | 4 |
| 6 | `parkinson-progression-subtypes` | Parkinson's Disease Progression Subtypes: Real Clinical and Omics Data... | 130 | 5 | 2 | 5 |
| 7 | `synaptic-loss-biomarkers-ad` | Synaptic Loss Biomarkers in Preclinical Alzheimer's Disease: Proteomic... | 120 | 5 | 1 | 5 |
| 8 | `tau-amyloid-causality-mr` | Tau vs. Amyloid Causality in Alzheimer's Disease: Mendelian Randomizat... | 110 | 5 | 1 | 5 |

### pharmacology-senolytics (7 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `dasatinib-quercetin-clinical-meta-analysis` | Systematic Review and Meta-Analysis of Dasatinib Plus Quercetin (D+Q) ... | 150 | 5 | 3 | 5 |
| 2 | `fisetin-pharmacokinetics-target-engagement` | Fisetin Pharmacokinetics, Bioavailability, and Target Engagement in Ag... | 120 | 5 | 3 | 5 |
| 3 | `navitoclax-bcl2-on-target-off-target` | Navitoclax (ABT-263) On-Target BCL-xL Inhibition vs. Off-Target Effect... | 130 | 5 | 3 | 5 |
| 4 | `senolytic-cardiac-toxicity-pharmacovigilance` | Cardiac Toxicity Risk of Senolytic Therapy: Pharmacovigilance Analysis... | 140 | 5 | 3 | 5 |
| 5 | `senolytic-drug-repurposing-screen` | Network-Based Drug Repurposing Screen for Novel Senolytics: Integratio... | 160 | 5 | 2 | 5 |
| 6 | `senolytic-senomorphic-combination-design` | Rational Design of Senolytic + Senomorphic Combination Therapy: Drug I... | 170 | 5 | 3 | 5 |
| 7 | `senolytics-covid-long-haulers-trial` | Senolytics for Post-Acute Sequelae of SARS-CoV-2 (Long COVID): Reanaly... | 140 | 5 | 3 | 5 |

### reproductive-aging (6 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `amh-afc-menopause-timing` | Ovarian Reserve Biomarkers Predict Menopause Timing | 120 | 5 | 1 | 5 |
| 2 | `chemo-ovarian-damage-fertility` | Chemotherapy-Induced Ovarian Damage and Fertility Preservation | 130 | 5 | 5 | 5 |
| 3 | `menopause-cvd-risk-mr` | Menopause Timing and Cardiovascular Disease Risk: Mendelian Randomizat... | 140 | 5 | 1 | 5 |
| 4 | `oocyte-aneuploidy-maternal-age` | Oocyte Aneuploidy Rate vs Maternal Age: Meta-Analysis and Modeling | 120 | 5 | 1 | 5 |
| 5 | `ovarian-aging-epigenetic-clock` | Ovarian Aging Epigenetic Clock Development and Validation | 150 | 5 | 1 | 5 |
| 6 | `reproductive-senescence-biological-aging` | Reproductive Senescence and Overall Biological Aging | 150 | 5 | 1 | 5 |

### stem-cell-regenerative (7 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `clonal-hematopoiesis-cardiovascular-ukbiobank` | Clonal Hematopoiesis and Cardiovascular Risk in UK Biobank | 120 | 6 | 1 | 5 |
| 2 | `hair-follicle-stem-cell-graying-scrna` | Hair Follicle Stem Cell Exhaustion and Melanocyte Depletion in Human H... | 100 | 6 | 1 | 5 |
| 3 | `intestinal-stem-cell-aging-barrier-organoid` | Intestinal Stem Cell Aging and Epithelial Barrier Dysfunction | 110 | 6 | 1 | 5 |
| 4 | `muscle-stem-cell-sarcopenia-scrna` | Muscle Stem Cell Transcriptional Dysfunction in Human Sarcopenia | 110 | 6 | 1 | 5 |
| 5 | `neural-stem-cell-adult-neurogenesis-controversy` | Neural Stem Cell Decline and the Adult Human Neurogenesis Controversy | 120 | 6 | 1 | 5 |
| 6 | `partial-reprogramming-safety-transcriptomic` | Safety Profiling of Partial Reprogramming via Transcriptomic Reanalysi... | 120 | 6 | 1 | 4 |
| 7 | `stem-cell-therapy-aging-meta-analysis` | Stem Cell Therapy Efficacy in Aging-Related Conditions: Systematic Rev... | 100 | 6 | 3 | 4 |

### systems (5 tasks)

| # | Task ID | Title | Hours | Papers | Trials | DBs |
|---|---------|-------|-------|--------|--------|-----|
| 1 | `aging-hallmark-causal-network` | Causal Network Analysis of the Interacting Hallmarks of Aging | 150 | 5 | 1 | 5 |
| 2 | `hallmark-crosstalk-multiomics-causal` | Aging Hallmark Cross-Talk: Multi-Omics Data-Driven Causal Graph Constr... | 160 | 5 | 1 | 5 |
| 3 | `multi-modal-biological-age-prediction` | Multi-Modal Biological Age Prediction and Mortality Risk Stratificatio... | 140 | 5 | 1 | 5 |
| 4 | `physiological-reserve-longitudinal` | Physiological Reserve and Resilience Quantification in Aging: Longitud... | 140 | 5 | 1 | 4 |
| 5 | `polypharmacy-aging-systems-pharmacology` | Systems Pharmacology of Polypharmacy in Aging: Drug-Drug Interaction a... | 150 | 5 | 1 | 5 |


## Running a Verifier

```bash
# Set environment variables
export HB_SUB=/path/to/agent/submission
export HB_DATA=/path/to/task/environment/data
export HB_REWARD=/tmp/reward.json

# Run verifier
python3 tasks/life-sciences/{domain}/{task_id}/tests/verify.py

# Output: JSON with reward (0-1) and per-layer scores
```

### LLM Judge (optional)

```bash
export LLM_JUDGE_API_KEY=your-key
export LLM_JUDGE_BASE_URL=https://api.openai.com/v1
export LLM_JUDGE_MODEL=gpt-4o
```

Without an API key, the verifier falls back to deterministic scoring only.

## Repository Layout

```
lifesci-harbor-bench/
├── tasks/life-sciences/      # 100 tasks across 14 domains
├── scripts/                   # Verification, packaging, regression scripts
├── docs/                      # Additional documentation
└── README.md                  # This file
```

## Source Traceability

Every task includes a `Source Traceability` section in `instruction.md` and a
`real_sources` field in `tests/fixtures/visible_truth.json` listing:
- **Primary literature**: DOIs of key papers that inspired the task
- **Clinical context**: NCT numbers of relevant clinical trials
- **Database sources**: URLs of real public databases used

All sources were verified via web search against real publications, ClinicalTrials.gov
registrations, and public database accessions. No fabricated sources are used.

## Citation

If you use LifeSci-Harbor-Bench in your research, please cite:

```
LifeSci-Harbor-Bench: 100 Open Research Tasks for Agentic Life Science.
https://huggingface.co/datasets/mondaycake/lifesci-harbor-bench
```

## License

MIT License.
