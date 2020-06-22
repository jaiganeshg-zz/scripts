# Identifying Words
A word in English is relatively simple to spot: words are separated by whitespace or (some) punctuation. Even in English, though, there can be controversy: is you’re one word or two? What about o’clock, cooperate, half-baked, or eyewitness?

## standard Analyzer
The standard analyzer is used by default for any full-text analyzed string field. If we were to reimplement the standard analyzer as a custom analyzer, it would be defined as follows:

```
{
    "type":      "custom",
    "tokenizer": "standard",
    "filter":  [ "lowercase", "stop" ]
}
```
## standard Tokenizer
A tokenizer accepts a string as input, processes the string to break it into individual words, or tokens (perhaps discarding some characters like punctuation), and emits a token stream as output.

```
GET /_analyze?tokenizer=whitespace
You're the 1st runner home!
```


## ICU Plug-in
The ICU plug-in is an essential tool for dealing with languages other than English, and it is highly recommended that you install and use it. Unfortunately, because it is based on the external ICU libraries, different versions of the ICU plug-in may not be compatible with previous versions. When upgrading, you may need to reindex your data.

