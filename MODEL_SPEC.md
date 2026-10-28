# Model specification

Mathematical notation follows the two Choi reference papers: phi_mu (\(\phi_\mu\)) is dimensionless microscale AF, phi_M (\(\phi_\mathrm{M}\)) is dimensionless macro AF, and capital Phi (\(\Phi\)) is SAF in kg^-1. The CSV label `AF` means phi_mu in the fitting dataset. A source-target pair is target <- source. Descriptive subscripts are upright in the manuscript; the underscore labels below are machine-readable names.

Ranges R_RBM, R_YBM and R_TB are in cm. The model uses R_marrow = cf R_RBM + (1-cf) R_YBM as an empirical arithmetic mixture descriptor, not an exact mixture CSDA range. delta=0.005 cm. Define u_R=R_RBM/delta, u_Y=R_YBM/delta, u_m=R_marrow/delta, x_m=R_marrow/l_marrow and x_t=R_TB/l_trab. The mean-intercept descriptors l=4V/S refer to isotropic line sampling; they are not asserted to be mean forward source-to-boundary distances under uniformly sampled interior sources. f_S is the fraction of trabecular surface adjoining RBM; f_R/f_Y are shallow volume fractions within RBM/YBM.

D(v;a,b,c)=(1+av)/(1+(a+b)v+cv^2). For a,b,c>=0 and v>=0 it is nonincreasing and D(0)=1. Its limit is zero if c>0; c=0 requires separate consideration.

Fitting minimizes the SciPy `least_squares` soft_l1 objective with f_scale=0.05 on unweighted AF residuals, one shared coefficient vector per pair. Parameter bounds and initial values are serialized in `model_spec.json`. Rows flagged trivial_zero or holdout_old are excluded. No prediction clipping is applied; the observed domain is reported in `results/training_domain_by_pair.csv`. Every held-out component loses all its energies. Cross-sex models use one sex for all fitting and the other for testing. Each validation fit starts from the fixed model starting point; central-fit parameters do not initialize validation folds. Three additional central-fit starts probe robustness; the original prespecified central fit is retained, and poorer basins are reported, not suppressed.

The source, sampling unit and dimensions of every input are documented in DATA_DICTIONARY.md. Neither pooled accuracy nor exact zero-range anchors establish safe extrapolation to ages, disease states, donors or geometry outside the supplied model collection.

Electron forms (all outputs are phi_mu):

Let f_eq,R and f_eq,E be the implemented initial-energy collision-stopping-power-weighted mass fractions; f_meq,R is the corresponding RBM fraction within marrow. These are practical high-range partition approximations. Uniform-fluence theory involves an energy-spectrum integral and does not by itself make the initial-energy estimator exact. See the article mathematical appendix for assumptions.

1. RBM <- RBM: f_eq,R + (f_meq,R-f_eq,R)D(x_m;a2,b2,c2) + (1-f_meq,R)D(u_R;a1,b1,c1).
2. Endosteum <- RBM: f_eq,E+(f_R-f_eq,E)D(u_R;a,b,c).
3. RBM <- YBM: f_meq,R[1-D(u_Y;a1,b1,c1)] + (f_eq,R-f_meq,R)[1-D(x_m;a2,b2,c2)].
4. Endosteum <- YBM: f_eq,E+(f_Y-f_eq,E)D(u_Y;a,b,c).
5. RBM <- TBS: f_eq,R+(f_S/2-f_eq,R)D(u_m;a,b,c).
6. Endosteum <- TBS: f_eq,E+(1/2-f_eq,E)D(u_m;a,b,c).
7. RBM <- TBV: f_eq,R[1-D(x_t;a,b,c)].
8. Endosteum <- TBV: f_eq,E[1-D(x_t;a,b,c)] + g x_t/[1+(x_t/s)^2].

Macro interpolation: phi_M=D(w;a,b,c), w=R_RBM/V_spong^(1/3). Its fit enforces c>=1e-12 to ensure the zero high-range limit. A calibrated component uses phi_M=D(k w;a,b,c); k is chosen from its single measured 1 MeV AF after its curve is omitted from the global fit. The composed component AF is phi_mu times phi_M. It is a within-component spongiosa-source self term, not the entire cortical/shaft or off-site cross-fire assembly.
