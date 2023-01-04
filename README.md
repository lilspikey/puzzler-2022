# Puzzler 2022

## Wordle

```
$ ./wordle --help
usage: wordle [-h] [--doc-test] [--benchmark BENCHMARK] [--guesser {interactive,automatic}]
              [--chooser {random,overlap,likelihood}] [--use-wordle-answers] [--verbose]

optional arguments:
  -h, --help            show this help message and exit
  --doc-test
  --benchmark BENCHMARK
  --guesser {interactive,automatic}
  --chooser {random,overlap,likelihood}
  --use-wordle-answers
  --verbose
```

### Interactive use

`./wordle` can be run to play a game of wordle interactively.

```
./wordle
>alien
1 ⬜⬜🟨⬜🟨
>nicks
2 🟨🟩⬜⬜🟩
>tines
3 🟨🟩🟩⬜🟩
>mi
invalid guess
>minnn
not in word list
>mints
4 ⬜🟩🟩🟩🟩
>lints
5 ⬜🟩🟩🟩🟩
>pints
6 ⬜🟩🟩🟩🟩
hints
```

### Automatic use

`./wordle` can also be run in "automatic" mode where it will generate a random word then attempt to solve it.

```
$ ./wordle --guesser automatic
cares
1 ⬜⬜⬜⬜⬜
ponty
2 ⬜⬜🟩⬜🟩
bingy
3 🟩⬜🟩⬜🟩
bunjy
4 🟩🟩🟩⬜🟩
bundy
5 🟩🟩🟩⬜🟩
bunny
6 🟩🟩🟩🟩🟩
```

Automatic mode uses the results to narrow down the list of words after each guess, which _usually_ results in being able to guess the 
word within 6 guesses.  Though not consistently.

#### Benchmark of automatic use

There are 3 different "choosers" for automatic mode. These are used to select the next guess from the current list of available words.

```
# pure random selection
$ ./wordle --guesser automatic --chooser random --benchmark 12972
10951/12972 (84.4%) found - average guess num 4.627157337229477

# pick a word that seems to overlap with the most other words (by at least one letter)
# also has a bias against repeated letters in a word
$ ./wordle --guesser automatic --chooser overlap --benchmark 12972
11495/12972 (88.6%) found - average guess num 4.338321009134407

# attempt to use probability to estimate how likely a word will at least match
# one or more letter/position
$ ./wordle --guesser automatic --chooser likelihood --benchmark 12972
11376/12972 (87.7%) found - average guess num 4.372802390998594
```

Instead of using the long list of 12,972 words we can also run using the much short list of known wordle answers.  This list
contains 2,315 words and the words are more "sensible" than in the original list.  Benchmarking against this list gives much
better results:

```
$ ./wordle --guesser automatic --chooser random --use-wordle-answers --benchmark 2315
2278/2315 (98.4%) found - average guess num 4.020193151887621

$ ./wordle --guesser automatic --chooser likelihood --use-wordle-answers --benchmark 2315
2304/2315 (99.5%) found - average guess num 3.640625

$ ./wordle --guesser automatic --chooser overlap --use-wordle-answers --benchmark 2315
2301/2315 (99.4%) found - average guess num 3.6262494567579315
```
