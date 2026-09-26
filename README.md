# Multimodal Supernova Analysis and Redshift Regression

This project analyzes the PLAsTiCC astronomical dataset as a heterogeneous multimodal dataset. Each object contains static metadata together with multiband light-curve observations. The notebook transforms the time-series measurements into statistical descriptors, explores the resulting feature space, predicts spectroscopic redshift, and studies the distribution of prediction errors.

The complete analysis is available in [`Phase1_485.ipynb`](Phase1_485.ipynb).

## Dataset

The notebook loads the `MultimodalUniverse/plasticc` dataset from Hugging Face in streaming mode.

The analysis uses 7,848 astronomical objects and combines two modalities:

- Static tabular metadata: `redshift`, `hostgal_photoz`, and `hostgal_specz`
- Light-curve time-series measurements across the `u`, `g`, `r`, `i`, and `z` bands

For each active photometric band, the notebook extracts:

- Mean flux
- Standard deviation of flux
- Maximum flux
- Linear time-series slope

The resulting exploratory-analysis matrix contains 23 features: 3 metadata features and 20 light-curve summary features. The Y band is excluded because its sampled descriptors contain no useful variation.

## Analysis included

### Exploratory data analysis

- Feature construction from multiband light curves
- Data-shape and feature-representation analysis
- Descriptive statistics, variance, and skewness
- Histograms and distribution visualizations
- Pairwise cosine-similarity heatmap for a sorted sample subset
- Interpretation of similarity patterns across astronomical object types

### Redshift regression

The light-curve features are used to estimate `hostgal_specz`, the spectroscopic redshift treated as the target distance-related value.

The notebook compares:

- A custom linear regression trained with gradient descent
- Scikit-learn linear regression
- A Random Forest regressor

The recorded evaluation results are:

| Model | MSE | R² |
| --- | ---: | ---: |
| Linear Regression | 14.5674 | 0.0081 |
| Random Forest | 4.8610 | 0.6690 |

The results suggest that the relationship between light-curve descriptors and redshift is substantially nonlinear, allowing the Random Forest model to outperform the linear baseline.

### Probabilistic residual analysis

The notebook examines regression residuals using:

- Residual mean and standard deviation
- A fitted Gaussian probability density function
- Empirical coverage within one, two, and three standard deviations
- Kernel density estimation with different bandwidth settings

The recorded residual statistics are approximately:

- Mean residual: `0.0006`
- Standard deviation: `0.3266`
- Within 1σ: `91.12%`
- Within 2σ: `96.23%`
- Within 3σ: `97.68%`

The analysis concludes that a single Gaussian distribution does not fully describe the errors: the residuals have a sharp central peak together with heavy tails and outliers.

## Technologies

- Python
- Jupyter Notebook
- Hugging Face Datasets
- NumPy
- pandas
- Matplotlib
- Seaborn
- SciPy
- scikit-learn

## Running the notebook

Install the required packages:

```bash
pip install datasets==3.6.0 pandas numpy matplotlib seaborn scikit-learn scipy
pip install git+https://github.com/MultimodalUniverse/MultimodalUniverse.git
```

Open `Phase1_485.ipynb` in Jupyter or Google Colab and run the cells from top to bottom. The notebook downloads the dataset from Hugging Face, so an internet connection is required. A Hugging Face token is optional for public access but may improve rate limits.

## Project status

This is an academic data-analysis project. The notebook contains the full exploratory workflow and recorded results, but it is not packaged as a reusable machine-learning application or production inference service.

