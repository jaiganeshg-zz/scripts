# Sorting
By default, results are returned sorted by relevance—with the most relevant docs first

In order to sort by relevance, we need to represent relevance as a value. In Elasticsearch, the relevance score is represented by the floating-point number returned in the search results as the _score, so the default sort order is _score descending.

Below query doesn't provide relevance score
```
GET /tweet/_search
{
 "query": {
   "term": {
     "user_id": 2
   }
 }
}
```

### Sorting by Field Values

```
GET /tweet/_search
{
 "query": {
   "term": {
     "user_id": 2
   }
 },
 "sort": [
   {
     "date": {
       "order": "asc"
     }
   },
   {
     "tweet.keyword": {
       "order": "asc"
     }
   }
 ]
}
```

### Multilevel Sorting
```
GET /tweet/_search
{
    "query" : {
      "bool": {
        "must": [
          {"match": {
            "tweet": "elasticsearch"
          }},
          {
            "term": {
              "user_id": 2
            }
          }
        ]
      }
    },
    "sort": [
        { "date":   { "order": "desc" }},
        { "_score": { "order": "desc" }}
    ]
}
```

### What Is Relevance?
The relevance score of each document is represented by a positive floating-point number called the _score. The higher the _score, the more relevant the document.
- Term frequency:
How often does the term appear in the field? The more often, the more relevant. A field containing five mentions of the same term is more likely to be relevant than a field containing just one mention.

- Inverse document frequency:
How often does each term appear in the index? The more often, the less relevant. Terms that appear in many documents have a lower weight than more-uncommon terms.

- Field-length norm:
How long is the field? The longer it is, the less likely it is that words in the field will be relevant. A term appearing in a short title field carries more weight than the same term appearing in a long content field.


