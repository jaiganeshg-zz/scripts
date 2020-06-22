# Aggregations
 two main concepts:

Buckets
Collections of documents that meet a criterion

Metrics
Statistics calculated on the documents in a bucket

## Buckets
A bucket is simply a collection of documents that meet a certain criteria:

An employee would land in either the male or female bucket.

The city of Albany would land in the New York state bucket.

The date 2014-10-28 would land within the October bucket.

As aggregations are executed, the values inside each document are evaluated to determine whether they match a bucket’s criteria. If they match, the document is placed inside the bucket and the aggregation continues.

Buckets can also be nested inside other buckets, giving you a hierarchy or conditional partitioning scheme. For example, Cincinnati would be placed inside the Ohio state bucket, and the entire Ohio bucket would be placed inside the USA country bucket.

Elasticsearch has a variety of buckets, which allow you to partition documents in many ways (by hour, by most-popular terms, by age ranges, by geographical location, and more). But fundamentally they all operate on the same principle: partitioning documents based on a criteria.

## Metrics
Buckets allow us to partition documents into useful subsets, but ultimately what we want is some kind of metric calculated on those documents in each bucket. Bucketing is the means to an end: it provides a way to group documents in a way that you can calculate interesting metrics.

Most metrics are simple mathematical operations (for example, min, mean, max, and sum) that are calculated using the document values. In practical terms, metrics allow you to calculate quantities such as the average salary, or the maximum sale price, or the 95th percentile for query latency.


```
POST /transactions/_bulk
{ "index": {}}
{ "price" : 10000, "color" : "red", "make" : "honda", "sold" : "2014-10-28" }
{ "index": {}}
{ "price" : 20000, "color" : "red", "make" : "honda", "sold" : "2014-11-05" }
{ "index": {}}
{ "price" : 30000, "color" : "green", "make" : "ford", "sold" : "2014-05-18" }
{ "index": {}}
{ "price" : 15000, "color" : "blue", "make" : "toyota", "sold" : "2014-07-02" }
{ "index": {}}
{ "price" : 12000, "color" : "green", "make" : "toyota", "sold" : "2014-08-19" }
{ "index": {}}
{ "price" : 20000, "color" : "red", "make" : "honda", "sold" : "2014-11-05" }
{ "index": {}}
{ "price" : 80000, "color" : "red", "make" : "bmw", "sold" : "2014-01-01" }
{ "index": {}}
{ "price" : 25000, "color" : "blue", "make" : "ford", "sold" : "2014-02-12" }

POST /transactions/_search

POST /transactions/_search
{
  "size": 0,
  "aggs": {
    "colors": {
      "terms": {
        "field": "color.keyword"
      },
      "aggs": {
        "avg_price": {
          "avg": {
            "field": "price"
          }
        },
        "count": {
          "value_count": {
            "field": "make.keyword"
          }
        },
        "make": {
          "terms": {
            "field": "make.keyword"
          }
        }
      }
    }
  }
}

POST /transactions/_search
{
  "size": 0,
  "aggs": {
    "average": {
      "avg": {
        "field": "price"
      }
    }
  }
}

POST /transactions/_search
{
  "size": 0,
  "aggs": {
    "average": {
      "avg": {
        "field": "price"
      }
    },
    "makes": {
      "terms": {
        "field": "make.keyword"
      },
      "aggs": {
        "avg_price": {
          "avg": {
            "field": "price"
          }
        }
      }
    }
  }
}

POST /transactions/_search
{
  "size": 0,
  "aggs": {
    "colors": {
      "terms": {
        "field": "color.keyword"
      },
      "aggs": {
        "avg_price": {
          "avg": {
            "field": "price"
          }
        },
        "make": {
          "terms": {
            "field": "make.keyword"
          },
          "aggs": {
            "min_price": {
              "min": {
                "field": "price"
              }
            },
            "max_price": {
              "max": {
                "field": "price"
              }
            }
          }
        }
      }
    }
  }
}

```

## histogram and bar charts

```
GET /transactions/_search
{
  "size": 0, 
   "aggs":{
      "price":{
         "histogram":{
            "field": "price",
            "interval": 20000
         },
         "aggs":{
            "revenue": {
               "sum": {
                 "field" : "price"
               }
             }
         }
      }
   }
}

GET /transactions/_search
{
  "size": 0, 
  "aggs": {
    "makes": {
      "terms": {
        "field": "make.keyword",
        "size": 10
      },
      "aggs": {
        "stats": {
          "extended_stats": {
            "field": "price"
          }
        }
      }
    }
  }
}
```

##  Looking at Time

```
GET /transactions/_search
{
   "aggs": {
      "sales": {
         "date_histogram": {
            "field": "sold",
            "interval": "month",
            "format": "yyyy-MM-dd"
         }
      }
   }
}

```

## Scoping
```
GET /transactions/_search
{
  "size": 0, 
  "query": {
    "match": {
      "make.keyword": "ford"
    }
  },
  "aggs": {
    "colors": {
      "terms": {
        "field": "color.keyword"
      }
    }
  }
}
```


