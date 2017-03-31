# Elasticsearch Fingerprint Ingest Processor

Fingerprint a field using by replacing values with a consistent HMAC function.

## Fingerprint Options
| Name | Required | Default | Description |
|------|----------|---------|-------------|
|`field`|yes|-|The field to fingerprint. The field in the document can be a string or an array.|
|`target_field`|no|`field`-hash|The field to assign the fingerprint value to.|
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
        "field" : "my_field",
        "target_field" : "hash",
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
  "_version": 1,
  "found": true,
  "_source": {
    "my_field": [
      "my_value1",
      "my_value2",
      "my_value3"
    ],
    "hash": [
      "a4b12fddb3fd1389dec57010bd7b8c68c0f613d07115b8a5b1fd166a981c93fa",
      "a9b22cef4d3cbd2dbb80f6209faa637d7d7ef96dbeb06bf10fac47eb23813859",
      "33510269cdd2ac8b819a85cc12cbd1a7948fa5abf695944c0250488334d2ce61"
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
  "_version": 1,
  "found": true,
  "_source": {
    "my_field": "Some content",
    "hash": "575c4b3e2f145755709b94c92973b3263997544cf26b478c5a34cdb12b47bbc8"
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
