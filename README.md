# Adult Skeleton Electron SAFs and S-values

Reference skeletal dosimetry data for the ICRP reference adult male and female, accompanying:

> Dawson R J, Choi C, Shin B, Wang Y, Tran-Gia J, Salas Ramirez M, Theisen A-L, Lassmann M, Jokisch D W, Bertolet A, Bolch W E.
> *Mesh-based detailed skeletal models for the ICRP Reference Adults: Part 3. electron specific absorbed fractions.*
> Physics in Medicine & Biology (submitted).

The values were calculated by Monte Carlo transport (PHITS) in the 73 tetrahedral-mesh spongiosa models of Part 1 of the series (Choi et al 2025 Phys. Med. Biol. 70 215012), which carry site-specific trabecular bone volume fraction and marrow cellularity and an explicit 50 um endosteal layer, together with the mesh-type reference computational phantoms for cortical bone sources and the long-bone medullary cavities. The companion repository is PMB-AdultSkeleton_Alpha_SAFs_Svalues (alpha particles, Part 2). This repository contains data only.

## Source and target regions, skeletal categories

Six source regions: trabecular bone surface (`TBS`), trabecular bone volume (`TBV`), red bone marrow (`RBM`), yellow bone marrow (`YBM`), cortical bone volume (`CBV`), and cortical bone surface (`CBS`, set equal to CBV). Two target regions: `RBM` and `Endosteum` (the 50 um layer of marrow along the trabecular surfaces and the medullary-cavity surfaces of the long-bone shafts). Source-target combinations are written target from source, for example RBM <- TBS.

Forty skeletal categories per phantom (`BoneSite`): 34 skeletal sites, the five lumbar vertebrae individually (`L1` to `L5`, already contained in `Lumbar_Vertebrae`), and `Whole_Skeleton`. `Group` is `AM` (adult male) or `AF` (adult female).

## Specific absorbed fractions and absorbed fractions

- `data/electron_SAF_adult.csv`: SAFs in **kg^-1**.
- `data/electron_AF_adult.csv`: the corresponding dimensionless AFs.

Both are wide-format tables with a header row:

```
Group,BoneSite,Particle,Source,Target,TargetMass,<E0>,<E1>, ... ,<En>
```

One row per phantom, skeletal category, source, and target (960 rows). `TargetMass` is in kg. The energy columns (in **MeV**) are the zero-energy limit and 25 energies from 0.01 to 10 MeV. The zero-energy column holds the geometric limit as the particle range tends to zero (for example one half for Endosteum <- TBS as an AF). Whole-skeleton values are source-weighted sums over the 34 sites (`tables/source_emission_fractions.csv`) divided by the whole-skeleton target mass. For spongiosa sources the microscale AF is multiplied by the macroscale self-AF of the spongiosa to account for electron escape.

## S values

`data/electron_svalues_ws.csv`: whole-skeleton S values in **mGy/MBq-s** for the electron emissions (discrete electrons plus beta spectra, ICRP Publication 107) of Lu-177, Y-90, Sr-89, Sm-153, I-131, Tb-161, Ac-225, At-211, Bi-212, Bi-213, Pb-212, Ra-223, Ra-224, Th-227, Th-228, and U-230, for all twelve source-target combinations and both phantoms (384 rows). Columns: `Element,Isotope,Phantom,Source,Target,Total,Photon,Electron,Beta,Alpha,AlphaRecoil,Group`; `Total = Electron + Beta`, and the photon, alpha, and recoil columns are zero by construction. The values are PARENT-ONLY: progeny are excluded. They include the energy deposited by bremsstrahlung from the electrons.

`data/electron_svalues_ws_progeny.csv`: parent-only electron S values, in the same format, for the 19 progeny of the ten alpha-emitting radionuclides (Fr-221, At-217, Po-213, Tl-209, Pb-209, Rn-219, Po-215, Pb-211, Bi-211, Tl-207, Po-211, Rn-220, Po-216, Tl-208, Po-212, Th-226, Ra-222, Rn-218, Po-214; 456 rows). The chain of U-230 is followed to Pb-210, which is omitted with its progeny. The electron S value of a complete chain per parent transformation is the sum of the member values weighted by the branching fractions leading to each member and by the ratio of each member's time-integrated activity to the parent's (unity in equilibrium). `supplementary_tables/table_S11_progeny_electron_svalues.csv` gives the same values in wide format with the parent chains named. The companion alpha repository gives the alpha particle S values of the same chains.

## Crossfire among skeletal sites

- `data/macroscale_crossfire_destination_matrix.csv`: for each phantom, spongiosa source group, and energy, the fraction of the emitted energy deposited in the marrow-bearing regions (spongiosa and medullary cavities) of every skeletal site. Columns: `phantom,group,src_site,energy,dest_site,af`.
- `data/macroscale_spongiosa_aggregate_enhancement.csv`: for activity distributed uniformly per unit spongiosa mass, the self-only and full AFs to the marrow-bearing regions and the fractional enhancements. The enhancement is below 1% up to 2 MeV and reaches 11% (male) and 13% (female) at 10 MeV. The tabulated SAFs and S values do NOT include crossfire; they include electron escape from the spongiosa.
- `data/electron_ranges.csv`: NIST ESTAR CSDA ranges, radiation yields, and collision stopping powers of the surrogate materials used as inputs to the morphometric functions.

## Target masses and source fractions

- `tables/target_masses_g.csv`: RBM and endosteum masses (g) of each skeletal site and of the whole skeleton, measured from the meshes.
- `tables/source_emission_fractions.csv`: fraction of the emissions of each source region assigned to each skeletal site. Volume sources follow the source mass; TBS follows the trabecular surface area of each site, estimated as the interface area of each microscale model multiplied by the ratio of the spongiosa volume it represents to the model volume and summed over the models of the site (16.0 m^2 in the male and 12.4 m^2 in the female in total); CBS equals CBV.

## Morphometric functions

- `data/morphometric_features.csv`: morphometric parameters of the 75 microscale models (73 current models and two older reconstructions): tissue volumes (mm^3), interface areas (mm^2), BV/TV (`tbvf`), cellularity factor (`cf`), mean intercept lengths (cm), and shallow-marrow fractions.
- `data/dataset.csv`: the microscale AFs with the parameters and particle ranges used to fit the functions (one row per model, combination, and energy).
- `MODEL_SPEC.md`, `model_spec.json`: the functions as given in the article. `DATA_DICTIONARY.md`, `data_dictionary.csv`: column definitions and units.
- `results/`: fitted coefficients at full precision (`fit_params.csv`), jackknife estimates, leave-one-site-out predictions (`predictions_loso.csv`) and errors by combination, energy, and anatomical class, cross-phantom predictions, paired-reconstruction comparisons, and the exploratory regression-tree results.

## Monte Carlo run statistics

`results/microscale_run_statistics.csv`: for each of the 7228 microscale PHITS runs (model, source region, energy index 1 to 26 of the microscale energy grid), the number of histories, the manner of termination (1% criterion met, 10^6 history limit, or job time limit), and the relative statistical uncertainty of the energy deposited in each tissue region (TB, shallow RBM, shallow YBM, deep RBM, deep YBM) and in the two target regions. Models without RBM have no RBM entries.

## Other folders

- `source_inputs/`: microscale and macroscale AFs from the Monte Carlo simulations, tissue volumes of the microscale models, skeleton region tables of the phantoms, source and target data, and the emission spectra used for the S values.
- `supplementary_tables/`: the supplementary tables of the article as CSV files, numbered as in the supplement. Table S7b gives ratios to the SAFs of ICRP Publication 133; the ICRP values themselves are not redistributed.
- `FILE_LIST.csv`: every file with its size and SHA-256 checksum.

The model geometry and the Monte Carlo input and output files are not included and can be requested from the corresponding author (W E Bolch, wbolch@ufl.edu).
