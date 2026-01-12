##NBA Player Statistics Web Scraping and Analysis
Introduction
This notebook demonstrates how to web scrape NBA player statistics from Basketball-Reference.com using Python's pandas library, specifically the read_html() function. It covers the process of constructing dynamic URLs, extracting data from HTML tables, initial data cleaning, and performing a quick exploratory data analysis.

Data Source
The data is scraped from Basketball-Reference.com, specifically the per-game statistics pages for various NBA seasons.

Example URL for 2019 season: https://www.basketball-reference.com/leagues/NBA_2019_per_game.html

Methods Used
1. URL Construction
Method 1: Modularized URL and Year: Demonstrates how to dynamically generate URLs for specific years by formatting a base URL string.
Method 2: Single URL: Shows how to use a predefined URL for a single season.
2. Web Scraping with pandas.read_html()
The pd.read_html() function is used to parse HTML tables directly into a list of pandas DataFrames.
Identifies the number of tables present on the webpage.
Selects the primary statistics table (first table) for further analysis.
3. Data Cleaning
Removing Header Rows: Addresses the issue of duplicate header rows appearing within the scraped data by identifying and dropping them based on specific column values (e.g., 'Age' column containing 'Age').
4. Exploratory Data Analysis (EDA)
Utilizes the seaborn library to visualize the distribution of player statistics.
Points Per Game (PTS) Distribution: Creates histograms to show the distribution of 'PTS' (points per game) among players, demonstrating how to customize plot aesthetics like edge color and fill color.
