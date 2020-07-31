
# Final Project, Module 01, Ryan McArthur

 In this exploration, Microsoft has requested information regarding the success of films, hoping to break out in the movie industry in the coming years. The first question I wanted to answer was what genre Microsoft should consider for their first film. Next, I investigated the distribution of profits by genre in order to show the risk versus reward that each genre offers in order to give Microsoft a full picture of the genre landscape of film.
 Next, I investigated the auteur theory of film which suggests that a director is the greatest contributing factor to the success of a film. This investigation would allow Microsoft evidence to decide between a director with a large filmography (whom would have been extremely expensive to hire) or a lesser known director (at a much lower price). 
 Finally, I could not in good concience disregard the impact that COVID-19 is having and will have on the film industry. I originally wanted to answer the question of what market would be best to release in, originally thinking of eastern vs western markets. When considering COVID-19, however, I realized that a comparison between streaming vs theater releases would be a more appropriate and insightful market analysis. A major disclaimer in this section is the lack of data to glean the 'success' of films released on streaming platforms, so I needed to use an aggregate of the streaming and theater markets, in this case Netflix and AMC stock, respectively. 
 

# 1. What genre gives the greatest profits on average?
The primary goal of any company is to turn a profit, so beginning at this metric is appropriate. 


```python
#import necessary libraries

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns
```

First, we need to load all of our data into pandas dataframes


```python
imdb_name_basics = pd.read_csv('zippedData/imdb.name.basics.csv.gz')
movie_gross = pd.read_csv('zippedData/bom.movie_gross.csv.gz')
imdb_title_akas = pd.read_csv('zippedData/imdb.title.akas.csv.gz')
imdb_title_basics = pd.read_csv('zippedData/imdb.title.basics.csv.gz')
imdb_title_crew = pd.read_csv('zippedData/imdb.title.crew.csv.gz')
imdb_title_principals = pd.read_csv('zippedData/imdb.title.principals.csv.gz')
imdb_title_ratings = pd.read_csv('zippedData/imdb.title.ratings.csv.gz')
rt_info = pd.read_csv('zippedData/rt.movie_info.tsv.gz', delimiter = '\t')
rt_reviews = pd.read_csv('zippedData/rt.reviews.tsv.gz', delimiter = '\t', encoding = "latin-1")
tmdb_movies = pd.read_csv('zippedData/tmdb.movies.csv.gz')
budgets = pd.read_csv('zippedData/tn.movie_budgets.csv.gz')
amc = pd.read_csv('stocks/AMC.csv')
netflix = pd.read_csv('stocks/NFLX.csv')
```

Next, we will go through the process of cleaning our data.


```python
# slice necessary information from table using concat
title_genre = [imdb_title_basics['primary_title'], imdb_title_basics['genres']]
df1 = pd.concat(title_genre, axis = 1)

#drop nan row
df1 = df1.dropna()

#convert genre values from strings to lists
df1['genres'] = list(map(lambda x: x.split(','), df1['genres']))

#expand the rows out to seperate by genre using explode funciton
df1 = df1.explode('genres').reset_index(drop = True)
df1
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
      <th>primary_title</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Sunghursh</td>
      <td>Action</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Sunghursh</td>
      <td>Crime</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Sunghursh</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>3</th>
      <td>One Day Before the Rainy Season</td>
      <td>Biography</td>
    </tr>
    <tr>
      <th>4</th>
      <td>One Day Before the Rainy Season</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>229545</th>
      <td>The Secret of China</td>
      <td>War</td>
    </tr>
    <tr>
      <th>229546</th>
      <td>Kuambil Lagi Hatiku</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>229547</th>
      <td>Rodolpho Teóphilo - O Legado de um Pioneiro</td>
      <td>Documentary</td>
    </tr>
    <tr>
      <th>229548</th>
      <td>Dankyavar Danka</td>
      <td>Comedy</td>
    </tr>
    <tr>
      <th>229549</th>
      <td>Chico Albuquerque - Revelações</td>
      <td>Documentary</td>
    </tr>
  </tbody>
</table>
<p>229550 rows × 2 columns</p>
</div>



Now that we have out movie genres expanded, lets bring in the budget data from the budgets dataframe


```python
#slice necessary information from table using concat

title_money = [budgets['movie'], budgets['production_budget'], budgets['worldwide_gross']]
df2 = pd.concat(title_money, axis = 1)

#Convert currency strings to floats

df2[df2.columns[1:]] = df2[df2.columns[1:]].replace('[\$,]', '', regex=True).astype(float)

#calculate profits

df2['profit'] = df2['worldwide_gross'] - df2['production_budget']

#drop budget and gross 

df2 = df2.drop(['production_budget', 'worldwide_gross'], axis = 1)
df2
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
      <th>movie</th>
      <th>profit</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Avatar</td>
      <td>2.351345e+09</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>6.350639e+08</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Dark Phoenix</td>
      <td>-2.002376e+08</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Avengers: Age of Ultron</td>
      <td>1.072414e+09</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Star Wars Ep. VIII: The Last Jedi</td>
      <td>9.997217e+08</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>5777</th>
      <td>Red 11</td>
      <td>-7.000000e+03</td>
    </tr>
    <tr>
      <th>5778</th>
      <td>Following</td>
      <td>2.344950e+05</td>
    </tr>
    <tr>
      <th>5779</th>
      <td>Return to the Land of Wonders</td>
      <td>-3.662000e+03</td>
    </tr>
    <tr>
      <th>5780</th>
      <td>A Plague So Pleasant</td>
      <td>-1.400000e+03</td>
    </tr>
    <tr>
      <th>5781</th>
      <td>My Date With Drew</td>
      <td>1.799410e+05</td>
    </tr>
  </tbody>
</table>
<p>5782 rows × 2 columns</p>
</div>



Next up is to merge these two processed dataframes, and to group profit by genre! We had to keep the titles in both dataframes, as this acts as the primary key. 


```python
#rename columns to be primary key

df1 = df1.rename(columns = {'primary_title':'movie'})
df1
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
      <th>movie</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Sunghursh</td>
      <td>Action</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Sunghursh</td>
      <td>Crime</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Sunghursh</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>3</th>
      <td>One Day Before the Rainy Season</td>
      <td>Biography</td>
    </tr>
    <tr>
      <th>4</th>
      <td>One Day Before the Rainy Season</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>229545</th>
      <td>The Secret of China</td>
      <td>War</td>
    </tr>
    <tr>
      <th>229546</th>
      <td>Kuambil Lagi Hatiku</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>229547</th>
      <td>Rodolpho Teóphilo - O Legado de um Pioneiro</td>
      <td>Documentary</td>
    </tr>
    <tr>
      <th>229548</th>
      <td>Dankyavar Danka</td>
      <td>Comedy</td>
    </tr>
    <tr>
      <th>229549</th>
      <td>Chico Albuquerque - Revelações</td>
      <td>Documentary</td>
    </tr>
  </tbody>
</table>
<p>229550 rows × 2 columns</p>
</div>




```python
#merge the two dataframes

df_merge = pd.merge(df1, df2, on='movie', how='inner')
df_merge
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
      <th>movie</th>
      <th>genres</th>
      <th>profit</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Foodfight!</td>
      <td>Action</td>
      <td>-44926294.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Foodfight!</td>
      <td>Animation</td>
      <td>-44926294.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Foodfight!</td>
      <td>Comedy</td>
      <td>-44926294.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Mortal Kombat</td>
      <td>Action</td>
      <td>102133227.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Mortal Kombat</td>
      <td>Adventure</td>
      <td>102133227.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7791</th>
      <td>Traitor</td>
      <td>Action</td>
      <td>5882226.0</td>
    </tr>
    <tr>
      <th>7792</th>
      <td>Traitor</td>
      <td>Drama</td>
      <td>5882226.0</td>
    </tr>
    <tr>
      <th>7793</th>
      <td>Traitor</td>
      <td>Romance</td>
      <td>5882226.0</td>
    </tr>
    <tr>
      <th>7794</th>
      <td>Ray</td>
      <td>Crime</td>
      <td>84823094.0</td>
    </tr>
    <tr>
      <th>7795</th>
      <td>Sublime</td>
      <td>Documentary</td>
      <td>-1800000.0</td>
    </tr>
  </tbody>
</table>
<p>7796 rows × 3 columns</p>
</div>




```python
#group profits by genre and sort

profit_by_genre = df_merge.groupby(['genres']).mean()
profit_by_genre = profit_by_genre.sort_values(by=['profit'], ascending = True)
profit_by_genre['profit'] = profit_by_genre['profit'] / 1000000
profit_by_genre = profit_by_genre.reset_index()
profit_by_genre
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
      <th>genres</th>
      <th>profit</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Reality-TV</td>
      <td>-1.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>News</td>
      <td>22.086768</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Western</td>
      <td>22.406852</td>
    </tr>
    <tr>
      <th>3</th>
      <td>War</td>
      <td>29.425873</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Drama</td>
      <td>35.694416</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Crime</td>
      <td>36.806190</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Documentary</td>
      <td>38.101898</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Romance</td>
      <td>39.164238</td>
    </tr>
    <tr>
      <th>8</th>
      <td>History</td>
      <td>40.222242</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Biography</td>
      <td>43.280094</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Horror</td>
      <td>44.112362</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Sport</td>
      <td>44.884715</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Mystery</td>
      <td>45.849107</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Thriller</td>
      <td>49.338631</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Music</td>
      <td>49.540757</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Comedy</td>
      <td>69.276468</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Family</td>
      <td>108.895300</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Action</td>
      <td>114.082004</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Fantasy</td>
      <td>149.960913</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Sci-Fi</td>
      <td>158.386000</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Musical</td>
      <td>182.732306</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Adventure</td>
      <td>195.089851</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Animation</td>
      <td>219.399378</td>
    </tr>
  </tbody>
</table>
</div>



And finally lets make a snappy visualization for this dataset.


```python
#graph profit_by_genre

ax = profit_by_genre.plot.barh(title = 'Profit by Film Genre')
ax.set_xlabel('Profits ($) in Millions')
ax.set_ylabel('Genre')
x_tick_labels = (profit_by_genre['profit'])/1000000
x_tick_labels.values
ax.set_xticks(range(0, 250, 50))


```




    [<matplotlib.axis.XTick at 0x120d75b38>,
     <matplotlib.axis.XTick at 0x120d75400>,
     <matplotlib.axis.XTick at 0x11c1ad748>,
     <matplotlib.axis.XTick at 0x1253e6e48>,
     <matplotlib.axis.XTick at 0x1253e69b0>]




![png](output_15_1.png)


This visualization shows that Animation, Adventure, Musical, Sci-Fi, and Fantasy bring in the greatest profits. As we move further on, many of the questions that we want to answer are based upon what genre a studio would like to pick, so we will continue breaking the films down by genre for the next investigation:

# 2. How are these profits distributed within genres?

The next question that we want to answer for Microsoft is the risk vs reward for the top performing genres in the above analysis. We will show the distribution of profits for each genre, showing how reliable a film genre is to earn its budget back. 


```python
#we will use the dataframe built from before, which already has genre rows expanded using the explode functio
df_merge
#using profit_by_genre, slicing df_merge to include only top_performing genres
top_genres = profit_by_genre['genres'][17:]
df_top_genres = df_merge.where(df_merge['genres'].isin(top_genres))
df_top_genres = df_top_genres.dropna()
#double checking that only top-performing genres are included
df_top_genres['genres'].unique()
```




    array(['Action', 'Animation', 'Adventure', 'Fantasy', 'Sci-Fi', 'Musical'],
          dtype=object)




```python
ax = sns.swarmplot(y = 'genres', x = 'profit', data = df_top_genres)
ax.set_xticklabels([])
ax.set_xticks([])
ax.set_title('Ditribution of Profits of Top-Performing Genres')
ax.set_xlabel('Profit')
ax.set_ylabel('Genre')
```




    Text(0, 0.5, 'Genre')




![png](output_18_1.png)


# 3. Should a studio appropriate a large part of their budget for a famous director?
The crew of a film often takes the greatest part of any budget. As these stars become more and more in demand, their paychecks swell to enourmous numbers, but is the correlation between their pricetag and the success of a film so related? Let's explore this by comparing the average ratings of a film to the number of films that a director is known for. If the director is a large part of what makes a movie successful (known as the $auteur$ theory), then we should see the movie ratings of directors known for more films be greater than the movie profits of lesser known directors. If this correlation does not exist, then we can conclude that a movie studio should not spend a large part of its budget on director costs, as the director does not contribute greatly to the movie's review. 


```python
#slice imdb_name_basics to include only directors
imdb_directors = imdb_name_basics.where(imdb_name_basics['primary_profession'].str.contains('director'))
imdb_directors = imdb_directors.dropna()
#get count of number of films known for
imdb_directors['known_for_titles'] = list(map(lambda x: x.split(','), imdb_directors['known_for_titles']))
num_known_for = []
for titles in imdb_directors['known_for_titles']:
    num_known_for.append(len(titles))
imdb_directors['num_known_for'] = num_known_for
#expand rows by known_for_titles using explode
imdb_directors_expanded = imdb_directors.explode('known_for_titles').reset_index(drop = True)
imdb_directors_expanded
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
      <th>nconst</th>
      <th>primary_name</th>
      <th>birth_year</th>
      <th>death_year</th>
      <th>primary_profession</th>
      <th>known_for_titles</th>
      <th>num_known_for</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>nm0083767</td>
      <td>Fernando Birri</td>
      <td>1925.0</td>
      <td>2017.0</td>
      <td>director,actor,writer</td>
      <td>tt0286104</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>nm0083767</td>
      <td>Fernando Birri</td>
      <td>1925.0</td>
      <td>2017.0</td>
      <td>director,actor,writer</td>
      <td>tt0056102</td>
      <td>4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>nm0083767</td>
      <td>Fernando Birri</td>
      <td>1925.0</td>
      <td>2017.0</td>
      <td>director,actor,writer</td>
      <td>tt0078039</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>nm0083767</td>
      <td>Fernando Birri</td>
      <td>1925.0</td>
      <td>2017.0</td>
      <td>director,actor,writer</td>
      <td>tt0096077</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>nm0101055</td>
      <td>George Bowers</td>
      <td>1944.0</td>
      <td>2012.0</td>
      <td>editor,director,actor</td>
      <td>tt0120681</td>
      <td>4</td>
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
    </tr>
    <tr>
      <th>5346</th>
      <td>nm8289928</td>
      <td>Arthur MacCaig</td>
      <td>1948.0</td>
      <td>2008.0</td>
      <td>director,cinematographer,editor</td>
      <td>tt8421302</td>
      <td>4</td>
    </tr>
    <tr>
      <th>5347</th>
      <td>nm8289928</td>
      <td>Arthur MacCaig</td>
      <td>1948.0</td>
      <td>2008.0</td>
      <td>director,cinematographer,editor</td>
      <td>tt7691988</td>
      <td>4</td>
    </tr>
    <tr>
      <th>5348</th>
      <td>nm8289928</td>
      <td>Arthur MacCaig</td>
      <td>1948.0</td>
      <td>2008.0</td>
      <td>director,cinematographer,editor</td>
      <td>tt5885972</td>
      <td>4</td>
    </tr>
    <tr>
      <th>5349</th>
      <td>nm8289928</td>
      <td>Arthur MacCaig</td>
      <td>1948.0</td>
      <td>2008.0</td>
      <td>director,cinematographer,editor</td>
      <td>tt10166724</td>
      <td>4</td>
    </tr>
    <tr>
      <th>5350</th>
      <td>nm9211845</td>
      <td>Jan C. Gabriel</td>
      <td>1940.0</td>
      <td>2010.0</td>
      <td>director,writer,editor</td>
      <td>tt7269630</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>5351 rows × 7 columns</p>
</div>




```python
#rename imdb_title_ratings[tconst] in order to merge with imdb_directors properly
imdb_title_ratings = imdb_title_ratings.rename(columns = {'tconst' : 'known_for_titles'})
imdb_title_ratings
dir_rating_merge = pd.merge(imdb_directors_expanded, imdb_title_ratings, on = 'known_for_titles', how = 'inner')
#no longer need title codes
dir_rating_merge = pd.concat([dir_rating_merge['num_known_for'], dir_rating_merge['averagerating']], axis = 1)
dir_rating_merge
#scatter plot and line of best fit to show trend
x = dir_rating_merge['num_known_for']
y = dir_rating_merge['averagerating']
m, b = np.polyfit(x, y, 1)
ax = plt.subplot()
plt.plot(x, y, 'o')
plt.plot(x, m*x+b)
plt.xlabel('Number of Prior Films')
plt.ylabel('Average Review')
plt.title("Directors' Prior Films vs Film Reveiw")
ax.set_xticks(range(1, 5, 1))
ax.set_yticks(range(0, 11, 1))
```




    [<matplotlib.axis.YTick at 0x116f71c18>,
     <matplotlib.axis.YTick at 0x116f71550>,
     <matplotlib.axis.YTick at 0x117851588>,
     <matplotlib.axis.YTick at 0x115d80a58>,
     <matplotlib.axis.YTick at 0x1177c3240>,
     <matplotlib.axis.YTick at 0x1177c3940>,
     <matplotlib.axis.YTick at 0x1177c3eb8>,
     <matplotlib.axis.YTick at 0x11db8e470>,
     <matplotlib.axis.YTick at 0x11db8e9e8>,
     <matplotlib.axis.YTick at 0x11db8ef60>,
     <matplotlib.axis.YTick at 0x11dba2518>]




![png](output_21_1.png)


From the visualization above, the number of films that a director is known for is only slightly correlated to the average review of the film. From this, we can say that the director is not as big as a part of the success of a film as some may say, and when deciding who to hire for this venture, a big name with a big price tag does not necessarily guarantee a successful film. I suggest that a director's past record of films be not considered when deciding who to hire. 

# 4. How has COVID-19 affected the theater market vs streaming markets?
We wouldn't be doing our due diligence if we did not consider what effect the COVID-19 pandemic will have on the movie industry. Because movies are a luxury good, they are often hit the hardest by economic factors. 
What is different about COVID-19 is the regulation on social distancing. As long as social distancing orders stand, patrons will likely avoid movie theatres altogether for the foreseeable future. Services that pick up this slack are streaming companies such as Netflix, HBO, and Hulu. Let's investigate the difference in success between films that release in theatres versus films that stream to patrons' homes. 
Because these streaming services do not readily provide their revenue per film, viewership, or any metric to accurately guage how successful a film is, we need to measure an aggregate of the success of these companies as a whole. Because we are comparing theatre releases to streaming releases, we will use AMC stock prices (one of the largest movie theatre chains in the US), and Netflix stock prices, the leader in streaming markets. 


```python
amc
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
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2019-07-29</td>
      <td>11.30</td>
      <td>11.4500</td>
      <td>10.93</td>
      <td>11.4500</td>
      <td>10.925329</td>
      <td>2746700</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2019-07-30</td>
      <td>11.34</td>
      <td>11.7400</td>
      <td>11.13</td>
      <td>11.7400</td>
      <td>11.202041</td>
      <td>2778100</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2019-07-31</td>
      <td>11.75</td>
      <td>11.9800</td>
      <td>11.55</td>
      <td>11.8300</td>
      <td>11.287916</td>
      <td>2818700</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2019-08-01</td>
      <td>11.70</td>
      <td>11.8200</td>
      <td>11.04</td>
      <td>11.2600</td>
      <td>10.744036</td>
      <td>4601900</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2019-08-02</td>
      <td>11.20</td>
      <td>11.6400</td>
      <td>11.08</td>
      <td>11.6200</td>
      <td>11.087539</td>
      <td>2870100</td>
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
    </tr>
    <tr>
      <th>249</th>
      <td>2020-07-23</td>
      <td>4.08</td>
      <td>4.1800</td>
      <td>4.00</td>
      <td>4.0600</td>
      <td>4.060000</td>
      <td>3614300</td>
    </tr>
    <tr>
      <th>250</th>
      <td>2020-07-24</td>
      <td>4.00</td>
      <td>4.1800</td>
      <td>3.96</td>
      <td>4.0000</td>
      <td>4.000000</td>
      <td>3282400</td>
    </tr>
    <tr>
      <th>251</th>
      <td>2020-07-27</td>
      <td>4.01</td>
      <td>4.0300</td>
      <td>3.81</td>
      <td>3.8700</td>
      <td>3.870000</td>
      <td>2980500</td>
    </tr>
    <tr>
      <th>252</th>
      <td>2020-07-28</td>
      <td>3.85</td>
      <td>4.2500</td>
      <td>3.84</td>
      <td>4.1500</td>
      <td>4.150000</td>
      <td>6003900</td>
    </tr>
    <tr>
      <th>253</th>
      <td>2020-07-29</td>
      <td>4.07</td>
      <td>4.1506</td>
      <td>3.95</td>
      <td>4.0385</td>
      <td>4.038500</td>
      <td>3538479</td>
    </tr>
  </tbody>
</table>
<p>254 rows × 7 columns</p>
</div>




```python
netflix
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
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2019-07-29</td>
      <td>335.980011</td>
      <td>336.399994</td>
      <td>328.769989</td>
      <td>332.700012</td>
      <td>332.700012</td>
      <td>5782800</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2019-07-30</td>
      <td>329.200012</td>
      <td>329.649994</td>
      <td>323.230011</td>
      <td>325.929993</td>
      <td>325.929993</td>
      <td>6029300</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2019-07-31</td>
      <td>325.160004</td>
      <td>331.769989</td>
      <td>318.529999</td>
      <td>322.989990</td>
      <td>322.989990</td>
      <td>6259500</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2019-08-01</td>
      <td>324.250000</td>
      <td>328.579987</td>
      <td>318.739990</td>
      <td>319.500000</td>
      <td>319.500000</td>
      <td>6563200</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2019-08-02</td>
      <td>317.489990</td>
      <td>319.410004</td>
      <td>311.799988</td>
      <td>318.829987</td>
      <td>318.829987</td>
      <td>6280300</td>
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
    </tr>
    <tr>
      <th>249</th>
      <td>2020-07-23</td>
      <td>491.130005</td>
      <td>491.899994</td>
      <td>472.019989</td>
      <td>477.579987</td>
      <td>477.579987</td>
      <td>7722000</td>
    </tr>
    <tr>
      <th>250</th>
      <td>2020-07-24</td>
      <td>468.769989</td>
      <td>487.170013</td>
      <td>467.540009</td>
      <td>480.450012</td>
      <td>480.450012</td>
      <td>7746200</td>
    </tr>
    <tr>
      <th>251</th>
      <td>2020-07-27</td>
      <td>484.510010</td>
      <td>496.920013</td>
      <td>482.309998</td>
      <td>495.649994</td>
      <td>495.649994</td>
      <td>7863100</td>
    </tr>
    <tr>
      <th>252</th>
      <td>2020-07-28</td>
      <td>496.019989</td>
      <td>497.790009</td>
      <td>487.760010</td>
      <td>488.510010</td>
      <td>488.510010</td>
      <td>5978100</td>
    </tr>
    <tr>
      <th>253</th>
      <td>2020-07-29</td>
      <td>492.250000</td>
      <td>494.920013</td>
      <td>484.970001</td>
      <td>486.475006</td>
      <td>486.475006</td>
      <td>4358277</td>
    </tr>
  </tbody>
</table>
<p>254 rows × 7 columns</p>
</div>




```python
#we only need one column to plot against, as these are daily prices. Here we use 'Close'
amc_close = pd.concat([amc['Date'], amc['Close']], axis = 1)
amc_close
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
      <th>Date</th>
      <th>Close</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2019-07-29</td>
      <td>11.4500</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2019-07-30</td>
      <td>11.7400</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2019-07-31</td>
      <td>11.8300</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2019-08-01</td>
      <td>11.2600</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2019-08-02</td>
      <td>11.6200</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>249</th>
      <td>2020-07-23</td>
      <td>4.0600</td>
    </tr>
    <tr>
      <th>250</th>
      <td>2020-07-24</td>
      <td>4.0000</td>
    </tr>
    <tr>
      <th>251</th>
      <td>2020-07-27</td>
      <td>3.8700</td>
    </tr>
    <tr>
      <th>252</th>
      <td>2020-07-28</td>
      <td>4.1500</td>
    </tr>
    <tr>
      <th>253</th>
      <td>2020-07-29</td>
      <td>4.0385</td>
    </tr>
  </tbody>
</table>
<p>254 rows × 2 columns</p>
</div>




```python
#rename Close column to AMC Price in order to distinguish from Netflix later
amc_close = amc_close.rename(columns = {'Close' : 'AMC Price'})
```


```python
netflix
netflix_close = pd.concat([netflix['Date'], netflix['Close']], axis = 1)

```


```python
netflix_close = netflix_close.rename(columns = {'Close' : 'Netflix Price'})
```


```python
ax = netflix_close.plot(x = 'Date')
ax.set_xlabel('Time')
ax.set_xticks([])
ax.set_ylabel('Closing Price')
ax1 = amc_close.plot(x = 'Date')
ax1.set_xlabel('Time')
ax1.set_xticks([])
ax1.set_ylabel('Closing Price')
ax.set_title('Netflix Stock')
ax1.set_title('AMC Stock')

```




    Text(0.5, 1.0, 'AMC Stock')




![png](output_30_1.png)



![png](output_30_2.png)


It is clear to see that netflix and amc stock prices have had a negative relationship in the last year. We wanted to show that while theatre releases are failing in the wake of COVID-19, streaming services remain unaffected and even benefiting from the change in media supply. 
Deciding to enter the film industry at this time is dangerous, especially if the studio is attempting to release their film in theaters. 
Depending on the profit a studio can secure through licensing fees paid by these streaming services, it may be beneficial to partner with one of these streamers rather than attempt a theater release. 

In order to provide a definitive answer to whether or not licensing is better than a theater release, additional data would need to be provided. There was not enough publicly available data on Netflix licensing fees to make a reasonable comparison between the success of those films versus film releases. 

In conclusion, a new movie studio needs to make a multitude of decisions before their first step can even be taken. Questions like what genre they will break out in, what the crew experience should be, and what market the film will be released in. The major takeaways that I would encourage are that large profits by genre on average do not necessarily guarantee success, as shown through our distrubution of profit, a director's past experience does not strongly correlate to the success of future films, and COVID-19 has devastaded theater markets, making streaming markets a possible other route to take when deciding how to release the film.

Thank you for your time, and good luck making that movie!


```python

```
