Trino-dataset


CREATE OR REPLACE TABLE `society-etc-310720.stockprices.taxi_trips` AS 
(
  SELECT * FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips` LIMIT 10000000
)

export PROJECT=society-etc-310720
export WORKERS=3
export REGION=us-central1
export BUCKET_NAME=Trino-dataset


https://cloud.google.com/dataproc/docs/tutorials/trino-dataproc?hl=en

