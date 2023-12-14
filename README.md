# Bank-Loan Analysis
#### Analysis on bank loan dataset using SQL

## Project Overview

The project aimed to analyze a dataset containing information about loan applications. The primary goal was to derive insights into various aspects of the loan application process and overall performance.

## Data Source

The dataset used for this analysis is the (Bank_financial_loan.csv), which provides comprehensive details about loan applications, details, and other financial metrics.

## Tool

The analysis was conducted using SQL

## Data Cleaning and Preparation

The dataset was uploaded into MYSQL from excel.csv. Data types were changed at the point of loading. 

## Problem Statement
  1. Total Number of Loan Applications

2. Total Amount of Loan Given, total Amount Received and outstanding debt.

3. Average Interest Rate and Average Debt-to-Income Ratio (DTI)

4. Employment Length Impact on Loan Approval

5. Loan Amount Distribution by Grade

6. Loan Performance Analysis

7. Income Distribution Analysis

8. Loan applications by purpose

9. Monthly trend of loan applications
 

## Analysis
1. Total number of Loan Applications: Determine the overall count of loan applications.

```
SELECT COUNT(id) AS total_loan_applications
FROM financial_loan
```
![image](https://github.com/rukkylatunde2001/Bank-Loan/assets/133374716/c2b2eb9e-1be3-484d-893b-5b3103a602b6)

2. Total Amount of loan given: Calculate the aggregate sum of loan amounts disbursed.
```
SELECT 
  SUM(loan_amount) AS total_loan_given,
  ROUND(SUM(loan_amount * (1 + int_rate)),2) AS total_loan_due,
  SUM(total_payment) AS total_loan_paid,
  ROUND((SUM(loan_amount * (1 + int_rate)) - SUM(total_payment)) / SUM(loan_amount * (1 + int_rate)) * 100, 2) AS percent_outstanding
FROM financial_loan;
```
![image](https://github.com/rukkylatunde2001/Bank-Loan/assets/133374716/5f16e4ac-cada-40f6-8e43-7d67907e5cbb)

3. Average Interest Rate: Calculate the average interest rate and  the average debt-to-income ratio across all loans.
```
SELECT ROUND(avg(int_rate), 4) *100 AS avg_interest, ROUND(avg(dti), 4) *100 AS avg_dti
FROM financial_loan
```
![image](https://github.com/rukkylatunde2001/Bank-Loan/assets/133374716/40d3a83d-55f6-43c3-8f3c-e25c1815fbfd)

4. Employment Length Impact on Loan Approval: Investigate the relationship between employment length and loan approval rates.
```
SELECT emp_length, COUNT(id) AS total_application 
FROM financial_loan
GROUP BY emp_length
ORDER BY COUNT(id) desc
```
![image](https://github.com/rukkylatunde2001/Bank-Loan/assets/133374716/75e7bb42-c507-4d17-b90c-5cd46c4ee985)

5. Loan Amount Distribution by Grade: Examine how loan amounts are distributed across different loan grades.
```
SELECT grade, AVG(loan_amount) AS avg_loan_amount
FROM financial_loan
GROUP BY grade
ORDER BY AVG(loan_amount) DESC
```
![image](https://github.com/rukkylatunde2001/Bank-Loan/assets/133374716/f7b4a4e8-9dd0-4f0d-8eed-62b567fd32ca)

6. Loan Performance Analysis: Evaluate the overall performance of loans in terms of loan status.
```
SELECT loan_status, COUNT(id) AS loan_count
FROM financial_loan
GROUP BY loan_status;

SELECT 
((SELECT COUNT(id) FROM financial_loan 
WHERE loan_status IN ('Fully Paid','Current')) * 100/ COUNT(id)) AS good_loan_percent,
((SELECT COUNT(id) AS bad_loan FROM financial_loan
WhERE loan_status = 'Charged Off') * 100 /  COUNT(id)) AS bad_loan_percent
FROM financial_loan
```
![image](https://github.com/rukkylatunde2001/Bank-Loan/assets/133374716/2ec6d7e7-2830-4771-860e-c3e61104e0fc) 
![image](https://github.com/rukkylatunde2001/Bank-Loan/assets/133374716/3773d1b6-d881-4b0c-a3d0-69dddc529a87)


7.Income Distribution Analysis
```
WITH IncomeRange AS (
  SELECT
    id,
    annual_income,
	loan_amount,
    CASE
      WHEN annual_income BETWEEN 4000 AND 10000 THEN '4,000 - 10,000'
      WHEN annual_income BETWEEN 10001 AND 25000 THEN '10,001 - 25,000'
      WHEN annual_income BETWEEN 25001 AND 50000 THEN '25,001 - 50,000'
      WHEN annual_income BETWEEN 50001 AND 100000 THEN '50,001 - 100,000'
      WHEN annual_income BETWEEN 100001 AND 500000 THEN '100,001 - 500,000'
      WHEN annual_income BETWEEN 500001 AND 1000000 THEN '500,001 - 1,000,000'
	  ELSE  '1,000,000+'
      
    END AS income_range
  FROM financial_loan
)

SELECT
  income_range,
  COUNT(id) AS applicant_count,
  AVG(annual_income) AS avg_income,
  AVG(loan_amount) AS avg_loan_amount
FROM IncomeRange
GROUP BY income_range
ORDER BY AVG(loan_amount) DESC;
```
![image](https://github.com/rukkylatunde2001/Bank-Loan/assets/133374716/e61d4cfc-1008-4e45-8db3-172b618740c5)

8. Loan applications by purpose
```SELECT purpose, COUNT(id) AS total_application, SUM(loan_amount) AS total_loan, SUM(total_payment) AS total_paid
FROM financial_loan
GROUP BY purpose
ORDER BY total_application DESC
```
![image](https://github.com/rukkylatunde2001/Bank-Loan/assets/133374716/ba1a897a-4b15-4f15-9a50-268bcbbc6b22)

9. Monthly trend of loan applications
```
SELECT MONTH(issue_date) AS month, COUNT(id) AS total_application,
SUM(loan_amount) AS total_loan, SUM(total_payment) AS total_loan_paid,
ROUND(AVG(annual_income), 2) AS avg_income
FROM financial_loan
GROUP BY MONTH(issue_date)
ORDER BY month
```
![image](https://github.com/rukkylatunde2001/Bank-Loan/assets/133374716/e8707738-3e42-4327-be43-67b2c0dc8538)

## Findings

1. The outstanding loan constitutes a small percentage (3.67%) of the total loan amount, indicating effective repayment

2. The average interest rate and debt-to-income ratio stand at 12.05% and 13.33%, respectively. This suggests a moderate financial burden on borrowers.
   
3. No clear correlation between loan application, employment length, and income range.
However, there is a notable concentration of loan applications for individuals with 10+ years of employment and an income range of $100,000 - $500,000.

4. Higher-grade loans show a higher average loan amount. This means that lower-risk grades tend to have higher loan amounts.

5. A substantial portion of loans (86%) is classified as 'good,' indicating satisfactory performance, while only 13% are classified as 'bad.'

6. Debt consolidation is a major purpose, accounting for a significant portion of total loan applications.

7. The monthly analysis reveals a continuous increase in total loan applications over the months.

## Recommendations

1. Assess if there are factors contributing to the outstanding loan percentage.

2. Consider adjusting loan grade criteria to optimize risk and return.

3. Explore ways to attract more applications for lower-risk grades.

 4. Look deeper into debt consolidation loans, given their prevalence.

 5. Regularly update the analysis to capture evolving trends and patterns.
