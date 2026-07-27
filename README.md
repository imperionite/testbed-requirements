# Workflow for the Validation and Analysis of Software Requirements for the Proposed SIMS

Repository reference for the thesis:

**Serverless Internship Management System for Higher Education Institutions**

This repository contains the requirements validation and data analysis workflow used to refine the proposed system design for a serverless internship management system for higher education institutions.

## Project Purpose

The goal of this project is to analyze stakeholder feedback and survey ratings to validate, clarify, and improve the system requirements for an Internship Management System intended for higher education institutions.

The repository is organized as a research support workspace and is used as a reference for thesis documentation, requirement refinement, and system scope validation.

## Repository Structure

```text
testbed-requirement-analysis/
├── qualitative_data_requirement_analysis.ipynb
├── quantitative_data_requirement_analysis.ipynb
└── datasets/
    ├── qualitative_data.csv
    └── quantitative_data.csv
```

## Notebooks

### Qualitative Data Analysis

This notebook performs manual thematic analysis assisted by Python. It processes open-ended responses to identify:

- unnecessary or redundant requirements,
- improvement suggestions,
- missing features,
- comments on usability, security, and scalability.

Key themes observed include:

- scope reduction,
- redundancy removal,
- external system integration,
- authentication and role clarification,
- notification support,
- document workflow,
- audit support,
- reporting and compliance.

### Quantitative Data Analysis

This notebook analyzes numeric ratings for functional and non-functional requirements. It includes:

- data validation,
- descriptive statistics,
- approval distribution,
- mean scores by requirement,
- grouped analysis by respondent role.

The results help show which requirements were rated highest and which areas were most consistently supported by respondents.

## Datasets

### qualitative_data.csv

Contains open-ended stakeholder feedback fields such as:

- unnecessary requirements,
- improvement suggestions,
- missing features,
- comments,
- approval status,
- response quality.

### quantitative_data.csv

Contains numeric requirement ratings and respondent metadata such as:

- role group,
- experience level,
- familiarity level,
- functional requirement scores,
- non-functional requirement scores,
- approval status.

## Main Findings

The analysis indicates strong support for the proposed system requirements overall. The qualitative feedback suggests that some items should be simplified, consolidated, or clarified, especially around attendance tracking, role access, document management, reporting, and external integration. The quantitative analysis shows that most requirements received very high ratings, and the approval rate is strongly positive.

## Thesis Relevance

This repository serves as a supporting reference for the thesis titled:

**Serverless Internship Management System for Higher Education Institutions**

It documents the requirement validation workflow used to:

- justify system scope,
- validate user needs,
- identify missing features,
- refine functional and non-functional requirements,
- support thesis methodology and design decisions.

## How to Run

1. Install Python 3.10 or later.
2. Install dependencies:
   ```bash
   pip install pandas jupyter
   ```
3. Open the notebooks in Jupyter Notebook or JupyterLab.
4. Run the notebooks from top to bottom.

## Suggested Citation Note

If this repository is used in the thesis, it may be cited as a project artifact or research support repository for requirement validation and data analysis.

## Author

Arnel Imperial
