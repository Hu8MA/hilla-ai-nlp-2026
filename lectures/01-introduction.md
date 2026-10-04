# Lecture 1: Introduction to Natural Language Processing

**University of Hilla** · Dept. AI / NLP · 2026/27
**Assistant lecturer:** Hussein Mahdi Al-Rubaie
**Duration:** 90 minutes (theory) · **Date:** 10/04/2026

---

## Questions for today

By the end of this lecture, you will be able to answer:

1. How did **NLP start**, and how has it changed?
2. Why is human language hard for computers?
3. What is NLP, and where does it **fit in AI**?
4. How does NLP **solve** a problem?
5. What **basic terms** do we need to know?
6. What will we learn in this course?

---

## 1. A Short History

### Can a computer understand language?

- People have asked this question since the first computers.
- Humans have written text for thousands of years.
- If computers could read it, they could use all that knowledge.

### Timeline

| Year | Event |
|---|---|
| 1950 | Alan Turing asks: "Can machines think?" |
| 1954 | Georgetown–IBM experiment: first machine translation demo (Russian → English) |
| 1957 | Noam Chomsky describes language with grammar rules |
| 1966 | **ELIZA**: the first chatbot |
| 1990s | Statistical NLP: learning from counting words in large text collections |
| 2010s | Neural networks and word vectors |
| 2017 | Transformers |
| 2022 | ChatGPT and large language models |

### ELIZA (1966)

```
User:  I need some help, that much seems certain.
ELIZA: WHAT WOULD IT MEAN TO YOU IF YOU GOT SOME HELP
User:  My mother takes care of me.
ELIZA: WHO ELSE IN YOUR FAMILY TAKES CARE OF YOU
```

- **ELIZA did not understand anything.**
- It **matched** simple word **patterns**: "I need X" → "What would it mean to you if you got X?"
- Still, many people believed it understood them.

### Three eras of NLP

1. **Rules:** humans write the rules by hand (ELIZA)
2. **Statistics:** the computer counts words in large text collections
3. **Neural networks:** the computer learns patterns from huge amounts of text (ChatGPT)

---

## 2. A Simple Example

*Text from Wikipedia:*

> London is the capital and most populous city of England and the United Kingdom. Standing on the River Thames in the south east of the island of Great Britain, London has been a major settlement for two millennia. It was founded by the Romans, who named it Londinium.

**Question to students:** What facts can you find in this text?

### What a computer can extract

| Text | Meaning |
|---|---|
| London | a **city** |
| London | the **capital** of the United Kingdom |
| River Thames | a **place** |
| Romans | a **group of people** |
| two millennia | a **time** |

*The computer turns free text into **structured data**.*

---

## 3. The Problem

### Language is ambiguous

> "Kids make nutritious snacks."

**Question to students:** What does this headline mean?

- Meaning 1: *Kids prepare healthy snacks.*
- Meaning 2: *Kids are healthy snacks (to eat)!*

*Humans know the answer immediately. Computers do not.*

### More examples

- "I saw the man with the telescope." Who has the telescope?
- "Time flies like an arrow." Is "flies" a verb or a noun?

### Why is language hard for computers?

- One word can have many meanings.
- One sentence can have many structures.
- The meaning depends on context.
- **New words** appear all the time.

---

## 4. What is NLP?

### Definition

**Natural Language Processing (NLP)** is a field of Artificial Intelligence that helps computers **understand, process, and generate** human language.

### Structured vs. unstructured data

| Structured | Unstructured |
|---|---|
| Tables, spreadsheets, databases | Emails, articles, social media posts |
| Easy for computers | Hard for computers |

*Most of the world's information is **unstructured text**.*

### Where does NLP fit?

NLP sits where three fields meet:

```
Computer Science  +  Linguistics  +  Artificial Intelligence
                         ↓
                        NLP
```

Inside Artificial Intelligence:

```
Artificial Intelligence
 └── Machine Learning      (learning from data)
      └── Deep Learning    (neural networks)
```

- **NLP uses all three levels:** hand-written rules, machine learning, and deep learning.
- NLP works with text, just as Computer Vision works with images and Speech Recognition works with audio.

### How does NLP solve problems?

```
Input text → Process the text → Model → Output
```

| Step | What happens | Spam filter example |
|---|---|---|
| 1. Input | Raw text | An email |
| 2. Process | Split and clean the text | Words: "win", "free", "money" |
| 3. Model | Rules or learning from examples | Learned from thousands of labeled emails |
| 4. Output | A useful result | Spam / Not spam |

### NLP in everyday life

- Google Translate
- Spam filters
- Search engines
- Autocomplete on your phone
- Voice assistants (Siri, Alexa)
- Chatbots (ChatGPT)

---

## 5. Basic Concepts

| Term | Meaning |
|---|---|
| **Corpus** | A large collection of text |
| **Token** | A unit of text (a word, part of a word, or punctuation) |
| **Vocabulary** | The set of different words in a corpus |
| **Ambiguity** | When text has more than one meaning |
| **Language model** | A model that predicts the next word |

---

## 6. Course Roadmap

### The big picture

```
Text Processing → N-Grams → POS Tagging → Parsing
      → Classification → Clustering → Sentiment
      → Word Vectors → Information Extraction
      → RNNs → Summarization → Machine Translation
```

*From **basic steps** → to **understanding** → to **real applications**.*

### Text Processing
- **Problem:** Raw text is messy.
- **Idea:** Clean the text and split it into sentences and words.
- **Example:** Preparing text before any analysis.

### N-Grams
- **Problem:** Which word comes next?
- **Idea:** Count word sequences and calculate probabilities.
- **Example:** Autocomplete on your phone.

### Part of Speech Tagging
- **Problem:** What role does each word play?
- **Idea:** Label each word as noun, verb, adjective, …
- **Example:** Understanding "book a flight" vs. "read a book".

### Context Free Grammar and Parsing (2 weeks)
- **Problem:** How are the words in a sentence connected?
- **Idea:** Build a tree that shows the sentence structure.
- **Example:** Grammar checkers.

### Text Classification
- **Problem:** Which category does a text belong to?
- **Idea:** Learn from labeled examples.
- **Example:** Spam vs. not spam.

### Text Clustering
- **Problem:** How to group many documents without labels?
- **Idea:** Put similar texts together automatically.
- **Example:** Grouping news articles by topic.

### Sentiment Analysis
- **Problem:** Is the writer happy or unhappy?
- **Idea:** Classify text as positive, negative, or neutral.
- **Example:** Analyzing product reviews.

### Semantics and Word Vectors
- **Problem:** How can a computer know that "king" and "queen" are related?
- **Idea:** Represent words as numbers (vectors).
- **Example:** Finding similar words.

### Information Extraction and NER
- **Problem:** How to pull facts out of text?
- **Idea:** Find names of people, places, dates, companies.
- **Example:** The London example from today.

### RNNs for Language Modeling
- **Problem:** How to remember earlier words in a sentence?
- **Idea:** Neural networks that read text word by word.
- **Example:** Text generation.

### Text Summarization
- **Problem:** Texts are too long to read.
- **Idea:** Produce a short version that keeps the main points.
- **Example:** News summaries.

### Machine Translation
- **Problem:** How to translate between languages?
- **Idea:** Learn from texts in two languages.
- **Example:** Google Translate.

---

## Wrap-up

- NLP helps computers work with human language.
- Language is hard because it is **ambiguous**.
- NLP moved from **rules** → **statistics** → **neural networks**.
- We solve NLP problems step by step using a **pipeline**.

---

## Homework

### Q1. Ambiguity
Find one sentence (English or Arabic) that has two meanings. Write both meanings, and explain in one or two lines why a computer would find it **hard to choose the right one**.

### Q2. NLP in your daily life
Choose one application you use every day (e.g., Google Translate, autocomplete, a spam filter, a chatbot). Describe it **using the four steps** from *How does NLP solve problems?*:

- **Input:** what text goes in?
- **Process:** what has to be done to the text?
- **Model:** does it use rules or learn from examples?
- **Output:** what result does it give?

### Q3. Rules vs. learning
ELIZA used hand-written rules, and ChatGPT learns from huge amounts of text. **Give one advantage and one disadvantage** of each approach.

---

## References

- Jurafsky & Martin, [*Speech and Language Processing*](https://web.stanford.edu/~jurafsky/slp3/) (Ch. 2 opening, Ch. 3 opening)
- Adam Geitgey, [*Natural Language Processing is Fun!*](https://medium.com/@ageitgey/natural-language-processing-is-fun-9a0bff37854e) (Medium, 2018)
