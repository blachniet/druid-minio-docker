# Druid and MinIO

This is a fork of the official [Druid Docker Compose]. This fork uses MinIO for
[Deep Storage]. You may also load data from MinIO with this setup.

## Get started

### Start the services

**Note for v0.21.0**: See the [known permission issue] and workaround.

Start the services with Docker Compose.

```
docker-compose up -d
```

Navigate to <http://localhost:9000>. Login with:

- Access Key: `minioroot`
- Secret Key: `minioroot`

Create buckets named `druid-deepstorage`, `druid-archive` and `druid-input`.

### Load data from MinIO

Copy the sample file from the container.

```bash
docker cp <druid-container-id>:/opt/druid/quickstart/tutorial/wikiticker-2015-09-12-sampled.json.gz wikiticker-2015-09-12-sampled.json.gz
```

Upload the sample file to a MinIO bucket named `druid-input`.

Navigate to <http://localhost:8888/unified-console.html#load-data>. Select "Amazon S3" and then click "Connect data".

On the next screen, enter the following options:

| Field                              | Value |
|------------------------------------|-------|
| Source type                        | `s3`  |
| S3 URIs                            | `s3://druid-input/wikiticker-2015-09-12-sampled.json.gz` |
| S3 prefixes                        | leave blank |
| Access key ID type                 | `environment` |
| Access key ID environment variable | `druid_s3_accessKey` |
| Secret key type                    | `envrionment` |
| Secret key value                   | `druid_s3_secretKey` |

Follow the [Load Data] instructions in the Druid Quickstart to finish building
the load data spec.

Once loaded, you can see the segement data in the deep storage bucket at
<http://localhost:9000/minio/druid-deepstorage/wikiticker-2015-09-12-sampled/>.

## How it works

Diff the `docker-compose.yml` and `environment` files with those in the Druid
repository to see what I had to do to use MinIO with Druid. Here's a couple of
one-liners to diff the two:

```bash
curl https://raw.githubusercontent.com/apache/druid/druid-0.21.0/distribution/docker/docker-compose.yml | diff - docker-compose.yml 
curl https://raw.githubusercontent.com/apache/druid/druid-0.21.0/distribution/docker/environment | diff - environment
```

[Druid Docker Compose]: https://github.com/apache/druid/tree/master/distribution/docker
[Deep Storage]: https://druid.apache.org/docs/latest/dependencies/deep-storage.html
[known permission issue]: https://github.com/apache/druid/releases/tag/druid-0.21.0#21-docker-volume-ownership
[Load Data]: https://druid.apache.org/docs/latest/tutorials/index.html#step-4-load-data