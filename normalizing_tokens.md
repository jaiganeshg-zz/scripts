# Normalizing Tokens
Breaking text into tokens is only half the job. To make those tokens more easily searchable, they need to go through a normalization process to remove insignificant differences between otherwise identical words, such as uppercase versus lowercase. Perhaps we also need to remove significant differences, to make esta, ésta, and está all searchable as the same word. Would you search for déjà vu, or just for deja vu?

The most frequently used token filter is the lowercase `filter`, which does exactly what you would expect; it transforms each token into its lowercase form:
```
GET /_analyze?tokenizer=standard&filters=lowercase
The QUICK Brown FOX!
```


