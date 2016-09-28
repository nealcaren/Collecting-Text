
# Chapter 3: Working with APIs

[Overview of what APIs are]

[Chronicling America](http://chroniclingamerica.loc.gov/about/) is a joint project of the National Endowment for the Humanities and the Library of Congress that [More description.

The website has a search function and my search for the term "[slavery](http://chroniclingamerica.loc.gov/search/pages/results/?andtext=slavery)" returned 404,325 results.

<img src="images/ca_slavery_search.png">

This is great, but they make it much better for researchers by providing an API to assist with searching and downloading their archive. [Note about bulk downloads.]

One of the nice things about APIs is that they are often intuitive, or at least interpretable after you see them. For example, to retrieve the first page of search results into an easily digestable format, you append ``&format=json`` to the end of the search URL ``http://chroniclingamerica.loc.gov/search/pages/results/?andtext=slavery``. In your browser, this returns a text file in the JSON format.

<img src="images/ca_slavery_api.png">

Thankfully, the programers have made the variable names understandable. As before, the search found 404,325 results (``"totalItems": 404325,``). The server did not return all of these, however, just 20 of them (``"itemsPerPage": 20``), starting with the first result (``"startIndex": 1,``) and ending with the 20th (``"endIndex": 20,``). 


http://chroniclingamerica.loc.gov/search/pages/results/?andtext=slavery&format=json


(http://chroniclingamerica.loc.gov/about/api/)

[``requests``](http://docs.python-requests.org/en/master/) is a useful and commonly used HTTP library for python. It is not a part of the default installation, but is included with Anaconda Python Distribution. 


```python
import requests
```

It would be possible to use the API URL and parameters directly in the requests command, but since the most likely scenario involves making repeating calls to ``requests`` as part of a loop -- the search returned less than 1% of the results -- I store the strings first. 


```python
base_url = 'http://chroniclingamerica.loc.gov/search/pages/results/'
parameters = '?andtext=slavery&format=json'
```

`requests.get()` is used for both accessing websites and APIs. The command can be modified by several arguements, but at a minimum, it requires the URL.


```python
r = requests.get(base_url + parameters)
```

`r` is a `requests` response object. Any JSON returned by the server are stored in `.json().`


```python
search_json = r.json()
```

JSONs are dictionary like objects, in that they have keys (think variable names) and values. `.keys()` returns a list of the keys.


```python
print search_json.keys()
```

    [u'totalItems', u'endIndex', u'startIndex', u'itemsPerPage', u'items']


You can return the value of any key by putting the key name in brackets.


```python
search_json['totalItems']
```




    404325



As is often the case with results from an API, most of the keys and values are metadate about either the search or what is being returned. These are useful for knowing if the search is returning what you want, which is particularly important when you are making multiple calls to the API. 

The data I'm intereted in is all in `items`. 


```python
print type(search_json['items'])
print len(search_json['items'])
```

    <type 'list'>
    20


`items` is a list with 20 items.


```python
print type(search_json['items'][0])
print type(search_json['items'][19])
```

    <type 'dict'>
    <type 'dict'>


Each of the 20 items in the list is a dictionary. 


```python
first_item =  search_json['items'][0]

print first_item.keys()
```

    [u'sequence', u'county', u'edition', u'frequency', u'id', u'section_label', u'city', u'date', u'title', u'end_year', u'note', u'state', u'subject', u'type', u'place_of_publication', u'start_year', u'edition_label', u'publisher', u'language', u'alt_title', u'lccn', u'country', u'ocr_eng', u'batch', u'title_normal', u'url', u'place', u'page']


While a standard CSV file has a header row that describes the contents of each column, a JSON file has keys identifying the values found in each case. Importantly, these keys need not be the same for each item. Additionally, values don't have to be numbers of strings, but could be lists or dictionaries. For example, this JSON could have included a `newspaper` key that was a dictionary with all the metadata about the newspaper the article and issue was published, an `article` key that include the article specific information as another dictionary, and a `text` key whose value was a string with the article text.

As before, we can examine the contents of a particular item, such as the publication's `title`.


```python
print first_item['title']
```

    Anti-slavery bugle. volume



```python
import pandas as pd

# Make sure all columns are displayed
pd.set_option("display.max_columns",101)
```


```python
pd.DataFrame(search_json['items'])
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>alt_title</th>
      <th>batch</th>
      <th>city</th>
      <th>country</th>
      <th>county</th>
      <th>date</th>
      <th>edition</th>
      <th>edition_label</th>
      <th>end_year</th>
      <th>frequency</th>
      <th>id</th>
      <th>language</th>
      <th>lccn</th>
      <th>note</th>
      <th>ocr_eng</th>
      <th>page</th>
      <th>place</th>
      <th>place_of_publication</th>
      <th>publisher</th>
      <th>section_label</th>
      <th>sequence</th>
      <th>start_year</th>
      <th>state</th>
      <th>subject</th>
      <th>title</th>
      <th>title_normal</th>
      <th>type</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[]</td>
      <td>batch_ohi_ariel_ver02</td>
      <td>[New Lisbon, Salem]</td>
      <td>Ohio</td>
      <td>[Columbiana, Columbiana]</td>
      <td>18490316</td>
      <td>None</td>
      <td></td>
      <td>1861</td>
      <td>Weekly</td>
      <td>/lccn/sn83035487/1849-03-16/ed-1/seq-1/</td>
      <td>[English]</td>
      <td>sn83035487</td>
      <td>[Archived issues are available in digital form...</td>
      <td>LAVE\nam\nJlile\nVOL. 4. NO. 30.\nSALEM. OHIO,...</td>
      <td></td>
      <td>[Ohio--Columbiana--New Lisbon, Ohio--Columbian...</td>
      <td>New-Lisbon, Ohio</td>
      <td>Ohio American Antislavery Society</td>
      <td></td>
      <td>1</td>
      <td>1845</td>
      <td>[Ohio, Ohio]</td>
      <td>[Antislavery movements--United States--Newspap...</td>
      <td>Anti-slavery bugle. volume</td>
      <td>anti-slavery bugle.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83035...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[]</td>
      <td>batch_iune_golf_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19140516</td>
      <td>None</td>
      <td>NOON EDITION</td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1914-05-16/ed-1/seq-10/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>r\nmmmmmmmmmmmmmmmmmmmmmmmm\n'SLAVERY RIFE IN ...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>10</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[]</td>
      <td>batch_iune_india_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19161109</td>
      <td>None</td>
      <td>EXTRA</td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1916-11-09/ed-1/seq-26/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>us remaining whites if we expect to\nstay on t...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>26</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[]</td>
      <td>batch_iune_golf_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19150327</td>
      <td>None</td>
      <td>NOON EDITION</td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1915-03-27/ed-1/seq-24/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>THOUSANDS OF VEILED WOMEN OF TURKISH\nHAREM ON...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>24</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[]</td>
      <td>batch_iune_foxtrot_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19130815</td>
      <td>None</td>
      <td></td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1913-08-15/ed-1/seq-5/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>LOLA NORRiajQlVS SiENSAT-iPN AL t EVIDENCE IN ...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>5</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>[]</td>
      <td>batch_iune_foxtrot_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19130308</td>
      <td>None</td>
      <td>NOON EDITION</td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1913-03-08/ed-1/seq-6/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>that every possible weakness in. a\ngirl as &amp;e...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>6</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>[]</td>
      <td>batch_iune_foxtrot_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19130424</td>
      <td>None</td>
      <td></td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1913-04-24/ed-1/seq-13/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>mpICFED FOR WHITE -SLAVERY.\nTop Lola Norris-a...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>13</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>[]</td>
      <td>batch_dlc_elf_ver03</td>
      <td>[Washington]</td>
      <td>District of Columbia</td>
      <td>[None]</td>
      <td>18540511</td>
      <td>None</td>
      <td></td>
      <td>1860</td>
      <td>Weekly</td>
      <td>/lccn/sn84026752/1854-05-11/ed-1/seq-1/</td>
      <td>[English]</td>
      <td>sn84026752</td>
      <td>[Also issued on microfilm by University Microf...</td>
      <td>I IiiLMI or SUBSCRimOM\nI T. \ .. &amp;m is publis...</td>
      <td></td>
      <td>[District of Columbia--Washington]</td>
      <td>Washington [D.C.]</td>
      <td>L.P. Noble</td>
      <td></td>
      <td>1</td>
      <td>1847</td>
      <td>[District of Columbia]</td>
      <td>[African Americans--Washington (D.C.)--Newspap...</td>
      <td>The national era.</td>
      <td>national era.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn84026...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>[]</td>
      <td>batch_iune_foxtrot_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19130225</td>
      <td>None</td>
      <td></td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1913-02-25/ed-1/seq-30/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>we are doing what the American\nmen did Avay b...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>30</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>[]</td>
      <td>batch_dlc_elf_ver03</td>
      <td>[Washington]</td>
      <td>District of Columbia</td>
      <td>[None]</td>
      <td>18540511</td>
      <td>None</td>
      <td></td>
      <td>1860</td>
      <td>Weekly</td>
      <td>/lccn/sn84026752/1854-05-11/ed-1/seq-4/</td>
      <td>[English]</td>
      <td>sn84026752</td>
      <td>[Also issued on microfilm by University Microf...</td>
      <td>f\nI 76\n[COXTIHCED PBOM KIMT PAGE.]\nour fath...</td>
      <td>76</td>
      <td>[District of Columbia--Washington]</td>
      <td>Washington [D.C.]</td>
      <td>L.P. Noble</td>
      <td></td>
      <td>4</td>
      <td>1847</td>
      <td>[District of Columbia]</td>
      <td>[African Americans--Washington (D.C.)--Newspap...</td>
      <td>The national era.</td>
      <td>national era.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn84026...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>[Freeman]</td>
      <td>batch_vtu_hildene_ver01</td>
      <td>[Montpelier]</td>
      <td>Vermont</td>
      <td>[Washington]</td>
      <td>18460827</td>
      <td>None</td>
      <td></td>
      <td>1884</td>
      <td>Weekly</td>
      <td>/lccn/sn84023209/1846-08-27/ed-1/seq-1/</td>
      <td>[English]</td>
      <td>sn84023209</td>
      <td>[Absorbed by: Vermont watchman (Montpelier, Vt...</td>
      <td>nr\nLIBERTY AND E Q UALITYMAN'S COMMON BIRTHRI...</td>
      <td></td>
      <td>[Vermont--Washington--Montpelier]</td>
      <td>Montpelier, Vt.</td>
      <td>J. Poland</td>
      <td></td>
      <td>1</td>
      <td>1844</td>
      <td>[Vermont]</td>
      <td>[Montpelier (Vt.)--Newspapers., Vermont--Montp...</td>
      <td>Green-Mountain freeman.</td>
      <td>green-mountain freeman.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn84023...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>[]</td>
      <td>batch_dlc_elf_ver03</td>
      <td>[Washington]</td>
      <td>District of Columbia</td>
      <td>[None]</td>
      <td>18590505</td>
      <td>None</td>
      <td></td>
      <td>1860</td>
      <td>Weekly</td>
      <td>/lccn/sn84026752/1859-05-05/ed-1/seq-2/</td>
      <td>[English]</td>
      <td>sn84026752</td>
      <td>[Also issued on microfilm by University Microf...</td>
      <td>70\nemancipation for the liberation of all its...</td>
      <td>70</td>
      <td>[District of Columbia--Washington]</td>
      <td>Washington [D.C.]</td>
      <td>L.P. Noble</td>
      <td></td>
      <td>2</td>
      <td>1847</td>
      <td>[District of Columbia]</td>
      <td>[African Americans--Washington (D.C.)--Newspap...</td>
      <td>The national era.</td>
      <td>national era.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn84026...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>[]</td>
      <td>batch_mimtptc_alpena_ver01</td>
      <td>[Grand Haven]</td>
      <td>Michigan</td>
      <td>[Ottawa]</td>
      <td>18600926</td>
      <td>None</td>
      <td></td>
      <td>1899</td>
      <td>Weekly</td>
      <td>/lccn/sn85033622/1860-09-26/ed-1/seq-1/</td>
      <td>[English]</td>
      <td>sn85033622</td>
      <td>[Archived issues are available in digital form...</td>
      <td>j . ' c'Y ' (: -A\nfa\nGMA.ND M AYEN WE\nft t ...</td>
      <td></td>
      <td>[Michigan--Ottawa--Grand Haven]</td>
      <td>Grand Haven, Mich.</td>
      <td>Barns &amp; Fosha</td>
      <td></td>
      <td>1</td>
      <td>1858</td>
      <td>[Michigan]</td>
      <td>[Grand Haven (Mich.)--Newspapers., Michigan--G...</td>
      <td>The Grand Haven news.</td>
      <td>grand haven news.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn85033...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>[Ashtabula telegraph]</td>
      <td>batch_ohi_guildenstern_ver01</td>
      <td>[Ashtabula]</td>
      <td>Ohio</td>
      <td>[Ashtabula]</td>
      <td>18610209</td>
      <td>None</td>
      <td></td>
      <td>1873</td>
      <td>Weekly</td>
      <td>/lccn/sn83035216/1861-02-09/ed-1/seq-1/</td>
      <td>[English]</td>
      <td>sn83035216</td>
      <td>[Archived issues are available in digital form...</td>
      <td>ASHT\nABULA\n5EKLY TELEGRAP\n0\nIndependent in...</td>
      <td></td>
      <td>[Ohio--Ashtabula--Ashtabula]</td>
      <td>Ashtabula, Ohio</td>
      <td>N.W. Thayer</td>
      <td></td>
      <td>1</td>
      <td>1853</td>
      <td>[Ohio]</td>
      <td>[Ashtabula (Ohio)--Newspapers., Ohio--Ashtabul...</td>
      <td>Ashtabula weekly telegraph.</td>
      <td>ashtabula weekly telegraph.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83035...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>[Voice, Voice of freedom]</td>
      <td>batch_vtu_green_ver02</td>
      <td>[Brandon, Montpelier]</td>
      <td>Vermont</td>
      <td>[Rutland, Washington]</td>
      <td>18390601</td>
      <td>None</td>
      <td></td>
      <td>1848</td>
      <td>Weekly</td>
      <td>/lccn/sn84022687/1839-06-01/ed-1/seq-1/</td>
      <td>[English]</td>
      <td>sn84022687</td>
      <td>["Published under the sanction of the Vermont ...</td>
      <td>THE VOICE OF FREEDOM\nALLEN &amp; POLAND, Publishe...</td>
      <td></td>
      <td>[Vermont--Rutland--Brandon, Vermont--Washingto...</td>
      <td>None</td>
      <td>None</td>
      <td></td>
      <td>1</td>
      <td>1839</td>
      <td>[Vermont, Vermont]</td>
      <td>[Antislavery movements--Vermont--Newspapers., ...</td>
      <td>The voice of freedom. volume</td>
      <td>voice of freedom.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn84022...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>[]</td>
      <td>batch_ohi_cobweb_ver04</td>
      <td>[Perrysburg]</td>
      <td>Ohio</td>
      <td>[Wood]</td>
      <td>18540701</td>
      <td>None</td>
      <td></td>
      <td>1861</td>
      <td>Weekly</td>
      <td>/lccn/sn85026192/1854-07-01/ed-1/seq-3/</td>
      <td>[English]</td>
      <td>sn85026192</td>
      <td>[Archived issues are available in digital form...</td>
      <td>Proceedings the Members of\nCongress adverse t...</td>
      <td>131</td>
      <td>[Ohio--Wood--Perrysburg]</td>
      <td>Perrysburg, Ohio</td>
      <td>Simon Clark</td>
      <td></td>
      <td>3</td>
      <td>1853</td>
      <td>[Ohio]</td>
      <td>[]</td>
      <td>The Perrysburg journal.</td>
      <td>perrysburg journal.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn85026...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>[]</td>
      <td>batch_ohi_byrd_ver01</td>
      <td>[New York, Cleveland]</td>
      <td>New York</td>
      <td>[New York, Cuyahoga]</td>
      <td>19201016</td>
      <td>None</td>
      <td></td>
      <td>1922</td>
      <td>Weekly</td>
      <td>/lccn/sn88078683/1920-10-16/ed-1/seq-4/</td>
      <td>[English]</td>
      <td>sn88078683</td>
      <td>["Official organ of the Communist Labor Party ...</td>
      <td>PAGE 4\nTHE TOILEK\nSATUKDAY, OCT. 16tb, 120\n...</td>
      <td>PAGE 4</td>
      <td>[New York--New York--New York, Ohio--Cuyahoga-...</td>
      <td>Cleveland, Ohio</td>
      <td>Communist Labor Party of Ohio</td>
      <td></td>
      <td>4</td>
      <td>1919</td>
      <td>[New York, Ohio]</td>
      <td>[Cleveland (Ohio)--Newspapers., Communism--Uni...</td>
      <td>The toiler.</td>
      <td>toiler.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn88078...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>[]</td>
      <td>batch_ohi_byrd_ver01</td>
      <td>[New York, Cleveland]</td>
      <td>New York</td>
      <td>[New York, Cuyahoga]</td>
      <td>19200910</td>
      <td>None</td>
      <td></td>
      <td>1922</td>
      <td>Weekly</td>
      <td>/lccn/sn88078683/1920-09-10/ed-1/seq-10/</td>
      <td>[English]</td>
      <td>sn88078683</td>
      <td>["Official organ of the Communist Labor Party ...</td>
      <td>PAGE 10\nTHE TOILER\nFRIDAY, SEPT. 10, 1920\nT...</td>
      <td>PAGE 10</td>
      <td>[New York--New York--New York, Ohio--Cuyahoga-...</td>
      <td>Cleveland, Ohio</td>
      <td>Communist Labor Party of Ohio</td>
      <td></td>
      <td>10</td>
      <td>1919</td>
      <td>[New York, Ohio]</td>
      <td>[Cleveland (Ohio)--Newspapers., Communism--Uni...</td>
      <td>The toiler.</td>
      <td>toiler.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn88078...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>[Freeman]</td>
      <td>batch_vtu_hildene_ver01</td>
      <td>[Montpelier]</td>
      <td>Vermont</td>
      <td>[Washington]</td>
      <td>18481019</td>
      <td>None</td>
      <td></td>
      <td>1884</td>
      <td>Weekly</td>
      <td>/lccn/sn84023209/1848-10-19/ed-1/seq-1/</td>
      <td>[English]</td>
      <td>sn84023209</td>
      <td>[Absorbed by: Vermont watchman (Montpelier, Vt...</td>
      <td>Devoted to the Emancipation, and Social, Moral...</td>
      <td></td>
      <td>[Vermont--Washington--Montpelier]</td>
      <td>Montpelier, Vt.</td>
      <td>J. Poland</td>
      <td></td>
      <td>1</td>
      <td>1844</td>
      <td>[Vermont]</td>
      <td>[Montpelier (Vt.)--Newspapers., Vermont--Montp...</td>
      <td>Green-Mountain freeman.</td>
      <td>green-mountain freeman.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn84023...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>[]</td>
      <td>batch_dlc_elf_ver03</td>
      <td>[Washington]</td>
      <td>District of Columbia</td>
      <td>[None]</td>
      <td>18540420</td>
      <td>None</td>
      <td></td>
      <td>1860</td>
      <td>Weekly</td>
      <td>/lccn/sn84026752/1854-04-20/ed-1/seq-2/</td>
      <td>[English]</td>
      <td>sn84026752</td>
      <td>[Also issued on microfilm by University Microf...</td>
      <td>?.? ?\nH v i\n62 !\nkottce to oira bostot sbbs...</td>
      <td>62</td>
      <td>[District of Columbia--Washington]</td>
      <td>Washington [D.C.]</td>
      <td>L.P. Noble</td>
      <td></td>
      <td>2</td>
      <td>1847</td>
      <td>[District of Columbia]</td>
      <td>[African Americans--Washington (D.C.)--Newspap...</td>
      <td>The national era.</td>
      <td>national era.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn84026...</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
