 # Stopwords: Performance Versus Precision
 Back in the early days of information retrieval, disk space and memory were limited to a tiny fraction of what we are accustomed to today. It was essential to make your index as small as possible.

Every kilobyte saved meant a significant improvement in performance. Stemming was important, not just for making searches broader and increasing retrieval in the same way that we use it today, but also as a tool for compressing index size.

Another way to reduce index size is simply to index fewer words. For search purposes, some words are more important than others. A significant reduction in index size can be achieved by indexing only the more important terms.

So which terms can be left out? We can divide terms roughly into two groups:

Low-frequency terms
Words that appear in relatively few documents in the collection. Because of their rarity, they have a high value, or weight.

High-frequency terms
Common words that appear in many documents in the index, such as the, and, and is. These words have a low weight and contribute little to the relevance score.


## Pros and Cons of Stopwords
- Distinguishing happy from not happy.

- Searching for the band The The.

- Finding Shakespeare’s quotation “To be, or not to be”

- Using the country code for Norway: no


## Stopwords and Performance
The biggest disadvantage of keeping stopwords is that of performance. When Elasticsearch performs a full-text search, it has to calculate the relevance _score on all matching documents in order to return the top 10 matches.

While most words typically occur in much fewer than 0.1% of all documents, a few words such as the may occur in almost all of them. Imagine you have an index of one million documents. A query for quick brown fox may match fewer than 1,000 documents. But a query for the quick brown fox has to score and sort almost all of the one million documents in your index, just in order to return the top 10!

The problem is that the quick brown fox is really a query for the OR quick OR brown OR fox—any document that contains nothing more than the almost meaningless term the is included in the result set. What we need is a way of reducing the number of documents that need to be scored.


