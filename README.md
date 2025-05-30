Litestream & Cloud Run example
===========================

This repository provides an example of running a Go application in the same
container as Litestream by using the built-in subprocess execution. This allows
developers to release their SQLite-based application and provide replication in
a single container.


## Usage

### Prerequisites

* Create a Google Cloud project, write down its project ID.
* [Create a Cloud Storage bucket](https://cloud.google.com/storage/docs/creating-buckets) in a single region, for example `us-central1`, and write down the bucket name.


### Build & deploy the sample to Cloud Run

Clone this repository and navigate to the cloned location.

Then build and deploy the application with the following command:

```sh
gcloud beta run deploy litestream-example \
  --source .  \
  --set-env-vars REPLICA_URL=gcs://BUCKET_NAME/database \
  --max-instances 1 \
  --execution-environment gen2 \
  --no-cpu-throttling \
  --allow-unauthenticated \
  --region REGION \
  --project PROJECT_ID
```

Replace:

* `BUCKET_NAME` with your Cloud Storage bucket name
* `REGION` with the same region where you created the bucket, for example `us-central1`
* `PROJECT_ID` with your Google Cloud project ID.

When the deployment completes, open the `.run.app` URL of the Cloud Run service.

**Important:** Litestream currently does not work well on automatically scaled platforms like Cloud Run because it **expects a maximum of one instance**. Make sure to use `--max-instances 1` or `--scale 1`.

The command has built the source code into a container using Cloud Build then deployed it to Cloud Run.

The command:

* sets the `REPLICA_URL` environment variable to point at the Cloud Storage URL (note that Livestream expects `gcs://` instead of `gs://`, this will change in the next Litestream release) 
* forces a maximum of one instance because Litestream isn't compatible with multiple servers
* uses the Cloud Run second generation execution environment for better performance
* asks for the CPU to always be allocated (evenoutside of requests processing)
* makes the service publicly accessible by allowing unauthenticated invocations

### Additional security

Containers deployed to Cloud Run run with an identity, by default, it is the "Default Compute Service Account", which has read/write access to all Cloud Storage buckets in the same project as well as a lot of other permissions on resources in the same project.
For additional security, it is recommended to create a dedicated Servie Account with read/write permission on the Cloud Storage bucket and then use this service account as the identity of the Cloud Run service. 
Read more [here](https://cloud.google.com/run/docs/securing/service-identity)
