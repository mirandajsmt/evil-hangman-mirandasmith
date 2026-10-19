# Evil Hangman

A Python word-maker that plays an *evil* version of Hangman. Instead of picking a secret word up front, it keeps the set of all words that are consistent with the guesses so far and picks the word as late as possible, always responding in the way that leaves the guesser with the hardest, largest set of remaining possibilities. Built for **DSC 395T: Algorithms and Data Structures** at UT Austin.

## How it works

When the guesser picks a letter, the word-maker splits the remaining words into **families** based on exactly where that letter appears. For example, if the guesser picks `E` and the dictionary of 4-letter words is `ALLY BETA COOL DEAL ELSE FLEW GOOD HOPE IBEX`, the families are:

| Pattern | Words |
|---------|-------|
| `----`  | ALLY, COOL, GOOD |
| `-E--`  | BETA, DEAL |
| `--E-`  | FLEW, IBEX |
| `E--E`  | ELSE |
| `---E`  | HOPE |

The program then commits to the **largest family**, which here is `----`, so the guess is marked wrong. Ties are broken by:

1. Preferring the family that reveals the fewest letters.
2. Preferring the no-letter family (`----`) above all else, since it costs the guesser a strike.
3. Otherwise, picking either family.

Families are keyed by letter *position*, not just letter count, so `BEER` and `HERE` land in different families even though both contain two E's.

## Implementation

The core of the project is the `WordMakerAI` class in `py_evil_hangman/word_maker.py`:

- `reset(word_length)`: starts a new game. The dictionary is preprocessed up front so resetting takes O(1) time.
- `guess(letter)`: groups the remaining words into families using hash tables, keeps the best family, and returns the sorted list of positions where the letter appears (`[]` if it doesn't appear).
- `get_letter_positions_in_word(word, letter)`: returns the positions of a letter in a single word as a sorted tuple.
- `get_amount_of_valid_words()`: returns how many words are still possible (shown in verbose mode).
- `get_valid_word()`: returns one remaining valid word, revealed to the guesser at the end of the game.

The code only builds the families that actually occur in the dictionary rather than enumerating all 2^n possible letter placements.

## Concepts practiced

- Hash tables (`dict`) for grouping and fast lookup
- Choosing data structures for efficiency
- Preprocessing data to speed up repeated operations
- Greedy decision-making with tie-breaking rules
- Working within and extending existing starter code

## Project structure

```
.
├── hangman.py                    # Game entry point
├── dictionary.txt                # Word list (one word per line)
└── py_evil_hangman/
    ├── word_maker.py             # Human word-maker and WordMakerAI (the cheating agent)
    └── word_guesser.py           # Word-guesser logic
```

## Getting started

**Requirements:** Python 3.10+. No third-party packages are used beyond the standard library (and `pytest` for testing).

```bash
git clone https://github.com/mirandajsmt/evil-hangman-mirandasmith.git
cd evil-hangman-mirandasmith
```

### Running the game

The project includes two PyCharm run configurations: **Hangman** (a normal game against a human word-maker) and **Evil Hangman** (a game against the cheating `WordMakerAI`).

Useful command-line flags:

| Flag | Description |
|------|-------------|
| `-v` | Verbose mode: shows how many valid words remain after each guess |
| `-f <file>` | Use a custom dictionary file (one word per line) |
| `-g <n>` | Set the number of guesses the guesser gets |
| `-k` | Use the `WordGuesserAI` (optional karma challenge) |

## Acknowledgments

This assignment was derived from the original Evil Hangman assignment by Keith Schwarz, and modified by Matthew Giordano and Calvin Lin for DSC 395T at UT Austin. The starter code comes from the course; the implementation here is my own.
