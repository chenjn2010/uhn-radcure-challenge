# UHN RADCURE Prognostic Modelling Challenge 2020

## Getting started
### Using conda (recommended):
1. [Install conda](https://docs.conda.io/en/latest/miniconda.html)
2. Create the virtual environment:
```CFLAGS='-std=c++11' conda env create -f environment.yml```
3. Activate the environment:
```conda activate radcure-challenge```

### Using pip (not recommended):
```pip install -r requirements.txt```

## Goals
Develop a prognostic model for patients with head and neck cancer using pre-treatment planning CT scans to predict two endpoints:
1. Binarized 2-year survival 
2. Overall survival (OS) (duration of survival from diagnosis date to death or censoring).

## Dataset
We will provide a fixed training set with the following data for each patient:
- Full planning CT image in NRRD format (the input)
- Binary segmentation mask of the primary gross tumour volume (GTVp)
- Binarized survival data given as a binary variable indicating the occurrence of event before 2 years of follow-up (endpoint 1)
- OS data given as a binary variable indicating the occurrence of event (death) and a continuous variable indicating the time to event or censoring (endpoint 2).

In phase II of the challenge, we will also provide de-identified clinical variables, including age, sex, tumour and lymph node stage, and HPV status, to test whether radiomics models can be improved by adding clinical variables. We will also provide information about radiation dose and systemic treatment (chemotherapy).

### Summary statistics
|                                            |           |
|--------------------------------------------|-----------|
| Training set size                          | 1802      |
| Test set size                              | 750       |
| Positive class frequency (training set)    | 323 (18%) |
| Median follow-up time/years (training set) | 5.36      |

#### Case distribution by disease site (training set)
![Case distribution by disease site (training set)](/results/cases_by_site.png)

## Evaluation and submission
At the end of phase II, we will release the test set without ground truth labels. Participants will be asked to run their models on the test set and submit the predictions. We will use the predictions and the ground truth labels to compute the test set metrics for each submission. The participants will be asked to submit their code alongside complete requirements and instructions on how to run it to ensure full reproducibility for publication.

### Submission format
Please submit your model predictions on the test set only in CSV format. The submission file should contain the following columns (named **exactly** as below):
- `Study ID`: the ID for each subject in the test set
- `binary`: the predicted probability of the positive class (i.e. death before 2 years) for the binary task
- `survival_event`: the predicted overall risk of event for the survival task (e.g. prediction type = `risk` in `R`'s `coxph`)
- `survival_time_<i>`: the predicted survival function in one-month bins between 0 and 2 years (i.e. `n` columns with `i = 0, 1, ..., 24`, where the value in each column is the probability of surviving `i` months).
The submission portal and instructions will be announced at the end of phase II.

The code used for evaluation can be found in [radcurechallenge/evaluation](radcurechallenge/evaluation). The accuracy of binarized 2-year survival prediction will be evaluated using the area under the receiver operating characteristic (ROC) curve (AUROC), and average precision (AP, variant of area under the precision-recall curve). For survival prediction, the concordance index (C-index) will be used.
Submissions will be ranked primarily by the AUROC on the binarized task. Average precision and the performance on the survival prediction task will be used to break ties.

### Reproducibility
To ensure reproducibility of results, the best performing teams will also be asked to submit their full code alongside detailed instructions on how to run it. Note that you don't need to use the specific virtual environment/package versions from this repository, as long as you provide a way to reproduce your environment.

## Benchmark models
In all basic models, logistic regression was used for binarized 2-year survival modelling, while Cox proportional hazards model was used for OS modelling.

**Basic:**
- Tumour volume
- Clinical parameters, including age, sex, stage, HPV status
- Engineered radiomics (PyRadiomics)

The code used to train the baseline models can be found in [radcurechallenge/baselines](radcurechallenge/baselines). Feel free to use it as a starting point for your submission.

### Baseline results
*Note:* higher is better. P values were computed from 10,000 iterations of randomized permutation test. For random predictions, AUC = C-index = .5, while AP depends on the class balance.
| Model     | ROC AUC (binary) | AP (binary)     | C-index (survival)   |
|-----------|------------------|-----------------|-----------------|
| radiomics | 0.71 (p < .001)  | 0.33 (p < .001) | 0.73 (p < .001) |
| volume    | 0.71 (p < .001)  | 0.32 (p < .001) | 0.71 (p < .001) |
| clinical  | 0.74 (p < .001)  | 0.37 (p < .001) | 0.70 (p < .001) |

## Further information
If you're a participant, you should also check our Slack workspace (invitations will be sent soon) for latest updates and discussion.

