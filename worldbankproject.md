# World Bank Project


### Using SQL to Analyze Year-Over-Year Financial Metrics 

In this project, I utilized SQL to structure data in a way that allowed me to look at financial metrics relating to loans over the past year. The data provided by the World Bank regarding funds loaned by the IDA had some interesting highlights:
* India has repaid over $2 billion worth of loans to the IDA
* The top 3 countries by the amount owed to the IDA, Bangladesh, Pakistan, and India, share borders (and were all part of the British Raj)
* The amount that Tanzania owes to the IDA has increased by nearly $1.8 billion dollars in the past year

Below is the analysis I did to understand how the data was structured, what SQL to use to filter and group the data, and how I used CTEs to calculate year-over-year differences in different loan-related metrics. 

### Data Exploration:
Initially, I executed a basic SELECT * query to understand the structure of the data, where I found that each row represented various amounts associated with a loan (borrowed, repaid, owed, etc.) for a specific project on a given date. In order to ensure I was using the most recent data, I filtered the End_of_Period field to the latest date using the following SQL statement:

```SQL
SELECT
  End_of_Period,
  Credit_Number,
  Region,
  Country_Code,
  Country,
  Borrower,
  Credit_Status,
  Service_Charge_Rate,
  Currency_of_Commitment,
  Project_ID,
  Project_Name,
  Original_Principal_Amount_USD,
  Cancelled_Amount_USD,
  Undisbursed_Amount_USD,
  Disbursed_Amount_USD,
  Repaid_to_IDA_USD,
  Due_to_IDA_USD,
  Exchange_Adjustment_USD,
  Borrowers_Obligation_USD,
  Sold_3rd_Party_USD,
  Repaid_3rd_Party_USD,
  Due_3rd_Party_USD,
  Credits_Held_USD,
  First_Repayment_Date,
  Last_Repayment_Date,
  Agreement_Signing_Date,
  Board_Approval_Date,
  Effective_Date_Most_Recent,
  Closed_Date_Most_Recent,
  Last_Disbursement_Date
FROM
  `dcj-projects.World_Bank_Project.World_Bank_Project`
WHERE
  End_of_Period='2024-03-31'
```

Result: ![image](https://github.com/dsmilie/dsmilie.github.io/assets/153857210/76adcede-aecd-4375-b93c-bddc3f416784)

Now that I was working off of the most recent data, I wanted to use this same filter (WHERE End_of_Period='2024-03-31') to see the total of these metrics for each country across all of their loans. I did this by grouping the data by country and summing all of these financial metrics:
```SQL
SELECT
    Country AS Country_2024,
    SUM(Original_Principal_Amount_USD) AS Original_Principal_Total_2024,
    SUM(Cancelled_Amount_USD) AS Cancelled_Amount_Total_2024,
    SUM(Undisbursed_Amount_USD) AS Undisbursed_Amount_Total_2024,
    SUM(Disbursed_Amount_USD) AS Disbursed_Amount_Total_2024,
    SUM(Repaid_to_IDA_USD) AS Repaid_to_IDA_Total_2024,
    SUM(Due_to_IDA_USD) AS Due_to_IDA_Total_2024,
    SUM(Exchange_Adjustment_USD) AS Exchange_Adjustment_Total_2024,
    SUM(Borrowers_Obligation_USD) Borrowers_Obligation_Total_2024,
    SUM(Sold_3rd_Party_USD) Sold_3rd_Party_Total_2024,
    SUM(Repaid_3rd_Party_USD) AS Repaid_3rd_Party_Total_2024,
    SUM(Due_3rd_Party_USD) Due_3rd_Party_Total_2024,
    SUM(Credits_Held_USD) AS Credits_Held_Total_2024
  FROM
    `dcj-projects.World_Bank_Project.World_Bank_Project`
  WHERE
    End_of_Period='2024-03-31'
  GROUP BY
    Country
```
Result: ![image](https://github.com/dsmilie/dsmilie.github.io/assets/153857210/2095e59e-97cd-49fd-ab67-4807efd0fb7f)
### Year-Over-Year Analysis:
To observe how loan statuses have evolved over the past year, I adapted the query to compare data from 2023 and 2024 using CTEs (Common Table Expressions). This approach allowed me to calculate the differences in financial metrics from one year to the next, ordered by the increase in amounts due to the IDA:

```SQL
WITH
  Data_2023 AS (
  SELECT ---------- This filters the data to the snapshot on 2023-03-31
    Country AS Country_2023,
    SUM(Original_Principal_Amount_USD) AS Original_Principal_Total_2023,
    SUM(Cancelled_Amount_USD) AS Cancelled_Amount_Total_2023,
    SUM(Undisbursed_Amount_USD) AS Undisbursed_Amount_Total_2023,
    SUM(Disbursed_Amount_USD) AS Disbursed_Amount_Total_2023,
    SUM(Repaid_to_IDA_USD) AS Repaid_to_IDA_Total_2023,
    SUM(Due_to_IDA_USD) AS Due_to_IDA_Total_2023,
    SUM(Exchange_Adjustment_USD) AS Exchange_Adjustment_Total_2023,
    SUM(Borrowers_Obligation_USD) Borrowers_Obligation_Total_2023,
    SUM(Sold_3rd_Party_USD) Sold_3rd_Party_Total_2023,
    SUM(Repaid_3rd_Party_USD) AS Repaid_3rd_Party_Total_2023,
    SUM(Due_3rd_Party_USD) Due_3rd_Party_Total_2023,
    SUM(Credits_Held_USD) AS Credits_Held_Total_2023
  FROM
    `dcj-projects.World_Bank_Project.World_Bank_Project`
  WHERE
    End_of_Period='2023-03-31'
  GROUP BY
    Country),
  
  Data_2024 AS (
  SELECT  ---------- This filters the data to the snapshot on 2024-03-31
    Country AS Country_2024,
    SUM(Original_Principal_Amount_USD) AS Original_Principal_Total_2024,
    SUM(Cancelled_Amount_USD) AS Cancelled_Amount_Total_2024,
    SUM(Undisbursed_Amount_USD) AS Undisbursed_Amount_Total_2024,
    SUM(Disbursed_Amount_USD) AS Disbursed_Amount_Total_2024,
    SUM(Repaid_to_IDA_USD) AS Repaid_to_IDA_Total_2024,
    SUM(Due_to_IDA_USD) AS Due_to_IDA_Total_2024,
    SUM(Exchange_Adjustment_USD) AS Exchange_Adjustment_Total_2024,
    SUM(Borrowers_Obligation_USD) Borrowers_Obligation_Total_2024,
    SUM(Sold_3rd_Party_USD) Sold_3rd_Party_Total_2024,
    SUM(Repaid_3rd_Party_USD) AS Repaid_3rd_Party_Total_2024,
    SUM(Due_3rd_Party_USD) Due_3rd_Party_Total_2024,
    SUM(Credits_Held_USD) AS Credits_Held_Total_2024
  FROM
    `dcj-projects.World_Bank_Project.World_Bank_Project`
  WHERE
    End_of_Period='2024-03-31'
  GROUP BY
    Country)

SELECT 
    Data_2024.Country_2024,
    Data_2024.Original_Principal_Total_2024 - Data_2023.Original_Principal_Total_2023 AS Original_Principal_Difference, ---------- These calculate the difference between 2023 and 2024 data
    Data_2024.Cancelled_Amount_Total_2024 - Data_2023.Cancelled_Amount_Total_2023 AS Cancelled_Amount_Difference,
    Data_2024.Undisbursed_Amount_Total_2024 - Data_2023.Undisbursed_Amount_Total_2023 AS Undisbursed_Amount_Difference,
    Data_2024.Disbursed_Amount_Total_2024 - Data_2023.Disbursed_Amount_Total_2023 AS Disbursed_Amount_Difference,
    Data_2024.Repaid_to_IDA_Total_2024 - Data_2023.Repaid_to_IDA_Total_2023 AS Repaid_to_IDA_Difference,
    Data_2024.Due_to_IDA_Total_2024 - Data_2023.Due_to_IDA_Total_2023 AS Due_to_IDA_Difference,
    Data_2024.Exchange_Adjustment_Total_2024 - Data_2023.Exchange_Adjustment_Total_2023 AS Exchange_Adjustment_Difference,
    Data_2024.Borrowers_Obligation_Total_2024 - Data_2023.Borrowers_Obligation_Total_2023 AS Borrowers_Obligation_Difference,
    Data_2024.Sold_3rd_Party_Total_2024 - Data_2023.Sold_3rd_Party_Total_2023 AS Sold_3rd_Party_Difference,
    Data_2024.Repaid_3rd_Party_Total_2024 - Data_2023.Repaid_3rd_Party_Total_2023 AS Repaid_3rd_Party_Difference,
    Data_2024.Due_3rd_Party_Total_2024 - Data_2023.Due_3rd_Party_Total_2023 AS Due_3rd_Party_Difference,
    Data_2024.Credits_Held_Total_2024 - Data_2023.Credits_Held_Total_2023 AS Credits_Held_Difference,
    Data_2024.Original_Principal_Total_2024,---------- selecting all fields from 2024 data 
    Data_2024.Cancelled_Amount_Total_2024,
    Data_2024.Undisbursed_Amount_Total_2024,
    Data_2024.Disbursed_Amount_Total_2024,
    Data_2024.Repaid_to_IDA_Total_2024,
    Data_2024.Due_to_IDA_Total_2024,
    Data_2024.Exchange_Adjustment_Total_2024,
    Data_2024.Borrowers_Obligation_Total_2024,
    Data_2024.Sold_3rd_Party_Total_2024,
    Data_2024.Repaid_3rd_Party_Total_2024,
    Data_2024.Due_3rd_Party_Total_2024,
    Data_2024.Credits_Held_Total_2024,
    Data_2023.Original_Principal_Total_2023,---------- selecting all fields from 2023 data
    Data_2023.Cancelled_Amount_Total_2023,
    Data_2023.Undisbursed_Amount_Total_2023,
    Data_2023.Disbursed_Amount_Total_2023,
    Data_2023.Repaid_to_IDA_Total_2023,
    Data_2023.Due_to_IDA_Total_2023,
    Data_2023.Exchange_Adjustment_Total_2023,
    Data_2023.Borrowers_Obligation_Total_2023,
    Data_2023.Sold_3rd_Party_Total_2023,
    Data_2023.Repaid_3rd_Party_Total_2023,
    Data_2023.Due_3rd_Party_Total_2023,
    Data_2023.Credits_Held_Total_2023
FROM Data_2024 
JOIN Data_2023 ON Data_2024.Country_2024=Data_2023.Country_2023 ---------- Joining different snapshots in time on country 
ORDER BY Due_to_IDA_Difference DESC ---------- Ordering by the difference in money owed to the IDA from one year to the next
```
Result, with some columns and middle rows removed, and put into Excel with a color scale: ![image](https://github.com/dsmilie/dsmilie.github.io/assets/153857210/b73bbcb5-175b-46ce-bf8e-8bad10ba34ab)


### Results:
The SQL above was how I was able to highlight that  Tanzania, Nigeria, and Pakistan have been loaned the most money from the IDA in the past year, and how  India, Indonesia, and China have repaid their debt to the IDA over the past year. 

## Takeaway:
When using data where each row is a snapshot in time, in this case a snapshot of where all loans stand, a CTE made breaking up these loans by snapshot much easier to understand. Grouping the data appropriately then using a LAG function to calculate the different between one row and the next would have been another option, but seeing the two snapshots in time broken out into separate CTEs made it very clear to understand. 

This was fun project and good exercise in using SQL to filter, group, and calculate financial data. 

