# Sales Analysis


## Business Request

Steven - Sales Manager:

Hi Mega

I hope you are doing well. We need to improve our internet sales reports and want to move from static reports to visual dashboards.

Essentially, we want to focus it on how much we have sold of what products, to which clients and how it has been over time.

Seeing as each salesperson works on different products and customers, it would be beneficial to be able to filter them also.

We measure our numbers against the budget so I added that in a spreadsheet so we can compare our values against performance.

The budget is for 2021 and we usually look 2 years back when we do sales analysis.
Let me know if you need anything else!

|# |Role |Request | Value| Acceptance Criteria|
|---|---|---|---|---|
|1 |Sales Manager | To get a dashboard overview of internet sales|Can follow better which customers and products sell the best | A Power BI dashboard which updates data once a day|
|2 |Sales Representative | A detailed overview of Internet Sales per Customers| Can follow up with my customers that buy the most and to whom we can sell more to| A Power BI dashboard which allows me to filter data for each customer| 
| 3| Sales Representative|A detailed overview of Internet Sales per Products | Can follow up my Products that sell the most| A Power BI dashboard which allows me to filter data for each Product|
|4 | Sales Manager| A dashboard overview of internet sales|Follow sales over time against budget |A Power Bi dashboard with graphs and KPIs comparing against the budget. |



## Data Transformation

o fulfill the business needs and create a data model, SQL was used to extract the necessary data.

The data model was also connected to an Excel data source containing the sales budget. In the following SQL statements, necessary data is extracted, cleansed, and transformed



***creating date table***
```
SELECT 
       [DateKey]
      ,[FullDateAlternateKey] AS Date
      ,[EnglishDayNameOfWeek] AS Day
      ,[WeekNumberOfYear] AS Week
      ,[EnglishMonthName] AS Month
	    ,LEFT([EnglishMonthName],3) AS MonthShort
      ,[MonthNumberOfYear] AS MonthNo
      ,[CalendarQuarter] AS Quarter
      ,[CalendarYear] AS Year
  FROM 
	[AdventureWorksDW2019].[dbo].[DimDate]
  WHERE
	CalendarYear >= 2019
  ```
  
  
  ***creating customer table***
  ```
SELECT 
     [CustomerKey] AS CustomerKey 
    ,[FirstName] AS "First Name" 
    ,[LastName] AS "Last Name"
    ,FirstName + ' ' + LastName AS "Full Name" 
    ,[BirthDate] AS Birthday 
    ,CASE [Gender] WHEN 'M' THEN 'Male' WHEN 'F' THEN 'Female' END AS Gender
    ,[EmailAddress] 
    ,[Phone]
    ,[DateFirstPurchase] AS DateFirstPurchase 
  ,City AS "Customer City" 
FROM 
  [AdventureWorksDW2019].[dbo].[DimCustomer] AS c 
  LEFT JOIN dbo.DimGeography AS g on g.GeographyKey = c.GeographyKey 
ORDER BY 
  CustomerKey ASC
  ```
  
  
  ***extracting internet sales table***
  ```
SELECT 
       [ProductKey]
      ,[OrderDateKey]
      ,[DueDateKey]
      ,[ShipDateKey]
      ,[CustomerKey]
      ,[SalesAmount]
  FROM 
	[AdventureWorksDW2019].[dbo].[FactInternetSales] 
  WHERE
	LEFT(OrderDateKey,4) >= YEAR(GETDATE()) -2  
/*Ensures only 2 years of date from extraction is gotten*/
  
ORDER BY
	OrderDateKey ASC
  ```
  
  
  
  ***creating product table***
  ```
SELECT 
       [ProductKey]
      ,[ProductAlternateKey] AS ProductItemCode
      ,[EnglishProductName] AS "Product Name"
      ,pc.EnglishProductCategoryName AS "Product Category"                               
      ,ps.EnglishProductSubcategoryName AS "Product Sub Category"
      ,[Color] AS "Product Color"
      ,[Size] AS "Product Size"
      ,[ProductLine] AS "Product Line"
      ,[ModelName] AS "Product Model Name"
      ,[EnglishDescription] AS "Product Description"
      ,ISNULL([Status],'Outdated') AS "Product Status" 
  FROM 
	[AdventureWorksDW2019].[dbo].[DimProduct] AS p
	LEFT JOIN DimProductSubcategory AS ps ON ps.ProductSubcategoryKey = p.ProductSubcategoryKey
	LEFT JOIN DimProductCategory AS pc ON ps.ProductCategoryKey = pc.ProductCategoryKey
ORDER BY
	ProductKey asc
  ```
  
  ## Data Model
  
  The data model created using Power BI is seen below. 
  
  ![Data Model](https://user-images.githubusercontent.com/111463558/196273937-655ea173-2095-4562-8b28-07e3d2c6552e.png)


## Sales Management Dashboard

The first page of the dashboard shows an overview of sales, products, and customers. A further two pages combine detail and visualizations to allow users to gain a better understanding of customer and product information.
As seen below

![Sales Overview](https://user-images.githubusercontent.com/111463558/196274115-afc1c8e8-a5bd-42ef-b9a9-e968a61f0355.png)


![Product Details](https://user-images.githubusercontent.com/111463558/196274155-137c9fbe-b8cc-48d2-b34a-8975aba42803.png)


![Customer Details](https://user-images.githubusercontent.com/111463558/196274183-41b9c838-ca7e-46fe-871f-1082ea63d969.png)

  
