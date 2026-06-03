# Netflix Shows Data Analysis

This project is an exploratory data analysis of the Netflix content catalog.
The main goal is to understand what types of content perform better, how different rating groups behave, how user ratings are distributed, and what insights can support content strategy decisions.

The project combines the original Netflix Shows dataset with several external datasets, including IMDb, Rotten Tomatoes, Metacritic, awards data, countries of production, and Netflix viewing hours.

---

## Project Overview

Netflix has a large and diverse content catalog, including movies, series, family content, animation, adult dramas, documentaries, and experimental projects. For a streaming platform, content strategy is one of the most important business decisions: the company needs to understand what to produce, what to license, which audience segments to target, and where to invest.

This project treats the dataset not only as a technical EDA task, but also as a business analytics case.

The key question of the project:

```text
How can data help Netflix make better content investment decisions?
```

---

## Business Goal

The goal of the analysis is to identify insights that can help answer the following business questions:

* What audience groups does Netflix focus on most?
* Which rating categories receive the highest user scores?
* How has Netflix content production changed over time?
* Are Netflix user ratings strongly related to external ratings such as IMDb?
* Which content types generate more viewing hours?
* What type of content should Netflix prioritize for stable engagement?
* What type of content helps Netflix gain awards and differentiate from competitors?

---

## Datasets Used

The project uses the main Netflix dataset and several additional datasets.

### 1. Main Dataset: `NetflixShows.xlsx`

The main dataset contains information about Netflix shows and movies.

Main columns:

| Column              | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| `title`             | Title of the show or movie                                   |
| `rating`            | Age rating category, for example `G`, `PG`, `TV-14`, `TV-MA` |
| `ratingLevel`       | Text description of the rating category                      |
| `ratingDescription` | Numeric encoding of the rating                               |
| `release year`      | Release year                                                 |
| `user rating score` | Netflix user rating score                                    |
| `user rating size`  | User rating size                                             |

---

### 2. External Ratings Dataset

File:

```text
netflix-rotten-tomatoes-metacritic-imdb.csv
```

This dataset was used to enrich the analysis with external quality indicators.

Main added columns:

| Column                  | Description                                       |
| ----------------------- | ------------------------------------------------- |
| `IMDb Score`            | IMDb user / critic score                          |
| `Rotten Tomatoes Score` | Share of positive critic reviews                  |
| `Metacritic Score`      | Weighted critic score                             |
| `Awards Received`       | Number of awards received                         |
| `Awards Nominated For`  | Number of award nominations                       |
| `Hidden Gem Score`      | Additional content quality / popularity indicator |
| `IMDb Votes`            | Number of IMDb votes                              |

---

### 3. Netflix Titles Dataset

File:

```text
netflix_titles.csv
```

This dataset was used to add information about the country of production.

Added column:

| Column    | Description                        |
| --------- | ---------------------------------- |
| `country` | Country or countries of production |

---

### 4. Netflix Engagement Report

File:

```text
What_We_Watched_A_Netflix_Engagement_Report_2023Jan-Jun.csv
```

This dataset was used to add viewing activity.

Added column:

| Column         | Description        |
| -------------- | ------------------ |
| `hours_viewed` | Total hours viewed |

---

## Data Cleaning

The original dataset contained many duplicates and missing values.
The cleaning process included several steps.

### Removed Duplicates

The dataset contained:

```text
500 duplicate rows
```

After removing duplicates, the analysis was based on unique titles.

Duplicates most often appeared in large rating groups such as:

* `TV-14`
* `PG`
* `G`

This likely happened because the dataset was collected automatically and the same title could appear multiple times.

---

### Removed Uninformative Columns

Two columns were removed from the main dataset:

```text
ratingDescription
user rating size
```

Reasons:

* `ratingDescription` only duplicated the information already contained in the rating category.
* `user rating size` did not provide useful variation for the analysis.

---

### Processed Missing Values

The project processed missing values in:

```text
ratingLevel
user rating score
```

For `ratingLevel`, missing values were filled using the corresponding rating group logic.

For `user rating score`, missing values were handled after additional external data was merged. The project used the relationship between Netflix user ratings and IMDb scores, as well as group-level statistics, to avoid losing a large part of the dataset.

---

## Feature Engineering

Several new features were created to make the analysis more useful.

### 1. `content_type`

A new feature was created to separate movies and series.

Logic:

```text
Ratings starting with "TV" → Series
Other ratings → Movie
```

Examples:

| Rating  | Content type |
| ------- | ------------ |
| `TV-14` | Series       |
| `TV-MA` | Series       |
| `PG`    | Movie        |
| `R`     | Movie        |

This feature was also used as an additional merge key when joining the main dataset with the IMDb / Rotten Tomatoes / Metacritic dataset.

---

### 2. `rating_overall`

Netflix contains both TV ratings and movie ratings.
Some of them represent similar audience groups but belong to different rating systems.

Examples:

| TV rating | Movie rating equivalent |
| --------- | ----------------------- |
| `TV-G`    | `G`                     |
| `TV-PG`   | `PG`                    |
| `TV-14`   | `PG-13`                 |
| `TV-MA`   | `R`                     |

The project created a unified rating feature to compare similar audience categories more correctly.

---

### 3. Normalized User Rating

Netflix user ratings were converted to a 10-point scale to make them comparable with IMDb scores.

Example:

```text
user_rating_score_norm = user rating score / 10
```

---

### 4. `award_ratio`

An additional feature was created to compare received awards with nominations:

```text
award_ratio = Awards Received / Awards Nominated For
```

This helped analyze which content was not only nominated, but also successful in winning awards.

---

## Exploratory Data Analysis

The analysis covered several major areas.

---

## 1. User Rating Distribution

The distribution of Netflix user ratings is shifted toward high scores.

Key statistics:

| Metric             | Value |
| ------------------ | ----: |
| Mean               |  81.4 |
| Median             |  83.5 |
| Standard deviation |  12.7 |
| Minimum            |    55 |
| Maximum            |    99 |

The median is higher than the mean, which suggests that lower scores pull the average down slightly.

A business interpretation is that Netflix users tend to rate content positively. This may happen because Netflix recommendations show users content they are more likely to enjoy, so low-quality or irrelevant content appears less often in the user journey.

---

## 2. Rating Groups and Audience Segments

The most common rating groups in the catalog are:

| Rating group | Number of titles |
| ------------ | ---------------: |
| `TV-14`      |              106 |
| `TV-MA`      |               82 |
| `PG`         |               76 |
| `G`          |               53 |

`TV-14` and `TV-MA` together account for almost 40% of the catalog.
This means that Netflix strongly focuses on teenagers and adult audiences.

Family content is also important: `PG` and `G` are among the largest groups.

Business insight:

```text
Netflix builds a large part of its catalog around teenage and adult audiences,
while also maintaining a strong family content segment.
```

---

## 3. Which Categories Receive Higher Ratings

Adult content has the most stable and high user ratings.

Main observations:

* `R` and `PG` content have high median ratings.
* `PG-13` is the most unpredictable category, with a wide rating spread.
* Children’s content is rated lower on average, but this does not necessarily mean lower quality.
* Children may rate content less often, so their preferences can be underrepresented in the dataset.

Business insight:

```text
Adult content gives more predictable rating performance,
while family content remains strategically important because of its broad audience.
```

---

## 4. Content Release Dynamics

The catalog contains content released between the 1940s and 2017.

Main observations:

* Older content appeared in the catalog because Netflix licensed existing movies and shows.
* Growth became stronger after 2011, when Netflix started developing Netflix Originals.
* The strongest growth happened around 2015–2016.
* 2016 was the peak year in the dataset.
* 2017 looks lower because the dataset contains only part of the year.

Business insight:

```text
The catalog growth reflects Netflix's transition from licensing existing content
to producing original content and expanding internationally.
```

---

## 5. Show Profile: Breaking Bad

The project includes a detailed profile of one specific show: `Breaking Bad`.

Key facts:

| Metric                      |    Value |
| --------------------------- | -------: |
| Rating group                |    TV-MA |
| Release year                |     2013 |
| Netflix user rating         | 97 / 100 |
| Dataset average             |     81.4 |
| TV-MA average               |     84.8 |
| Position among rated titles | Top 7.5% |

`Breaking Bad` is significantly above both the overall dataset average and the TV-MA category average.

Business interpretation:

```text
Breaking Bad is an example of a high-performing adult series that fits the segment
where Netflix has a strong audience and high engagement potential.
```

---

## 6. Netflix Ratings vs IMDb Ratings

The project compared Netflix user ratings with IMDb scores.

The main finding:

```text
Netflix user ratings are only weakly correlated with IMDb ratings.
```

The Spearman correlation was approximately:

```text
r ≈ 0.3
```

This is an important business insight. It means that external ratings should not be used as the only criterion for content decisions.

Business interpretation:

```text
A title that performs well on IMDb does not automatically perform well for Netflix users.
Netflix should prioritize its own audience behavior and platform-specific engagement data.
```

---

## 7. Viewing Hours Analysis

The project also analyzed Netflix viewing hours from the Engagement Report.

Main findings:

### Movies

Top-viewed movies were mostly family and animated titles, such as:

* `Minions`
* `Hotel Transylvania 2`
* `Kung Fu Panda`
* `White Chicks`
* `The Angry Birds Movie`

Family movies, especially animated content, perform well because they can be watched by the whole family and often have high repeat viewing.

### Series

Top-viewed series included:

* `The Walking Dead`
* `The Blacklist`
* `Gilmore Girls`
* `Breaking Bad`
* `Friends`
* `Shameless`
* `The Office`
* `The Flash`
* `NCIS`
* `Better Call Saul`

Series in the `TV-14` and `TV-MA` segments are especially important because viewers return to them across seasons.

Business insight:

```text
Movies drive broad family viewing, while series support long-term retention.
```

---

## 8. Country-Level Analysis

The project added country of production and created visualizations to compare content by country.

The analysis included:

* weighted user rating by country;
* number of shows by country;
* awards and average ratings by country.

The United States dominates the catalog in terms of the number of titles and awards.
The United Kingdom and India also appear as visible contributors in the dataset.

---

## 9. Awards Analysis

The project also explored awards and nominations.

Main insight:

```text
The most awarded content is not always the same as the most viewed content.
```

Niche and experimental projects may receive more critical recognition, while family and mainstream content may generate more stable viewing hours.

Business interpretation:

```text
Netflix should balance mass-market content for engagement with original projects
that strengthen brand differentiation and critical reputation.
```

---

## Final Business Conclusions

The project produced several key conclusions.

### 1. External ratings are not enough

Netflix user ratings are weakly related to IMDb ratings.
Therefore, relying only on IMDb, Rotten Tomatoes, or Metacritic would be ineffective.

Netflix should analyze its own user behavior and platform-specific engagement metrics.

---

### 2. Adult series are strategically important

`TV-14` and `TV-MA` are the most competitive and important segments.
They contain many titles, receive high ratings, and work well for long-term viewer retention.

---

### 3. Family animation is strong for views

Family movies and animation perform well in viewing hours.
They attract a broad audience and often generate repeat views.

---

### 4. Awards and views reflect different types of success

Highly viewed content is not always the most awarded.
Experimental and niche projects may be more important for critical recognition and brand image.

---

### 5. Best content strategy is balanced

The optimal strategy is not to focus only on one content type.

A strong Netflix content strategy should combine:

* adult series for retention;
* family animation for stable viewing hours;
* original and experimental projects for awards and differentiation.

---

## Tech Stack

The project was completed in Python.

Main libraries:

```text
pandas
numpy
matplotlib
seaborn
plotly
scipy
scikit-learn
re
```

Main methods used:

* data cleaning;
* missing value processing;
* feature engineering;
* dataset merging;
* exploratory data analysis;
* correlation analysis;
* rating normalization;
* visual analysis;
* business interpretation.

---

## Repository Structure

Recommended repository structure:

```text
.
├── data/
│   ├── NetflixShows.xlsx
│   ├── netflix-rotten-tomatoes-metacritic-imdb.csv
│   ├── netflix_titles.csv
│   └── What_We_Watched_A_Netflix_Engagement_Report_2023Jan-Jun.csv
│
├── notebooks/
│   └── Netflix_Data_Analysis.ipynb
│
├── presentation/
│   └── Data Analysis_Netflix.pdf
│
├── README.md
└── requirements.txt
```

If the original notebook has another name, it is better to rename it to:

```text
Netflix_Data_Analysis.ipynb
```

This makes the repository easier to understand for recruiters and reviewers.

---

## How to Run the Project

### 1. Clone the repository

```bash
git clone https://github.com/your-username/netflix-shows-analysis.git
cd netflix-shows-analysis
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

Activate it.

For macOS / Linux:

```bash
source venv/bin/activate
```

For Windows:

```bash
venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

If `requirements.txt` is not available, install the main libraries manually:

```bash
pip install pandas numpy matplotlib seaborn plotly scipy scikit-learn openpyxl jupyter
```

### 4. Add data files

Place all source datasets into the `data/` folder:

```text
data/NetflixShows.xlsx
data/netflix-rotten-tomatoes-metacritic-imdb.csv
data/netflix_titles.csv
data/What_We_Watched_A_Netflix_Engagement_Report_2023Jan-Jun.csv
```

### 5. Open the notebook

```bash
jupyter notebook notebooks/Netflix_Data_Analysis.ipynb
```

Then run all cells from top to bottom.

---

## Requirements

Example `requirements.txt`:

```text
pandas
numpy
matplotlib
seaborn
plotly
scipy
scikit-learn
openpyxl
jupyter
```

---

## Limitations

This project has several limitations:

1. **Dataset is not fully representative**
   The original dataset may not represent the full Netflix catalog.

2. **2017 data is incomplete**
   The dataset contains only part of 2017, so it is not correct to compare 2017 directly with full years such as 2016.

3. **User ratings may be transformed**
   Netflix user ratings in the dataset are represented on a 100-point scale, although Netflix historically used a different rating system. This means the scores may have been transformed.

4. **Merged datasets may contain title mismatches**
   Some titles are written differently across datasets, especially in the Engagement Report where series can be split by seasons.

5. **Correlation does not imply causation**
   The project identifies relationships and patterns, but it does not prove causal effects.

---

## Future Improvements

Possible next steps:

* improve title matching across datasets;
* add genre-level analysis;
* add country-level normalization by catalog size;
* analyze content duration and number of seasons;
* compare Netflix Originals vs licensed content;
* build a dashboard in Tableau, Power BI, or Streamlit;
* add clustering of content types;
* build a recommendation-style segmentation of shows;
* analyze how awards, ratings, and views interact;
* create a predictive model for high-engagement content.

---

## Key Results

Main project results:

```text
Removed duplicate rows: 500
Added content type feature: Movie / Series
Added unified rating groups
Added IMDb, Rotten Tomatoes, Metacritic and awards data
Added country of production
Added Netflix viewing hours
Found weak correlation between Netflix ratings and IMDb scores: r ≈ 0.3
Identified TV-14 and TV-MA as key adult audience segments
Identified PG family movies and animation as strong viewing-hour drivers
```

---

## Conclusion

This project shows how exploratory data analysis can support content strategy decisions for a streaming platform.

The main conclusion is that Netflix should not rely only on external ratings when deciding what content to invest in. Platform-specific audience behavior matters more.

A balanced content strategy should combine adult series, family animation, and original experimental projects. Adult series help retain viewers, family animation drives stable views, and original niche projects strengthen brand identity and critical reputation.
