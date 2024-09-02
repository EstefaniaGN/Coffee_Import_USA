>> About Dataset

This dataset was created to provide information about the importation of coffee from Colombia to the USA from 2019 to 2024. The dataset includes the HS code, product description, year, month, value in USD, and quantity in KG.

- Discover the historical trend of Colombian coffee imports to the USA.
- Perform an analysis of the products that are imported in the highest quantities
- Based on the collected data, draw some conclusions and perform an analysis.


>>> The language program I worked on was SQL in BigQuery. The code presented is not related to Python.

# Cleaning Data 

### In the countries column, there are many rows with null data, so I used an update query to fill in the missing information.

    1  UPDATE `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA` 
    2  SET 
    3  Country_Exporter = 'Colombia',
    4  Country_Importer = 'USA'
    5  WHERE Country_Exporter is null and Country_Importer is null 

### I noticed that the type in the 'Year' column was recognized as an integer instead of a string, so I used the query CAST to update it to the required type.

    1 SELECT CAST(Year AS STRING) AS Year_string
    2 FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`

### Despite running the query, it is still recognized as an integer. To fix this issue, I had to create a new column or table with the desired data.

-- Step 1: Add a new column

    1 ALTER TABLE `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
    2 ADD COLUMN year_string STRING;

-- Step 2: Update the new column with converted values

    1 UPDATE `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
    2 SET year_string = CAST(Year AS STRING) WHERE TRUE;

-- Step 3: Drop the old column

    1 ALTER TABLE `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
    2 DROP COLUMN Year;

-- Step 4: Rename the new column

    1 ALTER TABLE `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
    2 RENAME COLUMN year_string TO Year;

###  The values in the "months" column were inconsistent, so I fixed that with an update query, changing all the long months to their short versions.

     1 UPDATE 
     2 `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA` 
     3 SET Month = CASE
     4   WHEN Month ='January' THEN 'Jan'
     5   WHEN Month = 'February'THEN 'Feb'
     6   WHEN Month = 'March' THEN 'Mar'
     7   WHEN Month = 'April' THEN 'Apr'
     8   WHEN Month = 'June' THEN 'Jun'
     9   WHEN Month = 'July' THEN 'Jul'
     10 ELSE Month
     11 END
     12 WHERE Month IN ('January','February','March','April','June','July' )

# Analyzing data


### What are the three top products imported in the USA

    1 WITH RankedData AS (
    2 SELECT
    3  *,
    4  ROW_NUMBER() OVER (PARTITION BY HS_Code ORDER BY Value DESC) AS rn
    5  FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
    6  WHERE
    7  Year = '2019' and
    8  HS_Code is not null
    9    )
    10 SELECT
    11  *
    12 FROM RankedData
    13 WHERE rn = 1
    14 ORDER BY Value DESC
    15 LIMIT 3;

    -------------------------------------------------------
    
    1 WITH RankedData AS (
    2 SELECT
    3  *,
    4  ROW_NUMBER() OVER (PARTITION BY HS_Code ORDER BY Value DESC) AS rn
    5  FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
    6  WHERE
    7  Year = '2020' and
    8  HS_Code is not null
    9    )
    10 SELECT
    11  *
    12 FROM RankedData
    13 WHERE rn = 1
    14 ORDER BY Value DESC
    15 LIMIT 3;

    ----------------------------------------------------
    
    1 WITH RankedData AS (
    2 SELECT
    3  *,
    4  ROW_NUMBER() OVER (PARTITION BY HS_Code ORDER BY Value DESC) AS rn
    5  FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
    6  WHERE
    7  Year = '2021' and
    8  HS_Code is not null
    9    )
    10 SELECT
    11  *
    12 FROM RankedData
    13 WHERE rn = 1
    14 ORDER BY Value DESC
    15 LIMIT 3;
    
    --------------------------------------------------------------
    
    1 WITH RankedData AS (
    2 SELECT
    3  *,
    4  ROW_NUMBER() OVER (PARTITION BY HS_Code ORDER BY Value DESC) AS rn
    5  FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
    6  WHERE
    7  Year = '2021' and
    8  HS_Code is not null
    9    )
    10 SELECT
    11  *
    12 FROM RankedData
    13 WHERE rn = 1
    14 ORDER BY Value DESC
    15 LIMIT 3;
    
    ---------------------------------------------------------------------
    
    1 WITH RankedData AS (
    2 SELECT
    3  *,
    4  ROW_NUMBER() OVER (PARTITION BY HS_Code ORDER BY Value DESC) AS rn
    5  FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
    6  WHERE
    7  Year = '2022' and
    8  HS_Code is not null
    9    )
    10 SELECT
    11  *
    12 FROM RankedData
    13 WHERE rn = 1
    14 ORDER BY Value DESC
    15 LIMIT 3;
    
    -----------------------------------------------------------------------
    
    1 WITH RankedData AS (
    2 SELECT
    3  *,
    4  ROW_NUMBER() OVER (PARTITION BY HS_Code ORDER BY Value DESC) AS rn
    5  FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
    6  WHERE
    7  Year = '2023' and
    8  HS_Code is not null
    9    )
    10 SELECT
    11  *
    12 FROM RankedData
    13 WHERE rn = 1
    14 ORDER BY Value DESC
    15 LIMIT 3;
    
    ---------------------------------------------------------------------
    
    1 WITH RankedData AS (
    2 SELECT
    3  *,
    4  ROW_NUMBER() OVER (PARTITION BY HS_Code ORDER BY Value DESC) AS rn
    5  FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
    6  WHERE
    7  Year = '2024' and
    8  HS_Code is not null
    9    )
    10 SELECT
    11  *
    12 FROM RankedData
    13 WHERE rn = 1
    14 ORDER BY Value DESC
    15 LIMIT 3;
    
    ------------------------------------------------------------------------
    
### Now that I have the data for the top three products imported into the USA from 2019 to 2024, I would like to know the total imports (SUM) for each of the three products for each year.

        1 SELECT
        2 HS_Code, 
        3  SUM(Qty) AS Total_Qty,
        4  SUM(Value) AS Total_Value
        5 FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
        6 WHERE
        7  Year = '2019'
        8  AND HS_Code IN (901110025, 901110055, 2101112129)  
        9 GROUP BY
        10  HS_Code
        1 SELECT
        2 HS_Code, 
        3  SUM(Qty) AS Total_Qty,
        4  SUM(Value) AS Total_Value
        5 FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
        6 WHERE
        7  Year = '2020'
        8  AND HS_Code IN (901110025, 901110055, 2101112129)  
        9 GROUP BY
        10  HS_Code
        1 SELECT
        2 HS_Code, 
        3  SUM(Qty) AS Total_Qty,
        4  SUM(Value) AS Total_Value
        5 FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
        6 WHERE
        7  Year = '2021'
        8  AND HS_Code IN (901110025, 901110055, 2101112129)  
        9 GROUP BY
        10  HS_Code
        1 SELECT
        2 HS_Code, 
        3  SUM(Qty) AS Total_Qty,
        4  SUM(Value) AS Total_Value
        5 FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
        6 WHERE
        7  Year = '2022'
        8  AND HS_Code IN (901110025, 901110055, 2101112129)  
        9 GROUP BY
        10  HS_Code
        1 SELECT
        2 HS_Code, 
        3  SUM(Qty) AS Total_Qty,
        4  SUM(Value) AS Total_Value
        5 FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
        6 WHERE
        7  Year = '2023'
        8  AND HS_Code IN (901110025, 901110055, 2101112129)  
        9 GROUP BY
        10  HS_Code
        1 SELECT
        2 HS_Code, 
        3  SUM(Qty) AS Total_Qty,
        4  SUM(Value) AS Total_Value
        5 FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
        6 WHERE
        7  Year = '2019'
        8  AND HS_Code IN (901110025, 901120025, 2101112129)  
        9 GROUP BY
        10  HS_Code
        
### What is the average difference in the importation of product 901110025 between January and June of 2023 and 2024?

      1 WITH MonthlyTotals AS (
      2  SELECT
      3    Year,
      4    Month,
      5    HS_Code,
      6    SUM(Qty) AS Total_Qty,
      7    SUM(Value) AS Total_Value
      8  FROM `massive-capsule-433021-b8.Coffee_Imported.CO_Imported_USA`
      9  WHERE
      10    Month IN ('Jan', 'Feb', 'March', 'Apr', 'May', 'Jun')
      11    AND Year IN ('2023', '2024')
      12    AND HS_Code IN  (901110025)
      13  GROUP BY
      14    Year,
      15    Month,
      16    HS_Code
      17 ),
      18
      19 YearlyDifferences AS (
      20 SELECT
      21    a.HS_Code,
      22    a.Month,
      23    a.Total_Qty AS Total_Qty_2023,
      24    a.Total_Value AS Total_Value_2023,
      25    b.Total_Qty AS Total_Qty_2024,
      26    b.Total_Value AS Total_Value_2024,
      27    (a.Total_Qty - b.Total_Qty) AS Qty_Difference,
      28    (a.Total_Value - b.Total_Value) AS Value_Difference
      29  FROM
      30    MonthlyTotals a
      31  JOIN
      32    MonthlyTotals b
      33  ON
      34    a.HS_Code = b.HS_Code
      35    AND a.Month = b.Month
      36    AND a.Year = '2023'
      37    AND b.Year = '2024'
      38 ),
      39
      40 MonthlyAverages AS (
      41  SELECT
      42   Month,
      43    AVG(Qty_Difference) AS Avg_Qty_Difference,
      44    AVG(Value_Difference) AS Avg_Value_Difference
      45  FROM
      46    YearlyDifferences
      47  GROUP BY
      48    Month
      49 )
      50
      51 SELECT
      52  Month,
      53  Avg_Qty_Difference,
      54  Avg_Value_Difference
      55 FROM
      56  MonthlyAverages
      57 ORDER BY
      58  CASE Month
      59    WHEN 'Jan' THEN 1
      60    WHEN 'Feb' THEN 2
      61    WHEN 'Mar' THEN 3
      62    WHEN 'Apr' THEN 4
      63   WHEN 'May' THEN 5
      64    WHEN 'Jun' THEN 6
      65        ELSE 8  -- This handles any unexpected month values
      66  END;
