<div align="center">

# 🏀 CourtSide Analytics
## NBA Player Statistics — Web Scraping & Exploratory Analysis

[![Python](https://img.shields.io/badge/Python-3.8%2B-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![Pandas](https://img.shields.io/badge/Pandas-2.0%2B-150458?style=flat-square&logo=pandas&logoColor=white)](https://pandas.pydata.org)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=flat-square&logo=jupyter&logoColor=white)](https://jupyter.org)
[![Data Source](https://img.shields.io/badge/Data-Basketball--Reference-E63946?style=flat-square)](https://www.basketball-reference.com)
[![License](https://img.shields.io/badge/License-MIT-22C55E?style=flat-square)](LICENSE)

> *From raw HTML tables to meaningful insights — a complete, reproducible pipeline for NBA statistics analysis.*

</div>

---

## 📖 Table of Contents

- [Overview](#-overview)
- [What You'll Learn](#-what-youll-learn)
- [Project Structure](#-project-structure)
- [Data Source](#-data-source)
- [Getting Started](#-getting-started)
- [Workflow Walkthrough](#-workflow-walkthrough)
- [Exploratory Data Analysis](#-exploratory-data-analysis)
- [Key Findings](#-key-findings)
- [Dependencies](#-dependencies)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🏟️ Overview

**CourtSide Analytics** is a hands-on data science project that demonstrates how to collect, clean, and analyze NBA player statistics using Python. Rather than relying on a pre-packaged dataset, this project scrapes real, live data directly from [Basketball-Reference.com](https://www.basketball-reference.com) — the gold standard for basketball statistics.

The project covers the **entire data pipeline** from scratch:

```
URL Construction  →  Web Scraping  →  Data Cleaning  →  EDA  →  Visual Insights
```

Whether you're a data science student, a basketball fanatic, or someone exploring web scraping techniques — this project gives you a clean, well-commented, and reproducible notebook you can run and extend.

---

## 🎯 What You'll Learn

| Skill | Description |
|---|---|
| 🌐 **Dynamic URL Construction** | Build season-specific URLs programmatically to query any NBA year |
| 🕸️ **Web Scraping with Pandas** | Use `pd.read_html()` to extract structured tables from live web pages |
| 🧹 **Data Cleaning** | Handle multi-index columns, rename fields, fix data types, and remove noise |
| 🔎 **Exploratory Data Analysis** | Discover patterns, distributions, and outliers in player performance data |
| 📊 **Data Visualization** | Produce clear and insightful charts using Matplotlib and Seaborn |

---

## 📁 Project Structure

```
courtside-analytics/
│
├── 📓 nba_scraper.ipynb          # Main notebook — scraping, cleaning, and EDA
├── 📄 README.md                  # You are here
├── 📦 requirements.txt           # Python dependencies
│
├── data/
│   ├── raw/                      # Raw scraped data (auto-generated)
│   └── cleaned/                  # Cleaned and processed CSVs
│
└── visuals/                      # Exported charts and figures
```

---

## 🌐 Data Source

All statistics are sourced from **[Basketball-Reference.com](https://www.basketball-reference.com)** — specifically the **Per-Game Player Statistics** pages.

The URL pattern follows a predictable structure, making it easy to query any season programmatically:

```
https://www.basketball-reference.com/leagues/NBA_{YEAR}_per_game.html
```

**Example — 2019 Season:**

```
https://www.basketball-reference.com/leagues/NBA_2019_per_game.html
```

> ⚠️ **Scraping Etiquette:** Please be respectful of Basketball-Reference's servers. Add delays between requests and avoid running the scraper in rapid loops. Consider caching results locally after the first run.

---

## 🚀 Getting Started

### Prerequisites

Make sure you have **Python 3.8+** installed. Then clone the repository and install dependencies.

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/courtside-analytics.git
cd courtside-analytics
```

### 2. Create a Virtual Environment (Recommended)

```bash
python -m venv venv
source venv/bin/activate        # On Windows: venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Launch the Notebook

```bash
jupyter notebook nba_scraper.ipynb
```

That's it — run the cells top to bottom and the entire pipeline executes automatically. 🎉

---

## 🔄 Workflow Walkthrough

The notebook is organized into clearly marked sections. Here's what happens at each stage:

### Step 1 — URL Construction

```python
def build_url(season_year: int) -> str:
    """Construct the Basketball-Reference URL for a given NBA season year."""
    base_url = "https://www.basketball-reference.com/leagues"
    return f"{base_url}/NBA_{season_year}_per_game.html"

url = build_url(2023)
# → https://www.basketball-reference.com/leagues/NBA_2023_per_game.html
```

You can easily loop over multiple seasons to build a multi-year dataset.

---

### Step 2 — Scraping with `pd.read_html()`

Pandas' `read_html()` function parses all `<table>` tags on a page and returns a list of DataFrames — no BeautifulSoup or Selenium needed for this use case.

```python
import pandas as pd

tables = pd.read_html(url, header=0)
df = tables[0]   # The per-game stats table is always the first table
```

> 💡 **Why `read_html()`?** Basketball-Reference renders its stats tables as static HTML, making them directly parseable without a JavaScript engine. For dynamic (JS-rendered) content, you'd need Selenium or Playwright instead.

---

### Step 3 — Data Cleaning

Raw scraped data is never clean. This step handles:

- **Header rows embedded in data** — Basketball-Reference repeats column headers every 20 rows; these are filtered out
- **Column renaming** — Abbreviated stats columns are renamed to be human-readable
- **Type conversion** — Numeric columns stored as strings are cast to `float` / `int`
- **Duplicate removal** — Players traded mid-season appear multiple times (one row per team + one "TOT" row)
- **Missing values** — NaNs in counting stats are filled with `0` where appropriate

```python
# Remove repeated header rows
df = df[df['Player'] != 'Player'].reset_index(drop=True)

# Cast numeric columns
numeric_cols = df.columns.drop(['Player', 'Pos', 'Tm'])
df[numeric_cols] = df[numeric_cols].apply(pd.to_numeric, errors='coerce')
```

---

### Step 4 — Exploratory Data Analysis

Once the data is clean, we dive into the numbers.

---

## 📊 Exploratory Data Analysis

### Distribution of Points Per Game

Visualizing how scoring is distributed across all players — from bench contributors to elite scorers.

```python
import seaborn as sns
import matplotlib.pyplot as plt

sns.histplot(df['PTS'], bins=30, kde=True)
plt.title('Distribution of Points Per Game — NBA 2023')
plt.xlabel('Points Per Game')
plt.show()
```

### Top 15 Scorers

```python
top_scorers = df.nlargest(15, 'PTS')[['Player', 'Tm', 'PTS', 'AST', 'TRB']]
top_scorers.plot(kind='barh', x='Player', y='PTS', figsize=(10, 6))
```

### Correlation Heatmap

Understanding how offensive and defensive stats relate to each other.

```python
corr_cols = ['PTS', 'AST', 'TRB', 'STL', 'BLK', 'TOV', 'MP']
sns.heatmap(df[corr_cols].corr(), annot=True, cmap='coolwarm', fmt='.2f')
```

### Points vs. Minutes Played

Exploring the relationship between court time and scoring output:

```python
sns.scatterplot(data=df, x='MP', y='PTS', hue='Pos', alpha=0.6)
plt.title('Points Per Game vs. Minutes Played — Coloured by Position')
```

---

## 💡 Key Findings

A few highlights from the 2023 season analysis:

- **Scoring is right-skewed** — most players average under 10 PPG; elite scorers are true outliers
- **Minutes and points are strongly correlated** (r ≈ 0.80), confirming that usage drives counting stats
- **Guards dominate scoring** while **Centers lead in blocks and rebounds** — positional roles remain distinct
- **Turnovers correlate with assists** — high-usage playmakers create more opportunities but also more mistakes
- **The 30+ PPG club** remains exclusive — only a handful of players reach that threshold in any given season

---

## 📦 Dependencies

```txt
pandas>=2.0.0
numpy>=1.24.0
matplotlib>=3.7.0
seaborn>=0.12.0
lxml>=4.9.0          # Required by pd.read_html()
requests>=2.28.0
jupyter>=1.0.0
notebook>=6.5.0
```

Install all at once:

```bash
pip install -r requirements.txt
```

---

## 🔭 Extending This Project

Here are some ideas to take this further:

- **Multi-season analysis** — Loop over years 2010–2024 to track the evolution of playing styles (e.g. the 3-point revolution)
- **Advanced metrics** — Scrape PER, Win Shares, BPM, and VORP from the Advanced Stats tables
- **Team-level aggregation** — Group by team to compare franchise-level performance
- **Player career tracking** — Scrape individual player pages for season-by-season career arcs
- **Predictive modelling** — Use historical stats to predict All-Star selections or award winners

---

## 🤝 Contributing

Contributions are welcome! If you'd like to improve the notebook, add visualizations, or extend the scraper:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-new-analysis`
3. Commit your changes: `git commit -m 'Add: multi-season scraper loop'`
4. Push to the branch: `git push origin feature/my-new-analysis`
5. Open a Pull Request

Please keep code well-commented and follow the existing notebook structure.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgements

- **[Basketball-Reference.com](https://www.basketball-reference.com)** — for maintaining the most comprehensive and freely accessible basketball statistics database on the internet
- **The Pandas team** — for `read_html()`, which makes structured web scraping almost embarrassingly easy
- The open-source data science community for the incredible tooling that makes projects like this possible

---

<div align="center">

*😂😂😂😂😂😂😂😂😂😂😂😂*

**⭐ If you found this project useful, give it a star!**

</div>
