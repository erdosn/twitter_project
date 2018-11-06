

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
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer

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
api = tweepy.API(auth, wait_on_rate_limit=True)
```


```python
user = api.me()
query = "rick and morty"
```


```python
data = [] # set your data list
text_set = set() # set your text list
```


```python
searched_tweets = [status for status in tweepy.Cursor(api.search, q=query).items(5000)]
```


```python
analyzer = SentimentIntensityAnalyzer()
```


```python
for tweet in searched_tweets:    
    d=dict()
    text = tweet.text
    if text not in text_set:
        d["text"] = tweet.text # get tweet text
        d["date_created"] = tweet.created_at # get the date it was created
        # d["user"] = tweet.user.name # get user name
        blob = TextBlob(d["text"]) # create textblob object
        d["textblob_polarity"] = blob.sentiment.polarity # get polarity (positive/negative)
        d["textblob_subjectivity"] = blob.sentiment.subjectivity # get opinion/fact
        vader_dict = analyzer.polarity_scores(tweet.text)
        d.update(vader_dict)
        data.append(d)
        text_set.add(text)
```


```python
df = pd.DataFrame(data)
```


```python
df.shape
```




    (1628, 8)




```python
df["v_agrees_t"] = [1 if v*t > 0 else 0 for v, t in zip(df["compound"], df.textblob_polarity)]
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
      <th>compound</th>
      <th>date_created</th>
      <th>neg</th>
      <th>neu</th>
      <th>pos</th>
      <th>text</th>
      <th>textblob_polarity</th>
      <th>textblob_subjectivity</th>
      <th>v_agrees_t</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.0000</td>
      <td>2018-11-06 21:37:56</td>
      <td>0.0</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>RT @jovianjake: How is it possible that so man...</td>
      <td>0.125</td>
      <td>0.641667</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.6369</td>
      <td>2018-11-06 21:36:58</td>
      <td>0.0</td>
      <td>0.704</td>
      <td>0.296</td>
      <td>I voted for #TeamRickAndMorty on @TheTylt—I lo...</td>
      <td>0.500</td>
      <td>0.600000</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.0000</td>
      <td>2018-11-06 21:34:39</td>
      <td>0.0</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>Today is a day for blunts and rick and morty e...</td>
      <td>0.000</td>
      <td>0.000000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.0000</td>
      <td>2018-11-06 21:34:19</td>
      <td>0.0</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>Estas a ver Rick and morty ? https://t.co/7ahT...</td>
      <td>0.000</td>
      <td>0.000000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.0000</td>
      <td>2018-11-06 21:33:47</td>
      <td>0.0</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>Rick and Morty: The Rickshank Rickdemption Dec...</td>
      <td>-0.400</td>
      <td>0.400000</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>


