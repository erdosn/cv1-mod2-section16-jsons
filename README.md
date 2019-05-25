
### Questions
* recursive functions
    * how 
    * when
* kotlin

### Objectives
* YWBAT parse a json file effectively

### Outline
* Questions
* loading in jsons from mongo
* using mongo db to access data and find insights on text data

### What does noSQL mean?
* no relational tables
* in no sql, we lose structure and therefore lose joins


### What is a json file
* it's basically a dictionary, with some minor restraints

# Loading in string as dict


```python
stringjson = '{"key":"value"}'
stringjson
```




    '{"key":"value"}'




```python
stringd = json.loads(stringjson)
stringd
```




    {'key': 'value'}



# Loading json file as dict


```python
import json
```


```python
with open("/Users/rafael/file.json") as f:
    d = json.load(f)
d
```




    {'key': 'value',
     'key2': 'value2',
     'students': [{'age': 28, 'name': 'Bryan DiCarlo', 'state': 'TX'},
      {'age': 21, 'name': "Jonathan Ericksen's", 'state': 'not Tx'}]}



### Installing Mongo on a Mac
* install homebrew
* brew install mongo
 
* collections = tables
* documents = rows


```python
import json
import pandas as pd
import numpy as np

from pprint import pprint

from textblob import TextBlob
from pymongo import MongoClient
from bs4 import BeautifulSoup

import matplotlib.pyplot as plt
import seaborn as sns
```


```python
client = MongoClient(host='localhost', port=27017)
```


```python
client.list_database_names()
```




    ['admin',
     'config',
     'local',
     'marchmadness',
     'music_tweets',
     'mydb',
     'new_db',
     'tweets']




```python
musicdb = client["music_tweets"]
```


```python
kendricktweets = musicdb["kendrickLamar"]
```


```python
dlist = []
for index, tweet in enumerate(kendricktweets.find({})): # select * from kendrickLamar;
    d = dict()
    d['text'] = tweet["text"]
    blob = TextBlob(text)
    d['polarity'] = blob.sentiment.polarity
    d['subjectivity'] = blob.sentiment.subjectivity
    
    source = tweet["source"]
    soup = BeautifulSoup(source, 'html.parser')
    res = soup.find('a')
    d["source"] = res.contents[0]
    
    d['coor'] = tweet['coordinates']
    d['location'] = tweet['user']['location']
    if index%200 == 0:
        print("finished {} tweets".format(index))
    dlist.append(d)
```

    finished 0 tweets
    finished 200 tweets
    finished 400 tweets
    finished 600 tweets
    finished 800 tweets
    finished 1000 tweets
    finished 1200 tweets
    finished 1400 tweets
    finished 1600 tweets



```python
df = pd.DataFrame(dlist)
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
      <th>coor</th>
      <th>location</th>
      <th>polarity</th>
      <th>source</th>
      <th>subjectivity</th>
      <th>text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>None</td>
      <td>Indiana, USA</td>
      <td>0.0</td>
      <td>Radio.co now playing</td>
      <td>0.0</td>
      <td>Now Playing: Tech N9ne f. Kendall Morgan, Kend...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>None</td>
      <td>Why?</td>
      <td>0.0</td>
      <td>Twitter for iPhone</td>
      <td>0.0</td>
      <td>RT @Bj532x: امبي وايد حلوه 💔 https://t.co/2oDR...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>None</td>
      <td>None</td>
      <td>0.0</td>
      <td>Twitter for iPhone</td>
      <td>0.0</td>
      <td>@JayStephMD @kendricklamar Bitch can we win a ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>None</td>
      <td>Nightosphere.</td>
      <td>0.0</td>
      <td>Twitter for iPhone</td>
      <td>0.0</td>
      <td>Well this is trash cause 4:44 &amp;amp; u missed W...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>None</td>
      <td>Jeno's smile</td>
      <td>0.0</td>
      <td>Twitter for Android</td>
      <td>0.0</td>
      <td>RT @archivelyric: pray for me - the weeknd ft....</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.location.value_counts()
```




    New York, NY                     125
    Johannesburg, South Africa        21
    South Africa                      19
    Florida, USA                      18
    Nairobi, Kenya                    17
    Lagos, Nigeria                    16
    United States                     16
    Los Angeles, CA                   15
    Accra, Ghana                      11
    Paris, France                     11
    London, England                    8
    Houston, TX                        8
    Worldwide                          7
    Cork, Ireland                      6
    California, USA                    5
    Paris                              5
    Port Elizabeth, South Africa       5
    Chicago, IL                        5
    Michigan, USA                      5
    Nigeria                            5
    France                             5
    Nashville, TN                      5
    Miami, FL                          5
    Boston, MA                         4
    Dubai, United Arab Emirates        4
    United Kingdom                     4
    Warri                              4
    Kenya                              4
    hell                               4
    Durban, South Africa               4
                                    ... 
    just ere init                      1
    Bahrain                            1
    wake and bake, tx                  1
    floor 555                          1
    {e}                                1
    ~Rêveuse~                          1
    18                                 1
    Telford, Shropshire, UK            1
    Chapadão - Rio de Janeiro/RJ       1
    Hobbs, NM                          1
    Hollywood                          1
    Lagos City                         1
    Euphoria                           1
    cloud9                             1
    Dunder-Mifflin                     1
    Saskatoon, Saskatchewan            1
    Maryland to Houston                1
    George Mason, VA                   1
    Middle east                        1
    Paris, France.                     1
    Nairobi || Kenya ya bish           1
    롤플레이어 | Lahomie                    1
    west                               1
    Lekki, Nigeria                     1
    Baden-Württemberg, Germany         1
    Why?                               1
    sweden                             1
    Siete siete                        1
    All up in this place               1
    Kiev, Ukraine                      1
    Name: location, Length: 852, dtype: int64




```python
from string import ascii_letters
ascii_letters
```




    'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'




```python
all(ch in ascii_letters for ch in df.text[0])
```




    False




```python
for index, tweet in enumerate(kendricktweets.find({}, limit=5)): # select * from kendrickLamar;
    pprint(tweet)
    break
```

    {'_id': ObjectId('5c55441d26596444e00f9b3b'),
     'contributors': None,
     'coordinates': None,
     'created_at': 'Sat Feb 02 07:17:43 +0000 2019',
     'entities': {'hashtags': [{'indices': [108, 116], 'text': 'GetLive'},
                               {'indices': [118, 138],
                                'text': 'GetLiveMediaNetwork'}],
                  'symbols': [],
                  'urls': [],
                  'user_mentions': [{'id': 776128881107558401,
                                     'id_str': '776128881107558401',
                                     'indices': [97, 107],
                                     'name': '7Six5Live Radio',
                                     'screen_name': '7Six5Live'}]},
     'favorite_count': 0,
     'favorited': False,
     'filter_level': 'low',
     'geo': None,
     'id': 1091596511681630208,
     'id_str': '1091596511681630208',
     'in_reply_to_screen_name': None,
     'in_reply_to_status_id': None,
     'in_reply_to_status_id_str': None,
     'in_reply_to_user_id': None,
     'in_reply_to_user_id_str': None,
     'is_quote_status': False,
     'lang': 'en',
     'place': None,
     'quote_count': 0,
     'reply_count': 0,
     'retweet_count': 0,
     'retweeted': False,
     'source': '<a href="https://radio.co" rel="nofollow">Radio.co now playing</a>',
     'text': 'Now Playing: Tech N9ne f. Kendall Morgan, Kendrick Lamar &amp; '
             'Mayday! - Fragile (Radio Edit) on @7Six5Live #GetLive  '
             '#GetLiveMediaNetwork',
     'timestamp_ms': '1549091863924',
     'truncated': False,
     'user': {'contributors_enabled': False,
              'created_at': 'Wed Sep 14 18:41:59 +0000 2016',
              'default_profile': False,
              'default_profile_image': False,
              'description': '7Six5 Live is a mobile streaming radio station and '
                             'app playing Hip-Hop/Rap, R&B, EDM & Urban Pop music! '
                             'http://7Six5Live.com',
              'favourites_count': 161,
              'follow_request_sent': None,
              'followers_count': 526,
              'following': None,
              'friends_count': 201,
              'geo_enabled': False,
              'id': 776128881107558401,
              'id_str': '776128881107558401',
              'is_translator': False,
              'lang': 'en',
              'listed_count': 7,
              'location': 'Indiana, USA',
              'name': '7Six5Live Radio',
              'notifications': None,
              'profile_background_color': '000000',
              'profile_background_image_url': 'http://abs.twimg.com/images/themes/theme1/bg.png',
              'profile_background_image_url_https': 'https://abs.twimg.com/images/themes/theme1/bg.png',
              'profile_background_tile': False,
              'profile_banner_url': 'https://pbs.twimg.com/profile_banners/776128881107558401/1483569311',
              'profile_image_url': 'http://pbs.twimg.com/profile_images/816774823556870144/d8OcnUcX_normal.jpg',
              'profile_image_url_https': 'https://pbs.twimg.com/profile_images/816774823556870144/d8OcnUcX_normal.jpg',
              'profile_link_color': 'FF691F',
              'profile_sidebar_border_color': '000000',
              'profile_sidebar_fill_color': '000000',
              'profile_text_color': '000000',
              'profile_use_background_image': False,
              'protected': False,
              'screen_name': '7Six5Live',
              'statuses_count': 176093,
              'time_zone': None,
              'translator_type': 'none',
              'url': 'http://7six5live.com',
              'utc_offset': None,
              'verified': False}}


### Assessment
