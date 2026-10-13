*Variability key: 🟢 Low | 🟡 Medium | 🔴 High (sample-specific measurement noise) | 🔷 Feature heterogeneity (consistent across samples — model, do not simply remove)*

*See `variability_framework.md` for the full taxonomy and criteria.*

*QC notation: ✓ = confirmed in published protocol or Infinity Bio report | ◇ = recommended best practice, not confirmed as routinely applied*

---

> **Before reading:** Variability indicators in this document use a precise taxonomy defined in `variability_framework.md`. The key distinction is between 🔴 sample-specific measurement noise (the primary concern for protocol optimisation) and 🔷 feature heterogeneity (consistent across samples — better addressed by modelling than removal). Biological variability and analytical variability are out of scope here.

---


# PART 1: PhIP-Seq Protocol

## Background and Evolution

| Version | Reference | Key Changes |
|---|---|---|
| **v1** | Larman et al., *Nat Biotechnol*, 2011 | 36-aa tiles, human proteome, 2-round panning, magnetic bead IP |
| **v2** | Xu et al. (VirScan), *Science*, 2015 | 56-aa tiles, 28-aa overlap, virome library (93,904 peptides) |
| **v3** | Mohan & Larman et al., *Nat Protoc*, 2018 | Standardised protocol, pepsyn tool, single-round panning, Z-score analysis |
| **v4** | Shrock et al., *Bio Protoc*, 2022 | Video supplement, IEDB antigens added, reproducibility emphasis |

The core wet lab steps have remained largely stable since v2. The most consequential protocol change was the switch from two-round to single-round panning (v2 → v3), which improved quantitative linearity but means pre-2018 datasets are not directly comparable to modern data without harmonisation.

---

## Phase A: Library Design and Construction

---

### Step A1 — Proteome Selection and Peptide Tile Design
🟢 **Low variability**

**Description**

- Protein sequences of interest (pathogen proteomes, human proteome, allergens, or custom panels) downloaded in FASTA format
- Tiled into overlapping peptide fragments using the *pepsyn* tool with defined length and overlap parameters
- v1 (2011): 36-aa tiles with 18-aa overlap
- v3+ (2018 onward): 56-aa tiles with 28-aa overlap — longer tiles improve sensitivity for epitopes near tile boundaries
- Each peptide sequence back-translated to a DNA sequence optimised for T7 phage expression
- Flanking cloning sequences appended to each oligo

**Potential QC Procedures**

- ✓ Sequence verification of in silico library against source proteome (completeness check) — described in Mohan & Larman 2018
- ✓ Confirm even tiling — no gaps in protein sequence coverage — confirmed standard practice
- ◇ Check for duplicate or near-identical tiles that could confound alignment
- ◇ GC content distribution of designed oligos (extreme GC reduces synthesis yield)
- ◇ Reference database version documented and locked

**Variability Introduced**

- Library design is entirely computational — variability minimal when reference proteomes are versioned and pepsyn parameters are fixed
- Main risk: reference database version drift across studies creates non-comparable libraries between labs or time points
- Tile length and overlap choice is a systematic design decision, not a source of run-to-run variability

---

### Step A2 — Oligonucleotide Synthesis
🔷 **Feature heterogeneity**

> ⚠ **Consistency assumption:** This effect is expected to be stable across samples under fixed protocol conditions. If comparing across batches or sample sets, verify stability with diagnostic plots before proceeding — see `variability_framework.md` Section 5.

**Description**

- Designed DNA sequences synthesised as a pooled oligonucleotide library on microarray-based platforms (e.g. Agilent, Twist Bioscience)
- Each oligo encodes one peptide tile plus flanking primer sequences
- Libraries range from 50,000 to >700,000 distinct sequences per synthesis run
- Synthesised pool eluted and amplified by PCR to generate sufficient material for cloning

**Potential QC Procedures**

- ✓ NGS of amplified oligo pool to verify library complexity and representation evenness — described in Mohan & Larman 2018
- ✓ Gel electrophoresis to confirm oligo length distribution — confirmed standard practice
- ◇ Target: each library member within 10-fold of median (in practice >100-fold range is common — this is a known limitation, not a failure criterion)
- ◇ Flag severely underrepresented sequences before proceeding to cloning

**Variability Introduced**

- Microarray synthesis introduces systematic representation bias — some sequences synthesised at lower yield due to GC content, secondary structure, and positional effects on the array
- Persistent source of library imbalance that downstream normalisation must address
- One of the most consequential sources of background non-uniformity in the final count matrix
- Batch-to-batch variation between synthesis runs is real and not fully correctable

---

### Step A3 — Cloning into T7 Phage Display Vector
🔷 **Feature heterogeneity**

> ⚠ **Consistency assumption:** This effect is expected to be stable across samples under fixed protocol conditions. If comparing across batches or sample sets, verify stability with diagnostic plots before proceeding — see `variability_framework.md` Section 5.

**Description**

- Amplified oligo pool cloned into T7 bacteriophage display vector (T7Select system, Novagen/Millipore)
- T7 preferred over M13: displays larger inserts (up to 1,000 aa), smaller particle (~60 nm), no membrane export requirement — allows peptides with internal cysteines
- Insert fused to T7 10B capsid protein — peptide displayed on phage surface, linking genotype to phenotype
- Recombinant phage packaged in vitro and used to infect E. coli BLT5403 for amplification

**Potential QC Procedures**

- ✓ Plaque titration to determine phage library titre (target: >10⁸ pfu/ml) — confirmed in Mohan & Larman 2018
- ✓ PCR of individual plaques to verify insert presence — confirmed standard practice
- ◇ Deep sequencing of packaged library to confirm complexity maintained through cloning
- ◇ Calculate fraction of phage carrying insert vs empty vector

**Variability Introduced**

- Cloning efficiency is not uniform — sequences encoding toxic or poorly expressed peptides are selectively lost
- Compounds synthesis bias from A2: library complexity after cloning is reliably lower than designed complexity
- Losses at this step are irreversible — dropped library members cannot be recovered

---

## Phase B: Library Propagation

---

### Step B1 — E. coli Infection and Phage Amplification
🔷 **Feature heterogeneity**

> ⚠ **Consistency assumption:** This effect is expected to be stable across samples under fixed protocol conditions. If comparing across batches or sample sets, verify stability with diagnostic plots before proceeding — see `variability_framework.md` Section 5.

**Description**

- Packaged phage library used to infect E. coli BLT5403 (contains T7 RNA polymerase under IPTG-inducible control)
- Infected culture grown to lysis; lysate cleared by centrifugation
- Amplified phage stock generated for use in all downstream immunoprecipitation experiments
- Multiplicity of infection (MOI) — the phage:bacteria ratio — must be controlled to ensure uniform amplification

**Potential QC Procedures**

- ✓ Phage titre of amplified stock (pfu/ml) — confirmed standard practice
- ◇ Deep sequencing of amplified stock to verify library complexity maintained vs pre-amplification
- ◇ Confirm no catastrophic dropout of specific library members
- ◇ Monitor lysis time (too fast or too slow indicates culture problems)

**Variability Introduced**

- Phage encoding peptides that confer growth disadvantage to E. coli will be underrepresented after amplification
- Library representation can shift between amplification rounds — particularly for aged or re-amplified stocks
- Standardising MOI and growth conditions reduces but does not eliminate representation drift

---

## Phase C: Immunoprecipitation (Panning)

---

### Step C1 — Serum Pre-adsorption
🟡 **Medium variability**

**Description**

- Serum pre-adsorbed against E. coli lysate and/or empty phage particles (no insert) to remove non-specific antibodies
- Performed by incubation at 4°C followed by magnetic bead capture and removal
- Reduces background binding to E. coli antigens and phage coat proteins
- Not standardised until v3 (Mohan & Larman 2018)

**Potential QC Procedures**

- ✓ Monitor serum volume and dilution factor — confirmed in 2018 protocol
- ◇ Confirm reduction in background binding to empty phage controls post-adsorption
- ◇ Pre-adsorption material must be from the same lot across a study batch

**Variability Introduced**

- Pre-adsorption efficiency varies with serum composition, incubation time, and quality of the blocking material
- Insufficient pre-adsorption inflates background and increases false positive rate
- Excessive pre-adsorption can remove low-affinity true positives

---

### Step C2 — Phage-Antibody Incubation
🔴 **High variability**

**Description**

- Pre-adsorbed serum diluted 1:1000 in PBS-T and incubated with the phage library at 4°C with rotation for 16–18 hours
- Serum antibodies bind to cognate peptide epitopes displayed on the phage surface
- Library used at ~10⁵ phage per library member to ensure adequate representation
- v3 (2018): input IgG concentration normalised across samples to reduce between-sample variability

**Potential QC Procedures**

- ✓ Confirm consistent phage input across all samples (measured by titre) — confirmed in 2018 protocol
- ✓ Confirm consistent serum dilution — confirmed standard practice
- ✓ Positive control sera with known reactivity included — confirmed in 2018 protocol
- ✓ Negative controls (PBS or pre-immune serum) — confirmed in 2018 protocol
- ◇ IgG quantification and normalisation of input samples

**Variability Introduced**

- Largest source of sample-to-sample variability in the assay
- Total IgG concentration varies substantially between individuals — affects competition for library members
- Temperature fluctuations and rotation speed inconsistencies affect binding kinetics
- 16–18 hour window introduces time-of-day variability if plates are not processed simultaneously

---

### Step C3 — Immunoprecipitation with Protein A/G Magnetic Beads
🔴 **High variability**

**Description**

- Protein A/G magnetic beads added to capture IgG–phage complexes from the incubation mixture
- Beads separated by magnet and washed 3–5× with PBS-T to remove unbound phage
- Bound phage retained on beads contain the peptides recognised by the individual's antibodies
- v1 (2011): two panning rounds; v3 (2018): single-round panning became standard — two rounds over-enriched non-specific phage and reduced quantitative linearity

**Potential QC Procedures**

- ✓ Mock IP control (PBS, no serum) run in parallel — confirmed in 2018 protocol; primary background reference for all data analysis
- ✓ Positive control recovery confirmed — confirmed standard practice
- ◇ Phage titre in wash fractions to confirm adequate washing
- ◇ Bead volume and wash stringency logged and kept consistent across samples

**Variability Introduced**

- Bead capture efficiency varies with bead lot, total IgG load, and wash conditions
- Non-specific sticking of phage to beads is a persistent source of background
- Switch from 2-round to single-round panning (v2 → v3) significantly reduced non-specific enrichment — historical and modern datasets are not directly comparable without harmonisation

---

## Phase D: Sequencing Library Preparation and NGS

---

### Step D1 — PCR Amplification of Phage Inserts
🟡 **Medium variability**

**Description**

- Phage eluted from beads by boiling after final wash
- Insert DNA amplified by PCR using universal primers flanking the cloning site
- Illumina sequencing adapters and sample-specific index barcodes added in same or subsequent indexing PCR
- PCR cycles minimised (typically 20–25) to avoid over-amplification artefacts

**Potential QC Procedures**

- ✓ Gel electrophoresis to confirm PCR product at expected size — confirmed standard practice
- ✓ Library quantification by Qubit or qPCR — confirmed in 2018 protocol
- ◇ Check for primer dimer contamination
- ◇ Confirm index diversity across multiplexed samples
- ◇ Minimum input phage threshold before PCR to avoid jackpot effects

**Variability Introduced**

- PCR amplification biases toward shorter or higher-GC inserts
- Jackpot effects (clonal amplification of rare phage) can occur if input phage count is very low
- Consistent input phage numbers across samples partially controls this

---

### Step D2 — Next-Generation Sequencing
🟢 **Low variability**

**Description**

- Multiplexed libraries from all samples pooled and sequenced on Illumina (HiSeq or NextSeq)
- Target: 1–3 million reads per sample
- Single-end 50-bp reads standard — inserts are short enough that paired-end adds little information
- Reads demultiplexed by sample index after sequencing run

**Potential QC Procedures**

- ✓ Per-sample read count (target: >500,000 aligned reads; low counts trigger repeat) — confirmed in 2018 protocol
- ✓ Sequencing quality scores (Q30 threshold) — standard Illumina QC
- ◇ Duplication rate per sample
- ◇ Fraction of reads aligning to designed library (unaligned reads indicate contamination or artefacts)

**Variability Introduced**

- NGS is the most reproducible step in the entire protocol
- Run-to-run variability is low given standardised platform and chemistry
- Main technical risk: uneven pooling of samples before sequencing run leads to one sample dominating reads

---

## Phase E: Data Analysis

---

### Step E1 — Read Alignment and Count Matrix Generation
🟢 **Low variability**

**Description**

- Raw reads trimmed and aligned to the designed peptide library using Bowtie2
- Count matrix generated: rows = peptide tiles, columns = samples, values = read counts
- Tiles with zero counts across all samples flagged for investigation
- Phippery pipeline commonly used for integrated processing (alignment, QC, normalisation)

**Potential QC Procedures**

- ✓ Alignment rate (target: >85% of reads map to library) — confirmed in Phippery documentation
- ✓ Per-sample read depth and count distribution — confirmed standard practice
- ◇ Pearson correlation between technical replicates (target: ≥0.95)
- ◇ PCA of all samples — controls should cluster separately from patient samples

**Variability Introduced**

- Alignment is a deterministic computational step — minimal variability for fixed aligner parameters
- Parameter choices (mismatch tolerance, multi-mapping handling) affect the count matrix but are fixed within a study

---

### Step E2 — Normalisation and Hit Calling
🟡 **Medium variability**

**Description**

- Read counts normalised using RPM or edgeR TMM normalisation
- Each sample compared against the full set of mock IP controls using one of three methods:
  - **edgeR** (negative binomial GLM): FDR-adjusted p-value < 0.05
  - **BEER** (Bayesian hierarchical model): posterior probability of enrichment; higher sensitivity for weak reactivities
  - **Z-score**: Z = (RPM_sample − mean RPM_mock) / SD RPM_mock; threshold typically Z > 3.5
- All three methods operate at the per-tile level — no formal probe-aggregation across overlapping tiles

**Potential QC Procedures**

- ✓ Distribution of fold-over-background values (approximately log-normal with heavy right tail) — confirmed standard practice
- ✓ Positive control enrichment confirmed — confirmed standard practice
- ◇ Mock IPs cluster separately from serum samples in PCA
- ◇ Comparison of hit lists across methods as sensitivity check

**Variability Introduced**

- Choice of statistical method (edgeR vs BEER vs Z-score) affects hit lists — systematic variability, not random
- Particularly consequential for weak or early-stage antibody responses
- No probe-aggregation across overlapping tiles performed in any current pipeline — tile independence assumption is unverified and likely incorrect
- FDR-based thresholds carry all limitations of p-value-derived inference at scale

---
---
