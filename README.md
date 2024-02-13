## Preppin Data 2024 Wk1 Flight Data Transformation and Analysis with SQL

This Readme contains a breakdown of approaching building a SQL query to transforma and analyse hypothetical "flight data" table. Each query is presented with comments and explanations for clarity.

**Queries:**

**1. View The Tables You are working with:**

```sql
SELECT
  *
FROM TIL_PLAYGROUND.PREPPIN_DATA_INPUTS.PD2024_WK01;
```
**Explanation:** Before we can plan how to reshape the data we need to understand what we are working with. This query allows us to see that Flight_Details as a column contains a range of additional information including flight date, source, destination, fare etc. Split with two '/' for each piece of information.

**2. Splitting a Column:**

```sql
SELECT 
    SPLIT_PART(FLIGHT_DETAILS, '//', 1) AS FLIGHT_DATE,
    SPLIT_PART(FLIGHT_DETAILS, '//', 2) AS FLIGHT_NO,
    SPLIT_PART(SPLIT_PART(FLIGHT_DETAILS, '//', 3), '-', 1) AS SOURCE,
    SPLIT_PART(SPLIT_PART(FLIGHT_DETAILS, '//', 3), '-', 2) AS DESTINATION,
    SPLIT_PART(FLIGHT_DETAILS, '//', 4) AS CABIN,
    SPLIT_PART(FLIGHT_DETAILS, '//', 5) AS FARE,
    FLOW_CARD, 
    BAGS_CHECKED, 
    MEAL_TYPE
FROM TIL_PLAYGROUND.PREPPIN_DATA_INPUTS.PD2024_WK01;
```
**Explanation:** Having identified the additional information in Flight Details we use split part to pull each piece of information from that field. For source and destination we nest a split to get both parts of that section in individual columns.

**2. Correcting Datatypes:**

```sql
SELECT 
    DATE(SPLIT_PART(FLIGHT_DETAILS, '//', 1), 'YYYY-MM-DD') AS FLIGHT_DATE,
    SPLIT_PART(FLIGHT_DETAILS, '//', 2) AS FLIGHT_NO,
    SPLIT_PART(SPLIT_PART(FLIGHT_DETAILS, '//', 3), '-', 1) AS SOURCE,
    SPLIT_PART(SPLIT_PART(FLIGHT_DETAILS, '//', 3), '-', 2) AS DESTINATION,
    SPLIT_PART(FLIGHT_DETAILS, '//', 4) AS CABIN,
    TO_DECIMAL(SPLIT_PART(FLIGHT_DETAILS, '//', 5),10, 2) AS FARE,
    CASE FLOW_CARD
        WHEN 1 THEN 'YES'
        ELSE 'NO'
        END AS FLOW_CARD, 
    BAGS_CHECKED, 
    MEAL_TYPE
FROM TIL_PLAYGROUND.PREPPIN_DATA_INPUTS.PD2024_WK01;
```
**Explanation:** In order to store data effectively, data should be stored with the correct types. We use DATE and TO_DECIMAL to adjust the date to date format and the fare to a numeric datatype with 2 decimal places. Ordinarily storing a boolean as 1/0 is better for reducing size of data but we had a particular request in this challenge to convert to a YES/NO string. We use a simple case statement to convert this column.

**3. Filtering so the queries return two tables:**
FLOW_CARD holders in Query 1
```sql
SELECT 
    DATE(SPLIT_PART(FLIGHT_DETAILS, '//', 1), 'YYYY-MM-DD') AS FLIGHT_DATE,
    SPLIT_PART(FLIGHT_DETAILS, '//', 2) AS FLIGHT_NO,
    SPLIT_PART(SPLIT_PART(FLIGHT_DETAILS, '//', 3), '-', 1) AS SOURCE,
    SPLIT_PART(SPLIT_PART(FLIGHT_DETAILS, '//', 3), '-', 2) AS DESTINATION,
    SPLIT_PART(FLIGHT_DETAILS, '//', 4) AS CABIN,
    TO_DECIMAL(SPLIT_PART(FLIGHT_DETAILS, '//', 5),10, 2) AS FARE,
    CASE FLOW_CARD
        WHEN 1 THEN 'YES'
        ELSE 'NO'
        END AS FLOW_CARD, 
    BAGS_CHECKED, 
    MEAL_TYPE
FROM TIL_PLAYGROUND.PREPPIN_DATA_INPUTS.PD2024_WK01
WHERE FLOW_CARD = 1;
```
FLOW_CARD non-holders in Query2
```sql
SELECT 
    DATE(SPLIT_PART(FLIGHT_DETAILS, '//', 1), 'YYYY-MM-DD') AS FLIGHT_DATE,
    SPLIT_PART(FLIGHT_DETAILS, '//', 2) AS FLIGHT_NO,
    SPLIT_PART(SPLIT_PART(FLIGHT_DETAILS, '//', 3), '-', 1) AS SOURCE,
    SPLIT_PART(SPLIT_PART(FLIGHT_DETAILS, '//', 3), '-', 2) AS DESTINATION,
    SPLIT_PART(FLIGHT_DETAILS, '//', 4) AS CABIN,
    TO_DECIMAL(SPLIT_PART(FLIGHT_DETAILS, '//', 5),10, 2) AS FARE,
    CASE FLOW_CARD
        WHEN 1 THEN 'YES'
        ELSE 'NO'
        END AS FLOW_CARD, 
    BAGS_CHECKED, 
    MEAL_TYPE
FROM TIL_PLAYGROUND.PREPPIN_DATA_INPUTS.PD2024_WK01
WHERE FLOW_CARD = 0;
```
**Explanation:** We use the "WHERE" part of a SQL query to filter the final output on the FLOW_CARD Field, because we overwrote the field to string with case but kept the same column name and the order of operations on a SQL Query we filter on the pre-CASE statement values.

**Additional Notes:**

    Replace TIL_PLAYGROUND.PREPPIN_DATA_INPUTS.PD2024_WK01 with the actual table name and schema in your database.
    Challenge from Preppin Data.

