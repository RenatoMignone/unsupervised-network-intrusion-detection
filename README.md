# Laboratory 3 – Anomaly Detection for Network Security

![Polito Logo](resources/logo_polito.jpg)

This repository collects the material produced for Laboratory 3 of the **AI and Cybersecurity** course. The lab focuses on **Anomaly Detection** applied to Network Intrusion Detection Systems (NIDS). The goal is to detect malicious network traffic without using attack labels for training, simulating a real-world scenario where zero-day attacks are unknown.

## At a Glance

- **Goal**: Detect network anomalies (attacks) using unsupervised and semi-supervised learning.
- **Data**: Network connection records with statistical and content features (derived from KDD Cup 99 / NSL-KDD).
- **Workflow**: creating a baseline (OC-SVM) → deep anomaly detection (Autoencoders) → unsupervised clustering (K-means, DBSCAN) → visualization (PCA, t-SNE).
- **Deliverables**: interactive notebooks under `lab/notebooks/` and a LaTeX report in `report/`.

## Repository Layout

```
Laboratory3/
├── lab/
│   ├── data/           # Dataset files (train.csv, test.csv)
│   ├── notebooks/      # Task-specific Jupyter notebooks (Task1–Task4)
│   └── Plots/          # Generated figures for the report
├── report/             # LaTeX report sources
├── resources/          # Logos and reference material
└── README.md           # This file
```

## Dataset: Network Intrusion Detection
The dataset consists of network connection records, each labeled as either **normal** or a specific attack type (e.g., *DoS, Probe, R2L*).
- **Training Data**: `train.csv` (contains a balanced mix of normal and attack traffic, though labels are often withheld during unsupervised training).
- **Test Data**: `test.csv` (used to evaluate generalization to new, unseen traffic).
- **Features**: 
    - **Basic**: Duration, protocol, service, flags.
    - **Content**: Failed logins, root shell access, file creations.
    - **Traffic**: Connection counts to the same host/service (to detect scanning/flooding).

## Notebook Guide & Key Experiments

| Task | Notebook | Focus | Key Findings |
| --- | --- | --- | --- |
| **Task 1** | `lab/notebooks/Task1.ipynb` | **Exploratory Data Analysis** | - Identified categorical vs numerical features.<br>- Heatmaps revealed high correlation between *count* features and DoS attacks.<br>- Normalization (MinMax) is crucial for distance-based models. |
| **Task 2** | `lab/notebooks/Task2.ipynb` | **Shallow Anomaly Detection (OC-SVM)** | - **One-Class SVM**: Effective when trained on *normal-only* data (semi-supervised).<br>- Performance degrades significantly when trained on mixed data without labels (unsupervised).<br>- Hyperparameter `nu` (contamination) is critical. |
| **Task 3** | `lab/notebooks/Task3.ipynb` | **Deep Anomaly Detection (Autoencoders)** | - **Reconstruction Error**: Autoencoders learned to reconstruct normal traffic well; attacks yielded high error.<br>- **Thresholding**: Percentile-based thresholds on validation split maximized F1-score.<br>- **Latent Representation**: Using the bottleneck layer as input to OC-SVM improved separation. |
| **Task 4** | `lab/notebooks/Task4.ipynb` | **Unsupervised Clustering** | - **K-Means**: Clusters roughly corresponded to attack types, but "pure" clusters were rare.<br>- **DBSCAN**: Successfully isolated outliers (noise points) which often mapped to attacks.<br>- **t-SNE**: Visualized clear separation between normal traffic and volumetric attacks (DoS). |

### Highlights per Task

- **One-Class SVM (Task 2)**
    - Best performance achieved in a semi-supervised setting (training only on benign traffic).
    - Robustness test: The model maintained good precision even with 10% contamination in the training set.

- **Autoencoders (Task 3)**
    - Architecture: Undercomplete Autoencoder (Encoder compresses input, Decoder reconstructs it).
    - Anomaly Score: Mean Squared Error (MSE) between input and reconstruction.
    - Results: Outperformed OC-SVM in detecting complex, non-linear anomalies (e.g., R2L attacks).

- **Clustering & Visualization (Task 4)**
    - t-SNE plots demonstrated that while DoS attacks form distinct dense clusters, probe and R2L attacks often overlap with normal traffic, making them harder to detect without labels.
    - DBSCAN showed potential as a standalone anomaly detector by flagging low-density regions as malicious.

## Reproducing the Experiments

1. **Environment**: Install standard data science libraries (`pandas`, `numpy`, `scikit-learn`, `matplotlib`, `seaborn`, `torch`).
2. **Execution**: Run notebooks in order (`Task1.ipynb` $\to$ `Task4.ipynb`).
    - *Note*: Seed setting is included for reproducibility.
3. **Data**: Ensure `train.csv` and `test.csv` are in `lab/data/`.

## Authors

| Name | GitHub | LinkedIn | Email |
| --- | --- | --- | --- |
| Renato Mignone | [![GitHub](https://img.shields.io/badge/GitHub-Profile-informational?logo=github)](https://github.com/RenatoMignone) | [![LinkedIn](https://img.shields.io/badge/LinkedIn-Profile-blue?logo=linkedin)](https://www.linkedin.com/in/renato-mignone/) | [![Email](https://img.shields.io/badge/Email-Send-blue?logo=gmail)](mailto:renato.mignone@studenti.polito.it) |
| Claudia Sanna | [![GitHub](https://img.shields.io/badge/GitHub-Profile-informational?logo=github)](https://github.com/sannaclaudia) | [![LinkedIn](https://img.shields.io/badge/LinkedIn-Profile-blue?logo=linkedin)](https://www.linkedin.com/in/claudiasanna1/) | [![Email](https://img.shields.io/badge/Email-Send-blue?logo=gmail)](mailto:claudia.sanna@studenti.polito.it) |
| Chiara Iorio | [![GitHub](https://img.shields.io/badge/GitHub-Profile-informational?logo=github)](https://github.com/iochia02) | [![LinkedIn](https://img.shields.io/badge/LinkedIn-Profile-blue?logo=linkedin)](https://www.linkedin.com/) | [![Email](https://img.shields.io/badge/Email-Send-blue?logo=gmail)](mailto:chiara.iorio@studenti.polito.it) |
