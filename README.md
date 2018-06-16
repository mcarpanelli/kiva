# Kiva Data Analysis
June 16, 2018

### About Kiva
Kiva is an international nonprofit founded in 2005 with a mission to connect people through lending to alleviate poverty. In June 2018 Kiva was in 85 countries, and had served 2.9 Million borrowers through $1.16 Billion worth of loans.

### How it works
1. A borrower applies for a loan (through a micro-finance partner, or to Kiva directly).
2. The loan goes through the underwriting and approval process.
3. The loan is posted to Kiva for lenders to support, and a 30-day fundraising period begins.
4. Borrowers repay the loans.
    * Note that Kiva does not collect interest on loans, and Kiva lenders do not receive interest from loans they support on Kiva. However, Field Partners collect interest from borrowers to cover their operation costs.

### About Kiva's Data
This analysis uses Kiva's Data Snapshot (https://build.kiva.org/docs/data/snapshots) downloaded on June 16th, 2018.

### Today's Objectives
* Clean data as needed.
* Run general exploratory data analysis.
* Formulate a relevant business or research question.
    * Ideally related to Galvanize DSI Week 3 (probability and statistics) :)
* Provide evidence to support your conclusions.
* Present notebook to the group and publish.

### Considerations
1. The dataset has 34 features and >1.4 million loans (rows). To avoid crashing your computer consider:
    * Working with a subset of the data by passing nrows or skiprows arguments to Pandas' read_csv function. Example:
    ```
    import pandas as pd
    df = pd.read_csv(data, nrows=100000)
    ```
    * Overwriting (instead of copying) the dataframe after cleaning up features you will not include in your analysis.
    ```
    df = df.drop(['column_name1', 'column_name2'], axis=1)
    ```

2. Pass the parse_dates optional argument to the read_csv function in order to manipulate dates-columns easily (features named '*_TIME').
    ```
    df = pd.read_csv(data, parse_dates=['date_var1', 'date_var2'])
    ```

3. Check your data for missings. Here is a shortcut to building a summary table:
    ```
    total = df.isnull().sum().sort_values(ascending = False)
    percent = (df.isnull().sum()/df.isnull().count()).sort_values(ascending = False)
    missing_data = pd.concat([total, percent], axis=1, keys=['Total Missings', 'Percent Missings'])
    missing_data
    ```

4. Make sure you understand Kiva's business model. These are some worth-noting considerations:
* Read the data dictionary here: http://build.kiva.org/docs/data/loans. 
* The dataset contains both group and individual loans (i.e. loans where the borrower can be an individual or a group of individuals). You might want to analyze these separately.
    * If you want to create a new feature, here is how:
    ```
    df['GROUP'] = np.where(np.logical_and(df['BORROWER_GENDERS']!= 'male', df['BORROWER_GENDERS']!= 'female'), 1, 0)
    ```
* Kiva lends mostly through on-the-field microfinance business partners, but in some countries it also provides direct financing (see feature 'DISTRIBUTION_MODEL'). You might want to consider these two models separately.
* Loan requests are posted for a maximum of 30 days. If they're not funded within that timeframe, the loan expires and the Field Partner does not receive the funds for that particular loan.
    * If you are interested in the behavior of expired loans or loans that are about to expire, it might be useful to know how to compute days elapsed between two dates. Here is an example:
    ```
    df['diff_posted_planned'] = df['PLANNED_EXPIRATION_TIME'].sub(df['POSTED_TIME'], axis=0)
    df['diff_posted_planned'] = df['diff_posted_planned'] / np.timedelta64(1, 'D')
    ```
* First-time borrowers can raise up to $5,000. The maximum loan amount for all borrowers is $20,000.
* Kiva crowdfunds loans so there are many individual lenders who come together to contribute to each successful loan (see feature 'NUM_LENDERS_TOTAL'). A lender can lend $25 or more to a borrower to help them reach their goal, and see the other lenders who supported that borrower. Explore how this works here: https://www.kiva.org/lend. 