# Multifield Search
## Multiple Query Strings

```
GET /_search
{
  "query": {
    "bool": {
      "should": [
        { "match": { "title":  "War and Peace" }},
        { "match": { "author": "Leo Tolstoy"   }}
      ]
    }
  }

  GET /_search
{
  "query": {
    "bool": {
      "should": [
        { "match": { "title":  "War and Peace" }},
        { "match": { "author": "Leo Tolstoy"   }},
        { "bool":  {
          "should": [
            { "match": { "translator": "Constance Garnett" }},
            { "match": { "translator": "Louise Maude"      }}
          ]
        }}
      ]
    }
  }
}
```

## Prioritizing Clauses
```
GET /_search
{
  "query": {
    "bool": {
      "should": [
        { "match": { 1
            "title":  {
              "query": "War and Peace",
              "boost":
        }}},
        { "match": {
            "author":  {
              "query": "Leo Tolstoy",
              "boost":2
        }}},
        { "bool":  {
            "should": [
              { "match": { "translator": "Constance Garnett" }},
              { "match": { "translator": "Louise Maude"      }}
            ]
        }}
      ]
    }
  }
}
```

## Single Query String




