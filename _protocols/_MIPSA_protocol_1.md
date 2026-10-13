*Variability key: 🟢 Low | 🟡 Medium | 🔴 High*

*QC notation: ✓ = confirmed in published protocol or Infinity Bio report | ◇ = recommended best practice, not confirmed as routinely applied*

---

# PART 2: MIPSA Protocol

## Background and Evolution

| Version | Reference | Key Changes |
|---|---|---|
| **v1** | Larman lab, *bioRxiv*, March 2021 | Proof-of-concept; IFNL3 autoantibody; ~50% cis-barcoding; release factors excluded |
| **v2** | Credle & Larman et al., *Nat Biomed Eng*, 2022 | 11,076-ORF library; Gateway cloning; pDEST-MIPSA vector; I-SceI linearisation; 10× UCI/ORF |
| **v3** | Infinity Bio, ongoing (VirSIGHT / HuSIGHT) | Peptide tile libraries; Dolphyn-optimised selection; 96-well plate format; column 12 controls |

---

## Phase A: Library Assembly

> **Phase A differs entirely by library type.** The two MIPSA library formats — full-length ORFeome (HuSIGHT) and peptide tile (VirSIGHT) — follow completely distinct paths through Phase A. Both paths produce the same end product: a pool of linearised pDEST-MIPSA plasmids ready for in vitro transcription in Phase B. From Phase B onward the protocol is identical for both library types.

---

## Phase A — Path 1: Full-Length ORFeome Libraries (HuSIGHT)

> Each antigen is a full-length protein, expressed in cell-free solution and represented by ~10 unique barcoded copies (UCIs) in the library. Conformational epitopes are accessible. The redundancy structure is the direct analogue of multi-probe microarray design.

---

### Step A1.1 — pDEST-MIPSA Vector Design and ORF Cloning
🟢 **Low variability**

**Description**

- MIPSA Gateway Destination vector (pDEST-MIPSA) encodes in order: T7 promoter → UCI barcode → E. coli RBS → N-terminal HaloTag (891 nt) → FLAG epitope → Gateway recombination sites → I-SceI site
- UCI barcode sequence: (SW)₁₈–AGGGA–(SW)₁₈ where S = C/G and W = A/T — isothermal design ensures balanced GC content across the barcode space
- Each ORF inserted by Gateway LR recombination from a source entry clone library (e.g. the human ORFeome)
- Each ORF represented by ~10 UCIs (unique barcoded copies of the same full-length protein) — the designed redundancy enabling probe-aggregation analysis
- Unlike PhIP-Seq, no oligo synthesis or phage cloning is required — the recombination is direct and efficient

**Potential QC Procedures**

- ✓ Sanger sequencing of recombined clones to verify ORF orientation and reading frame fusion to HaloTag — confirmed in Credle & Larman 2022
- ◇ Deep sequencing of plasmid pool to verify UCI barcode diversity (~10 UCIs per ORF)
- ◇ Confirm no frameshifts or stop codons introduced at recombination junctions

**Variability Introduced**

- Vector design is fixed — minimal variability at this step
- Gateway recombination efficiency varies by ORF size but is well-established
- UCI diversity is stochastic by design — isothermal sequence ensures uniqueness and identifiability
- ~10× UCI redundancy per ORF is the key design feature enabling multi-probe statistical aggregation

---

### Step A1.2 — Plasmid Linearisation
🟢 **Low variability**

**Description**

- Circular pDEST-MIPSA plasmid pool linearised at the I-SceI restriction site located downstream of each ORF
- Prevents read-through transcription beyond the ORF — uncontrolled transcript length would interfere with RT-priming geometry and HaloTag cis-coupling in Phase B
- Linearised DNA purified by column or ethanol precipitation
- This step has no equivalent in PhIP-Seq — it is specific to the MIPSA cell-free expression system

**Potential QC Procedures**

- ✓ Gel electrophoresis to confirm complete linearisation (no supercoiled band remaining) — confirmed in Credle & Larman 2022
- ◇ DNA quantification post-purification (Qubit or NanoDrop)

**Variability Introduced**

- I-SceI is a highly efficient and specific homing endonuclease — low intrinsic variability
- Incomplete linearisation is the primary failure mode and is readily detected by gel electrophoresis
- Operator error (wrong buffer, wrong temperature) is the main risk

---

## Phase A — Path 2: Peptide Tile Libraries (VirSIGHT)

> Each antigen is a short peptide tile (not a full-length protein). Library design follows the same in silico tiling and oligo synthesis logic as PhIP-Seq, with one important addition: Dolphyn-optimised tile selection (v3 onward) replaces naive uniform tiling. The peptide tiles are then cloned into the pDEST-MIPSA vector — not into a phage vector — so that from Phase B onward the MIPSA self-assembly mechanism applies. Each antigen is represented by multiple overlapping tiles (not multiple UCIs of the same protein), making the probe redundancy structure analogous to PhIP-Seq rather than to the ORFeome path.

---

### Step A2.1 — Proteome Selection and Peptide Tile Design
🟢 **Low variability**

**Description**

- Protein sequences of interest downloaded in FASTA format (viral proteomes, human antigens, allergen panels, or custom)
- Tiled into overlapping peptide fragments using in silico design tools; tile length and overlap parameters defined per study
- From v3 onward: **Dolphyn** machine-learning optimisation applied — selects tiles most likely to span reactive epitopes, compresses library size by ~78% while increasing the reactive peptide fraction from ~10% to ~31% (Liebhoff & Larman et al., *Nat Comms*, 2024)
- Without Dolphyn: naive uniform tiling analogous to PhIP-Seq A1 (pepsyn tool or equivalent)
- Each tile back-translated to a DNA sequence optimised for MIPSA vector expression

**Potential QC Procedures**

- ✓ Sequence verification of in silico library against source proteome (completeness check) — standard practice
- ◇ Confirm tiling coverage — no unintended gaps in protein sequence coverage
- ◇ GC content distribution of designed oligos
- ◇ Reference database version documented and locked

**Variability Introduced**

- Library design is entirely computational — variability minimal when proteomes are versioned and design parameters are fixed
- Dolphyn-optimised vs naive tiling produces materially different libraries — these are not interchangeable across studies
- Tile selection method is a systematic design decision, not a source of run-to-run variability

---

### Step A2.2 — Oligonucleotide Synthesis
🔴 **High variability**

**Description**

- Designed DNA sequences synthesised as a pooled oligonucleotide library on microarray-based platforms (e.g. Agilent, Twist Bioscience)
- Each oligo encodes one peptide tile plus flanking sequences compatible with the pDEST-MIPSA cloning site
- Synthesised pool eluted and amplified by PCR
- This step is structurally identical to PhIP-Seq Step A2 — the same synthesis biases apply

**Potential QC Procedures**

- ✓ NGS of amplified oligo pool to verify library complexity and representation evenness — standard practice
- ✓ Gel electrophoresis to confirm oligo length distribution — standard practice
- ◇ Flag severely underrepresented sequences before proceeding to cloning

**Variability Introduced**

- Microarray synthesis introduces systematic representation bias due to GC content, secondary structure, and positional effects on the array — identical limitation to PhIP-Seq A2
- Persistent source of library imbalance that downstream normalisation must address
- Batch-to-batch variation between synthesis runs is real and not fully correctable

---

### Step A2.3 — Cloning into pDEST-MIPSA Vector
🔴 **High variability**

**Description**

- Amplified oligo pool cloned into pDEST-MIPSA vector (not into a phage vector as in PhIP-Seq)
- Inserts fused downstream of the HaloTag and FLAG sequences — each tile will be expressed as a HaloTag-FLAG-tile fusion protein during cell-free translation in Phase B
- Recombinant plasmid pool transformed into E. coli and amplified; plasmid DNA extracted and purified
- Followed by plasmid linearisation at the I-SceI site (same as ORFeome Path Step A1.2) before proceeding to Phase B

**Potential QC Procedures**

- ✓ Plasmid titration and insert verification by PCR — standard practice
- ◇ Deep sequencing of plasmid pool to confirm library complexity maintained through cloning
- ◇ Calculate fraction of clones carrying insert vs empty vector
- ✓ Gel electrophoresis to confirm complete linearisation before Phase B — confirmed in Credle & Larman 2022

**Variability Introduced**

- Cloning efficiency is not uniform — tiles encoding poorly expressed or toxic peptides may be selectively lost
- Compounds synthesis bias from A2.2: library complexity after cloning is reliably lower than designed
- Key difference from PhIP-Seq: cloning into pDEST-MIPSA (plasmid) is more tractable than cloning into phage, but the same selective loss mechanisms apply

---

> **Both paths converge here.** From Phase B onward — IVT, self-assembly, immunoprecipitation, sequencing, and data analysis — the protocol is the same regardless of whether the library is ORFeome or peptide tile. The linearised pDEST-MIPSA plasmid pool is the common input to Phase B in both cases.

---

## Phase B: Self-Assembly — IVT, Reverse Transcription, and Cell-Free Translation

> ⚠ Steps B1–B3 are tightly coupled. The RNA produced in B1 serves simultaneously as the translation template and the molecule to which the cDNA barcode attaches in B2, before self-assembly occurs during translation in B3.

---

### Step B1 — In Vitro Transcription (IVT)
🟡 **Medium variability**

**Description**

- Linearised pDEST-MIPSA pool used as template for in vitro transcription using T7 RNA polymerase
- Produces pool of RNA molecules, each containing: UCI barcode + HaloTag coding sequence + ORF sequence
- RNA serves a dual role: translation template AND the molecule to which the UCI cDNA barcode is attached in B2
- RNA purified and stored at −80°C in small aliquots to prevent freeze-thaw degradation

**Potential QC Procedures**

- ✓ RNA yield and concentration (Qubit) — confirmed standard practice
- ◇ RNA integrity check (Bioanalyzer or TapeStation) — degraded RNA reduces translation efficiency
- ◇ Confirm expected size distribution of transcripts across ORF sizes
- ◇ RNase contamination check — strict RNase-free technique required throughout

**Variability Introduced**

- RNA is inherently less stable than DNA — the most chemically fragile intermediate in the pipeline
- Transcription efficiency varies by template GC content and secondary structure
- RNase contamination is a critical risk — a single contaminated reagent can destroy the entire pool
- RNA degradation between IVT and the coupled RT/translation step is a meaningful source of per-ORF efficiency variability

---

### Step B2 — Reverse Transcription with HaloLigand-Conjugated Primer
🔴 **High variability**

**Description**

- RT primer conjugated to a succinimidyl ester–haloalkane (HaloLigand) moiety annealed to each RNA at position −32 nt upstream of the AUG start codon
- Position −32 empirically determined: primers within 20 nt of the RBS interfere with ribosome assembly and abolish translation; −32 is closest position that does not reduce translation yield
- Reverse transcriptase synthesises cDNA from the UCI barcode region, producing an RNA:cDNA hybrid
- HaloLigand-conjugated cDNA is now physically proximal to the ribosome entry site, poised for cis coupling during translation in B3

**Potential QC Procedures**

- ◇ Confirm RT primer incorporation by gel or bioanalyzer
- ◇ RT efficiency assessed by qPCR on cDNA product
- ◇ Confirm HaloLigand coupling chemistry is intact (supplier QC certificates; functional validation by test translation with FLAG western blot)
- ◇ Primer batch QC — each new lot should be validated before library-scale use

**Variability Introduced**

- Most technically demanding and variable step in the MIPSA protocol
- RT efficiency varies with RNA secondary structure, template GC content, and primer batch quality
- HaloLigand conjugation chemistry must be intact — degraded or improperly conjugated primer breaks the barcode–protein link entirely
- Batch-to-batch variability in the HaloLigand-conjugated primer is a real and consequential risk

---

### Step B3 — Cell-Free Translation and Cis HaloTag Self-Assembly
🔴 **High variability**

**Description**

- RNA:cDNA hybrid pool added to E. coli cell-free translation system
- Release factors excluded from the reaction to stall ribosomes at stop codons — improves cis-coupling efficiency
- Translation order: HaloTag (N-terminal) → FLAG tag → ORF protein
- As nascent HaloTag domain folds, it forms an irreversible covalent bond with the HaloLigand on the nearby UCI cDNA — linking barcode to protein in cis
- ~50% of protein-UCI conjugates form in cis; ~50% form in trans (barcode from one molecule coupling to HaloTag of a nearby different molecule)
- 50% cis efficiency is sufficient for assay performance — trans events contribute to characterisable background estimated by mock IP controls

**Potential QC Procedures**

- ✓ Anti-FLAG western blot to confirm translation of HaloTag-FLAG-ORF fusion protein — confirmed in Credle & Larman 2022
- ✓ Test IP of a known positive antigen (e.g. TRIM21, NT5C1A) with validated positive serum — confirmed in Credle & Larman 2022
- ◇ Estimate cis coupling efficiency by HaloLigand bead pull-down followed by qPCR of UCI region
- ◇ Negative control: translation without HaloLigand-conjugated primer (should produce protein but no protein-UCI conjugate)

**Variability Introduced**

- Cis vs trans coupling ratio is inherently stochastic and varies by ORF size — larger proteins are released from ribosome before HaloTag matures, favouring trans coupling
- 50% cis efficiency is an average; actual rate varies per ORF — fundamental source of UCI-level efficiency variation
- This step is the primary statistical rationale for the proposed probe-aggregation model: UCI-level efficiency must be estimated, not assumed uniform
- Release factor exclusion improves cis rate but introduces ribosome stalling — a biological trade-off managed empirically

---

## Phase C: Immunoprecipitation

---

### Step C1 — Sample Preparation and Dilution
🟡 **Medium variability**

**Description**

- Patient or control serum/plasma diluted in IP buffer (typically 1:100 to 1:500)
- Processed in 96-well plate format
- Column 12 reserved for internal controls: pooled serum controls (same lot across all plates) and PBS mock IP controls (beads only, no serum)
- Each sample individually compared against the full set of mock IP controls in statistical analysis

**Potential QC Procedures**

- ✓ Column 12 control layout (serum controls + mock IPs) — confirmed in Infinity Bio VirSIGHT report
- ✓ Consistent dilution factor across all samples — confirmed standard practice
- ◇ Confirm sample identity and volume
- ◇ Flag samples with haemolysis or lipaemia (affects IgG recovery)

**Variability Introduced**

- Sample quality (haemolysis, freeze-thaw history) varies across biobank specimens
- Dilution factor affects the competition landscape — higher dilution reduces sensitivity but improves specificity
- Column 12 control design is the most explicit QC structure documented in the Infinity Bio operational protocol

---

### Step C2 — Incubation of Serum with MIPSA Library
🟡 **Medium variability**

**Description**

- Diluted serum mixed with MIPSA protein-barcode library and incubated at 4°C with rotation for 16–18 hours
- Serum antibodies bind to cognate antigens in solution
- Solution-phase binding kinetics more closely approximate native immunoassay conditions than phage display
- Conformational epitopes accessible — proteins are soluble and unconstrained by phage capsid geometry

**Potential QC Procedures**

- ✓ Positive control serum with known reactivity included on each plate — confirmed standard practice
- ✓ Mock IP (PBS) controls in column 12 — confirmed in VirSIGHT report
- ◇ Consistent library input across samples (normalised by protein concentration)
- ◇ Temperature and rotation speed logged for each run

**Variability Introduced**

- Total IgG varies between individuals — affects competition for library members
- Temperature fluctuations and rotation inconsistencies affect binding kinetics
- 16–18 hour window introduces time-of-day variability if plates not processed simultaneously

---

### Step C3 — Immunoprecipitation with Protein A/G Magnetic Beads
🔴 **High variability**

**Description**

- Protein A/G magnetic beads added to capture IgG–antigen complexes
- Beads separated magnetically and washed 3–5 times
- Antibody-bound MIPSA library members (protein + covalently linked UCI cDNA barcode) retained on beads
- Structurally equivalent to PhIP-Seq C3, but targeting soluble protein-DNA conjugates rather than intact phage particles

**Potential QC Procedures**

- ✓ Mock IP control (PBS) run in parallel — confirmed in VirSIGHT report; primary background reference for all statistical comparisons
- ✓ Positive control antigen recovery confirmed — confirmed standard practice
- ◇ Wash number and volume consistent across all samples
- ◇ Bead lot inter-lot validation before use in new library experiments

**Variability Introduced**

- Same bead-capture variability as PhIP-Seq C3
- Additional risk: MIPSA proteins are soluble — incomplete washing releases more background than from intact phage particles
- Trans-coupled barcode-protein pairs (from B3) contribute to non-specific background not distinguishable from true signal at the individual library member level
- Bead lot variation is a batch-level risk

---

## Phase D: Sequencing Library Preparation and NGS

---

### Step D1 — PCR Amplification of UCI Barcodes
🟡 **Medium variability**

**Description**

- After washing, beads resuspended and UCI cDNA barcodes amplified by PCR
- Universal primers flanking UCI region used, with Illumina adapters and sample-specific indices added
- UCIs are short (~50 bp) — PCR is efficient and requires fewer cycles than for PhIP-Seq inserts
- Each sample generates one indexed sequencing library

**Potential QC Procedures**

- ✓ Gel electrophoresis to confirm PCR product at expected size — confirmed standard practice
- ✓ Quantify libraries (Qubit or qPCR) — confirmed standard practice
- ◇ Check for primer dimer contamination
- ◇ Confirm index diversity across multiplexed samples

**Variability Introduced**

- PCR cycle number affects representation — more cycles introduce greater amplification bias
- Jackpot effects possible if input barcode counts are very low
- Short UCI amplicons are less susceptible to length bias than PhIP-Seq inserts

---

### Step D2 — Next-Generation Sequencing
🟢 **Low variability**

**Description**

- Multiplexed libraries pooled and sequenced on Illumina (NextSeq or HiSeq)
- Target: 1–3 million reads per sample
- Single-end reads standard — UCIs are short fixed-length sequences, paired-end adds no information
- Higher alignment rates than PhIP-Seq due to short, structured barcode targets

**Potential QC Procedures**

- ✓ Per-sample aligned read count (target: >500,000; low counts trigger repeat) — confirmed in VirSIGHT report
- ✓ Sequencing quality scores (Q30 threshold) — standard Illumina QC
- ✓ Alignment rate to validated UCI dictionary (target: >90%) — confirmed in VirSIGHT report
- ✓ Per-sample Pearson correlation with serum controls (target: ≥0.90 among column 12 controls) — confirmed in VirSIGHT report

**Variability Introduced**

- NGS is the most reproducible step in the pipeline
- Main risk: uneven sample pooling before sequencing run
- Run-to-run variability is low given standardised Illumina chemistry

---

## Phase E: Data Analysis

---

### Step E1 — Barcode-to-ORF Mapping and Count Matrix Generation
🟢 **Low variability**

**Description**

- Raw reads aligned using Bowtie2 to a validated dictionary of mono-associated barcode-ORF pairs
- Only UCIs confirmed to associate with a single ORF (by prior sequencing of unselected library) are retained
- Count matrix generated: rows = UCI-ORF pairs, columns = samples, values = read counts
- Multi-UCI structure per ORF is explicit in the matrix — each ORF has ~10 rows (one per UCI)

**Potential QC Procedures**

- ✓ Alignment rate to validated UCI dictionary (target: >90%) — confirmed in VirSIGHT report
- ✓ Per-sample read depth — confirmed in VirSIGHT report
- ✓ Pearson correlation heatmap of serum controls across plates (target: ≥0.90) — confirmed in VirSIGHT report
- ◇ Flag samples with anomalously low read counts or low inter-control correlation
- ◇ PCA — mock IPs must cluster separately from serum samples

**Variability Introduced**

- Alignment is a deterministic computational step — minimal variability for fixed aligner parameters
- Validated UCI dictionary must be version-controlled and matched to the specific library lot used
- UCI dictionary validation is a one-time upstream step with its own sequencing QC

---

> **Hit calling differs by library type:**
>
> - **ORFeome libraries (HuSIGHT):** Each ORF has ~10 UCI rows in the count matrix. An antigen is called reactive if multiple UCIs for the same ORF are independently significant — the multi-UCI confirmation rule.
>
> - **Peptide tile libraries (VirSIGHT):** Each antigen is covered by multiple overlapping tiles (analogous to PhIP-Seq). An antigen is called reactive if multiple overlapping tiles are enriched. This is structurally closer to the PhIP-Seq confirmation logic.
>
> In both cases the current pipeline operates at the per-probe level (per-UCI or per-tile) without a formal probe-aggregation model. This is the primary analytical limitation motivating the proposed RMA-like approach.

### Step E2 — Hit Calling with edgeR
🟡 **Medium variability**

**Description**

- Each sample compared against the full set of mock IP controls using edgeR (negative binomial model)
- Fold-over-background (FOB) calculated per UCI per sample
- Hits defined at FDR-adjusted p-value < 0.05
- Secondary confirmation rule applied: antigen called reactive if multiple UCIs targeting same ORF are independently significant
- Current pipeline treats each UCI as an independent observation — no formal probe-aggregation model is applied

**Potential QC Procedures**

- ✓ FOB distribution check (approximately log-normal with heavy right tail) — confirmed standard practice
- ✓ Positive control reactivity confirmed at expected FOB — confirmed in VirSIGHT report
- ✓ Mock IP Pearson correlation ≥ 0.90 among column 12 controls — confirmed in VirSIGHT report
- ◇ Mock IPs cluster separately from serum samples in PCA

**Variability Introduced**

- Current per-UCI, per-test approach does not model UCI-level efficiency — variation from B3 (cis vs trans coupling) is absorbed into noise rather than explicitly estimated
- Multi-UCI confirmation rule is a conservative heuristic — reduces false positives at cost of sensitivity for antigens with low cis-coupling efficiency or few UCIs
- FDR-based thresholds carry all the limitations of p-value-derived inference at scale
- Primary analytical limitation motivating the proposed RMA-like probe-aggregation model

---
