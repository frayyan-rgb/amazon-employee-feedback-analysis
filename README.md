# Amazon Fulfillment Center — Employee Voice Analysis

Text analysis of Amazon fulfillment associate feedback from two public sources — Glassdoor reviews and YouTube "day in the life" videos — to surface the themes and sentiment behind employee retention.

## Why this project

Companies collect far more employee feedback than anyone has time to read. This project treats Glassdoor and YouTube as two different lenses on the same workforce — one written and retrospective, one spoken and in-the-moment — and uses NLP to turn hundreds of individual reviews into patterns a decision-maker could act on, and to see where the two sources agree or disagree.

## Data

| Source | Volume | Notes |
|---|---|---|
| Glassdoor | 125 reviews | Amazon Fulfillment Center reviews (Glassdoor.ca), collected manually |
| YouTube | 10 videos → 9 with usable transcripts | First-person "day in the life" / warehouse-shift videos |

Raw and cleaned data files are excluded from this repo (see `.gitignore`) since they're scraped third-party content, not original data. The code here is fully documented and reproducible against your own export of the same sources.

## Pipeline

| Notebook | What it does |
|---|---|
| `01_glassdoor_data_cleaning.ipynb` | Loads raw Glassdoor export, audits missing values, drops 6 empty/constant columns (28 → 22) |
| `02_text_cleaning.ipynb` | Builds a reusable `clean_text()` function (lowercase → strip punctuation → remove NLTK stopwords) and applies it to 4 Glassdoor review fields and the YouTube transcript field |
| `03_keyword_analysis.ipynb` | Tokenizes cleaned text, counts word/bigram frequency, and visualizes results (bar charts + word clouds) per platform |
| `04_sentiment_analysis.ipynb` | *In progress* — TextBlob polarity/subjectivity scoring, Glassdoor vs. YouTube comparison |

## Findings so far (keyword analysis)

**Glassdoor** — compensation and benefits dominate the positive language. Top bigrams: `good pay`, `good benefits`, `place work`.

**YouTube** — a schedule/fatigue theme not visible in the written reviews: `shift`, `night`, `hours`, `tired`, `sleep` cluster together, alongside `enjoy` and `worth`, suggesting the job is physically demanding but not universally disliked. Sample size is small (9 transcripts), so this is read as a theme worth investigating further, not a confirmed pattern.

Full 3–5 insight cross-platform comparison is pending the sentiment analysis stage.

## Data-quality decisions worth noting

A few cleaning-stage judgment calls, documented because they'd change the results if made differently:

- **NLTK's stopword list removes negations** (`not`, `no`) along with filler words. `"not interactive"` becomes `"interactive"` — the literal opposite meaning. Handled by treating single-word keyword counts as directional signal, not literal sentiment, and flagging it as a known limitation rather than silently trusting the output.
- **`string.punctuation` is ASCII-only.** It misses curly quotes (`’`) and em dashes (`—`), which are common in copy-pasted review/transcript text and otherwise show up in the keyword list as fake "words." Added a regex pass (`re.fullmatch(r"[a-z]+", w)`) at the keyword-analysis stage to catch what basic punctuation stripping missed.
- **HTML entities survive cleaning.** `&amp;` in raw Glassdoor text degrades to a stray `amp` token rather than being recognized as `&`. Left in at the cleaning stage (documented as a deliberate lightweight-cleanup tradeoff) and filtered out at the keyword-analysis stage instead.
- **Applied the same filtering rules to both platforms** so the Glassdoor/YouTube comparison isn't an artifact of one dataset being cleaned more aggressively than the other.

## Tech stack

Python · pandas · NLTK · TextBlob · Matplotlib · WordCloud · Google Colab

## Status

- [x] Data collection & structuring
- [x] Text cleaning
- [x] Keyword frequency analysis
- [ ] Sentiment scoring (TextBlob)
- [ ] Combined dataset + cross-platform insight summary
