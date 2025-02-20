
---
title: "AutoLDLStack: A Guide to Stacking Models for LDL Prediction"
author: "AutoLDLStack Team"
date: "`r Sys.Date()`"
output:
  github_document:
    toc: true
    toc_depth: 3
    number_sections: true
---

## Introduction

`AutoLDLStack` is an R package designed to predict **LDL cholesterol** using **stacking ensemble models**. It integrates traditional **LDL estimation equations, clinical features, and multiple machine learning algorithms** for robust and automated LDL estimation.

This tutorial covers:
- Installing and loading the package
- Using built-in datasets
- Training a stacking model
- Evaluating model performance
- Making predictions

## Installation & Loading the Package

```r
# Install devtools if not installed
if (!requireNamespace("devtools", quietly = TRUE)) install.packages("devtools")

# Install AutoLDLStack from GitHub
# devtools::install_github("YourGitHubRepo/AutoLDLStack")

# Load the package
library(AutoLDLStack)
```

## Exploring the Data

The package includes a dataset **LDLdata**, containing necessary features for LDL estimation.

```r
# Load the dataset
data(LDLdata)
LDLdata <- as.data.frame(LDLdata)

# View structure and summary
str(LDLdata)
summary(LDLdata)
```

## Training a Stacking Model

To train a stacking model, use the `train_stacked_model()` function:

```r
# Train the model with 10-fold Cross Validation
StackModeltrain <- train_stacked_model(LDLdata, nCV = 10)
```

## Using the Pre-trained Model

To get started quickly, users can load a **pre-trained stacking model**:

```r
# Load the pre-trained model
data(StackModeltrain)

# Inspect model structure
str(StackModeltrain)
```

## Visualizing Model Performance

The function `visualizeModelPerformance()` generates key performance plots.

```r
# Generate visualization plots
visualizeModelPerformance_plots <- visualizeModelPerformance(StackModeltrain)

# Display Learning Curve and Actual vs. Predicted plot
grid.arrange(visualizeModelPerformance_plots$Canvas1)
```

> **📌 Note:** The plot above includes the learning curve (Train/Test RMSE) and the scatter plot of Actual vs. Predicted LDL values.

```r
# Display Resamples - RMSE and MAE
grid.arrange(visualizeModelPerformance_plots$Canvas2)
```

> **📌 Note:** This plot compares performance across different baseline models.

## Feature Importance

The stacking model includes **feature importance analysis**, which helps identify the most influential predictors.

```r
# View feature importance
StackModeltrain$var_importance
```

## Making Predictions

### Using the Stacking Model

```r
# Predict LDL values on test data
predictions <- predict(StackModeltrain$stacked_model, newdata = StackModeltrain$test_data)
print(predictions)
```

### Using Individual Baseline Models

```r
# Predict using base models (ensemble members)
predictions_rf <- predict(StackModeltrain$base_models$rf, newdata = StackModeltrain$test_data)
print(predictions_rf)
```

## Adding LDL Classifications

The function `fAddLDLClassifications()` computes LDL classifications based on clinical guidelines.

```r
# Compute classifier labels
LDLdata_labeled <- fAddLDLClassifications(LDLdata, ldl_col = "LDLd")
head(LDLdata_labeled)
```

## Computing LDL Using Equations

To generate LDL predictions using multiple equations:

```r
library(LDLcalc)

# Compute LDL estimates for each row in the dataset
ldl_matrix <- t(
  apply(LDLdata[, c("CHOL", "HDL", "TG")], 1, function(x) {
    get_LDL_named_vector(x[1], x[2], x[3])
  })
)

# Convert to DataFrame and merge with original data
ldl_df <- as.data.frame(ldl_matrix, stringsAsFactors = FALSE)
new_data <- cbind(LDLdata, LDLdata_labeled, ldl_df)

# View transformed dataset
head(new_data)
```

## Conclusion

With **AutoLDLStack**, users can:
✅ Train and evaluate a **stacking ensemble model** for LDL prediction  
✅ Use **pre-trained models** for quick results  
✅ Analyze feature importance  
✅ Compute LDL using **estimation equations** and classifier labels  
✅ Generate visualizations to **assess model performance**  

---

🚀 **Ready to try it?** Clone the repository and start predicting LDL with cutting-edge ensemble models!
