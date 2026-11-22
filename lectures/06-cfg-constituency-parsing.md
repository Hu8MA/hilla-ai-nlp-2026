# Lecture 6: Context-Free Grammars and Constituency Parsing

**University of Hilla** · Dept. AI / NLP · 2026/27
**Assistant lecturer:** Hussein Mahdi Al-Rubaie
**Duration:** 90 minutes (theory) · **Date:** 11/22/2026

---

## Questions for today

By the end of this lecture, you will be able to answer:

1. What is **constituency**, and what is **constituency parsing**?
2. What are the parts of a **context-free grammar (CFG)**: rules, lexicon, terminals and non-terminals?
3. How does a grammar **generate** a sentence (**derivation**), and how do we draw it (**parse tree**, **bracketed notation**)?
4. What makes a sentence **grammatical** or **ungrammatical** for a grammar?
5. What is the **formal definition** of a CFG?
6. What is a **treebank**?
7. What is **structural ambiguity**, and what is **syntactic disambiguation**?

---

## 1. Where We Stopped Last Lecture

- **Part-of-speech (POS) tagging** assigns grammatical labels to **individual words**: nouns, verbs, adjectives, adverbs.
- It helps the computer identify the **grammatical role** of each word in a sentence.
- **Today:** we move beyond single words, to how words **combine** to form **phrases and sentences**.
- We use **grammatical rules** to find the **structure** of a sentence.

### Different approaches to language modeling

| Approach               | Example models    | Idea                                                       |
|------------------------|-------------------|------------------------------------------------------------|
| **Statistical**        | N-grams           | Use probabilities to predict the next word                 |
| **Grammar-based**      | CFG → PCFG        | Use grammatical rules to describe sentence structure       |
| **Neural**             | Transformers      | Learn linguistic patterns and context from data            |

*Today we move from statistical models to grammar-based models. First: what is constituency?*

---

## 2. What is Constituency?

- **Constituency** = the way words **group together** to form meaningful grammatical units called **constituents**.
- A **constituent** is a word or group of words that **functions as a single unit** within a sentence.
- These groups form **phrases**, such as **noun phrases (NPs)**.

Example: *The young student reads a book.*

| Group                  | Type                    |
|------------------------|-------------------------|
| *The young student*    | **Noun phrase (NP)**    |
| *a book*               | **Noun phrase (NP)**    |
| *reads a book*         | **Verb phrase (VP)**    |

- Each group performs a **specific grammatical function** within the sentence.

---

## 3. What is Constituency Parsing?

- **Constituency parsing** (syntactic parsing) = taking a sentence and **finding its parse tree** using a grammar.
- **Input:** a sentence and a CFG.
- **Output:** one or more **parse trees** showing how the words group into constituents (NP, VP, PP, …).

```
"I prefer a morning flight"  →  [ Parser + CFG ]  →  Parse tree
```

### Generation vs. parsing

| Direction       | Starts from      | Produces       |
|-----------------|------------------|----------------|
| **Derivation**  | S                | The sentence   |
| **Parsing**     | The sentence     | Its tree       |

**Key idea:** the grammar says which structures are **allowed**. The parser finds which of those structures **fit** the sentence.

---

## 4. Why is Constituency Important in NLP?

- It helps NLP systems understand the **internal structure** of sentences, rather than treating words as separate elements.
- Example: in *The young student reads a book*, the sentence splits into an NP (*the young student*) and a VP (*reads a book*).

**Main idea:** constituency parsing moves computers from identifying **individual words** to analyzing how words are **organized into phrases and sentences**, a foundation for **semantic analysis**.

*But how do we know words really form groups?*

---

## 5. Why Can't We Divide a Noun Phrase into Random Words?

> *Three students from Baghdad arrive.*

- The phrase *Three students from Baghdad* functions as **one grammatical unit** (a constituent).
- It can appear **together before the verb**.
- Its words are connected and form a **single grammatical phrase**.
- Dividing it into **random parts** may produce ungrammatical structures.

| Expression                              | Result        | Explanation                                         |
|-----------------------------------------|---------------|-----------------------------------------------------|
| *Three students from Baghdad arrive.*   | **Correct**   | Complete sentence                                   |
| *Three students arrive.*                | **Correct**   | A valid sentence, with a shorter noun phrase        |
| *Students from Baghdad arrive.*         | **Correct**   | A valid noun phrase before the verb                 |
| \**From Baghdad arrive.*                | **Incorrect** | Not a complete noun phrase in this context          |
| \**Three from arrive.*                  | **Incorrect** | Random words do not form a grammatical unit         |
| \**Students the arrive.*                | **Incorrect** | Incorrect word order and structure                  |

- The **asterisk (\*)** marks an ungrammatical expression.

**Key idea:** words do **not** combine randomly. They form structured groups called **constituents**.

> **In the book:** §18.1 gives the same test with *three parties from Brooklyn arrive* (p. 404).

---

## 6. What Does Constituency Have to Do with NLP?

> *I saw the man with a telescope.*

| Meaning                                       | What *with a telescope* belongs to |
|-----------------------------------------------|------------------------------------|
| *I used a telescope to see the man.*          | The **seeing** (the verb)          |
| *I saw the man who was carrying a telescope.* | **The man** (the noun)             |

- Same words, **two structures**, two meanings. To understand the sentence, a computer must know **how the words group**.

*We come back to this sentence in the ambiguity part. First, the tool that describes structure: the CFG.*

---

## 7. Components of a Context-Free Grammar

- A **context-free grammar (CFG)** is a set of grammatical rules that describes how words and phrases **combine to form sentences**.
- Unlike approaches that focus on individual words or their probabilities, a CFG focuses on the **structural organization** of language.

### 1. Grammar rules

- Sentences are built from **smaller grammatical units**.
- **Words** combine to form **phrases**.
- **Phrases** combine to form **larger structures**.
- **Grammatical rules** describe how these combinations occur.

### 2. Lexicon

- Contains the **words** used by the grammar.
- Associates words with their **grammatical categories**:

```
Det  → a | the
Noun → flight | book
```

**Key idea:** grammar rules define how **structures** are formed, while the lexicon provides the **words** used in those structures.

---

## 8. Terminal and Non-Terminal Symbols

| Type                   | What it is                                  | Expanded by rules? | Examples                    |
|------------------------|---------------------------------------------|--------------------|-----------------------------|
| **Terminal**           | An **actual word**; appears in the final sentence | No           | *the, flight, student, reads* |
| **Non-terminal**       | A **grammatical category** or abstraction   | Yes                | S, NP, VP, Noun, Det        |

```
NP   → Det Noun      (non-terminals only)
Det  → a             (non-terminal → terminal)
Noun → flight        (non-terminal → terminal)
```

**Important:** terminals are the words that appear in the final sentence; non-terminals represent grammatical structures.

---

## 9. Start Symbol and Phrase Structure Rules

- A CFG uses a designated **start symbol**, usually **S**, from which sentence generation begins.

| Symbol  | Meaning                    |
|---------|----------------------------|
| **S**   | Sentence                   |
| **NP**  | Noun phrase                |
| **VP**  | Verb phrase                |
| **PP**  | Prepositional phrase       |

### Understanding the rules

| Rule                     | Meaning                                                       |
|--------------------------|---------------------------------------------------------------|
| **S → NP VP**            | A sentence is a noun phrase followed by a verb phrase         |
| **VP → Verb NP**         | A verb phrase can be a verb followed by a noun phrase         |
| **VP → Verb NP PP**      | A verb phrase can be a verb, a noun phrase and a prepositional phrase |
| **VP → Verb PP**         | A verb phrase can be a verb followed by a prepositional phrase |
| **PP → Preposition NP**  | A prepositional phrase is a preposition followed by a noun phrase |

*The textbook puts these rules together into a small grammar called L₀.*

---

## 10. Lexicon and Grammar Rules: L₀

- **L₀** is a simple grammar for the **ATIS** language (questions about air travel).

### Lexicon (L₀)

| Category              | Example words                   |
|-----------------------|---------------------------------|
| **Noun**              | *flights, flight, trip, morning* |
| **Verb**              | *is, prefer, like, need, fly*   |
| **Adjective**         | *cheapest, non-stop, latest*    |
| **Pronoun**           | *me, I, you, it*                |
| **Proper noun**       | *Alaska, Chicago, United*       |
| **Determiner (Det)**  | *the, a, an, this*              |
| **Preposition**       | *from, to, on, in*              |
| **Conjunction**       | *and, or, but*                  |

> **In the book:** Fig. 18.2, the full lexicon for L₀ (p. 406).

### Grammar rules (L₀)

```
S       → NP VP
NP      → Pronoun | Proper-Noun | Det Nominal
Nominal → Nominal Noun | Noun
VP      → Verb | Verb NP | Verb NP PP | Verb PP
PP      → Preposition NP
```

> **In the book:** Fig. 18.3, the grammar for L₀ with an example phrase for each rule (p. 407).

- The **lexicon** lists **word categories** (8). The **grammar rules** build **phrase categories** (5: S, NP, Nominal, VP, PP).
- *Adjective* and *Conjunction* are in the lexicon, but L₀ has **no rule that uses them yet**.

**Key idea:** L₀ combines vocabulary with grammatical rules to describe how sentences in a simplified ATIS language are formed.

---

## 11. How Does a CFG Generate Sentences?

- Start with **S**, then **repeatedly apply rules** until only words (terminals) remain.
- This sequence of rule applications is called a **derivation**.

Example: *I prefer a morning flight*

| Step | Rule applied              | Resulting string               |
|------|---------------------------|--------------------------------|
| 0    | (start)                   | S                              |
| 1    | S → NP VP                 | NP VP                          |
| 2    | NP → Pronoun              | Pronoun VP                     |
| 3    | Pronoun → I               | I VP                           |
| 4    | VP → Verb NP              | I Verb NP                      |
| 5    | Verb → prefer             | I prefer NP                    |
| 6    | NP → Det Nominal          | I prefer Det Nominal           |
| 7    | Det → a                   | I prefer a Nominal             |
| 8    | Nominal → Nominal Noun    | I prefer a Nominal Noun        |
| 9    | Nominal → Noun            | I prefer a Noun Noun           |
| 10   | Noun → morning            | I prefer a morning Noun        |
| 11   | Noun → flight             | **I prefer a morning flight**  |

*A long list of steps is hard to read. A tree shows the same thing at a glance.*

---

## 12. Parse Trees

- A **parse tree** represents the derivation as a **hierarchical structure**.

```
                S
     ___________|____
    |                VP
    |      __________|____
    |     |               NP
    |     |      _________|______
    |     |     |              Nominal
    |     |     |          _______|_____
    NP    |     |      Nominal          |
    |     |     |         |             |
 Pronoun Verb  Det       Noun          Noun
    |     |     |         |             |
    I   prefer  a      morning        flight
```

> **In the book:** Fig. 18.4, the parse tree for *I prefer a morning flight* according to L₀ (p. 407).

### Tree relationships

| Term                      | Meaning                                    | Example                                                   |
|---------------------------|--------------------------------------------|-----------------------------------------------------------|
| **Root**                  | The top node                               | **S**                                                     |
| **Immediately dominates** | Connected **directly** by one line         | S immediately dominates NP and VP                         |
| **Dominates**             | Everything **below** it in its subtree     | VP dominates *prefer a morning flight*, but not *I*       |

---

## 13. Bracketed Notation

- Another way to write a parse tree, in a **compact text form**:

```
[S [NP [Pro I]] [VP [V prefer] [NP [Det a] [Nom [N morning] [Nom [N flight]]]]]]
```

Written with one level per line:

```
[S
  [NP [Pro I]]
  [VP
    [V prefer]
    [NP
      [Det a]
      [Nom
        [N morning]
        [Nom [N flight]]
      ]
    ]
  ]
]
```

### How to read it

- Every **[** opens a node, and its matching **]** closes it.
- The **first label** inside the brackets is the node's name.
- **Nested brackets** show smaller constituents inside larger ones.

**Key idea:** bracketed notation expresses the **same hierarchy** as a parse tree, in a more compact text form.

---

## 14. Grammatical vs. Ungrammatical Sentences

- In §5 we used our **intuition** to judge sentences. Now the **grammar** decides.

| Type                     | Definition                                   | Example                          |
|--------------------------|----------------------------------------------|----------------------------------|
| **Grammatical**          | The grammar's rules **can** derive it from S | *I prefer a morning flight*      |
| **Ungrammatical (\*)**   | The grammar's rules **can't** derive it      | \**I prefer morning a flight*    |

- **Generative grammar:** the grammar **generates** the language. A sentence is grammatical **if and only if** the grammar's rules can derive it from S.
- **Limitation:** natural language is more complex than any formal grammar. Whether a sentence is acceptable can also depend on **context and meaning**.

**Key idea:** §5 asked whether a sentence **sounds** right. Here we ask whether the grammar can **derive** it.

---

## 15. Formal Definition of a CFG

A context-free grammar **G** is a **4-tuple**:

$$
G = (N, \Sigma, R, S)
$$

| Symbol | Meaning                                                                        |
|--------|--------------------------------------------------------------------------------|
| **N**  | Set of **non-terminal** symbols (variables)                                    |
| **Σ**  | Set of **terminal** symbols, disjoint from N                                   |
| **R**  | Set of **rules** of the form A → β, where A is a non-terminal and β is a string of terminals and/or non-terminals |
| **S**  | The designated **start symbol**, a member of N                                 |

---

## 16. Derivation (Formal)

| Term                       | Meaning                    | Example                                                  |
|----------------------------|----------------------------|----------------------------------------------------------|
| **Directly derives (⇒)**   | Apply **one** rule         | NP VP ⇒ Pronoun VP (using NP → Pronoun)                  |
| **Derives (⇒\*)**          | Apply a **sequence** of rules | S ⇒\* I prefer a morning flight (our 11-step table)   |

### The language of a grammar

$$
\mathcal{L}_G = \{\, w \mid w \text{ is in } \Sigma^* \text{ and } S \overset{*}{\Rightarrow} w \,\}
$$

- All strings of words that S can derive.

**Key idea:** a sentence is grammatical if **S ⇒\* sentence**. This is what **generative grammar** means.

*So far we wrote trees by hand. Real systems learn from large collections of trees.*

---

## 17. Treebanks

- A **treebank** is a corpus in which **every sentence** is annotated with a **parse tree**.
- Treebanks are used in **parsing** and **linguistic research**.
- They are usually created by **automatic parsing**, followed by **human correction**.
- Trees can be stored in **bracketed notation** or drawn as **tree diagrams**.
- Example: the **Penn Treebank**, with annotated sentences from different sources and languages (English, Arabic, Chinese).

> **In the book:**
> - Fig. 18.5, Penn Treebank parses in bracketed notation for (a) a Brown corpus sentence and (b) an ATIS sentence (p. 409).
> - Fig. 18.6, the same Brown sentence drawn as a tree (p. 409).

### Why are the labels different?

- In our examples we used **simplified** labels: NP, VP, PP, S.
- The Penn Treebank uses a **more detailed** scheme, with specific labels for **parts of speech** (NN, VBD, …) and **grammatical functions** (e.g. NP-SBJ for a subject).

*A grammar can give one sentence more than one tree. This is the biggest problem for parsers.*

---

## 18. Ambiguity

- **Ambiguity** = a word, phrase or sentence has **more than one possible interpretation**.
- In parsing, ambiguity means a sentence can have **multiple possible parse trees**.

> *I saw the man with a telescope.*

| Interpretation                     |
|------------------------------------|
| I used a telescope to see the man. |
| The man had a telescope.           |

### Structural ambiguity

- **Structural ambiguity** = a sentence can have **more than one syntactic parse tree**, leading to different interpretations.

> *I shot an elephant in my pajamas.*

| Interpretation                                         | *in my pajamas* attaches to |
|--------------------------------------------------------|-----------------------------|
| The elephant was wearing my pajamas.                   | *an elephant* (the NP)      |
| I was wearing my pajamas when I shot the elephant.     | *shot* (the VP)             |

> **In the book:** Fig. 18.9, the two parse trees for this sentence side by side (p. 412).

---

## 19. Types of Structural Ambiguity

### 1. Attachment ambiguity

- A phrase can be **attached to more than one position** in a parse tree.
- *I saw the man with a telescope*: the PP attaches to the **verb** (I used a telescope) or to the **noun** (the man had one).
- **PP-attachment ambiguity** is a specific type involving **prepositional phrases (PP)**.

### 2. Coordination ambiguity

- A coordinated phrase can have **more than one possible structure**.
- *Old men and women*:

| Grouping                    | Meaning                         |
|-----------------------------|---------------------------------|
| [old [men and women]]       | Both men and women are old      |
| [[old men] and women]       | Only the men are old            |

| Type             | In one line                                         |
|------------------|-----------------------------------------------------|
| **Attachment**   | **One phrase**, different **parents** in the tree   |
| **Coordination** | **Same words**, different **grouping** around *and* |

---

## 20. Syntactic Disambiguation

- Structural ambiguity can produce **multiple parse trees** for the same sentence.
- **Syntactic disambiguation** = selecting the **most appropriate** structure among the possible parses.
- A parser may generate several trees. Some are **grammatically correct** but **semantically unreasonable** (the elephant in my pajamas).
- Parsing algorithms such as **CKY** help handle structural ambiguity **efficiently**.

**Key idea:** the goal is to identify the **most appropriate parse tree** for a sentence.

---

## Summary

| Question                              | Answer                                                                 |
|---------------------------------------|------------------------------------------------------------------------|
| **What is constituency?**             | Words group into units (NP, VP, PP) that act as a single unit         |
| **What is a CFG?**                    | G = (N, Σ, R, S): rules and a lexicon that describe how constituents are built |
| **Derivation and parse tree?**        | Applying rules from S produces a sentence; a tree (or brackets) shows its structure |
| **When is a sentence grammatical?**   | When S ⇒\* sentence (generative grammar)                               |
| **What is a treebank?**               | A corpus of sentences annotated with parse trees (e.g. Penn Treebank)  |
| **What is structural ambiguity?**     | One sentence, several parse trees (attachment or coordination)         |
| **What is syntactic disambiguation?** | Choosing the best parse tree                                           |

---

## Quiz

**Q1.** A constituent can be any sequence of words, even random ones. *(True / False)*

**Q2.** In the rule NP → Det Nominal, which symbols are non-terminals?
- a) Only NP
- b) NP, Det and Nominal
- c) Only Det
- d) None of them

**Q3.** A sentence is grammatical according to a CFG if it can be derived from the start symbol S. *(True / False)*

**Q4.** *"I saw the man with a telescope"* is an example of:
- a) Coordination ambiguity
- b) POS ambiguity
- c) Attachment ambiguity
- d) No ambiguity

**Q5.** What is a treebank?
- a) A list of grammar rules
- b) A corpus where each sentence is annotated with a parse tree
- c) A dictionary of words and their meanings
- d) An algorithm for parsing sentences

<details>
<summary><b>Answer key</b></summary>

| Q | Answer    | Why                                                                                         |
|---|-----------|---------------------------------------------------------------------------------------------|
| 1 | **False** | Constituents are structured groups. \**From arrive* is not one.                             |
| 2 | **b**     | Det is a word category, and the lexicon expands it (Det → a), so it is still a non-terminal. Only actual words are terminals. |
| 3 | **True**  | This is the generative grammar idea: S ⇒\* sentence.                                        |
| 4 | **c**     | The PP *with a telescope* can attach to the VP or to the NP.                                |
| 5 | **b**     | For example, the Penn Treebank.                                                             |

</details>

---

## Wrap-up

- **Constituents** are groups of words that act as a single unit (NP, VP, PP).
- A **CFG** G = (N, Σ, R, S) uses **rules** and a **lexicon** to describe how constituents are built.
- A **derivation** applies rules from **S** until only words remain; a **parse tree** or **bracketed notation** shows the structure.
- A sentence is **grammatical** if **S ⇒\* sentence** (generative grammar).
- **Treebanks** are corpora of sentences annotated with parse trees, like the **Penn Treebank**.
- **Structural ambiguity** gives one sentence several trees (**attachment** or **coordination**); **syntactic disambiguation** chooses the best one.
- **Next lecture:** the **CKY algorithm**, which finds all parse trees efficiently.

---

## References

- Jurafsky & Martin, [*Speech and Language Processing*](https://web.stanford.edu/~jurafsky/slp3/), Chapter 18: Context-Free Grammars and Constituency Parsing, Sections 18.1–18.5 (pp. 403–412)
