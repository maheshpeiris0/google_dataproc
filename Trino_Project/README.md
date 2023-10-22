Trino-dataset


CREATE OR REPLACE TABLE `project.stockprices.taxi_trips` AS 
(
  SELECT * FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips` LIMIT 10000000
)

export PROJECT=society-etc-310720
export WORKERS=3
export REGION=us-central1
export ZONE=us-central1-a
export BUCKET_NAME=trino-dataset


https://cloud.google.com/dataproc/docs/tutorials/trino-dataproc?hl=en

zone : us-central1-a

gcloud beta dataproc clusters create trino-cluster \
    --project=${PROJECT} \
    --region=${REGION} \
    --zone=${ZONE} \
    --num-workers=${WORKERS} \
    --scopes=cloud-platform \
    --optional-components=TRINO \
    --image-version=2.1  \
    --enable-component-gateway


    n1-standard-2

    gcloud beta dataproc clusters create trino-cluster \
    --project=${PROJECT} \
    --region=${REGION} \
    --num-workers=${WORKERS} \
    --scopes=cloud-platform \
    --optional-components=TRINO \
    --image-version=2.1 \
    --enable-component-gateway \
    --master-machine-type=n1-standard-2 \
    --worker-machine-type=n1-standard-2




bq --location=us extract --destination_format=CSV \
     --field_delimiter=',' --print_header=false \
       "society-etc-310720:stockprices.taxi_trips" \
       gs://trino-dataset/chicago_taxi_trips/csv/*.csv

enable Cloud Resource Manager API

gcloud dataproc jobs submit hive \
    --cluster trino-cluster \
    --region=${REGION} \
    --execute "
        CREATE EXTERNAL TABLE chicago_taxi_trips_csv(
          unique_key   STRING,
          taxi_id  STRING,
          trip_start_timestamp  TIMESTAMP,
          trip_end_timestamp  TIMESTAMP,
          trip_seconds  INT,
          trip_miles   FLOAT,
          pickup_census_tract  INT,
          dropoff_census_tract  INT,
          pickup_community_area  INT,
          dropoff_community_area  INT,
          fare  FLOAT,
          tips  FLOAT,
          tolls  FLOAT,
          extras  FLOAT,
          trip_total  FLOAT,
          payment_type  STRING,
          company  STRING,
          pickup_latitude  FLOAT,
          pickup_longitude  FLOAT,
          pickup_location  STRING,
          dropoff_latitude  FLOAT,
          dropoff_longitude  FLOAT,
          dropoff_location  STRING)
        ROW FORMAT DELIMITED
        FIELDS TERMINATED BY ','
        STORED AS TEXTFILE
        location 'gs://${BUCKET_NAME}/chicago_taxi_trips/csv/';"


gcloud dataproc jobs submit hive \
    --cluster trino-cluster \
    --region=${REGION} \
    --execute "SELECT COUNT() FROM chicago_taxi_trips_csv;"

  gcloud dataproc jobs submit hive \
    --cluster trino-cluster \
    --region=${REGION} \
    --execute "
        CREATE EXTERNAL TABLE chicago_taxi_trips_parquet(
          unique_key   STRING,
          taxi_id  STRING,
          trip_start_timestamp  TIMESTAMP,
          trip_end_timestamp  TIMESTAMP,
          trip_seconds  INT,
          trip_miles   FLOAT,
          pickup_census_tract  INT,
          dropoff_census_tract  INT,
          pickup_community_area  INT,
          dropoff_community_area  INT,
          fare  FLOAT,
          tips  FLOAT,
          tolls  FLOAT,
          extras  FLOAT,
          trip_total  FLOAT,
          payment_type  STRING,
          company  STRING,
          pickup_latitude  FLOAT,
          pickup_longitude  FLOAT,
          pickup_location  STRING,
          dropoff_latitude  FLOAT,
          dropoff_longitude  FLOAT,
          dropoff_location  STRING)
        STORED AS PARQUET
        location 'gs://${BUCKET_NAME}/chicago_taxi_trips/parquet/';"

gcloud dataproc jobs submit hive \
    --cluster trino-cluster \
    --region=${REGION} \
    --execute "
        INSERT OVERWRITE TABLE chicago_taxi_trips_parquet
        SELECT * FROM chicago_taxi_trips_csv;"