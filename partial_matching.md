# Partial Matching

**DATA for this tutorial**

```
PUT /address/_doc/1
{ "postcode": "W1V 3DG" }

PUT /address/_doc/2
{ "postcode": "W2F 8HW" }

PUT /address/_doc/3
{ "postcode": "W1F 7HW" }

PUT /address/_doc/4
{ "postcode": "WC1N 1LZ" }

PUT /address/_doc/5
{ "postcode": "SW5 0BE" }
```

## prefix Query

```
POST /address/_search
{
  "query": {
    "prefix": {
      "postcode.keyword": "W1"
    }
  }
}
```

## wildcard and regexp Queries

```
POST /address/_search
{
  "query": {
    "wildcard": {
      "postcode.keyword": "W????HW"
    }
  }
}


POST /address/_search
{
  "query": {
    "regexp": {
      "postcode.keyword": "W[0-9].+"
    }
  }
}
```
