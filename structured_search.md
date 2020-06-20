# Structured Search

## Finding Exact Values

### term Filter with Numbers

**Data for this tutorial**
```
POST /products/_bulk
{ "index": { "_id": 1 }}
{ "price" : 10, "productID" : "XHDK-A-1293-#fJ3" }
{ "index": { "_id": 2 }}
{ "price" : 20, "productID" : "KDKE-B-9947-#kL5" }
{ "index": { "_id": 3 }}
{ "price" : 30, "productID" : "JODL-X-1937-#pV7" }
{ "index": { "_id": 4 }}
{ "price" : 30, "productID" : "QQPX-R-3956-#aD8" }
```
Search by price

```
POST /products/_search
{
  "query": {
    "term": {
      "price": 20
    }
  }
}
```


### term Filter with Text
```
POST /products/_search
{
  "query": {
    "term": {
      "productID.keyword": "JODL-X-1937-#pV7"
    }
  }
}
```

### Combining Filters
```
{
   "bool" : {
      "must" :     [],
      "should" :   [],
      "must_not" : [],
   }
}
```

- must: 
All of these clauses must match. The equivalent of AND.

- must_not: 
All of these clauses must not match. The equivalent of NOT.

- should: 
At least one of these clauses must match. The equivalent of OR.



### Finding Multiple Exact Values
```
{
    "terms" : {
        "price" : [20, 30]
    }
}

{
    "terms" : {
        "tags.keyword" : ["book", "article"]
    }
}
```

### Ranges
```
POST /products/_search
{
  "query": {
    "range": {
      "price": {
        "gte": 10,
        "lte": 20
      }
    }
  }
}
```

### Ranges on Dates

```
"range" : {
    "timestamp" : {
        "gt" : "2014-01-01 00:00:00",
        "lt" : "2014-01-07 00:00:00"
    }
}
```

### Ranges on Strings
```
"range" : {
    "title" : {
        "gte" : "a",
        "lt" :  "b"
    }
}
```

### exists Filter
```
POST /products/_search
{
  "query": {
    "exists": {
      "field": "name"
    }
  }
}
```

### missing Filter
```
POST /products/_search
{
  "query": {
    "bool": {
      "must_not": [
        {
          "exists": {
            "field": "name"
          }
        }
      ]
    }
  }
}
```


