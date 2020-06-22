# Proximity Matching

Standard full-text search with TF/IDF treats documents, or at least each field within a document, as a big bag of words. The match query can tell us whether that bag contains our search terms, but that is only part of the story. It can’t tell us anything about the relationship between words.

Consider the difference between these sentences:

Sue ate the alligator.

The alligator ate Sue.

Sue never goes anywhere without her alligator-skin purse.

## Phrase Matching

```
GET /my_index/my_type/_search
{
    "query": {
        "match_phrase": {
            "title": "quick brown fox"
        }
    }
}
```

### Slop

```
POST /my_index/my_type/_search
{
   "query": {
      "match_phrase": {
         "title": {
            "query": "quick dog",
            "slop":  50
         }
      }
   }
}
```

## Proximity for Relevance

Instead of using proximity matching as an absolute requirement, we can use it as a signal—as one of potentially many queries, each of which contributes to the overall score for each document

```
ET /my_index/my_type/_search
{
  "query": {
    "bool": {
      "must": {
        "match": {
          "title": {
            "query":                "quick brown fox",
            "minimum_should_match": "30%"
          }
        }
      },
      "should": {
        "match_phrase": {
          "title": {
            "query": "quick brown fox",
            "slop":  50
          }
        }
      }
    }
  }
}
```
