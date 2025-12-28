# 🔍 GA-Based Feature Selection for Fault Detection
### Optimizing Sensor Observability in Semiconductor Manufacturing

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?style=flat&logo=python) 
![Status](https://img.shields.io/badge/Status-Optimization_Active-success)
![Focus](https://img.shields.io/badge/Focus-Observability%20%26%20RCA-orange)

## 📋 Overview
The goal of this project is to apply a **Hybrid Genetic Algorithm (HGA)** for feature reduction in high-dimensional manufacturing data. By using a **Random Forest (binary classifier)** as the fitness function, the system identifies the most critical sensors for fault detection.

This addresses the "Curse of Dimensionality" in the [SECOM dataset](https://archive.ics.uci.edu/dataset/179/secom), where 591 sensors create significant noise. Reducing this to a compact subset allows for:
* **Better Observability:** focusing on signals that actually matter.
* **Reduced Compute:** faster inference times for real-time monitoring.
* **Clearer RCA:** easier identification of root causes for manufacturing defects.

## ⚙️ Performance Engineering & Optimizations
*To ensure the solution scales, several engineering optimizations were implemented in `HGA_generic.ipynb`:*

* **Parallelization:** The `evaluate_population()` function is parallelized to assess multiple chromosome candidates simultaneously, significantly reducing runtime.
* **Smart Caching:** Implemented a caching mechanism to store `(individual, fitness)` pairs. The population is pre-filtered so only new, unique individuals are calculated, avoiding redundant model training.
* **Probability-Based Initialization:** Population initialization is weighted by `probability = desired_features / total_features`. This biases the algorithm to focus on smaller, more efficient feature subsets early in the evolutionary process.
* **Current Performance:** ~8 minutes for 10 generations (Pop=100, Target Features=50) with early convergence.

## 📂 Dataset Information
**Source:** [UCI Machine Learning Repository - SECOM](https://archive.ics.uci.edu/dataset/179/secom)

* **Structure:** 1567 examples × 591 features.
* **Format:** Raw text file with space-separated features. Null values are represented as `NaN`.
* **Components:**
    * `secom.data`: The 1567 x 591 feature matrix.
    * `secom_labels.data`: Classifications (Pass/Fail) and timestamps.
* **Preprocessing:** The `unifies_dataset.py` script merges these files into a single CSV located at `dataset/SECOM_combined_dataset.csv`.

## 🛠️ File Structure & Description

### Core Logic
* **`HGA_generic.ipynb`** *(Active Development)*
    * The main evolutionary engine.
    * **Updates:** Includes parallel fitness evaluation, caching strategies, and improved progress tracking.
    * **Results:** Recent tests show strong reduction capabilities (e.g., Generation 10 reached Best Fitness = -0.5814 with only **29 features selected**).
* **`HGA_with_synthetic_data (1).ipynb`** *(Legacy/Reference)*
    * Original modularized code. Kept as a reference for the probability-based initialization logic before the move to the generic version.
* **`unifies_dataset.py`**
    * ETL script. Reads the raw space-separated text files, handles `NaN` values, and unifies features with labels into a production-ready CSV.

### Validation & Tools
* **`syn_data_on_SCOM.ipynb`**
    * Generates synthetic data based on SECOM statistics. Used to validate that the HGA can recover "ground truth" features in a controlled environment.
* **`bestracker.py`**
    * Utility class for tracking experiment results (best chromosomes per generation) to analyze convergence over time.

## 🚀 Usage

1.  **Prepare Data:**
    ```bash
    python unifies_dataset.py
    ```
    *Creates `dataset/SECOM_combined_dataset.csv`.*

2.  **Run Optimization:**
    Open `HGA_generic.ipynb` in Jupyter/Colab.
    * Set your `desired_features` count.
    * Run the notebook to observe the Genetic Algorithm narrowing down the sensor list.

3.  **Analyze:**
    The notebook outputs progress logs (e.g., `Generation 10/10: Best Fitness = -0.5814`) to track how effectively the system is eliminating noise.

