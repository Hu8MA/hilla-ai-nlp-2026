# Lecture 4: N-gram Language Models

**University of Hilla** · Dept. AI / NLP · 2026/27
**Assistant lecturer:** Hussein Mahdi Al-Rubaie
**Duration:** 90 minutes (theory) · **Date:** 11/01/2026

---

## Questions for today

By the end of this lecture, you will be able to answer:

1. What is a **language model**, and why do we want to **predict the next word**?
2. What is an **n-gram**, and what is the **Markov assumption**?
3. How do we **estimate** n-gram probabilities by **counting** (MLE)?
4. Why do we use **log probabilities**?
5. How do we **evaluate** a language model, and what is **perplexity**?
6. What does **sampling** show us about a model?
7. What is the **zero problem**, and how does **smoothing** fix it?

---

## 1. Where We Stopped Last Lecture

- Last lectures: we learned how to cut text into **tokens** and build a **vocabulary**.
- **Today's question:** once we have tokens, can a computer **predict the next one**?
- This is the job of a **language model**, and it is exactly how **large language models (LLMs)** are trained.

*Let's try predicting a word ourselves.*

---

## 2. Predicting the Next Word

What word comes next?

> The water of Walden Pond is so beautifully ...

| Likely                     | Unlikely                        |
|----------------------------|---------------------------------|
| *blue, green, clear*       | *refrigerator, this, dog*       |

- We all have this intuition. Today we turn it into **numbers**.

*A model that does this is called a language model.*

---

## 3. What is a Language Model?

- A **language model (LM)** is a machine learning model that **predicts upcoming words**.
- More precisely, it gives a **probability to each possible next word**.
- It can also give a probability to a **whole sentence**:

| Sentence                                                          | Probability                              |
|-------------------------------------------------------------------|------------------------------------------|
| *all of a sudden I notice three guys standing on the sidewalk*    | **Higher**                               |
| *on guys all I of notice sidewalk three a sudden standing the*    | **Lower** (same words, wrong order)      |

### Why predict words?

| Use                                  | Example                                                                                          |
|--------------------------------------|--------------------------------------------------------------------------------------------------|
| **Large language models**            | LLMs learn a huge amount about language just by being trained to predict the next word           |
| **Grammar and spelling correction**  | *There are* is more probable than *Their are*; *has improved* than *has improve*                 |
| **Speech recognition**               | *I will be back soonish* is more probable than *I will be bassoon dish*                          |
| **Communication aids (AAC)**         | People who can't speak or sign select words with eye gaze; the LM suggests likely words for the menu |

*The simplest language model is the n-gram model. What is an n-gram?*

---

## 4. What is an N-gram?

- An **n-gram** is a sequence of **n words**.
- An **n-gram language model** estimates the probability of a word given only the **n − 1 previous words**, and from that assigns probabilities to whole sequences.

| Name        | n  | Examples from "The water of Walden Pond"     |
|-------------|----|----------------------------------------------|
| **Unigram** | 1  | *The*, *water*                               |
| **Bigram**  | 2  | *The water*, *water of*                      |
| **Trigram** | 3  | *The water of*, *water of Walden*            |

- The word "n-gram" is also used for the **model** that predicts a word from the n − 1 previous words.
- N-grams are simple, so we use them to learn ideas that **LLMs also use**: training and test sets, perplexity, sampling.

*So how do we compute the probability of the next word?*

---

## 5. The Direct Way: Count the Whole History

- We want **P(w | h)**: the probability of word **w** given the history **h** before it.
- **Direct way:** count in a huge corpus.

$$
P(\text{blue} \mid \text{The water of Walden Pond is so beautifully}) = \frac{C(\text{The water of Walden Pond is so beautifully blue})}{C(\text{The water of Walden Pond is so beautifully})}
$$

- So, of all the times we saw the history, how many times did *blue* come next?
- **Problem:** language is **creative**; new sentences are invented all the time. Even the whole web is not big enough to count every long history.

### The chain rule

- The probability of a whole sentence = the probability of **each word given all the words before it**, multiplied together:

$$
P(w_{1:n}) = P(w_1)\,P(w_2 \mid w_1)\,P(w_3 \mid w_{1:2}) \cdots P(w_n \mid w_{1:n-1})
$$

- In plain words, for *I am Sam*:

```
P(I) × P(am | I) × P(Sam | I am)
```

- **But this doesn't solve the problem:** the last word still needs the **whole history** before it, and we can't count long histories.

*The n-gram idea: forget most of the history.*

---

## 6. The Markov Assumption

- **Idea:** instead of the whole history, look only at the **last few words**.
- **Bigram model:** look only at the **previous word**.

| Instead of                                              | We use                    |
|---------------------------------------------------------|---------------------------|
| P(blue \| The water of Walden Pond is so beautifully)   | P(blue \| beautifully)    |

$$
P(w_n \mid w_{1:n-1}) \approx P(w_n \mid w_{n-1})
$$

- This assumption is called the **Markov assumption**: we can predict the future **without looking too far into the past**.

| Model       | Looks back     |
|-------------|----------------|
| **Bigram**  | 1 word         |
| **Trigram** | 2 words        |
| **N-gram**  | n − 1 words    |

### Sentence probability with bigrams

$$
P(w_{1:n}) \approx \prod_{k=1}^{n} P(w_k \mid w_{k-1})
$$

- **Multiply** the probability of each word given **only the word before it**:

```
I am Sam  →  P(I | <s>) × P(am | I) × P(Sam | am) × P(</s> | Sam)
```

- `<s>` = start of sentence, `</s>` = end of sentence (special symbols we add).
- After the Markov assumption, we know **what** to compute: P(am | I). But we still don't have the **numbers**.

*Where do numbers like P(am | I) come from? From counting.*

---

## 7. Estimating Probabilities by Counting (MLE)

$$
P(w_n \mid w_{n-1}) = \frac{C(w_{n-1}\, w_n)}{C(w_{n-1})}
$$

- **Count the pair "A B", then divide by the count of A.**
- This is called **maximum likelihood estimation (MLE)**, or **relative frequency**.
- Same idea for one word: *Chinese* appears 400 times in a 1,000,000-word corpus:

```
P(Chinese) = 400 / 1,000,000 = 0.0004
```

### Worked example: the "I am Sam" corpus

Mini-corpus (p. 40), with `<s>` and `</s>` added:

```
<s> I am Sam </s>
<s> Sam I am </s>
<s> I do not like green eggs and ham </s>
```

| Probability       | Count the pair      | Count the first word | Result            |
|-------------------|---------------------|----------------------|-------------------|
| P(I \| `<s>`)     | `<s> I` = 2         | `<s>` = 3            | 2/3 = **0.67**    |
| P(Sam \| `<s>`)   | `<s> Sam` = 1       | `<s>` = 3            | 1/3 = **0.33**    |
| P(am \| I)        | `I am` = 2          | `I` = 3              | 2/3 = **0.67**    |
| P(do \| I)        | `I do` = 1          | `I` = 3              | 1/3 = **0.33**    |
| P(Sam \| am)      | `am Sam` = 1        | `am` = 2             | 1/2 = **0.5**     |
| P(`</s>` \| Sam)  | `Sam </s>` = 1      | `Sam` = 2            | 1/2 = **0.5**     |

### The probability of a sentence

P(`<s>` I am Sam `</s>`):

| Step              | Probability                                |
|-------------------|--------------------------------------------|
| P(I \| `<s>`)     | 2/3                                        |
| P(am \| I)        | 2/3                                        |
| P(Sam \| am)      | 1/2                                        |
| P(`</s>` \| Sam)  | 1/2                                        |
| **Multiply**      | 2/3 × 2/3 × 1/2 × 1/2 = **1/9 ≈ 0.111**    |

---

## 8. A Real Corpus: Berkeley Restaurant Project

- **9,332 sentences** of users asking about restaurants, e.g. *tell me about chez panisse*.
- Part of the bigram counts (Fig. 3.1, p. 41): **row = first word, column = next word**.

|              | i  | want | to  | eat | chinese | food |
|--------------|----|------|-----|-----|---------|------|
| **i**        | 5  | 827  | 0   | 9   | 0       | 0    |
| **want**     | 2  | 0    | 608 | 1   | 6       | 6    |
| **to**       | 2  | 0    | 4   | 686 | 2       | 0    |
| **chinese**  | 1  | 0    | 0   | 0   | 0       | 82   |

- Unigram counts: *i* = 2533, *want* = 927, *to* = 2417, *chinese* = 158.
- Example:

```
P(want | i) = 827 / 2533 = 0.33
```

- Notice: **most counts are zero**. Remember this; it becomes a problem later.

### What bigrams capture

Some probabilities from the book (p. 42):

```
P(i | <s>)         = 0.25
P(english | want)  = 0.0011
P(food | english)  = 0.5
P(</s> | food)     = 0.68
```

```
P(<s> i want english food </s>) = 0.25 × 0.33 × 0.0011 × 0.5 × 0.68 = 0.000031
```

What these numbers know about language:

| Kind of fact  | Example                                                                  |
|---------------|--------------------------------------------------------------------------|
| **Grammar**   | After *eat* usually comes a noun or adjective; after *to* usually a verb |
| **The task**  | Sentences often start with *I*                                           |
| **Culture**   | People ask for Chinese food more than English food                       |

*Notice how small 0.000031 is. With long texts, numbers get too small for the computer.*

---

## 9. Log Probabilities

- Probabilities are **≤ 1**, so multiplying many of them gives a number **too small for the computer** (**underflow**).
- **Solution:** use **logs**. Adding logs = multiplying probabilities.

$$
p_1 \times p_2 \times p_3 \times p_4 = \exp(\log p_1 + \log p_2 + \log p_3 + \log p_4)
$$

Example with *I am Sam* (natural log):

| Probability | log                                               |
|-------------|---------------------------------------------------|
| 2/3         | −0.405                                            |
| 2/3         | −0.405                                            |
| 1/2         | −0.693                                            |
| 1/2         | −0.693                                            |
| **Sum**     | **−2.197** → exp(−2.197) = **0.111** (same answer) |

---

## 10. Longer N-grams and Large Datasets

*(Just overview)*

- With enough data, we use **trigrams, 4-grams or 5-grams**.
- A trigram needs **two words of context**, so the first word uses two start symbols: P(I | `<s>` `<s>`).
- Large n-gram datasets:
  - **Google Web 5-gram corpus:** from **1 trillion** words of English
  - **Google Books Ngrams:** **800 billion** tokens in **8 languages**
- **Infini-gram** allows n-grams of **any length**, over **5 trillion** tokens.

*We can build many models. How do we know which one is better?*

---

## 11. Evaluating Language Models

### Extrinsic and intrinsic evaluation

**1. Extrinsic evaluation: testing within a real application**

- We place both models inside a task (e.g. a **speech recognition** system) and compare the actual results.
- **Example:** the first model produces text with **10 errors**, the second with **6 errors**. So the second model is better for the task.

**2. Intrinsic evaluation: testing the model itself**

- We measure the model's quality using a mathematical metric such as **perplexity**, without running the full speech recognition or machine translation system.
- **Example:** we compare the perplexity of the two models on the **same test set**. A **lower** value usually means the model predicts the text better.

| Type          | How                                                                                 | Good                                       | Problem                      |
|---------------|-------------------------------------------------------------------------------------|--------------------------------------------|------------------------------|
| **Extrinsic** | Put the LM inside an application (e.g. speech recognition) and measure the improvement | The only way to know it really helps the task | Expensive to run         |
| **Intrinsic** | Measure the model's quality on its own                                              | Fast                                       | May not reflect the real task |

### Training, development and test sets

| Set                            | Used for                                     |
|--------------------------------|----------------------------------------------|
| **Training set**               | Learn the model (for n-grams: the counts)    |
| **Development set (devset)**   | Test the model while we improve it           |
| **Test set**                   | Final test, **once**, on unseen data         |

- The test set should **match** the language we want to use the model for (e.g. chemistry lectures → test on chemistry lectures).
- **Never let test sentences into the training set.** This is called **training on the test set** or **data contamination**: the probabilities look too high.

### A better model gives the test set a higher probability

- The better model **predicts the test set better**, so it gives it a **higher probability**.
- **Problem:** the probability of a text gets **smaller the longer it is**, so we can't compare texts of different lengths.
- We need a **per-word** measure.

*That per-word measure is perplexity.*

---

## 12. Perplexity

- **Perplexity (PP)** = the **inverse** probability of the test set, **normalized** by the number of words N:

$$
\text{perplexity}(W) = P(w_1 w_2 \ldots w_N)^{-\frac{1}{N}}
$$

**Two parts:**

- **Normalized** (the power 1/N, i.e. the **N-th root**): makes texts of different lengths comparable.
- **Inverse** (the minus sign): higher probability → **lower** perplexity.

**Lower perplexity = better model.**

### Why the N-th root?

**Example:** a model gives **every word** probability **1/2**.

**With multiplication:** the probability of a text is a product, so it **shrinks with every word**:

```
2 words:   P(W) = 1/2 × 1/2 = 1/4
10 words:  P(W) = (1/2)^10  = 1/1024
```

**With logs:** logs avoid underflow, but the **same problem remains**. The sum gets more negative with every word (log 1/2 = −0.693):

```
2 words:   (−0.693) + (−0.693)               = −1.386
10 words:  (−0.693) + (−0.693) + … (10 times) = −6.93
```

- Multiplication and logs compute the **same probability**, so **both depend on the length** of the text.
- Same model, but the long text always looks worse. So raw probability **can't compare** texts of different lengths.

### The fix: the N-th root

- The **N-th root** is the average of a product (the **geometric mean**): the probability **per word**.

```
2 words:   √(1/4)        = 1/2
10 words:  ¹⁰√(1/1024)   = 1/2
```

- Now both are the same: **1/2 per word**. Length no longer matters.
- Then the **inverse**: 1 / (1/2) = **perplexity 2** for both texts.

### Perplexity example: a 3-color language

A tiny language with 3 words: {red, blue, green}. Test set: *red red red red blue*.

| Model | Probabilities                          | Perplexity |
|-------|----------------------------------------|------------|
| **A** | every color = 1/3                      | **3**      |
| **B** | red = 0.8, green = 0.1, blue = 0.1     | **1.89**   |

- **Model A:** at each step there are **3 equal choices** → perplexity 3.
- **Model B:** knows *red* is likely, so it is **less surprised** → lower perplexity → **better** model for this test set.
- Perplexity can be read as the **average number of choices** the model is unsure between.

### Comparing n-gram models

Trained on **38 million** words of the Wall Street Journal, tested on **1.5 million** words:

|                | Unigram | Bigram | Trigram |
|----------------|---------|--------|---------|
| **Perplexity** | 962     | 170    | **109** |

- **More context → better predictions → lower perplexity.**
- **Two warnings:**
  - Perplexities are comparable **only if the models use the same vocabulary**.
  - Better perplexity **doesn't guarantee** a better application; confirm with an **extrinsic** test when possible.

---

## 13. Sampling Sentences from a Model

- **Sampling** = generating sentences by choosing each word **according to its probability**.
- It means using the language model to **write** sentences, instead of scoring them. At each step, the model picks the next word **at random**, but **likely words get picked more often**. It's like a loaded die: frequent words have bigger faces.
- **In one line:** sampling = letting the model write: at each step, pick the next word at random according to its probability, **starting after `<s>`** and **stopping at `</s>`**.

**Interactive demo:** [How a language model writes: sampling](../labs/demos/ngram_sampling.html)

### More context, better sentences

Sentences generated from models trained on **Shakespeare** (Fig. 3.4):

| Model       | Example                                                                 | What we see                              |
|-------------|-------------------------------------------------------------------------|------------------------------------------|
| **Unigram** | *To him swallowed confess hear both. Which. Of save on trail…*          | No connection between words              |
| **Bigram**  | *What means, sir. I confess she? then all sorts, he is trim, captain.*  | Some local word-to-word sense            |
| **Trigram** | *This shall forbid it should be branded, if renown made it empty.*      | Starts to look like Shakespeare          |
| **4-gram**  | *It cannot be but so.*                                                  | **Copied** from Shakespeare (*King John*) |

- Most 3-word sequences appear in Shakespeare **only once or a few times**.
- **Example:** after *It cannot be*, the model saw only **7** next words in all of Shakespeare: *but, I, that, thus, this*, and the period.

---

## 14. Generalizing vs. Overfitting

- An n-gram model **depends on its training corpus**: its probabilities record facts about that corpus.
- **Generalizing** = working well on **new** text. **Overfitting** = fitting the training text **too closely**, like the 4-gram copying Shakespeare.
- A different corpus gives a different model: models trained on the **Wall Street Journal** write news-like sentences with almost **no overlap** with the Shakespeare ones.
- **Lesson:** the training corpus must **match** the text we will use the model on:
  - the same **genre**: legal documents for legal translation, questions for question answering
  - the same **dialect or variety**: e.g. tweets with words like *finna* or *den* (for *then*)

### Unseen words?

- What if *Jurafsky* never appears in training but appears in the test set?
- Remember Lecture 3: language models usually work on **subword tokens (BPE)**, not words.
- Any word can be **built from known tokens**, down to single letters if needed.
- So the test set **never contains unseen tokens**.

```
Training (once):
  training text → BPE tokenizer (learns merges) → tokens → n-gram counts → stored probabilities

Testing (new text):
  new text → the same BPE tokenizer → tokens → look up the stored probabilities → P(sentence), perplexity
```

*But there is a different problem: known words in an **unseen combination**.*

---

## 15. The Zero Problem

- Training has *ruby* and *slippers*, but never the pair *ruby slippers*. So P(slippers | ruby) = **0**.
- These unseen n-grams are called **zeros**. They cause two problems:
  1. We **underestimate** sequences that can really occur.
  2. If **one** word in the test set has probability 0, the **whole** test set has probability 0, and perplexity **can't be computed** (we can't divide by zero).

### Example

Test sentence: `<s> I Sam </s>`. In training, *I* was followed by *am*, *am* and *do*, **never** by *Sam*. So C(I Sam) = 0.

| Bigram            | Probability    |
|-------------------|----------------|
| P(I \| `<s>`)     | 2/3            |
| P(Sam \| I)       | 0/3 = **0**    |
| P(`</s>` \| Sam)  | 1/2            |

**1. With multiplication:**

```
2/3 × 0 × 1/2 = 0
```

- Anything multiplied by 0 is 0, so the **whole sentence** gets probability 0.
- **Perplexity:** P^(−1/N) = 1 / ∛0 = **1 / 0**, which can't be computed.

**2. With logs:**

| Probability | log              |
|-------------|------------------|
| 2/3         | −0.405           |
| **0**       | **log(0) = ?**   |
| 1/2         | −0.693           |

- **log(0) has no value**: the smaller the probability, the more negative the log, going toward **minus infinity**. So logs don't save us from a zero either.

---

## 16. Smoothing: The Idea

- **Smoothing** (or **discounting**) = take a little probability from **frequent** events and give it to **unseen** events.
- So **nothing has probability exactly 0**.

| Method                  | Idea                                              |
|-------------------------|---------------------------------------------------|
| **Laplace (add-one)**   | Add 1 to every count                              |
| **Add-k**               | Add a small number k instead of 1                 |
| **Interpolation**       | Mix trigram, bigram and unigram probabilities     |
| **Stupid backoff**      | If the n-gram is unseen, use a shorter one        |

---

## Summary

| Question                       | Answer                                                              |
|--------------------------------|---------------------------------------------------------------------|
| **What is a language model?**  | A model that gives probabilities to next words and to sentences     |
| **What is an n-gram model?**   | It predicts a word from the n − 1 previous words (Markov assumption) |
| **How do we estimate it?**     | Count and divide: C(A B) / C(A)                                     |
| **Why logs?**                  | To avoid numbers too small for the computer                         |
| **How do we evaluate it?**     | Perplexity on a test set: lower is better                           |
| **What does it know?**         | Sampling shows it; it depends on the training corpus                |
| **What is the zero problem?**  | Unseen n-grams get probability 0                                    |
| **How do we fix it?**          | Smoothing: Laplace, add-k, interpolation, backoff                   |

**Road map of Chapters 2–3:** [From text to probabilities](../labs/demos/roadmap_ch2_ch3.html)

---

## Wrap-up

- A **language model** predicts the next word by giving a **probability** to every possible next word, and can score whole sentences.
- An **n-gram model** uses the **Markov assumption**: the next word depends only on the **n − 1 previous words**.
- Probabilities are estimated by **counting and dividing** (MLE): C(A B) / C(A).
- We compute in **log space** to avoid underflow.
- Models are evaluated on a separate **test set** with **perplexity**: the inverse probability per word, so **lower is better**.
- **Sampling** shows what a model has learned; more context gives better sentences, but a small corpus leads to **overfitting**.
- Unseen word **pairs** get probability **0** (the **zero problem**); **smoothing** fixes it.

---

## References

- Jurafsky & Martin, [*Speech and Language Processing*](https://web.stanford.edu/~jurafsky/slp3/), Chapter 3: N-gram Language Models, Sections 3.1–3.6 (pp. 37–54)
