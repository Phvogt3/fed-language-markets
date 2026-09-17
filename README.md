# Federal Reserve Language and Market Corrections

Research project for Applied Machine Learning at Baylor University. The study tests whether the language the Federal Reserve Chair uses in FOMC press conferences predicts short term S&P 500 movement.

## Data

89 press conference transcripts from April 2011 to March 2026, covering Ben Bernanke (11), Janet Yellen (16), and Jerome Powell (62). Transcripts were scraped as PDFs from the Federal Reserve website. Daily S&P 500 and VIX data came from Yahoo Finance through yfinance.

## Features

Each transcript was split into prepared remarks and Q&A using pattern matching on transition phrases like "I'd be happy to take your questions." For each press conference, the following were computed:

| Feature | Description |
|---|---|
| fk_grade | Flesch-Kincaid grade level |
| avg_sentence_length | Average words per sentence |
| jargon_freq | Monetary policy jargon per 1,000 words |
| cautious_ratio | Hedging words divided by definitive words |
| prepared_sentiment, qa_sentiment | Average sentence level VADER sentiment for each section |
| tone_shift | Prepared sentiment minus Q&A sentiment |
| sp500_same_day_abs | Absolute open to close S&P 500 return on the press conference day |
| reversal | 1 if the next day return moved in the opposite direction |
| vix_close | VIX closing value on the press conference day |

## Hypotheses and Results

| Hypothesis | Test | Result |
|---|---|---|
| Language complexity predicts same day movement | Multiple regression | Not significant (FK p = 0.437, sentence length p = 0.295, jargon p = 0.798) |
| Cautious ratio predicts next day reversal | Logistic regression | Not significant (p = 0.502) |
| Tone shift affects same day movement | Two sample t-test | Not significant (p = 0.644) |
| VIX moderates the effect of complexity | Regression with interaction term | VIX (p = 0.009) and FK grade (p = 0.048) significant, interaction marginal (p = 0.055), R-squared 0.35 |

Language features did not predict market movement on their own. Once volatility was included, readability became significant, which suggests complex language matters more when markets are already stressed.

## Files

`ResearchPaperDatasetCreate.ipynb` downloads the transcripts, builds the linguistic and market features, and exports `fed_presser_dataset.csv`.

`ResearchPaper.ipynb` rebuilds the dataset and runs the four hypothesis tests.

`Research_paper_AML.pdf` is the final paper.

## Running

```
pip install yfinance textstat vaderSentiment pdfplumber requests pandas numpy scipy statsmodels
```

Run `ResearchPaperDatasetCreate.ipynb` first to generate the dataset, then run `ResearchPaper.ipynb`. The notebooks were built in Google Colab, so remove the `google.colab` download lines if you run them locally.

## Limitations

Returns are daily open to close, but press conferences start at 2:30 PM, so other events during the day add noise. The models also do not control for the rate decision itself or whether it surprised the market.
