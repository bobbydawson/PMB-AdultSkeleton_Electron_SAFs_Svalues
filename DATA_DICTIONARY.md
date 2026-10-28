# Data dictionary and provenance

`data_dictionary.csv` lists every tidy fitting column. Numeric descriptors refer to the specific model named by `site`; repeated energies share geometry and are not independent samples. AM and AF refer to male and female reference phantoms. `_old` names identify two reconstruction controls excluded from fitting; `_new` is retained. `site` is the validation component key and may represent a bone part used in larger source-phantom assemblies.

Native geometry volumes V_* are mm^3; S_* are mm^2. BS_BV and BS_TV are mm^-1. Intercept lengths and CSDA ranges explicitly ending `_cm` are cm; TbTh_mm and TbSp_mm are mm, and TbN_per_mm is mm^-1. `m_RBM_g` and `m_endo_g` are model-domain g, whereas `TargetMass` in adult AF/SAF grids is full target-region kg. `tables/target_masses_g.csv` reports full target masses in g. Confusing these two mass scales changes SAFs by orders of magnitude.

The fits use the cell fractions `cf` and `tbvf` from the measured morphology table; `_nominal` columns record construction inputs and are not substituted into the fits. `bvtv_from_vols` and `cf_from_vols` are descriptor cross-checks, not replacement inputs. Shallow fractions are conditioned on the relevant marrow tissue and may be undefined where that tissue has zero volume. Such zero-source/zero-RBM-target fitting rows are explicitly flagged. All dimensionless absorbed fractions are energy-deposition fractions, not probabilities of individual-particle absorption.

Electron-only: `phi_M` is the native component spongiosa self-AF after the adopted per-macrogroup clipping/mass-weighted assembly; `AF_site=AF*phi_M` is the composed component AF. `m_spong_g`, `V_spong_cm3`, `L_spong_cm` describe its native macro geometry. `sp_*` are mass collision stopping powers (MeV cm^2 g^-1); `f_eq_*` and `f_meq_RBM` use them at the initial source energy. The 0.7 MeV macro value is log-energy/log-AF interpolated; flags in the regenerated macro predictions exclude it from validation.

Adult grids are wide by energy, with Group/BoneSite/Particle/Source/Target as keys; zero-energy values are analytical anchors. S-value files use mGy per MBq s (equivalently mGy/(MBq s)) and emission spectra are normalized per parent transformation. Parent contributions can only be combined across particle papers when source/target definitions, normalization and units match; doing so does not add photons or progeny kinetics.

NIST ASTAR/ESTAR snapshots are numeric public-source input records cited in the paper. Spectra are the author-supplied per-decay records consumed by the existing calculator; their file hashes and energy/yield summaries are retained. Source/target and model mapping tables are author-supplied common inputs. Absence of raw geometry histories and transport histories is an explicit input boundary.


## data/electron_svalues_ws_progeny.csv

Same columns as `data/electron_svalues_ws.csv`; `Isotope` is one of the 19 progeny of the ten alpha-emitting radionuclides. Parent-only values in mGy MBq^-1 s^-1.

## results/microscale_run_statistics.csv

`model`, `source` (TBS, TBV, RBM, YBM), `energy_index` (1 to 26 on the microscale energy grid), `histories`, `termination` (`1% criterion met`, `10^6 history limit`, or `job time limit`), `rel_err_*` (relative statistical uncertainty of the tallied energy deposition in each region and in the endosteum and RBM targets, from the PHITS [T-Deposit] tally; blank where the region does not exist).
