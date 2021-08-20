# Flatiron Data Science Phase 1 Project

## **Author:** Madeleine Reiser
***

## Overview

This project used exploratory data analysis to help the business stake owners of a potential new movie streaming service figure out the best ways to make box office revenue. Datasets from IMDB and Box Office Mojo were analyzed to show what movies have made the most money, which are the highest rated, and which are the most popular, from the years 2010-2018. 

## Business Problem

Microsoft Stake Owners are interested in developing their own streaming service but aren’t sure what kinds of movies do the best in the box office. 


## Data Understanding

Using datasets from IMDB and Box Office Mojo, this project explores how box office revenue is correlated with variables such as ratings, popularity (which is measured by number of reviews left by viewers), specific studios and genres.  


## IMDB Ratings Dataset

Contains "tconst", which appears to be the code for each movie, the film's average rating, and the number of votes on IMDB

## IMDB Basic Dataset

Contains more information for each film on IMDB including "tconst" again, Primary and Original titles, start year, runtime in minutes, and the genre. 

## Box Office Mojo dataset

Contains the title, studio, domestic gross, foreign gross and year released according to data from Box Office Mojo 

## Data Modeling

I first created a correlation matrix to see which variables have a relationship with "Domestic Gross" or "Foreign Gross" 

### Notable Correlations:

Domestic Gross & Foreign Gross have a strong positive correlation of .83. This indicates that films that do well in the Domestic Box Office will do well in the Foreign Box Office.

Number of Votes & Domestic Gross have a moderate-strong positive correlation of .66. This means that more votes on IMDB tend to lead to a higher Domestic revenue.

The correlation between Number of Votes & Foreign Gross is a little weaker, at .56, but still notable.

Finally, Average Rating and Number of Votes have a mild positive correlation of .28, meaning that films with a higher rating can bring in more votes, or vice versa.


```python
ax = plt.axes()
sns.set(font_scale = 1)
sns.heatmap(alljoined.corr(), ax = ax, annot = True, vmin=-1, vmax=1, center= 0, cmap= 'coolwarm')

ax.set_title('Correlation Matrix of Relevant Variables', fontsize = 18)
ax.set_xticklabels(ax.get_xticklabels(), fontsize = 13)
ax.set_yticklabels(ax.get_yticklabels(), fontsize = 13)

```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-2-00ba59d3ca6c> in <module>
    ----> 1 ax = plt.axes()
          2 sns.set(font_scale = 1)
          3 sns.heatmap(alljoined.corr(), ax = ax, annot = True, vmin=-1, vmax=1, center= 0, cmap= 'coolwarm')
          4 
          5 ax.set_title('Correlation Matrix of Relevant Variables', fontsize = 18)
    

    NameError: name 'plt' is not defined


This graph represents the mean Domestic Gross of the highest-grossing genres from 2010-2018. Adventure/Drama/Sport films blew the rest out of the water and made the most money at $400,700,000.


```python
with plt.style.context('bmh'):
    (alljoined.groupby(['Genres'])['Domestic Gross'].mean().sort_values(ascending = False).head(15)/100000000).plot(kind = 'barh')
    plt.title('Mean Domestic Box Office Gross by Genre', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in Hundreds of Millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('Genre', fontsize = 14, fontweight = 'bold') 
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)
    
# Adventure/Drama/Sport movies make an average of about $400 million in
# the domestic box office 


```


    
![png](output_11_0.png)
    


"Mean Domestic Gross by Genre for films with an 8+ Rating" displays that the average Domestic Gross for Action/Thriller movies with a high rating was $448,100,000.


```python
with plt.style.context('bmh'):
    (alljoined_8_or_better.groupby(['Genres'])['Domestic Gross'].mean().sort_values(ascending = False).head(15)/100000000).plot(kind = 'barh')
    plt.title('Mean Domestic Gross by Genre for films with an 8+ Rating', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in 100s of Millions', fontsize = 16, fontweight = 'bold')
    plt.ylabel('Genre', fontsize = 16, fontweight = 'bold')
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)

# Action/Thriller movies with an 8+ rating make an average of $450
# million
#means you can make a high-rated film and make the same 
# amount as Action/Drama/Sport


```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-1-7721799c7b5a> in <module>
    ----> 1 with plt.style.context('bmh'):
          2     (alljoined_8_or_better.groupby(['Genres'])['Domestic Gross'].mean().sort_values(ascending = False).head(15)/100000000).plot(kind = 'barh')
          3     plt.title('Mean Domestic Gross by Genre for films with an 8+ Rating', fontsize = 16, fontweight = 'bold')
          4     plt.gca().invert_yaxis()
          5     plt.xlabel('Domestic Gross in 100s of Millions', fontsize = 16, fontweight = 'bold')
    

    NameError: name 'plt' is not defined


"Mean Domestic Gross by Year" depicted the average Domestic Gross a film made for each year from 2010-2018. The year 2018 had the highest average Domestic Gross of $46,349,360. This number seemed low compared to the other graphs, but that's because the average was affected by low-grossing films.


```python
with plt.style.context('bmh'):
    (alljoined.groupby(['IMDB Year'])['Domestic Gross'].mean().sort_values(ascending = False).head(15)/10000000).plot(kind = 'barh')
    plt.title('Mean Domestic Gross by Year', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in 10s of Millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('IMDB Year', fontsize = 14, fontweight = 'bold')
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)
    
# the average DBO revenue for a movie in 2018 was ~$48 million,
# this number is lower than the mean per genre because it includes
# the lowest-grossing films as well


```


    
![png](output_15_0.png)
    



```python
just2018 = alljoined[alljoined['IMDB Year'] == 2018]
```

"Mean Domestic Gross by Genre 2018" shows the average Domestic Gross a film from a certain genre(s) made in the year 2018. Again, Action/Adventure/Sci-fi makes the highest grossing films on average with a Domestic Gross of $412,300,000.


```python
with plt.style.context('bmh'):
    (just2018.groupby(['Genres'])['Domestic Gross'].mean().sort_values(ascending = False).head(15)/100000000).plot(kind = 'barh')
    plt.title('Mean Domestic Gross by Genre 2018', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in 100s of Millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('Genre', fontsize = 14, fontweight = 'bold')
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)
#Action/Adventure/Sci-fi yet again

```


    
![png](output_18_0.png)
    


I was interested in exploring which genres had the most movies with a "high rating", meaning an 8 or better on IMDB. The bar graph "Number of Movies per Genre with a rating of 8+ on IMDB" shows that Documentaries have the most movies with a rating of 8+ at 2715 films.


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

```


    
![png](output_20_0.png)
    


Because of the strong positive correlation between Number of Votes and Domestic Box Office Gross, I decided to explore which genres garnered the most votes. Again, Action/Adventure/Sci-fi had a giant lead with a total of 19,721,992 votes. Adventure/Animation/Comedy had 8,037,681 votes, and Action/Adventure/Comedy had 7,763,568.


```python
with plt.style.context('bmh'):
    (alljoined.groupby(['Genres'])['Number of Votes'].sum().sort_values(ascending = False).head(10)/1000000).plot(kind = 'barh')
    plt.title('Total Number of Votes per Genre on IMDB', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Votes in Millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('Genre', fontsize = 14, fontweight = 'bold')
    plt.xticks(fontsize = 12)
    plt.yticks(fontsize = 12)
#Action/Adventure/Sci-fi
```


    
![png](output_22_0.png)
    


I was interested in which production studios had the highest Domestic Box Office revenue, and which of their movies did best. I took the mean Domestic Gross for each studio rather than the sum, because an individual studio might not make many movies, or have a couple that did well. This gives a better idea of how much an individual film might make. 

"Mean Domestic Gross by Studio" determined that BV, P/DW and WB made the most money per film on average in the Domestic Box Office.

BV: $ 175,240,200

P/DW: $ 168,290,000

WB: $ 89,799,160


```python
with plt.style.context('bmh'):
    (alljoined.groupby(['Studio'])['Domestic Gross'].mean().sort_values(ascending = False).head(10)/100000000).plot(kind = 'barh')
    plt.title('Mean Domestic Gross by Studio', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in Hundreds of Millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('Studio', fontsize = 14, fontweight = 'bold')
    plt.xticks(fontsize = 12)
    plt.yticks(fontsize = 12)
```


    
![png](output_24_0.png)
    


The top 3 studios that made the most money in the Domestic Box Office are BV, Paramount/Dreamworks, and WB. I created 3 separate dataframes to analyze each studio individually. I discovered that there were some duplicate titles for films, which led to outliers on the graphs, so I dropped them.  


```python
BVonly = alljoined[alljoined['Studio']== 'BV']
PDWonly = alljoined[alljoined['Studio']== 'P/DW']
WBonly = alljoined[alljoined['Studio']== 'WB']
```


```python
PDWonly.dropna()
WBonly.dropna()
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
    </tr>
    <tr>
      <th>2753</th>
      <td>Ralph Breaks the Internet</td>
      <td>BV</td>
      <td>201100000.0</td>
      <td>328100000.0</td>
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
<p>90 rows × 11 columns</p>
</div>



The 5 Highest Grossing BV movies are:

    Black Panther:                  $700,100,000
Avengers: Infinity War:         $678,800,000 
    Star Wars: The Last Jedi:       $620,200,000 
Incredibles 2:                  $608,600,000 
    Rogue One: A Star Wars Story    $532,200,000


```python
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


    
![png](output_29_0.png)
    


The 5 Highest Grossing P/DW movies are:

    Transformers: Dark of the Moon:        $352,400,000
Shrek Forever After:                   $238,700,000 
    How to Train Your Dragon:              $217,600,000 
Madagascar 3: Europe's Most Wanted:    $216,400,000 
    Kung Fu Panda 2:                       $165,200,000


```python
PDWonly = PDWonly.drop_duplicates(subset = 'Title')

with plt.style.context('bmh'):
    (PDWonly.groupby(['Title'])['Domestic Gross'].sum().sort_values(ascending = False).head(15)/100000000).plot(kind = 'barh')
    plt.title('Highest Grossing Paramount/Dreamworks Movies', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in 100s of millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('Title', fontsize = 14, fontweight = 'bold')
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)
    

```


    
![png](output_31_0.png)
    


The 5 Highest Grossing WB movies are:

    The Dark Knight Rises:              $448,100,000
Wonder Woman:                       $412,600,000 
    American Sniper:                    $350,100,000 
Aquaman:                            $335,100,000 
    Batman v Superman: Dawn of Justice: $330,400,000


```python
WBonly = WBonly.drop_duplicates(subset = 'Title')

with plt.style.context('bmh'):
    (WBonly.groupby(['Title'])['Domestic Gross'].sum().sort_values(ascending = False).head(15)/100000000).plot(kind = 'barh')
    plt.title('Highest Grossing WB Movies', fontsize = 16, fontweight = 'bold')
    plt.gca().invert_yaxis()
    plt.xlabel('Domestic Gross in 100s of millions', fontsize = 14, fontweight = 'bold')
    plt.ylabel('Title', fontsize = 14, fontweight = 'bold')
    plt.xticks(fontsize = 13)
    plt.yticks(fontsize = 13)


```


    
![png](output_33_0.png)
    


## Conclusions
Provide your conclusions about the work you've done, including any limitations or next steps.

***
Questions to consider:
* What would you recommend the business do as a result of this work?
* What are some reasons why your analysis might not fully solve the business problem?
* What else could you do in the future to improve this project?
***
