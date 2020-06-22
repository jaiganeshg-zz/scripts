# Reducing Words to Their Root Form
Most languages of the world are inflected, meaning that words can change their form to express differences in the following:

Number: fox, foxes

Tense: pay, paid, paying

Gender: waiter, waitress

Person: hear, hears

Case: I, me, my

Aspect: ate, eaten

Mood: so be it, were it so

Stemming attempts to remove the differences between inflected forms of a word, in order to reduce each word to its root form. For instance foxes may be reduced to the root fox, to remove the difference between singular and plural in the same way that we removed the difference between lowercase and uppercase.

The root form of a word may not even be a real word. The words jumping and jumpiness may both be stemmed to jumpi. It doesn’t matter—as long as the same terms are produced at index time and at search time, search will just work.

If stemming were easy, there would be only one implementation. Unfortunately, stemming is an inexact science that suffers from two issues: understemming and overstemming.

Understemming is the failure to reduce words with the same meaning to the same root. For example, jumped and jumps may be reduced to jump, while jumping may be reduced to jumpi. Understemming reduces retrieval relevant documents are not returned.

Overstemming is the failure to keep two words with distinct meanings separate. For instance, general and generate may both be stemmed to gener. Overstemming reduces precision: irrelevant documents are returned when they shouldn’t be.

## Algorithmic Stemmers
Most of the stemmers available in Elasticsearch are algorithmic in that they apply a series of rules to a word in order to reduce it to its root form, such as stripping the final s or es from plurals. They don’t have to know anything about individual words in order to stem them.

## Dictionary Stemmers
Dictionary stemmers work quite differently from algorithmic stemmers. Instead of applying a standard set of rules to each word, they simply look up the word in the dictionary. Theoretically, they could produce much better results than an algorithmic stemmer. A dictionary stemmer should be able to do the following:

