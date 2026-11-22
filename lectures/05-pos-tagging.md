# Lecture 5: Part-of-Speech Tagging and Named Entities

**University of Hilla** · Dept. AI / NLP · 2026/27
**Assistant lecturer:** Hussein Mahdi Al-Rubaie
**Duration:** 90 minutes (theory) · **Date:** 11/01/2026

---

## Questions for today

By the end of this lecture, you will be able to answer:

1. What is **sequence labeling**?
2. What are **parts of speech**, and what are the **UD** and **Penn Treebank** tagsets?
3. What is **part-of-speech (POS) tagging**, and why is it **hard**?
4. What is **named entity recognition (NER)**, and what is **BIO tagging**?
5. What is a **Hidden Markov Model (HMM)**, and what are its parts **A**, **B** and **π**?
6. How do we **build A and B** from a tagged corpus?
7. How does the **Viterbi algorithm** find the best tags for a sentence?

---

## 1. Where We Stopped Last Lecture

- Last lecture: an **n-gram model** predicts the next word from the previous word(s).
- We used the **Markov assumption** and estimated probabilities by **counting and dividing**.
- **Today's question:** every word has **hidden information** behind it. Is *book* a noun or a verb? Is *Washington* a person or a place?
- The computer only sees the **words**. It must **infer** this hidden information.

*Today we use the same tools (Markov assumption + counting) to find this hidden information.*

---

## 2. Sequence Labeling

- **Sequence labeling** = give **one label to each word** in a sentence.
- Input: words x₁, x₂, …, xₙ. Output: labels y₁, y₂, …, yₙ (**same length**).

| Task                                | Labels                              | Example                    |
|-------------------------------------|-------------------------------------|----------------------------|
| **Part-of-speech (POS) tagging**    | Noun, verb, adjective, …            | *book* → NOUN or VERB      |
| **Named entity recognition (NER)**  | Person, location, organization, …   | *Washington* → PER or LOC  |

*First: what are parts of speech?*

---

## 3. Parts of Speech in Brief

- **Parts of speech (POS)** = word classes: noun, verb, adjective, adverb, …
- They are defined by **how a word behaves with its neighbors**, not only by its meaning.
- Two big groups:

| Group            | Membership                       | Examples                              | Note                                  |
|------------------|----------------------------------|---------------------------------------|---------------------------------------|
| **Open class**   | New words are added all the time | nouns, verbs, adjectives, adverbs     | *iPhone*, *to fax* are new words      |
| **Closed class** | Almost fixed                     | *the, of, and, can, she*              | Function words: short and very frequent |

*To tag words, we need an agreed list of tags: a **tagset**. We meet two.*

---

## 4. Two Tagsets

### Universal Dependencies (UD): 17 tags

- Designed to work for **many languages**.
- Simple, general tags:

| Group            | Tags                                                        |
|------------------|-------------------------------------------------------------|
| **Open class**   | NOUN, VERB, ADJ, ADV, PROPN (proper noun), INTJ             |
| **Closed class** | DET, ADP (preposition), AUX, PRON, NUM, CCONJ, SCONJ, PART  |
| **Other**        | PUNCT, SYM, X                                               |

> **In the book:** Fig. 17.1, the full list of the 17 UD tags with descriptions and examples (  17.1).

### Penn Treebank: 36 core tags (45 with punctuation)

- Designed for **English**; used to label many famous corpora (e.g. the **Wall Street Journal**, WSJ).
- **More detailed:** for example, it splits verbs by tense.

> **In the book:** Fig. 17.2, the full list of the Penn Treebank tags (  17.1).

The tags we use in the rest of this lecture:

| Tag     | Meaning                | Example        |
|---------|------------------------|----------------|
| **NNP** | Proper noun            | *Janet, IBM*   |
| **NN**  | Noun (singular)        | *bill, book*   |
| **NNS** | Noun (plural)          | *llamas*       |
| **VB**  | Verb (base form)       | *eat, back*    |
| **VBZ** | Verb (he/she form)     | *eats, plays*  |
| **VBD** | Verb (past)            | *ate*          |
| **MD**  | Modal verb             | *will, can*    |
| **JJ**  | Adjective              | *yellow*       |
| **RB**  | Adverb                 | *quickly*      |
| **DT**  | Determiner             | *a, the*       |
| **IN**  | Preposition            | *of, in*       |
| **PRP** | Personal pronoun       | *I, you, he*   |

### Same sentence, two tagsets

Format: word / UD tag / Penn tag.

```
There/PRON/EX are/VERB/VBP 70/NUM/CD children/NOUN/NNS there/ADV/RB ./PUNCT/.
```

- Penn is **more detailed**: *are* = VBP (present tense), *children* = NNS (plural).

**From now on, we use the Penn Treebank tagset.**

---

## 5. Part-of-Speech Tagging

- **POS tagging** = assigning a part of speech to **each word** in a text.

```
Input:   Janet  will  back  the  bill
Output:  NNP    MD    VB    DT   NN
```

> **In the book:** Fig. 17.3, a diagram of the tagger mapping each input word x₁…xₙ to a tag y₁…yₙ (  17.2).

### Why is it hard? Ambiguity

- Many words have **more than one possible tag**. The tagger must choose the right one **from the context**.

| Sentence               | *book* is a     |
|------------------------|-----------------|
| *Hand me that book*    | **Noun (NN)**   |
| *Book that flight*     | **Verb (VB)**   |

- The word *back* has **6** different tags:

| Sentence                                          | Tag     |
|---------------------------------------------------|---------|
| *earnings growth took a **back** seat*            | **JJ**  |
| *a small building in the **back***                | **NN**  |
| *a clear majority of senators **back** the bill*  | **VBP** |
| *Dave began to **back** toward the door*          | **VB**  |
| *enable the country to buy **back** debt*         | **RP**  |
| *I was twenty-one **back** then*                  | **RB**  |

### How much ambiguity?

- Remember **types** (different words) and **tokens** (all running words) from Lecture 2:

|            | Unambiguous (1 tag) | Ambiguous (2+ tags) |
|------------|---------------------|---------------------|
| **Types**  | 85–86%              | 14–15%              |
| **Tokens** | 33–45%              | **55–67%**          |

> **In the book:** Fig. 17.4, the exact counts for the WSJ and Brown corpora (  17.2).

- Most words in the dictionary have **one tag** (*Janet* is always NNP).
- But the ambiguous ones (*that, back, down, put, set*) are **very frequent**, so **more than half** of the words in a real text are ambiguous.

*So how well can a computer do?*

---

## 6. How Good Are Taggers?

| Method                                                                    | Accuracy |
|---------------------------------------------------------------------------|----------|
| **Most frequent tag baseline:** give each word the tag it had most often in training | ~92%     |
| **Good taggers** (HMM, CRF, BERT) and **humans**                          | **~97%** |

- **Baseline** = the simplest method. Any new tagger must **beat** it to be useful.
- **Lesson:** this is why we can learn tagging with a simple model like the **HMM**: it already reaches the top level.

### Why do we need POS tags?

| Use                 | How tags help                                                     |
|---------------------|-------------------------------------------------------------------|
| **Parsing**         | Knowing nouns and verbs helps build the sentence structure        |
| **Named entities**  | Proper nouns (NNP) are good candidates for names                  |
| **Text-to-speech**  | Pronunciation can depend on the tag (*record* as noun vs. verb)   |

*POS tagging tells us **Janet** is a proper noun. But is Janet a person, a place, or a company? That is NER.*

---

## 7. Named Entities and NER

- **Named entity** = anything referred to with a **proper name**: a person, a place, an organization.
- **Named entity recognition (NER)** = find the names in a text and **label their type**.

| Tag     | Type                   | Example                                  |
|---------|------------------------|------------------------------------------|
| **PER** | Person                 | *Turing*                                 |
| **ORG** | Organization           | *IPCC, United Airlines*                  |
| **LOC** | Location               | *Mt. Sanitas*                            |
| **GPE** | Geo-political entity   | *Palo Alto* (countries, cities, states)  |

### Why is NER harder than POS tagging?

| Problem            | Explanation                                                                  |
|--------------------|------------------------------------------------------------------------------|
| **Segmentation**   | A name can be **many words** (*United Airlines Holding*). Where does it start and end? |
| **Type ambiguity** | The **same name** can be **different types** (below)                          |

| Sentence                                       | *Washington* is   |
|------------------------------------------------|-------------------|
| *Washington was born into slavery …*           | **PER**           |
| *Washington went up 2 games to 1 …*            | **ORG** (a team)  |
| *Blair arrived in Washington …*                | **LOC**           |
| *In June, Washington passed a seatbelt law.*   | **GPE**           |

*How do we turn "find multi-word names" into one tag per word?*

---

## 8. BIO Tagging

- **BIO tagging** turns NER into a task with **one tag per word**, just like POS tagging.
- **The problem:** a name can span several words, so the model needs to know **where each name starts and ends**, not just its type.
- **The solution:** three kinds of tags:

| Tag             | Meaning                                          | Example        |
|-----------------|--------------------------------------------------|----------------|
| **B** (Begin)   | The **first** word of a name                     | B-PER, B-ORG   |
| **I** (Inside)  | Every **following** word of the same name        | I-PER, I-ORG   |
| **O** (Outside) | A word that **isn't part of any name**           | O              |

| Word | Jane  | Villanueva | of | United | Airlines | Holding | discussed | the | Chicago | route |
|------|-------|------------|----|--------|----------|---------|-----------|-----|---------|-------|
| Tag  | B-PER | I-PER      | O  | B-ORG  | I-ORG    | I-ORG   | O         | O   | B-LOC   | O     |

- **Number of tags: 2n + 1**, where n is the number of entity types.
- Each type gets a B and an I, and there is one shared O. With 4 types (PER, ORG, LOC, GPE), that's **9 tags**.

*Now: a model that can find these hidden tags.*

---

## 9. From Markov Chain to Hidden Markov Model

### Markov chain (what we already know)

- A **Markov chain** gives probabilities to a sequence of **states we can see**.
- **Markov assumption:** the next state depends **only on the current state**:

$$
P(q_i \mid q_1 \ldots q_{i-1}) = P(q_i \mid q_{i-1})
$$

- This is exactly the **bigram model** from last lecture, where the states are words.

> **In the book:** Fig. 17.8, two Markov chains: (a) for weather, (b) for words, with the transition probabilities on the arrows (  17.4.1).

### The problem: tags are hidden

- In a text we **see the words**, but we **don't see the tags**.
- A Markov chain on words can tell us P(*boy* | *the*), but it **cannot** tell us whether *boy* is a **noun**.
- So we need a model with **two layers**:

| Layer             | In POS tagging | Can we see it? |
|-------------------|----------------|----------------|
| **Hidden states** | The tags       | **No**         |
| **Observations**  | The words      | **Yes**        |

**Hidden Markov Model (HMM)** = a Markov chain over **hidden states**, where each state **emits** (produces) an **observation**.

---

## 10. The Parts of an HMM

| Symbol | Name                         | In POS tagging                                 |
|--------|------------------------------|------------------------------------------------|
| **Q**  | States                       | The set of tags (NN, VB, MD, …)                |
| **A**  | Transition probabilities     | P(tag \| previous tag), e.g. P(VB \| MD)       |
| **B**  | Emission probabilities       | P(word \| tag), e.g. P(*will* \| MD)           |
| **π**  | Initial probabilities        | P(tag \| start of sentence)                    |
| **V**  | Vocabulary                   | All words the model knows                      |
| **O**  | Observations                 | The words of the sentence we are tagging now   |

- **A** links **tag → tag**.
- **B** links **tag → word**.

> **In the book:** Fig. 17.9, an illustration of the A and B parts of an HMM for three tags (MD, VB, NN) (  17.4.3).

### Two simplifying assumptions

| Assumption               | Equation                                 | In words                                 |
|--------------------------|------------------------------------------|------------------------------------------|
| **Markov assumption**    | P(tᵢ \| t₁ … tᵢ₋₁) = P(tᵢ \| tᵢ₋₁)       | A tag depends only on the **previous tag** |
| **Output independence**  | P(wᵢ \| everything) = P(wᵢ \| tᵢ)        | A word depends only on **its own tag**     |

- The "window" is **one tag back** (a bigram over tags). A trigram HMM would look two tags back.

*Where do the numbers in A and B come from? From counting, like n-grams.*

---

## 11. Training: Building A and B by Counting

- We need a **tagged corpus**: text where humans have already written the correct tag for each word (e.g. the WSJ corpus).
- Then we **count and divide**, exactly like n-grams.

### Transition probabilities (A)

$$
P(t_i \mid t_{i-1}) = \frac{C(t_{i-1}, t_i)}{C(t_{i-1})}
$$

- How often is tag X followed by tag Y, out of all X?
- WSJ: MD appears **13,124** times; it is followed by VB **10,471** times:

```
P(VB | MD) = 10471 / 13124 = 0.80
```

- Makes sense: after *will / can / should* usually comes a base verb (*will go*).

### Emission probabilities (B)

$$
P(w_i \mid t_i) = \frac{C(t_i, w_i)}{C(t_i)}
$$

- Out of all MD tags, how often is the word *will*?

```
P(will | MD) = 4046 / 13124 = 0.31
```

- **Careful:** P(*will* | MD) is **not** "how likely is *will* to be MD". It means: **if the tag is MD**, how likely is the word to be *will* (and not *can*, *should*, …)?

### A tiny example

Tagged training corpus:

```
I/PRP     eat/VB    apples/NNS
You/PRP   eat/VB    bread/NN
They/PRP  like/VB   apples/NNS
```

| Probability          | Count              | Result            |
|----------------------|--------------------|-------------------|
| P(PRP \| start)      | 3 of 3 sentences   | 3/3 = **1**       |
| P(VB \| PRP)         | 3 of 3 PRP         | 3/3 = **1**       |
| P(NNS \| VB)         | 2 of 3 VB          | 2/3 = **0.67**    |
| P(NN \| VB)          | 1 of 3 VB          | 1/3 = **0.33**    |
| P(*eat* \| VB)       | 2 of 3 VB          | 2/3 = **0.67**    |
| P(*apples* \| NNS)   | 2 of 2 NNS         | 2/2 = **1**       |

### Two separate stages

| Stage        | Input                              | Output         | Algorithm    |
|--------------|------------------------------------|----------------|--------------|
| **Training** | Tagged corpus                      | A, B, π        | **Counting** |
| **Decoding** | A new untagged sentence + A, B, π  | The best tags  | **Viterbi**  |

- Viterbi **does not build** the model; it **uses** the model.

---

## 12. Decoding: What Are We Looking For?

- **Decoding** = find the **hidden tags** for a sentence we can see.
- We want the tag sequence with the **highest probability**. With the two assumptions, the score of a tag sequence is:

$$
\hat{t}_{1:n} = \underset{t_1 \ldots t_n}{\operatorname{argmax}} \prod_{i=1}^{n} \underbrace{P(w_i \mid t_i)}_{\text{emission (B)}} \; \underbrace{P(t_i \mid t_{i-1})}_{\text{transition (A)}}
$$

- **In words:** for each word, multiply "**does this word fit this tag?**" (B) × "**does this tag fit after the previous tag?**" (A). Choose the tag sequence with the **biggest product**.
- **argmax** = "the choice that gives the maximum".

### Why not try every tag sequence?

| Sentence             | Possible tag sequences   |
|----------------------|--------------------------|
| 5 words, 7 tags      | 7⁵ = **16,807**          |
| 20 words, 45 tags    | 45²⁰ ≈ **10³³**          |

- **Impossible** to try them all. Viterbi finds the best one **without trying them all**.

---

## 13. The Viterbi Algorithm

### The idea in one sentence

> For each word and each tag, keep **only the best path** that reaches it, then move to the next word.

- Like finding the shortest road between cities: if you know the best road to every city at step t − 1, you only need **one more step** to get the best road to every city at step t.
- This is **dynamic programming** (like minimum edit distance): **save partial results** instead of recomputing them.

### The lattice (table)

- **Columns** = the words. **Rows** = the possible tags.
- Each cell **v_t(j)** = the probability of the **best path** that ends in tag j at word t.

> **In the book:** Fig. 17.11, a sketch of the lattice for *Janet will back the bill*, with the correct path highlighted (  17.4.5).

### The equation

$$
v_t(j) = \max_{i} \; v_{t-1}(i) \times a_{ij} \times b_j(o_t)
$$

| Factor          | Meaning                                             | From          |
|-----------------|-----------------------------------------------------|---------------|
| **v_{t−1}(i)**  | Best path probability to tag i at the previous word | Last column   |
| **a_ij**        | Transition: P(tag j \| tag i)                       | Table **A**   |
| **b_j(o_t)**    | Emission: P(current word \| tag j)                  | Table **B**   |
| **max**         | Keep only the best previous tag i                   |               |

- **Why max?** We want the **best** path, not the sum of all paths.
- Each cell also stores a **backpointer**: which previous tag gave the max.

### Computing one column: max first, then × B

- **b_j(o_t)** does **not depend on i** (the previous tag), so we can take it **out of the max**:

$$
v_t(j) = b_j(o_t) \times \max_{i} \big[\, v_{t-1}(i) \times a_{ij} \,\big]
$$

- So a full column is computed in **three steps**:

| Step | What we compute | Shape |
|------|-----------------|-------|
| **1. Matrix** | v_{t−1}(i) × a_ij for every previous tag i (rows) and every current tag j (columns) | N × N **matrix** |
| **2. Max** | The biggest value in each column j (and **which row i** gave it → backpointer) | N **values** |
| **3. × B** | Multiply each max by the emission b_j(o_t) | N **values** = the new column v_t |

- **Same result**, but **fewer multiplications**: B is multiplied **N** times (once per tag), not **N²** times (once per pair).
- **Note:** B doesn't change **which** previous tag wins, because it is the **same number** for every i. It only scales the result.

### The three steps

| Step              | What we do                                                                          |
|-------------------|-------------------------------------------------------------------------------------|
| **1. Start**      | First column: v₁(j) = π_j × b_j(o₁) (start probability × emission)                  |
| **2. Fill**       | Every next column: apply the equation; save the backpointer                         |
| **3. Backtrace**  | In the last column take the **biggest** cell, then follow the backpointers back to the first word |

### Cost

- Each column: N tags × N previous tags. For T words: **N² × T**.
- 5 words, 7 tags: 7² × 5 = **245** steps, instead of **16,807** paths.

---

## 14. Worked Example: *Janet will back the bill*

*(Not required; we review and discuss it during the lecture.)*

- The model (A and B) was trained on the **WSJ corpus**.

> **In the book:**
> - Fig. 17.12, the transition probabilities **A** between the 7 tags, computed from the WSJ corpus (  17.4.6).
> - Fig. 17.13, the emission probabilities **B** for the 5 words of the sentence (  17.4.6).
> - The full Viterbi lattice for this sentence is worked through in the same section.

---

## 15. Limits of the HMM

| Problem                   | Example                                                | Fix                              |
|---------------------------|--------------------------------------------------------|----------------------------------|
| **Zero probabilities**    | A tag pair never seen in training → 0 kills the path   | **Smoothing** (Lecture 4)        |
| **Unknown words**         | A new name, not in B → emission 0                      | **UNK** token, or word features  |
| **No extra clues**        | Capital letter → likely NNP; ending *-ed* → likely past verb | Hard to add to an HMM      |

- Models that add such clues easily: **CRF** (next part of the chapter), then **neural** taggers (RNNs, Transformers).

---

## 16. How It All Connects

| Technique    | Works on                  | Job                                       |
|--------------|---------------------------|-------------------------------------------|
| **BPE**      | Characters → subwords     | Cut words into known pieces               |
| **N-gram**   | Words                     | P(next word \| previous word)             |
| **HMM**      | Tags + words              | P(tag \| previous tag) and P(word \| tag) |
| **Viterbi**  | The HMM lattice           | Find the best tag sequence                |

- The HMM **transitions** are a **bigram model over tags** instead of words.

---

## Summary

| Question                        | Answer                                                                 |
|---------------------------------|------------------------------------------------------------------------|
| **What is sequence labeling?**  | One label for each word; output length = input length                  |
| **Which tagset do we use?**     | Penn Treebank (UD = 17 general tags for many languages)                |
| **Why is POS tagging hard?**    | Ambiguity: 55–67% of words in a text have 2+ possible tags             |
| **What is NER?**                | Find names and label their type (PER, ORG, LOC, GPE)                   |
| **What is BIO?**                | B = begin, I = inside, O = outside; NER becomes one tag per word       |
| **What is an HMM?**             | Hidden states (tags) that emit observations (words)                    |
| **A and B?**                    | A = P(tag \| previous tag), B = P(word \| tag); both by counting       |
| **What does Viterbi do?**       | Finds the best tag sequence: keep the best path to each cell, then backtrace |

---

## Wrap-up

- **POS tagging** and **NER** are **sequence labeling** tasks: one tag per word.
- We use the **Penn Treebank** tagset; **UD** is a simpler, multilingual tagset.
- Tagging is hard because frequent words are **ambiguous**; good taggers reach **~97%**, the baseline **~92%**.
- **NER** finds names and their types; **BIO** tags turn it into one tag per word.
- An **HMM** has **hidden** tags and **visible** words, with **transition (A)** and **emission (B)** probabilities, learned by **counting** a tagged corpus.
- **Viterbi** fills a table column by column with **v = previous × A × B**, keeps the **max** and a **backpointer**, then **backtraces** the best path.

---

## References

- Jurafsky & Martin, [*Speech and Language Processing*](https://web.stanford.edu/~jurafsky/slp3/), Chapter 17: Sequence Labeling for Parts of Speech and Named Entities, Sections 17.1–17.4 (pp. 378–392)
