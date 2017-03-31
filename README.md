# Elasticsearch Fingerprint Ingest Processor

Fingerprint a field using by replacing values with a consistent HMAC function.

## Fingerprint Options
| Name | Required | Default | Description |
|------|----------|---------|-------------|
|`source`|yes|-|The field to fingerprint. The field in the document can be a string or an array.|
|`target`|no|`source`-hash|The field to assign the fingerprint value to.|
|`key`|no|supersecrethere|The key value used for the `hmac`.|
|`method`|no|HmacSHA1|Hmac algorithm to use. See [here](https://docs.oracle.com/javase/8/docs/technotes/guides/security/StandardNames.html#Mac) for a complete list.|

## Usage

```
PUT _ingest/pipeline/fingerprint-pipeline
{
  "description": "A pipeline to do whatever",
  "processors": [
    {
      "fingerprint" : {
        "source" : "my_field",
        "target" : "hash",
        "key"    : "testkey",
        "method" : "HmacSHA256"
      }
    }
  ]
}

PUT /my-index/my-type/1?pipeline=fingerprint-pipeline
{
  "my_field" : ["my_value1", "my_value2","my_value3"]
}

GET /my-index/my-type/1
{
  "_index": "my-index",
  "_type": "my-type",
  "_id": "1",
  "_version": 12,
  "found": true,
  "_source": {
    "my_field": [
      "my_value1",
      "my_value2",
      "my_value3"
    ],
    "hash": [
      "a2e77314d4685a5d2cd21e94ff5a8cb332b55ca761a47eb3356e65afd8348b3",
      "4f0499a1110f023cac3b0624600c81b03a0250fd6caa758199f29f3e7da495b3",
      "7b6ee3bec364e65802d30c04633424c59ffedb68c3b3cb9d9445c74e7e6d35c3"
    ]
  }
}

PUT /my-index/my-type/2?pipeline=fingerprint-pipeline
{
  "my_field" : "Some content"
}

GET /my-index/my-type/2
{
  "_index": "my-index",
  "_type": "my-type",
  "_id": "2",
  "_version": 10,
  "found": true,
  "_source": {
    "my_field": "Some content",
    "hash": "e76f918a5f7c69775512ec8dbe743a1cda17420e48a1d5ab38dec6b08b9cba5b"
  }
}
```

## Configuration
No configuration required

## Setup

In order to install this plugin, you need to create a zip distribution first by running

```bash
gradle clean check
```
This will produce a zip file in `build/distributions`.

After building the zip file, you can install it like this

```bash
bin/plugin install file:///path/to/ingest-fingerprint/build/distribution/ingest-fingerprint-0.0.1-SNAPSHOT.zip
```

**Important**: in order to build the project you have to use gradle version 2.13

In case you can create a gradle-wrapper:
```
gradle wrapper --gradle-version 2.13
```

## Bugs & TODO

* There are always bugs
* and todos...

## Acknowledgements
Thanks to [Alexander Reelsen](https://github.com/spinscale) for the fantastic project
https://github.com/spinscale/cookiecutter-elasticsearch-ingest-processor.

Build from scratch an `Ingestion Plugin` using his project it has been very easy.
