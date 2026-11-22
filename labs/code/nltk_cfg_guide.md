# NLTK CFG Quick Guide

A short reference for writing context-free grammars, parsing sentences, and working with parse trees in NLTK.
Tested with NLTK 3.10.

---

## 1. Install and import

```powershell
pip install nltk
```

```python
import nltk
from nltk import CFG, Tree, Nonterminal
```

---

## 2. Writing a grammar

Put the grammar in a text string and pass it to `CFG.fromstring`.

```python
grammar = CFG.fromstring("""
S -> NP VP
NP -> Pronoun | Det Noun
VP -> Verb NP
Pronoun -> 'I'
Det -> 'a' | 'the'
Noun -> 'flight' | 'book'
Verb -> 'prefer'
""")
```

### Syntax rules

| Rule | Example |
|---|---|
| One rule per line | `S -> NP VP` |
| Arrow is `->` (not →) | `NP -> Pronoun` |
| Symbols separated by spaces | `VP -> Verb NP` |
| Non-terminals: no quotes | `NP`, `Verb` |
| Terminals (words): single quotes | `'flight'` |
| Alternatives with `\|` | `Det -> 'a' \| 'the'` |
| No hyphens in names | `ProperNoun`, not `Proper-Noun` |
| First rule's left side = start symbol | put `S -> ...` first |
| Recursion is allowed | `Nominal -> Nominal Noun` |

---

## 3. Inspecting a grammar

| Code | Returns |
|---|---|
| `print(grammar)` | Start symbol and all rules |
| `grammar.start()` | Start symbol, e.g. `S` |
| `grammar.productions()` | List of all rules |
| `grammar.productions(lhs=Nonterminal('NP'))` | Only the rules for `NP` |
| `grammar.is_chomsky_normal_form()` | `True` / `False` |

### All `CFG` methods

| Group | Method | What it does |
|---|---|---|
| Create | `CFG.fromstring(text)` | Builds a grammar from text |
| Read | `start()` | Start symbol |
| | `productions(lhs=..., rhs=...)` | All rules, or only those with a given left/right side |
| Check | `check_coverage(words)` | Error if a word is not in the lexicon |
| | `is_chomsky_normal_form()` | Is every rule `A -> B C` or `A -> 'w'`? |
| | `is_binarised()` | Does every rule have at most 2 symbols on the right? |
| | `is_lexical()` / `is_nonlexical()` | Do all rules contain words / no rule mixes words and symbols? |
| | `min_len()` / `max_len()` | Shortest / longest right side |
| Convert (CNF) | `chomsky_normal_form()` | Returns a new grammar in CNF (all steps below together) |
| | `remove_unitary_rules()` | Removes unit rules like `NP -> Pronoun` |
| | `binarize()` | Splits long rules: `A -> B C D` into `A -> B X`, `X -> C D` |
| | `remove_mixed_rules()` | Separates words from symbols in the same rule |
| | `eliminate_start()` | Makes sure S never appears on a right side |
| Advanced | `leftcorners()`, `leftcorner_parents()`, `is_leftcorner()`, `is_flexible_chomsky_normal_form()`, `is_nonempty()` | Used inside parsers; not needed for this course |

`chomsky_normal_form()` performs the same conversion steps as the CKY section of Chapter 18 (§18.6.1):

```python
cnf_grammar = grammar.chomsky_normal_form()
print(cnf_grammar)
print(cnf_grammar.is_chomsky_normal_form())   # True
```

New symbols it creates look like `VP@$@Verb`; these are the dummy non-terminals (like `X1` in the book).

Each rule (production) has:

| Code | Returns |
|---|---|
| `rule.lhs()` | Left side, e.g. `NP` |
| `rule.rhs()` | Right side as a tuple, e.g. `(Det, Noun)` |
| `rule.is_lexical()` | `True` if the rule contains a word |

---

## 4. Generating sentences

Lists sentences the grammar can derive from `S`.

```python
from nltk.parse.generate import generate

for words in generate(grammar, n=5):   # first 5 sentences
    print(" ".join(words))
```

Use `n=` (and `depth=` for recursive grammars), or it may run forever.

---

## 5. Parsing a sentence

```python
parser = nltk.ChartParser(grammar)
words = "I prefer a flight".split()      # sentence -> list of words
trees = list(parser.parse(words))        # all parse trees

print(len(trees))                        # number of trees
for t in trees:
    t.pretty_print()
```

| Number of trees | Meaning |
|---|---|
| 0 | Ungrammatical: the grammar cannot derive it |
| 1 | One structure |
| 2 or more | Ambiguous |

`parser.parse()` returns an iterator, so wrap it in `list(...)` before counting.

---

## 6. Working with trees

### Create a tree from bracketed notation

NLTK uses round brackets `( )`, not square ones.

```python
t = Tree.fromstring("(S (NP (Pronoun I)) (VP (Verb prefer) (NP (Det a) (Noun flight))))")
```

### Display

| Code | Result |
|---|---|
| `t.pretty_print()` | Tree drawn as text |
| `t.draw()` | Tree in a pop-up window |
| `print(t)` | Indented bracketed form |
| `t.pformat(margin=1000)` | Bracketed form on one line |

### Read parts of a tree

| Code | Returns |
|---|---|
| `t.label()` | Root label, e.g. `S` |
| `t.leaves()` | The words: `['I', 'prefer', 'a', 'flight']` |
| `t[0]`, `t[1]` | First / second child (subtree) |
| `t[1, 0]` | First child of the second child |
| `[c.label() for c in t]` | Labels of the children (immediately dominated) |
| `t[1].leaves()` | Words dominated by the second child |
| `t.height()` | Height of the tree |
| `t.productions()` | The rules used to build the tree |
| `t.subtrees()` | Every subtree (node) in the tree |

### Chomsky Normal Form on a tree

```python
cnf = t.copy(deep=True)
cnf.chomsky_normal_form()        # binarize: adds dummy nodes like VP|<NP-PP>
cnf.un_chomsky_normal_form()     # remove dummy nodes, restore original
```

---

## 7. Penn Treebank sample

```python
nltk.download('treebank')        # once
from nltk.corpus import treebank

trees = treebank.parsed_sents()
trees[0].pretty_print()
```

---

## 8. Common errors

| Error | Cause | Fix |
|---|---|---|
| `ValueError: Grammar does not cover some of the input words: 'like'` | A word in the sentence is not in the lexicon | Add it, e.g. `Verb -> 'like'` |
| `ValueError: Unable to parse line` | Wrong syntax in the grammar string | Check `->`, quotes, no hyphens |
| 0 trees, no error | Words are known, but no rule combination fits | Missing rule, or the sentence is ungrammatical |
| Word not found although it is in the grammar | Capital letters differ | `'Book'` ≠ `'book'`; match the case |
| `ModuleNotFoundError: No module named 'nltk'` | Editor uses a different Python | Select the Python where NLTK is installed |
| `LookupError: Resource treebank not found` | Data not downloaded | `nltk.download('treebank')` |



