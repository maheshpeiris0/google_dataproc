Trino-dataset


CREATE OR REPLACE TABLE `society-etc-310720.stockprices.taxi_trips` AS 
(
  SELECT * FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips` LIMIT 10000000
)