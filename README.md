# Assessing the Accuracy of the NeuroBooster Array for GBA1 Variant Detection
#Author: Marco Toffoli (m.toffoli@ucl.ac.uk)
#Last modified 08th September 2026
## GP2 ❤️ Open Science 😍

Accuracy of NeuroBooster Array (NBA) genotype calls for GBA1 variants, benchmarked
against orthogonal sequencing in three GP2 cohorts.

## Cohorts (truth sets)
| Cohort                                    | Truth source file                                        |
|-------------------------------------------|----------------------------------------------------------|
| PD-Frontline                              | `RAPSODIDNA-KitsAndSequencing_DATA_LABELS_2026-06-10_1747.csv` |
| PPMI                                      | `PPMI_export_final_no_missing.csv`                       |
| LongNext (also referred to as PDGEN)      | `GBA_variants_Pavia_NBA_toffoli.csv`                     |

Array data: GP2 tier-2 EU release 11 raw genotypes (plink2), accessed on the
Verily Workbench.

## Contents
```
code_for_analysing_all_data.ipynb   full analysis pipeline
```

## Pipeline (notebook, in order)
1. Load and harmonise the three truth sets; normalise variant aliases.
2. Subset plink2 filesets to GBA1 region and to truth samples.
3. Map probes to variants by GRCh38 position / rsID (`variant_map`).
4. Merge array genotypes with truth calls; classify each call TP / FP / TN / FN.
5. Per-probe and pooled metrics; select best probe per variant.
6. Attach GP2 release-11 phenotypes; allele frequencies and carrier counts by
   cohort and phenotype group (PD / Control / Other).
7. Targeted checks: N409S homozygotes, FP breakdown by variant and cohort,
   unnamed GBA1-region probes.

## Key outputs
| File | Content |
|------|---------|
| `gba_probe_metrics.csv`            | TP/FP/TN/FN, sensitivity, specificity per probe |
| `gba_best_probe_per_variant.csv`   | selected probe per variant |
| `gba_false_positives.csv` / `gba_false_negatives.csv` | discordant calls |
| `gba_carrier_reconciliation.csv`   | carrier-level concordance |
| `gba_allele_freq_by_cohort*.csv`   | allele frequencies by cohort / phenotype group |
| `gba_carrier_counts_by_cohort_group.csv` | carriers by cohort and group |
| `gba_phenotype_counts_by_cohort.csv` | phenotype distribution |
| `gba_all_unique_probes.csv`        | all GBA1-region probes on the array |
| `gba_sample_level_calls.csv`          | table with all calls for each sample included in this analysis |

## Requirements
| Software/python package | Version               | RRID            |
|-------------------------|-----------------------|-----------------|
| Python 3                | v3.10.19              | RRID:SCR_008394  |
| plink2                  | v2.0.0-a.6.9LM 64-bit Intel (29 Jan 2025) | RRID:SCR_001757 |
| pandas                  | v2.3.3               | RRID:SCR_018214  |
| numpy                   | v2.4.3               | RRID:SCR_008633  |
| pgenlib                 | v0.93.0              | RRID:SCR_001757  |

## Repository orientation

<pre> NBA_GBA1_accuracy/
  ├── analyses/
  |     └── 00_GBA1_NBA_accuracy.ipynb
  ├── LICENSE
  └── README.md
</pre>

## Analysis notebooks

The whole analysis is contained in a single notebook, run top to bottom. It is not
split by cohort: the three truth sets (PD-Frontline, PPMI, LongNext) are harmonised
in the first sections and then processed together through one shared pipeline.

| Directory   | Notebooks                       | Description |
|-------------|---------------------------------|-------------|
| `analyses/` | `00_GBA1_NBA_accuracy.ipynb`    | Full pipeline: harmonisation of the three sequencing truth sets, extraction of the GBA1 region from the GP2 release 11 plink2 filesets, probe-to-variant mapping, TP/FP/TN/FN classification, per-probe and pooled accuracy metrics, best-probe selection, and phenotype-linked allele frequency and carrier tables. |

### Sections within the notebook

| Section | Description |
|---------|-------------|
| Setting paths | Workspace bucket paths and inputs |
| PD-Frontline / PPMI / LongNext | Load and harmonise each sequencing truth set |
| Merging everything together | Single harmonised truth table, variant alias normalisation |
| Filtering plink files for GBA1 only | Subset release 11 plink2 filesets to the GBA1 region |
| Filter truth to samples present in plink2 | Restrict to samples genotyped on the NBA |
| Building table to cross-reference variants | `variant_map`: probes to variants by GRCh38 position / rsID |
| Loading genotypes / Merge with truth set | Extract array genotypes and join to sequencing calls |
| Per-probe and pooled metrics | TP/FP/TN/FN, sensitivity, specificity |
| Sample phenotypes by cohort | Attach GP2 release 11 master key phenotypes |
| Allele frequency table / Carriers by group | Frequencies and carrier counts by cohort and phenotype group |
| Best probe per variant | Probe selection per variant |
| Accuracy for homozygous N409S | Targeted check on homozygotes |
| Probe sanity check / full probe list | Unnamed GBA1-region probes on the array |
| False positive calls per variant per cohort | FP breakdown |
| Sample-level call table | Sequencing reference vs NBA call per sample |

---

## Notes
- Run inside the Verily Workbench; paths in the notebook are relative to the
  workspace bucket mount.
- pgenlib will need to be installed separately (eg %pip install pgenlib)
- pgenlib is the Python API shipped inside plink-ng 2.0
