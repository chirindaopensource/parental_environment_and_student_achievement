# `README.md`

# An Independent Implementation of "Parental environment and student achievement: Does a Matthew effect exist?"

<!-- PROJECT SHIELDS -->
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Python Version](https://img.shields.io/badge/python-3.9%2B-blue.svg)](https://www.python.org/)
[![arXiv](https://img.shields.io/badge/arXiv-2510.18481v1-b31b1b.svg)](https://arxiv.org/abs/2510.18481v1)
[![Year](https://img.shields.io/badge/Year-2025-purple)](https://github.com/chirindaopensource/parental_environment_and_student_achievement)
[![Discipline](https://img.shields.io/badge/Discipline-Economics%20of%20Education-00529B)](https://github.com/chirindaopensource/parental_environment_and_student_achievement)
[![Data Sources](https://img.shields.io/badge/Data-Madrid%20Gov%20%7C%20Barro--Lee-lightgrey)](https://github.com/chirindaopensource/parental_environment_and_student_achievement)
[![Core Method](https://img.shields.io/badge/Method-IV%20%7C%20Fixed%20Effects-orange)](https://github.com/chirindaopensource/parental_environment_and_student_achievement)
[![Analysis](https://img.shields.io/badge/Analysis-Causal%20Inference%20%7C%20Matthew%20Effect-red)](https://github.com/chirindaopensource/parental_environment_and_student_achievement)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![Type Checking: mypy](https://img.shields.io/badge/type%20checking-mypy-blue)](http://mypy-lang.org/)
[![NumPy](https://img.shields.io/badge/numpy-%23013243.svg?style=flat&logo=numpy&logoColor=white)](https://numpy.org/)
[![Pandas](https://img.shields.io/badge/pandas-%23150458.svg?style=flat&logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![Matplotlib](https://img.shields.io/badge/matplotlib-%2311557c.svg?style=flat&logo=matplotlib&logoColor=white)](https://matplotlib.org/)
[![linearmodels](https://img.shields.io/badge/linearmodels-003F72-blue)](https://bashtage.github.io/linearmodels/)
[![PyYAML](https://img.shields.io/badge/PyYAML-gray?logo=yaml&logoColor=white)](https://pyyaml.org/)
[![Faker](https://img.shields.io/badge/Faker-lightgrey)](https://faker.readthedocs.io/)
[![Jupyter](https://img.shields.io/badge/Jupyter-%23F37626.svg?style=flat&logo=Jupyter&logoColor=white)](https://jupyter.org/)
---

**Repository:** `https://github.com/chirindaopensource/parental_environment_and_student_achievement`

**Owner:** 2025 Craig Chirinda (Open Source Projects)

This repository contains an **independent**, professional-grade Python implementation of the research methodology from the 2025 paper entitled **"Parental environment and student achievement: Does a Matthew effect exist?"** by:

*   Gaëlle Aymeric
*   Emmanuelle Lavaine
*   Brice Magdalou

The project provides a complete, end-to-end computational framework for replicating the paper's findings. It delivers a modular, auditable, and extensible pipeline that executes the entire research workflow: from rigorous data validation and feature engineering to the core econometric estimations (OLS and IV with Fixed Effects) and the final generation of all tables, figures, and reports.

## Table of Contents

- [Introduction](#introduction)
- [Theoretical Background](#theoretical-background)
- [Features](#features)
- [Methodology Implemented](#methodology-implemented)
- [Core Components (Notebook Structure)](#core-components-notebook-structure)
- [Key Callable: `execute_full_study`](#key-callable-execute_full_study)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Input Data Structure](#input-data-structure)
- [Usage](#usage)
- [Output Structure](#output-structure)
- [Project Structure](#project-structure)
- [Customization](#customization)
- [Contributing](#contributing)
- [Recommended Extensions](#recommended-extensions)
- [License](#license)
- [Citation](#citation)
- [Acknowledgments](#acknowledgments)

## Introduction

This project provides a Python implementation of the methodologies presented in the 2025 paper "Parental environment and student achievement: Does a Matthew effect exist?". The core of this repository is the iPython Notebook `parental_environment_and_student_achievement_draft.ipynb`, which contains a comprehensive suite of functions to replicate the paper's findings, from initial data validation to the final generation of all analytical tables and figures.

The paper investigates the causal impact of parental environment on student achievement, distinguishing between a static "persistent effect" and a dynamic "Matthew Effect" (the changing influence of parental background over time). This codebase operationalizes the paper's framework, allowing users to:
-   Rigorously validate and manage the entire experimental configuration via a single `config.yaml` file.
-   Process raw student survey data and external instrument data, applying a sequence of cleaning, harmonization, and feature engineering steps.
-   Estimate all 33 econometric models from the paper, including pooled OLS, interaction OLS, and their 2SLS instrumental variable counterparts, all with high-dimensional (school and year) fixed effects and clustered standard errors.
-   Run a full suite of diagnostic and robustness checks to validate the credibility of the findings.
-   Automatically generate all key figures and a final narrative report summarizing the results and policy implications.

## Theoretical Background

The implemented methods are grounded in modern microeconometrics and causal inference.

**1. High-Dimensional Fixed Effects (HDFE) OLS:**
The baseline models for the persistent effect (Equation 1) and the Matthew Effect (Equation 2) are estimated via OLS with fixed effects for both school ($b_s$) and academic year ($a_t$).
$$
Y_{its} = \alpha_0 + \sum_{j=1,2} \alpha_j p_{ji} + \text{Controls} + a_t + b_s + \epsilon_{its} \quad (1)
$$
$$
Y_{igts} = \dots + \sum_{j=1,2} \theta_j p_{ji} I(u,v) + \dots \quad (2)
$$
The fixed effects are absorbed using the Frisch-Waugh-Lovell theorem to control for all time-invariant school characteristics and year-specific shocks.

**2. Two-Stage Least Squares (2SLS) with an Instrumental Variable (IV):**
To address the endogeneity of parental education (`PHE`), the study employs a 2SLS strategy. The instrument is the gender gap in tertiary education in the parent's country of origin in 1960 (`GG`).
-   **First Stage (Relevance):** The endogenous variable is regressed on the instrument and all exogenous controls.
    $$
    \text{PHE}_i = \pi_0 + \pi_1 \text{GG}_i + \text{Controls} + a_t + b_s + \nu_{its} \quad (3)
    $$
-   **Second Stage (Causal Effect):** The outcome is regressed on the *predicted* value of the endogenous variable from the first stage.
    $$
    Y_{its} = \alpha_0 + \alpha_1 \widehat{\text{PHE}}_i + \text{Controls} + a_t + b_s + \epsilon_{its} \quad (4)
    $$
This methodology is extended to the interaction models to estimate the *causal* Matthew Effect.

**3. Cluster-Robust Standard Errors:**
All models are estimated with standard errors clustered at the `school_id` level. This accounts for the fact that residuals for students within the same school may be correlated, providing more conservative and reliable statistical inference.

## Features

The provided iPython Notebook (`parental_environment_and_student_achievement_draft.ipynb`) implements the full research pipeline, including:

-   **Modular, Multi-Task Architecture:** The entire pipeline is broken down into 27 distinct, modular tasks, each with its own orchestrator function, ensuring clarity and testability.
-   **Configuration-Driven Design:** All study parameters are managed in an external `config.yaml` file, allowing for easy customization and replication.
-   **Rigorous Data Validation:** A multi-stage validation process checks the schema, content integrity, and statistical properties of all input data before any analysis begins.
-   **Systematic Feature Engineering:** A transparent, step-by-step process for constructing the main endogenous variable (`PHE`), the instrumental variable (`GG`), and the full set of dummy-coded control variables.
-   **Advanced Econometric Engine:** A generic estimation function that correctly handles high-dimensional fixed effects and clustered standard errors for both OLS and 2SLS models using the `linearmodels` library.
-   **Comprehensive Estimation Suite:** Programmatically specifies and estimates all 33 models from the paper.
-   **Automated Diagnostics and Reporting:** Concludes by automatically generating all key figures, a full suite of robustness checks, and a final narrative summary of the findings.

## Methodology Implemented

The core analytical steps directly implement the methodology from the paper:

1.  **Data Validation & Cleaning (Tasks 1-5):** Ingests and validates the `config.yaml` and raw data, cleanses the student survey data by applying inclusion criteria, and harmonizes all categorical codings.
2.  **Feature Engineering (Tasks 6-8):** Constructs the `PHE` variable and its dummies, creates the full set of control variables, and constructs the `GG` instrumental variable.
3.  **Sample & Model Definition (Tasks 9-11):** Programmatically defines the 33 distinct analysis samples using listwise deletion and creates the formal specifications for all OLS and IV models.
4.  **Estimation (Tasks 12-20):** Executes all OLS and IV regressions (pooled, by-grade, and interaction), extracting and formatting the results.
5.  **Visualization & Diagnostics (Tasks 21-24):** Generates all CDF and coefficient plots, and performs final diagnostic checks on sample composition and data normalization.
6.  **Robustness & Reporting (Tasks 25-27):** Provides top-level orchestrators to run the full pipeline, execute a suite of robustness checks, and synthesize the final narrative report.

## Core Components (Notebook Structure)

The `parental_environment_and_student_achievement_draft.ipynb` notebook is structured as a logical pipeline with modular orchestrator functions for each of the major tasks. All functions are self-contained, fully documented with type hints and docstrings, and designed for professional-grade execution.

## Key Callable: `execute_full_study`

The project is designed around a single, top-level user-facing interface function:

-   **`execute_full_study`:** This master orchestrator function, located in the final section of the notebook, runs the entire automated research pipeline from end-to-end. A single call to this function reproduces the entire computational portion of the project, from data validation to the final report generation.

## Prerequisites

-   Python 3.9+
-   Core dependencies: `pandas`, `numpy`, `matplotlib`, `linearmodels`, `pyyaml`, `faker`.

## Installation

1.  **Clone the repository:**
    ```sh
    git clone https://github.com/chirindaopensource/parental_environment_and_student_achievement.git
    cd parental_environment_and_student_achievement
    ```

2.  **Create and activate a virtual environment (recommended):**
    ```sh
    python -m venv venv
    source venv/bin/activate  # On Windows, use `venv\Scripts\activate`
    ```

3.  **Install Python dependencies:**
    ```sh
    pip install pandas numpy matplotlib linearmodels pyyaml Faker
    ```

## Input Data Structure

The pipeline requires two `pandas.DataFrame` objects with specific schemas, which are rigorously validated by the pipeline. A synthetic data generator is included in the notebook for demonstration purposes.
1.  **`student_parent_survey_raw`**: The primary dataset containing student and parent information.
2.  **`barro_lee_raw`**: The external dataset with historical education data for constructing the instrument.

All other parameters are controlled by the `config.yaml` file.

## Usage

The `parental_environment_and_student_achievement_draft.ipynb` notebook provides a complete, step-by-step guide. The primary workflow is to execute the final cell of the notebook, which demonstrates how to use the top-level `execute_full_study` orchestrator:

```python
# Final cell of the notebook

# This block serves as the main entry point for the entire project.
if __name__ == '__main__':
    # The notebook includes a synthetic data generation stage.
    # We assume `student_parent_survey_raw` and `barro_lee_raw` are in memory.
    
    # Load the configuration from the YAML file.
    with open('config.yaml', 'r') as f:
        config = yaml.safe_load(f)
    
    # Define the top-level directory for all outputs.
    RESULTS_DIRECTORY = "study_replication_output"

    # Execute the entire replication study.
    full_results = execute_full_study(
        student_parent_survey_raw=student_parent_survey_raw,
        barro_lee_raw=barro_lee_raw,
        config=config,
        output_dir=RESULTS_DIRECTORY
    )
```

## Output Structure

The pipeline creates a `base_output_dir` and returns a comprehensive dictionary containing all analytical artifacts, structured as follows:
-   **`main_analysis`**: Contains all primary results.
    -   `results`: Nested dictionary of all regression tables (DataFrames).
    -   `diagnostics`: Diagnostic tables (e.g., sample flow).
    -   `figures`: List of paths to all generated plots (PNG files).
-   **`robustness_checks`**: Contains all sensitivity analysis reports.
-   **`final_summary_report`**: A string containing the final narrative summary.

## Project Structure

```
parental_environment_and_student_achievement/
│
├── parental_environment_and_student_achievement_draft.ipynb  # Main implementation notebook
├── config.yaml                                               # Master configuration file
├── requirements.txt                                          # Python package dependencies
│
├── study_replication_output/                                 # Example output directory
│   └── figures/
│       ├── cdf_math_grade_3.png
│       └── ...
│
├── LICENSE                                                   # MIT Project License File
└── README.md                                                 # This file
```

## Customization

The pipeline is highly customizable via the `config.yaml` file. Users can easily modify all study parameters, including date ranges, variable definitions, and model specifications, without altering the core Python code.

## Contributing

Contributions are welcome. Please fork the repository, create a feature branch, and submit a pull request with a clear description of your changes. Adherence to PEP 8, type hinting, and comprehensive docstrings is required.

## Recommended Extensions

Future extensions could include:
-   **Alternative Instruments:** Integrating and testing alternative instrumental variables to check the robustness of the causal claims.
-   **Heterogeneity Analysis:** Exploring how the Matthew Effect varies across different subgroups of the population (e.g., by gender, immigrant status, or school type).
-   **Machine Learning Models:** Using non-parametric or machine learning methods (e.g., causal forests) to explore potential non-linearities in the treatment effects.
-   **Structural Modeling:** Building and estimating a structural model of skill formation to provide deeper theoretical insights into the observed dynamic patterns.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.

## Citation

If you use this code or the methodology in your research, please cite the original paper:

```bibtex
@article{aymeric2025parental,
  title={Parental environment and student achievement: Does a Matthew effect exist?},
  author={Aymeric, Ga{\"e}lle and Lavaine, Emmanuelle and Magdalou, Brice},
  journal={arXiv preprint arXiv:2510.18481},
  year={2025}
}
```

For the implementation itself, you may cite this repository:
```
Chirinda, C. (2025). A Professional-Grade Implementation of "Parental environment and student achievement: Does a Matthew effect exist?".
GitHub repository: https://github.com/chirindaopensource/parental_environment_and_student_achievement
```

## Acknowledgments

-   Credit to **Gaëlle Aymeric, Emmanuelle Lavaine, and Brice Magdalou** for the foundational research that forms the entire basis for this computational replication.
-   This project is built upon the exceptional tools provided by the open-source community. Sincere thanks to the developers of the scientific Python ecosystem, including **Pandas, NumPy, Matplotlib, and the `linearmodels` library**.

--

*This README was generated based on the structure and content of the `parental_environment_and_student_achievement_draft.ipynb` notebook and follows best practices for research software documentation.*
