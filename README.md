# Sales-Analysis-Projects


### Project Overview
This Data analysis project aims to provide insight into the sales performance of a retail store  and suggest improvements. 
By analysing Various aspect of sales data set, we seek to identify trend, make data driven recommendations and gain a deeper understanding of the store performance.
![Sales_1 Analysis Picture_022550](https://github.com/user-attachments/assets/e21e085f-7012-46c8-b317-9086a5c9d533)


### Data Sources
The Primary Data used for this Analysis is the "sales_1.csv" dataset, which contains details of each aspects of the analysis. 

### Tools
- SQL SERVER - Used for Data cleaning, Querying, manipulation and Analysis.
- POWER BI - Creating Reports and Visualizations.

### Data Cleaning / Preparation
In the initial Data cleaning phase we performed the following tasks;
- Data Loading and Inspection.
- Handling Missing Values and Duplicates.
- Data cleaning and Formating

  Below are the codes used for Data Cleaning and Prepartion;
  ```
                    ----(DATA-CLEANING)----
  Replaced NULL cells in the product column with the MAX Product name
UPDATE sales_1
```
```SET productname = 'WidgetA'
WHERE productname IS NULL;
`````
```
------SET ALL null product ID to "100"; so Null products ID is 100
UPDATE sales_1
SET productID = 100
WHERE productID IS NULL;

-----FILLED category column with the Max product category
UPDATE sales_1 
SET category = (SELECT  MAX(category) from sales_1)
WHERE category IS NULL

------replaced blank cells with "0"
UPDATE sales_1 
SET price = 0
WHERE price IS NULL;


UPDATE sales_1
SET Quantity = (SELECT 
AVG(Quantity) 
FROM sales_1 
WHERE category = 'Furniture' 
GROUP BY category)
WHERE Quantity IS NULL;

-----DELETE ROWS WHERE date is Null
DELETE FROM sales_1
 where Saledate IS NULL;


 ----Updated CusromerID with most occuring CustomerID
 UPDATE sales_1
 SET CustomerID = (SELECT  MAX(CustomerID) AS Count_ID FROM sales_1 )
 FROM sales_1
 WHERE CUSTOMERID IS NULL;


------Updated the region Column with the most occuring Region
UPDATE sales_1
 SET Region = (SELECT  MAX(Region) FROM sales_1 )
 FROM sales_1
 WHERE Region IS NULL;


 -----Replace blank cells with avg Discount
 UPDATE sales_1
 SET Discount = (SELECT  AVG( Discount) FROM sales_1 )
 FROM sales_1
 WHERE  Discount IS NULL;


 ------price column updated with avg price
UPDATE sales_1
 SET profit = (SELECT  AVG( profit) FROM sales_1 )
 FROM sales_1
 WHERE  profit IS NULL;


------Round Price column 
UPDATE sales_1
 SET profit = ROUND( profit, 3 )
 FROM sales_1;


 ------Round Discount Column
 UPDATE sales_1
 SET Discount = ROUND( Discount, 3 )
 FROM sales_1;


 ----Round price Column
  UPDATE sales_1
 SET price = ROUND( price, 3 )
 FROM sales_1;
  ```

### Exploratory Data Analysis
We Explored the Data to answer Questions such as; 
- Total Quantity of Products sold, Total Revenue, Profits and Best selling products.
- Monthly Sales Trend.

### Data Analysis
Below are some SQL code worked with
```
                                       -----(DATA EXPLORATION AND ANALYSIS)-----

 -----Sales Trend----
WITH Monthlytrend AS
	( 
	SELECT 
	   Region,
		YEAR(saledate) AS year,
		Month(SaleDate) AS month_Num,
		FORMAT(SaleDate, 'MMM') AS month,
		ROUND(SUM(Quantity), 2)  AS sales_Amount
	FROM Sales_1
    GROUP BY 
		Region,
        YEAR(saledate),
	    Month(SaleDate),
	    FORMAT(SaleDate, 'MMM')
  )
		SELECT 
			  Region,
			  year,
			  month, 
			  month_num,
			  sales_amount,
			  LAG(Sales_amount) OVER(ORDER BY month_num) AS prev_month,						   
		CASE
		WHEN 
			  LAG(Sales_amount) OVER(ORDER BY  month_num) IS NULL THEN 0
		ELSE 
		      ROUND((Sales_amount - LAG(Sales_amount) OVER(ORDER BY month_num)) / LAG(Sales_amount) OVER(ORDER BY month_num) * 100  ,2)
		END AS trend
		FROM 
			  Monthlytrend;
 


 --------TOP SELLING PRODUCT------
SELECT 
	Region,
	YEAR(SaleDate) AS year,
	FORMAT(SaleDate, 'MMM') AS Month,
	MONTH(Saledate) AS month_Num,
	ProductName,
	ROUND(SUM(Quantity), 2) AS total_Quantity
FROM sales_1
GROUP BY productname, 
	Year(saledate), 
	Format(saledate, 'MMM'),
	MONTH(Saledate)
Order BY SUM(Quantity) DESC;


------Sales Data----
 Select 
	  SaleDate, 
	  YEAR(SaleDate) AS year, 
	  MONTH(SaleDate) AS month_num, 
	  FORMAT(SaleDate, 'MMM') AS month,  
	  PRODUCTNAME, 
	  PRICE, 
	  QUANTITY,  
	  Region, 
	  Discount,
	  Profit  
 FROM sales_1;
````
### Result and Findings
- The Northen Region have experienced more declind in sales than other region.
- The Western Region have more Quantity Sold than other parts of the Country.
- They Haven't been cosistent  increase in sales from January to December.
- Product WIDGET-D have been the top performing products over others across all regions.
- Products WIDGET-C have not been selling like other products.
