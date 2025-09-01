# Online-Business-Insights

Analyzed online sales data using SQL to identify key performance indicators and customer purchasing trends. Developed interactive dashboards in Tableau to visualize the data, providing stakeholders with real-time insights to optimize marketing strategies and inventory management.

The source of my dataset comes from Kaggle. E-Commerce Data , it's a public dataset. "The UCI Machine Learning Repository has made this dataset containing actual transactions from 2010 and 2011." Also, ""This is a transnational data set which contains all the transactions occurring between 01/12/2010 and 09/12/2011 for a UK-based and registered non-store online retail.The company mainly sells unique all-occasion gifts. Many customers of the company are wholesalers."" 
https://www.kaggle.com/datasets/carrie1/ecommerce-data

Comments: Use comments within your code to explain what each part of the query does (e.g., explaining a complex JOIN, a CTE, or a window function). This shows you can write clean, understandable code.
Results and Insights: What did the queries reveal? Include screenshots of the output or link to the resulting data visualizations.



https://public.tableau.com/app/profile/kathleneanderson/viz/RecencyFrequencyMonetaryDashboard/CUSDASH

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


https://public.tableau.com/app/profile/kathleneanderson/viz/ProductAnalysisDashboard_17549601810070/PRODDASH


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

https://public.tableau.com/app/profile/kathleneanderson/viz/OnlineSalesKPIDashboard_17549599787590/KPIDASH

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
































