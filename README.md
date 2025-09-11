# Online-Business-Insights

Analyzed online sales data using SQL to identify key performance indicators and customer purchasing trends. Developed interactive dashboards in Tableau to visualize the data, providing stakeholders with real-time insights to optimize marketing strategies and inventory management.

The source of my dataset comes from Kaggle. E-Commerce Data , it's a public dataset. "The UCI Machine Learning Repository has made this dataset containing actual transactions from 2010 and 2011." Also, ""This is a transnational data set which contains all the transactions occurring between 01/12/2010 and 09/12/2011 for a UK-based and registered non-store online retail.The company mainly sells unique all-occasion gifts. Many customers of the company are wholesalers."" 
https://www.kaggle.com/datasets/carrie1/ecommerce-data


<img width="1285" height="1026" alt="image" src="https://github.com/user-attachments/assets/cc8fa4f6-f645-4b7d-a16c-d93ae327fc2e" />
(https://public.tableau.com/app/profile/kathleneanderson/viz/OnlineSalesKPIDashboard_17549599787590/KPIDASH)

KPI
The map highlights a significant revenue disparity across countries. The United Kingdom stands out with the highest revenue, exceeding $49 million, while other major countries like the United States, Canada, Brazil, and regions such as South Africa and Saudi Arabia generated a much smaller revenue, ranging from $5,000 to $30,000.

To address the low revenue from these countries, a targeted marketing strategy is recommended. This could involve focused advertising campaigns and offering first-time customer discounts to help boost sales and increase market penetration.

An analysis of the company's revenue history shows a consistent upward trend across monthly, weekly, and quarterly data. The only noticeable decline occurs in December, which is likely a post-holiday shopping effect.

 To mitigate the December revenue drop, the company could introduce a post-Christmas sales event. Aside from this seasonal dip, the online store is on a positive growth path.

-- -- Customer Insight Dashboard-- --

-- customer segmentation
```	
SELECT customerno, DATEDIFF('2020-01-01', MAX(date)) AS recency
FROM order_info
GROUP BY customerno;

SELECT customerno,
       COUNT(*) AS frequency
FROM order_info
GROUP BY customerno;

select customerno, floor(sum(total)) as monetary_value
from order_info
Group by customerno;

SELECT r.customerno,
       r.recency,
       f.frequency,
       m.monetary_value
FROM (SELECT customerno,
             DATEDIFF('2020-01-01', MAX(date)) AS recency
      FROM order_info
      GROUP BY customerno) AS r
JOIN (SELECT customerno,
             COUNT(*) AS frequency
      FROM order_info
      GROUP BY customerno) AS f
ON r.customerno = f.customerno
JOIN (SELECT customerno,
             SUM(total) AS monetary_value
      FROM order_info
      GROUP BY customerno) AS m
ON r.customerno = m.customerno;

WITH rfm AS (
    SELECT customerno,
           recency,
           frequency,
           monetary_value,
           NTILE(5) OVER(ORDER BY recency) AS recency_score,
           NTILE(5) OVER(ORDER BY frequency DESC) AS frequency_score,
           NTILE(5) OVER(ORDER BY monetary_value DESC) AS monetary_score
    FROM (
        SELECT r.customerno,
               r.recency,
               f.frequency,
               m.monetary_value
        FROM (SELECT customerno,
                     DATEDIFF(CURRENT_DATE, MAX(date)) AS recency
              FROM order_info
              GROUP BY customerno) AS r
        JOIN (SELECT customerno,
                     COUNT(*) AS frequency
              FROM order_info
              GROUP BY customerno) AS f
        ON r.customerno = f.customerno
        JOIN (SELECT customerno,
                     SUM(total) AS monetary_value
              FROM order_info
              GROUP BY customerno) AS m
        ON r.customerno = m.customerno
    ) AS rfm_data
)
SELECT customerno,
       recency,
       frequency,
       monetary_value,
       recency_score + frequency_score + monetary_score AS rfm_score
FROM rfm;
```

-- cutomer retention
```
 WITH DateRange AS 
	(Select customerno,
 min(date) dateMin,
 max(date) dateMax
 From salestransaction
 group by customerno)
 
select customerno, datediff(dateMax, dateMin) as HowFrequent
FROM   DateRange;
```

<img width="1280" height="1008" alt="image" src="https://github.com/user-attachments/assets/270fadd4-f0cb-4dc6-9fd3-29a85506ed64" />

(https://public.tableau.com/app/profile/kathleneanderson/viz/ProductAnalysisDashboard_17549601810070/PRODDASH)

TOP SALES
Our analysis shows that popcorn holders and the World War 2 gliders were the leading products, with sales surpassing $500,000. Overall, the top 15 products each brought in more than $200,000.

Just like Amazon's yearly sales events, implementing a major sale could incentivize customers who are considering these products to buy in bundles, increasing overall sales volume.

TOP PRODUCT
In terms of units sold, the same products led the way. The World War 2 trinkets sold over 54,000 units, while popcorn holders sold more than 56,000 units.

RETURN
The most returned item was the travel card wallet, with returns coming from various transactions and customers. This suggests a widespread issue rather than an isolated incident.

The high return rate for the travel card wallets indicates a potential quality control or design issue. We recommend temporarily pulling this product from shelves and conducting a customer survey to determine the root cause. This will inform a decision on whether to find a new supplier or address the issue internally.

-- product
```
select productname , count(productname) as TopPurchased
from salestransaction
group by productname
ORDER BY TopPurchased DESC;

select productname , sum(amount) as TopRevenue
from salestransaction
group by productname
ORDER BY TopRevenue DESC;
```

-- return
```
select productname, count(productname) ReturnedAmount
from salestransaction
where quantity <= 0
group by productname
order by ReturnedAmount desc;
```
<img width="1285" height="1025" alt="image" src="https://github.com/user-attachments/assets/71aa44ad-1004-474f-ab0d-7359e4850879" /> 

(https://public.tableau.com/app/profile/kathleneanderson/viz/RecencyFrequencyMonetaryDashboard/CUSDASH)

CUSTOMER
From December 2018 to December 2019, the company served over 4,739 customers. The largest segment of our customer base, representing over 32% of customers, falls into the Occasional Buyers group.

Occasional Buyers have a purchase recency of approximately 154 days (or about 5 months) and make an average of 2 purchases. Their average spending is $3,000, and we saw their spending increase by 3.5x from Q4 2018 to Q4 2019.


 The Loyal Customer group consists of 644 individuals, representing 13.6% of our customer base. These are our most valuable customers, with a purchase recency of just 46.7 days and an average of 9.27 purchases per year. They spend over $19,100 annually, and their numbers have grown steadily each quarter.
To further nurture this key segment, we can implement new loyalty programs. Offering special discounts and codes will help us retain these customers and increase their already-significant spending over time.



##KPI------------------------------------------

```
Select floor(sum(amount)) as TotalRevenueKPI
from salestransaction;

select COUNT(distinct customerno) as TotalCustomers
FROM salestransaction;

select productname, sum(quantity) as QuatitySold
from salestransaction
group by productname
order by QuatitySold desc;
```

##GEO--------------------------------------------
```
select country, floor(sum(amount)) as TotalCountryRev
from salestransaction
group by country;

select country, count(distinct transactionno) as PurchasesPerCon
from salestransaction
group by country
order by PurchasesPerCon desc;
```

-- Sales Trends 
```
Select SUM(Total) AS Dec18
FROM ecom.order_info
where date between '2018-12-01' AND '2018-12-31';
-- 
Select SUM(Total) AS Jan19
FROM ecom.order_info
where date between '2019-01-01' AND '2019-01-31';

Select SUM(Total) AS Feb19
FROM ecom.order_info
where date between '2019-02-01' AND '2019-02-28';

Select SUM(Total) AS Mar19
FROM ecom.order_info
where date between '2019-03-01' AND '2019-03-31';

Select SUM(Total) AS Apr19
FROM ecom.order_info
where date between '2019-04-01' AND '2019-04-30';

Select SUM(Total) AS May19
FROM ecom.order_info
where date between '2019-05-01' AND '2019-05-31';

Select SUM(Total) AS Jun19
FROM ecom.order_info
where date between '2019-06-01' AND '2019-06-30';

Select SUM(Total) AS Jul19
FROM ecom.order_info
where date between '2019-07-01' AND '2019-07-31';

Select SUM(Total) AS Aug19
FROM ecom.order_info
where date between '2019-08-01' AND '2019-08-31';

Select SUM(Total) AS Sep19
FROM ecom.order_info
where date between '2019-09-01' AND '2019-09-30';

Select SUM(Total) AS Oct19
FROM ecom.order_info
where date between '2019-10-01' AND '2019-10-30';

Select SUM(Total) AS Nov19
FROM ecom.order_info
where date between '2019-11-01' AND '2019-11-30';

Select SUM(Total) AS Dec19
FROM ecom.order_info
where date between '2019-12-01' AND '2019-12-31';
```

-- Quarter Decemeber

```
Select SUM(Total) AS Q0
FROM ecom.order_info
where date between '2018-12-01' AND '2018-12-31';
```

-- Quarter Jan-Mar

```
Select SUM(Total) AS Q1
FROM ecom.order_info
where date between '2019-01-01' AND '2019-03-31';
```

-- Quarter Apr-Jun

```
Select SUM(Total) AS Q2
FROM ecom.order_info
where date between '2019-04-01' AND '2019-06-30';
```

-- Quarter Jul-Sep

```
Select SUM(Total) AS Q3
FROM ecom.order_info
where date between '2019-07-01' AND '2019-09-30';
```

-- Quarter Oct-Dec

```
Select SUM(Total) AS Q4
FROM ecom.order_info
where date between '2019-10-01' AND '2019-12-31';
```

-- Sales By

```
select productname , count(productname) as TopSelling
from salestransaction
group by productname
ORDER BY amount DESC;
```
































