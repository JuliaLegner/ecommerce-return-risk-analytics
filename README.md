## E-Commerce Return Risk Analytics

A transaction-level analytics project for estimating the financial
risk of e-commerce returns, developed as part of an MSc Data Science
group project.

Rather than ranking purchases only by how likely they are to be
returned, the project introduces an Expected Return Loss (ERL) score
that combines return likelihood, likely return reason, and the financial
cost associated with that reason.

## Business Problem

E-commerce returns create costs beyond refunded revenue, including
return shipping, inspection, restocking, disposal, and markdown losses.
These costs can vary substantially depending on why a product is
returned.

A probability-only model treats two transactions with the same return
probability as equally important. From a business perspective, however,
a return involving a high-value defective product can create much
greater financial exposure than a low-cost size-related return.

This project therefore asks:

### Can return probability, return reason, and reason-specific cost be
### combined into a single transaction-level financial risk score?

## Project Overview

The complete project was designed as a four-stage pipeline:

### Return Probability Modelling (ST1) -- 
estimates the likelihood that a transaction will be returned.

### Return Reason Classification (ST2) -- 
estimates the probability distribution across five return reasons.

### Expected Return Loss Construction (ST3) -- 
combines probability, reason distribution, and financial cost into an ERL score.

### Intervention Evaluation (ST4) -- 
evaluates whether return-prevention actions are financially worthwhile.

This repository focuses primarily on ST3: Expected Return Loss
construction, which was my main individual contribution to the group
project.

## Expected Return Loss Framework

For transaction i, Expected Return Loss is calculated as:

### ERLᵢ = P(returnᵢ) × Σᵣ [P(reasonᵣ | i) × C(reasonᵣ)]

where:

P(returnᵢ) = estimated return likelihood from ST1

P(reasonᵣ | i) = probability of each return reason from ST2

C(reasonᵣ) = estimated financial cost associated with that
reason

The five return-reason categories are:

size_fit

defect

expectation_mismatch

wrong_item

changed_mind

Most costs are represented as flat processing estimates. Defect cost is
value-dependent:

Defect Cost = £15 + 20% × Order Value

This allows higher-value products with substantial defect exposure to
receive a higher financial-risk score even when their return probability
is not especially high.

## Data

The overall framework uses 85,187 completed fictional transactions
from Google's synthetic TheLook e-commerce dataset.

## ST3 receives two upstream inputs:

a transaction-level return-likelihood output from ST1

a product-type return-reason probability matrix from ST2

The project uses synthetic/public data and should be interpreted as a
prototype rather than a production retail risk model.

ST3 Pipeline

The ST3 notebook performs the following workflow:

ST1 Return Probability Output
            +
ST2 Return Reason Distribution
            |
            v
      Data Validation
            |
            v
       Cost Matrix
            |
            v
Reason-Weighted Expected Cost
            |
            v
 Expected Return Loss (ERL)
            |
            v
  Transaction Risk Ranking
            |
            v
Loss Capture + Risk Tier Analysis
            |
            v
      Output for ST4

The implementation also validates required fields, probability ranges,
positive order values, reason-probability sums, and product-type
coverage before calculating financial exposure.

## Key Results

The analysis demonstrates that return likelihood and financial return
risk are not equivalent.

Result                                                           Value

Transactions analysed                                           85,187
Transactions shifting 1,000+ ranking positions          80,968 (95.0%)
Top 10% exposure captured using ERL ranking                      16.6%
Top 10% exposure captured using probability ranking              12.5%
Gini coefficient -- ERL ranking                                 0.1601
Gini coefficient -- probability ranking                         0.1058
Relative Gini improvement                                        1.51×

Introducing financial cost caused 95% of transactions to move by more
than 1,000 positions compared with ranking transactions by return
probability alone.

At the top 10% targeting threshold, ERL ranking captured 16.6% of
total financial exposure, compared with 12.5% using probability-only
ranking.

The Gini coefficient increased from 0.1058 to 0.1601, indicating
greater concentration of financial exposure under the ERL-based ranking.

## Risk Tiers

Transactions are segmented into risk tiers based on ERL. The analysis
found that observed return rates remained relatively similar across the
tiers, while average ERL increased substantially.

This illustrates the central insight of the project:

A high-risk transaction is not necessarily one that is more likely
to be returned; it may instead be one that is considerably more
expensive if a return occurs.

Defect-related costs were the dominant financial driver for many product
types, while size/fit exposure was particularly important for some
categories.

## Technologies & Skills

Programming & Analysis

Python

Pandas

NumPy

Matplotlib

Jupyter / Google Colab

### Data Science & Analytics

Data cleaning and validation

Data integration

Probability-based modelling

Cost-sensitive analytics

Expected-loss modelling

Risk scoring

Ranking analysis

Sensitivity analysis

Gini coefficient analysis

Data visualisation

Business-focused interpretation

## Repository Structure

ecommerce-return-risk-analytics/
│
├── README.md
├── notebooks/
│   └── st3_pipeline_colab.ipynb
│
├── report/
│   └── report_group_projectERL.pdf
│
├── data/
│   └── README.md
│
├── images/
│   └── project visualisations
│
└── requirements.txt

The original datasets and intermediate outputs may not be included in
this repository due to file size and project-distribution
considerations.

## How to Run

The ST3 notebook expects the processed outputs produced by the preceding
stages of the project.

Main input files used by the notebook include:

output_p_return.csv
table10_p_reason_product_type.csv

Install the required Python libraries:

pip install pandas numpy matplotlib

Then open:

notebooks/st3_pipeline_colab.ipynb

and run the notebook cells sequentially.

If the upstream input files are not included in the repository, the
notebook serves as a demonstration of the ERL methodology and
implementation rather than a fully standalone execution environment.

## My Contribution

This was a collaborative MSc Data Science project. My primary
responsibility was Sub-Topic 3: Expected Return Loss (ERL)
Construction.

My contribution included:

designing and implementing the ST3 methodology

integrating outputs from the return-probability and return-reason
stages

constructing the reason-specific financial cost framework

implementing the transaction-level ERL calculation in Python

validating the incoming data and pipeline joins

developing ERL-based transaction rankings and risk tiers

comparing ERL ranking with probability-only ranking

implementing loss-capture and Gini analyses

conducting sensitivity analysis

creating visualisations

performing formal analysis and interpretation of ST3 results

contributing to the project report, review, and editing

## Limitations

The results should be interpreted as a prototype and analytical
demonstration, not as production-ready estimates.

The primary transaction dataset is synthetic. In addition, the
return-probability model used upstream did not achieve the project's
predefined AUC-ROC validation threshold, so a scenario-based return
score was used instead. The reason-specific processing costs are also
based on published research and assumptions rather than
retailer-specific accounting data.

A production implementation should therefore use:

real historical transaction and return data

a clearly defined return window

validated transaction-level return probabilities

retailer-specific processing and markdown costs

improved labelled data for underrepresented return reasons

real intervention outcomes from controlled pilots

## Future Improvements

Future development could extend the prototype by incorporating real
retailer data, validating cost assumptions against operational records,
improving upstream probability estimates, and deploying the ERL score in
an interactive dashboard for commercial decision-making.

Potential extensions include Power BI or Tableau dashboards,
automated risk monitoring, category-level return-cost reporting, and
integration with intervention or inventory-management workflows.

Academic Context

Developed for the COMP1884 Group Project as part of the MSc Data
Science and Its Applications programme at the University of Greenwich.

The full academic report is included in the report/ directory for
methodology, literature review, assumptions, limitations, and complete
project results.

## Author

Julia Legner
MSc Data Science and Its Applications

Primary project contribution: Expected Return Loss (ERL) modelling,
financial risk analytics, Python implementation, formal analysis, and
visualisation.
