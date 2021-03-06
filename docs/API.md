# Python Client API Reference [![Slack](https://slack.min.io/slack?type=svg)](https://slack.min.io)

## Initialize MinIO Client object.

## MinIO

```py
from minio import Minio
from minio.error import ResponseError

minioClient = Minio(
	'play.min.io',
	access_key='Q3AM3UQ867SPQQA43P2F',
	secret_key='zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG',
	secure=True,
)
```

## AWS S3

```py
from minio import Minio
from minio.error import ResponseError

s3Client = Minio(
	's3.amazonaws.com',
	access_key='YOUR-ACCESSKEYID',
	secret_key='YOUR-SECRETACCESSKEY',
	secure=True,
)
```

| Bucket operations                                           | Object operations                                               |
|:------------------------------------------------------------|:----------------------------------------------------------------|
| [`make_bucket`](#make_bucket)                               | [`get_object`](#get_object)                                     |
| [`list_buckets`](#list_buckets)                             | [`put_object`](#put_object)                                     |
| [`bucket_exists`](#bucket_exists)                           | [`copy_object`](#copy_object)                                   |
| [`remove_bucket`](#remove_bucket)                           | [`stat_object`](#stat_object)                                   |
| [`list_objects`](#list_objects)                             | [`remove_object`](#remove_object)                               |
| [`get_bucket_versioning`](#get_bucket_versioning)           | [`remove_objects`](#remove_objects)                             |
| [`set_bucket_versioning`](#set_bucket_versioning)           | [`fput_object`](#fput_object)                                   |
| [`delete_bucket_replication`](#delete_bucket_replication)   | [`fget_object`](#fget_object)                                   |
| [`get_bucket_replication`](#get_bucket_replication)         | [`select_object_content`](#select_object_content)               |
| [`set_bucket_replication`](#set_bucket_replication)         | [`delete_object_tags`](#delete_object_tags)                     |
| [`delete_bucket_lifecycle`](#delete_bucket_lifecycle)       | [`get_object_tags`](#get_object_tags)                           |
| [`get_bucket_lifecycle`](#get_bucket_lifecycle)             | [`set_object_tags`](#set_object_tags)                           |
| [`set_bucket_lifecycle`](#set_bucket_lifecycle)             | [`enable_object_legal_hold`](#enable_object_legal_hold)         |
| [`delete_bucket_tags`](#delete_bucket_tags)                 | [`disable_object_legal_hold`](#disable_object_legal_hold)       |
| [`get_bucket_tags`](#get_bucket_tags)                       | [`is_object_legal_hold_enabled`](#is_object_legal_hold_enabled) |
| [`set_bucket_tags`](#set_bucket_tags)                       | [`get_object_retention`](#get_object_retention)                 |
| [`delete_bucket_policy`](#delete_bucket_policy)             | [`set_object_retention`](#set_object_retention)                 |
| [`get_bucket_policy`](#get_bucket_policy)                   | [`presigned_get_object`](#presigned_get_object)                 |
| [`set_bucket_policy`](#set_bucket_policy)                   | [`presigned_put_object`](#presigned_put_object)                 |
| [`delete_bucket_notification`](#delete_bucket_notification) | [`presigned_post_policy`](#presigned_post_policy)               |
| [`get_bucket_notification`](#get_bucket_notification)       |                                                                 |
| [`set_bucket_notification`](#set_bucket_notification)       |                                                                 |
| [`listen_bucket_notification`](#listen_bucket_notification) |                                                                 |
| [`delete_bucket_encryption`](#delete_bucket_encryption)     |                                                                 |
| [`get_bucket_encryption`](#get_bucket_encryption)           |                                                                 |
| [`set_bucket_encryption`](#set_bucket_encryption)           |                                                                 |
| [`delete_object_lock_config`](#delete_object_lock_config)   |                                                                 |
| [`get_object_lock_config`](#get_object_lock_config)         |                                                                 |
| [`set_object_lock_config`](#set_object_lock_config)         |                                                                 |

## 1. Constructor

<a name="MinIO"></a>

### Minio(endpoint, access_key=None, secret_key=None, session_token=None, secure=True, region=None, http_client=None, credentials=None)
|                                                                                                                                       |
|---------------------------------------------------------------------------------------------------------------------------------------|
| `Minio(endpoint, access_key=None, secret_key=None, session_token=None, secure=True, region=None, http_client=None, credentials=None)` |
| Initializes a new client object.                                                                                                      |

__Parameters__

| Param           | Type                              | Description                                                                      |
|:----------------|:----------------------------------|:---------------------------------------------------------------------------------|
| `endpoint`      | _str_                             | Hostname of a S3 service.                                                        |
| `access_key`    | _str_                             | (Optional) Access key (aka user ID) of your account in S3 service.               |
| `secret_key`    | _str_                             | (Optional) Secret Key (aka password) of your account in S3 service.              |
| `session_token` | _str_                             | (Optional) Session token of your account in S3 service.                          |
| `secure`        | _bool_                            | (Optional) Flag to indicate to use secure (TLS) connection to S3 service or not. |
| `region`        | _str_                             | (Optional) Region name of buckets in S3 service.                                 |
| `http_client`   | _urllib3.poolmanager.PoolManager_ | (Optional) Customized HTTP client.                                               |
| `credentials`   | _minio.credentials.Credentials_   | (Optional) Credentials of your account in S3 service.                            |


**NOTE on concurrent usage:** The `Minio` object is thread safe when using the Python `threading` library. Specifically, it is **NOT** safe to share it between multiple processes, for example when using `multiprocessing.Pool`. The solution is simply to create a new `Minio` object in each process, and not share it between processes.

__Example__

### MinIO

```py
from minio import Minio
from minio.error import ResponseError

minioClient = Minio(
	'play.min.io',
	access_key='Q3AM3UQ867SPQQA43P2F',
	secret_key='zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG',
)
```

> NOTE: If there is a corporate proxy, specify a custom httpClient using *urllib3.ProxyManager* as shown below:

```py
from minio import Minio
from minio.error import ResponseError
import urllib3

httpClient = urllib3.ProxyManager(
	'https://proxy_host.sampledomain.com:8119/',
	timeout=urllib3.Timeout.DEFAULT_TIMEOUT,
	cert_reqs='CERT_REQUIRED',
	retries=urllib3.Retry(
		total=5,
		backoff_factor=0.2,
		status_forcelist=[500, 502, 503, 504],
	)
)

minioClient = Minio(
	'your_hostname.sampledomain.com:9000',
	access_key='ACCESS_KEY',
	secret_key='SECRET_KEY',
	secure=True,
	http_client=httpClient,
)
```

### AWS S3

```py
from minio import Minio
from minio.error import ResponseError

s3Client = Minio(
	's3.amazonaws.com',
	access_key='ACCESS_KEY',
	secret_key='SECRET_KEY',
)
```

## 2. Bucket operations

<a name="make_bucket"></a>

### make_bucket(self, bucket_name, location='us-east-1', object_lock=False)

Create a bucket with region and object lock.

__Parameters__

| Param         | Type   | Description                                 |
|---------------|--------|---------------------------------------------|
| `bucket_name` | _str_  | Name of the bucket.                         |
| `location`    | _str_  | Region in which the bucket will be created. |
| `object_lock` | _bool_ | Flag to set object-lock feature.            |

__Example__

```py
minio.make_bucket('foo')
minio.make_bucket('foo', 'us-west-1')
minio.make_bucket('foo', 'us-west-1', object_lock=True)
```

<a name="list_buckets"></a>

### list_buckets()

List information of all accessible buckets.

__Parameters__

| Return                                   |
|:-----------------------------------------|
| An iterator contains bucket information. |

__Example__

```py
bucket_list = minio.list_buckets()
for bucket in bucket_list:
    print(bucket.name, bucket.creation_date)
```

<a name="bucket_exists"></a>

### bucket_exists(bucket_name)

Check if a bucket exists.

__Parameters__

| Param         | Type  | Description         |
|:--------------|:------|:--------------------|
| `bucket_name` | _str_ | Name of the bucket. |

__Example__

```py
found = minio.bucket_exists("my-bucketname")
if found:
    print("my-bucketname exists")
else:
    print("my-bucketname does not exist")
```

<a name="remove_bucket"></a>

### remove_bucket(bucket_name)

Remove an empty bucket.

__Parameters__

| Param         | Type  | Description         |
|:--------------|:------|:--------------------|
| `bucket_name` | _str_ | Name of the bucket. |

__Example__

```py
minio.remove_bucket("my-bucketname")
```

<a name="list_objects"></a>

### list_objects(bucket_name, prefix=None, recursive=False, include_version=False)

Lists object information of a bucket using S3 API version 1, optionally for prefix recursively.

__Parameters__

| Param             | Type   | Description                                          |
|:------------------|:-------|:-----------------------------------------------------|
| `bucket_name`     | _str_  | Name of the bucket.                                  |
| `prefix`          | _str_  | Object name starts with prefix.                      |
| `recursive`       | _bool_ | List recursively than directory structure emulation. |
| `include_version` | _bool_ | Flag to control whether include object versions.     |

__Return Value__

| Return                                                    |
|:----------------------------------------------------------|
| An iterator contains object information as _minio.Object_ |

__Example__

```py
# List objects information.
objects = minio.list_objects('foo')
for object in objects:
    print(object)

# List objects information whose names starts with 'hello/'.
objects = minio.list_objects('foo', prefix='hello/')
for object in objects:
    print(object)

# List objects information recursively.
objects = minio.list_objects('foo', recursive=True)
for object in objects:
    print(object)

# List objects information recursively whose names starts with
# 'hello/'.
objects = minio.list_objects(
    'foo', prefix='hello/', recursive=True,
)
for object in objects:
    print(object)

# List objects information recursively after object name
# 'hello/world/1'.
objects = minio.list_objects(
    'foo', recursive=True, start_after='hello/world/1',
)
for object in objects:
    print(object)
```

<a name="get_bucket_policy"></a>

### get_bucket_policy(bucket_name)

Get bucket policy configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

__Return Value__

| Param                                       |
|:--------------------------------------------|
| Bucket policy configuration as JSON string. |

__Example__

```py
config = minio.get_bucket_policy("my-bucketname")
```

<a name="set_bucket_policy"></a>

### set_bucket_policy(bucket_name, policy)

Set bucket policy configuration to a bucket.

__Parameters__

| Param           | Type  | Description                                 |
|:----------------|:------|:--------------------------------------------|
| ``bucket_name`` | _str_ | Name of the bucket.                         |
| ``Policy``      | _str_ | Bucket policy configuration as JSON string. |

__Example__

```py
minio.set_bucket_policy("my-bucketname", config)
```

<a name="delete_bucket_policy"></a>

### delete_bucket_policy(bucket_name)

Delete bucket policy configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

__Example__

```py
minio.delete_bucket_policy("my-bucketname")
```

<a name="get_bucket_notification"></a>

### get_bucket_notification(bucket_name)

Get notification configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

__Return Value__

| Param                        |
|:-----------------------------|
| _NotificationConfig_ object. |

__Example__

```py
config = minio.get_bucket_notification("my-bucketname")
```

<a name="set_bucket_notification"></a>

### set_bucket_notification(bucket_name, config)

Set notification configuration of a bucket.

__Parameters__

| Param           | Type                 | Description                 |
|:----------------|:---------------------|:----------------------------|
| ``bucket_name`` | _str_                | Name of the bucket.         |
| ``config``      | _NotificationConfig_ | Notification configuration. |

__Example__

```py
config = NotificationConfig(
    queue_config_list=[
        QueueConfig(
            "QUEUE-ARN-OF-THIS-BUCKET",
            ['s3:ObjectCreated:*'],
            config_id="1",
            prefix_filter_rule=PrefixFilterRule("abc"),
        ),
    ],
)
minio.set_bucket_notification("my-bucketname", config)
```

<a name="delete_bucket_notification"></a>

### delete_bucket_notification(bucket_name)

Delete notification configuration of a bucket. On success, S3 service stops notification of events previously set of the bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

__Example__

```py
minio.delete_bucket_notification("my-bucketname")
```

<a name="listen_bucket_notification"></a>

### listen_bucket_notification(bucket_name, prefix='', suffix='', events=('s3:ObjectCreated:*', 's3:ObjectRemoved:*', 's3:ObjectAccessed:*'))

Listen events of object prefix and suffix of a bucket. Caller should iterate returned iterator to read new events.

__Parameters__

| Param         | Type   | Description                                 |
|:--------------|:-------|:--------------------------------------------|
| `bucket_name` | _str_  | Name of the bucket.                         |
| `prefix`      | _str_  | Listen events of object starts with prefix. |
| `suffix`      | _str_  | Listen events of object ends with suffix.   |
| `events`      | _list_ | Events to listen.                           |

```py
iter = minio.listen_bucket_notification(
    "my-bucketname",
    events=('s3:ObjectCreated:*', 's3:ObjectAccessed:*'),
)
for events in iter:
    print(events)
```

<a name="get_bucket_encryption"></a>

### get_bucket_encryption(bucket_name)

Get encryption configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

__Return Value__

| Param               |
|:--------------------|
| _SSEConfig_ object. |

__Example__

```py
config = minio.get_bucket_encryption("my-bucketname")
```

<a name="set_bucket_encryption"></a>

### set_bucket_encryption(bucket_name, config)

Set encryption configuration of a bucket.

__Parameters__

| Param           | Type        | Description                           |
|:----------------|:------------|:--------------------------------------|
| ``bucket_name`` | _str_       | Name of the bucket.                   |
| ``config``      | _SSEConfig_ | Server-side encryption configuration. |

__Example__

```py
minio.set_bucket_encryption(
    "my-bucketname", SSEConfig(Rule.new_sse_s3_rule()),
)
```

<a name="delete_bucket_encryption"></a>

### delete_bucket_encryption(bucket_name)

Delete encryption configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

__Example__

```py
minio.delete_bucket_encryption("my-bucketname")
```

<a name="get_bucket_versioning"></a>

### get_bucket_versioning(bucket_name)

Get versioning configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

__Example__

```py
config = minio.get_bucket_versioning("my-bucketname")
print(config.status)
```

<a name="set_bucket_versioning"></a>

### set_bucket_versioning(bucket_name, config)

Set versioning configuration to a bucket.

__Parameters__

| Param           | Type               | Description               |
|:----------------|:-------------------|:--------------------------|
| ``bucket_name`` | _str_              | Name of the bucket.       |
| ``config``      | _VersioningConfig_ | Versioning configuration. |

__Example__

```py
minio.set_bucket_versioning("my-bucketname", VersioningConfig(ENABLED))
```

<a name="delete_bucket_replication"></a>

### delete_bucket_replication(bucket_name)

Delete replication configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

__Example__

```py
minio.delete_bucket_replication("my-bucketname")
```

<a name="get_bucket_replication"></a>

### get_bucket_replication(bucket_name)

Get replication configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

| Return                                  |
|:----------------------------------------|
| _ReplicationConfig_ object. |

__Example__

```py
config = minio.get_bucket_replication("my-bucketname")
```

<a name="set_bucket_replication"></a>

### set_bucket_replication(bucket_name, config)

Set replication configuration to a bucket.

__Parameters__

| Param           | Type                | Description                |
|:----------------|:--------------------|:---------------------------|
| ``bucket_name`` | _str_               | Name of the bucket.        |
| ``config``      | _ReplicationConfig_ | Replication configuration. |

__Example__

```py
config = ReplicationConfig(
    "REPLACE-WITH-ACTUAL-ROLE",
    [
        Rule(
            Destination(
                "REPLACE-WITH-ACTUAL-DESTINATION-BUCKET-ARN",
            ),
            ENABLED,
            delete_marker_replication=DeleteMarkerReplication(
                DISABLED,
            ),
            rule_filter=Filter(
                AndOperator(
                    "TaxDocs",
                    {"key1": "value1", "key2": "value2"},
                ),
            ),
            rule_id="rule1",
            priority=1,
        ),
    ],
)
minio.set_bucket_replication("my-bucketname", config)
```

<a name="delete_bucket_lifecycle"></a>

### delete_bucket_lifecycle(bucket_name)

Delete lifecycle configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

__Example__

```py
minio.delete_bucket_lifecycle("my-bucketname")
```

<a name="get_bucket_lifecycle"></a>

### get_bucket_lifecycle(bucket_name)

Get lifecycle configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

| Return                    |
|:--------------------------|
| _LifecycleConfig_ object. |


__Example__

```py
config = minio.get_bucket_lifecycle("my-bucketname")
```

<a name="set_bucket_lifecycle"></a>

### set_bucket_lifecycle(bucket_name, config)

Set lifecycle configuration to a bucket.

__Parameters__

| Param           | Type              | Description              |
|:----------------|:------------------|:-------------------------|
| ``bucket_name`` | _str_             | Name of the bucket.      |
| ``config``      | _LifecycleConfig_ | Lifecycle configuration. |

__Example__

```py
config = LifecycleConfig(
    [
        Rule(
            ENABLED,
            rule_filter=Filter(prefix="documents/"),
            rule_id="rule1",
            transition=Transition(days=30, storage_class="GLACIER"),
        ),
        Rule(
            ENABLED,
            rule_filter=Filter(prefix="logs/"),
            rule_id="rule2",
            expiration=Expiration(days=365),
        ),
    ],
)
minio.set_bucket_lifecycle("my-bucketname", config)
```

<a name="delete_bucket_tags"></a>

### delete_bucket_tags(bucket_name)

Delete tags configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

__Example__

```py
minio.delete_bucket_tags("my-bucketname")
```

<a name="get_bucket_tags"></a>

### get_bucket_tags(bucket_name)

Get tags configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

| Return         |
|:---------------|
| _Tags_ object. |

__Example__

```py
tags = minio.get_bucket_tags("my-bucketname")
```

<a name="set_bucket_tags"></a>

### set_bucket_tags(bucket_name, tags)

Set tags configuration to a bucket.

__Parameters__

| Param           | Type   | Description         |
|:----------------|:-------|:--------------------|
| ``bucket_name`` | _str_  | Name of the bucket. |
| ``tags``        | _Tags_ | Tags configuration. |

__Example__

```py
tags = Tags.new_bucket_tags()
tags["Project"] = "Project One"
tags["User"] = "jsmith"
client.set_bucket_tags("my-bucketname", tags)
```

<a name="delete_object_lock_config"></a>

### delete_object_lock_config(bucket_name)

Delete object-lock configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

__Example__

```py
minio.delete_object_lock_config("my-bucketname")
```

<a name="get_object_lock_config"></a>

### get_object_lock_config(bucket_name)

Get object-lock configuration of a bucket.

__Parameters__

| Param           | Type  | Description         |
|:----------------|:------|:--------------------|
| ``bucket_name`` | _str_ | Name of the bucket. |

| Return                     |
|:---------------------------|
| _ObjectLockConfig_ object. |

__Example__

```py
config = minio.get_object_lock_config("my-bucketname")
```

<a name="set_object_lock_config"></a>

### set_object_lock_config(bucket_name, config)

Set object-lock configuration to a bucket.

__Parameters__

| Param           | Type               | Description                |
|:----------------|:-------------------|:---------------------------|
| ``bucket_name`` | _str_              | Name of the bucket.        |
| ``config``      | _ObjectLockConfig_ | Object-Lock configuration. |

__Example__

```py
config = ObjectLockConfig(GOVERNANCE, 15, DAYS)
minio.set_object_lock_config("my-bucketname", config)
```

## 3. Object operations

<a name="get_object"></a>

### get_object(bucket_name, object_name, offset=0, length=0, request_headers=None, ssec=None, version_id=None, extra_query_params=None)

Gets data from offset to length of an object. Returned response should be closed after use to release network resources. To reuse the connection, it's required to call `response.release_conn()` explicitly.

__Parameters__

| Param                | Type             | Description                                          |
|:---------------------|:-----------------|:-----------------------------------------------------|
| `bucket_name`        | _str_            | Name of the bucket.                                  |
| `object_name`        | _str_            | Object name in the bucket.                           |
| `offset`             | _int_            | Start byte position of object data.                  |
| `length`             | _int_            | Number of bytes of object data from offset.          |
| `request_headers`    | _dict_           | Any additional headers to be added with GET request. |
| `ssec`               | _SseCustomerKey_ | Server-side encryption customer key.                 |
| `version_id`         | _str_            | Version-ID of the object.                            |
| `extra_query_params` | _dict_           | Extra query parameters for advanced usage.           |

__Return Value__

| Return                                  |
|:----------------------------------------|
| _urllib3.response.HTTPResponse_ object. |

__Example__

```py
// Get entire object data.
 try:
    response = minio.get_object('foo', 'bar')
    // Read data from response.
finally:
    response.close()
    response.release_conn()

// Get object data for offset/length.
try:
    response = minio.get_object('foo', 'bar', 2, 4)
    // Read data from response.
finally:
    response.close()
    response.release_conn()
```

<a name="select_object_content"></a>

### select_object_content(bucket_name, object_name, opts)

Select content of an object by SQL expression.

__Parameters__

| Param         | Type            | Description                |
|:--------------|:----------------|:---------------------------|
| `bucket_name` | _str_           | Name of the bucket.        |
| `object_name` | _str_           | Object name in the bucket. |
| `request`     | _SelectRequest_ | Select request.            |

__Return Value__

| Return                                                                               |
|:-------------------------------------------------------------------------------------|
| A reader contains requested records and progress information as _SelectObjectReader_ |

__Example__

```py
request = SelectRequest(
    "select * from s3object",
    CSVInputSerialization(),
    CSVOutputSerialization(),
    request_progress=True,
)
data = client.select_object_content('my-bucket', 'my-object', request)
with open('my-record-file', 'w') as record_data:
    for d in data.stream(10*1024):
        record_data.write(d)
    # Get the stats
    print(data.stats())
```

<a name="fget_object"></a>

### fget_object(bucket_name, object_name, file_path, request_headers=None, ssec=None, version_id=None, extra_query_params=None)

Downloads data of an object to file.

__Parameters__

| Param                | Type             | Description                                          |
|:---------------------|:-----------------|:-----------------------------------------------------|
| `bucket_name`        | _str_            | Name of the bucket.                                  |
| `object_name`        | _str_            | Object name in the bucket.                           |
| `file_path`          | _str_            | Name of file to download.                            |
| `request_headers`    | _dict_           | Any additional headers to be added with GET request. |
| `ssec`               | _SseCustomerKey_ | Server-side encryption customer key.                 |
| `version_id`         | _str_            | Version-ID of the object.                            |
| `extra_query_params` | _dict_           | Extra query parameters for advanced usage.           |

__Return Value__

| Return                         |
|:-------------------------------|
| Object information as _Object_ |

__Example__

```py
minio.fget_object('foo', 'bar', 'localfile')
minio.fget_object(
    'foo', 'bar', 'localfile', version_id='VERSION-ID',
)
```

<a name="copy_object"></a>

### copy_object(bucket_name, object_name, object_source, conditions=None, source_sse=None, sse=None, metadata=None)

Create an object by server-side copying data from another object. In this API maximum supported source object size is 5GiB.

__Parameters__

| Param           | Type             | Description                                                           |
|:----------------|:-----------------|:----------------------------------------------------------------------|
| `bucket_name`   | _str_            | Name of the bucket.                                                   |
| `object_name`   | _str_            | Object name in the bucket.                                            |
| `object_source` | _str_            | Source object to be copied.                                           |
| `conditions`    | _CopyConditions_ | Collection of supported CopyObject conditions.                        |
| `source_sse`    | _SseCustomerKey_ | Server-side encryption customer key of source object.                 |
| `sse`           | _Sse_            | Server-side encryption of destination object.                         |
| `metadata`      | _dict_           | Any user-defined metadata to be copied along with destination object. |

__Return Value__

| Return                      |
|:----------------------------|
| _ObjectWriteResult_ object. |

__Example__

```py
import time
from datetime import datetime
from minio import CopyConditions

minio.copy_object(
    "my-bucketname",
    "my-objectname",
    "my-source-bucketname/my-source-objectname",
)

minio.copy_object(
    "my-bucketname",
    "my-objectname",
    "my-source-bucketname/my-source-objectname"
    "?versionId=b6602757-7c9c-449b-937f-fed504d04c94",
)

copy_conditions = CopyConditions()
# Set modified condition, copy object modified since 2014 April.
t = (2014, 4, 0, 0, 0, 0, 0, 0, 0)
mod_since = datetime.utcfromtimestamp(time.mktime(t))
copy_conditions.set_modified_since(mod_since)

# Set unmodified condition, copy object unmodified since 2014 April.
copy_conditions.set_unmodified_since(mod_since)

# Set matching ETag condition, copy object which matches the following ETag.
copy_conditions.set_match_etag("31624deb84149d2f8ef9c385918b653a")

# Set matching ETag except condition, copy object which does not match the following ETag.
copy_conditions.set_match_etag_except("31624deb84149d2f8ef9c385918b653a")

# Set metadata, which will be copied along with the destination object.
metadata = {"test-key": "test-data"}

result = minioClient.copy_object(
	"my-bucketname",
	"my-objectname",
	"my-source-bucketname/my-source-objectname",
	copy_conditions,metadata=metadata,
)
print(result.object_name, result.version_id)
```

<a name="put_object"></a>

### put_object(bucket_name, object_name, data, length, content_type='application/octet-stream', metadata=None, sse=None, progress=None, part_size=DEFAULT_PART_SIZE)

Uploads data from a stream to an object in a bucket.

__Parameters__

| Param          | Type           | Description                                                         |
|:---------------|:---------------|:--------------------------------------------------------------------|
| `bucket_name`  | _str_          | Name of the bucket.                                                 |
| `object_name`  | _str_          | Object name in the bucket.                                          |
| `data`         | _io.RawIOBase_ | Contains object data.                                               |
| `content_type` | _str_          | Content type of the object.                                         |
| `metadata`     | _dict_         | Any additional metadata to be uploaded along with your PUT request. |
| `sse`          | _Sse_          | Server-side encryption.                                             |
| `progress`     | _threading_    | A progress object.                                                  |
| `part_size`    | _int_          | Multipart part size.                                                |

__Return Value__

| Return                            |
|:----------------------------------|
| etag and version ID if available. |

__Example__
```py
file_stat = os.stat('hello.txt')
with open('hello.txt', 'rb') as data:
    minio.put_object(
        'foo', 'bar', data, file_stat.st_size, 'text/plain',
    )
```

<a name="fput_object"></a>

### fput_object(bucket_name, object_name, file_path, content_type='application/octet-stream', metadata=None, sse=None, progress=None, part_size=DEFAULT_PART_SIZE)

Uploads data from a file to an object in a bucket.

| Param          | Type        | Description                                                         |
|:---------------|:------------|:--------------------------------------------------------------------|
| `bucket_name`  | _str_       | Name of the bucket.                                                 |
| `object_name`  | _str_       | Object name in the bucket.                                          |
| `file_path`    | _str_       | Name of file to upload.                                             |
| `content_type` | _str_       | Content type of the object.                                         |
| `metadata`     | _dict_      | Any additional metadata to be uploaded along with your PUT request. |
| `sse`          | _Sse_       | Server-side encryption.                                             |
| `progress`     | _threading_ | A progress object.                                                  |
| `part_size`    | _int_       | Multipart part size.                                                |

__Return Value__

| Return                            |
|:----------------------------------|
| etag and version ID if available. |

__Example__

```py
minio.fput_object('foo', 'bar', 'filepath', 'text/plain')
```

<a name="stat_object"></a>

### stat_object(bucket_name, object_name, ssec=None, version_id=None, extra_query_params=None)

Get object information and metadata of an object.

__Parameters__

| Param                | Type             | Description                                |
|:---------------------|:-----------------|:-------------------------------------------|
| `bucket_name`        | _str_            | Name of the bucket.                        |
| `object_name`        | _str_            | Object name in the bucket.                 |
| `ssec`               | _SseCustomerKey_ | Server-side encryption customer key.       |
| `version_id`         | _str_            | Version ID of the object.                  |
| `extra_query_params` | _dict_           | Extra query parameters for advanced usage. |

__Return Value__

| Return   |
|:---------|
| Object information as _Object_ |

__Example__

```py
stat = minio.stat_object("my-bucketname", "my-objectname")
```

<a name="remove_object"></a>

### remove_object(bucket_name, object_name, version_id=None)

Remove an object.

__Parameters__

| Param         | Type  | Description                |
|:--------------|:------|:---------------------------|
| `bucket_name` | _str_ | Name of the bucket.        |
| `object_name` | _str_ | Object name in the bucket. |
| `version_id`  | _str_ | Version ID of the object.  |

__Example__

```py
minio.remove_object("my-bucketname", "my-objectname")
minio.remove_object(
    "my-bucketname",
    "my-objectname",
    version_id="13f88b18-8dcd-4c83-88f2-8631fdb6250c",
)
```

<a name="remove_objects"></a>

### remove_objects(bucket_name, delete_object_list, bypass_governance_mode=False)

Remove multiple objects.

__Parameters__

| Param                    | Type       | Description                                                         |
|:-------------------------|:-----------|:--------------------------------------------------------------------|
| `bucket_name`            | _str_      | Name of the bucket.                                                 |
| `delete_object_list`     | _iterable_ | An iterable containing :class:`DeleteObject <DeleteObject>` object. |
| `bypass_governance_mode` | _bool_     | Bypass Governance retention mode.                                   |

__Return Value__

| Return                                                           |
|:-----------------------------------------------------------------|
| An iterator containing :class:`DeleteError <DeleteError>` object |

__Example__

```py
errors = minio.remove_objects(
    "my-bucketname",
    [
        DeleteObject("my-objectname1"),
        DeleteObject("my-objectname2"),
        DeleteObject(
            "my-objectname3",
            "13f88b18-8dcd-4c83-88f2-8631fdb6250c",
        ),
    ],
)
for error in errors:
    print("error occured when deleting object", error)
```

<a name="delete_object_tags"></a>

### delete_object_tags(bucket_name, object_name, version_id=None)

Delete tags configuration of an object.

__Parameters__

| Param           | Type  | Description                |
|:----------------|:------|:---------------------------|
| ``bucket_name`` | _str_ | Name of the bucket.        |
| ``object_name`` | _str_ | Object name in the bucket. |
| ``version_id``  | _str_ | Version ID of the object.  |

__Example__

```py
minio.delete_object_tags("my-bucketname", "my-objectname")
```

<a name="get_object_tags"></a>

### get_object_tags(bucket_name, object_name, version_id=None)

Get tags configuration of an object.

__Parameters__

| Param           | Type  | Description                |
|:----------------|:------|:---------------------------|
| ``bucket_name`` | _str_ | Name of the bucket.        |
| ``object_name`` | _str_ | Object name in the bucket. |
| ``version_id``  | _str_ | Version ID of the object.  |

| Return         |
|:---------------|
| _Tags_ object. |

__Example__

```py
tags = minio.get_object_tags("my-bucketname", "my-objectname")
```

<a name="set_object_tags"></a>

### set_object_tags(bucket_name, object_name, tags, version_id=None)

Set tags configuration to an object.

__Parameters__

| Param           | Type   | Description                |
|:----------------|:-------|:---------------------------|
| ``bucket_name`` | _str_  | Name of the bucket.        |
| ``object_name`` | _str_  | Object name in the bucket. |
| ``tags``        | _Tags_ | Tags configuration.        |
| ``version_id``  | _str_  | Version ID of the object.  |

__Example__

```py
tags = Tags.new_object_tags()
tags["Project"] = "Project One"
tags["User"] = "jsmith"
client.set_object_tags("my-bucketname", "my-objectname", tags)
```

<a name="enable_object_legal_hold"></a>

### enable_object_legal_hold(bucket_name, object_name, version_id=None)

Enable legal hold on an object.

__Parameters__

| Param         | Type  | Description                |
|:--------------|:------|:---------------------------|
| `bucket_name` | _str_ | Name of the bucket.        |
| `object_name` | _str_ | Object name in the bucket. |
| `version_id`  | _str_ | Version ID of the object.  |

__Example__

```py
minio.enable_object_legal_hold("my-bucketname", "my-objectname")
```

<a name="disable_object_legal_hold"></a>

### disable_object_legal_hold(bucket_name, object_name, version_id=None)

Disable legal hold on an object.

__Parameters__

| Param         | Type  | Description                |
|:--------------|:------|:---------------------------|
| `bucket_name` | _str_ | Name of the bucket.        |
| `object_name` | _str_ | Object name in the bucket. |
| `version_id`  | _str_ | Version ID of the object.  |

__Example__

```py
minio.disable_object_legal_hold("my-bucketname", "my-objectname")
```

<a name="is_object_legal_hold_enabled"></a>

### is_object_legal_hold_enabled(bucket_name, object_name, version_id=None)

Returns true if legal hold is enabled on an object.

__Parameters__

| Param         | Type  | Description                |
|:--------------|:------|:---------------------------|
| `bucket_name` | _str_ | Name of the bucket.        |
| `object_name` | _str_ | Object name in the bucket. |
| `version_id`  | _str_ | Version ID of the object.  |

__Example__

```py
minio.is_object_legal_hold_enabled("my-bucketname", "my-objectname")
```

<a name="get_object_retention"></a>

### get_object_retention(bucket_name, object_name, version_id=None)

Get retention information of an object.

__Parameters__

| Param                | Type             | Description                                |
|:---------------------|:-----------------|:-------------------------------------------|
| `bucket_name`        | _str_            | Name of the bucket.                        |
| `object_name`        | _str_            | Object name in the bucket.                 |
| `version_id`         | _str_            | Version ID of the object.                  |

__Return Value__

| Return             |
|:-------------------|
| _Retention_ object |


__Example__

```py
config = minio.get_object_retention("my-bucketname", "my-objectname")
```

<a name="set_object_retention"></a>

### set_object_retention(bucket_name, object_name, config, version_id=None)

Set retention information to an object.

__Parameters__

| Param         | Type        | Description                |
|:--------------|:------------|:---------------------------|
| `bucket_name` | _str_       | Name of the bucket.        |
| `object_name` | _str_       | Object name in the bucket. |
| `config`      | _Retention_ | Retention configuration.   |
| `version_id`  | _str_       | Version ID of the object.  |

__Example__

```py
config = Retention(GOVERNANCE, datetime.utcnow() + timedelta(days=10))
minio.set_object_retention("my-bucketname", "my-objectname", config)
```

<a name="presigned_get_object"></a>

### presigned_get_object(bucket_name, object_name, expires=timedelta(days=7), response_headers=None, request_date=None, version_id=None, extra_query_params=None)

Get presigned URL of an object to download its data with expiry time and custom request parameters.

__Parameters__

| Param                | Type                 | Description                                                                                                          |
|:---------------------|:---------------------|:---------------------------------------------------------------------------------------------------------------------|
| `bucket_name`        | _str_                | Name of the bucket.                                                                                                  |
| `object_name`        | _str_                | Object name in the bucket.                                                                                           |
| `expires`            | _datetime.timedelta_ | Expiry in seconds; defaults to 7 days.                                                                               |
| `response_headers`   | _dict_               | Optional response_headers argument to specify response fields like date, size, type of file, data about server, etc. |
| `request_date`       | _datetime.datetime_  | Optional request_date argument to specify a different request date. Default is current date.                         |
| `version_id`         | _str_                | Version ID of the object.                                                                                            |
| `extra_query_params` | _dict_               | Extra query parameters for advanced usage.                                                                           |

__Return Value__

| Return     |
|:-----------|
| URL string |

__Example__

```py
# Get presigned URL string to download 'my-objectname' in
# 'my-bucketname' with default expiry.
url = minio.presigned_get_object("my-bucketname", "my-objectname")
print(url)

# Get presigned URL string to download 'my-objectname' in
# 'my-bucketname' with two hours expiry.
url = minio.presigned_get_object(
    "my-bucketname", "my-objectname", expires=timedelta(hours=2),
)
print(url)
```

<a name="presigned_put_object"></a>

### presigned_put_object(bucket_name, object_name, expires=timedelta(days=7))

Get presigned URL of an object to upload data with expiry time and custom request parameters.

__Parameters__

| Param         | Type                 | Description                            |
|:--------------|:---------------------|:---------------------------------------|
| `bucket_name` | _str_                | Name of the bucket.                    |
| `object_name` | _str_                | Object name in the bucket.             |
| `expires`     | _datetime.timedelta_ | Expiry in seconds; defaults to 7 days. |

__Return Value__

| Return     |
|:-----------|
| URL string |

__Example__

```py
# Get presigned URL string to upload data to 'my-objectname' in
# 'my-bucketname' with default expiry.
url = minio.presigned_put_object("my-bucketname", "my-objectname")
print(url)

# Get presigned URL string to upload data to 'my-objectname' in
# 'my-bucketname' with two hours expiry.
url = minio.presigned_put_object(
    "my-bucketname", "my-objectname", expires=timedelta(hours=2),
)
print(url)
```

<a name="presigned_post_policy"></a>

### presigned_post_policy(policy)

Get form-data of PostPolicy of an object to upload its data using POST method.

__Parameters__

| Param    | Type         | Description  |
|:---------|:-------------|:-------------|
| `policy` | _PostPolicy_ | Post policy. |

__Return Value__

| Return                      |
|:----------------------------|
| Form-data containing _dict_ |

__Example__

```py
policy = PostPolicy(
    "my-bucket", datetime.utcnow() + timedelta(days=10),
)
policy.add_starts_with_condition("key", "my/object/prefix/")
policy.add_content_length_range_condition(
    1*1024*1024, 10*1024*1024,
)
form_data = client.presigned_post_policy(policy)
```

## 5. Explore Further

- [MinIO Golang Client SDK Quickstart Guide](https://docs.min.io/docs/golang-client-quickstart-guide)
- [MinIO Java Client SDK Quickstart Guide](https://docs.min.io/docs/java-client-quickstart-guide)
- [MinIO JavaScript Client SDK Quickstart Guide](https://docs.min.io/docs/javascript-client-quickstart-guide)
