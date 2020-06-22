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

- Best fields:
  When searching for words that represent a concept, such as “brown fox,” the words mean more together than they do individually. Fields like the title and body, while related, can be considered to be in competition with each other. Documents should have as many words as possible in the same field, and the score should come from the best-matching field.
- Most fields: A common technique for fine-tuning relevance is to index the same data into multiple fields, each with its own analysis chain. The main field may contain words in their stemmed form, synonyms, and words stripped of their diacritics, or accents. It is used to match as many documents as possible.
- Cross fields: For some entities, the identifying information is spread across multiple fields, each of which contains just a part of the whole:

  - Person: first_name and last_name

  - Book: title, author, and description

  - Address: street, city, country, and postcode

  In this case, we want to find as many words as possible in any of the listed fields. We need to search across multiple fields as if they were one big field.

**Data for below queries**

```
PUT /my_index/_doc/1
{
    "title": "Quick brown rabbits",
    "body":  "Brown rabbits are commonly seen."
}

PUT /my_index/_doc/2
{
    "title": "Keeping pets healthy",
    "body":  "My quick brown fox eats rabbits on a regular basis."
}
```

## Best Fields

```
POST /my_index/_search
{
    "query": {
        "bool": {
            "should": [
                { "match": { "title": "Brown fox" }},
                { "match": { "body":  "Brown fox" }}
            ]
        }
    }
}

```

- Doc1 is ranked higher than doc 2
- Doc 1 has two fields matching brown. Doc2 has one field matching brown.
- So Doc 1 is ranked higher. Though Doc2 has complete string matched under body

## dis_max Query

```
POST /my_index/_search
{
    "query": {
        "dis_max": {
            "queries": [
                { "match": { "title": "Brown fox" }},
                { "match": { "body":  "Brown fox" }}
            ]
        }
    }
}
```

- Ranks documents based on best match which is body on doc2

## Tuning Best Fields Queries

- User searches for `quick pets` which is not presented in both the docs
- `Quick` Presented one time in Doc1 and Doc2
- `Pets` presented one time in Doc2
- `quick pets` both words are not present
- So no best matching field
- dis_query will give same score to both the docs
- Can be fixed via tie_braker. `tie_braker` will boost the score depends on number of matches

```
{
    "query": {
        "dis_max": {
            "queries": [
                { "match": { "title": "Quick pets" }},
                { "match": { "body":  "Quick pets" }}
            ],
            "tie_breaker": 0.3
        }
    }
}
```

## multi_match Query

```
{
  "dis_max": {
    "queries":  [
      {
        "match": {
          "title": {
            "query": "Quick brown fox",
            "minimum_should_match": "30%"
          }
        }
      },
      {
        "match": {
          "body": {
            "query": "Quick brown fox",
            "minimum_should_match": "30%"
          }
        }
      },
    ],
    "tie_breaker": 0.3
  }
}
```

to

```


{
"multi_match": {
"query": "Quick brown fox",
"type": "best_fields", 1
"fields": [ "title", "body" ],
"tie_breaker": 0.3,
"minimum_should_match": "30%" 2
}
}

```

Boosting fields

```
POST /my_index/_search
{
  "query": {
    "multi_match": {
      "query": "Quick pets",
      "fields": ["title", "body^2"],
      "tie_breaker": 0.3
    }
  }
}
```

## Cross-fields Entity Search

```
POST /full_text/_search
{
  "query": {
    "multi_match": {
      "query": "Jai Ganesh",
      "fields": ["firstname", "lastname"],
      "type": "best_fields"
    }
  }
}
```

- Type can be `best_fields` `most_fields` `cross_fields`
- Run with validate to check each field

```
GET /full_text/_validate/query?explain
{
  "query": {
    "multi_match": {
      "query":   "Jai Ganesh",
      "type":    "most_fields",
      "fields":  [ "firstname", "lastname"],
      "operator": "and"
    }
  }
}
```

## Custom field

```
POST /full_text/_doc
{
  "mappings": {
    "properties": {
      "firstname": {
        "type": "string",
        "copy_to": "full_name"
      },
      "lastname": {
        "type": "string",
        "copy_to": "full_name"
      },
      "full_name": {
        "type": "string"
      }
    }
  }
}
```
