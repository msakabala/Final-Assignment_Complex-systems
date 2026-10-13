# Power-network disruption: predicting later interruptions

## Project overview

This project tests whether information about a substation's local network position improves prediction of a later **simulated** interruption.

Two logistic regression models are compared:

1. **Original substation features**
   - `load_ratio`
   - `equipment_age_years`

2. **Substation + network features**
   - `load_ratio`
   - `equipment_age_years`
   - `degree`
   - `local_clustering`

Only substations that were functioning at the observation point are included in the prediction task.

## Data

The project uses two synthetic teaching datasets:

- `power_substations.csv`
- `power_links.csv`

There are 144 substations and 344 undirected physical links. One substation is initially interrupted, so **143 substations** are eligible for modelling. Among these, **39** have a later simulated interruption.

The data are synthetic and are not operational power-system data.

## Workflow

The notebook:

1. Inspects the substation and link data.
2. Excludes the initially interrupted substation.
3. Compares load ratio and equipment age across later outcomes.
4. Builds an undirected NetworkX graph.
5. Calculates two network features:
   - **degree**: number of direct physical links;
   - **local clustering coefficient**: how strongly a substation's neighbours are connected to one another.
6. Uses one stratified 70/30 train-test split.
7. Fits scaling on the training data only.
8. Fits two logistic regression models on the same training observations.
9. Compares accuracy, F1, ROC AUC, and confusion matrices.

## Main results

### Original substation features
- Accuracy: **0.767**
- F1 score: **0.444**
- ROC AUC: **0.828**
- Confusion matrix: 29 true negatives, 2 false positives, 8 false negatives, 4 true positives.

### Substation + network features
- Accuracy: **0.860**
- F1 score: **0.750**
- ROC AUC: **0.949**
- Confusion matrix: 28 true negatives, 3 false positives, 3 false negatives, 9 true positives.

On this test split, adding degree and local clustering improved all three summary metrics and reduced the number of missed later interruptions.

## Interpretation and limitations

The result suggests that network position contains additional predictive information in this fixed synthetic dataset. It does **not** show that failures propagate through links or that network position causes interruptions.

The graph is deliberately simplified, with no impedance, capacity, load-flow model, protection system, or dynamic cascade. The result also comes from a small synthetic dataset and one held-out split, so it should not be treated as evidence that the model would work for a real power grid.

## Reflection

The most challenging part was keeping the comparison fair: both models needed to use exactly the same train-test split, and scaling had to be learned only from the training data. It was also important to distinguish degree from local clustering, because they describe different aspects of network position.

With more time, I would repeat the comparison across several train-test splits or use cross-validation to check whether the apparent improvement from the network features is stable. For a more realistic application, I would also want electrical features such as line capacity, impedance, power flow, and independent validation data.

## Files

- `Option2_Power_Network_Disruption_COMPLETED.ipynb` – completed notebook with code, outputs, plots, and written interpretations.
- `README_Power_Network_Disruption.md` – project summary.
