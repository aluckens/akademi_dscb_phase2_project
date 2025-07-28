![Movie studio market](./Images/cinema.jpg "Movie studio market")
# The state of cinema

## Overview

In today's content-driven world, audiences are seeking fresh, compelling stories. We're meeting this demand by launching a film studio that marries artistic vision with market intelligence. By analyzing real viewer behavior and box office patterns, we gain valuable insights about what stories connect with audiences. This informed approach allows us to create commercially viable films while minimizing the risks of being a new industry player. Ultimately, we're building a studio that makes smart, audience-focused entertainment decisions.

## Business Understanding

People can't get enough great stories. Whether on streaming services or at the movies. We see a real opportunity to create content that audiences will love. Since we're new to film production, we're being smart about it: we'll use data as our compass to understand what viewers actually want. By analyzing what types of movies perform best, what stories resonate with different audiences, and where the market has unmet needs, we'll make informed decisions about which projects to greenlight. This approach will help us compete with established studios while staying true to our vision of creating commercially successful entertainment.


```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
import sqlite3 as sqlite 
import seaborn as sns
%matplotlib inline
```

## Data Understanding

To guide our production strategy, we will conduct comprehensive analysis using the IMDB database as our primary source. This robust dataset will enable us to examine:

- Genre performance trends across different markets
- Box office results (domestic/international)
- Audience rating patterns and demographic preferences
- Release timing and seasonal performance factors

Before analysis, we will:
- Validate data completeness for recent releases
- Assess potential biases in user-generated ratings

This rigorous approach will ensure our insights reflect genuine market patterns rather than data artifacts, allowing us to identify the genre characteristics and creative elements most associated with commercial success in today's market.


```python
df_bom = pd.read_csv('Data/bom.movie_gross.csv')
df_bom.sample(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>studio</th>
      <th>domestic_gross</th>
      <th>foreign_gross</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3110</th>
      <td>Skyscraper</td>
      <td>Uni.</td>
      <td>68400000.0</td>
      <td>236400000</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>2067</th>
      <td>Phoenix (2015)</td>
      <td>IFC</td>
      <td>3200000.0</td>
      <td>NaN</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>2133</th>
      <td>Peggy Guggenheim Art Addict</td>
      <td>SD</td>
      <td>498000.0</td>
      <td>NaN</td>
      <td>2015</td>
    </tr>
  </tbody>
</table>
</div>




```python
conn = sqlite.connect('Data/im.db')

# Display tbale from the IMdb dataset
query_table = "SELECT * FROM sqlite_master where type='table';"
df_table = pd.read_sql_query(query_table, conn)
df_table
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>name</th>
      <th>tbl_name</th>
      <th>rootpage</th>
      <th>sql</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>table</td>
      <td>movie_basics</td>
      <td>movie_basics</td>
      <td>2</td>
      <td>CREATE TABLE "movie_basics" (\n"movie_id" TEXT...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>table</td>
      <td>directors</td>
      <td>directors</td>
      <td>3</td>
      <td>CREATE TABLE "directors" (\n"movie_id" TEXT,\n...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>table</td>
      <td>known_for</td>
      <td>known_for</td>
      <td>4</td>
      <td>CREATE TABLE "known_for" (\n"person_id" TEXT,\...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>table</td>
      <td>movie_akas</td>
      <td>movie_akas</td>
      <td>5</td>
      <td>CREATE TABLE "movie_akas" (\n"movie_id" TEXT,\...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>table</td>
      <td>movie_ratings</td>
      <td>movie_ratings</td>
      <td>6</td>
      <td>CREATE TABLE "movie_ratings" (\n"movie_id" TEX...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>table</td>
      <td>persons</td>
      <td>persons</td>
      <td>7</td>
      <td>CREATE TABLE "persons" (\n"person_id" TEXT,\n ...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>table</td>
      <td>principals</td>
      <td>principals</td>
      <td>8</td>
      <td>CREATE TABLE "principals" (\n"movie_id" TEXT,\...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>table</td>
      <td>writers</td>
      <td>writers</td>
      <td>9</td>
      <td>CREATE TABLE "writers" (\n"movie_id" TEXT,\n  ...</td>
    </tr>
  </tbody>
</table>
</div>




```python
query_mb = "SELECT * FROM movie_basics;"
df_mb = pd.read_sql_query(query_mb, conn)
df_mb.sample(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movie_id</th>
      <th>primary_title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>11060</th>
      <td>tt1619021</td>
      <td>Dead Hollywood Blondes</td>
      <td>Dead Hollywood Blondes</td>
      <td>2010</td>
      <td>60.0</td>
      <td>Musical</td>
    </tr>
    <tr>
      <th>11739</th>
      <td>tt1640569</td>
      <td>The Super</td>
      <td>The Super</td>
      <td>2010</td>
      <td>98.0</td>
      <td>Horror</td>
    </tr>
    <tr>
      <th>85256</th>
      <td>tt4935926</td>
      <td>Vicious Thunder</td>
      <td>Vicious Thunder</td>
      <td>2016</td>
      <td>95.0</td>
      <td>Action</td>
    </tr>
  </tbody>
</table>
</div>




```python
query_rt = "SELECT * FROM movie_ratings;"
df_rt = pd.read_sql_query(query_rt, conn)
df_rt.sample(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movie_id</th>
      <th>averagerating</th>
      <th>numvotes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>11163</th>
      <td>tt2639098</td>
      <td>6.4</td>
      <td>7</td>
    </tr>
    <tr>
      <th>68045</th>
      <td>tt3833568</td>
      <td>6.3</td>
      <td>7</td>
    </tr>
    <tr>
      <th>33265</th>
      <td>tt4048186</td>
      <td>3.6</td>
      <td>347</td>
    </tr>
    <tr>
      <th>41956</th>
      <td>tt7689934</td>
      <td>7.5</td>
      <td>71</td>
    </tr>
    <tr>
      <th>72563</th>
      <td>tt0938283</td>
      <td>4.1</td>
      <td>137734</td>
    </tr>
  </tbody>
</table>
</div>



## Data Preparation


```python

df_mb['original_title'] = df_mb['original_title'].str.strip().str.title()
df_mb['primary_title'] = df_mb['primary_title'].str.strip().str.title()
df_bom['title'] = df_bom['title'].str.strip().str.title()

# Merge on original_title
df_original_title_merge = pd.merge(df_mb, df_bom, left_on='original_title', right_on='title', how='inner').drop( columns=['original_title', 'primary_title'])

# Merge on primary_title
df_primary_title_merge = pd.merge(df_mb, df_bom, left_on='primary_title', right_on='title', how='inner').drop( columns=['original_title', 'primary_title'])

# Merge together
df_movie_gross = pd.concat([df_original_title_merge, df_primary_title_merge]).drop_duplicates(subset=df_primary_title_merge.columns)

df_movie_gross_rt = pd.merge(df_movie_gross, df_rt, on='movie_id', how='inner')

df_movie_gross_rt['domestic_gross']  = pd.to_numeric(df_movie_gross_rt['domestic_gross'], errors='coerce') 
df_movie_gross_rt['foreign_gross'] = pd.to_numeric(df_movie_gross_rt['foreign_gross'], errors='coerce')  

# Handle missing values
df_movie_gross_rt['foreign_gross'] = df_movie_gross_rt['foreign_gross'].fillna(0)  # Replace NaN with 0
df_movie_gross_rt['domestic_gross'] = df_movie_gross_rt['domestic_gross'].fillna(0)  # Replace NaN with 0
df_movie_gross_rt['runtime_minutes'] = df_movie_gross_rt['runtime_minutes'].fillna(df_movie_gross_rt['runtime_minutes'].median())  # Replace fill with median to avoid skewing with 0
df_movie_gross_rt['worldwide_gross'] = (df_movie_gross_rt['domestic_gross'] + df_movie_gross_rt['foreign_gross']) 

df_movie_gross_rt['worldwide_gross_m'] = df_movie_gross_rt['worldwide_gross'] / 1_000_000
df_movie_gross_rt['domestic_gross_m'] = df_movie_gross_rt['domestic_gross'] / 1_000_000
df_movie_gross_rt['foreign_gross_m'] = df_movie_gross_rt['foreign_gross'] / 1_000_000


df_clean = df_movie_gross_rt
df_clean.sample(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movie_id</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
      <th>title</th>
      <th>studio</th>
      <th>domestic_gross</th>
      <th>foreign_gross</th>
      <th>year</th>
      <th>averagerating</th>
      <th>numvotes</th>
      <th>worldwide_gross</th>
      <th>worldwide_gross_m</th>
      <th>domestic_gross_m</th>
      <th>foreign_gross_m</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3074</th>
      <td>tt5606538</td>
      <td>2017</td>
      <td>125.0</td>
      <td>Action</td>
      <td>Confidential Assignment</td>
      <td>CJ</td>
      <td>476000.0</td>
      <td>55500000.0</td>
      <td>2017</td>
      <td>6.5</td>
      <td>1828</td>
      <td>55976000.0</td>
      <td>55.976</td>
      <td>0.476</td>
      <td>55.5</td>
    </tr>
    <tr>
      <th>1571</th>
      <td>tt2624412</td>
      <td>2014</td>
      <td>88.0</td>
      <td>Drama</td>
      <td>Boulevard</td>
      <td>SM</td>
      <td>126000.0</td>
      <td>0.0</td>
      <td>2015</td>
      <td>5.8</td>
      <td>7446</td>
      <td>126000.0</td>
      <td>0.126</td>
      <td>0.126</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>298</th>
      <td>tt1235189</td>
      <td>2010</td>
      <td>98.0</td>
      <td>Action,Comedy,Crime</td>
      <td>Wild Target</td>
      <td>Free</td>
      <td>109000.0</td>
      <td>3300000.0</td>
      <td>2010</td>
      <td>6.8</td>
      <td>33915</td>
      <td>3409000.0</td>
      <td>3.409</td>
      <td>0.109</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>1220</th>
      <td>tt2097298</td>
      <td>2015</td>
      <td>129.0</td>
      <td>Biography,Drama,Sport</td>
      <td>Mcfarland, Usa</td>
      <td>BV</td>
      <td>44500000.0</td>
      <td>1200000.0</td>
      <td>2015</td>
      <td>7.4</td>
      <td>31735</td>
      <td>45700000.0</td>
      <td>45.700</td>
      <td>44.500</td>
      <td>1.2</td>
    </tr>
    <tr>
      <th>2170</th>
      <td>tt4741754</td>
      <td>2014</td>
      <td>109.0</td>
      <td>Thriller</td>
      <td>The Call</td>
      <td>TriS</td>
      <td>51900000.0</td>
      <td>16700000.0</td>
      <td>2013</td>
      <td>6.6</td>
      <td>5</td>
      <td>68600000.0</td>
      <td>68.600</td>
      <td>51.900</td>
      <td>16.7</td>
    </tr>
  </tbody>
</table>
</div>



## Analysis and Results


```python
# Analysis 1: Top Grossing Genres at the Box Office 
genre_gross = df_clean.groupby('genres').agg({
    'worldwide_gross_m': ['mean', 'median', 'count'],
    'domestic_gross_m': 'mean',
    'foreign_gross_m': 'mean',
    'averagerating': 'mean'
}).round(2).sort_values(('worldwide_gross_m', 'mean'), ascending=False)

# Reset index and rename columns
genre_gross.columns = ['avg_gross_millions', 'median_gross_millions', 'movie_count', 'avg_domestic_gross_m', 'avg_foreign_gross_m', 'avg_rating']
genre_gross = genre_gross.reset_index()


# Double Vertical Bar Plot for Domestic and Foreign Gross
plt.figure(figsize=(12, 6))
bar_width = 0.35
index = np.arange(len(genre_gross.head(10)))

# Plot bars
plt.bar(index, genre_gross['avg_domestic_gross_m'].head(10), bar_width, label='Domestic Gross', color='#36A2EB')
plt.bar(index + bar_width, genre_gross['avg_foreign_gross_m'].head(10), bar_width, label='Foreign Gross', color='#FFCE56')

# Customize plot
plt.xlabel('Genre')
plt.ylabel('Average Gross (M$)')
plt.title('Domestic vs. Foreign Gross for Top 10 Genres by Worldwide Gross')
plt.xticks(index + bar_width / 2, genre_gross['genres'].head(10), rotation=45)
plt.legend()
plt.tight_layout()
plt.show()



```


    
![png](./Images/output_15_0.png)
    



```python
# Calculate Pearson correlation
correlation = df_clean[['numvotes', 'worldwide_gross']].corr().iloc[0, 1].round(2)
print(f"Correlation between numvotes and worldwide_gross: {correlation}")

# Bin numvotes into quartiles and get bin edges
bins = pd.qcut(df_clean['numvotes'], q=4, retbins=True, labels=['Low', 'Medium-Low', 'Medium-High', 'High'])
df_clean['votes_bin'] = bins[0]
bin_edges = bins[1].round().astype(int)

# Aggregate total gross by votes bin
votes_gross = df_clean.groupby('votes_bin', observed=True).agg({
    'worldwide_gross': ['sum', 'count'],
    'averagerating': 'mean'
}).round(2).reset_index()
votes_gross.columns = ['votes_bin', 'total_gross_millions', 'movie_count', 'avg_rating']

total_gross = votes_gross['total_gross_millions'].sum()
votes_gross['gross_share'] = (votes_gross['total_gross_millions'] / total_gross * 100).round(2)

bin_labels = [
    f"Low ({bin_edges[0]:,}-{bin_edges[1]:,}) - ${votes_gross['total_gross_millions'][0]:,.2f}",
    f"Medium-Low ({bin_edges[1]:,}-{bin_edges[2]:,}) - ${votes_gross['total_gross_millions'][1]:,.2f}",
    f"Medium-High ({bin_edges[2]:,}-{bin_edges[3]:,}) - ${votes_gross['total_gross_millions'][2]:,.2f}",
    f"High (>{bin_edges[3]:,}) - ${votes_gross['total_gross_millions'][3]:,.2f}"
]

plt.figure(figsize=(10, 8))
plt.pie(votes_gross['gross_share'], labels=bin_labels, autopct='%1.1f%%', 
        colors=['#36A2EB', '#FFCE56', 'brown', 'lightgreen'])
plt.title('Share of Total Worldwide Gross by Number of Votes Bin') 
plt.tight_layout()
plt.show()
```

    Correlation between numvotes and worldwide_gross: 0.65
    


    
![png](./Images/output_16_1.png)
    



```python
# Split genres and explode into separate rows
df_clean['genres_split'] = df_clean['genres'].str.split(',')
df_genres = df_clean.explode('genres_split')

# Group by genres_split to get movie count, total gross, and average numvotes
genre_stats = df_genres.groupby('genres_split').agg({
    'movie_id': 'count',
    'worldwide_gross': 'sum',
    'numvotes': 'mean',
    'averagerating': 'mean'
}).reset_index().rename(columns={'movie_id': 'movie_count', 'worldwide_gross': 'total_gross_millions', 'numvotes': 'avg_numvotes'})

# Convert gross to millions
genre_stats['total_gross_millions'] = (genre_stats['total_gross_millions'] / 1_000_000).round(2)
genre_stats['avg_numvotes'] = genre_stats['avg_numvotes'].round(0).astype(int)

# Select top 10 genres by movie count
top_genres = genre_stats.nlargest(10, 'movie_count')


plt.figure(figsize=(12, 8))
scatter = plt.scatter(
    top_genres['movie_count'], 
    top_genres['total_gross_millions'], 
    s=top_genres['avg_numvotes'] / 10,
    c=top_genres['averagerating'], 
    cmap='viridis', 
    alpha=0.6
)
plt.colorbar(label='Average Rating')
for i, genre in enumerate(top_genres['genres_split']):
    plt.annotate(genre, (top_genres['movie_count'].iloc[i], top_genres['total_gross_millions'].iloc[i]))
plt.title('Top Genres: Movie Count vs. Total Worldwide Gross')
plt.xlabel('Number of Movies')
plt.ylabel('Total Gross ($M)')
plt.grid(True)
plt.tight_layout()
plt.show()
 
```


    
![png](./Images/output_17_0.png)
    



```python
# Calculate gross per film
studio_performance['gross_per_film'] = studio_performance['total_gross'] / studio_performance['movie_count']

# Plot most efficient studios
plt.figure(figsize=(14, 7))
efficient_studios = studio_performance.groupby('studio')['gross_per_film'].mean().nlargest(10).index
sns.lineplot(data=studio_performance[studio_performance['studio'].isin(efficient_studios)],
             x='year',
             y='gross_per_film',
             hue='studio',
             marker='o')
plt.title('Top 5 Studios by Average Gross per Film')
plt.ylabel('Average Gross per Film (Millions USD)')
plt.xlabel('Year')
plt.grid(True)
plt.show()
```


    
![png](./Images/output_18_0.png)
    


### Business Recommendation 1

Focus on High-Grossing Genres:
The analysis reveals that certain genres, such as Action, Adventure, and Comedy, consistently perform well at the box office. To maximize ROI, the studio should prioritize producing films in these genres. Additionally, hybrid genres (e.g., Action-Drama or Adventure-Comedy) have shown strong audience appeal, offering opportunities for creative storytelling while minimizing risk.

### Business Recommendation 2

Leverage International Markets:
Foreign box office revenue often surpasses domestic earnings, indicating the importance of global appeal. The studio should:
Develop culturally adaptable content with universal themes.
Partner with international distributors to enhance market penetration.
Invest in marketing campaigns tailored to key regions like Asia and Europe.

### Business Recommendation 3

Enhance competitiveness by leveraging insights from high-performing studios and forming strategic collaborations:
Analysis identifies studios like BV, Fox, and LG/S as consistent high performers in the market. Partner with top-performing studios like (co-productions, distribution deals) to leverage the company expertise and market reach.

## Conclusion

By strategically partnering with top-performing studios, prioritizing high-grossing genres, expanding into international markets, and optimizing budget allocation, the new film studio can:
- Accelerate market entry by leveraging proven industry strategies
- Minimize financial and creative risks through data-driven decisions
- Establish competitive positioning in the global film industry
- Produce commercially successful films that align with audience preferences

### Next Steps

- Finalize partnership opportunities with leading studios.
- Develop a balanced production slate combining franchise films and original content.
- Implement robust market research for international expansion.
- Establish budget monitoring systems for optimal resource allocation.
- This comprehensive strategy ensures both immediate competitiveness and long-term sustainability in the evolving film landscape.



## Repo Structure
```
akademi_dscb_phase2_project/
│
├── Data/
│   ├── bom.movie_gross.csv
│   ├── im.db.rar
├── Images/
│   ├── cinema.jpg
│   ├── output_15_0.png
│   ├── output_16_1.png
│   ├── output_17_0.png
│   ├── output_18_0.png
├── .gitignore
├── index.ipynb
├── index.ipynb.pdf
├── LICENSE
├── README.md
├── presentation.pdf
```