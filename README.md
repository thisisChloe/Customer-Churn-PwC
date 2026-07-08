# Customer-Churn-PwC: Churn Analysis Report

## Project Overview
This project is an exploratory data analysis of customer churn for a telecom provider, based on a dataset of 7,043 customers and 23 attributes (02 Churn-Dataset.xlsx). The notebook churn_eda_v2.ipynb walks through data cleaning, churn pattern discovery, revenue impact quantification, and a simple rule-based risk scoring approach for flagging customers likely to leave.

## Data Preparation
The raw TotalCharges field was stored as text and coerced to numeric, with the handful of blank values (all belonging to brand-new customers with zero tenure) filled in as zero. The Churn column was converted into a binary Churned flag to support aggregation and correlation analysis throughout the notebook.

## Headline Numbers
Across the full customer base, the overall churn rate is 26.5%. Churn is heavily concentrated among customers in their first year: those with under 12 months of tenure churn at 48.3%, nearly double the baseline. Month-to-month contract holders churn at 42.7%, fiber optic internet subscribers at 41.9%, and customers without online security or tech support add-ons churn at roughly 42% as well. Senior citizens also churn well above average, at 41.7%.

## Revenue at Risk
Total monthly recurring revenue across the customer base is roughly $456,000, of which about $139,000, or 30.5%, is tied to customers who have already churned, meaning churners disproportionately skew toward higher-paying accounts. Month-to-month contracts alone account for around $121,000 of monthly revenue lost to churn, versus a combined $18,000 for one- and two-year contracts, making contract length the single largest lever for revenue retention.

## The Fiber Optic Paradox
Fiber optic customers pay noticeably more on average, $91.50 per month, than DSL customers at $58.10 per month, yet also churn more than twice as often, 41.9% versus 19.0%. The notebook traces part of this gap to service experience: fiber customers log nearly triple the average tech support tickets of DSL customers, and are more likely to be on flexible month-to-month contracts, suggesting reliability or support issues, not price alone, are pushing higher-value fiber customers out.

## Service Bundling and Support Tickets
Customers with more add-on services subscribed, such as security, backup, device protection, tech support, and streaming, tend to churn less and pay more, with churn dropping from over 40% for customers with only one or two services to under 6% for customers with all seven. Support ticket volume shows a similar tipping point: churn stays in the 20 to 40% range for customers with up to five total tickets, then jumps sharply to 70 to 83% for customers with six or more, pointing to repeated unresolved issues as a strong churn trigger.

## High-Value Churners
Focusing on the top 20% of customers by monthly charge, $94.25 and above, 467 high-value customers churned, representing a quarter of all churners and about $47,000 in monthly revenue. This group is disproportionately on month-to-month contracts, 75.4%, entirely on fiber optic internet, 100%, and largely without tech support, 70.9%, reinforcing the same risk pattern seen across the broader dataset.

## Rule-Based Risk Scoring
Rather than building a predictive model, the notebook constructs an additive risk score from seven binary flags: month-to-month contract, fiber optic service, no online security, no tech support, tenure under 12 months, three or more support tickets, and senior citizen status. This simple score correlates strongly with actual churn, rising from about 1% churn at a score of zero to roughly 74% churn at a score of six. Applying this score to still-active customers identifies 423 people with a risk score of five or higher, representing about $34,000 in monthly recurring revenue that is currently at elevated risk.

## Recommendations
The analysis points toward a small number of high-leverage retention actions: prioritizing contract upgrades away from month-to-month plans, auditing fiber optic service quality and support responsiveness, proactively reaching out to customers after their third support ticket rather than their sixth, and using the risk score to target the 423 active high-risk accounts identified above before they churn.

## Repository Contents
The repository includes churn_eda_v2.ipynb, the full exploratory analysis notebook with all cleaning steps, charts, and computed statistics referenced in this report, along with 02 Churn-Dataset.xlsx, the underlying customer-level dataset used for the analysis.

## Getting Started
Clone this repository, install the required dependencies (pandas, numpy, matplotlib, seaborn, and jupyter), then open churn_eda_v2.ipynb in Jupyter Notebook or JupyterLab and run the cells in order to reproduce the analysis and figures.

## Status
This repository currently reflects the exploratory analysis phase of the project. Future work may extend the rule-based risk score into a full predictive model.
