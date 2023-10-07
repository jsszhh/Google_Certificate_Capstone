# Methodology

Working in SQL and even Excel can be highly insightful for cleaning data and conducting some kinds of exploratory tasks, but if we really want to dive into a dataset and understand the relationships/make predictions about future data, we need to use statistical approaches and machine learning.

In Python, we can do all of this.

## Step 1. Machine Learning

First, I will start by conducting machine learning approaches on the dataset.

In machine learning approaches, the goal is to train a model to sort data points (in this case, students) into the correct categories of the DV (in this case, course grade), then test the generated model to determine its accuracy.

This is a good start to diving into the dataset because it can highlight the most important variables (features) from the model(s) that best predict the DV.

[Here is the link to the Jupyter Notebook containing all the Python work I did for this step.](https://github.com/jsszhh/Google_Certificate_Capstone/blob/main/Exploratory_Analyses/2.%20Python/PYTHON_ML_ANALYSES.ipynb "Machine Learning Jupyter Notebook") (_Note._ The Jupyter Notebook file is in this repository, directly below this file in this folder.)

## Step 2. Statistical Analyses

Next, I'll carry out traditional statistical procedures (namely, logistic regression) to explore how the generated machine learning models compare to the statistical analyses. Optimally, both approaches will yield similar (if not identical) outcomes, highlighting the most important variables for predicting a student's grades in their course from this dataset. Ideally, there can be predictive power, helping us as researchers understand how future datasets with similar population demographics can be approached.
