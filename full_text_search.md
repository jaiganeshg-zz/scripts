# Full-Text Search
The two most important aspects of full-text search are as follows:

Relevance
The ability to rank results by how relevant they are to the given query, whether relevance is calculated using TF/IDF (see “What Is Relevance?”), proximity to a geolocation, fuzzy similarity, or some other algorithm.

Analysis
The process of converting a block of text into distinct, normalized tokens (see “Analysis and Analyzers”) in order to (a) create an inverted index and (b) query the inverted index.


**Setup for this tutorial**
```
POST /full_text/_bulk
{ "index": { "_id": 1 }}
{ "title": "The quick brown fox" }
{ "index": { "_id": 2 }}
{ "title": "The quick brown fox jumps over the lazy dog" }
{ "index": { "_id": 3 }}
{ "title": "The quick brown fox jumps over the quick dog" }
{ "index": { "_id": 4 }}
{ "title": "Brown fox brown dog" }
```

### A Single-Word Query
```
POST /full_text/_search
{
    "query": {
        "match": {
            "title": "QUICK!"
        }
    }
}
```

### Multiword Queries
```
POST /full_text/_search
{
    "query": {
        "match": {
            "title": "BROWN DOG"
        }
    }
}
```
`Check the relevance score in output`

### Improving Precision
```
POST /full_text/_search
{
  "query": {
    "match": {
      "title": {
        "query": "BROWN DOG",
        "operator": "and"
      }
    }
  }
}
```

### Controlling Precision
```
POST /full_text/_search
{
  "query": {
    "match": {
      "title": {
        "query": "BROWN DOG",
        "minimum_should_match": "75%"
      }
    }
  }
}
```

### Boosting Query Clauses
```
GET /_search
{
    "query": {
        "bool": {
            "must": {
                "match": {  1
                    "content": {
                        "query":    "full text search",
                        "operator": "and"
                    }
                }
            },
            "should": [
                { "match": {
                    "content": {
                        "query": "Elasticsearch",
                        "boost": 3
                    }
                }},
                { "match": {
                    "content": {
                        "query": "Lucene",
                        "boost": 2
                    }
                }}
            ]
        }
    }
}
```




