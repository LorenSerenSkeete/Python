# Data Cleaning & Analysis - Most Streamed Spotify Songs 2024


```python
!pip install chardet
!pip install dash
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px
import chardet
import plotly.graph_objects as go
from plotly.subplots import make_subplots
import dash
from dash import dcc
from dash import html 
from dash.dependencies import Input, Output
```

    Requirement already satisfied: chardet in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (5.2.0)
    
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m A new release of pip is available: [0m[31;49m24.0[0m[39;49m -> [0m[32;49m24.2[0m
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m To update, run: [0m[32;49mpip install --upgrade pip[0m
    Requirement already satisfied: dash in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (2.18.1)
    Requirement already satisfied: Flask<3.1,>=1.0.4 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from dash) (3.0.3)
    Requirement already satisfied: Werkzeug<3.1 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from dash) (3.0.4)
    Requirement already satisfied: plotly>=5.0.0 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from dash) (5.24.1)
    Requirement already satisfied: dash-html-components==2.0.0 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from dash) (2.0.0)
    Requirement already satisfied: dash-core-components==2.0.0 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from dash) (2.0.0)
    Requirement already satisfied: dash-table==5.0.0 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from dash) (5.0.0)
    Requirement already satisfied: importlib-metadata in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from dash) (8.5.0)
    Requirement already satisfied: typing-extensions>=4.1.1 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from dash) (4.12.2)
    Requirement already satisfied: requests in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from dash) (2.32.3)
    Requirement already satisfied: retrying in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from dash) (1.3.4)
    Requirement already satisfied: nest-asyncio in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from dash) (1.6.0)
    Requirement already satisfied: setuptools in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from dash) (72.1.0)
    Requirement already satisfied: Jinja2>=3.1.2 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from Flask<3.1,>=1.0.4->dash) (3.1.4)
    Requirement already satisfied: itsdangerous>=2.1.2 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from Flask<3.1,>=1.0.4->dash) (2.2.0)
    Requirement already satisfied: click>=8.1.3 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from Flask<3.1,>=1.0.4->dash) (8.1.7)
    Requirement already satisfied: blinker>=1.6.2 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from Flask<3.1,>=1.0.4->dash) (1.8.2)
    Requirement already satisfied: tenacity>=6.2.0 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from plotly>=5.0.0->dash) (9.0.0)
    Requirement already satisfied: packaging in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from plotly>=5.0.0->dash) (24.1)
    Requirement already satisfied: MarkupSafe>=2.1.1 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from Werkzeug<3.1->dash) (2.1.5)
    Requirement already satisfied: zipp>=3.20 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from importlib-metadata->dash) (3.20.2)
    Requirement already satisfied: charset-normalizer<4,>=2 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from requests->dash) (3.3.2)
    Requirement already satisfied: idna<4,>=2.5 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from requests->dash) (3.7)
    Requirement already satisfied: urllib3<3,>=1.21.1 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from requests->dash) (2.2.2)
    Requirement already satisfied: certifi>=2017.4.17 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from requests->dash) (2024.7.4)
    Requirement already satisfied: six>=1.7.0 in /Users/serenskeete/Desktop/venv/lib/python3.12/site-packages (from retrying->dash) (1.16.0)
    
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m A new release of pip is available: [0m[31;49m24.0[0m[39;49m -> [0m[32;49m24.2[0m
    [1m[[0m[34;49mnotice[0m[1;39;49m][0m[39;49m To update, run: [0m[32;49mpip install --upgrade pip[0m



```python
with open(r'/Users/serenskeete/Downloads/most_streamed_spotify_songs.csv', 'rb') as file:
    data = file.read(100000)
    result = chardet.detect(data)
    encoding = result['encoding']
    print(f"Detected encoding: {encoding}")
```

    Detected encoding: ISO-8859-1



```python
df = pd.read_csv(r'/Users/serenskeete/Downloads/most_streamed_spotify_songs.csv',encoding="ISO-8859-1")
```


```python
pd.set_option('display.max_columns',85)
```


```python
df.head()
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
      <th>Track</th>
      <th>Album Name</th>
      <th>Artist</th>
      <th>Release Date</th>
      <th>ISRC</th>
      <th>All Time Rank</th>
      <th>Track Score</th>
      <th>Spotify Streams</th>
      <th>Spotify Playlist Count</th>
      <th>Spotify Playlist Reach</th>
      <th>Spotify Popularity</th>
      <th>YouTube Views</th>
      <th>YouTube Likes</th>
      <th>TikTok Posts</th>
      <th>TikTok Likes</th>
      <th>TikTok Views</th>
      <th>YouTube Playlist Reach</th>
      <th>Apple Music Playlist Count</th>
      <th>AirPlay Spins</th>
      <th>SiriusXM Spins</th>
      <th>Deezer Playlist Count</th>
      <th>Deezer Playlist Reach</th>
      <th>Amazon Playlist Count</th>
      <th>Pandora Streams</th>
      <th>Pandora Track Stations</th>
      <th>Soundcloud Streams</th>
      <th>Shazam Counts</th>
      <th>TIDAL Popularity</th>
      <th>Explicit Track</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>MILLION DOLLAR BABY</td>
      <td>Million Dollar Baby - Single</td>
      <td>Tommy Richman</td>
      <td>4/26/2024</td>
      <td>QM24S2402528</td>
      <td>1</td>
      <td>725.4</td>
      <td>390,470,936</td>
      <td>30,716</td>
      <td>196,631,588</td>
      <td>92.0</td>
      <td>84,274,754</td>
      <td>1,713,126</td>
      <td>5,767,700</td>
      <td>651,565,900</td>
      <td>5,332,281,936</td>
      <td>150,597,040</td>
      <td>210.0</td>
      <td>40,975</td>
      <td>684</td>
      <td>62.0</td>
      <td>17,598,718</td>
      <td>114.0</td>
      <td>18,004,655</td>
      <td>22,931</td>
      <td>4,818,457</td>
      <td>2,669,262</td>
      <td>NaN</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Not Like Us</td>
      <td>Not Like Us</td>
      <td>Kendrick Lamar</td>
      <td>05/04/2024</td>
      <td>USUG12400910</td>
      <td>2</td>
      <td>545.9</td>
      <td>323,703,884</td>
      <td>28,113</td>
      <td>174,597,137</td>
      <td>92.0</td>
      <td>116,347,040</td>
      <td>3,486,739</td>
      <td>674,700</td>
      <td>35,223,547</td>
      <td>208,339,025</td>
      <td>156,380,351</td>
      <td>188.0</td>
      <td>40,778</td>
      <td>3</td>
      <td>67.0</td>
      <td>10,422,430</td>
      <td>111.0</td>
      <td>7,780,028</td>
      <td>28,444</td>
      <td>6,623,075</td>
      <td>1,118,279</td>
      <td>NaN</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>i like the way you kiss me</td>
      <td>I like the way you kiss me</td>
      <td>Artemas</td>
      <td>3/19/2024</td>
      <td>QZJ842400387</td>
      <td>3</td>
      <td>538.4</td>
      <td>601,309,283</td>
      <td>54,331</td>
      <td>211,607,669</td>
      <td>92.0</td>
      <td>122,599,116</td>
      <td>2,228,730</td>
      <td>3,025,400</td>
      <td>275,154,237</td>
      <td>3,369,120,610</td>
      <td>373,784,955</td>
      <td>190.0</td>
      <td>74,333</td>
      <td>536</td>
      <td>136.0</td>
      <td>36,321,847</td>
      <td>172.0</td>
      <td>5,022,621</td>
      <td>5,639</td>
      <td>7,208,651</td>
      <td>5,285,340</td>
      <td>NaN</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Flowers</td>
      <td>Flowers - Single</td>
      <td>Miley Cyrus</td>
      <td>01/12/2023</td>
      <td>USSM12209777</td>
      <td>4</td>
      <td>444.9</td>
      <td>2,031,280,633</td>
      <td>269,802</td>
      <td>136,569,078</td>
      <td>85.0</td>
      <td>1,096,100,899</td>
      <td>10,629,796</td>
      <td>7,189,811</td>
      <td>1,078,757,968</td>
      <td>14,603,725,994</td>
      <td>3,351,188,582</td>
      <td>394.0</td>
      <td>1,474,799</td>
      <td>2,182</td>
      <td>264.0</td>
      <td>24,684,248</td>
      <td>210.0</td>
      <td>190,260,277</td>
      <td>203,384</td>
      <td>NaN</td>
      <td>11,822,942</td>
      <td>NaN</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Houdini</td>
      <td>Houdini</td>
      <td>Eminem</td>
      <td>5/31/2024</td>
      <td>USUG12403398</td>
      <td>5</td>
      <td>423.3</td>
      <td>107,034,922</td>
      <td>7,223</td>
      <td>151,469,874</td>
      <td>88.0</td>
      <td>77,373,957</td>
      <td>3,670,188</td>
      <td>16,400</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>112,763,851</td>
      <td>182.0</td>
      <td>12,185</td>
      <td>1</td>
      <td>82.0</td>
      <td>17,660,624</td>
      <td>105.0</td>
      <td>4,493,884</td>
      <td>7,006</td>
      <td>207,179</td>
      <td>457,017</td>
      <td>NaN</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
print(df.info())
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 4600 entries, 0 to 4599
    Data columns (total 29 columns):
     #   Column                      Non-Null Count  Dtype  
    ---  ------                      --------------  -----  
     0   Track                       4600 non-null   object 
     1   Album Name                  4600 non-null   object 
     2   Artist                      4595 non-null   object 
     3   Release Date                4600 non-null   object 
     4   ISRC                        4600 non-null   object 
     5   All Time Rank               4600 non-null   object 
     6   Track Score                 4600 non-null   float64
     7   Spotify Streams             4487 non-null   object 
     8   Spotify Playlist Count      4530 non-null   object 
     9   Spotify Playlist Reach      4528 non-null   object 
     10  Spotify Popularity          3796 non-null   float64
     11  YouTube Views               4292 non-null   object 
     12  YouTube Likes               4285 non-null   object 
     13  TikTok Posts                3427 non-null   object 
     14  TikTok Likes                3620 non-null   object 
     15  TikTok Views                3619 non-null   object 
     16  YouTube Playlist Reach      3591 non-null   object 
     17  Apple Music Playlist Count  4039 non-null   float64
     18  AirPlay Spins               4102 non-null   object 
     19  SiriusXM Spins              2477 non-null   object 
     20  Deezer Playlist Count       3679 non-null   float64
     21  Deezer Playlist Reach       3672 non-null   object 
     22  Amazon Playlist Count       3545 non-null   float64
     23  Pandora Streams             3494 non-null   object 
     24  Pandora Track Stations      3332 non-null   object 
     25  Soundcloud Streams          1267 non-null   object 
     26  Shazam Counts               4023 non-null   object 
     27  TIDAL Popularity            0 non-null      float64
     28  Explicit Track              4600 non-null   int64  
    dtypes: float64(6), int64(1), object(22)
    memory usage: 1.0+ MB
    None



```python
df.drop(columns=['Soundcloud Streams','TIDAL Popularity'], axis=1,inplace=True)
```


```python
df = df.map(lambda x: x.replace(',','') if isinstance(x,str) else x)
```


```python
for x in df.columns[6:]:
    df[x] = pd.to_numeric(df[x])
    print(df[x].dtype)
```

    float64
    float64
    float64
    float64
    float64
    float64
    float64
    float64
    float64
    float64
    float64
    float64
    float64
    float64
    float64
    float64
    float64
    float64
    float64
    float64
    int64



```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 4600 entries, 0 to 4599
    Data columns (total 27 columns):
     #   Column                      Non-Null Count  Dtype  
    ---  ------                      --------------  -----  
     0   Track                       4600 non-null   object 
     1   Album Name                  4600 non-null   object 
     2   Artist                      4595 non-null   object 
     3   Release Date                4600 non-null   object 
     4   ISRC                        4600 non-null   object 
     5   All Time Rank               4600 non-null   object 
     6   Track Score                 4600 non-null   float64
     7   Spotify Streams             4487 non-null   float64
     8   Spotify Playlist Count      4530 non-null   float64
     9   Spotify Playlist Reach      4528 non-null   float64
     10  Spotify Popularity          3796 non-null   float64
     11  YouTube Views               4292 non-null   float64
     12  YouTube Likes               4285 non-null   float64
     13  TikTok Posts                3427 non-null   float64
     14  TikTok Likes                3620 non-null   float64
     15  TikTok Views                3619 non-null   float64
     16  YouTube Playlist Reach      3591 non-null   float64
     17  Apple Music Playlist Count  4039 non-null   float64
     18  AirPlay Spins               4102 non-null   float64
     19  SiriusXM Spins              2477 non-null   float64
     20  Deezer Playlist Count       3679 non-null   float64
     21  Deezer Playlist Reach       3672 non-null   float64
     22  Amazon Playlist Count       3545 non-null   float64
     23  Pandora Streams             3494 non-null   float64
     24  Pandora Track Stations      3332 non-null   float64
     25  Shazam Counts               4023 non-null   float64
     26  Explicit Track              4600 non-null   int64  
    dtypes: float64(20), int64(1), object(6)
    memory usage: 970.4+ KB



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
      <th>Track</th>
      <th>Album Name</th>
      <th>Artist</th>
      <th>Release Date</th>
      <th>ISRC</th>
      <th>All Time Rank</th>
      <th>Track Score</th>
      <th>Spotify Streams</th>
      <th>Spotify Playlist Count</th>
      <th>Spotify Playlist Reach</th>
      <th>Spotify Popularity</th>
      <th>YouTube Views</th>
      <th>YouTube Likes</th>
      <th>TikTok Posts</th>
      <th>TikTok Likes</th>
      <th>TikTok Views</th>
      <th>YouTube Playlist Reach</th>
      <th>Apple Music Playlist Count</th>
      <th>AirPlay Spins</th>
      <th>SiriusXM Spins</th>
      <th>Deezer Playlist Count</th>
      <th>Deezer Playlist Reach</th>
      <th>Amazon Playlist Count</th>
      <th>Pandora Streams</th>
      <th>Pandora Track Stations</th>
      <th>Shazam Counts</th>
      <th>Explicit Track</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>MILLION DOLLAR BABY</td>
      <td>Million Dollar Baby - Single</td>
      <td>Tommy Richman</td>
      <td>4/26/2024</td>
      <td>QM24S2402528</td>
      <td>1</td>
      <td>725.4</td>
      <td>3.904709e+08</td>
      <td>30716.0</td>
      <td>196631588.0</td>
      <td>92.0</td>
      <td>8.427475e+07</td>
      <td>1713126.0</td>
      <td>5767700.0</td>
      <td>6.515659e+08</td>
      <td>5.332282e+09</td>
      <td>1.505970e+08</td>
      <td>210.0</td>
      <td>40975.0</td>
      <td>684.0</td>
      <td>62.0</td>
      <td>17598718.0</td>
      <td>114.0</td>
      <td>18004655.0</td>
      <td>22931.0</td>
      <td>2669262.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Not Like Us</td>
      <td>Not Like Us</td>
      <td>Kendrick Lamar</td>
      <td>05/04/2024</td>
      <td>USUG12400910</td>
      <td>2</td>
      <td>545.9</td>
      <td>3.237039e+08</td>
      <td>28113.0</td>
      <td>174597137.0</td>
      <td>92.0</td>
      <td>1.163470e+08</td>
      <td>3486739.0</td>
      <td>674700.0</td>
      <td>3.522355e+07</td>
      <td>2.083390e+08</td>
      <td>1.563804e+08</td>
      <td>188.0</td>
      <td>40778.0</td>
      <td>3.0</td>
      <td>67.0</td>
      <td>10422430.0</td>
      <td>111.0</td>
      <td>7780028.0</td>
      <td>28444.0</td>
      <td>1118279.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>i like the way you kiss me</td>
      <td>I like the way you kiss me</td>
      <td>Artemas</td>
      <td>3/19/2024</td>
      <td>QZJ842400387</td>
      <td>3</td>
      <td>538.4</td>
      <td>6.013093e+08</td>
      <td>54331.0</td>
      <td>211607669.0</td>
      <td>92.0</td>
      <td>1.225991e+08</td>
      <td>2228730.0</td>
      <td>3025400.0</td>
      <td>2.751542e+08</td>
      <td>3.369121e+09</td>
      <td>3.737850e+08</td>
      <td>190.0</td>
      <td>74333.0</td>
      <td>536.0</td>
      <td>136.0</td>
      <td>36321847.0</td>
      <td>172.0</td>
      <td>5022621.0</td>
      <td>5639.0</td>
      <td>5285340.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Flowers</td>
      <td>Flowers - Single</td>
      <td>Miley Cyrus</td>
      <td>01/12/2023</td>
      <td>USSM12209777</td>
      <td>4</td>
      <td>444.9</td>
      <td>2.031281e+09</td>
      <td>269802.0</td>
      <td>136569078.0</td>
      <td>85.0</td>
      <td>1.096101e+09</td>
      <td>10629796.0</td>
      <td>7189811.0</td>
      <td>1.078758e+09</td>
      <td>1.460373e+10</td>
      <td>3.351189e+09</td>
      <td>394.0</td>
      <td>1474799.0</td>
      <td>2182.0</td>
      <td>264.0</td>
      <td>24684248.0</td>
      <td>210.0</td>
      <td>190260277.0</td>
      <td>203384.0</td>
      <td>11822942.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Houdini</td>
      <td>Houdini</td>
      <td>Eminem</td>
      <td>5/31/2024</td>
      <td>USUG12403398</td>
      <td>5</td>
      <td>423.3</td>
      <td>1.070349e+08</td>
      <td>7223.0</td>
      <td>151469874.0</td>
      <td>88.0</td>
      <td>7.737396e+07</td>
      <td>3670188.0</td>
      <td>16400.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.127639e+08</td>
      <td>182.0</td>
      <td>12185.0</td>
      <td>1.0</td>
      <td>82.0</td>
      <td>17660624.0</td>
      <td>105.0</td>
      <td>4493884.0</td>
      <td>7006.0</td>
      <td>457017.0</td>
      <td>1</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>4595</th>
      <td>For the Last Time</td>
      <td>For the Last Time</td>
      <td>$uicideboy$</td>
      <td>09/05/2017</td>
      <td>QM8DG1703420</td>
      <td>4585</td>
      <td>19.4</td>
      <td>3.050500e+08</td>
      <td>65770.0</td>
      <td>5103054.0</td>
      <td>71.0</td>
      <td>1.492477e+08</td>
      <td>1397590.0</td>
      <td>48370.0</td>
      <td>2.020200e+07</td>
      <td>1.439344e+08</td>
      <td>5.301600e+04</td>
      <td>3.0</td>
      <td>6.0</td>
      <td>NaN</td>
      <td>2.0</td>
      <td>14217.0</td>
      <td>NaN</td>
      <td>20104066.0</td>
      <td>13184.0</td>
      <td>656337.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4596</th>
      <td>Dil Meri Na Sune</td>
      <td>Dil Meri Na Sune (From "Genius")</td>
      <td>Atif Aslam</td>
      <td>7/27/2018</td>
      <td>INT101800122</td>
      <td>4575</td>
      <td>19.4</td>
      <td>5.228236e+07</td>
      <td>4602.0</td>
      <td>1449767.0</td>
      <td>56.0</td>
      <td>9.439202e+08</td>
      <td>5347766.0</td>
      <td>NaN</td>
      <td>1.721000e+03</td>
      <td>1.993500e+04</td>
      <td>2.497305e+07</td>
      <td>1.0</td>
      <td>412.0</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>927.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>193590.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4597</th>
      <td>Grace (feat. 42 Dugg)</td>
      <td>My Turn</td>
      <td>Lil Baby</td>
      <td>2/28/2020</td>
      <td>USUG12000043</td>
      <td>4571</td>
      <td>19.4</td>
      <td>1.899727e+08</td>
      <td>72066.0</td>
      <td>6704802.0</td>
      <td>65.0</td>
      <td>2.010273e+08</td>
      <td>1081402.0</td>
      <td>7596.0</td>
      <td>5.288677e+06</td>
      <td>3.684900e+07</td>
      <td>2.925315e+07</td>
      <td>19.0</td>
      <td>204.0</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>74.0</td>
      <td>6.0</td>
      <td>84426740.0</td>
      <td>28999.0</td>
      <td>1135998.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4598</th>
      <td>Nashe Si Chadh Gayi</td>
      <td>November Top 10 Songs</td>
      <td>Arijit Singh</td>
      <td>11/08/2016</td>
      <td>INY091600067</td>
      <td>4591</td>
      <td>19.4</td>
      <td>1.454670e+08</td>
      <td>14037.0</td>
      <td>7387064.0</td>
      <td>66.0</td>
      <td>1.118595e+09</td>
      <td>3868828.0</td>
      <td>11433.0</td>
      <td>2.534831e+06</td>
      <td>3.775730e+07</td>
      <td>2.515052e+07</td>
      <td>1.0</td>
      <td>1200.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7.0</td>
      <td>6817840.0</td>
      <td>NaN</td>
      <td>448292.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4599</th>
      <td>Me Acostumbre (feat. Bad Bunny)</td>
      <td>Me Acostumbre (feat. Bad Bunny)</td>
      <td>Arcï¿½ï¿½</td>
      <td>04/11/2017</td>
      <td>USB271700107</td>
      <td>4593</td>
      <td>19.4</td>
      <td>2.557407e+08</td>
      <td>32138.0</td>
      <td>14066526.0</td>
      <td>64.0</td>
      <td>8.663008e+08</td>
      <td>3826829.0</td>
      <td>78000.0</td>
      <td>1.005546e+07</td>
      <td>7.019639e+07</td>
      <td>1.758314e+08</td>
      <td>11.0</td>
      <td>2083.0</td>
      <td>NaN</td>
      <td>4.0</td>
      <td>127479.0</td>
      <td>4.0</td>
      <td>69006739.0</td>
      <td>11320.0</td>
      <td>767006.0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>4600 rows × 27 columns</p>
</div>




```python
df['Track'] = df['Track'].apply(lambda x: x[:20] + '...' if len(x) > 30 else x)
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
      <th>Track</th>
      <th>Album Name</th>
      <th>Artist</th>
      <th>Release Date</th>
      <th>ISRC</th>
      <th>All Time Rank</th>
      <th>Track Score</th>
      <th>Spotify Streams</th>
      <th>Spotify Playlist Count</th>
      <th>Spotify Playlist Reach</th>
      <th>Spotify Popularity</th>
      <th>YouTube Views</th>
      <th>YouTube Likes</th>
      <th>TikTok Posts</th>
      <th>TikTok Likes</th>
      <th>TikTok Views</th>
      <th>YouTube Playlist Reach</th>
      <th>Apple Music Playlist Count</th>
      <th>AirPlay Spins</th>
      <th>SiriusXM Spins</th>
      <th>Deezer Playlist Count</th>
      <th>Deezer Playlist Reach</th>
      <th>Amazon Playlist Count</th>
      <th>Pandora Streams</th>
      <th>Pandora Track Stations</th>
      <th>Shazam Counts</th>
      <th>Explicit Track</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>MILLION DOLLAR BABY</td>
      <td>Million Dollar Baby - Single</td>
      <td>Tommy Richman</td>
      <td>4/26/2024</td>
      <td>QM24S2402528</td>
      <td>1</td>
      <td>725.4</td>
      <td>3.904709e+08</td>
      <td>30716.0</td>
      <td>196631588.0</td>
      <td>92.0</td>
      <td>8.427475e+07</td>
      <td>1713126.0</td>
      <td>5767700.0</td>
      <td>6.515659e+08</td>
      <td>5.332282e+09</td>
      <td>1.505970e+08</td>
      <td>210.0</td>
      <td>40975.0</td>
      <td>684.0</td>
      <td>62.0</td>
      <td>17598718.0</td>
      <td>114.0</td>
      <td>18004655.0</td>
      <td>22931.0</td>
      <td>2669262.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Not Like Us</td>
      <td>Not Like Us</td>
      <td>Kendrick Lamar</td>
      <td>05/04/2024</td>
      <td>USUG12400910</td>
      <td>2</td>
      <td>545.9</td>
      <td>3.237039e+08</td>
      <td>28113.0</td>
      <td>174597137.0</td>
      <td>92.0</td>
      <td>1.163470e+08</td>
      <td>3486739.0</td>
      <td>674700.0</td>
      <td>3.522355e+07</td>
      <td>2.083390e+08</td>
      <td>1.563804e+08</td>
      <td>188.0</td>
      <td>40778.0</td>
      <td>3.0</td>
      <td>67.0</td>
      <td>10422430.0</td>
      <td>111.0</td>
      <td>7780028.0</td>
      <td>28444.0</td>
      <td>1118279.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>i like the way you kiss me</td>
      <td>I like the way you kiss me</td>
      <td>Artemas</td>
      <td>3/19/2024</td>
      <td>QZJ842400387</td>
      <td>3</td>
      <td>538.4</td>
      <td>6.013093e+08</td>
      <td>54331.0</td>
      <td>211607669.0</td>
      <td>92.0</td>
      <td>1.225991e+08</td>
      <td>2228730.0</td>
      <td>3025400.0</td>
      <td>2.751542e+08</td>
      <td>3.369121e+09</td>
      <td>3.737850e+08</td>
      <td>190.0</td>
      <td>74333.0</td>
      <td>536.0</td>
      <td>136.0</td>
      <td>36321847.0</td>
      <td>172.0</td>
      <td>5022621.0</td>
      <td>5639.0</td>
      <td>5285340.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Flowers</td>
      <td>Flowers - Single</td>
      <td>Miley Cyrus</td>
      <td>01/12/2023</td>
      <td>USSM12209777</td>
      <td>4</td>
      <td>444.9</td>
      <td>2.031281e+09</td>
      <td>269802.0</td>
      <td>136569078.0</td>
      <td>85.0</td>
      <td>1.096101e+09</td>
      <td>10629796.0</td>
      <td>7189811.0</td>
      <td>1.078758e+09</td>
      <td>1.460373e+10</td>
      <td>3.351189e+09</td>
      <td>394.0</td>
      <td>1474799.0</td>
      <td>2182.0</td>
      <td>264.0</td>
      <td>24684248.0</td>
      <td>210.0</td>
      <td>190260277.0</td>
      <td>203384.0</td>
      <td>11822942.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Houdini</td>
      <td>Houdini</td>
      <td>Eminem</td>
      <td>5/31/2024</td>
      <td>USUG12403398</td>
      <td>5</td>
      <td>423.3</td>
      <td>1.070349e+08</td>
      <td>7223.0</td>
      <td>151469874.0</td>
      <td>88.0</td>
      <td>7.737396e+07</td>
      <td>3670188.0</td>
      <td>16400.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.127639e+08</td>
      <td>182.0</td>
      <td>12185.0</td>
      <td>1.0</td>
      <td>82.0</td>
      <td>17660624.0</td>
      <td>105.0</td>
      <td>4493884.0</td>
      <td>7006.0</td>
      <td>457017.0</td>
      <td>1</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>4595</th>
      <td>For the Last Time</td>
      <td>For the Last Time</td>
      <td>$uicideboy$</td>
      <td>09/05/2017</td>
      <td>QM8DG1703420</td>
      <td>4585</td>
      <td>19.4</td>
      <td>3.050500e+08</td>
      <td>65770.0</td>
      <td>5103054.0</td>
      <td>71.0</td>
      <td>1.492477e+08</td>
      <td>1397590.0</td>
      <td>48370.0</td>
      <td>2.020200e+07</td>
      <td>1.439344e+08</td>
      <td>5.301600e+04</td>
      <td>3.0</td>
      <td>6.0</td>
      <td>NaN</td>
      <td>2.0</td>
      <td>14217.0</td>
      <td>NaN</td>
      <td>20104066.0</td>
      <td>13184.0</td>
      <td>656337.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4596</th>
      <td>Dil Meri Na Sune</td>
      <td>Dil Meri Na Sune (From "Genius")</td>
      <td>Atif Aslam</td>
      <td>7/27/2018</td>
      <td>INT101800122</td>
      <td>4575</td>
      <td>19.4</td>
      <td>5.228236e+07</td>
      <td>4602.0</td>
      <td>1449767.0</td>
      <td>56.0</td>
      <td>9.439202e+08</td>
      <td>5347766.0</td>
      <td>NaN</td>
      <td>1.721000e+03</td>
      <td>1.993500e+04</td>
      <td>2.497305e+07</td>
      <td>1.0</td>
      <td>412.0</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>927.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>193590.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4597</th>
      <td>Grace (feat. 42 Dugg)</td>
      <td>My Turn</td>
      <td>Lil Baby</td>
      <td>2/28/2020</td>
      <td>USUG12000043</td>
      <td>4571</td>
      <td>19.4</td>
      <td>1.899727e+08</td>
      <td>72066.0</td>
      <td>6704802.0</td>
      <td>65.0</td>
      <td>2.010273e+08</td>
      <td>1081402.0</td>
      <td>7596.0</td>
      <td>5.288677e+06</td>
      <td>3.684900e+07</td>
      <td>2.925315e+07</td>
      <td>19.0</td>
      <td>204.0</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>74.0</td>
      <td>6.0</td>
      <td>84426740.0</td>
      <td>28999.0</td>
      <td>1135998.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4598</th>
      <td>Nashe Si Chadh Gayi</td>
      <td>November Top 10 Songs</td>
      <td>Arijit Singh</td>
      <td>11/08/2016</td>
      <td>INY091600067</td>
      <td>4591</td>
      <td>19.4</td>
      <td>1.454670e+08</td>
      <td>14037.0</td>
      <td>7387064.0</td>
      <td>66.0</td>
      <td>1.118595e+09</td>
      <td>3868828.0</td>
      <td>11433.0</td>
      <td>2.534831e+06</td>
      <td>3.775730e+07</td>
      <td>2.515052e+07</td>
      <td>1.0</td>
      <td>1200.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7.0</td>
      <td>6817840.0</td>
      <td>NaN</td>
      <td>448292.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4599</th>
      <td>Me Acostumbre (feat....</td>
      <td>Me Acostumbre (feat. Bad Bunny)</td>
      <td>Arcï¿½ï¿½</td>
      <td>04/11/2017</td>
      <td>USB271700107</td>
      <td>4593</td>
      <td>19.4</td>
      <td>2.557407e+08</td>
      <td>32138.0</td>
      <td>14066526.0</td>
      <td>64.0</td>
      <td>8.663008e+08</td>
      <td>3826829.0</td>
      <td>78000.0</td>
      <td>1.005546e+07</td>
      <td>7.019639e+07</td>
      <td>1.758314e+08</td>
      <td>11.0</td>
      <td>2083.0</td>
      <td>NaN</td>
      <td>4.0</td>
      <td>127479.0</td>
      <td>4.0</td>
      <td>69006739.0</td>
      <td>11320.0</td>
      <td>767006.0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>4600 rows × 27 columns</p>
</div>




```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 4600 entries, 0 to 4599
    Data columns (total 27 columns):
     #   Column                      Non-Null Count  Dtype  
    ---  ------                      --------------  -----  
     0   Track                       4600 non-null   object 
     1   Album Name                  4600 non-null   object 
     2   Artist                      4595 non-null   object 
     3   Release Date                4600 non-null   object 
     4   ISRC                        4600 non-null   object 
     5   All Time Rank               4600 non-null   object 
     6   Track Score                 4600 non-null   float64
     7   Spotify Streams             4487 non-null   float64
     8   Spotify Playlist Count      4530 non-null   float64
     9   Spotify Playlist Reach      4528 non-null   float64
     10  Spotify Popularity          3796 non-null   float64
     11  YouTube Views               4292 non-null   float64
     12  YouTube Likes               4285 non-null   float64
     13  TikTok Posts                3427 non-null   float64
     14  TikTok Likes                3620 non-null   float64
     15  TikTok Views                3619 non-null   float64
     16  YouTube Playlist Reach      3591 non-null   float64
     17  Apple Music Playlist Count  4039 non-null   float64
     18  AirPlay Spins               4102 non-null   float64
     19  SiriusXM Spins              2477 non-null   float64
     20  Deezer Playlist Count       3679 non-null   float64
     21  Deezer Playlist Reach       3672 non-null   float64
     22  Amazon Playlist Count       3545 non-null   float64
     23  Pandora Streams             3494 non-null   float64
     24  Pandora Track Stations      3332 non-null   float64
     25  Shazam Counts               4023 non-null   float64
     26  Explicit Track              4600 non-null   int64  
    dtypes: float64(20), int64(1), object(6)
    memory usage: 970.4+ KB



```python
transDf = df.set_index('Track').transpose()
```


```python
# def mape(x):
#     return 'Explicit' if x==1 else 'Not Explicit'
# df['Explicit Track'] = df['Explicit Track'].apply(mape)
# df = df.loc[df['Track'].shift(-1) != df['Track']]
```


```python
df['Release Date'] = pd.to_datetime(df['Release Date'],format='%m/%d/%Y')
df['year'] = df['Release Date'].dt.year
timedf = df.groupby('year')['Track'].size().reset_index()
fig1 =px.bar(timedf[timedf['year']>2005],x='year',y='Track')
fig1.update_layout(title_text = '<b>Number of songs released till 2024</b>',title_x=0.5)
```


<div>                            <div id="67c6321a-5e3a-4b9f-8a7a-3d62fcbea665" class="plotly-graph-div" style="height:525px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("67c6321a-5e3a-4b9f-8a7a-3d62fcbea665")) {                    Plotly.newPlot(                        "67c6321a-5e3a-4b9f-8a7a-3d62fcbea665",                        [{"alignmentgroup":"True","hovertemplate":"year=%{x}\u003cbr\u003eTrack=%{y}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"","marker":{"color":"#636efa","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"v","showlegend":false,"textposition":"auto","x":[2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024],"xaxis":"x","y":[3,5,11,15,27,51,47,55,70,96,123,213,249,296,360,408,694,1158,693],"yaxis":"y","type":"bar"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"year"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Track"}},"legend":{"tracegroupgap":0},"margin":{"t":60},"barmode":"relative","title":{"text":"\u003cb\u003eNumber of songs released till 2024\u003c\u002fb\u003e","x":0.5}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('67c6321a-5e3a-4b9f-8a7a-3d62fcbea665');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })                };                });            </script>        </div>



```python
dff =df.copy()
dff['Explicit Track'] = dff['Explicit Track'].replace({0: 'Not Explicit', 1: 'Explicit'})
pie = px.pie(dff,names='Explicit Track')
pie.update_layout(title_text='<b>Ratio of Explicit to UnExcplicit<b>',title_x=0.47)
```


<div>                            <div id="7653826d-5fbf-4b9c-8721-0b700bc00ee6" class="plotly-graph-div" style="height:525px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("7653826d-5fbf-4b9c-8721-0b700bc00ee6")) {                    Plotly.newPlot(                        "7653826d-5fbf-4b9c-8721-0b700bc00ee6",                        [{"domain":{"x":[0.0,1.0],"y":[0.0,1.0]},"hovertemplate":"Explicit Track=%{label}\u003cextra\u003e\u003c\u002fextra\u003e","labels":["Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit"],"legendgroup":"","name":"","showlegend":true,"type":"pie"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"legend":{"tracegroupgap":0},"margin":{"t":60},"title":{"text":"\u003cb\u003eRatio of Explicit to UnExcplicit\u003cb\u003e","x":0.47}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('7653826d-5fbf-4b9c-8721-0b700bc00ee6');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })                };                });            </script>        </div>



```python
spstream = df.copy()
spstream.sort_values(by='Spotify Streams',ascending =False,inplace = True)
spstream =spstream.loc[spstream['Track'].shift() != spstream['Track']]
fig = px.bar(spstream.head(20)[::-1],y = 'Track',x='Spotify Streams',color = 'Explicit Track',
             height=1000,color_continuous_scale=['#486fd9','#d94848'])
fig.update_layout(showlegend=True,title_text='<b>Top 20 streamed songs</b><br><sub>Accordng to spotify</sub><br>', title_x=0.5)
fig
```


<div>                            <div id="7ca41b27-6d47-4206-bb81-5c289d01d17b" class="plotly-graph-div" style="height:1000px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("7ca41b27-6d47-4206-bb81-5c289d01d17b")) {                    Plotly.newPlot(                        "7ca41b27-6d47-4206-bb81-5c289d01d17b",                        [{"alignmentgroup":"True","hovertemplate":"Spotify Streams=%{x}\u003cbr\u003eTrack=%{y}\u003cbr\u003eExplicit Track=%{marker.color}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"","marker":{"color":[0,0,0,0,0,0,0,1,0,0,0,0,0,0,1,0,0,0,0,0],"coloraxis":"coloraxis","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"textposition":"auto","x":[2596644721.0,2607121959.0,2708151499.0,2738293818.0,2830962669.0,2880536961.0,2916412507.0,2960046642.0,2969999682.0,2996181078.0,3006226762.0,3071214106.0,3107100349.0,3192204066.0,3291262413.0,3301814535.0,3358704125.0,3427498835.0,3909458734.0,4281468720.0],"xaxis":"x","y":["Something Just Like This","Don't Start Now","Watermelon Sugar","Se\u00ef\u00bf\u00bd\u00ef\u00bf\u00bdo","Say You Won't Let Go","Closer","Sweater Weather","rockstar (feat. 21 Savage)","Perfect","Heat Waves","Believer","Dance Monkey","STAY (with Justin Bieber)","One Dance","Starboy","As It Was","Sunflower - Spider-M...","Someone You Loved","Shape of You","Blinding Lights"],"yaxis":"y","type":"bar"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"Spotify Streams"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Track"}},"coloraxis":{"colorbar":{"title":{"text":"Explicit Track"}},"colorscale":[[0.0,"#486fd9"],[1.0,"#d94848"]]},"legend":{"tracegroupgap":0},"margin":{"t":60},"barmode":"relative","height":1000,"title":{"text":"\u003cb\u003eTop 20 streamed songs\u003c\u002fb\u003e\u003cbr\u003e\u003csub\u003eAccordng to spotify\u003c\u002fsub\u003e\u003cbr\u003e","x":0.5},"showlegend":true},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('7ca41b27-6d47-4206-bb81-5c289d01d17b');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })                };                });            </script>        </div>



```python
youstream = df.copy()
youstream.sort_values(by='YouTube Views',ascending =False,inplace = True)
youstream =youstream.loc[youstream['Track'].shift() != youstream['Track']]
fig = px.bar(youstream.head(20)[::-1],y = 'Track',x='YouTube Views',color = 'Explicit Track',
             height=700,width=1000,color_continuous_scale=['#486fd9','#d94848'],
            labels=['Exceplicit','Not'],hover_data=['YouTube Likes'])
fig. update_coloraxes(showscale=True)
fig.update_layout(showlegend=True,title_text='<b>Top 20 streamed songs</b><br><sub>Accordng to youtube</sub><br>', title_x=0.55)

fig
```


<div>                            <div id="c66cdce1-3922-483e-a624-93599f799c50" class="plotly-graph-div" style="height:700px; width:1000px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("c66cdce1-3922-483e-a624-93599f799c50")) {                    Plotly.newPlot(                        "c66cdce1-3922-483e-a624-93599f799c50",                        [{"alignmentgroup":"True","customdata":[[11455880.0],[23606907.0],[18144051.0],[22091228.0],[16983201.0],[11589558.0],[19822950.0],[22464625.0],[42240881.0],[34925612.0],[20218394.0],[20305999.0],[24960395.0],[31460015.0],[43663933.0],[39632673.0],[16785489.0],[45770887.0],[61799279.0],[48757673.0]],"hovertemplate":"YouTube Views=%{x}\u003cbr\u003eTrack=%{y}\u003cbr\u003eYouTube Likes=%{customdata[0]}\u003cbr\u003eExplicit Track=%{marker.color}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"","marker":{"color":[0,0,0,0,1,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0],"coloraxis":"coloraxis","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"textposition":"auto","x":[3982526341.0,4062829308.0,4069201723.0,4086121880.0,4151880976.0,4168791151.0,4386626671.0,4467507940.0,4495059988.0,4542997940.0,5004189392.0,5049428366.0,5644117304.0,5849588091.0,6335989683.0,6473698897.0,6736502312.0,8013997219.0,9537135721.0,16322756555.0],"xaxis":"x","y":["I Am Your Gummy Bear...","Waka Waka (This Time...","Counting stars","Closer","Sugar","Let It Go - From \"Fr...","Roar","Sorry","Believer","Faded","Dame Tu Cosita","Axel F","Uptown Funk","Gangnam Style (\u00ef\u00bf\u00bd\u00ef\u00bf...","See You Again (feat....","Perfect","bathroom floor","Shape of You","Despacito","Baby Shark"],"yaxis":"y","type":"bar"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"YouTube Views"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Track"}},"coloraxis":{"colorbar":{"title":{"text":"Explicit Track"}},"colorscale":[[0.0,"#486fd9"],[1.0,"#d94848"]],"showscale":true},"legend":{"tracegroupgap":0},"margin":{"t":60},"barmode":"relative","height":700,"width":1000,"title":{"text":"\u003cb\u003eTop 20 streamed songs\u003c\u002fb\u003e\u003cbr\u003e\u003csub\u003eAccordng to youtube\u003c\u002fsub\u003e\u003cbr\u003e","x":0.55},"showlegend":true},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('c66cdce1-3922-483e-a624-93599f799c50');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })                };                });            </script>        </div>



```python
tikstream = df.copy()
tikstream.sort_values(by='TikTok Views',ascending =False,inplace = True)
tikstream =tikstream.loc[tikstream['Track'].shift() != tikstream['Track']]
fig = px.bar(tikstream.head(20)[::-1],y = 'Track',x='TikTok Views',color = 'Explicit Track',
             height=700,width=1000,color_continuous_scale=['#486fd9','#d94848'],
            labels=['Exceplicit','Not'])
fig. update_coloraxes(showscale=True)
fig.update_layout(showlegend=True,title_text='<b>Top 20 streamed songs</b><br><sub>Accordng to Tiktok</sub><br>', title_x=0.55)

fig
```


<div>                            <div id="076724f5-b17c-40a7-b5bc-da2c9a196b4a" class="plotly-graph-div" style="height:700px; width:1000px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("076724f5-b17c-40a7-b5bc-da2c9a196b4a")) {                    Plotly.newPlot(                        "076724f5-b17c-40a7-b5bc-da2c9a196b4a",                        [{"alignmentgroup":"True","hovertemplate":"TikTok Views=%{x}\u003cbr\u003eTrack=%{y}\u003cbr\u003eExplicit Track=%{marker.color}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"","marker":{"color":[1,0,0,1,0,0,0,0,0,0,1,1,0,0,0,0,0,1,0,0],"coloraxis":"coloraxis","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"textposition":"auto","x":[17759263793.0,18153723096.0,18216656774.0,18762950045.0,21650351404.0,22086963233.0,22241102823.0,22817879063.0,22902123485.0,22918660728.0,23712377029.0,23933379729.0,28032695489.0,33201904006.0,33359398879.0,33870791454.0,38373644011.0,61088562861.0,214843956590.0,233232311463.0],"xaxis":"x","y":["Ginseng Strip 2002","Beggin'","Calm Down","SugarCrash!","Get You The Moon (feat. Sn\u00ef\u00bf\u00bd\u00ef","Sunroof","Infinity","Astronaut In The Ocean","Steven Universe","Beat Automotivo Tan ...","STAY (with Justin Bieber)","love nwantiti (ah ah ah)","Pieces","She Share Story","Spongebob","Aesthetic","Funny Song","Oh No","Love You So","Monkeys Spinning Monkeys"],"yaxis":"y","type":"bar"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"TikTok Views"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Track"}},"coloraxis":{"colorbar":{"title":{"text":"Explicit Track"}},"colorscale":[[0.0,"#486fd9"],[1.0,"#d94848"]],"showscale":true},"legend":{"tracegroupgap":0},"margin":{"t":60},"barmode":"relative","height":700,"width":1000,"title":{"text":"\u003cb\u003eTop 20 streamed songs\u003c\u002fb\u003e\u003cbr\u003e\u003csub\u003eAccordng to Tiktok\u003c\u002fsub\u003e\u003cbr\u003e","x":0.55},"showlegend":true},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('076724f5-b17c-40a7-b5bc-da2c9a196b4a');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })                };                });            </script>        </div>



```python
df['totalView'] = df['Spotify Streams'].fillna(0)+df['YouTube Views'].fillna(0)
```


```python
artistdf = df.groupby('Artist')
totalV = artistdf['Spotify Streams'].sum().reset_index()
totalV.sort_values(by='Spotify Streams',ascending=False,inplace = True)
```


```python
fig = px.bar(totalV.head(20)[::-1],y = 'Artist',x='Spotify Streams',
             height=700,width=1000,text_auto='Spotify Streams')
fig.update_layout(showlegend=True,title_text='<b>Top 20 most popular artists </b><br><sub>Accordng to spotify total streams</sub><br>', title_x=0.55)
fig
```


<div>                            <div id="c741c751-32ab-40a2-8108-724d9e6f85c6" class="plotly-graph-div" style="height:700px; width:1000px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("c741c751-32ab-40a2-8108-724d9e6f85c6")) {                    Plotly.newPlot(                        "c741c751-32ab-40a2-8108-724d9e6f85c6",                        [{"alignmentgroup":"True","hovertemplate":"Spotify Streams=%{x}\u003cbr\u003eArtist=%{y}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"","marker":{"color":"#636efa","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"textposition":"auto","texttemplate":"%{x:Spotify Streams}","x":[15649310557.0,15856288522.0,16000309235.0,16225111478.0,16460200849.0,16647801287.0,16811833201.0,16923515048.0,17157121647.0,17353388340.0,18878880174.0,19729219749.0,22866685573.0,23464991696.0,24014900390.0,26137472958.0,34470771165.0,34962157577.0,36948540278.0,37054834425.0],"xaxis":"x","y":["Dua Lipa","Doja Cat","Maroon 5","Bruno Mars","Travis Scott","Justin Bieber","Billie Eilish","XXXTENTACION","Imagine Dragons","Harry Styles","Eminem","Olivia Rodrigo","MUSIC LAB JPN","Ariana Grande","Ed Sheeran","Post Malone","Taylor Swift","Drake","The Weeknd","Bad Bunny"],"yaxis":"y","type":"bar"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"Spotify Streams"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Artist"}},"legend":{"tracegroupgap":0},"margin":{"t":60},"barmode":"relative","height":700,"width":1000,"title":{"text":"\u003cb\u003eTop 20 most popular artists \u003c\u002fb\u003e\u003cbr\u003e\u003csub\u003eAccordng to spotify total streams\u003c\u002fsub\u003e\u003cbr\u003e","x":0.55},"showlegend":true},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('c741c751-32ab-40a2-8108-724d9e6f85c6');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })                };                });            </script>        </div>



```python
totalV = artistdf['YouTube Views'].sum().reset_index()
totalV.sort_values(by='YouTube Views',ascending=False,inplace = True)
fig = px.bar(totalV.head(20)[::-1],y = 'Artist',x='YouTube Views',
             height=700,width=1000,text_auto='YouTube Views')
fig.update_layout(showlegend=True,title_text='<b>Top 20 most popular artists </b><br><sub>Accordng to spotify total youtube views</sub><br>', title_x=0.55)
fig
```


<div>                            <div id="3787918f-4073-4312-b991-58c2419f2971" class="plotly-graph-div" style="height:700px; width:1000px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("3787918f-4073-4312-b991-58c2419f2971")) {                    Plotly.newPlot(                        "3787918f-4073-4312-b991-58c2419f2971",                        [{"alignmentgroup":"True","hovertemplate":"YouTube Views=%{x}\u003cbr\u003eArtist=%{y}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"","marker":{"color":"#636efa","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"textposition":"auto","texttemplate":"%{x:YouTube Views}","x":[13306912975.0,13465040833.0,14335943126.0,14920591066.0,14978888345.0,15018884324.0,15339748154.0,15655401215.0,15898755499.0,16372763663.0,16804170656.0,17173289711.0,17400636811.0,17465688790.0,18635601580.0,19336283617.0,19377678489.0,20140603852.0,25072757092.0,27273658604.0],"xaxis":"x","y":["Sia","Post Malone","Eminem","Luis Fonsi","Bruno Mars","BTS","Imagine Dragons","J Balvin","Shakira","Pinkfong","BLACKPINK","KAROL G","Katy Perry","Maroon 5","The Weeknd","Justin Bieber","Ariana Grande","Taylor Swift","Bad Bunny","Ed Sheeran"],"yaxis":"y","type":"bar"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"YouTube Views"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Artist"}},"legend":{"tracegroupgap":0},"margin":{"t":60},"barmode":"relative","height":700,"width":1000,"title":{"text":"\u003cb\u003eTop 20 most popular artists \u003c\u002fb\u003e\u003cbr\u003e\u003csub\u003eAccordng to spotify total youtube views\u003c\u002fsub\u003e\u003cbr\u003e","x":0.55},"showlegend":true},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('3787918f-4073-4312-b991-58c2419f2971');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })                };                });            </script>        </div>



```python
totalV = artistdf['totalView'].sum().reset_index()
totalV.sort_values(by='totalView',ascending=False,inplace = True)
fig = px.bar(totalV.head(20)[::-1],y = 'Artist',x='totalView',
             height=700,width=1000,text_auto='totalView')
fig.update_layout(showlegend=True,title_text='<b>Top 20 most popular artists </b><br><sub>Accordng to spotify streams & total youtube views</sub><br>', title_x=0.55)
fig
```


<div>                            <div id="27f429ea-6659-4498-aed2-9f2b4ceccddb" class="plotly-graph-div" style="height:700px; width:1000px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("27f429ea-6659-4498-aed2-9f2b4ceccddb")) {                    Plotly.newPlot(                        "27f429ea-6659-4498-aed2-9f2b4ceccddb",                        [{"alignmentgroup":"True","hovertemplate":"totalView=%{x}\u003cbr\u003eArtist=%{y}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"","marker":{"color":"#636efa","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"textposition":"auto","texttemplate":"%{x:totalView}","x":[24784125190.0,24789508862.0,25119741996.0,25320552780.0,26213409817.0,27048168865.0,27759287075.0,30937189387.0,31203999823.0,32496869801.0,33214823300.0,33465998025.0,35984084904.0,39602513791.0,42842670185.0,46496860577.0,51288558994.0,54611375017.0,55584141858.0,62127591517.0],"xaxis":"x","y":["XXXTENTACION","Dua Lipa","BTS","Katy Perry","J Balvin","Rihanna","Calvin Harris","KAROL G","Bruno Mars","Imagine Dragons","Eminem","Maroon 5","Justin Bieber","Post Malone","Ariana Grande","Drake","Ed Sheeran","Taylor Swift","The Weeknd","Bad Bunny"],"yaxis":"y","type":"bar"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"totalView"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Artist"}},"legend":{"tracegroupgap":0},"margin":{"t":60},"barmode":"relative","height":700,"width":1000,"title":{"text":"\u003cb\u003eTop 20 most popular artists \u003c\u002fb\u003e\u003cbr\u003e\u003csub\u003eAccordng to spotify streams & total youtube views\u003c\u002fsub\u003e\u003cbr\u003e","x":0.55},"showlegend":true},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('27f429ea-6659-4498-aed2-9f2b4ceccddb');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })                };                });            </script>        </div>



```python
artistEx = df.groupby('Artist')
artistEx = artistEx['Explicit Track'].sum().reset_index()
artistEx.sort_values(by='Explicit Track',ascending=False,inplace = True)
fig = px.bar(artistEx.head(20)[::-1],y = 'Artist',x='Explicit Track',
             height=700,width=1000,text_auto='totalView')
fig.update_layout(showlegend=True,title_text='<b>Top 20 most artists with explicit tracks</b>', title_x=0.55)
fig
```


<div>                            <div id="c7b7b391-5654-4b59-b9e8-bc950bd54b51" class="plotly-graph-div" style="height:700px; width:1000px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("c7b7b391-5654-4b59-b9e8-bc950bd54b51")) {                    Plotly.newPlot(                        "c7b7b391-5654-4b59-b9e8-bc950bd54b51",                        [{"alignmentgroup":"True","hovertemplate":"Explicit Track=%{x}\u003cbr\u003eArtist=%{y}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"","marker":{"color":"#636efa","pattern":{"shape":""}},"name":"","offsetgroup":"","orientation":"h","showlegend":false,"textposition":"auto","texttemplate":"%{x:totalView}","x":[13,13,13,13,14,14,14,14,15,16,16,16,18,19,20,21,22,29,42,54],"xaxis":"x","y":["XXXTENTACION","Rod Wave","Pop Smoke","Taylor Swift","KAROL G","MUSIC LAB JPN","Eminem","Gunna","Doja Cat","Post Malone","The Weeknd","21 Savage","Kendrick Lamar","Peso Pluma","Lil Baby","Juice WRLD","Future","Travis Scott","Bad Bunny","Drake"],"yaxis":"y","type":"bar"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"Explicit Track"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Artist"}},"legend":{"tracegroupgap":0},"margin":{"t":60},"barmode":"relative","height":700,"width":1000,"title":{"text":"\u003cb\u003eTop 20 most artists with explicit tracks\u003c\u002fb\u003e","x":0.55},"showlegend":true},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('c7b7b391-5654-4b59-b9e8-bc950bd54b51');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })                };                });            </script>        </div>



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
      <th>Track</th>
      <th>Album Name</th>
      <th>Artist</th>
      <th>Release Date</th>
      <th>ISRC</th>
      <th>All Time Rank</th>
      <th>Track Score</th>
      <th>Spotify Streams</th>
      <th>Spotify Playlist Count</th>
      <th>Spotify Playlist Reach</th>
      <th>Spotify Popularity</th>
      <th>YouTube Views</th>
      <th>YouTube Likes</th>
      <th>TikTok Posts</th>
      <th>TikTok Likes</th>
      <th>TikTok Views</th>
      <th>YouTube Playlist Reach</th>
      <th>Apple Music Playlist Count</th>
      <th>AirPlay Spins</th>
      <th>SiriusXM Spins</th>
      <th>Deezer Playlist Count</th>
      <th>Deezer Playlist Reach</th>
      <th>Amazon Playlist Count</th>
      <th>Pandora Streams</th>
      <th>Pandora Track Stations</th>
      <th>Shazam Counts</th>
      <th>Explicit Track</th>
      <th>year</th>
      <th>totalView</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>MILLION DOLLAR BABY</td>
      <td>Million Dollar Baby - Single</td>
      <td>Tommy Richman</td>
      <td>2024-04-26</td>
      <td>QM24S2402528</td>
      <td>1</td>
      <td>725.4</td>
      <td>3.904709e+08</td>
      <td>30716.0</td>
      <td>196631588.0</td>
      <td>92.0</td>
      <td>8.427475e+07</td>
      <td>1713126.0</td>
      <td>5767700.0</td>
      <td>6.515659e+08</td>
      <td>5.332282e+09</td>
      <td>1.505970e+08</td>
      <td>210.0</td>
      <td>40975.0</td>
      <td>684.0</td>
      <td>62.0</td>
      <td>17598718.0</td>
      <td>114.0</td>
      <td>18004655.0</td>
      <td>22931.0</td>
      <td>2669262.0</td>
      <td>0</td>
      <td>2024</td>
      <td>4.747457e+08</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Not Like Us</td>
      <td>Not Like Us</td>
      <td>Kendrick Lamar</td>
      <td>2024-05-04</td>
      <td>USUG12400910</td>
      <td>2</td>
      <td>545.9</td>
      <td>3.237039e+08</td>
      <td>28113.0</td>
      <td>174597137.0</td>
      <td>92.0</td>
      <td>1.163470e+08</td>
      <td>3486739.0</td>
      <td>674700.0</td>
      <td>3.522355e+07</td>
      <td>2.083390e+08</td>
      <td>1.563804e+08</td>
      <td>188.0</td>
      <td>40778.0</td>
      <td>3.0</td>
      <td>67.0</td>
      <td>10422430.0</td>
      <td>111.0</td>
      <td>7780028.0</td>
      <td>28444.0</td>
      <td>1118279.0</td>
      <td>1</td>
      <td>2024</td>
      <td>4.400509e+08</td>
    </tr>
    <tr>
      <th>2</th>
      <td>i like the way you kiss me</td>
      <td>I like the way you kiss me</td>
      <td>Artemas</td>
      <td>2024-03-19</td>
      <td>QZJ842400387</td>
      <td>3</td>
      <td>538.4</td>
      <td>6.013093e+08</td>
      <td>54331.0</td>
      <td>211607669.0</td>
      <td>92.0</td>
      <td>1.225991e+08</td>
      <td>2228730.0</td>
      <td>3025400.0</td>
      <td>2.751542e+08</td>
      <td>3.369121e+09</td>
      <td>3.737850e+08</td>
      <td>190.0</td>
      <td>74333.0</td>
      <td>536.0</td>
      <td>136.0</td>
      <td>36321847.0</td>
      <td>172.0</td>
      <td>5022621.0</td>
      <td>5639.0</td>
      <td>5285340.0</td>
      <td>0</td>
      <td>2024</td>
      <td>7.239084e+08</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Flowers</td>
      <td>Flowers - Single</td>
      <td>Miley Cyrus</td>
      <td>2023-01-12</td>
      <td>USSM12209777</td>
      <td>4</td>
      <td>444.9</td>
      <td>2.031281e+09</td>
      <td>269802.0</td>
      <td>136569078.0</td>
      <td>85.0</td>
      <td>1.096101e+09</td>
      <td>10629796.0</td>
      <td>7189811.0</td>
      <td>1.078758e+09</td>
      <td>1.460373e+10</td>
      <td>3.351189e+09</td>
      <td>394.0</td>
      <td>1474799.0</td>
      <td>2182.0</td>
      <td>264.0</td>
      <td>24684248.0</td>
      <td>210.0</td>
      <td>190260277.0</td>
      <td>203384.0</td>
      <td>11822942.0</td>
      <td>0</td>
      <td>2023</td>
      <td>3.127382e+09</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Houdini</td>
      <td>Houdini</td>
      <td>Eminem</td>
      <td>2024-05-31</td>
      <td>USUG12403398</td>
      <td>5</td>
      <td>423.3</td>
      <td>1.070349e+08</td>
      <td>7223.0</td>
      <td>151469874.0</td>
      <td>88.0</td>
      <td>7.737396e+07</td>
      <td>3670188.0</td>
      <td>16400.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.127639e+08</td>
      <td>182.0</td>
      <td>12185.0</td>
      <td>1.0</td>
      <td>82.0</td>
      <td>17660624.0</td>
      <td>105.0</td>
      <td>4493884.0</td>
      <td>7006.0</td>
      <td>457017.0</td>
      <td>1</td>
      <td>2024</td>
      <td>1.844089e+08</td>
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
      <th>4595</th>
      <td>For the Last Time</td>
      <td>For the Last Time</td>
      <td>$uicideboy$</td>
      <td>2017-09-05</td>
      <td>QM8DG1703420</td>
      <td>4585</td>
      <td>19.4</td>
      <td>3.050500e+08</td>
      <td>65770.0</td>
      <td>5103054.0</td>
      <td>71.0</td>
      <td>1.492477e+08</td>
      <td>1397590.0</td>
      <td>48370.0</td>
      <td>2.020200e+07</td>
      <td>1.439344e+08</td>
      <td>5.301600e+04</td>
      <td>3.0</td>
      <td>6.0</td>
      <td>NaN</td>
      <td>2.0</td>
      <td>14217.0</td>
      <td>NaN</td>
      <td>20104066.0</td>
      <td>13184.0</td>
      <td>656337.0</td>
      <td>1</td>
      <td>2017</td>
      <td>4.542977e+08</td>
    </tr>
    <tr>
      <th>4596</th>
      <td>Dil Meri Na Sune</td>
      <td>Dil Meri Na Sune (From "Genius")</td>
      <td>Atif Aslam</td>
      <td>2018-07-27</td>
      <td>INT101800122</td>
      <td>4575</td>
      <td>19.4</td>
      <td>5.228236e+07</td>
      <td>4602.0</td>
      <td>1449767.0</td>
      <td>56.0</td>
      <td>9.439202e+08</td>
      <td>5347766.0</td>
      <td>NaN</td>
      <td>1.721000e+03</td>
      <td>1.993500e+04</td>
      <td>2.497305e+07</td>
      <td>1.0</td>
      <td>412.0</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>927.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>193590.0</td>
      <td>0</td>
      <td>2018</td>
      <td>9.962026e+08</td>
    </tr>
    <tr>
      <th>4597</th>
      <td>Grace (feat. 42 Dugg)</td>
      <td>My Turn</td>
      <td>Lil Baby</td>
      <td>2020-02-28</td>
      <td>USUG12000043</td>
      <td>4571</td>
      <td>19.4</td>
      <td>1.899727e+08</td>
      <td>72066.0</td>
      <td>6704802.0</td>
      <td>65.0</td>
      <td>2.010273e+08</td>
      <td>1081402.0</td>
      <td>7596.0</td>
      <td>5.288677e+06</td>
      <td>3.684900e+07</td>
      <td>2.925315e+07</td>
      <td>19.0</td>
      <td>204.0</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>74.0</td>
      <td>6.0</td>
      <td>84426740.0</td>
      <td>28999.0</td>
      <td>1135998.0</td>
      <td>1</td>
      <td>2020</td>
      <td>3.910000e+08</td>
    </tr>
    <tr>
      <th>4598</th>
      <td>Nashe Si Chadh Gayi</td>
      <td>November Top 10 Songs</td>
      <td>Arijit Singh</td>
      <td>2016-11-08</td>
      <td>INY091600067</td>
      <td>4591</td>
      <td>19.4</td>
      <td>1.454670e+08</td>
      <td>14037.0</td>
      <td>7387064.0</td>
      <td>66.0</td>
      <td>1.118595e+09</td>
      <td>3868828.0</td>
      <td>11433.0</td>
      <td>2.534831e+06</td>
      <td>3.775730e+07</td>
      <td>2.515052e+07</td>
      <td>1.0</td>
      <td>1200.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7.0</td>
      <td>6817840.0</td>
      <td>NaN</td>
      <td>448292.0</td>
      <td>0</td>
      <td>2016</td>
      <td>1.264062e+09</td>
    </tr>
    <tr>
      <th>4599</th>
      <td>Me Acostumbre (feat....</td>
      <td>Me Acostumbre (feat. Bad Bunny)</td>
      <td>Arcï¿½ï¿½</td>
      <td>2017-04-11</td>
      <td>USB271700107</td>
      <td>4593</td>
      <td>19.4</td>
      <td>2.557407e+08</td>
      <td>32138.0</td>
      <td>14066526.0</td>
      <td>64.0</td>
      <td>8.663008e+08</td>
      <td>3826829.0</td>
      <td>78000.0</td>
      <td>1.005546e+07</td>
      <td>7.019639e+07</td>
      <td>1.758314e+08</td>
      <td>11.0</td>
      <td>2083.0</td>
      <td>NaN</td>
      <td>4.0</td>
      <td>127479.0</td>
      <td>4.0</td>
      <td>69006739.0</td>
      <td>11320.0</td>
      <td>767006.0</td>
      <td>1</td>
      <td>2017</td>
      <td>1.122041e+09</td>
    </tr>
  </tbody>
</table>
<p>4600 rows × 29 columns</p>
</div>




```python
artistAnalysis = df.copy()
artistAnalysis =artistAnalysis[artistAnalysis['Artist'] == 'Drake']
artistAnalysis['Explicit Track'] = artistAnalysis['Explicit Track'].replace({0:'Not Explicit',1:'Explicit'})
topsongs = artistAnalysis.sort_values(by='Spotify Streams', ascending=False)
topsongs = topsongs.loc[topsongs['Track'].shift() != topsongs['Track']]
fig = make_subplots(rows=1,cols=2,specs=[[{"type": "pie"}, {"type": "bar"}]])
pie = go.Pie(labels=artistAnalysis["Explicit Track"], title='Explicit Track Distribution<br>')
bar = go.Bar(x=topsongs['Track'][:6],y=topsongs['Spotify Streams'],marker_color='#5ab873',name='Top viewed songs')
fig.add_trace(pie,row=1,col=1)
fig.add_trace(bar,row=1,col=2)
fig.update_layout(height=500, width=1000, title_text="Drake's Analysis",title_x=0.5)
fig.update_yaxes(title_text="Views", row=1, col=2)
fig.show()
```


<div>                            <div id="2e2263be-dbf8-4aa4-8aae-460f54509d26" class="plotly-graph-div" style="height:500px; width:1000px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("2e2263be-dbf8-4aa4-8aae-460f54509d26")) {                    Plotly.newPlot(                        "2e2263be-dbf8-4aa4-8aae-460f54509d26",                        [{"labels":["Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Not Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit","Explicit"],"title":{"text":"Explicit Track Distribution\u003cbr\u003e"},"type":"pie","domain":{"x":[0.0,0.45],"y":[0.0,1.0]}},{"marker":{"color":"#5ab873"},"name":"Top viewed songs","x":["One Dance","God's Plan","Passionfruit","In My Feelings","Hotline Bling","Nice For What"],"y":[3192204066.0,2529948475.0,1625307365.0,1389607643.0,1335494972.0,1276685592.0,1118459427.0,1059262917.0,1052473730.0,971473231.0,971473231.0,970219741.0,967826246.0,962634706.0,820380309.0,819136167.0,812274757.0,793287638.0,763384135.0,732652170.0,622498643.0,597923434.0,559759920.0,549699659.0,459480135.0,406875019.0,383261731.0,372248119.0,356685934.0,349849639.0,337858357.0,328526298.0,308396120.0,273457499.0,253540951.0,253177498.0,253114498.0,219008713.0,202169670.0,201467069.0,199662854.0,167840439.0,165204421.0,147688885.0,143220845.0,126296463.0,123630149.0,123522254.0,107762230.0,86155124.0,81093233.0,78417212.0,74265576.0,72335127.0,65870686.0,64168685.0,61355849.0,59262518.0,48556312.0,null],"type":"bar","xaxis":"x","yaxis":"y"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.55,1.0]},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Views"}},"title":{"text":"Drake's Analysis","x":0.5},"height":500,"width":1000},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('2e2263be-dbf8-4aa4-8aae-460f54509d26');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })                };                });            </script>        </div>



```python
app = dash.Dash('__name__')
artistAnalysis = df.copy()
app.layout = html.Div(children=[

html.H1('Individual Artist Most Streamed Spotify Songs 2024' , style={'text-align':'center','color':'#303030','font-family': 'Arial, Helvetica, sans-serif'}),
html.Div([
dcc.Dropdown(id = 'artist',options=[{'label':html.Span([x], style={'color': '#303030', 'font-family': 'Arial, Helvetica, sans-serif'}),'value':x} for x in artistAnalysis ['Artist'].astype('str').unique()],
                                     value='PARTYNEXTDOOR',placeholder="Select an Artist",
                                     multi=False)],style={'width':'100%','border-radius': '10px'}),
    dcc.Graph(id='figg',figure={},style={'width':'100%'}),
])
@app.callback(
Output(component_id='figg',component_property='figure'),
Input(component_id='artist',component_property='value')

)
def update(op):
    fil =artistAnalysis[artistAnalysis['Artist'] == op]
    fil['Explicit Track'] = artistAnalysis['Explicit Track'].replace({0:'Not Explicit',1:'Explicit'})
    filtopsongs = fil.sort_values(by='Spotify Streams', ascending=False)
    filtopsongs = filtopsongs.loc[filtopsongs['Track'].shift() != filtopsongs['Track']]
    fig = make_subplots(rows=1,cols=2,specs=[[{"type": "pie"}, {"type": "bar"}]])
    pie = go.Pie(labels=fil["Explicit Track"], title='Explicit Track Distribution<br>')
    bar = go.Bar(x=filtopsongs['Track'][:6],y=filtopsongs['Spotify Streams'],marker_color='#5ab873',name='Top viewed songs',hovertext=filtopsongs['Explicit Track'])
    fig.add_trace(pie,row=1,col=1)
    fig.add_trace(bar,row=1,col=2)
    fig.update_layout(height=500, width=1000, title_text=f"{op}'s Analysis",title_x=0.5)
    fig.update_yaxes(title_text="Views", row=1, col=2)
    return fig
app.run_server(mode='inline')
```



<iframe
    width="100%"
    height="650"
    src="http://127.0.0.1:8050/"
    frameborder="0"
    allowfullscreen

></iframe>




```python

```
