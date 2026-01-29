# RNA Half-Life Estimation


**Proposed Methodology:**
- Dataset: Malaria IDC (1000 genes, 48 timepoints)
- Preprocessing: Z-score normalization (uniform 1h sampling)
- λ selection: L-curve method (result: λ=15)
- Filtering: Fixed 9th percentile MSE threshold


## Dependencies

The following Python packages are required:

```bash
pip install numpy pandas matplotlib seaborn scipy scikit-learn
```

### Required Packages:
- **numpy**: Numerical computations and array operations
- **pandas**: Data manipulation (if loading CSV data)
- **matplotlib**: Plotting and visualization
- **seaborn**: Statistical data visualization
- **scipy**: Scientific computing (stats, optimization, integration)
- **scikit-learn**: Machine learning algorithms (Lasso regression)

## How to Run the Code

1. **Environment Setup**:
   ```bash
   # Create a virtual environment (recommended)
   python -m venv starek_env
   source starek_env/bin/activate  # On Windows: starek_env\Scripts\activate
   
   # Install dependencies
   pip install numpy pandas matplotlib seaborn scipy scikit-learn
   ```

2. **Launch Jupyter Notebook**:
   ```bash
   jupyter notebook
   ```

3. **Open the Notebook**:
   - Navigate to `RNA_HalfLife_Estimation.ipynb`
   - Run cells sequentially from top to bottom

4. **Execution Order**:
   - **Cell 1-2**: Import libraries and set up environment
   - **Cell 3-4**: Load preprocessing functions and core algorithm
   - **Cell 5**: Generate/load dataset
   - **Cell 6**: Run StaRTrEK algorithm
   - **Cell 7**: Validate against ground truth
   - **Cell 8**: Generate performance plots

## Dataset Usage

### Simulated Data (Default)
The notebook generates synthetic exponential decay data with known half-lives for validation:

- **1000 genes** × **48 timepoints** (1-48 hours)
- **Ground truth half-lives**: Uniform distribution (0.1-10 hours)
- **Noise**: Gaussian noise (σ=0.05) added to clean exponential decays
- **Preprocessing**: Z-score normalization (mean=0, std=1)

### Real Data (Optional)
To use the original paper's Malaria IDC dataset:

1. Download `DataExp.mat` and `DataHL.mat` from PMC9123730 supplementary materials
2. Place files in the same directory as the notebook
3. Modify the data loading section to use:
   ```python
   from scipy.io import loadmat
   
   # Load expression data
   data_exp = loadmat('DataExp.mat')
   expression_data = data_exp['DataExp']  # Adjust key as needed
   
   # Load half-life ground truth
   data_hl = loadmat('DataHL.mat')
   ground_truth_hl = data_hl['DataHL']  # Adjust key as needed
   ```

### Data Format Requirements
- **Expression matrix**: (n_genes × n_timepoints) numpy array
- **Time points**: 1D array of time points (hours)
- **Ground truth**: Optional 1D array of known half-lives (hours)
- **Preprocessing**: Automatic Z-score normalization applied

## Key Parameters

- **λ (lambda_reg)**: Regularization parameter (default: 15, paper value)
- **percentile_threshold**: MSE filtering threshold (default: 9.0, paper's 9th percentile)
- **use_ks_filtering**: Enable KS statistical filtering (default: False for speed)
- **n_shuffles**: Number of KS test shuffles (default: 20)
- **smoothing_sigma**: Gaussian smoothing (default: 0, no smoothing)

## Output

The notebook produces:
- **Half-life estimates**: Array of predicted half-lives (hours)
- **Performance metrics**: Pearson correlation, valid estimate percentage
- **Error analysis**: Relative and absolute error distributions
- **Visualization**: 3-panel figure showing model performance

## Expected Results

With default parameters on simulated data:
- **Valid estimates**: ~90% of genes
- **Correlation**: r ≈ 0.55-0.65 (depending on parameters)
- **Median relative error**: ~20-30%
- **Median absolute error**: ~1-2 hours

## Troubleshooting

**Common Issues:**
- **Memory errors**: Reduce dataset size (n_genes) for large datasets
- **Slow execution**: Disable KS filtering or reduce n_shuffles
- **Low correlation**: Check data preprocessing and parameter settings
- **NaN values**: Ensure proper data cleaning and normalization

**Performance Optimization:**
- For speed: Set `use_ks_filtering=False` and `percentile_threshold=50.0`
- For accuracy: Enable KS filtering with `n_shuffles=100`
- For large datasets: Process in batches or reduce pairwise comparisons

## Citation

If you use this implementation, please cite:
```
StaRTrEK: Stable Transcript half-life Estimation using Kolmogorov filtering
PMC9123730
```

## License

This implementation is provided for educational and research purposes. Please respect the original paper's license and citation requirements.
