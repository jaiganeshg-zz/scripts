# Getting Started with Languages
analyzers typically perform four roles:

- Tokenize text into individual words:

  The quick brown foxes → [The, quick, brown, foxes]

- Lowercase tokens:

  The → the

- Remove common stopwords:

  [The, quick, brown, foxes] → [quick, brown, foxes]

- Stem tokens to their root form:

  foxes → fox


- Each analyzer may also apply other transformations specific to its language in order to make words from that language more searchable:

  The english analyzer removes the possessive 's:

  John's → john

- The french analyzer removes elisions like l' and qu' and diacritics like ¨ or ^:

  l'église → eglis

- The german analyzer normalizes terms, replacing ä and ae with a, or ß with ss, among others:

  äußerst → ausserst


## Using Language Analyzers
The built-in language analyzers are available globally and don’t need to be configured before being used. They can be specified directly in the field mapping:

```
PUT /my_index
{
  "mappings": {
    "blog": {
      "properties": {
        "title": {
          "type":     "string",
          "analyzer": "english"
        }
      }
    }
  }
}
```

Multiple analyzers
```
PUT /my_index
{
  "mappings": {
    "blog": {
      "properties": {
        "title": {
          "type": "string",
          "fields": {
            "english": {
              "type":     "string",
              "analyzer": "english"
            }
          }
        }
      }
    }
  }
}
```

## Configuring Language Analyzers
```
PUT /my_index
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_english": {
          "type": "english",
          "stem_exclusion": [ "organization", "organizations" ],
          "stopwords": [
            "a", "an", "and", "are", "as", "at", "be", "but", "by", "for",
            "if", "in", "into", "is", "it", "of", "on", "or", "such", "that",
            "the", "their", "then", "there", "these", "they", "this", "to",
            "was", "will", "with"
          ]
        }
      }
    }
  }
}
```

Strategies
- One Language per Document
- One Language per Field
- Mixed-Language Fields
- Use n-grams