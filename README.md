# FairWard supporting evidence

Hospitals need synthetic data to study patient outcomes and audit group disparities while source records stay local. However, privacy noise can distort protected-group proportions, and pooled estimates can confuse a hospital's patient mix with its clinical practice. For instance, before calibration FairWard's clipped composition estimate gave, on average over twenty eICU fits, 23.006% of the fitted mass to the Other, Unknown and Missing gender categories, whose training proportion is 0.0225%.

Existing private generators address these problems only in part. In Table I of the paper, AIM (McKenna et al., 2022) and PreFair (Pujol et al., 2023) are not marked as federated, and FLAIM (Maddock et al., 2024) is not marked with a synthesis fairness objective. Only FairWard is marked for federation, differential privacy and fairness together, under simulated trusted coordination. To address both problems, FairWard reconstructs hospital count distributions from four private measurements of bounded counts. A trusted coordinator adjusts pooled clinical estimates for each hospital's patient mix and calibrates protected-group proportions from the same measurements. Clinical fields are then sampled from one distribution shared across protected groups within each hospital and declared context.

This repository holds the evidence behind the results of *FairWard: Federated Synthetic Data Generation for Healthcare* by Md Jannatul Rakib Joy, Viet Vo and Caslon Chua. It holds this README and the executed notebook `FairWard_supporting_evidence.ipynb`, whose saved outputs show every check. The archive of aggregate result files that the notebook reads, `FairWard_data.zip`, is not included here. The notebook rebuilds Tables III to IX and redraws Figs. 2 and 3. It compares 281 numbers printed in those tables and 182 further numbers printed in the text with values recomputed from the released files. It also reads 27 printed numbers from released audit records and tests 92 comparative statements. All agree. Four printed numbers are not in the released files, and the notebook lists them as withheld.

## Main findings

- **Composition.** Calibration lowers eICU protected-marginal total variation (TV) from 0.22984 to 0.01663 across twenty shared transcripts, with no additional private query (Section V-B, Table V). Calibration does not restore rare groups. Their eICU mass stays at 1.248% against a training proportion of 0.0225%, and an observed rare category disappears in eighteen of twenty fits.
- **Finite-output fairness.** Calibration lowers sampled Female/Male conditional TV in all three reused clinical cohorts. FairWard remains worse than PreFair in eICU and MIMIC-III and worse than FLAIM in MIMIC-III, while it improves on all three common-policy baselines in Texas. On SUPPORT2, its sampled conditional TV of 0.0843 exceeds that of AIM-C (0.0623) and PreFair (0.0639).
- **Reconstruction.** On frozen SUPPORT2, patient-mix adjustment lowers sparse-cell mean absolute error (MAE) from 0.1490 to 0.1364 against matched pooled borrowing, as means over three fits. AIM-C, PreFair and FLAIM have lower sparse error, and AIM-C, PreFair and global-only estimation have lower overall error. On the reused cohorts, FairWard improves on local-only estimation, but global pooling has lower overall MIMIC-III MAE (0.05091 against 0.05467).
- **Prediction.** FairWard's logistic area under the receiver operating characteristic curve (AUROC) stays below the leading private comparator in each reused cohort (Section V-B, Table VI). The values are 0.69423, 0.71132 and 0.70250 against 0.71235, 0.72243 and 0.70484 for eICU, MIMIC-III and Texas.
- **Locked holdout.** On 5,749 held-out CRASH-2 patients, the contextual tree extension, whose weights were selected on reused SUPPORT2, has the lowest mean clinical-pattern log loss (2.1653 against 2.2481 for AIM-C and 2.1984 for PreFair). Under matched training-only tuning, its AUROC is lower (0.7861 against 0.7973 and 0.7963), and it misses the prespecified fairness and detection gate.
- **Privacy audit.** The four-case attack audit of the primary four-query output, run on four public artificial neighboring pairs, detects no declared confidence-supported bound violation and detects all eight unsafe controls. Attack AUROC ranges from 0.4411 to 0.5634, and the test false-positive rate (FPR) reaches 4.30% against a 1% calibration target.

The paper reads these results as gains in representation and reconstruction under a declared fairness policy, while predictive utility needs separate evaluation. It treats the attack audit as finite-power evidence rather than a leakage percentage or a privacy certificate.

## Paper-to-notebook map

Each numbered notebook section ends with a check table, and the notebook's Released files table lists every result file with the sections that read it.

| Paper element | Notebook section |
| --- | --- |
| Table III, with the cohort sizes and run counts of Section IV | 1 |
| Table IV and the matched-control results of Section V-A | 2 |
| Fig. 2 and the private-generator results of Section V-A | 3 |
| Table V and the composition results of Section V-B | 4 |
| Finite-output conditional TV in Section V-B | 5 |
| Table VI and Fig. 3 | 6 |
| Table VII | 7 |
| Table VIII and the SUPPORT2 results of Section V-B | 8 |
| Contextual tree and CRASH-2 results of Section V-B | 9 |
| Richer ordinal variant in Section V-B | 10 |
| Table IX and the stress results of Section V-C | 11 |
| Cost results of Section V-C | 12 |
| Privacy evidence of Section V-D | 13 |
| Abstract, Section VI and Section VII | 14 |

## Running the notebook

To re-run the notebook, place the archive `FairWard_data.zip` next to it, because this repository does not include the archive. The notebook needs Python 3.11 or newer. It was executed with Python 3.11.9 and the package versions pinned below, and it also passed with pandas 3.1.1, numpy 2.4.6 and matplotlib 3.11.2. Run it from the repository folder, because it opens `FairWard_data.zip` relative to the notebook.

```bash
python -m venv .venv
source .venv/bin/activate        # on Windows: .venv\Scripts\activate
python -m pip install pandas==2.3.3 numpy==2.4.2 matplotlib==3.10.8 ipykernel==7.2.0 nbconvert==7.17.0
python -m nbconvert --to notebook --execute --output rerun.ipynb FairWard_supporting_evidence.ipynb
```

The last cell raises an error, and the command exits with a nonzero status, if any recomputed value differs from the printed value copied into the notebook. These printed values come from the manuscript version compiled on 9 November 2026. On Windows, nbconvert may also print a harmless pyzmq RuntimeWarning about the Proactor event loop.

## Released data and privacy boundaries

The data archive holds aggregate results only: summaries across fits, per-fit metrics, paired differences, audit and verification records, the SUPPORT2 study report and protocol, and timing records. It contains no patient record, synthetic record, fitted model, private measurement transcript or prediction file. Nine of its files were trimmed, and the notebook's Released files table states the change for each. The trimming removed hashes of private input files, run configurations, code hashes and timestamps. It also removed the real-data group shares and counts listed in the manifest, including the eICU training counts of the rare gender categories, which fall below ten. Every retained value keeps its original digits.

Six SHA-256 strings remain in the archive's verification, timing and report files. They fingerprint the authors' code, protocol and artificial-data files, which are not part of this repository. Several files index fits as 0, 1 and 2, and the baseline generators ran with fixed research seeds tied to these indices. No synthetic data or private measurements from any fit are released.

The aggregates are research results rather than a differentially private release. Each private fit targets epsilon 3 and delta 1e-6 under the paper's assumptions, namely a trusted coordinator that receives exact bounded counts, the stated contribution bounds and ideal Gaussian noise. The rounded implementation's fidelity certificate remains unvalidated, FLAIM's budget assumes public cohort and client sizes, and Texas protects discharge records rather than patients. Repeated fits compose, so twenty clinical repeats compose to approximately epsilon 15.7031 at delta 1e-6 for each cohort, in addition to earlier development releases. The twelve SUPPORT2 generator fits compose to a nominal epsilon of about 11.65, and the accounting excludes development selection and the exact research audits.

The clinical sources remain under their own access terms.

- **eICU and MIMIC-III.** Credentialed access through PhysioNet ([eICU](https://physionet.org/content/eicu-crd/), [MIMIC-III](https://physionet.org/content/mimiciii/)).
- **Texas-100X.** Derived from the Texas Hospital Inpatient Discharge Public Use Data File for 2006 ([Texas-100X](https://github.com/bargavj/Texas-100X)).
- **SUPPORT2.** Data obtained from <http://hbiostat.org/data> courtesy of the Vanderbilt University Department of Biostatistics.
- **CRASH-2.** London School of Hygiene and Tropical Medicine Data Compass ([doi:10.17037/DATA.10](https://doi.org/10.17037/DATA.10)).

## Scope and limitations

The notebook checks reporting rather than replication. It shows that each checked value follows from the released aggregates, but it cannot refit a generator, redraw privacy noise or rescore predictions. Those steps need the FairWard implementation, which this repository does not include. For every clinical cohort except SUPPORT2, they also need records held under access terms. Method constants, design settings and results reported only in the authors' companion document are outside its scope.

Section VI of the paper lists the limitations in full, and the main ones apply directly here. The eICU, MIMIC-III and Texas partitions informed development, so their results are exploratory, and the tree selection on reused SUPPORT2 is also exploratory. MIMIC-III divides one center into ten simulated clients, SUPPORT2 and CRASH-2 lack hospital identifiers, and every federated operation is simulated locally. The fairness contexts lack clinical adjudication, and conditional parity can remove genuine group-specific risk relationships. No common context supports all five protected categories, some rare groups receive zero fitted mass, and finite attacks prove neither zero leakage nor a 1e-6 failure bound.

## Citation

Please cite the paper when you use this evidence.

```bibtex
@misc{joy2026fairward,
  title  = {{FairWard}: Federated Synthetic Data Generation for Healthcare},
  author = {Joy, Md Jannatul Rakib and Vo, Viet and Chua, Caslon},
  year   = {2026},
  note   = {Manuscript}
}
```

The datasets have their own references: eICU (Pollard et al., *Scientific Data*, 2018), MIMIC-III (Johnson et al., *Scientific Data*, 2016), Texas-100X (Jayaraman), SUPPORT (Knaus et al., *Annals of Internal Medicine*, 1995) and CRASH-2 (CRASH-2 Trial Collaborators, *The Lancet*, 2010).

Claude (Anthropic) assisted in assembling this repository's notebook and README from the authors' result files. The paper's own disclosure covers the manuscript.
