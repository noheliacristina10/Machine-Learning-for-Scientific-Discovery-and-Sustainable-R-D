
Project # 1
Machine Learning for Pesticide Removal from Water (ZnO Photocatalysis)
Which treatment conditions are most worth testing next in the lab? This project uses published experiments to answer that question for three pesticides treated with a zinc-oxide (ZnO) photocatalyst under UV light.

Author: Nohelya Borrero Arias · M.S. Chemical and Environmental Engineering, Missouri University of Science and Technology Course: Graduate Computational Intelligence (independent project) · Completed May 2026

Why it matters
Pesticide residues from agricultural runoff reach surface and ground water, and they are hard to remove. Photocatalysis is one option: ZnO under UV light produces reactive species that break down organic contaminants. How well it works depends on several conditions at once: pH, catalyst dose, treatment time, and the initial pesticide concentration. Testing every combination in the lab is slow and uses up catalyst, so the question is which conditions to test first.
Data
Source: Dashti et al. (2024), Chemosphere 362, 142792, compiled from published tables and digitized graphs.
Size: 163 measurements of % removal, grouped into 32 experimental runs. A run is one batch sampled over time.
Pesticides: diazinon (44 measurements), dichlorvos (73), phenol (46). Each comes from a single source study, and all use undoped ZnO.
Inputs: initial concentration, pH, treatment time, catalyst dose. Target: % degradation.

Approach
Compile and group: reconcile the measurements and rebuild the 32 experimental runs.
Predict: compare 11 approaches for each pesticide: ridge, elastic net, decision tree, random forest, extra trees, bagged trees, gradient boosting, CatBoost, a small neural network, and two naive baselines (the training mean and median).
Validate without leakage: five-fold cross-validation grouped by run, so measurements from the same batch never appear in both training and testing. Preprocessing is fitted inside each fold.
Screen: search for conditions that balance high removal, short treatment time and low catalyst dose. The search stays within the measured range and discounts predictions where the five fold models disagree.
Recommend: propose one condition per pesticide for lab confirmation.
Results
Pesticide
Error (MAE, % points)
R²
Improvement vs. naive baseline
Diazinon
8.38
0.734
54% lower error
Dichlorvos
7.61
0.851
71% lower error
Phenol
3.41
0.947
79% lower error


The small neural network was the most accurate model for all three pesticides.
The ranking is robust for phenol. For diazinon and dichlorvos, its lead over the next-best model falls within normal fold-to-fold variation.

Recommended conditions to test next (not yet tested):
pH
Catalyst (mg/L)
Time (h)
Conservative predicted removal
Dichlorvos (test first)
7.01
131
0.64
68.6%
Phenol
2.72
664
1.12
55.7%
Diazinon
6.28
47
0.80
46.5%

Limitations
The same folds were used to select the model and to evaluate it, and there is no independent test set, so the scores may be slightly optimistic.
Each pesticide comes from a single study, so the models have not been tested across laboratories.

Project #2

Machine Learning for CO₂-to-Methanol Catalyst Screening

Can machine learning tell a lab which catalyst to test next, and where does it stop being reliable? This project answers that using a published campaign of 120 catalyst experiments for converting captured CO₂ and hydrogen into methanol.

Authors: Nohelya Borrero Arias and Esther Awusah· Two-person graduate project, Math for Machine Learning, Missouri University of Science and Technology ·

Why it matters

Converting captured CO₂ and hydrogen into methanol turns a waste stream into a fuel and chemical feedstock. The reaction depends on a catalyst that must do three things at once:

convert a meaningful share of the CO₂,
send that carbon to methanol rather than to by-products,
avoid methane, which wastes carbon and is itself a potent greenhouse gas.

Candidates are tested a few dozen at a time, each run costs reactor time and materials, and most formulations underperform. The recurring decision is which formulation deserves the next experiment.

Data
Source: a published high-throughput campaign by an ETH Zurich group (Pacheco Gutierrez et al., CHIMIA 77 (2023) 7–16).
Size: 120 priced experiments in five rounds of 24. After round 1, each batch was chosen by Bayesian optimization in the original study; this project did not run the optimization.
Inputs: metal loadings (Cu, Zn, Ce, K, In, Fe, Co) and support material.
Outcomes: CO₂ conversion, methanol selectivity, methane selectivity.


Approach
Describe the campaign: outcome ranges and how performance changed across the five rounds.
Build chemically meaningful features: describe each metal relative to the catalyst recipe, with total metal loading kept separate, and group the recipes into formulation types using PCA and K-means clustering.
Define targets: split each outcome into bad, good and best classes at the 33rd and 67th percentiles, calculated inside each training set to prevent leakage. Methane is scored in reverse, so "best" always means desirable.
Check for confounding: the support switched to zirconia at round 2 and never changed back, so support, time and composition moved together. Regressions with and without controlling for support test whether metal effects reverse.
Compare models: seven classifiers (random forest, bagging, gradient boosting, decision tree, SVM, voting classifier, small neural network). Each is tested with a rolling design that trains on earlier rounds and predicts the next, which mirrors how a lab actually works.
Test shortlist stability: 50 bootstrap refits check whether top-ranked candidates stay near the top.
Run an economic and footprint sensitivity check: margin and CO₂-equivalent per kg of methanol under three scenarios, using placeholder inputs.
Results

Models are useful for two of the three outcomes (rolling balanced accuracy; chance = 0.33):

Outcome	Best model	Score
CO₂ conversion	Bagging	0.71
Methanol selectivity	Voting classifier	0.74
Methane selectivity	Gradient boosting	0.51, not reliable
Methane is the gap. No model is reliable enough to rule out a candidate on methane risk. That is where new experiments would add the most information.
The yield gain came from one change. Median yield rose from 0.05% to 3.2% of fed carbon between rounds 1 and 2, when the support switched to zirconia. The data cannot separate the support effect from the optimizer learning better recipes.
Controlling for support reverses 13 of 21 metal effects. For example, indium appears to lower methanol selectivity overall but raises it within a fixed support. Only three reversals have p < 0.05, uncorrected for multiple comparisons, so they are hypotheses to test, not established chemistry.
One candidate is stable. Candidate 103 stayed in the methanol top five in 88% of 50 refits. Candidate 118 stayed in the low-methane top five in 70%. These percentages measure ranking stability, not performance.
Energy matters more than the catalyst. Under placeholder inputs, only the favorable scenario is profitable (+$0.17/kg vs. −$0.59 and −$1.94). The footprint ranges from 0.27 to 4.25 kg CO₂e per kg of methanol. Hydrogen and electricity drive both.
Recommendations
Advance Candidates 103 and 118 to lab validation, measuring conversion, methanol and methane together.
Test supports side by side within one round before standardizing on zirconia.
Aim the next round of experiments at methane.
Replace the placeholder economic and footprint inputs with measured values before any investment case.
Limitations
No candidate has been tested in the lab.
The confounding check assumes a linear adjustment captures the support effect.
Catalyst lifetime, deactivation, safety and manufacturability were not measured.
All economic and life-cycle inputs are placeholders, not plant data.
The models can prioritize experiments. They do not establish causal chemistry or justify commercial commitment.
Energy use, catalyst recovery, by-product toxicity and cost were not assessed.
The recommendations are hypotheses for lab testing, not evidence that the process is scalable or environmentally beneficial.
