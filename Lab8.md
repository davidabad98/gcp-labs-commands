PROJECT_ID="lab7-hadoop-hive"
BUCKET_NAME="lab8-20240407"
REGION="us-central1"
# Step 1: Create a Storage Bucket
gsutil mb -p $PROJECT_ID -l $REGION gs://$BUCKET_NAME/

# Step 2: Create a Dataproc Cluster with JupyterLab
gcloud dataproc clusters create yellow-taxi-cluster \
    --region=$REGION \
    --single-node \
    --enable-component-gateway \
    --optional-components=JUPYTER \
    --image-version=2.1-debian11 \
    --bucket=$BUCKET_NAME \
    --project=$PROJECT_ID \
    --properties=dataproc:jupyter.notebook.gcs.dir=gs://$BUCKET_NAME/notebooks/


# Step 3: Open JupyterLab
Go to Dataproc > Clusters > [your cluster]

Click on Web Interfaces

Open JupyterLab

# Step 4: Use Terminal in JupyterLab
Inside JupyterLab:

Click "Terminal" under Launcher

Use this terminal to download directly from NYC TLC.

# Step 5: Download Yellow Taxi 2022 Data Using wget
Run this in JupyterLab terminal:

mkdir yellow-taxi-2022
cd yellow-taxi-2022

for month in {1..12}; do
  wget https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2022-$(printf "%02d" $month).parquet
done

# Step 6: Copy Files to GCS in Parallel
Use gsutil -m cp to copy all files at once:

gsutil -m cp *.parquet gs://lab8-20240407/data/


# Step 7: Verify and Clean Up
✅ Verify GCS:
gsutil ls gs://lab8-20240407/data/


# Step 8: Load Data From GCS in JupyterLab Notebook
Now you can open a Jupyter notebook and use PySpark or Pandas with GCS.

from pyspark.sql import SparkSession

spark = SparkSession.builder.getOrCreate()

df = spark.read.parquet("gs://your-bucket-name/data/")
df.show(5)


