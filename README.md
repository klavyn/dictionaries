# klavyn dictionaries

Per-language chord dictionaries for [klavyn](https://github.com/klavyn/klavyn), plus the tooling that builds them.
This is where new languages and abbreviation packs live.

## What a dictionary is

A chord is an unordered **set** of the letters in a word.
A dictionary maps each letter-set to the single word klavyn should emit for it, together with that word's frequency.

The format is a three-column CSV with a header row: `letterset,word,frequency`.

```csv
letterset,word,frequency
ouy,you,28787591
eht,the,22761659
adn,and,10572938
```

`letterset` is the word's letters lowercased, de-duplicated, and sorted — so `you` becomes `ouy` and `the` becomes `eht`.
`frequency` is the raw count from the source corpus, and rows are ordered most-frequent first.

Because chords are sets, some words share one — `{a,e,r}` is `are`, `ear`, and `era`.
The dictionary resolves every collision by keeping only the **highest-frequency** word for that letter-set, so a chord is never ambiguous.

## Layout

| File | Purpose |
| ---- | ------- |
| `dictionary.<lang>.csv`  | The core `letterset,word,frequency` map for a language. |
| `abbrev.<lang>.csv`      | An abbreviation overlay layered on top of the dictionary (e.g. `htx` → `thanks`), in the same three-column format. Community-extensible. |
| `build/`                 | `build_dictionary.py`, which generates a dictionary from a frequency word list. |

`<lang>` is a BCP-47-ish tag: `en`, `fr`, `ko`, `ru`, `ar`, …

## Adding or improving a language

1. Start from a permissively licensed, frequency-sorted word list for the language, and cite the source in your PR.
   The [hermitdave/FrequencyWords](https://github.com/hermitdave/FrequencyWords) lists (MIT, ~60 languages, `word<space>count` format) are what the shipped dictionaries are built from.
2. Run `build/build_dictionary.py <lang_50k.txt> dictionary.<lang>.csv` to produce the CSV.
   It lowercases each word, folds diacritics to their base Latin letter for chord detection (a plain keyboard has no physical key for `é`), and applies the highest-frequency collision rule.
3. Open a PR with the generated CSV **and** the source and command you used, so the result is reproducible.

Non-Latin scripts are welcome and expected — Korean (Dubeolsik jamo), Cyrillic, Arabic, and more.
The chord model is script-agnostic: it works on whatever units a layout produces.

## Abbreviation packs

`abbrev.<lang>.csv` is meant to grow by community submission.
Keep entries unambiguous and broadly useful; niche or personal shortcuts belong in a user's own config, not the shipped pack.

## Provenance and licensing

The shipped dictionaries are built from [hermitdave/FrequencyWords](https://github.com/hermitdave/FrequencyWords) (MIT), which derives its counts from the OpenSubtitles corpus.

The dictionary data in this repository is licensed under [Creative Commons Attribution 4.0 International](LICENSE-CC-BY-4.0.txt) (CC-BY-4.0) — reuse it freely, with attribution.
Only contribute word lists whose license permits redistribution, and note the provenance in your PR.
