# 1. Set up your environment variables
On your local shell (or Cloud Shell), define these so you don’t have to repeat yourself:

export PROJECT_ID=$(gcloud config get-value project)  
export REGION=northamerica-northeast1         # Toronto region  
export ZONE=northamerica-northeast1-b        # one of the zones in that region  
export BUCKET=lab9-20250419                  # your existing bucket  
export CLUSTER=dp-zeppelin-cluster           # pick a name for your cluster  

