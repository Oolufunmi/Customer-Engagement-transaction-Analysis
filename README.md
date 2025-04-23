# Customer-Engagement-Transaction-Analysis


## Table Of Content
- [Objective](#objective)
- [Tools](#tools)
- [Data Cleaning](#data-cleaning)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [My Data Analysis Approach](#my-data-analysis-approach)
- [Result And Findings ](#result-and-findings)
- [Key Insights](#key-insights)
- [Recommendations](#recommendations)
- [References](#references)



### Objective
 The objective is to create a dashboard highlighting key business metrics and user behavior patterns within the platform.


 ###
 I  leverage SQL to efficiently query and structure the User and Transaction datasets I used Python to visualize the data. i worked on 2different tables with about 160000 data entries.

### Data Source: I got ths data from a Financial services company that does Currency Exchange.

### Tools 
 Microsoft Excel, MySQL, Python and Powerpoint.
 
- Microsoft Excel: To properly view all the data entries and for data cleaning.
     - [Download here](https://microsoft.com)
- MySQL: To write Queries that solve specfic Business problem.
- Python: To visualize qriiten Sql query ( I connected MySql with Python).
- Powerpoint : To Present Insight from Analytics.

### Data Cleaning
In the intial dat presentation phase , i perform the following task
- Data loading and Inspection
- Handling missing values
- Data Cleaning and Formatting
- 

# Exploratory Data Analysis
Data Files Provided:
- **Users Table**: Contains information about users, including registration details, KYC
status, deactivation status, and occupation.
- **Transactions Table**: Contains transaction details, including amounts, currency corridors,
and transaction dates.
See Link to Data Below
User_Table
Transaction Table
Analysis and Visualization Tasks:
1. Users Analysis
Provide insights into the user base by analyzing the following metrics:Track and visualize
user growth Daily,WOW,MOM.
- **Total Number of Users**,
- **Verified and Non-Verified Users**
- **Average Volume per User**
- **Max and Min Transaction Volume**
- **Age Distribution**:
- **Gender Distribution**
- **User Location**:
- **User Profession**:
2. KYC Status:
Provide insights into the status of the KYC process:
- **Total KYC Passed**: Number of users whose KYC process was successfully completed.
- **KYC Not Started**: Number of users who have not started the KYC process.
- **KYC Failed**: Number of users who failed the KYC process.
- **KYC Pending**: Number of users with pending KYC status.
- **KYC In Review**: Number of users with KYC status in Manual.

KYC Status: Breakdown of users by their KYC status, using the following keys:
1 = Not Started,2 = Pending, 3 = Passed, 4 = In Review, 6 = Failed
3. Transaction Analysis:
Analyze transaction data with the following KPIs:
- **Total Number of Transactions**: Display the total number of transactions .
- **Total Amount of Transactions ($)**: Calculate the total transaction value IN CAD(Use
BaseAmount).
- **Transaction Volume by SendCurrency Corridor**: Breakdown of the volume by each
Sendcurrency corridor (e.g., CAD,NGN),Sender Location (CAD, AUS, NG, etc.)insights by
user location to see how each country is performing..
- **Transaction Volume by Send Currency and Receive currency Corridor**: Breakdown of
the volume by corridor (e.g., CAD-NGN,NGN-GBP).
- **Growth per Corridor in Volume**: Analyze growth in volume per corridor(Monthly only).
- **Transactions Daily, Month-on-Month (MoM) and Week-on-Week (WoW)**: Visualize
transaction growth by volume and number for MoM and WoW comparisons(
NOTE: THE BASE AMOUNT COLUMN IS THE VALUE OF THE SEND AMOUNT
CONVERTED TO CAD, SO THIS CAN BE USED AS FOR THE TOTAL TRANSACTION
VOLUME IN CAD AND YOU CAN REPRESENT ALL THE VALUES IN CAD EXCEPT
WHEN BREAKING DOWN BY CORRIDORS
4. Retention:**
Track user and transaction retention with the following metrics:
- **Active Users**: Monthly and weekly active users(Transacting Users).
- **Users with More Than 3, 5, 10, and 20 Transactions**: Breakdown of users based on
their transaction volume (e.g., more than 3, 5, 10, and 20 transactions).
- **Date (Day, Week, Month)**: Provide the ability to view insights by different date ranges.
5. Funnel Analysis:
Perform a funnel analysis on user activity across different stages of the user journey, and
visualize the conversion rates and drop-off points.
● Funnel Stages:
○ Acquisition → Complete Profile → KYC Verified → Transaction
● Visual Representation of the Funnel: Create a funnel chart or flow diagram that
represents the stages of the user journey.
● Conversion Rate & Drop-off Points: Analyze and calculate the conversion rates at
each stage of the funnel and identify key drop-off points where users are
disengaging.
● Key Drop-off Points: Discuss the drop-off points in detail, considering factors that
may contribute to the user disengagement.
● Strategies to Address Drop-offs: Propose actionable strategies to address these
drop-offs and improve the overall funnel conversion rate.

Additional Requirements:
- **Dashboard Design**: Ensure the dashboard is intuitive, user-friendly, and well-organized.
Use appropriate colors and labels to improve readability.
- **Filters**: Use filters if neccessary to allow users to drill down into specific segments like
date range, user tier, or currency.
- **Insight Generation**: Based on your analysis, provide insights into key trends and
patterns.
- **Recommendations**: Based on your findings, offer actionable recommendations to
improve user engagement, transaction volumes, and overall performance.



### My Data Analysis Approach📌

Include the code and features work with
```Mysql
Users Analysis
1.	Total Number of Users
SELECT COUNT(*) AS TotalUsers FROM user_table;

2.Verified and Non-Verified Users
SELECT 
    IsKYCVerified, 
    COUNT(*) AS UserCount 
FROM user_table
GROUP BY IsKYCVerified;

3.	Average Transaction Volume per User
SELECT 
    u.Id AS UserId, 
    u.UserName,
    COALESCE(AVG(t.BaseAmount), 0) AS AvgTransactionVolume
FROM user_table u
LEFT JOIN transaction_list t ON u.Id = t.UserId
GROUP BY u.Id, u.UserName;

4.	Max and Min Transaction Volume
SELECT 
    MAX(BaseAmount) AS MaxTransactionVolume, 
    MIN(BaseAmount) AS MinTransactionVolume 
FROM transaction_list;

5. Age Distribution
SELECT 
    CASE 
        WHEN TIMESTAMPDIFF(YEAR, DateOfBirth, CURDATE()) < 18 THEN 'Under 18'
        WHEN TIMESTAMPDIFF(YEAR, DateOfBirth, CURDATE()) BETWEEN 18 AND 25 THEN '18-25'
        WHEN TIMESTAMPDIFF(YEAR, DateOfBirth, CURDATE()) BETWEEN 26 AND 35 THEN '26-35'
        WHEN TIMESTAMPDIFF(YEAR, DateOfBirth, CURDATE()) BETWEEN 36 AND 50 THEN '36-50'
        ELSE '50+'
    END AS AgeGroup,
    COUNT(*) AS UserCount
FROM user_table
GROUP BY AgeGroup;
6. Gender Distribution
SELECT 
    Gender, 
    COUNT(*) AS UserCount 
FROM user_table
GROUP BY Gender;
7. User Location (Country Distribution)
SELECT 
    ResidenceCountry, 
    COUNT(*) AS UserCount 
FROM user_table
GROUP BY ResidenceCountry
ORDER BY UserCount DESC;
8. User Profession Distribution
SELECT 
    Occupation, 
    COUNT(*) AS UserCount 
FROM user_table
GROUP BY Occupation
ORDER BY UserCount DESC;


9. Daily, Week-over-Week (WOW), and Month-over-Month (MOM) User Growth
### Daily User Growth
SELECT 
    DATE(DateCreated) AS RegistrationDate, 
    COUNT(*) AS NewUsers 
FROM user_table
GROUP BY RegistrationDate
ORDER BY RegistrationDate;
Week-over-Week (WOW) Growth
SELECT 
    YEARWEEK(DateCreated) AS YearWeek, 
    COUNT(*) AS NewUsers 
FROM user_table
GROUP BY YearWeek
ORDER BY YearWeek;
Month-over-Month (MOM) Growth
SELECT 
    DATE_FORMAT(DateCreated, '%Y-%m') AS YearMonth, 
    COUNT(*) AS NewUsers 
FROM user_table
GROUP BY YearMonth
ORDER BY YearMonth;


 
2.	KYC Status

### Total KYC Passed
SELECT COUNT(*) AS TotalKYCPassed 
FROM user_table 
WHERE IsKYCVerified = 3;
### KYC Not Started
SELECT COUNT(*) AS KYCNotStarted 
FROM user_table 
WHERE IsKYCVerified = 1;
### KYC Failed
SELECT COUNT(*) AS KYCFailed 
FROM user_table 
WHERE IsKYCVerified = 6;
### KYC Pending
SELECT COUNT(*) AS KYCPending 
FROM user_table 
WHERE IsKYCVerified = 2;
	KYC In Review
SELECT COUNT(*) AS KYCInReview 
FROM user_table 
WHERE IsKYCVerified = 4;
### Breakdown of Users by KYC Status
SELECT 
    CASE 
        WHEN IsKYCVerified = 1 THEN 'Not Started'
        WHEN IsKYCVerified = 2 THEN 'Pending'
        WHEN IsKYCVerified = 3 THEN 'Passed'
        WHEN IsKYCVerified = 4 THEN 'In Review'
        WHEN IsKYCVerified = 6 THEN 'Failed'
        ELSE 'Unknown'
    END AS KYCStatus,
    COUNT(*) AS UserCount
FROM user_table
GROUP BY KYCStatus;
 
3.	Transaction Analysis:
### Total Number of Transactions
SELECT COUNT(*) AS TotalTransactions FROM transaction_list;
### Total Amount of Transactions (in CAD)
SELECT SUM(BaseAmount) AS TotalTransactionAmountCAD FROM transaction_list;
### Transaction Volume by Send Currency and Sender Location
SELECT 
    t.SendCurrencyId AS SendCurrency, 
    u.ResidenceCountry AS SenderLocation, 
    COUNT(*) AS TransactionCount, 
    SUM(t.BaseAmount) AS TotalTransactionVolumeCAD 
FROM transaction_list t
JOIN user_table u ON t.UserId = u.Id
GROUP BY t.SendCurrencyId, u.ResidenceCountry
ORDER BY TotalTransactionVolumeCAD DESC;
### Transaction Volume by Send and Receive Currency Corridor
SELECT 
    CONCAT(t.SendCurrencyId, '-', t.ReceiveCurrencyId) AS CurrencyCorridor, 
    COUNT(*) AS TransactionCount, 
    SUM(t.BaseAmount) AS TotalTransactionVolumeCAD 
FROM transaction_list t
GROUP BY CurrencyCorridor
ORDER BY TotalTransactionVolumeCAD DESC;



### Growth per Corridor in Volume (Monthly)
SELECT 
    DATE_FORMAT(t.DateCreated, '%Y-%m') AS YearMonth, 
    CONCAT(t.SendCurrencyId, '-', t.ReceiveCurrencyId) AS CurrencyCorridor, 
    SUM(t.BaseAmount) AS TotalTransactionVolumeCAD,
    COUNT(*) AS TransactionCount
FROM transaction_list t
GROUP BY YearMonth, CurrencyCorridor
ORDER BY YearMonth DESC, TotalTransactionVolumeCAD DESC;
	Transaction Growth Analysis (Daily, WoW, MoM)
### Daily Transaction Growth

SELECT 
    DATE(t.DateCreated) AS TransactionDate, 
    COUNT(*) AS TransactionCount, 
    SUM(t.BaseAmount) AS TotalTransactionVolumeCAD 
FROM transaction_list t
GROUP BY TransactionDate
ORDER BY TransactionDate DESC;
Week-over-Week (WoW) Growth
SELECT 
    YEARWEEK(t.DateCreated) AS YearWeek, 
    COUNT(*) AS TransactionCount, 
    SUM(t.BaseAmount) AS TotalTransactionVolumeCAD 
FROM transaction_list t
GROUP BY YearWeek
ORDER BY YearWeek DESC;
 
### Month-over-Month (MoM) Growth
SELECT 
    DATE_FORMAT(t.DateCreated, '%Y-%m') AS YearMonth, 
    COUNT(*) AS TransactionCount, 
    SUM(t.BaseAmount) AS TotalTransactionVolumeCAD 
FROM transaction_list t
GROUP BY YearMonth
ORDER BY YearMonth DESC;
4.	Retention:
	Active Users (Weekly and Monthly)
Weekly Active Users (WAU)
SELECT 
    YEARWEEK(t.DateCreated) AS YearWeek, 
    COUNT(DISTINCT t.UserId) AS WeeklyActiveUsers 
FROM transaction_list t
GROUP BY YearWeek
ORDER BY YearWeek DESC;
Monthly Active Users (MAU)
SELECT 
    DATE_FORMAT(t.DateCreated, '%Y-%m') AS YearMonth, 
    COUNT(DISTINCT t.UserId) AS MonthlyActiveUsers 
FROM transaction_list t
GROUP BY YearMonth
ORDER BY YearMonth DESC;




### Users with More Than 3, 5, 10, and 20 Transactions
SELECT 
    CASE 
        WHEN TransactionCount > 20 THEN 'More than 20'
        WHEN TransactionCount > 10 THEN 'More than 10'
        WHEN TransactionCount > 5 THEN 'More than 5'
        WHEN TransactionCount > 3 THEN 'More than 3'
    END AS TransactionCategory,
    COUNT(*) AS UserCount
FROM (
    SELECT UserId, COUNT(*) AS TransactionCount
    FROM transaction_list
    GROUP BY UserId
) AS UserTransactionCounts
GROUP BY TransactionCategory
ORDER BY UserCount DESC;
Retention Insights by Different Date Ranges
Daily Retention (Users Transacting Per Day)
SELECT 
    DATE(t.DateCreated) AS TransactionDate, 
    COUNT(DISTINCT t.UserId) AS DailyActiveUsers 
FROM transaction_list t
GROUP BY TransactionDate
ORDER BY TransactionDate DESC;



### Weekly Retention (Users Transacting Per Week)
SELECT 
    YEARWEEK(t.DateCreated) AS YearWeek, 
    COUNT(DISTINCT t.UserId) AS WeeklyActiveUsers 
FROM transaction_list t
GROUP BY YearWeek
ORDER BY YearWeek DESC;
Monthly Retention (Users Transacting Per Month)
	SELECT 
    DATE_FORMAT(t.DateCreated, '%Y-%m') AS YearMonth, 
    COUNT(DISTINCT t.UserId) AS MonthlyActiveUsers 
FROM transaction_list t
GROUP BY YearMonth
ORDER BY YearMonth DESC;
 
5.	Funnel Analysis
Funnel Stages & Drop-Off Analysis
Query: Count Users at Each Stage
WITH Funnel AS (
    SELECT 
        (SELECT COUNT(*) FROM user_table) AS AcquiredUsers,
        (SELECT COUNT(*) FROM user_table WHERE DateOfBirth IS NOT NULL AND Gender IS NOT NULL) AS ProfileCompletedUsers,
        (SELECT COUNT(*) FROM user_table WHERE IsKYCVerified = 3) AS KYCVerifiedUsers,
        (SELECT COUNT(DISTINCT UserId) FROM transaction_list) AS TransactingUsers
)
SELECT *, 
    ROUND((ProfileCompletedUsers / AcquiredUsers) * 100, 2) AS ProfileCompletionRate,
    ROUND((KYCVerifiedUsers / ProfileCompletedUsers) * 100, 2) AS KYCCompletionRate,
    ROUND((TransactingUsers / KYCVerifiedUsers) * 100, 2) AS TransactionConversionRate
FROM Funnel;
 
CONVERSION RATE & DROP-OFF POINTS CALCULATION
    Profile Completion Rate = (Users who completed profile / Total acquired users) * 100
    KYC Completion Rate = (Users who completed KYC / Users who completed profile) * 100
    Transaction Conversion Rate = (Users who made transactions / KYC verified users) * 100
These conversion rates highlight where the biggest drop-offs occur in the user journey.
```


### Result And Findings 

The analysis result are summarized as follows:
- The success of a transaction isn’t contingent on having a UserID, I have included all
transaction rows in the analysis—even those with missing UserIDs—to ensure we
capture the full volume of successful transactions.
The objective was to create a dashboard highlighting key business metrics and user behavior patterns within the platform.
### Where is the largest drop-off in the funnel?
  The biggest drop occurs between Acquisition (21,906) and Complete Profile
(12,901), indicating many new sign-ups do not finish their profiles.
- Simplify Profile Completion: Reduce required steps and provide clear instructions
to help users quickly finish their profiles.
- Highlight KYC Benefits: Emphasize the advantages (e.g., faster transactions, higher
limits) to motivate users to verify their accounts.

### What strategies can address this drop-off?

Simplify Profile Completion – Make required steps fewer and more intuitive.

Provide Clear Benefits – Highlight why completing a profile is valuable (e.g.,
faster transactions).

Automated Reminders – Send friendly follow-ups via email or in-app prompts
to encourage users to complete their profiles.
Automated Reminders and Incentives: Send follow-ups or small rewards to
nudge users who haven’t completed their profiles or KYC.

Promote First Transactions: Offer clear guides or promos to encourage
verified users to transact, keeping them active in the funnel.

### Key Insights

💡Tracked and visualize user growth and demographic breakdowns.

💡Analyzed the efficiency of the KYC processes.

💡Insight on transaction flow across different currency corridors and user locations.

💡Measured user and transaction retention.

💡Identified key drop-off points in the user journey and suggesting actionable improvements.

### Recommendations
📌Simplify Profile Completion: Reduce required steps and provide clear instructions
to help users quickly finish their profiles.

📌 Highlight KYC Benefits: Emphasize the advantages (e.g., faster transactions, higher
limits) to motivate users to verify their accounts.

### References
1. SQl For Business Anlayisis by Werty
2. YOutube Tutorials By Alex the Data Analyst
