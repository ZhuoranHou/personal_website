---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "How to create a Spotify dataset SQLite3 schema"
subtitle: ""
summary: "Create a Spotify dataset SQLite3 3NF schema using pandas and sqlite3"
authors: []
tags: [Python,SQLlite3,SQL]
categories: []
date: 2020-09-30T10:02:37-04:00
lastmod: 2020-09-30T10:02:37-04:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

The task is to download the Spotify songs data set. Create a SQLite3 schema to store this data in at least 3rd normal form (3NF), and populate the tables. Use an SQL query to find the names of all playlists that contain instrumentals. Dataset: https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-01-21/readme.md

### 1. Read the database


```python
import pandas as pd
```


```python
df = pd.read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-01-21/spotify_songs.csv')
```


```python
df
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
      <th>track_id</th>
      <th>track_name</th>
      <th>track_artist</th>
      <th>track_popularity</th>
      <th>track_album_id</th>
      <th>track_album_name</th>
      <th>track_album_release_date</th>
      <th>playlist_name</th>
      <th>playlist_id</th>
      <th>playlist_genre</th>
      <th>...</th>
      <th>key</th>
      <th>loudness</th>
      <th>mode</th>
      <th>speechiness</th>
      <th>acousticness</th>
      <th>instrumentalness</th>
      <th>liveness</th>
      <th>valence</th>
      <th>tempo</th>
      <th>duration_ms</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6f807x0ima9a1j3VPbc7VN</td>
      <td>I Don't Care (with Justin Bieber) - Loud Luxur...</td>
      <td>Ed Sheeran</td>
      <td>66</td>
      <td>2oCs0DGTsRO98Gh5ZSl2Cx</td>
      <td>I Don't Care (with Justin Bieber) [Loud Luxury...</td>
      <td>2019-06-14</td>
      <td>Pop Remix</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>pop</td>
      <td>...</td>
      <td>6</td>
      <td>-2.634</td>
      <td>1</td>
      <td>0.0583</td>
      <td>0.102000</td>
      <td>0.000000</td>
      <td>0.0653</td>
      <td>0.5180</td>
      <td>122.036</td>
      <td>194754</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0r7CVbZTWZgbTCYdfa2P31</td>
      <td>Memories - Dillon Francis Remix</td>
      <td>Maroon 5</td>
      <td>67</td>
      <td>63rPSO264uRjW1X5E6cWv6</td>
      <td>Memories (Dillon Francis Remix)</td>
      <td>2019-12-13</td>
      <td>Pop Remix</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>pop</td>
      <td>...</td>
      <td>11</td>
      <td>-4.969</td>
      <td>1</td>
      <td>0.0373</td>
      <td>0.072400</td>
      <td>0.004210</td>
      <td>0.3570</td>
      <td>0.6930</td>
      <td>99.972</td>
      <td>162600</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1z1Hg7Vb0AhHDiEmnDE79l</td>
      <td>All the Time - Don Diablo Remix</td>
      <td>Zara Larsson</td>
      <td>70</td>
      <td>1HoSmj2eLcsrR0vE9gThr4</td>
      <td>All the Time (Don Diablo Remix)</td>
      <td>2019-07-05</td>
      <td>Pop Remix</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>pop</td>
      <td>...</td>
      <td>1</td>
      <td>-3.432</td>
      <td>0</td>
      <td>0.0742</td>
      <td>0.079400</td>
      <td>0.000023</td>
      <td>0.1100</td>
      <td>0.6130</td>
      <td>124.008</td>
      <td>176616</td>
    </tr>
    <tr>
      <th>3</th>
      <td>75FpbthrwQmzHlBJLuGdC7</td>
      <td>Call You Mine - Keanu Silva Remix</td>
      <td>The Chainsmokers</td>
      <td>60</td>
      <td>1nqYsOef1yKKuGOVchbsk6</td>
      <td>Call You Mine - The Remixes</td>
      <td>2019-07-19</td>
      <td>Pop Remix</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>pop</td>
      <td>...</td>
      <td>7</td>
      <td>-3.778</td>
      <td>1</td>
      <td>0.1020</td>
      <td>0.028700</td>
      <td>0.000009</td>
      <td>0.2040</td>
      <td>0.2770</td>
      <td>121.956</td>
      <td>169093</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1e8PAfcKUYoKkxPhrHqw4x</td>
      <td>Someone You Loved - Future Humans Remix</td>
      <td>Lewis Capaldi</td>
      <td>69</td>
      <td>7m7vv9wlQ4i0LFuJiE2zsQ</td>
      <td>Someone You Loved (Future Humans Remix)</td>
      <td>2019-03-05</td>
      <td>Pop Remix</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>pop</td>
      <td>...</td>
      <td>1</td>
      <td>-4.672</td>
      <td>1</td>
      <td>0.0359</td>
      <td>0.080300</td>
      <td>0.000000</td>
      <td>0.0833</td>
      <td>0.7250</td>
      <td>123.976</td>
      <td>189052</td>
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
      <th>32828</th>
      <td>7bxnKAamR3snQ1VGLuVfC1</td>
      <td>City Of Lights - Official Radio Edit</td>
      <td>Lush &amp; Simon</td>
      <td>42</td>
      <td>2azRoBBWEEEYhqV6sb7JrT</td>
      <td>City Of Lights (Vocal Mix)</td>
      <td>2014-04-28</td>
      <td>♥ EDM LOVE 2020</td>
      <td>6jI1gFr6ANFtT8MmTvA2Ux</td>
      <td>edm</td>
      <td>...</td>
      <td>2</td>
      <td>-1.814</td>
      <td>1</td>
      <td>0.0936</td>
      <td>0.076600</td>
      <td>0.000000</td>
      <td>0.0668</td>
      <td>0.2100</td>
      <td>128.170</td>
      <td>204375</td>
    </tr>
    <tr>
      <th>32829</th>
      <td>5Aevni09Em4575077nkWHz</td>
      <td>Closer - Sultan &amp; Ned Shepard Remix</td>
      <td>Tegan and Sara</td>
      <td>20</td>
      <td>6kD6KLxj7s8eCE3ABvAyf5</td>
      <td>Closer Remixed</td>
      <td>2013-03-08</td>
      <td>♥ EDM LOVE 2020</td>
      <td>6jI1gFr6ANFtT8MmTvA2Ux</td>
      <td>edm</td>
      <td>...</td>
      <td>0</td>
      <td>-4.462</td>
      <td>1</td>
      <td>0.0420</td>
      <td>0.001710</td>
      <td>0.004270</td>
      <td>0.3750</td>
      <td>0.4000</td>
      <td>128.041</td>
      <td>353120</td>
    </tr>
    <tr>
      <th>32830</th>
      <td>7ImMqPP3Q1yfUHvsdn7wEo</td>
      <td>Sweet Surrender - Radio Edit</td>
      <td>Starkillers</td>
      <td>14</td>
      <td>0ltWNSY9JgxoIZO4VzuCa6</td>
      <td>Sweet Surrender (Radio Edit)</td>
      <td>2014-04-21</td>
      <td>♥ EDM LOVE 2020</td>
      <td>6jI1gFr6ANFtT8MmTvA2Ux</td>
      <td>edm</td>
      <td>...</td>
      <td>6</td>
      <td>-4.899</td>
      <td>0</td>
      <td>0.0481</td>
      <td>0.108000</td>
      <td>0.000001</td>
      <td>0.1500</td>
      <td>0.4360</td>
      <td>127.989</td>
      <td>210112</td>
    </tr>
    <tr>
      <th>32831</th>
      <td>2m69mhnfQ1Oq6lGtXuYhgX</td>
      <td>Only For You - Maor Levi Remix</td>
      <td>Mat Zo</td>
      <td>15</td>
      <td>1fGrOkHnHJcStl14zNx8Jy</td>
      <td>Only For You (Remixes)</td>
      <td>2014-01-01</td>
      <td>♥ EDM LOVE 2020</td>
      <td>6jI1gFr6ANFtT8MmTvA2Ux</td>
      <td>edm</td>
      <td>...</td>
      <td>2</td>
      <td>-3.361</td>
      <td>1</td>
      <td>0.1090</td>
      <td>0.007920</td>
      <td>0.127000</td>
      <td>0.3430</td>
      <td>0.3080</td>
      <td>128.008</td>
      <td>367432</td>
    </tr>
    <tr>
      <th>32832</th>
      <td>29zWqhca3zt5NsckZqDf6c</td>
      <td>Typhoon - Original Mix</td>
      <td>Julian Calor</td>
      <td>27</td>
      <td>0X3mUOm6MhxR7PzxG95rAo</td>
      <td>Typhoon/Storm</td>
      <td>2014-03-03</td>
      <td>♥ EDM LOVE 2020</td>
      <td>6jI1gFr6ANFtT8MmTvA2Ux</td>
      <td>edm</td>
      <td>...</td>
      <td>5</td>
      <td>-4.571</td>
      <td>0</td>
      <td>0.0385</td>
      <td>0.000133</td>
      <td>0.341000</td>
      <td>0.7420</td>
      <td>0.0894</td>
      <td>127.984</td>
      <td>337500</td>
    </tr>
  </tbody>
</table>
<p>32833 rows × 23 columns</p>

</div>




```python
df.shape
```




    (32833, 23)




```python
df.columns
```




    Index(['track_id', 'track_name', 'track_artist', 'track_popularity',
           'track_album_id', 'track_album_name', 'track_album_release_date',
           'playlist_name', 'playlist_id', 'playlist_genre', 'playlist_subgenre',
           'danceability', 'energy', 'key', 'loudness', 'mode', 'speechiness',
           'acousticness', 'instrumentalness', 'liveness', 'valence', 'tempo',
           'duration_ms'],
          dtype='object')



### 2. First Normal Form (1NF)

- Table has a primary key (unique, non-null column that identifies each row)
- No repeating groups of columns
- Each cell contains a single value

**It seems that the database is already 1NF.**

### 3. Second Normal Form (2NF) & Third Normal Form (3NF)

- Break partial dependencies
- Remove transitive dependencies


```python
df_songs = df.iloc[:, [0,1,2,3]].drop_duplicates()
df_songs
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
      <th>track_id</th>
      <th>track_name</th>
      <th>track_artist</th>
      <th>track_popularity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6f807x0ima9a1j3VPbc7VN</td>
      <td>I Don't Care (with Justin Bieber) - Loud Luxur...</td>
      <td>Ed Sheeran</td>
      <td>66</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0r7CVbZTWZgbTCYdfa2P31</td>
      <td>Memories - Dillon Francis Remix</td>
      <td>Maroon 5</td>
      <td>67</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1z1Hg7Vb0AhHDiEmnDE79l</td>
      <td>All the Time - Don Diablo Remix</td>
      <td>Zara Larsson</td>
      <td>70</td>
    </tr>
    <tr>
      <th>3</th>
      <td>75FpbthrwQmzHlBJLuGdC7</td>
      <td>Call You Mine - Keanu Silva Remix</td>
      <td>The Chainsmokers</td>
      <td>60</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1e8PAfcKUYoKkxPhrHqw4x</td>
      <td>Someone You Loved - Future Humans Remix</td>
      <td>Lewis Capaldi</td>
      <td>69</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>32828</th>
      <td>7bxnKAamR3snQ1VGLuVfC1</td>
      <td>City Of Lights - Official Radio Edit</td>
      <td>Lush &amp; Simon</td>
      <td>42</td>
    </tr>
    <tr>
      <th>32829</th>
      <td>5Aevni09Em4575077nkWHz</td>
      <td>Closer - Sultan &amp; Ned Shepard Remix</td>
      <td>Tegan and Sara</td>
      <td>20</td>
    </tr>
    <tr>
      <th>32830</th>
      <td>7ImMqPP3Q1yfUHvsdn7wEo</td>
      <td>Sweet Surrender - Radio Edit</td>
      <td>Starkillers</td>
      <td>14</td>
    </tr>
    <tr>
      <th>32831</th>
      <td>2m69mhnfQ1Oq6lGtXuYhgX</td>
      <td>Only For You - Maor Levi Remix</td>
      <td>Mat Zo</td>
      <td>15</td>
    </tr>
    <tr>
      <th>32832</th>
      <td>29zWqhca3zt5NsckZqDf6c</td>
      <td>Typhoon - Original Mix</td>
      <td>Julian Calor</td>
      <td>27</td>
    </tr>
  </tbody>
</table>
<p>28356 rows × 4 columns</p>

</div>




```python
df_albums = df.iloc[:, [4,5,6]].drop_duplicates()
df_albums
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
      <th>track_album_id</th>
      <th>track_album_name</th>
      <th>track_album_release_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2oCs0DGTsRO98Gh5ZSl2Cx</td>
      <td>I Don't Care (with Justin Bieber) [Loud Luxury...</td>
      <td>2019-06-14</td>
    </tr>
    <tr>
      <th>1</th>
      <td>63rPSO264uRjW1X5E6cWv6</td>
      <td>Memories (Dillon Francis Remix)</td>
      <td>2019-12-13</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1HoSmj2eLcsrR0vE9gThr4</td>
      <td>All the Time (Don Diablo Remix)</td>
      <td>2019-07-05</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1nqYsOef1yKKuGOVchbsk6</td>
      <td>Call You Mine - The Remixes</td>
      <td>2019-07-19</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7m7vv9wlQ4i0LFuJiE2zsQ</td>
      <td>Someone You Loved (Future Humans Remix)</td>
      <td>2019-03-05</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>32828</th>
      <td>2azRoBBWEEEYhqV6sb7JrT</td>
      <td>City Of Lights (Vocal Mix)</td>
      <td>2014-04-28</td>
    </tr>
    <tr>
      <th>32829</th>
      <td>6kD6KLxj7s8eCE3ABvAyf5</td>
      <td>Closer Remixed</td>
      <td>2013-03-08</td>
    </tr>
    <tr>
      <th>32830</th>
      <td>0ltWNSY9JgxoIZO4VzuCa6</td>
      <td>Sweet Surrender (Radio Edit)</td>
      <td>2014-04-21</td>
    </tr>
    <tr>
      <th>32831</th>
      <td>1fGrOkHnHJcStl14zNx8Jy</td>
      <td>Only For You (Remixes)</td>
      <td>2014-01-01</td>
    </tr>
    <tr>
      <th>32832</th>
      <td>0X3mUOm6MhxR7PzxG95rAo</td>
      <td>Typhoon/Storm</td>
      <td>2014-03-03</td>
    </tr>
  </tbody>
</table>
<p>22545 rows × 3 columns</p>

</div>




```python
df_playlist = df.iloc[:, [7,8,9,10]].drop_duplicates()
df_playlist
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
      <th>playlist_name</th>
      <th>playlist_id</th>
      <th>playlist_genre</th>
      <th>playlist_subgenre</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Pop Remix</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>pop</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Dance Pop</td>
      <td>37i9dQZF1DWZQaaqNMbbXa</td>
      <td>pop</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>167</th>
      <td>Dance Room</td>
      <td>37i9dQZF1DX2ENAPP1Tyed</td>
      <td>pop</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>223</th>
      <td>Cardio</td>
      <td>37i9dQZF1DWSJHnPb1f0X3</td>
      <td>pop</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>272</th>
      <td>Dance Pop Hits</td>
      <td>37i9dQZF1DX6pH08wMhkaI</td>
      <td>pop</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>32409</th>
      <td>Fresh EDM | Progressive House | Electro House ...</td>
      <td>0FCHg9zJMNNiOokh3hVcxd</td>
      <td>edm</td>
      <td>progressive electro house</td>
    </tr>
    <tr>
      <th>32504</th>
      <td>Festival Music 2019 - Warm Up Music (EDM, Big ...</td>
      <td>73uj4YmsC7SJ6SbUMTvf07</td>
      <td>edm</td>
      <td>progressive electro house</td>
    </tr>
    <tr>
      <th>32582</th>
      <td>Underground Party | Hypnotic | Minimal | Acid ...</td>
      <td>29jj7pQlDqnWclbHQk21Rq</td>
      <td>edm</td>
      <td>progressive electro house</td>
    </tr>
    <tr>
      <th>32674</th>
      <td>Trending EDM by Nik Cooper</td>
      <td>4N1ipiKR3xla8UXtE12XBm</td>
      <td>edm</td>
      <td>progressive electro house</td>
    </tr>
    <tr>
      <th>32753</th>
      <td>♥ EDM LOVE 2020</td>
      <td>6jI1gFr6ANFtT8MmTvA2Ux</td>
      <td>edm</td>
      <td>progressive electro house</td>
    </tr>
  </tbody>
</table>
<p>480 rows × 4 columns</p>

</div>




```python
df_attri = df.iloc[:, [0,11,12,13,14,15,16,17,18,19,20,21,22]].drop_duplicates()
df_attri
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
      <th>track_id</th>
      <th>danceability</th>
      <th>energy</th>
      <th>key</th>
      <th>loudness</th>
      <th>mode</th>
      <th>speechiness</th>
      <th>acousticness</th>
      <th>instrumentalness</th>
      <th>liveness</th>
      <th>valence</th>
      <th>tempo</th>
      <th>duration_ms</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6f807x0ima9a1j3VPbc7VN</td>
      <td>0.748</td>
      <td>0.916</td>
      <td>6</td>
      <td>-2.634</td>
      <td>1</td>
      <td>0.0583</td>
      <td>0.102000</td>
      <td>0.000000</td>
      <td>0.0653</td>
      <td>0.5180</td>
      <td>122.036</td>
      <td>194754</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0r7CVbZTWZgbTCYdfa2P31</td>
      <td>0.726</td>
      <td>0.815</td>
      <td>11</td>
      <td>-4.969</td>
      <td>1</td>
      <td>0.0373</td>
      <td>0.072400</td>
      <td>0.004210</td>
      <td>0.3570</td>
      <td>0.6930</td>
      <td>99.972</td>
      <td>162600</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1z1Hg7Vb0AhHDiEmnDE79l</td>
      <td>0.675</td>
      <td>0.931</td>
      <td>1</td>
      <td>-3.432</td>
      <td>0</td>
      <td>0.0742</td>
      <td>0.079400</td>
      <td>0.000023</td>
      <td>0.1100</td>
      <td>0.6130</td>
      <td>124.008</td>
      <td>176616</td>
    </tr>
    <tr>
      <th>3</th>
      <td>75FpbthrwQmzHlBJLuGdC7</td>
      <td>0.718</td>
      <td>0.930</td>
      <td>7</td>
      <td>-3.778</td>
      <td>1</td>
      <td>0.1020</td>
      <td>0.028700</td>
      <td>0.000009</td>
      <td>0.2040</td>
      <td>0.2770</td>
      <td>121.956</td>
      <td>169093</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1e8PAfcKUYoKkxPhrHqw4x</td>
      <td>0.650</td>
      <td>0.833</td>
      <td>1</td>
      <td>-4.672</td>
      <td>1</td>
      <td>0.0359</td>
      <td>0.080300</td>
      <td>0.000000</td>
      <td>0.0833</td>
      <td>0.7250</td>
      <td>123.976</td>
      <td>189052</td>
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
      <td>...</td>
    </tr>
    <tr>
      <th>32828</th>
      <td>7bxnKAamR3snQ1VGLuVfC1</td>
      <td>0.428</td>
      <td>0.922</td>
      <td>2</td>
      <td>-1.814</td>
      <td>1</td>
      <td>0.0936</td>
      <td>0.076600</td>
      <td>0.000000</td>
      <td>0.0668</td>
      <td>0.2100</td>
      <td>128.170</td>
      <td>204375</td>
    </tr>
    <tr>
      <th>32829</th>
      <td>5Aevni09Em4575077nkWHz</td>
      <td>0.522</td>
      <td>0.786</td>
      <td>0</td>
      <td>-4.462</td>
      <td>1</td>
      <td>0.0420</td>
      <td>0.001710</td>
      <td>0.004270</td>
      <td>0.3750</td>
      <td>0.4000</td>
      <td>128.041</td>
      <td>353120</td>
    </tr>
    <tr>
      <th>32830</th>
      <td>7ImMqPP3Q1yfUHvsdn7wEo</td>
      <td>0.529</td>
      <td>0.821</td>
      <td>6</td>
      <td>-4.899</td>
      <td>0</td>
      <td>0.0481</td>
      <td>0.108000</td>
      <td>0.000001</td>
      <td>0.1500</td>
      <td>0.4360</td>
      <td>127.989</td>
      <td>210112</td>
    </tr>
    <tr>
      <th>32831</th>
      <td>2m69mhnfQ1Oq6lGtXuYhgX</td>
      <td>0.626</td>
      <td>0.888</td>
      <td>2</td>
      <td>-3.361</td>
      <td>1</td>
      <td>0.1090</td>
      <td>0.007920</td>
      <td>0.127000</td>
      <td>0.3430</td>
      <td>0.3080</td>
      <td>128.008</td>
      <td>367432</td>
    </tr>
    <tr>
      <th>32832</th>
      <td>29zWqhca3zt5NsckZqDf6c</td>
      <td>0.603</td>
      <td>0.884</td>
      <td>5</td>
      <td>-4.571</td>
      <td>0</td>
      <td>0.0385</td>
      <td>0.000133</td>
      <td>0.341000</td>
      <td>0.7420</td>
      <td>0.0894</td>
      <td>127.984</td>
      <td>337500</td>
    </tr>
  </tbody>
</table>
<p>28356 rows × 13 columns</p>

</div>




```python
df_songs_albums = df.iloc[:, [0, 4]].drop_duplicates()  ## link table
df_songs_albums
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
      <th>track_id</th>
      <th>track_album_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6f807x0ima9a1j3VPbc7VN</td>
      <td>2oCs0DGTsRO98Gh5ZSl2Cx</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0r7CVbZTWZgbTCYdfa2P31</td>
      <td>63rPSO264uRjW1X5E6cWv6</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1z1Hg7Vb0AhHDiEmnDE79l</td>
      <td>1HoSmj2eLcsrR0vE9gThr4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>75FpbthrwQmzHlBJLuGdC7</td>
      <td>1nqYsOef1yKKuGOVchbsk6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1e8PAfcKUYoKkxPhrHqw4x</td>
      <td>7m7vv9wlQ4i0LFuJiE2zsQ</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>32828</th>
      <td>7bxnKAamR3snQ1VGLuVfC1</td>
      <td>2azRoBBWEEEYhqV6sb7JrT</td>
    </tr>
    <tr>
      <th>32829</th>
      <td>5Aevni09Em4575077nkWHz</td>
      <td>6kD6KLxj7s8eCE3ABvAyf5</td>
    </tr>
    <tr>
      <th>32830</th>
      <td>7ImMqPP3Q1yfUHvsdn7wEo</td>
      <td>0ltWNSY9JgxoIZO4VzuCa6</td>
    </tr>
    <tr>
      <th>32831</th>
      <td>2m69mhnfQ1Oq6lGtXuYhgX</td>
      <td>1fGrOkHnHJcStl14zNx8Jy</td>
    </tr>
    <tr>
      <th>32832</th>
      <td>29zWqhca3zt5NsckZqDf6c</td>
      <td>0X3mUOm6MhxR7PzxG95rAo</td>
    </tr>
  </tbody>
</table>
<p>28356 rows × 2 columns</p>

</div>




```python
df_songs_playlist = df.iloc[:, [0, 8]].drop_duplicates()  ## link table
df_songs_playlist
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
      <th>track_id</th>
      <th>playlist_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6f807x0ima9a1j3VPbc7VN</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0r7CVbZTWZgbTCYdfa2P31</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1z1Hg7Vb0AhHDiEmnDE79l</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
    </tr>
    <tr>
      <th>3</th>
      <td>75FpbthrwQmzHlBJLuGdC7</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1e8PAfcKUYoKkxPhrHqw4x</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>32828</th>
      <td>7bxnKAamR3snQ1VGLuVfC1</td>
      <td>6jI1gFr6ANFtT8MmTvA2Ux</td>
    </tr>
    <tr>
      <th>32829</th>
      <td>5Aevni09Em4575077nkWHz</td>
      <td>6jI1gFr6ANFtT8MmTvA2Ux</td>
    </tr>
    <tr>
      <th>32830</th>
      <td>7ImMqPP3Q1yfUHvsdn7wEo</td>
      <td>6jI1gFr6ANFtT8MmTvA2Ux</td>
    </tr>
    <tr>
      <th>32831</th>
      <td>2m69mhnfQ1Oq6lGtXuYhgX</td>
      <td>6jI1gFr6ANFtT8MmTvA2Ux</td>
    </tr>
    <tr>
      <th>32832</th>
      <td>29zWqhca3zt5NsckZqDf6c</td>
      <td>6jI1gFr6ANFtT8MmTvA2Ux</td>
    </tr>
  </tbody>
</table>
<p>32251 rows × 2 columns</p>

</div>



### 4. Create a SQLite3 schema to store this data


```python
import sqlite3

conn = sqlite3.connect('SpotifyDB.db')  # You can create a new database by changing the name within the quotes
c = conn.cursor() # The database will be saved in the location where your 'py' file is saved

df_songs.to_sql('songs', conn, if_exists='append', index = False) # Insert the values from the dataframe into the table 'songs' 
df_albums.to_sql('albums', conn, if_exists='append', index = False)
df_playlist.to_sql('playlist', conn, if_exists='append', index = False)
df_attri.to_sql('attri', conn, if_exists='append', index = False)
df_songs_albums.to_sql('songs_albums', conn, if_exists='append', index = False)
df_songs_playlist.to_sql('songs_playlist', conn, if_exists='append', index = False)

```


```python
c.close()
conn.close()
```

###  5.Use an SQL query to find the names of all playlists that contain instrumentals


```python
%load_ext sql
```

    The sql extension is already loaded. To reload it, use:
      %reload_ext sql



```python
%sql sqlite:///SpotifyDB.db
```




    'Connected: @SpotifyDB.db'




```python
%%sql

SELECT * FROM sqlite_master WHERE type='table';
```

       sqlite://
     * sqlite:///SpotifyDB.db
    Done.





<table>
    <tr>
        <th>type</th>
        <th>name</th>
        <th>tbl_name</th>
        <th>rootpage</th>
        <th>sql</th>
    </tr>
    <tr>
        <td>table</td>
        <td>songs</td>
        <td>songs</td>
        <td>2</td>
        <td>CREATE TABLE &quot;songs&quot; (<br>&quot;track_id&quot; TEXT,<br>  &quot;track_name&quot; TEXT,<br>  &quot;track_artist&quot; TEXT,<br>  &quot;track_popularity&quot; INTEGER<br>)</td>
    </tr>
    <tr>
        <td>table</td>
        <td>albums</td>
        <td>albums</td>
        <td>1288</td>
        <td>CREATE TABLE &quot;albums&quot; (<br>&quot;track_album_id&quot; TEXT,<br>  &quot;track_album_name&quot; TEXT,<br>  &quot;track_album_release_date&quot; TEXT<br>)</td>
    </tr>
    <tr>
        <td>table</td>
        <td>playlist</td>
        <td>playlist</td>
        <td>2374</td>
        <td>CREATE TABLE &quot;playlist&quot; (<br>&quot;playlist_name&quot; TEXT,<br>  &quot;playlist_id&quot; TEXT,<br>  &quot;playlist_genre&quot; TEXT,<br>  &quot;playlist_subgenre&quot; TEXT<br>)</td>
    </tr>
    <tr>
        <td>table</td>
        <td>attri</td>
        <td>attri</td>
        <td>2384</td>
        <td>CREATE TABLE &quot;attri&quot; (<br>&quot;track_id&quot; TEXT,<br>  &quot;danceability&quot; REAL,<br>  &quot;energy&quot; REAL,<br>  &quot;key&quot; INTEGER,<br>  &quot;loudness&quot; REAL,<br>  &quot;mode&quot; INTEGER,<br>  &quot;speechiness&quot; REAL,<br>  &quot;acousticness&quot; REAL,<br>  &quot;instrumentalness&quot; REAL,<br>  &quot;liveness&quot; REAL,<br>  &quot;valence&quot; REAL,<br>  &quot;tempo&quot; REAL,<br>  &quot;duration_ms&quot; INTEGER<br>)</td>
    </tr>
    <tr>
        <td>table</td>
        <td>songs_albums</td>
        <td>songs_albums</td>
        <td>3191</td>
        <td>CREATE TABLE &quot;songs_albums&quot; (<br>&quot;track_id&quot; TEXT,<br>  &quot;track_album_id&quot; TEXT<br>)</td>
    </tr>
    <tr>
        <td>table</td>
        <td>songs_playlist</td>
        <td>songs_playlist</td>
        <td>3558</td>
        <td>CREATE TABLE &quot;songs_playlist&quot; (<br>&quot;track_id&quot; TEXT,<br>  &quot;playlist_id&quot; TEXT<br>)</td>
    </tr>
</table>




According to the definition of instrumentalness:

- Predicts whether a track contains no vocals. “Ooh” and “aah” sounds are treated as instrumental in this context. Rap or spoken word tracks are clearly “vocal”. The closer the instrumentalness value is to 1.0, the greater likelihood the track contains no vocal content. Values above 0.5 are intended to represent instrumental tracks, but confidence is higher as the value approaches 1.0.

**Here, we use 0.5 as the threshold of if the song contains instrumentals**


```python
%%sql 

Select DISTINCT playlist_name
From songs
Inner join attri
    ON songs.track_id = attri.track_id
Inner join songs_playlist
    ON songs.track_id = songs_playlist.track_id
Inner join playlist
    ON songs_playlist.playlist_id = playlist.playlist_id
Where instrumentalness > 0.5
```

       sqlite://
     * sqlite:///SpotifyDB.db
    Done.





<table>
    <tr>
        <th>playlist_name</th>
    </tr>
    <tr>
        <td>Pop Remix</td>
    </tr>
    <tr>
        <td>Dance Room</td>
    </tr>
    <tr>
        <td>Pop Warmup 130 BPM</td>
    </tr>
    <tr>
        <td>Dance Pop</td>
    </tr>
    <tr>
        <td>Dance Pop Tunes</td>
    </tr>
    <tr>
        <td>Pop / Dance</td>
    </tr>
    <tr>
        <td>Most Popular 2020 TOP 50</td>
    </tr>
    <tr>
        <td>post-teen alternative, indie, pop (large variety)</td>
    </tr>
    <tr>
        <td>Todo Éxitos</td>
    </tr>
    <tr>
        <td>Charts 2020 🔥Top 2020🔥Hits 2020🔥Summer 2020🔥Pop 2020🔥Popular Music🔥Clean Pop 2020🔥Sing Alongs</td>
    </tr>
    <tr>
        <td>2020 Hits &amp; 2019  Hits – Top Global Tracks 🔥🔥🔥</td>
    </tr>
    <tr>
        <td>Pop Hits 2020</td>
    </tr>
    <tr>
        <td>Music&amp;Other Drugs</td>
    </tr>
    <tr>
        <td>90s Dance Hits</td>
    </tr>
    <tr>
        <td>CHRISTIAN ELECTRO / DANCE / EDM</td>
    </tr>
    <tr>
        <td>Christian Dance Party</td>
    </tr>
    <tr>
        <td>........</td>
    </tr>
    <tr>
        <td>EDM/POP</td>
    </tr>
    <tr>
        <td>Selected House</td>
    </tr>
    <tr>
        <td>Vocal House</td>
    </tr>
    <tr>
        <td>Electro/Progressive/Club House</td>
    </tr>
    <tr>
        <td>Underground Party | Hypnotic | Minimal | Acid | Big Room | Tech | Liquid</td>
    </tr>
</table>



