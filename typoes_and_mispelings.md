# Typoes and Mispelings
We expect a query on structured data like dates and prices to return only documents that match exactly. However, good full-text search shouldn’t have the same restriction. Instead, we can widen the net to include words that may match, but use the relevance score to push the better matches to the top of the result set.

In fact, full-text search that only matches exactly will probably frustrate your users. Wouldn’t you expect a search for “quick brown fox” to match a document containing “fast brown foxes,” “Johnny Walker” to match “Johnnie Walker,” or “Arnold Shcwarzenneger” to match “Arnold Schwarzenegger”?


## Fuzziness
Fuzzy matching treats two words that are “fuzzily” similar as if they were the same word. First, we need to define what we mean by fuzziness.


Substitution of one character for another: _f_ox → _b_ox

Insertion of a new character: sic → sic_k_

Deletion of a character:: b_l_ack → back


Transposition of two adjacent characters: _st_ar → _ts_ar

For example, to convert the word bieber into beaver requires the following steps:

Substitute v for b: bie_b_er → bie_v_er

Substitute a for i: b_i_ever → b_a_ever

Transpose a and e: b_ae_ver → b_ea_ver


Elasticsearch supports a maximum edit distance, specified with the fuzziness parameter, of 2.

Of course, the impact that a single edit has on a string depends on the length of the string. Two edits to the word hat can produce mad, so allowing two edits on a string of length 3 is overkill. The fuzziness parameter can be set to AUTO, which results in the following maximum edit distances:

0 for strings of one or two characters

1 for strings of three, four, or five characters

2 for strings of more than five characters

Of course, you may find that an edit distance of 2 is still overkill, and returns results that don’t appear to be related. You may get better results, and better performance, with a maximum fuzziness of 1.


## Fuzzy Query
```
POST /fuzzy/_bulk
{ "index": { "_id": 1 }}
{ "text": "Surprise me!"}
{ "index": { "_id": 2 }}
{ "text": "That was surprising."}
{ "index": { "_id": 3 }}
{ "text": "I wasn't surprised."}


GET /fuzzy/_search
{
  "query": {
    "fuzzy": {
      "text": "surprize",

    }
  }
}

GET /fuzzy/_search
{
 "query": {
    "fuzzy": {
      "text": {
        "value": "surprize",
        "fuzziness": 1
      }
    }
  }
}
```

