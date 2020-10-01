---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Star Wars universe API data analysis"
subtitle: ""
summary: "Use requests and pandas packages to deal with the API"
authors: []
tags: [Python,Pandas,API]
categories: []
date: 2020-09-30T20:57:59-04:00
lastmod: 2020-09-30T20:57:59-04:00
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

The task is : use the requests library, download all the people in the Star Wars universe using the Star Wars API (https://swapi.dev/documentation). Show the name of the oldest person (or robot or alien) and list the titles of all the films they appeared in. 

### 1. Download Star Wars universe database


```python
import requests
import numpy as np
import pandas as pd
import os
import json
```


```python
base_url = 'http://swapi.dev/api/'
```


```python
i = 1
urls=[]

while(True):
    urls.append(os.path.join(base_url, f'people/{i}'))
    if requests.get(os.path.join(base_url, f'people/{i+1}')).json() == {'detail': 'Not found'}:
        i +=1
        if requests.get(os.path.join(base_url, f'people/{i+1}')).json() == {'detail': 'Not found'}:
            break
    
    i +=1
```


```python
len(urls)
```




    82




```python
people = [requests.get(url).json() for url in urls]
```


```python
people
```




```python
[{'name': 'Luke Skywalker',
  'height': '172',
  'mass': '77',
  'hair_color': 'blond',
  'skin_color': 'fair',
  'eye_color': 'blue',
  'birth_year': '19BBY',
  'gender': 'male',
  'homeworld': 'http://swapi.dev/api/planets/1/',
  'films': ['http://swapi.dev/api/films/1/',
   'http://swapi.dev/api/films/2/',
   'http://swapi.dev/api/films/3/',
   'http://swapi.dev/api/films/6/'],
  'species': [],
  'vehicles': ['http://swapi.dev/api/vehicles/14/',
   'http://swapi.dev/api/vehicles/30/'],
  'starships': ['http://swapi.dev/api/starships/12/',
   'http://swapi.dev/api/starships/22/'],
  'created': '2014-12-09T13:50:51.644000Z',
  'edited': '2014-12-20T21:17:56.891000Z',
  'url': 'http://swapi.dev/api/people/1/'},
 {'name': 'C-3PO',
  'height': '167',
  'mass': '75',
  'hair_color': 'n/a',
  'skin_color': 'gold',
  'eye_color': 'yellow',
  'birth_year': '112BBY',
  'gender': 'n/a',
  'homeworld': 'http://swapi.dev/api/planets/1/',
  'films': ['http://swapi.dev/api/films/1/',
   'http://swapi.dev/api/films/2/',
   'http://swapi.dev/api/films/3/',
   'http://swapi.dev/api/films/4/',
   'http://swapi.dev/api/films/5/',
   'http://swapi.dev/api/films/6/'],
  'species': ['http://swapi.dev/api/species/2/'],
  'vehicles': [],
  'starships': [],
  'created': '2014-12-10T15:10:51.357000Z',
  'edited': '2014-12-20T21:17:50.309000Z',
  'url': 'http://swapi.dev/api/people/2/'},
............
............
............
 {'name': 'Tion Medon',
  'height': '206',
  'mass': '80',
  'hair_color': 'none',
  'skin_color': 'grey',
  'eye_color': 'black',
  'birth_year': 'unknown',
  'gender': 'male',
  'homeworld': 'http://swapi.dev/api/planets/12/',
  'films': ['http://swapi.dev/api/films/6/'],
  'species': ['http://swapi.dev/api/species/37/'],
  'vehicles': [],
  'starships': [],
  'created': '2014-12-20T20:35:04.260000Z',
  'edited': '2014-12-20T21:17:50.498000Z',
  'url': 'http://swapi.dev/api/people/83/'}]
```




```python
len(people)
```




    82



Then, I wrote a function to get nested film information.


```python
def get_nested(d):
    """
    This function is to download nested film information in the api.
    
    Parameters
    ----------
    d: dict
    the json dict you downloaded
       
    Returns
    ----------
    d: dict  
    the nested json dict with additional film information
    
    """
    films_title=[]
    urls = d['films']
    films = [requests.get(url).json() for url in urls]
    
    for x in films:
        films_title.append( {'title' : x['title']} ) 
   
    d['films']  = films_title
    
    return d
```


```python
for x in people:
    get_nested(x)
```


```python
people
```


```python
[{'name': 'Luke Skywalker',
  'height': '172',
  'mass': '77',
  'hair_color': 'blond',
  'skin_color': 'fair',
  'eye_color': 'blue',
  'birth_year': '19BBY',
  'gender': 'male',
  'homeworld': 'http://swapi.dev/api/planets/1/',
  'films': [{'title': 'A New Hope'},
   {'title': 'The Empire Strikes Back'},
   {'title': 'Return of the Jedi'},
   {'title': 'Revenge of the Sith'}],
  'species': [],
  'vehicles': ['http://swapi.dev/api/vehicles/14/',
   'http://swapi.dev/api/vehicles/30/'],
  'starships': ['http://swapi.dev/api/starships/12/',
   'http://swapi.dev/api/starships/22/'],
  'created': '2014-12-09T13:50:51.644000Z',
  'edited': '2014-12-20T21:17:56.891000Z',
  'url': 'http://swapi.dev/api/people/1/'},
 {'name': 'C-3PO',
  'height': '167',
  'mass': '75',
  'hair_color': 'n/a',
  'skin_color': 'gold',
  'eye_color': 'yellow',
  'birth_year': '112BBY',
  'gender': 'n/a',
  'homeworld': 'http://swapi.dev/api/planets/1/',
  'films': [{'title': 'A New Hope'},
   {'title': 'The Empire Strikes Back'},
   {'title': 'Return of the Jedi'},
   {'title': 'The Phantom Menace'},
   {'title': 'Attack of the Clones'},
   {'title': 'Revenge of the Sith'}],
  'species': ['http://swapi.dev/api/species/2/'],
  'vehicles': [],
  'starships': [],
  'created': '2014-12-10T15:10:51.357000Z',
  'edited': '2014-12-20T21:17:50.309000Z',
  'url': 'http://swapi.dev/api/people/2/'},
  ............
```



### 2. Data analysis


```python
df = pd.json_normalize(people)
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
      <th>name</th>
      <th>height</th>
      <th>mass</th>
      <th>hair_color</th>
      <th>skin_color</th>
      <th>eye_color</th>
      <th>birth_year</th>
      <th>gender</th>
      <th>homeworld</th>
      <th>films</th>
      <th>species</th>
      <th>vehicles</th>
      <th>starships</th>
      <th>created</th>
      <th>edited</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Luke Skywalker</td>
      <td>172</td>
      <td>77</td>
      <td>blond</td>
      <td>fair</td>
      <td>blue</td>
      <td>19BBY</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[{'title': 'A New Hope'}, {'title': 'The Empir...</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/vehicles/14/, http://swa...</td>
      <td>[http://swapi.dev/api/starships/12/, http://sw...</td>
      <td>2014-12-09T13:50:51.644000Z</td>
      <td>2014-12-20T21:17:56.891000Z</td>
      <td>http://swapi.dev/api/people/1/</td>
    </tr>
    <tr>
      <th>1</th>
      <td>C-3PO</td>
      <td>167</td>
      <td>75</td>
      <td>n/a</td>
      <td>gold</td>
      <td>yellow</td>
      <td>112BBY</td>
      <td>n/a</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[{'title': 'A New Hope'}, {'title': 'The Empir...</td>
      <td>[http://swapi.dev/api/species/2/]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-10T15:10:51.357000Z</td>
      <td>2014-12-20T21:17:50.309000Z</td>
      <td>http://swapi.dev/api/people/2/</td>
    </tr>
    <tr>
      <th>2</th>
      <td>R2-D2</td>
      <td>96</td>
      <td>32</td>
      <td>n/a</td>
      <td>white, blue</td>
      <td>red</td>
      <td>33BBY</td>
      <td>n/a</td>
      <td>http://swapi.dev/api/planets/8/</td>
      <td>[{'title': 'A New Hope'}, {'title': 'The Empir...</td>
      <td>[http://swapi.dev/api/species/2/]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-10T15:11:50.376000Z</td>
      <td>2014-12-20T21:17:50.311000Z</td>
      <td>http://swapi.dev/api/people/3/</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Darth Vader</td>
      <td>202</td>
      <td>136</td>
      <td>none</td>
      <td>white</td>
      <td>yellow</td>
      <td>41.9BBY</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[{'title': 'A New Hope'}, {'title': 'The Empir...</td>
      <td>[]</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/starships/13/]</td>
      <td>2014-12-10T15:18:20.704000Z</td>
      <td>2014-12-20T21:17:50.313000Z</td>
      <td>http://swapi.dev/api/people/4/</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Leia Organa</td>
      <td>150</td>
      <td>49</td>
      <td>brown</td>
      <td>light</td>
      <td>brown</td>
      <td>19BBY</td>
      <td>female</td>
      <td>http://swapi.dev/api/planets/2/</td>
      <td>[{'title': 'A New Hope'}, {'title': 'The Empir...</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/vehicles/30/]</td>
      <td>[]</td>
      <td>2014-12-10T15:20:09.791000Z</td>
      <td>2014-12-20T21:17:50.315000Z</td>
      <td>http://swapi.dev/api/people/5/</td>
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
    </tr>
    <tr>
      <th>77</th>
      <td>Grievous</td>
      <td>216</td>
      <td>159</td>
      <td>none</td>
      <td>brown, white</td>
      <td>green, yellow</td>
      <td>unknown</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/59/</td>
      <td>[{'title': 'Revenge of the Sith'}]</td>
      <td>[http://swapi.dev/api/species/36/]</td>
      <td>[http://swapi.dev/api/vehicles/60/]</td>
      <td>[http://swapi.dev/api/starships/74/]</td>
      <td>2014-12-20T19:43:53.348000Z</td>
      <td>2014-12-20T21:17:50.488000Z</td>
      <td>http://swapi.dev/api/people/79/</td>
    </tr>
    <tr>
      <th>78</th>
      <td>Tarfful</td>
      <td>234</td>
      <td>136</td>
      <td>brown</td>
      <td>brown</td>
      <td>blue</td>
      <td>unknown</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/14/</td>
      <td>[{'title': 'Revenge of the Sith'}]</td>
      <td>[http://swapi.dev/api/species/3/]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-20T19:46:34.209000Z</td>
      <td>2014-12-20T21:17:50.491000Z</td>
      <td>http://swapi.dev/api/people/80/</td>
    </tr>
    <tr>
      <th>79</th>
      <td>Raymus Antilles</td>
      <td>188</td>
      <td>79</td>
      <td>brown</td>
      <td>light</td>
      <td>brown</td>
      <td>unknown</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/2/</td>
      <td>[{'title': 'A New Hope'}, {'title': 'Revenge o...</td>
      <td>[]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-20T19:49:35.583000Z</td>
      <td>2014-12-20T21:17:50.493000Z</td>
      <td>http://swapi.dev/api/people/81/</td>
    </tr>
    <tr>
      <th>80</th>
      <td>Sly Moore</td>
      <td>178</td>
      <td>48</td>
      <td>none</td>
      <td>pale</td>
      <td>white</td>
      <td>unknown</td>
      <td>female</td>
      <td>http://swapi.dev/api/planets/60/</td>
      <td>[{'title': 'Attack of the Clones'}, {'title': ...</td>
      <td>[]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-20T20:18:37.619000Z</td>
      <td>2014-12-20T21:17:50.496000Z</td>
      <td>http://swapi.dev/api/people/82/</td>
    </tr>
    <tr>
      <th>81</th>
      <td>Tion Medon</td>
      <td>206</td>
      <td>80</td>
      <td>none</td>
      <td>grey</td>
      <td>black</td>
      <td>unknown</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/12/</td>
      <td>[{'title': 'Revenge of the Sith'}]</td>
      <td>[http://swapi.dev/api/species/37/]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-20T20:35:04.260000Z</td>
      <td>2014-12-20T21:17:50.498000Z</td>
      <td>http://swapi.dev/api/people/83/</td>
    </tr>
  </tbody>
</table>
<p>82 rows × 16 columns</p>

</div>



For the birth year of the person, using the in-universe standard of BBY or ABY - Before the Battle of Yavin or After the Battle of Yavin. The Battle of Yavin is a battle that occurs at the end of Star Wars episode IV: A New Hope.

Thus, we should only look at those with BBY.


```python
df1 = df[df['birth_year'].str.contains("BBY")]
df1.head(2)
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
      <th>name</th>
      <th>height</th>
      <th>mass</th>
      <th>hair_color</th>
      <th>skin_color</th>
      <th>eye_color</th>
      <th>birth_year</th>
      <th>gender</th>
      <th>homeworld</th>
      <th>films</th>
      <th>species</th>
      <th>vehicles</th>
      <th>starships</th>
      <th>created</th>
      <th>edited</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Luke Skywalker</td>
      <td>172</td>
      <td>77</td>
      <td>blond</td>
      <td>fair</td>
      <td>blue</td>
      <td>19BBY</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[{'title': 'A New Hope'}, {'title': 'The Empir...</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/vehicles/14/, http://swa...</td>
      <td>[http://swapi.dev/api/starships/12/, http://sw...</td>
      <td>2014-12-09T13:50:51.644000Z</td>
      <td>2014-12-20T21:17:56.891000Z</td>
      <td>http://swapi.dev/api/people/1/</td>
    </tr>
    <tr>
      <th>1</th>
      <td>C-3PO</td>
      <td>167</td>
      <td>75</td>
      <td>n/a</td>
      <td>gold</td>
      <td>yellow</td>
      <td>112BBY</td>
      <td>n/a</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[{'title': 'A New Hope'}, {'title': 'The Empir...</td>
      <td>[http://swapi.dev/api/species/2/]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-10T15:10:51.357000Z</td>
      <td>2014-12-20T21:17:50.309000Z</td>
      <td>http://swapi.dev/api/people/2/</td>
      </tr>
        </tbody>
</table>

​      


  </tbody>
</table>

​      

</div>




```python
df1['birth_year'] = df1['birth_year'].str.rstrip('BBY')
df1['birth_year'] = pd.to_numeric(df1['birth_year'])
df1.head(2)
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
      <th>name</th>
      <th>height</th>
      <th>mass</th>
      <th>hair_color</th>
      <th>skin_color</th>
      <th>eye_color</th>
      <th>birth_year</th>
      <th>gender</th>
      <th>homeworld</th>
      <th>films</th>
      <th>species</th>
      <th>vehicles</th>
      <th>starships</th>
      <th>created</th>
      <th>edited</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Luke Skywalker</td>
      <td>172</td>
      <td>77</td>
      <td>blond</td>
      <td>fair</td>
      <td>blue</td>
      <td>19</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[{'title': 'A New Hope'}, {'title': 'The Empir...</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/vehicles/14/, http://swa...</td>
      <td>[http://swapi.dev/api/starships/12/, http://sw...</td>
      <td>2014-12-09T13:50:51.644000Z</td>
      <td>2014-12-20T21:17:56.891000Z</td>
      <td>http://swapi.dev/api/people/1/</td>
    </tr>
    <tr>
      <th>1</th>
      <td>C-3PO</td>
      <td>167</td>
      <td>75</td>
      <td>n/a</td>
      <td>gold</td>
      <td>yellow</td>
      <td>112</td>
      <td>n/a</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[{'title': 'A New Hope'}, {'title': 'The Empir...</td>
      <td>[http://swapi.dev/api/species/2/]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-10T15:10:51.357000Z</td>
      <td>2014-12-20T21:17:50.309000Z</td>
      <td>http://swapi.dev/api/people/2/</td>
      </tr>
        </tbody>
</table>


</div>




```python
df1.loc[df1['birth_year'].idxmax()]['name']
```




    'Yoda'




```python
df1.loc[df1['birth_year'].idxmax()]
```




    name                                                       Yoda
    height                                                       66
    mass                                                         17
    hair_color                                                white
    skin_color                                                green
    eye_color                                                 brown
    birth_year                                                  896
    gender                                                     male
    homeworld                      http://swapi.dev/api/planets/28/
    films         [{'title': 'The Empire Strikes Back'}, {'title...
    species                       [http://swapi.dev/api/species/6/]
    vehicles                                                     []
    starships                                                    []
    created                             2014-12-15T12:26:01.042000Z
    edited                              2014-12-20T21:17:50.345000Z
    url                             http://swapi.dev/api/people/20/
    Name: 18, dtype: object



**Thus, the name of the oldest person (alien) is _Yoda_ and list the titles of all the films they appeared in is:**


```python
df1.loc[df1['birth_year'].idxmax()]['films']
```




    [{'title': 'The Empire Strikes Back'},
     {'title': 'Return of the Jedi'},
     {'title': 'The Phantom Menace'},
     {'title': 'Attack of the Clones'},
     {'title': 'Revenge of the Sith'}]

