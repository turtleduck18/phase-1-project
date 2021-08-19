![example](images/director_shot.jpeg)

# Project Title

## **Author:** Madeleine Reiser
***

## Overview

This project used exploratory data analysis to help the business stake owners of a potential new movie streaming service figure out the best ways to make box office revenue. Datasets from IMDB and Box Office Mojo were analyzed to show what movies have made the most money, which are the highest rated, and which are the most popular, from the years 2010-2018. 

## Business Problem

Microsoft Stake Owners are interested in developing their own streaming service but aren’t sure what kinds of movies do the best in the box office. 


## Data Understanding

Using datasets from IMDB and Box Office Mojo, this project explores how box office revenue is correlated with variables such as ratings, popularity (which is measured by number of reviews left by viewers), specific studios and genres.  


## Import necessary packages and load Datasets


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import gzip
import json
import requests
import re
from bs4 import BeautifulSoup
%matplotlib inline
```


```python
#load data 
imdb_title_ratings = pd.read_csv('imdb.title.ratings.csv.gz')
imdb_title_basics = pd.read_csv('imdb.title.basics.csv.gz')
bom_movie_gross = pd.read_csv('bom.movie_gross.csv.gz')
```

## IMDB Ratings Dataset

Contains "tconst", which appears to be the code for each movie, the film's average rating, and the number of votes on IMDB


```python
imdb_title_ratings.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 73856 entries, 0 to 73855
    Data columns (total 3 columns):
     #   Column         Non-Null Count  Dtype  
    ---  ------         --------------  -----  
     0   tconst         73856 non-null  object 
     1   averagerating  73856 non-null  float64
     2   numvotes       73856 non-null  int64  
    dtypes: float64(1), int64(1), object(1)
    memory usage: 1.7+ MB
    


```python
imdb_title_ratings.head()
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
      <th>tconst</th>
      <th>averagerating</th>
      <th>numvotes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt10356526</td>
      <td>8.3</td>
      <td>31</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt10384606</td>
      <td>8.9</td>
      <td>559</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt1042974</td>
      <td>6.4</td>
      <td>20</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt1043726</td>
      <td>4.2</td>
      <td>50352</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt1060240</td>
      <td>6.5</td>
      <td>21</td>
    </tr>
  </tbody>
</table>
</div>



## IMDB Basic Dataset

Contains more information for each film on IMDB including "tconst" again, Primary and Original titles, start year, runtime in minutes, and the genre. 


```python
imdb_title_basics.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 146144 entries, 0 to 146143
    Data columns (total 6 columns):
     #   Column           Non-Null Count   Dtype  
    ---  ------           --------------   -----  
     0   tconst           146144 non-null  object 
     1   primary_title    146144 non-null  object 
     2   original_title   146123 non-null  object 
     3   start_year       146144 non-null  int64  
     4   runtime_minutes  114405 non-null  float64
     5   genres           140736 non-null  object 
    dtypes: float64(1), int64(1), object(4)
    memory usage: 6.7+ MB
    

## Box Office Mojo dataset

Contains the title, studio, domestic gross, foreign gross and year released according to data from Box Office Mojo 


```python
bom_movie_gross.info()

```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 3387 entries, 0 to 3386
    Data columns (total 5 columns):
     #   Column          Non-Null Count  Dtype  
    ---  ------          --------------  -----  
     0   title           3387 non-null   object 
     1   studio          3382 non-null   object 
     2   domestic_gross  3359 non-null   float64
     3   foreign_gross   2037 non-null   object 
     4   year            3387 non-null   int64  
    dtypes: float64(1), int64(1), object(3)
    memory usage: 132.4+ KB
    


```python
bom_movie_gross.describe()

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
      <th>domestic_gross</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>3.359000e+03</td>
      <td>3387.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>2.874585e+07</td>
      <td>2013.958075</td>
    </tr>
    <tr>
      <th>std</th>
      <td>6.698250e+07</td>
      <td>2.478141</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000e+02</td>
      <td>2010.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1.200000e+05</td>
      <td>2012.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1.400000e+06</td>
      <td>2014.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.790000e+07</td>
      <td>2016.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>9.367000e+08</td>
      <td>2018.000000</td>
    </tr>
  </tbody>
</table>
</div>



## Data Preparation

Describe and justify the process for preparing the data for analysis.

***
Questions to consider:
* Were there variables you dropped or created?
* How did you address missing values or outliers?
* Why are these choices appropriate given the data and the business problem?
***

## Box Office Mojo dataset


```python
bom_movie_gross = pd.read_csv('bom.movie_gross.csv.gz')
bom_movie_gross.sort_values(by='year', ascending=False)
bom_movie_gross.sort_values(by='domestic_gross', ascending=False).head(30)
bom_movie_gross.info()
#last year is 2018
#includes studio
#foreign_gross is an object
bom_movie_gross.columns = ['Title', 'Studio', 'Domestic Gross', 
                        'Foreign Gross', 'BOM Year']
bom_movie_gross
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 3387 entries, 0 to 3386
    Data columns (total 5 columns):
     #   Column          Non-Null Count  Dtype  
    ---  ------          --------------  -----  
     0   title           3387 non-null   object 
     1   studio          3382 non-null   object 
     2   domestic_gross  3359 non-null   float64
     3   foreign_gross   2037 non-null   object 
     4   year            3387 non-null   int64  
    dtypes: float64(1), int64(1), object(3)
    memory usage: 132.4+ KB
    




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
      <th>Title</th>
      <th>Studio</th>
      <th>Domestic Gross</th>
      <th>Foreign Gross</th>
      <th>BOM Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Toy Story 3</td>
      <td>BV</td>
      <td>415000000.0</td>
      <td>652000000</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alice in Wonderland (2010)</td>
      <td>BV</td>
      <td>334200000.0</td>
      <td>691300000</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Harry Potter and the Deathly Hallows Part 1</td>
      <td>WB</td>
      <td>296000000.0</td>
      <td>664300000</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Inception</td>
      <td>WB</td>
      <td>292600000.0</td>
      <td>535700000</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Shrek Forever After</td>
      <td>P/DW</td>
      <td>238700000.0</td>
      <td>513900000</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>3382</th>
      <td>The Quake</td>
      <td>Magn.</td>
      <td>6200.0</td>
      <td>NaN</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>3383</th>
      <td>Edward II (2018 re-release)</td>
      <td>FM</td>
      <td>4800.0</td>
      <td>NaN</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>3384</th>
      <td>El Pacto</td>
      <td>Sony</td>
      <td>2500.0</td>
      <td>NaN</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>3385</th>
      <td>The Swan</td>
      <td>Synergetic</td>
      <td>2400.0</td>
      <td>NaN</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>3386</th>
      <td>An Actor Prepares</td>
      <td>Grav.</td>
      <td>1700.0</td>
      <td>NaN</td>
      <td>2018</td>
    </tr>
  </tbody>
</table>
<p>3387 rows × 5 columns</p>
</div>



## Merging the two IMDB Sets

I merged both IMDB Datasets to keep all IMDB data together. 


```python
#Join imdb_title_basics and imdb_title_ratings 
imdb_ratings_joined = imdb_title_basics.merge(imdb_title_ratings, on = 'tconst',  how = 'outer')
imdb_ratings_joined.sort_values(by = 'start_year', ascending = False)
#data begins at 2010 and goes up to 2115, limit year to 2010-2018

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
      <th>tconst</th>
      <th>primary_title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
      <th>averagerating</th>
      <th>numvotes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>89506</th>
      <td>tt5174640</td>
      <td>100 Years</td>
      <td>100 Years</td>
      <td>2115</td>
      <td>NaN</td>
      <td>Drama</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>96592</th>
      <td>tt5637536</td>
      <td>Avatar 5</td>
      <td>Avatar 5</td>
      <td>2027</td>
      <td>NaN</td>
      <td>Action,Adventure,Fantasy</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2949</th>
      <td>tt10300398</td>
      <td>Untitled Star Wars Film</td>
      <td>Untitled Star Wars Film</td>
      <td>2026</td>
      <td>NaN</td>
      <td>Fantasy</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>52213</th>
      <td>tt3095356</td>
      <td>Avatar 4</td>
      <td>Avatar 4</td>
      <td>2025</td>
      <td>NaN</td>
      <td>Action,Adventure,Fantasy</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>105187</th>
      <td>tt6149054</td>
      <td>Fantastic Beasts and Where to Find Them 5</td>
      <td>Fantastic Beasts and Where to Find Them 5</td>
      <td>2024</td>
      <td>NaN</td>
      <td>Adventure,Family,Fantasy</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>74712</th>
      <td>tt4264626</td>
      <td>Civil War Life: Shot to Pieces</td>
      <td>Civil War Life: Shot to Pieces</td>
      <td>2010</td>
      <td>79.0</td>
      <td>Documentary</td>
      <td>5.7</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>14471</th>
      <td>tt1716746</td>
      <td>Heinrich Kieber - Datendieb</td>
      <td>Heinrich Kieber - Datendieb</td>
      <td>2010</td>
      <td>52.0</td>
      <td>Documentary</td>
      <td>7.7</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>74692</th>
      <td>tt4263706</td>
      <td>Mushrooms of America</td>
      <td>Mushrooms of America</td>
      <td>2010</td>
      <td>46.0</td>
      <td>Adventure,Comedy,Documentary</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>118065</th>
      <td>tt7059624</td>
      <td>Zamana</td>
      <td>Zamana</td>
      <td>2010</td>
      <td>140.0</td>
      <td>Drama</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>94000</th>
      <td>tt5475580</td>
      <td>A Boy and A Girl</td>
      <td>A Boy and A Girl</td>
      <td>2010</td>
      <td>NaN</td>
      <td>Romance</td>
      <td>6.0</td>
      <td>9.0</td>
    </tr>
  </tbody>
</table>
<p>146144 rows × 8 columns</p>
</div>



I renamed the columns to make them more readable. 


```python
imdb_ratings_joined.columns = ['tconst', 'Title', 'Original Title', 
                               'IMDB Year', 'Runtime (in minutes)', 
                              'Genres', 'Average Rating', 'Number of Votes']
imdb_ratings_joined.sort_values(by = 'IMDB Year')
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
      <th>tconst</th>
      <th>Title</th>
      <th>Original Title</th>
      <th>IMDB Year</th>
      <th>Runtime (in minutes)</th>
      <th>Genres</th>
      <th>Average Rating</th>
      <th>Number of Votes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>9599</th>
      <td>tt1566491</td>
      <td>Brainiacs in La La Land</td>
      <td>Brainiacs in La La Land</td>
      <td>2010</td>
      <td>NaN</td>
      <td>Comedy</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>43264</th>
      <td>tt2578092</td>
      <td>Fireplace for your Home: Crackling Fireplace w...</td>
      <td>Fireplace for your Home: Crackling Fireplace w...</td>
      <td>2010</td>
      <td>61.0</td>
      <td>Music</td>
      <td>6.9</td>
      <td>17.0</td>
    </tr>
    <tr>
      <th>11550</th>
      <td>tt1634300</td>
      <td>Role/Play</td>
      <td>Role/Play</td>
      <td>2010</td>
      <td>85.0</td>
      <td>Drama,Romance</td>
      <td>5.0</td>
      <td>894.0</td>
    </tr>
    <tr>
      <th>11551</th>
      <td>tt1634332</td>
      <td>Johan1</td>
      <td>Johan Primero</td>
      <td>2010</td>
      <td>78.0</td>
      <td>Comedy,Drama,Romance</td>
      <td>7.1</td>
      <td>124.0</td>
    </tr>
    <tr>
      <th>11552</th>
      <td>tt1634334</td>
      <td>Hands Up</td>
      <td>Les mains en l'air</td>
      <td>2010</td>
      <td>90.0</td>
      <td>Drama</td>
      <td>6.2</td>
      <td>271.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2948</th>
      <td>tt10300396</td>
      <td>Untitled Star Wars Film</td>
      <td>Untitled Star Wars Film</td>
      <td>2024</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>52213</th>
      <td>tt3095356</td>
      <td>Avatar 4</td>
      <td>Avatar 4</td>
      <td>2025</td>
      <td>NaN</td>
      <td>Action,Adventure,Fantasy</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2949</th>
      <td>tt10300398</td>
      <td>Untitled Star Wars Film</td>
      <td>Untitled Star Wars Film</td>
      <td>2026</td>
      <td>NaN</td>
      <td>Fantasy</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>96592</th>
      <td>tt5637536</td>
      <td>Avatar 5</td>
      <td>Avatar 5</td>
      <td>2027</td>
      <td>NaN</td>
      <td>Action,Adventure,Fantasy</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>89506</th>
      <td>tt5174640</td>
      <td>100 Years</td>
      <td>100 Years</td>
      <td>2115</td>
      <td>NaN</td>
      <td>Drama</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>146144 rows × 8 columns</p>
</div>



I limited the data from the joined IMDB datasets to 2018, which is the last year this set was updated. 


```python
#get relevant years (2010-2018) 
imdb_ratings_joined2010_2018 = imdb_ratings_joined.loc[imdb_ratings_joined['IMDB Year'] <= 2018]
imdb_ratings_joined2010_2018.sort_values(by = 'IMDB Year', ascending = False)
imdb_ratings_joined2010_2018.sort_values(by = 'Average Rating', ascending = False)


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
      <th>tconst</th>
      <th>Title</th>
      <th>Original Title</th>
      <th>IMDB Year</th>
      <th>Runtime (in minutes)</th>
      <th>Genres</th>
      <th>Average Rating</th>
      <th>Number of Votes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>85712</th>
      <td>tt4960818</td>
      <td>Revolution Food</td>
      <td>Revolution Food</td>
      <td>2015</td>
      <td>70.0</td>
      <td>Documentary</td>
      <td>10.0</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>117359</th>
      <td>tt6991826</td>
      <td>A Dedicated Life: Phoebe Brand Beyond the Group</td>
      <td>A Dedicated Life: Phoebe Brand Beyond the Group</td>
      <td>2015</td>
      <td>93.0</td>
      <td>Documentary</td>
      <td>10.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>44279</th>
      <td>tt2632430</td>
      <td>Hercule contre Hermès</td>
      <td>Hercule contre Hermès</td>
      <td>2012</td>
      <td>72.0</td>
      <td>Documentary</td>
      <td>10.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>4016</th>
      <td>tt10378660</td>
      <td>The Dark Knight: The Ballad of the N Word</td>
      <td>The Dark Knight: The Ballad of the N Word</td>
      <td>2018</td>
      <td>129.0</td>
      <td>Comedy,Drama</td>
      <td>10.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>1857</th>
      <td>tt10176328</td>
      <td>Exteriores: Mulheres Brasileiras na Diplomacia</td>
      <td>Exteriores: Mulheres Brasileiras na Diplomacia</td>
      <td>2018</td>
      <td>52.0</td>
      <td>Documentary</td>
      <td>10.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>146136</th>
      <td>tt9916186</td>
      <td>Illenau - die Geschichte einer ehemaligen Heil...</td>
      <td>Illenau - die Geschichte einer ehemaligen Heil...</td>
      <td>2017</td>
      <td>84.0</td>
      <td>Documentary</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>146140</th>
      <td>tt9916622</td>
      <td>Rodolpho Teóphilo - O Legado de um Pioneiro</td>
      <td>Rodolpho Teóphilo - O Legado de um Pioneiro</td>
      <td>2015</td>
      <td>NaN</td>
      <td>Documentary</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>146141</th>
      <td>tt9916706</td>
      <td>Dankyavar Danka</td>
      <td>Dankyavar Danka</td>
      <td>2013</td>
      <td>NaN</td>
      <td>Comedy</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>146142</th>
      <td>tt9916730</td>
      <td>6 Gunn</td>
      <td>6 Gunn</td>
      <td>2017</td>
      <td>116.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>146143</th>
      <td>tt9916754</td>
      <td>Chico Albuquerque - Revelações</td>
      <td>Chico Albuquerque - Revelações</td>
      <td>2013</td>
      <td>NaN</td>
      <td>Documentary</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>136702 rows × 8 columns</p>
</div>



I dropped rows where average rating is NaN



```python
rating_na_dropped = imdb_ratings_joined2010_2018[imdb_ratings_joined2010_2018['Average Rating'].notna()]

```

I limited the average rating to 8 or better, and show the value counts per genre. This merged dataset doesn't include data from BOM, only IMDB.


```python
#limit average rating to 8 or better
rating_na_dropped.loc[rating_na_dropped['Average Rating'] >= 8]
imdb_8_or_better = rating_na_dropped.loc[rating_na_dropped['Average Rating'] >= 8]
#genres where rating was better than an 8
imdb_8_or_better['Genres'].value_counts().head(10)

```




    Documentary                      2715
    Drama                            1234
    Comedy                            402
    Comedy,Drama                      220
    Documentary,Music                 204
    Documentary,Drama                 158
    Biography,Documentary             155
    Biography,Documentary,History     142
    Documentary,History               131
    Biography,Documentary,Drama       117
    Name: Genres, dtype: int64



## Merge the BOM dataset with the IMDB dataset

Joined the tables by 'Title', and dropped the year given by BOM since we already have the year from IMDB, which provides most of the data. 


```python
alljoined = bom_movie_gross.merge(rating_na_dropped, on = 'Title',  how = 'inner')
alljoined
#sort by # votes, gives best idea of rating
alljoined.sort_values(by = 'Domestic Gross', ascending = False)


#dropping BOM year because it has the least entries and most of 
#the data is from IMDB 
alljoined.drop_duplicates()
alljoined.drop(['BOM Year'], axis='columns', inplace=True)
alljoined.info()

```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 3015 entries, 0 to 3014
    Data columns (total 11 columns):
     #   Column                Non-Null Count  Dtype  
    ---  ------                --------------  -----  
     0   Title                 3015 non-null   object 
     1   Studio                3012 non-null   object 
     2   Domestic Gross        2993 non-null   float64
     3   Foreign Gross         1821 non-null   object 
     4   tconst                3015 non-null   object 
     5   Original Title        3015 non-null   object 
     6   IMDB Year             3015 non-null   int64  
     7   Runtime (in minutes)  2970 non-null   float64
     8   Genres                3008 non-null   object 
     9   Average Rating        3015 non-null   float64
     10  Number of Votes       3015 non-null   float64
    dtypes: float64(4), int64(1), object(6)
    memory usage: 282.7+ KB
    

I further sliced the dataset to limit results to any films with an average rating of 8 or better to explore which films are highest rated


```python
alljoined_8_or_better = alljoined.loc[alljoined['Average Rating'] >= 8]
#limit alljoined table to ratings 8 or better
alljoined_8_or_better.sort_values(by = 'Average Rating')
alljoined_8_or_better['Genres'].value_counts()
```




    Drama                         18
    Documentary                   10
    Action,Adventure,Sci-Fi        4
    Adventure,Animation,Comedy     4
    Drama,Thriller                 3
                                  ..
    Drama,Sport                    1
    Biography,Comedy,Drama         1
    Crime                          1
    Action                         1
    Comedy,Documentary,Drama       1
    Name: Genres, Length: 69, dtype: int64




```python
alljoined.sort_values(by = 'Domestic Gross', ascending = False)
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
      <th>Title</th>
      <th>Studio</th>
      <th>Domestic Gross</th>
      <th>Foreign Gross</th>
      <th>tconst</th>
      <th>Original Title</th>
      <th>IMDB Year</th>
      <th>Runtime (in minutes)</th>
      <th>Genres</th>
      <th>Average Rating</th>
      <th>Number of Votes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2740</th>
      <td>Black Panther</td>
      <td>BV</td>
      <td>700100000.0</td>
      <td>646900000</td>
      <td>tt1825683</td>
      <td>Black Panther</td>
      <td>2018</td>
      <td>134.0</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>7.3</td>
      <td>516148.0</td>
    </tr>
    <tr>
      <th>2739</th>
      <td>Avengers: Infinity War</td>
      <td>BV</td>
      <td>678800000.0</td>
      <td>1,369.5</td>
      <td>tt4154756</td>
      <td>Avengers: Infinity War</td>
      <td>2018</td>
      <td>149.0</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>8.5</td>
      <td>670926.0</td>
    </tr>
    <tr>
      <th>1608</th>
      <td>Jurassic World</td>
      <td>Uni.</td>
      <td>652300000.0</td>
      <td>1,019.4</td>
      <td>tt0369610</td>
      <td>Jurassic World</td>
      <td>2015</td>
      <td>124.0</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>7.0</td>
      <td>539338.0</td>
    </tr>
    <tr>
      <th>2424</th>
      <td>Star Wars: The Last Jedi</td>
      <td>BV</td>
      <td>620200000.0</td>
      <td>712400000</td>
      <td>tt2527336</td>
      <td>Star Wars: Episode VIII - The Last Jedi</td>
      <td>2017</td>
      <td>152.0</td>
      <td>Action,Adventure,Fantasy</td>
      <td>7.1</td>
      <td>462903.0</td>
    </tr>
    <tr>
      <th>2742</th>
      <td>Incredibles 2</td>
      <td>BV</td>
      <td>608600000.0</td>
      <td>634200000</td>
      <td>tt3606756</td>
      <td>Incredibles 2</td>
      <td>2018</td>
      <td>118.0</td>
      <td>Action,Adventure,Animation</td>
      <td>7.7</td>
      <td>203510.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2162</th>
      <td>Solace</td>
      <td>LGP</td>
      <td>NaN</td>
      <td>22400000</td>
      <td>tt2140411</td>
      <td>Solace</td>
      <td>2013</td>
      <td>76.0</td>
      <td>Thriller</td>
      <td>7.2</td>
      <td>59.0</td>
    </tr>
    <tr>
      <th>2163</th>
      <td>Solace</td>
      <td>LGP</td>
      <td>NaN</td>
      <td>22400000</td>
      <td>tt3240102</td>
      <td>Solace</td>
      <td>2018</td>
      <td>81.0</td>
      <td>Drama</td>
      <td>4.9</td>
      <td>28.0</td>
    </tr>
    <tr>
      <th>2281</th>
      <td>Viral</td>
      <td>W/Dim.</td>
      <td>NaN</td>
      <td>552000</td>
      <td>tt2594078</td>
      <td>Viral</td>
      <td>2013</td>
      <td>95.0</td>
      <td>Comedy,Horror,Thriller</td>
      <td>4.4</td>
      <td>227.0</td>
    </tr>
    <tr>
      <th>2282</th>
      <td>Viral</td>
      <td>W/Dim.</td>
      <td>NaN</td>
      <td>552000</td>
      <td>tt2597892</td>
      <td>Viral</td>
      <td>2016</td>
      <td>85.0</td>
      <td>Drama,Horror,Sci-Fi</td>
      <td>5.5</td>
      <td>7150.0</td>
    </tr>
    <tr>
      <th>2490</th>
      <td>Secret Superstar</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>122000000</td>
      <td>tt6108090</td>
      <td>Secret Superstar</td>
      <td>2017</td>
      <td>150.0</td>
      <td>Drama,Music</td>
      <td>8.0</td>
      <td>16563.0</td>
    </tr>
  </tbody>
</table>
<p>3015 rows × 11 columns</p>
</div>



Created a master dataframe by merging the BOM dataset with the merged IMDB datasets


```python
alljoined = bom_movie_gross.merge(rating_na_dropped, on = 'Title',  how = 'inner')
alljoined['Foreign Gross'] = pd.to_numeric(alljoined['Foreign Gross'], errors = 'coerce')
alljoined.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 3015 entries, 0 to 3014
    Data columns (total 12 columns):
     #   Column                Non-Null Count  Dtype  
    ---  ------                --------------  -----  
     0   Title                 3015 non-null   object 
     1   Studio                3012 non-null   object 
     2   Domestic Gross        2993 non-null   float64
     3   Foreign Gross         1817 non-null   float64
     4   BOM Year              3015 non-null   int64  
     5   tconst                3015 non-null   object 
     6   Original Title        3015 non-null   object 
     7   IMDB Year             3015 non-null   int64  
     8   Runtime (in minutes)  2970 non-null   float64
     9   Genres                3008 non-null   object 
     10  Average Rating        3015 non-null   float64
     11  Number of Votes       3015 non-null   float64
    dtypes: float64(5), int64(2), object(5)
    memory usage: 306.2+ KB
    

## Data Modeling

I first created a correlation matrix to see which variables have a relationship with "Domestic Gross" or "Foreign Gross" 


```python
ax = plt.axes()
sns.set(font_scale = 1)
sns.heatmap(alljoined.corr(), ax = ax, annot = True, vmin=-1, vmax=1, center= 0, cmap= 'coolwarm')

ax.set_title('Correlation Matrix of Relevant Variables', fontsize = 18)
ax.set_xticklabels(ax.get_xticklabels(), fontsize = 13)
ax.set_yticklabels(ax.get_yticklabels(), fontsize = 13)

#Notable correlations: 
#Domestic & Foreign Gross = .83 
#Num Votes & FG = .56
#Num Votes & DG = .66
#Average Rating & Num Votes = .28
```




    [Text(0, 0.5, 'Domestic Gross'),
     Text(0, 1.5, 'Foreign Gross'),
     Text(0, 2.5, 'BOM Year'),
     Text(0, 3.5, 'IMDB Year'),
     Text(0, 4.5, 'Runtime (in minutes)'),
     Text(0, 5.5, 'Average Rating'),
     Text(0, 6.5, 'Number of Votes')]




    
![png](output_37_1.png)
    


# Notable correlations: 

Domestic Gross & Foreign Gross have a strong positive correlation of .83

Number of Votes & Domestic Gross have a moderate-strong positive correlation of .66

Number of Votes & Foreign Gross have a moderate-strong positive correlation of .56

Average Rating and Number of Votes have a mild positive correlation of .28




```python
with plt.style.context('bmh'):
    (alljoined.groupby(['Genres'])['Domestic Gross'].mean().sort_values(ascending = False).head(15)/100000000).plot(kind = 'barh')
    plt.title('Average Domestic Box Office Gross by Genre', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in 100s of Millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('Genre', fontsize = 14, fontweight = 'bold') 
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)
```


    
![png](output_39_0.png)
    



```python
with plt.style.context('bmh'):
    (alljoined_8_or_better.groupby(['Genres'])['Domestic Gross'].mean().sort_values(ascending = False).head(15)/100000000).plot(kind = 'barh')
    plt.title('Average Domestic Gross by Genre for films with an 8+ Rating', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in 100s of Millions', fontsize = 16, fontweight = 'bold')
    plt.ylabel('Genre', fontsize = 16, fontweight = 'bold')
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)

#if you care about ratings, Action, Thriller, Adventure, Sci-Fi is the way to go 
```


    
![png](output_40_0.png)
    



```python
with plt.style.context('bmh'):
    (alljoined.groupby(['IMDB Year'])['Domestic Gross'].mean().sort_values(ascending = False).head(15)/10000000).plot(kind = 'barh')
    plt.title('Average Domestic Gross by Year', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in 10s of Millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('IMDB Year', fontsize = 14, fontweight = 'bold')
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)
#why 2018 so high?  
```


    
![png](output_41_0.png)
    


I created a dataset from the master with data from only 2018, with the intention of exploring why it was the highest-grossing year. 


```python
just2018 = alljoined[alljoined['IMDB Year'] == 2018]
just2018['Genres'].value_counts()
```




    Drama                      23
    Comedy,Drama,Romance        7
    Comedy                      7
    Action,Crime,Drama          6
    Biography,Drama             6
                               ..
    Comedy,Family,Fantasy       1
    Adventure,Comedy,Sci-Fi     1
    Comedy,Musical,Romance      1
    Mystery,Thriller            1
    Drama,Horror,Sci-Fi         1
    Name: Genres, Length: 88, dtype: int64



The graph below shows that Action/Adventure/Sci-fi was the highest grossing genre in the highest grossing year, 2018. 


```python
with plt.style.context('bmh'):
    (just2018.groupby(['Genres'])['Domestic Gross'].mean().sort_values(ascending = False).head(15)/10000000).plot(kind = 'barh')
    plt.title('Average Domestic Gross by Genre 2018', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in 10s of Millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('Genre', fontsize = 14, fontweight = 'bold')
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)
#action/adventure/scifi

```


    
![png](output_45_0.png)
    


I was interested in exploring which genres had the most movies with a "high rating", meaning an 8 or better on IMDB. The bar graph below shows that Documentaries are the highest rated genre, with more than 2500 movies with a high rating. However, the graph "Sum Domestic Gross by Genre for films with an 8+ Rating on IMDB" lists "Documentary" toward the bottom of the list, with ~1.5 hundred million. This could be useful for gaining a reputation with independent documentarians. 


```python
with plt.style.context("bmh"):
    imdb_8_or_better['Genres'].value_counts().head(15).plot(kind = 'barh')
    plt.title('Number of Movies per Genre with a rating of 8+ on IMDB', fontsize = 16, fontweight = 'bold')
    plt.xlabel('Number of Movies Made', fontsize = 15, fontweight = 'bold')
    plt.ylabel('Genre', fontsize = 15, fontweight = 'bold')
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)
    plt.gca().invert_yaxis()

#if you're after ratings and don't necessarily care about popularity or gross income
#number of votes should be considered for a better idea 
#average number of votes per genres
```


    
![png](output_47_0.png)
    


Because of the strong positive correlation between Number of Votes and Domestic Box Office Gross, I decided to explore which genres garnered the most votes. 


```python
with plt.style.context('bmh'):
    (alljoined.groupby(['Genres'])['Number of Votes'].sum().sort_values(ascending = False).head(10)/1000000).plot(kind = 'barh')
    plt.title('Number of Votes per Genre on IMDB', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Votes in Millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('Genre', fontsize = 14, fontweight = 'bold')
    plt.xticks(fontsize = 12)
    plt.yticks(fontsize = 12)

```


    
![png](output_49_0.png)
    


I was interested in which production studios had the highest Domestic Box Office revenue, and which of their movies did best. 


```python
with plt.style.context('bmh'):
    (alljoined.groupby(['Studio'])['Domestic Gross'].sum().sort_values(ascending = False).head(10)/1000000000).plot(kind = 'barh')
    plt.title('Total Domestic Gross by Studio', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in Billions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('Studio', fontsize = 14, fontweight = 'bold')
    plt.xticks(fontsize = 12)
    plt.yticks(fontsize = 12)
```


    
![png](output_51_0.png)
    


The top 3 studios that made the most money in the Domestic Box Office are BV, Universal, and Fox.


```python
BVonly = alljoined[alljoined['Studio']== 'BV']
UNIonly = alljoined[alljoined['Studio']== 'Uni.']
FOXonly = alljoined[alljoined['Studio']== 'Fox']
```


```python
UNIonly.dropna()
FOXonly.dropna()
BVonly.dropna()
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
      <th>Title</th>
      <th>Studio</th>
      <th>Domestic Gross</th>
      <th>Foreign Gross</th>
      <th>BOM Year</th>
      <th>tconst</th>
      <th>Original Title</th>
      <th>IMDB Year</th>
      <th>Runtime (in minutes)</th>
      <th>Genres</th>
      <th>Average Rating</th>
      <th>Number of Votes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Toy Story 3</td>
      <td>BV</td>
      <td>415000000.0</td>
      <td>652000000.0</td>
      <td>2010</td>
      <td>tt0435761</td>
      <td>Toy Story 3</td>
      <td>2010</td>
      <td>103.0</td>
      <td>Adventure,Animation,Comedy</td>
      <td>8.3</td>
      <td>682218.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Tangled</td>
      <td>BV</td>
      <td>200800000.0</td>
      <td>391000000.0</td>
      <td>2010</td>
      <td>tt0398286</td>
      <td>Tangled</td>
      <td>2010</td>
      <td>100.0</td>
      <td>Adventure,Animation,Comedy</td>
      <td>7.8</td>
      <td>366366.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Prince of Persia: The Sands of Time</td>
      <td>BV</td>
      <td>90800000.0</td>
      <td>245600000.0</td>
      <td>2010</td>
      <td>tt0473075</td>
      <td>Prince of Persia: The Sands of Time</td>
      <td>2010</td>
      <td>116.0</td>
      <td>Action,Adventure,Fantasy</td>
      <td>6.6</td>
      <td>254975.0</td>
    </tr>
    <tr>
      <th>31</th>
      <td>The Sorcerer's Apprentice</td>
      <td>BV</td>
      <td>63200000.0</td>
      <td>152100000.0</td>
      <td>2010</td>
      <td>tt0963966</td>
      <td>The Sorcerer's Apprentice</td>
      <td>2010</td>
      <td>109.0</td>
      <td>Action,Adventure,Family</td>
      <td>6.1</td>
      <td>143862.0</td>
    </tr>
    <tr>
      <th>68</th>
      <td>The Last Song</td>
      <td>BV</td>
      <td>63000000.0</td>
      <td>26100000.0</td>
      <td>2010</td>
      <td>tt1294226</td>
      <td>The Last Song</td>
      <td>2010</td>
      <td>107.0</td>
      <td>Drama,Music,Romance</td>
      <td>6.0</td>
      <td>74914.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2753</th>
      <td>Ralph Breaks the Internet</td>
      <td>BV</td>
      <td>201100000.0</td>
      <td>328100000.0</td>
      <td>2018</td>
      <td>tt5848272</td>
      <td>Ralph Breaks the Internet</td>
      <td>2018</td>
      <td>112.0</td>
      <td>Adventure,Animation,Comedy</td>
      <td>7.1</td>
      <td>85694.0</td>
    </tr>
    <tr>
      <th>2757</th>
      <td>Solo: A Star Wars Story</td>
      <td>BV</td>
      <td>213800000.0</td>
      <td>179200000.0</td>
      <td>2018</td>
      <td>tt3778644</td>
      <td>Solo: A Star Wars Story</td>
      <td>2018</td>
      <td>135.0</td>
      <td>Action,Adventure,Fantasy</td>
      <td>7.0</td>
      <td>226243.0</td>
    </tr>
    <tr>
      <th>2763</th>
      <td>Mary Poppins Returns</td>
      <td>BV</td>
      <td>172000000.0</td>
      <td>177600000.0</td>
      <td>2018</td>
      <td>tt5028340</td>
      <td>Mary Poppins Returns</td>
      <td>2018</td>
      <td>130.0</td>
      <td>Comedy,Family,Fantasy</td>
      <td>6.9</td>
      <td>52103.0</td>
    </tr>
    <tr>
      <th>2774</th>
      <td>The Nutcracker and the Four Realms</td>
      <td>BV</td>
      <td>54900000.0</td>
      <td>119100000.0</td>
      <td>2018</td>
      <td>tt5523010</td>
      <td>The Nutcracker and the Four Realms</td>
      <td>2018</td>
      <td>99.0</td>
      <td>Adventure,Family,Fantasy</td>
      <td>5.5</td>
      <td>18734.0</td>
    </tr>
    <tr>
      <th>2782</th>
      <td>A Wrinkle in Time</td>
      <td>BV</td>
      <td>100500000.0</td>
      <td>32200000.0</td>
      <td>2018</td>
      <td>tt1620680</td>
      <td>A Wrinkle in Time</td>
      <td>2018</td>
      <td>109.0</td>
      <td>Adventure,Family,Fantasy</td>
      <td>4.2</td>
      <td>34888.0</td>
    </tr>
  </tbody>
</table>
<p>90 rows × 12 columns</p>
</div>




```python
BVonly['Genres'].value_counts()
BVonly = BVonly.drop_duplicates(subset = 'Title')
        

with plt.style.context('bmh'):
    (BVonly.groupby(['Title'])['Domestic Gross'].sum().sort_values(ascending = False).head(15)/100000000).plot(kind = 'barh')
    plt.title('Highest Grossing BV Movies', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in Hundreds of Millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('Title', fontsize = 14, fontweight = 'bold')
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)
    

```


    
![png](output_55_0.png)
    



```python
UNIonly = UNIonly.drop_duplicates(subset = 'Title')

with plt.style.context('bmh'):
    (UNIonly.groupby(['Title'])['Domestic Gross'].sum().sort_values(ascending = False).head(15)/100000000).plot(kind = 'barh')
    plt.title('Highest Grossing Universal Movies', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in 100s of millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('Title', fontsize = 14, fontweight = 'bold')
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)
    

```


    
![png](output_56_0.png)
    



```python
FOXonly = FOXonly.drop_duplicates(subset = 'Title')

with plt.style.context('bmh'):
    (FOXonly.groupby(['Title'])['Domestic Gross'].sum().sort_values(ascending = False).head(15)/100000000).plot(kind = 'barh')
    plt.title('Highest Grossing Fox Movies', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in 100s of millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('Title', fontsize = 14, fontweight = 'bold')
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)

```


    
![png](output_57_0.png)
    


## Evaluation
Evaluate how well your work solves the stated business problem.

***
Questions to consider:
* How do you interpret the results?
* How well does your model fit your data? How much better is this than your baseline model?
* How confident are you that your results would generalize beyond the data you have?
* How confident are you that this model would benefit the business if put into use?
***

## Conclusions
Provide your conclusions about the work you've done, including any limitations or next steps.

***
Questions to consider:
* What would you recommend the business do as a result of this work?
* What are some reasons why your analysis might not fully solve the business problem?
* What else could you do in the future to improve this project?
***
