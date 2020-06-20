# Search

- search lite
- query body search

## Search lite

```
GET /\_all/tweet/\_search?q=tweet:elasticsearch
GET /_search?q=mary
```

## Query body search

### Empty Search

```
GET /_search
{}

Paginated

GET /_search
{
  "from": 30,
  "size": 10
}
```

### Query DSL

```
GET /_search
{
    "query": YOUR_QUERY_HERE
}
```

### Empty search

```
GET /_search
{
    "query": {
        "match_all": {}
    }
}
```

### Search Request

```
GET /_search
{
    "query": {
        "match": {
            "tweet": "elasticsearch"
        }
    }
}
```

### Combining Multiple Clauses

```
{
    "bool": {
        "must":     { "match": { "tweet": "elasticsearch" }},
        "must_not": { "match": { "name":  "mary" }},
        "should":   { "match": { "tweet": "full text" }}
    }
}

{
    "bool": {
        "must": { "match":      { "email": "business opportunity" }},
        "should": [
             { "match":         { "starred": true }},
             { "bool": {
                   "must":      { "folder": "inbox" }},
                   "must_not":  { "spam": true }}
             }}
        ],
        "minimum_should_match": 1
    }
}
```

### Queries and Filters

- A filter asks a yes|no question of every document and is used for fields that contain exact values:

- Is the created date in the range 2013 - 2014?

- Does the status field contain the term published?

- Is the lat_lon field within 10km of a specified point?

- A query is similar to a filter, but also asks the question: How well does this document match?

- A typical use for a query is to find documents

- Best matching the words full text search

- Containing the word run, but maybe also matching runs, running, jog, or sprint

- Containing the words quick, brown, and fox—the closer together they are, the more relevant the document

- Tagged with lucene, search, or java—the more tags, the more relevant the document

### Queries vs Filters Performance Differences

The output from most filter clauses—a simple list of the documents that match the filter—is quick to calculate and easy to cache in memory, using only 1 bit per document. These cached filters can be reused efficiently for subsequent requests.

Queries have to not only find matching documents, but also calculate how relevant each document is, which typically makes queries heavier than filters. Also, query results are not cachable.

Thanks to the inverted index, a simple query that matches just a few documents may perform as well or better than a cached filter that spans millions of documents. In general, however, a cached filter will outperform a query, and will do so consistently.

The goal of filters is to reduce the number of documents that have to be examined by the query.

### When to Use Which

As a general rule, use query clauses for full-text search or for any condition that should affect the relevance score, and use filter clauses for everything else.

### Most Important Queries and Filters

#### Term Filter

The term filter is used to filter by exact values, be they numbers, dates, Booleans, or not_analyzed exact-value string fields:

```
{ "term": { "age": 26 }}
{ "term": { "date": "2014-09-01" }}
{ "term": { "public": true }}
{ "term": { "tag": "full_text" }}

```

#### terms Filter

The terms filter is the same as the term filter, but allows you to specify multiple values to match. If the field contains any of the specified values, the document matches:

```
{ "terms": { "tag": [ "search", "full_text", "nosql" ] }}

```

#### range Filter

The range filter allows you to find numbers or dates that fall into a specified range:

```
{
"range": {
"age": {
"gte": 20,
"lt": 30
}
}
}
```

#### exists and missing Filters

The exists and missing filters are used to find documents in which the specified field either has one or more values (exists) or doesn’t have any values (missing). It is similar in nature to IS_NULL (missing) and NOT IS_NULL (exists)in SQL:

```
{
"exists": {
"field": "title"
}
}
```

These filters are frequently used to apply a condition only if a field is present, and to apply a different condition if it is missing.

#### bool Filter

The bool filter is used to combine multiple filter clauses using Boolean logic. It accepts three parameters:

must
These clauses must match, like and.

must_not
These clauses must not match, like not.

should
At least one of these clauses must match, like or.

Each of these parameters can accept a single filter clause or an array of filter clauses:

```{
"bool": {
"must": { "term": { "folder": "inbox" }},
"must_not": { "term": { "tag": "spam" }},
"should": [
{ "term": { "starred": true }},
{ "term": { "unread": true }}
]
}
}
```

#### match_all Query

The match_all query simply matches all documents. It is the default query that is used if no query has been specified:

```
{ "match_all": {}}
```

This query is frequently used in combination with a filter—for instance, to retrieve all emails in the inbox folder. All documents are considered to be equally relevant, so they all receive a neutral \_score of 1.

#### match Query

The match query should be the standard query that you reach for whenever you want to query for a full-text or exact value in almost any field.

If you run a match query against a full-text field, it will analyze the query string by using the correct analyzer for that field before executing the search:

```
{ "match": { "tweet": "About Search" }}
```

If you use it on a field containing an exact value, such as a number, a date, a Boolean, or a not_analyzed string field, then it will search for that exact value:

```
{ "match": { "age": 26 }}
{ "match": { "date": "2014-09-01" }}
{ "match": { "public": true }}
{ "match": { "tag": "full_text" }}
```

TIP
For exact-value searches, you probably want to use a filter instead of a query, as a filter will be cached.
Unlike the query-string search that we showed in “Search Lite”, the match query does not use a query syntax like +user_id:2 +tweet:search. It just looks for the words that are specified. This means that it is safe to expose to your users via a search field; you control what fields they can query, and it is not prone to throwing syntax errors.

#### multi_match Query

The multi_match query allows to run the same match query on multiple fields:

```
{
"multi_match": {
"query": "full text search",
"fields": [ "title", "body" ]
}
}
```

#### bool Query

The bool query, like the bool filter, is used to combine multiple query clauses. However, there are some differences. Remember that while filters give binary yes/no answers, queries calculate a relevance score instead. The bool query combines the \_score from each must or should clause that matches. This query accepts the following parameters:

must
Clauses that must match for the document to be included.

must_not
Clauses that must not match for the document to be included.

should
If these clauses match, they increase the \_score; otherwise, they have no effect. They are simply used to refine the relevance score for each document.

The following query finds documents whose title field matches the query string how to make millions and that are not marked as spam. If any documents are starred or are from 2014 onward, they will rank higher than they would have otherwise. Documents that match both conditions will rank even higher:

```
{
"bool": {
"must": { "match": { "title": "how to make millions" }},
"must_not": { "match": { "tag": "spam" }},
"should": [
{ "match": { "tag": "starred" }},
{ "range": { "date": { "gte": "2014-01-01" }}}
]
}
}
```

### Validating Queries

```
GET /gb/tweet/_validate/query
{
   "query": {
      "tweet" : {
         "match" : "really powerful"
      }
   }
}

GET /gb/tweet/_validate/query?explain
{
   "query": {
      "tweet" : {
         "match" : "really powerful"
      }
   }
}
```


### Combiled Query
```
GET /tweet/_search
{
    "query" : {
        "bool": {
          "must": [
            {
              "match": {
                "tweet": "Elasticsearch"
              }
            }
          ],
          "filter": [
            {
              "term": {
                "user_id": 2
              }
            }
          ], 
          "should": [
            {
              "match": {
                "tweet": "text"
              }
            }
          ]
        }
    }
}
```