# Customer Lifetime Value Dashboard

![alt text](https://github.com/PisutSukpool/BADS7105-CRM-analytics-and-intelligence/blob/main/Homework%2005/Main.png?raw=true)

tableau public link -> https://public.tableau.com/app/profile/pisut5343/viz/CLV_Dashboard/Dashboard1

## Step by Step

Summary each months -> No. of Customer , Total Spending , Average Order Value

![alt text](https://github.com/PisutSukpool/BADS7105-CRM-analytics-and-intelligence/blob/main/Homework%2005/Summary_each_month.png?raw=true)

--------------------
No. of Customer 

   Label : 
   
               {FIXED MONTH([Shop Date]) , YEAR([Shop Date]) : COUNTD([Cust Code])}
   
   Area Chart : 
   
               Row -> COUNTD([Cust Code])  , Column -> Day([Shop Date])

Total Spending

   Label : 
   
               Formula -> {FIXED  MONTH([Shop Date]) , YEAR([Shop Date]) : SUM([Spend]) }
   
   Area Chart : 
   
               Row -> SUM([Spend]) , Column -> Day([Shop Date])

Average Order Value

   Formula -> Total Spending / No. of Orders

   Label : 
         
               {FIXED MONTH([Shop Date]) , YEAR([Shop Date]) : SUM([Spend])/COUNTD([Basket Id])}
   
   Area Chart : 
   
               Row -> SUM([Spend])/COUNTD([Basket Id]) , Column -> Day([Shop Date])

--------------------
Average revenue per user (ARPU) per month

![alt text](https://github.com/PisutSukpool/BADS7105-CRM-analytics-and-intelligence/blob/main/Homework%2005/ARPU_per_Month.png?raw=true)
   
   Formula -> Total Revenue / No. of Customer
   
              SUM([Spend])/COUNTD([Cust Code])
   
--------------------
Churn Rate per month

![alt text](https://github.com/PisutSukpool/BADS7105-CRM-analytics-and-intelligence/blob/main/Homework%2005/ChurnRate_per_Month.png?raw=true)

   Formula ->  ((Clients at the beginning of a month - Clients at the end of a month) / Clients at the beginning of a month) x 100
   
              (WINDOW_SUM(COUNTD([Cust Code]),-1,-1)  -  WINDOW_SUM(COUNTD([Cust Code]),0,0))/WINDOW_SUM(COUNTD([Cust Code]),-1,-1)
   
--------------------
Average customer lifespan per month

![alt text](https://github.com/PisutSukpool/BADS7105-CRM-analytics-and-intelligence/blob/main/Homework%2005/ALT_per_Month.png?raw=true)

   Formula -> 1 / Churn Rate
   
              1 / [Churn Rate]
              
--------------------
Customer Lifetime Value per month

![alt text](https://github.com/PisutSukpool/BADS7105-CRM-analytics-and-intelligence/blob/main/Homework%2005/CLV_per_Month.png?raw=true)

   Define
   
   T = Average number of transactions per month
   
   AOV = Average order value
   
   AGM = Average gross margin = 10 % (Assume)
   
   ALT = Average customer lifespan in months
   
   Formula -> T x AOV x AGM x ALT
   
              [ARPU x AGM] x [Average Customer Lifespan]
              
              (SUM([Spend])/COUNTD([Cust Code]) x 0.1 ) x (1 / [Churn Rate])
              
--------------------
Customer Movement per month

![alt text](https://github.com/PisutSukpool/BADS7105-CRM-analytics-and-intelligence/blob/main/Homework%2005/CustMove_per_Month.png?raw=true)

| Customer Type | Description |
| ------------- | ------------- |
| New           | First Transaction or 3 months disappear, then come back again  |
| Repeat        | Previous Month and Current Month have transaction  |
| Reactivate    | They disappeared but come back again within 3 months  |
| Churn         | Previous Month has transaction but Current Month doesn't have transaction |

   Formula ->
    
             IF NOT  ISNULL(LOOKUP(ATTR( [Cust Code] ),-1)) AND NOT ISNULL(LOOKUP(ATTR( [Cust Code] ),0)) THEN 'Repeat'
             ELSEIF  NOT ISNULL(LOOKUP(ATTR( [Cust Code] ),-1)) AND ISNULL(LOOKUP(ATTR( [Cust Code] ),0)) THEN 'Churn'
             ELSEIF  ISNULL(LOOKUP(ATTR( [Cust Code] ),-1)) AND ISNULL(LOOKUP(ATTR( [Cust Code] ),0)) THEN ''
             ELSEIF  ISNULL(LOOKUP(ATTR( [Cust Code] ),-3)) AND ISNULL(LOOKUP(ATTR( [Cust Code] ),-2)) 
                     AND isnull(LOOKUP(ATTR( [Cust Code] ),-1)) AND NOT ISNULL(LOOKUP(ATTR( [Cust Code] ),0)) THEN 'New' 
             ELSE 'Reactivate'
             END

--------------------
Spending MTD vs Last Month

![alt text](https://github.com/PisutSukpool/BADS7105-CRM-analytics-and-intelligence/blob/main/Homework%2005/MTDvsLM.png?raw=true)

   Spending Current Month : Formula ->
      
                                       WINDOW_SUM(sum([Spend]),0,0)
                                       

   Spending Last Month : Formula ->
      
                                       WINDOW_SUM(sum([Spend]),-1,-1)        
                                       
--------------------
Customer Segmentation by RFM

![alt text](https://github.com/PisutSukpool/BADS7105-CRM-analytics-and-intelligence/blob/main/Homework%2005/RFM.png?raw=true)

Segment Table

![alt text](https://github.com/PisutSukpool/BADS7105-CRM-analytics-and-intelligence/blob/main/Homework%2005/RFM_ref.png?raw=true)

Segmentation Reference : https://blog.rsquaredacademy.com/customer-segmentation-using-rfm-analysis/

   Recency (R) : Formula -> Change latest date that a customer has transaction to R Score (1-5)
   
                  IF ROUND(RANK_PERCENTILE([Max Shope Date])/0.2,0) = 0 THEN 1
                  ELSE ROUND(RANK_PERCENTILE([Max Shope Date])/0.2,0) END
                  
   Frequency (F) : Formula -> Change frequency that a customer come to shop and has transaction to F Score (1-5)
   
                  IF ROUND(RANK_PERCENTILE(COUNT([Shop Date]))/0.2,0) = 0 THEN 1
                  ELSE ROUND(RANK_PERCENTILE(COUNT([Shop Date]))/0.2,0) END
                  
   Monetary (M) : Formula -> Change average order value (AOV) to M Score (1-5)
   
                  IF ROUND(RANK_PERCENTILE([Average order value])/0.2,0) = 0 THEN 1
                  ELSE ROUND(RANK_PERCENTILE([Average order value])/0.2,0) END   
                  
--------------------
