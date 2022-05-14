Litestream & Docker Example
===========================

This repository provides an example of running a Go application in the same
container as Litestream by using the built-in subprocess execution. This allows
developers to release their SQLite-based application and provide replication in
a single container.


## Usage

### Prerequisites

* Create a Google Cloud project, write down its project ID
* [Create a Cloud Storage bucket](https://cloud.google.com/storage/docs/creating-buckets), use a single region, for example `us-central1`, and write down the bucket name


### Building & deploy the sample to Cloud Run

Clone this repository and na

You can build the application with the following command:

```sh
gcloud run deploy litestream-example \
  --source .  \
  --set-env-vars REPLICA_URL=gcs://BUCKET_NAME/database \
  --execution-environment gen2 \
  --region REGION \
  --no-cpu-throttling \
  --allow-unauthenticated \ 
  --project PROJECT_ID
```

Replace:

* `BUCKET_NAME` with your Cloud Storage bucket name
* `REGION` with the same region where you created the bucket, for example `us-central1`
* `PROJECT_ID` with your Google Cloud project ID.

When the deployment completes, open the `.run.app` URL of the Cloud Run service.

The command has built the current source code into a container using Cloud Build then deployed it to Cloud Run.
We have set the `REPLICA_URL` environment variable to point at the Cloud Storage URL, we are using the second generation execution environment, we asked to have CPU always allocated  and we have made the service publicly accessible by allowing unauthenticated invocations.

### Additional security

Containers deployed to Cloud Run run with an identity, by default, it is the "Default Compute Service Account", which has read/write access to Cloud Storage buckets in the same project.
For additional security, it is recommended to create a dedicated Servie Account with read/write permission on the Cloud Storage bucket and then use this service account as the identity of the Cloud Run service. 
Read more [here](https://cloud.google.com/run/docs/securing/service-identity)
