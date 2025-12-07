# Antidepressant Molecule Classification

Machine learning classifier to predict whether a molecule is an antidepressant based on its chemical structure.

## Project Goal

Predict antidepressant activity from molecular structure represented as SMILES strings. The model extracts chemical descriptors (molecular weight, lipophilicity, hydrogen bonding capacity, ring count) and trains a Random Forest classifier to distinguish active compounds from inactive ones.

## Approach

**Input**: SMILES notation (text representation of molecular structure)  
**Feature Extraction**: RDKit chemical descriptors  
**Model**: Random Forest with 100 trees  
**Output**: Binary classification (antidepressant / not antidepressant)

## Chemical Descriptors Used

- **MolWt**: Molecular weight (Daltons) - relates to bioavailability
- **LogP**: Lipophilicity - predicts membrane permeability and blood-brain barrier crossing
- **NumHAcceptors**: Hydrogen bond acceptors - impacts protein binding
- **NumHDonors**: Hydrogen bond donors - affects solubility and receptor interactions
- **RingCount**: Number of ring structures - common in CNS-active drugs

## Why These Features?

**Lipinski's Rule of Five**: Drug-like molecules typically have MolWt < 500, LogP < 5, HBD ≤ 5, HBA ≤ 10. These descriptors capture drug-likeness heuristics developed from analyzing thousands of approved drugs.

**CNS Penetration**: Antidepressants must cross the blood-brain barrier. LogP and molecular weight are critical predictors of brain penetration.

## Installation & Usage

```bash
pip install pandas rdkit scikit-learn
python antidepressant_classifier.py
```

## Questions Worth Exploring

### On Feature Selection

**Why only 5 descriptors instead of 200+ available in RDKit?**  
RDKit offers hundreds of molecular descriptors (TPSA, rotatable bonds, aromatic rings, etc.). Would adding more features improve accuracy or cause overfitting on this small dataset?  
→ [RDKit Descriptor Documentation](https://www.rdkit.org/docs/GettingStartedInPython.html#list-of-available-descriptors)

**Are these descriptors sufficient to distinguish antidepressants?**  
Antidepressants target specific receptors (serotonin, norepinephrine). Do structural features alone capture this mechanism, or do you need pharmacophore-based features?  
→ [Molecular Descriptors in QSAR](https://pubs.acs.org/doi/10.1021/ci00057a005)

**Why not use molecular fingerprints instead?**  
Morgan fingerprints or MACCS keys encode substructure patterns. Would these capture antidepressant-specific motifs better than scalar descriptors?  
→ [Molecular Fingerprints for Machine Learning](https://jcheminf.biomedcentral.com/articles/10.1186/s13321-015-0069-3)

### On Model Choice

**Why Random Forest over other algorithms?**  
Random Forests handle non-linear relationships and feature interactions well. But with only 5 features, would logistic regression be interpretable enough while maintaining performance?  
→ [Random Forests in Cheminformatics](https://pubs.acs.org/doi/10.1021/ci034160g)

**Why 100 trees?**  
Test n_estimators ∈ [10, 50, 100, 200, 500]. Does performance plateau, or does the small dataset make additional trees redundant?  
→ [Hyperparameter Tuning Guide](https://scikit-learn.org/stable/modules/ensemble.html#parameters)

**Would deep learning outperform Random Forest here?**  
Graph neural networks (GNNs) can learn directly from molecular graphs. But with <10 training samples, would a GNN overfit catastrophically?  
→ [Deep Learning for Drug Discovery](https://arxiv.org/abs/1706.06689)

### On Data Quality

**Is 4 molecules enough to train a reliable model?**  
This dataset is a toy example. Real drug discovery datasets have 1000s-10000s of compounds. How would you evaluate if this model generalizes?  
→ [Benchmark Datasets in Cheminformatics](http://moleculenet.ai/)

**Why no external validation set?**  
Splitting 4 molecules into train/test is statistically meaningless. Should you test on completely independent molecules from public databases like ChEMBL?  
→ [ChEMBL Database](https://www.ebi.ac.uk/chembl/)

**How do you handle class imbalance?**  
If 90% of molecules are inactive, accuracy is misleading. Should you use AUROC, precision-recall, or SMOTE for balancing?  
→ [Dealing with Imbalanced Data](https://arxiv.org/abs/1106.1813)

### On Chemical Validity

**What if SMILES parsing fails?**  
`Chem.MolFromSmiles()` returns `None` for invalid SMILES. Should you sanitize inputs with structure canonicalization or stereochemistry checks?  
→ [RDKit Molecule Sanitization](https://www.rdkit.org/docs/RDKit_Book.html#molecular-sanitization)

**Do isomers matter?**  
SMILES doesn't always specify stereochemistry (R/S configuration). Enantiomers can have vastly different biological activities. Should you use isomeric SMILES?  
→ [Stereochemistry in Drug Design](https://pubs.acs.org/doi/10.1021/jm0306024)

### On Interpretability

**Which features are most predictive?**  
Extract `feature_importances_` from the Random Forest. Is LogP (BBB crossing) the dominant factor, or do ring structures matter more?  
→ [Feature Importance in Random Forests](https://scikit-learn.org/stable/auto_examples/ensemble/plot_forest_importances.html)

**Can you visualize decision boundaries?**  
Project the 5D feature space into 2D using PCA or t-SNE. Do antidepressants cluster in a distinct region?  
→ [Dimensionality Reduction Techniques](https://arxiv.org/abs/1802.03426)

## Potential Enhancements

**Expand Feature Set**  
Add TPSA (polar surface area), rotatable bonds, aromatic atoms. Test if they improve cross-validation accuracy.

**Use Molecular Fingerprints**  
Replace descriptors with Morgan fingerprints (radius=2, 2048 bits). Train on substructure patterns instead of scalar properties.

**Multi-Task Learning**  
Predict multiple properties simultaneously (antidepressant activity + toxicity + BBB penetration). Does this improve generalization?

**Incorporate Domain Knowledge**  
Weight features based on known pharmacology (LogP weighted higher for CNS drugs). Does this physics-informed ML improve performance?

**Active Learning**  
Iteratively select which molecules to test experimentally based on model uncertainty. Can you reduce wet-lab costs?

**Compare Against Baseline**  
Test commercial tools like Schrödinger's QikProp or SwissADME. Does your model match expert systems?

## Key Learnings

**SMILES as Molecular Representation**: Text-based encoding of molecular graphs enables direct application of NLP and classical ML techniques.

**Feature Engineering Matters**: Domain-specific descriptors (LogP for BBB crossing) are more powerful than raw SMILES for small datasets.

**Data Scarcity Challenge**: Drug discovery ML suffers from limited labeled data. Transfer learning from large chemical databases (ChEMBL, PubChem) is critical.

**Validation is Everything**: A model trained on 4 molecules has no statistical power. Real-world deployment requires thousands of diverse structures and external validation.

## References

**Cheminformatics Fundamentals**
- [RDKit Documentation](https://www.rdkit.org/docs/index.html)
- [Introduction to Cheminformatics (Leach & Gillet, 2007)](https://www.springer.com/gp/book/9781402062902)

**QSAR & Drug Discovery**
- [Lipinski's Rule of Five (Lipinski et al., 1997)](https://www.sciencedirect.com/science/article/abs/pii/S0169409X96004231)
- [QSAR in Drug Design (Kubinyi, 1993)](https://onlinelibrary.wiley.com/doi/book/10.1002/9783527614776)

**Machine Learning in Chemistry**
- [Deep Learning for Molecular Design (Schneider & Fechner, 2005)](https://pubs.acs.org/doi/10.1021/ci050075m)
- [MoleculeNet Benchmark (Wu et al., 2018)](https://arxiv.org/abs/1703.00564)

**Graph Neural Networks**
- [Neural Message Passing for Quantum Chemistry (Gilmer et al., 2017)](https://arxiv.org/abs/1704.01212)
- [Analyzing Learned Molecular Representations (Yang et al., 2019)](https://pubs.acs.org/doi/10.1021/acs.jcim.9b00237)

---

**Disclaimer**: This is an educational toy example. Real drug discovery requires extensive medicinal chemistry expertise, experimental validation, toxicity studies, and regulatory approval. Never use ML predictions alone for therapeutic decisions.
