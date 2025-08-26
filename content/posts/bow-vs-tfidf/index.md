---
title: "BoW Vs. TF-IDF"
date: 2024-10-11T08:06:25+06:00
description: BoW Vs. TF-IDF
menu:
  sidebar:
    name: BoW Vs. TF-IDF
    identifier: bow
    weight: 10
author:
  name: Unique Karki
  image: /images/author/unique.jpeg
# hero: reads.jpeg
---

We humans read words effortlessly, but computers only understand numbers. So how do we bridge that gap? That’s where methods like Bag-of-Words (BoW) and TF-IDF come in.

## What problem are we solving?

When we read a piece of text, the words seem never-ending with an infinite vocabulary. The problem with raw text is that it is messy, unstructured, and consists of a multitude of words. We need a way to represent raw text in numerical form such that the data is structured and easier to process. This process is known as vectorization, and we’ll discuss it.

## Bag-of-Words (BoW)

Bag-of-Words (BoW) is one of the fundamental ways of converting text to numbers. In simple terms, it keeps count of the occurrence of each unique word in a document. It is used for its simplicity, intuitiveness, and also its effectiveness. Let’s break it down step-by-step and see how it works.

Suppose you have the following sentences:

“How are you?”
“I am very good.”
“What a lovely day.”

### Step 1
Make a set of unique words (vocabulary)
vocab = (how, are, you, i, am, very, good, what, a, lovely, day)
len(vocab) = 11

### Step 2
Initialize a zero vector of length len(vocab) for each sentence

v1 = [0,0,0,0,0,0,0,0,0,0,0] <br>
v2 = [0,0,0,0,0,0,0,0,0,0,0] <br>
v3 = [0,0,0,0,0,0,0,0,0,0,0]

### Step 3
Keep count of how many times a word in the vocab has occurred in the sentence, and replace the 0 with the count.

v1 = [1,1,1,0,0,0,0,0,0,0,0] <br>
v2 = [0,0,0,1,1,1,1,0,0,0,0] <br>
v3 = [0,0,0,0,0,0,0,1,1,1,1]

Now you have successfully converted the sentence to numerical form using BoW. Notice how each sentence is now just a row of numbers; no meaning, no grammar, just counts.

But, easy as it looks, there are many drawbacks to BoW.

### Drawbacks:
1. All of the words are given the same priority.
2. Lots of filler words, such as “a”, “am”, “the”, etc., make the vector size large.
3. There is no priority given to rare words in the sentences.

But BoW isn’t perfect. That’s where TF-IDF comes in — it adds some ‘common sense’ to word importance.

## Term Frequency - Inverse Document Frequency(TF-IDF)

This technique of vectorization measures the importance of different words in a document. To understand it, let’s break down each term from the name itself.

TF = word frequency in one document <br>
DF = how many documents contain the word <br>
IDF = how rare a word is across all documents <br>
TF-IDF = TF × IDF <br>

### Term Frequency (TF)

t -> term/word <br>
d -> document/page <br>
N -> Number of document/page <br>

tf(t,d)=count of t in d/Total no. of words in d

This reflects how frequently a word appears within a text

### Document Frequency (DF)

df(t) = No. of d where t has occured

This reflects the number of documents/pages in which the term/word appears.

### Inverse Document Frequency (IDF)

The main drawback of Term-Frequency is that it considers all of the words as equally important

The most occurring words, such as a, an, the, etc., should be weighed down and rare words should be scaled up.

idf(t) =N/df

Problems with the above formula:
1. If N becomes very high, idf explodes
2. If df = 0, idf becomes invalid

The updated formula for idf becomes,
idf(t) =log(N/(df + 1))

### TF-IDF
Incorporating all of the above, we get,
tf-idf(t) = tf(t,d) x log(N/(df + 1)) 

Here’s a side-by-side view to make the differences clear.

| BoW    | TF-IDF |
| -------- | ------- |
| Treats all words equally  | Adjusts word importance based on word frequency and rarity across documents    |
| Stopwords reduce the meaningfulness of the model | Dilutes the importance of stopwords     |
| Document length affects word frequency    |   Normalizes the effect of document length  |
| Simple to implement, but creates a high-dimensional sparse vector    | More complex, provides a more informative representation   |

BoW and TF-IDF may look old-school compared to embeddings like BERT, but they’re still powerful starting points. Personally, I find that learning these simple methods makes the modern ones much less of a black box.
