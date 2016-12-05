

```python
import requests
import re
import json
import pandas as pd
from pandas.io.json import json_normalize
```


```python
url = 'http://www.espn.com/nfl/game?gameId=400874491'
```


```python
text = requests.get(url).text
```


```python
wp = re.findall("espn\.gamepackage\.probability\.data = (.*\]);", text)[0]
```


```python
wp = json.loads(wp)
```


```python
df = json_normalize(wp)
```


```python
df['delta_homeWinPercentage']  = df['homeWinPercentage'].diff()
```


```python
df.groupby(['play.start.team.id']).mean()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>homeWinPercentage</th>
      <th>play.awayScore</th>
      <th>play.homeScore</th>
      <th>play.period.number</th>
      <th>play.start.distance</th>
      <th>play.start.down</th>
      <th>play.start.yardLine</th>
      <th>play.start.yardsToEndzone</th>
      <th>tiePercentage</th>
      <th>delta_homeWinPercentage</th>
    </tr>
    <tr>
      <th>play.start.team.id</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12</th>
      <td>0.842274</td>
      <td>10.452055</td>
      <td>17.287671</td>
      <td>2.616438</td>
      <td>8.027397</td>
      <td>1.835616</td>
      <td>41.356164</td>
      <td>58.643836</td>
      <td>0.0</td>
      <td>0.004479</td>
    </tr>
    <tr>
      <th>18</th>
      <td>0.826769</td>
      <td>9.230769</td>
      <td>17.615385</td>
      <td>2.571429</td>
      <td>8.032967</td>
      <td>1.802198</td>
      <td>52.571429</td>
      <td>52.571429</td>
      <td>0.0</td>
      <td>-0.000078</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['play.type.text'].value_counts()
```




    Pass Reception                   49
    Rush                             49
    Pass Incompletion                17
    Penalty                          11
    Kickoff                          10
    Punt                              8
    Timeout                           7
    Passing Touchdown                 5
    Field Goal Good                   2
    Two-minute warning                2
    End Period                        2
    Fumble Recovery (Opponent)        1
    Sack                              1
    Fumble Recovery (Own)             1
    End of Half                       1
    Interception Return Touchdown     1
    End of Game                       1
    Name: play.type.text, dtype: int64




```python

```
