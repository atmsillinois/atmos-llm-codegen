# Bogota Precipitation Type Classifier CLI

## Overview

A Python CLI script that trains a Random Forest model to classify precipitation type (rain vs snow) at Bogota, Colombia using surface meteorological observations.

## Target Location

- **City**: Bogota, Colombia
- **Coordinates**: ~4.6°N, 74.1°W
- **Elevation**: ~2,640m (high altitude tropical climate)
- **Note**: Due to Bogota's high elevation, snow is rare but possible. The model will learn to distinguish conditions where snow could occur.

## Input Features

| Feature | Variable | Units | Description |
|---------|----------|-------|-------------|
| Temperature | `t2m` | K or °C | 2-meter air temperature |
| Humidity | `rh` or `d2m` | % or K | Relative humidity or dewpoint |
| Pressure | `sp` or `msl` | Pa or hPa | Surface or mean sea level pressure |
| Rainfall | `tp` | mm | Total precipitation amount |
| Precipitation Type | `ptype` | categorical | ERA5 precipitation type flag |

## Target Variable

Binary classification:
- `0` = Rain
- `1` = Snow

## Project Structure

```
atmos-llm-codegen/
├── src/
│   └── atmos_llm_codegen/
│       ├── __init__.py
│       ├── cli.py              # Main Typer CLI entry point
│       └── models/
│           ├── __init__.py
│           └── precip_classifier.py  # Random Forest training logic
├── data/
│   └── bogota/                 # Downloaded/cached ERA5 data
├── models/
│   └── bogota_rf_precip.joblib # Saved trained model
├── pyproject.toml
└── plans/
    └── bogota-precip-classifier.md
```

## CLI Design (Typer)

### Commands

```bash
# Train the model
atmos-llm train-precip \
    --start-date 2010-01-01 \
    --end-date 2023-12-31 \
    --lat 4.6 \
    --lon -74.1 \
    --output models/bogota_rf_precip.joblib

# Predict precipitation type
atmos-llm predict-precip \
    --model models/bogota_rf_precip.joblib \
    --temp 2.5 \
    --humidity 95 \
    --pressure 1013.25 \
    --rainfall 5.2

# Evaluate model performance
atmos-llm eval-precip \
    --model models/bogota_rf_precip.joblib \
    --test-start 2024-01-01 \
    --test-end 2024-12-31
```

### CLI Options

#### `train-precip`

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--start-date` | str | Required | Training period start (YYYY-MM-DD) |
| `--end-date` | str | Required | Training period end (YYYY-MM-DD) |
| `--lat` | float | 4.6 | Latitude of location |
| `--lon` | float | -74.1 | Longitude of location |
| `--output` | Path | `models/precip_rf.joblib` | Output model path |
| `--n-estimators` | int | 100 | Number of trees in forest |
| `--max-depth` | int | None | Maximum tree depth |
| `--test-split` | float | 0.2 | Fraction for validation |
| `--random-state` | int | 42 | Random seed for reproducibility |

#### `predict-precip`

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--model` | Path | Required | Path to trained model |
| `--temp` | float | Required | Temperature (°C) |
| `--humidity` | float | Required | Relative humidity (%) |
| `--pressure` | float | Required | Pressure (hPa) |
| `--rainfall` | float | Required | Precipitation amount (mm) |

## Implementation Steps

### 1. Project Setup

- [ ] Create package structure under `src/atmos_llm_codegen/`
- [ ] Configure `pyproject.toml` with dependencies and entry points
- [ ] Set up Typer CLI scaffold

### 2. Data Acquisition Module

- [ ] Implement ERA5 data fetcher (or integrate with ERA5 MCP server)
- [ ] Extract point data for Bogota coordinates
- [ ] Handle hourly/daily aggregation
- [ ] Cache downloaded data locally

### 3. Data Preprocessing

- [ ] Load ERA5 variables (t2m, humidity, pressure, precipitation)
- [ ] Convert units (K to °C, Pa to hPa if needed)
- [ ] Filter for precipitation events only (tp > 0)
- [ ] Create binary target from precipitation type
- [ ] Handle missing values
- [ ] Feature scaling (optional for RF, but good practice)

### 4. Model Training

- [ ] Split data into train/validation sets
- [ ] Initialize `sklearn.ensemble.RandomForestClassifier`
- [ ] Fit model on training data
- [ ] Evaluate on validation set
- [ ] Report metrics (accuracy, precision, recall, F1, confusion matrix)
- [ ] Save model with `joblib`

### 5. Prediction Interface

- [ ] Load saved model
- [ ] Accept single observation inputs
- [ ] Return predicted class and probability
- [ ] Format output for CLI display

### 6. Evaluation Command

- [ ] Load model and test data
- [ ] Generate classification report
- [ ] Plot confusion matrix (optional, save to file)
- [ ] Calculate feature importances

## Dependencies

```toml
[project]
dependencies = [
    "typer>=0.9.0",
    "scikit-learn>=1.3.0",
    "pandas>=2.0.0",
    "numpy>=1.24.0",
    "xarray>=2023.0.0",
    "joblib>=1.3.0",
    "rich>=13.0.0",      # For CLI formatting
]

[project.optional-dependencies]
era5 = [
    "cdsapi>=0.6.0",     # ERA5 Climate Data Store API
]
```

## Model Considerations

### Class Imbalance

Snow is rare in Bogota. Strategies to address:
- Use `class_weight='balanced'` in RandomForestClassifier
- Consider SMOTE oversampling if severe imbalance
- Report class-specific metrics, not just accuracy

### Feature Engineering (Future)

Potential derived features:
- Wet-bulb temperature
- Temperature lapse rate (surface vs 850hPa)
- Pressure tendency (change over past hours)
- Seasonal indicators (month, day of year)

### Baseline Comparison

Compare RF performance against:
- Temperature threshold rule (snow if T < 0°C)
- Logistic regression
- Simple decision tree

## Output Examples

### Training Output

```
Training Random Forest classifier for Bogota precipitation type...

Data Summary:
  Period: 2010-01-01 to 2023-12-31
  Total precipitation events: 15,234
  Rain events: 15,180 (99.6%)
  Snow events: 54 (0.4%)

Training with 80/20 split...
  Training samples: 12,187
  Validation samples: 3,047

Validation Results:
  Accuracy: 0.987
  Precision (snow): 0.72
  Recall (snow): 0.65
  F1 (snow): 0.68

Model saved to: models/bogota_rf_precip.joblib
```

### Prediction Output

```
Prediction for input conditions:
  Temperature: 2.5°C
  Humidity: 95%
  Pressure: 1013.25 hPa
  Rainfall: 5.2 mm

Result: SNOW (probability: 0.73)
```

## Testing Strategy

- Unit tests for data preprocessing functions
- Integration test for full training pipeline with mock data
- Validation against known meteorological rules (snow unlikely above 5°C)