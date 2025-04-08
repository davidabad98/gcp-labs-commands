# GCP Commands Guide
## Lab7 
gsutil mb gs://lab7-20250407

cluster-62ab
2.1 (Ubuntu 20.04 LTS, Hadoop 3.3, Spark 3.3)
First released on 12/12/2022.

hadoop fs -mkdir -p /user/davidabad081998/cars_data
hadoop fs -ls /user/davidabad081998
hadoop fs -cp gs://lab7-20250407/all_anonymized_2015_11_2017_03.csv /user/davidabad081998/cars_data/
hadoop fs -ls /user/davidabad081998/cars_data/

hive

CREATE EXTERNAL TABLE IF NOT EXISTS cars (
    maker               STRING,
    model               STRING,
    mileage             DOUBLE,
    manufacture_year    DOUBLE,
    engine_displacement DOUBLE,
    engine_power        DOUBLE,
    body_type           STRING,
    color_slug          STRING,
    stk_year            DOUBLE,
    transmission        STRING,
    door_count          DOUBLE,
    seat_count          DOUBLE,
    fuel_type           STRING,
    date_created        STRING,
    date_last_seen      STRING,
    price_eur           DOUBLE
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION '/user/davidabad081998/cars_data/'
TBLPROPERTIES ("skip.header.line.count"="1");

SELECT COUNT(*) FROM cars;
SELECT * FROM cars LIMIT 10;

#Identify Null or Outlier Values
SELECT COUNT(*) FROM cars WHERE price_eur IS NULL OR price_eur = 0;

#extreme values
SELECT 
    maker, 
    model, 
    price_eur
FROM cars
WHERE price_eur > 1000000  -- Adjust threshold as needed
ORDER BY price_eur DESC
LIMIT 20;



Question 1: What is the Relationship Between Car Makes, Models, and Price?
-- Filter out extremely high prices (likely data issues)
SELECT 
    maker, 
    model, 
    AVG(price_eur) AS avg_price, 
    COUNT(*) AS total_listings
FROM cars
WHERE price_eur BETWEEN 100 AND 200000  -- Filtering out unrealistic extremes
GROUP BY maker, model
ORDER BY avg_price DESC;


--Option A: Limit to Top Models by Volume
SELECT 
    maker, 
    model, 
    COUNT(*) AS total_listings, 
    ROUND(AVG(price_eur), 2) AS avg_price
FROM cars
WHERE price_eur BETWEEN 100 AND 200000
  AND maker IS NOT NULL AND TRIM(maker) != ''
  AND model IS NOT NULL AND TRIM(model) != ''
GROUP BY maker, model
HAVING COUNT(*) > 100
ORDER BY total_listings DESC
LIMIT 20;


--Option C: Just Focus on Top 5 Makers
SELECT 
    maker, 
    model, 
    COUNT(*) AS total_listings, 
    AVG(price_eur) AS avg_price
FROM cars
WHERE price_eur BETWEEN 100 AND 200000
  AND maker IN ('BMW', 'Mercedes-Benz', 'Audi', 'Toyota', 'Volkswagen')  -- Change as needed
GROUP BY maker, model
ORDER BY avg_price DESC;




Question 2: What are the Top Five Vehicle Manufacturers You Would Recommend? Why?
SELECT 
    maker, 
    ROUND(AVG(price_eur), 2) AS avg_price, 
    COUNT(*) AS listings
FROM cars
WHERE price_eur BETWEEN 100 AND 200000
  AND maker IS NOT NULL AND TRIM(maker) != ''
  AND model IS NOT NULL AND TRIM(model) != ''
GROUP BY maker
HAVING COUNT(*) > 50
ORDER BY listings DESC
LIMIT 5;


Question 3: Does Fuel Type Have Any Impact on Car Price? Explain
SELECT 
    fuel_type, 
    ROUND(AVG(price_eur), 2) AS avg_price, 
    COUNT(*) AS listings
FROM cars
WHERE price_eur BETWEEN 100 AND 200000
  AND fuel_type IS NOT NULL AND TRIM(fuel_type) != ''
  AND maker IS NOT NULL AND TRIM(maker) != ''
  AND model IS NOT NULL AND TRIM(model) != ''
GROUP BY fuel_type
ORDER BY avg_price DESC;





