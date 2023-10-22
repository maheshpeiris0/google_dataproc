Trino-dataset


CREATE OR REPLACE TABLE `project.stockprices.taxi_trips` AS 
(
  SELECT * FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips` LIMIT 10000000
)

export PROJECT=project
export WORKERS=3
export REGION=us-central1
export ZONE=us-central1-a
export BUCKET_NAME=Trino-dataset


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