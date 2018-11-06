

```python
import json
import requests
import pprint
import tweepy
import info

import pandas as pd
import numpy as np

from importlib import reload
from textblob import TextBlob

import matplotlib.pyplot as plt
```


```python
with open(info.get_path()) as f:
    d = json.load(f)
    consumer_key = d["consumer_key"]
    consumer_secret = d["consumer_secret"]
    access_token = d["access_token"]
    access_token_secret = d['access_secret']
```


```python
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tweepy.API(auth, retry_delay=2, retry_count=10)
```


```python
user = api.me()
data = []
text_set = set()
query = "rick and morty"
```


```python
searched_tweets = [status for status in tweepy.Cursor(api.search, q=query).items(1000)]
```


```python
for tweet in searched_tweets:    
    d=dict()
    text = tweet.text
    if text not in text_set:
        d["text"] = tweet.text
        d["date_created"] = tweet.created_at
        d["user"] = tweet.user.name
        blob = TextBlob(d["text"])
        d["polarity"] = blob.sentiment.polarity
        d["subjectivity"] = blob.sentiment.subjectivity
        data.append(d)
        text_set.add(text)
```


```python
df = pd.DataFrame(data)
```


```python
df.shape
```




    (499, 5)




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
      <th>date_created</th>
      <th>polarity</th>
      <th>subjectivity</th>
      <th>text</th>
      <th>user</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2018-11-04 22:11:02</td>
      <td>0.0</td>
      <td>0.333333</td>
      <td>RT @chuuzus: we really went a whole year witho...</td>
      <td>Narnia Business 🇱🇨🇧🇧</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2018-11-04 22:10:44</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>RT @3abdosafwat: إجتماع لمحبي Rick and Morty ل...</td>
      <td>mooma</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2018-11-04 22:10:07</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>@Dysmo Hulu. Rick and Morty all the way dawg</td>
      <td>Cole es bonito e simpático</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2018-11-04 22:09:00</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>I Made Myself @McJuggerNuggets Rick And Morty ...</td>
      <td>JermaineTheLoudHouseBoy</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2018-11-04 22:08:35</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>RT @Fallout: Wubba lubba dub dub! Watch @adult...</td>
      <td>𝖒𝖔𝖎𝖘𝖙</td>
    </tr>
  </tbody>
</table>
</div>


