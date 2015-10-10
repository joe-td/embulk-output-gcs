# Google Cloud Storage output plugin for Embulk

Google Cloud Storage output plugin for [Embulk](https://github.com/embulk/embulk).

## Overview

* **Plugin type**: file output
* **Load all or nothing**: no
* **Resume supported**: yes
* **Cleanup supported**: no

## Configuration

- **bucket**: Google Cloud Storage bucket name (string, required)
- **path_prefix**: Prefix of output keys (string, required)
- **file_ext**: Extention of output file (string, required)
- **content_type**: content type of output file (string, optional, default value is "application/octet-stream")
- **auth_method**: Authentication method `private_key`, `json_key` or `compute_engine` (string, optional, default value is "private_key")
- **service_account_email**: Google Cloud Platform service account email (string, required when auth_method is private_key)
- **p12_keyfile**: Private key file fullpath of Google Cloud Platform service account (string, required when auth_method is private_key)
- **json_keyfile** fullpath of json_key (string, required when auth_method is json_key)
- **application_name**: Application name, anything you like (string, optional, default value is "embulk-output-gcs")

## Example

```yaml
out:
  type: gcs
  bucket: your-gcs-bucket-name
  path_prefix: logs/out
  file_ext: .csv
  auth_method: `private_key` #default
  service_account_email: 'XYZ@developer.gserviceaccount.com'
  p12_keyfile: '/path/to/private/key.p12'
  formatter:
    type: csv
    encoding: UTF-8
```

## Authentication

There are three methods supported to fetch access token for the service account.

1. Public-Private key pair of GCP(Google Cloud Platform)'s service account
2. JSON key of GCP(Google Cloud Platform)'s service account
3. Pre-defined access token (Google Compute Engine only)

### Public-Private key pair of GCP's service account

You first need to create a service account (client ID), download its private key and deploy the key with embulk.

```yaml
out:
  type: gcs
  auth_method: private_key
  service_account_email: ABCXYZ123ABCXYZ123.gserviceaccount.com
  p12_keyfile: /path/to/p12_keyfile.p12
```

### JSON key of GCP's service account

You first need to create a service account (client ID), download its json key and deploy the key with embulk.

```yaml
out:
  type: gcs
  auth_method: json_key
  json_keyfile: /path/to/json_keyfile.json
```

You can also embed contents of json_keyfile at config.yml.

```yaml
out:
  type: gcs
  auth_method: json_key
  json_keyfile:
    content: |
      {
          "private_key_id": "123456789",
          "private_key": "-----BEGIN PRIVATE KEY-----\nABCDEF",
          "client_email": "..."
       }
```

### Pre-defined access token(GCE only)

On the other hand, you don't need to explicitly create a service account for embulk when you
run embulk in Google Compute Engine. In this third authentication method, you need to
add the API scope "https://www.googleapis.com/auth/devstorage.read_write" to the scope list of your
Compute Engine VM instance, then you can configure embulk like this.

[Setting the scope of service account access for instances](https://cloud.google.com/compute/docs/authentication)

```yaml
out:
  type: gcs
  auth_method: compute_engine
```

## Build

```
$ ./gradlew gem
```
