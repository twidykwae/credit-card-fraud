# Credit Card Fraud Detection

## What this notebook analyzes

The notebook [Credit_Card_Fraud_Detection.ipynb](Credit_Card_Fraud_Detection.ipynb) looks at 999 credit card transactions from [card_transdata.csv](card_transdata.csv) and asks two questions:

1. **How do fraudulent transactions differ from legitimate ones?** It uses charts to compare fraud and non-fraud across every column: how far from home the purchase was, how far from the previous purchase, how large the purchase was compared with the cardholder's usual spending, and whether it was an online order, used the chip, used a PIN, or was at a store the cardholder had used before.
2. **Can a model predict fraud?** The data is split 80% for training and 20% for testing. Two models are compared: a "never fraud" baseline that always guesses "not fraud", and a K-Nearest Neighbors (KNN) model with k = 3.

**Main findings**
- Only about 8% of transactions are fraud.
- Fraud is linked to **unusually large purchases**, **online orders**, and **purchases far from home**.
- **Using a PIN** is a strong sign of a legitimate purchase: none of the PIN transactions were fraud. Using the chip is also linked to less fraud.
- The "never fraud" baseline is 92% accurate but catches **zero** fraud, which shows why accuracy alone is misleading when fraud is rare.
- KNN (k = 3, unscaled features) reaches 94% accuracy and catches 8 of the 15 fraud cases in the test set (precision 0.62, recall 0.53, F1 0.57).

**Repository contents:** `Credit_Card_Fraud_Detection.ipynb` (the notebook), `card_transdata.csv` (the dataset), and this `README.md`.

**To run it yourself:** install the packages with `pip install pandas numpy matplotlib seaborn scikit-learn jupyter`, keep the CSV in the same folder as the notebook, and run all cells.

## How I used AI

- **AI system:** Claude Code, Anthropic's AI coding assistant, used through its VS Code extension
- **AI model / version:** Claude Opus 5.5 (model ID `claude-opus-5-5`)

I built the notebook over three main prompts:
1. **Prompt 1** asked for the first full version: load the data with pandas, explore and visualize fraud vs non-fraud for every variable, and build and evaluate a KNN model with scikit-learn.
2. **Prompt 2** reshaped the analysis: specific new charts (a purchase-ratio distribution, one chart per binary feature, two scatter plots), a fresh start on modeling with an 80/20 split, a "never fraud" baseline, KNN with k = 3, confusion matrices, and a comparison bar chart.
3. **Prompt 3** simplified everything: seaborn styling with green for non-fraud and red for fraud, plain count plots and histograms, dropping rows with missing values, KNN on raw unscaled features, heatmap confusion matrices, and a four-metric comparison chart.

The key prompts are quoted in the notebook as markdown cells, placed right before the code they produced. Small follow-up tweaks (like asking for the remaining gray charts to be colored) are left out.

## Reflection on AI assistance

Using AI saved a lot of time on the parts that are mostly typing and formatting. It turned a few sentences into a full, organized notebook, with data quality checks, more than a dozen labelled charts in consistent colors, a train/test split, two models, and a comparison chart. It also explained new metrics in plain terms, such as precision, recall and F1, and why they matter more than accuracy for fraud. But I still had to check its output. When I switched KNN to raw, unscaled features, recall fell from about 0.81 in the earlier scaled version to 0.53. That made me check *why* instead of just reporting the new number: KNN measures distance, so columns in the hundreds (distance from home) drown out the 0/1 columns (PIN, online order) that the charts showed were the best clues. Its written conclusions also needed checking against the charts. One early description said fraud was "concentrated" among very large purchases, but the chart showed a fair number of fraud cases were normal-sized. In the first version, a "tuned" model scored slightly *worse* on the test set than the default one. That only made sense once I noticed the test set had about 20 fraud cases, so one case moves recall by 5 points. I knew to look at these because the numbers either didn't match the pictures or didn't match what I knew about how the methods work.

The danger is that AI output looks finished and confident whether or not it is right, so someone who hasn't learned data science can turn in something that looks professional and is misleading. For example, a student could see "92% accuracy" for the never-fraud model or "94% accuracy" for KNN and call the model a success, without realizing the baseline catches no fraud at all and KNN still misses about half of it. Someone could also accept "train KNN on the raw features" without knowing that unscaled features quietly make the model ignore the strongest predictors. They could fill in missing values using the whole dataset before splitting, which leaks test information into training. Or they could drop rows with missing values without checking how many were lost (here it was 22). AI also has to guess when a prompt is vague. When I asked to remove "the continuous features by class graph", it had to decide which charts I meant, and a beginner might not notice if it guessed wrong. Without the background to ask "does this number make sense?", it is easy to present wrong conclusions as facts.
