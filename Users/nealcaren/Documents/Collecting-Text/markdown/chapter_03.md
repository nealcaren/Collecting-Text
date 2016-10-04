
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
first_item = search_json['items'][0]

print first_item.keys()
```

    [u'sequence', u'county', u'edition', u'frequency', u'id', u'section_label', u'city', u'date', u'title', u'end_year', u'note', u'state', u'subject', u'type', u'place_of_publication', u'start_year', u'edition_label', u'publisher', u'language', u'alt_title', u'lccn', u'country', u'ocr_eng', u'batch', u'title_normal', u'url', u'place', u'page']


While a standard CSV file has a header row that describes the contents of each column, a JSON file has keys identifying the values found in each case. Importantly, these keys need not be the same for each item. Additionally, values don't have to be numbers of strings, but could be lists or dictionaries. For example, this JSON could have included a `newspaper` key that was a dictionary with all the metadata about the newspaper the article and issue was published, an `article` key that include the article specific information as another dictionary, and a `text` key whose value was a string with the article text.

As before, we can examine the contents of a particular item, such as the publication's `title`.


```python
print first_item['title']
```

    Anti-slavery bugle. volume


The easiest way to view or analyze this data is to convert it to a dataset-like structure. While Python does not have a builting in dataframe type, the popular `pandas` library does. By convention, it is imported as `pd`.


```python
import pandas as pd

# Make sure all columns are displayed
pd.set_option("display.max_columns",101)
```

pandas is prety smart about importing different JSON-type objects and converting them to dataframes with its `.DataFrame()` function.


```python
df = pd.DataFrame(search_json['items'])

df.head(10)
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
  </tbody>
</table>
</div>



Note that I converted `search_json['items']` to  dataframe and not the entire JSON file. This is because I wanted each row to be an article. 

If this dataframe contained all the items that you were looking for, it would be easy to save this to a csv file for storage and later analysis.


```python
df.to_csv('lynching_articles.csv')
```

This is only a small subset of the articles on lynching that are available, however. As is often the case, I'll need to make multiple calls to the API. The easiest way to do that is to define a small function for getting the article information and put that in a loop. While it isn't a requirement that you create a function for making the API call, it will make your code easier to read and debug.



```python
def get_articles(searchterm, page_number):
    '''
    Make calls to the Chronicling America API.
    Returns a dataframe of articles 
    '''
    base_url = 'http://chroniclingamerica.loc.gov/search/pages/results/'
    parameters = {'andtext': searchterm,
                 'format'  : 'json'}
    r = requests.get(base_url, params=parameters)

    df = pd.DataFrame(r.json()['items'])
    return df

# empty dataframe to store results
df = pd.DataFrame()

for page_number  in range(1,10):
    new_df = get_articles('lynching', page_number)
    df = df.append(new_df , ignore_index=True)
```


```python
print len(df)
df.head(5)
```

    180





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
      <td>batch_mimtptc_jackson_ver01</td>
      <td>[Dearborn]</td>
      <td>Michigan</td>
      <td>[Wayne]</td>
      <td>19211022</td>
      <td>None</td>
      <td></td>
      <td>1927</td>
      <td>Weekly</td>
      <td>/lccn/2013218776/1921-10-22/ed-1/seq-1/</td>
      <td>[English]</td>
      <td>2013218776</td>
      <td>["The Ford international weekly" appears with ...</td>
      <td>"Mis-Picturing Us Abroad" Introduces the Serie...</td>
      <td></td>
      <td>[Michigan--Wayne--Dearborn]</td>
      <td>Dearborn, Mich.</td>
      <td>Suburban Pub. Co.</td>
      <td></td>
      <td>1</td>
      <td>1901</td>
      <td>[Michigan]</td>
      <td>[Dearborn (Mich.)--Newspapers., Michigan--Dear...</td>
      <td>Dearborn independent.</td>
      <td>dearborn independent.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/2013218...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[]</td>
      <td>batch_iune_hotel_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19150818</td>
      <td>None</td>
      <td>LAST EDITION</td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1915-08-18/ed-1/seq-4/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>25 patriots who took into their own\nhands a l...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>4</td>
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
      <td>[Star, Sunday star]</td>
      <td>batch_dlc_dalek_ver01</td>
      <td>[Washington]</td>
      <td>District of Columbia</td>
      <td>[None]</td>
      <td>19221123</td>
      <td>None</td>
      <td></td>
      <td>1972</td>
      <td>Daily</td>
      <td>/lccn/sn83045462/1922-11-23/ed-1/seq-34/</td>
      <td>[English]</td>
      <td>sn83045462</td>
      <td>["From April 25 through May 24, 1861 one sheet...</td>
      <td>T\nTU^\nnit;\n- V E\nxl\nII\nb&lt;\nin rour\n3436...</td>
      <td>34</td>
      <td>[District of Columbia--Washington]</td>
      <td>Washington, D.C.</td>
      <td>W.D. Wallach &amp; Hope</td>
      <td></td>
      <td>34</td>
      <td>1854</td>
      <td>[District of Columbia]</td>
      <td>[Washington (D.C.)--fast--(OCoLC)fst01204505, ...</td>
      <td>Evening star.</td>
      <td>evening star.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[]</td>
      <td>batch_wa_elm_ver01</td>
      <td>[Seattle]</td>
      <td>Washington</td>
      <td>[King]</td>
      <td>19180608</td>
      <td>None</td>
      <td></td>
      <td>1921</td>
      <td>Weekly</td>
      <td>/lccn/sn87093353/1918-06-08/ed-1/seq-4/</td>
      <td>[English]</td>
      <td>sn87093353</td>
      <td>["A publication of general information, but in...</td>
      <td>DAMNABLE WHITE WHELPS\nSome of the white subsc...</td>
      <td></td>
      <td>[Washington--King--Seattle]</td>
      <td>Seattle, Wash.</td>
      <td>H.R. Cayton</td>
      <td></td>
      <td>4</td>
      <td>1916</td>
      <td>[Washington]</td>
      <td>[African Americans--Washington (State)--Seattl...</td>
      <td>Cayton's weekly.</td>
      <td>cayton's weekly.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn87093...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[]</td>
      <td>batch_iune_delta_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19130507</td>
      <td>None</td>
      <td></td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1913-05-07/ed-1/seq-12/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>TEN COMPANIES OF MILITIA PROTECT ..THE\nALLEGE...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>12</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
  </tbody>
</table>
</div>




```python
def get_articles(searchterm):
    base_url = 'http://chroniclingamerica.loc.gov/search/pages/results/'
    parameters = {'andtext': searchterm,
                 'format'  : 'json'}
    r = requests.get(base_url, params=parameters)

    df = pd.DataFrame(r.json()['items'])

    return df

get_articles('lynching')
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
      <td>batch_mimtptc_jackson_ver01</td>
      <td>[Dearborn]</td>
      <td>Michigan</td>
      <td>[Wayne]</td>
      <td>19211022</td>
      <td>None</td>
      <td></td>
      <td>1927</td>
      <td>Weekly</td>
      <td>/lccn/2013218776/1921-10-22/ed-1/seq-1/</td>
      <td>[English]</td>
      <td>2013218776</td>
      <td>["The Ford international weekly" appears with ...</td>
      <td>"Mis-Picturing Us Abroad" Introduces the Serie...</td>
      <td></td>
      <td>[Michigan--Wayne--Dearborn]</td>
      <td>Dearborn, Mich.</td>
      <td>Suburban Pub. Co.</td>
      <td></td>
      <td>1</td>
      <td>1901</td>
      <td>[Michigan]</td>
      <td>[Dearborn (Mich.)--Newspapers., Michigan--Dear...</td>
      <td>Dearborn independent.</td>
      <td>dearborn independent.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/2013218...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[]</td>
      <td>batch_iune_hotel_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19150818</td>
      <td>None</td>
      <td>LAST EDITION</td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1915-08-18/ed-1/seq-4/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>25 patriots who took into their own\nhands a l...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>4</td>
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
      <td>[Star, Sunday star]</td>
      <td>batch_dlc_dalek_ver01</td>
      <td>[Washington]</td>
      <td>District of Columbia</td>
      <td>[None]</td>
      <td>19221123</td>
      <td>None</td>
      <td></td>
      <td>1972</td>
      <td>Daily</td>
      <td>/lccn/sn83045462/1922-11-23/ed-1/seq-34/</td>
      <td>[English]</td>
      <td>sn83045462</td>
      <td>["From April 25 through May 24, 1861 one sheet...</td>
      <td>T\nTU^\nnit;\n- V E\nxl\nII\nb&lt;\nin rour\n3436...</td>
      <td>34</td>
      <td>[District of Columbia--Washington]</td>
      <td>Washington, D.C.</td>
      <td>W.D. Wallach &amp; Hope</td>
      <td></td>
      <td>34</td>
      <td>1854</td>
      <td>[District of Columbia]</td>
      <td>[Washington (D.C.)--fast--(OCoLC)fst01204505, ...</td>
      <td>Evening star.</td>
      <td>evening star.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[]</td>
      <td>batch_wa_elm_ver01</td>
      <td>[Seattle]</td>
      <td>Washington</td>
      <td>[King]</td>
      <td>19180608</td>
      <td>None</td>
      <td></td>
      <td>1921</td>
      <td>Weekly</td>
      <td>/lccn/sn87093353/1918-06-08/ed-1/seq-4/</td>
      <td>[English]</td>
      <td>sn87093353</td>
      <td>["A publication of general information, but in...</td>
      <td>DAMNABLE WHITE WHELPS\nSome of the white subsc...</td>
      <td></td>
      <td>[Washington--King--Seattle]</td>
      <td>Seattle, Wash.</td>
      <td>H.R. Cayton</td>
      <td></td>
      <td>4</td>
      <td>1916</td>
      <td>[Washington]</td>
      <td>[African Americans--Washington (State)--Seattl...</td>
      <td>Cayton's weekly.</td>
      <td>cayton's weekly.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn87093...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[]</td>
      <td>batch_iune_delta_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19130507</td>
      <td>None</td>
      <td></td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1913-05-07/ed-1/seq-12/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>TEN COMPANIES OF MILITIA PROTECT ..THE\nALLEGE...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>12</td>
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
      <td>batch_scu_garydavis_ver01</td>
      <td>[Newberry]</td>
      <td>South Carolina</td>
      <td>[Newberry]</td>
      <td>19041018</td>
      <td>None</td>
      <td></td>
      <td>1937</td>
      <td>Weekly</td>
      <td>/lccn/sn86063758/1904-10-18/ed-1/seq-2/</td>
      <td>[English]</td>
      <td>sn86063758</td>
      <td>[Archived issues are available in digital form...</td>
      <td>CONGRESSMAN AIKEN.\nSpeaks Out Openly On Impro...</td>
      <td>4</td>
      <td>[South Carolina--Newberry--Newberry]</td>
      <td>Newberry S.C.</td>
      <td>E.H. Aull</td>
      <td></td>
      <td>2</td>
      <td>1903</td>
      <td>[South Carolina]</td>
      <td>[Newberry County (S.C.)--Newspapers., South Ca...</td>
      <td>The herald and news.</td>
      <td>herald and news.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn86063...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>[Evening star, Star]</td>
      <td>batch_fu_criser_ver02</td>
      <td>[Ocala]</td>
      <td>Florida</td>
      <td>[Marion]</td>
      <td>19200323</td>
      <td>None</td>
      <td></td>
      <td>1943</td>
      <td>Daily (except Sunday)</td>
      <td>/lccn/sn84027621/1920-03-23/ed-1/seq-2/</td>
      <td>[English]</td>
      <td>sn84027621</td>
      <td>[Archived issues are available in digital form...</td>
      <td>OCALA EVENING STAR, TUESDAY, MARCH 23, 1920\ni...</td>
      <td></td>
      <td>[Florida--Marion--Ocala]</td>
      <td>Ocala, Fla.</td>
      <td>Porter &amp; Harding</td>
      <td></td>
      <td>2</td>
      <td>1895</td>
      <td>[Florida]</td>
      <td>[Florida--Marion County.--fast--(OCoLC)fst0120...</td>
      <td>The Ocala evening star.</td>
      <td>ocala evening star.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn84027...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>[]</td>
      <td>batch_iune_echo_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19120909</td>
      <td>None</td>
      <td></td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1912-09-09/ed-1/seq-4/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>IKey are openly condemning thV\nlynching of Jo...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>4</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>[]</td>
      <td>batch_mimtptc_jackson_ver01</td>
      <td>[Dearborn]</td>
      <td>Michigan</td>
      <td>[Wayne]</td>
      <td>19211022</td>
      <td>None</td>
      <td></td>
      <td>1927</td>
      <td>Weekly</td>
      <td>/lccn/2013218776/1921-10-22/ed-1/seq-12/</td>
      <td>[English]</td>
      <td>2013218776</td>
      <td>["The Ford international weekly" appears with ...</td>
      <td>12\nThe Colorado, Mother of an Inland Empire\n...</td>
      <td>12</td>
      <td>[Michigan--Wayne--Dearborn]</td>
      <td>Dearborn, Mich.</td>
      <td>Suburban Pub. Co.</td>
      <td></td>
      <td>12</td>
      <td>1901</td>
      <td>[Michigan]</td>
      <td>[Dearborn (Mich.)--Newspapers., Michigan--Dear...</td>
      <td>Dearborn independent.</td>
      <td>dearborn independent.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/2013218...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>[]</td>
      <td>batch_iune_delta_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19131014</td>
      <td>None</td>
      <td></td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1913-10-14/ed-1/seq-13/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>: forward? Pass byhalfbacIk:\nBYV'HURRY UP" YO...</td>
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
      <th>10</th>
      <td>[]</td>
      <td>batch_iune_golf_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19141013</td>
      <td>None</td>
      <td>LAST EDITION</td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1914-10-13/ed-1/seq-27/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>0\n0\nt i- HsR9 jw- i yjBi-ESifasspBBiBB\nATT'...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>27</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>[National Afro-American newspaper]</td>
      <td>batch_mnhi_funkley_ver02</td>
      <td>[Chicago, Minneapolis, Saint Paul]</td>
      <td>Minnesota</td>
      <td>[Cook, Hennepin, Ramsey]</td>
      <td>19221202</td>
      <td>None</td>
      <td></td>
      <td>1999</td>
      <td>Weekly</td>
      <td>/lccn/sn83016810/1922-12-02/ed-1/seq-2/</td>
      <td>[English]</td>
      <td>sn83016810</td>
      <td>[Archived issues are available in digital form...</td>
      <td>rJS*\n$r'\nTHE APPEAL\nAN AMERICAN NEWSPAPER\n...</td>
      <td></td>
      <td>[Illinois--Cook--Chicago, Minnesota--Hennepin-...</td>
      <td>Saint Paul, Minn. ;</td>
      <td>Northwestern Pub. Co.</td>
      <td></td>
      <td>2</td>
      <td>1889</td>
      <td>[Illinois, Minnesota, Minnesota]</td>
      <td>[African American newspapers--Illinois., Afric...</td>
      <td>The Appeal.</td>
      <td>appeal.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83016...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>[]</td>
      <td>batch_iune_hotel_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19150818</td>
      <td>None</td>
      <td>LAST EDITION</td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1915-08-18/ed-1/seq-3/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>IW5B5iB!e5SS\nTEXAS RECOVERING FROM GALE\nWHIC...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>3</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>[]</td>
      <td>batch_iune_echo_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19111128</td>
      <td>None</td>
      <td></td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1911-11-28/ed-1/seq-25/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>mgup\n-\n.\nTEXAS, WHO;DIDN"I LlYNCH. MURDERER...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>25</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>[]</td>
      <td>batch_iune_echo_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19120115</td>
      <td>None</td>
      <td></td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1912-01-15/ed-1/seq-4/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>apwagywy 'Hr?wug'iiiBjJBByp\n.and Detective Ja...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>4</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>[]</td>
      <td>batch_kyu_albatross_ver01</td>
      <td>[Berea]</td>
      <td>Kentucky</td>
      <td>[Madison]</td>
      <td>19221129</td>
      <td>None</td>
      <td></td>
      <td>1958</td>
      <td>Weekly</td>
      <td>/lccn/sn85052076/1922-11-29/ed-1/seq-5/</td>
      <td>[English]</td>
      <td>sn85052076</td>
      <td>[Archived issues are available in digital form...</td>
      <td>November so, im\nTBS CITIZEN\nTHE CITIZEN\nA s...</td>
      <td>Page Five</td>
      <td>[Kentucky--Madison--Berea]</td>
      <td>Berea, Ky.</td>
      <td>T.G. Pasco</td>
      <td></td>
      <td>5</td>
      <td>1899</td>
      <td>[Kentucky]</td>
      <td>[Berea (Ky.)--Newspapers., Kentucky--Berea.--f...</td>
      <td>The citizen.</td>
      <td>citizen.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn85052...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>[Daily Bridgeport farmer, Daily Republican Far...</td>
      <td>batch_ct_goshen_ver01</td>
      <td>[Bridgeport]</td>
      <td>Connecticut</td>
      <td>[Fairfield]</td>
      <td>19160517</td>
      <td>None</td>
      <td></td>
      <td>1917</td>
      <td>Daily (except Sun.)</td>
      <td>/lccn/sn84022472/1916-05-17/ed-1/seq-6/</td>
      <td>[English]</td>
      <td>sn84022472</td>
      <td>[Also issued on microfilm from Connecticut Sta...</td>
      <td>f\nTHE FABMER: MAY 17, 1916\nBRJDGEPOR TE VENI...</td>
      <td>6</td>
      <td>[Connecticut--Fairfield--Bridgeport]</td>
      <td>Bridgeport, Conn.</td>
      <td>Pomeroy, Gould &amp; Co.</td>
      <td></td>
      <td>6</td>
      <td>1866</td>
      <td>[Connecticut]</td>
      <td>[Bridgeport (Conn.)--Newspapers., Connecticut-...</td>
      <td>The Bridgeport evening farmer.</td>
      <td>bridgeport evening farmer.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn84022...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>[]</td>
      <td>batch_iune_golf_ver01</td>
      <td>[Chicago]</td>
      <td>Illinois</td>
      <td>[Cook County]</td>
      <td>19150227</td>
      <td>None</td>
      <td>LAST EDITION</td>
      <td>1917</td>
      <td>Daily (except Sunday and holidays)</td>
      <td>/lccn/sn83045487/1915-02-27/ed-1/seq-3/</td>
      <td>[English]</td>
      <td>sn83045487</td>
      <td>["An adless daily newspaper.", Archived issues...</td>
      <td>wmmmmzmmmmwmm\n;POLICE FEAR LYNCHING Of AURORA...</td>
      <td></td>
      <td>[Illinois--Cook County--Chicago]</td>
      <td>Chicago, Ill.</td>
      <td>N.D. Cochran</td>
      <td></td>
      <td>3</td>
      <td>1911</td>
      <td>[Illinois]</td>
      <td>[Chicago (Ill.)--Newspapers., Illinois--Chicag...</td>
      <td>The day book.</td>
      <td>day book.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn83045...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>[]</td>
      <td>batch_lu_iceman_ver01</td>
      <td>[Lafayette]</td>
      <td>Louisiana</td>
      <td>[Lafayette]</td>
      <td>19030822</td>
      <td>None</td>
      <td></td>
      <td>1921</td>
      <td>Daily (except Sun.)</td>
      <td>/lccn/sn88064111/1903-08-22/ed-1/seq-2/</td>
      <td>[English]</td>
      <td>sn88064111</td>
      <td>["Official journal of the parish",  Sept. 15, ...</td>
      <td>W tGAZETTE.\nBY HOMER MOUTON.\n.' 5Sea..wsat o...</td>
      <td></td>
      <td>[Louisiana--Lafayette--Lafayette]</td>
      <td>Lafayette, La.</td>
      <td>Chas. A. Thomas and Homer J. Mouton</td>
      <td></td>
      <td>2</td>
      <td>1893</td>
      <td>[Louisiana]</td>
      <td>[Lafayette (La.)--Newspapers., Lafayette Paris...</td>
      <td>The Lafayette gazette.</td>
      <td>lafayette gazette.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn88064...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>[]</td>
      <td>batch_wa_elm_ver01</td>
      <td>[Seattle]</td>
      <td>Washington</td>
      <td>[King]</td>
      <td>19191206</td>
      <td>None</td>
      <td></td>
      <td>1921</td>
      <td>Weekly</td>
      <td>/lccn/sn87093353/1919-12-06/ed-1/seq-3/</td>
      <td>[English]</td>
      <td>sn87093353</td>
      <td>["A publication of general information, but in...</td>
      <td>ARKANSAS METHODIST A LIAR\nThere is not much c...</td>
      <td></td>
      <td>[Washington--King--Seattle]</td>
      <td>Seattle, Wash.</td>
      <td>H.R. Cayton</td>
      <td></td>
      <td>3</td>
      <td>1916</td>
      <td>[Washington]</td>
      <td>[African Americans--Washington (State)--Seattl...</td>
      <td>Cayton's weekly.</td>
      <td>cayton's weekly.</td>
      <td>page</td>
      <td>http://chroniclingamerica.loc.gov/lccn/sn87093...</td>
    </tr>
  </tbody>
</table>
</div>




```python
def get_articles(searchterm, page):
    base_url = 'http://chroniclingamerica.loc.gov/search/pages/results/'
    parameters = {'andtext': searchterm,
                  'page'  :  page,
                  'format' : 'json'}
    r = requests.get(base_url, params=parameters)
    return r.json()

get_articles('lynching', 2)
```




    {u'endIndex': 40,
     u'items': [{u'alt_title': [u'Advocate',
        u'Mount Sterling advocate',
        u'Mount Sterling advocate the sentinel democrat',
        u'Mt. Sterling advocate the sentinel democrat'],
       u'batch': u'batch_kyu_batman_ver01',
       u'city': [u'Mount Sterling'],
       u'country': u'Kentucky',
       u'county': [u'Montgomery'],
       u'date': u'18931003',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 9999,
       u'frequency': u'Weekly',
       u'id': u'/lccn/sn86069675/1893-10-03/ed-1/seq-8/',
       u'language': [u'English'],
       u'lccn': u'sn86069675',
       u'note': [u'Archived issues are available in digital format from the Library of Congress Chronicling America online collection.',
        u'Description based on: Vol. 1, no. 28 (Feb. 17, 1891).',
        u'For a short time published as the Mt. Sterling advocate the sentinel democrat.'],
       u'ocr_eng': u'f\nI\nft A\nTHE ADVOCATE\ni\nSOUS EARNEST WORDS AS TO\nLYNCfflNCf\nEverybody can see that lynching\ngrows worse and worse Such prac\ntices are contagious Public descrip\ntion of ono caso suggests another\nwhere it might not have been thought\nof What In tho world will this lead\nto As a permanent and growing\npractice lynching must bo destructive\nof civilization Is this statement too\nbroad Think a moment and soo ii\nit would not bo so\nNow tho apology for lynching must\nlie in ono of two things Some sa\nthat our laws and our courts can 1101\nbo relied on to puuish as outragoou\ncriminals ought to bo punished\nOthers toll us that lynching will bo\nmore likely to striko terror into brut\nish criminals than the slow nud dull\nprocesses of law\nAs to tho last point I gravoly doubt\nwhothor tho view is correct Even\ntho lowest of mankind aro not brutes\nThey have some notions ol right and\nwrong something of what wo call\nconscience If you try to restrain\nsuch a man lrom great crimes only by\nfear of lynching you excite the brutish\nelements in him and do not appeal to\ntho human elements He thinks to\nhimself that if he gots caught he will\nbo lynched and ho simply rages at the\nthought and really considers himself\nas in such a caso tho innocont party\nBesides he hopes to escape Ho feds\ncunning Ho thinks maybe some other\nfollow will bo caught and lynched\nEverybody knows that this does some\ntimes happen though tho parties on\ngaged in lynching naturally cover it\nup after the mistake is ascertained\nTho tempted criminal grins to think\nhow some other fellow may bo swung\nup while ho goes free Altogether it\nis a form of punishment thatdocB not\nstrike terror certainly not so much an\nmany seem to imagine On tho other\nhand if law is properly administered\nthere is something about it that ap\npeals to the human in a tempted\nwretch I was glad to see tho Courier-Journal\nthe other day expressing\na simular persuasion The idea ot\nhaving all the facts searched out and\nproved against him having his guilt\nfully established and then having to\nwait for weeks with a knowledge\nthat at last ho will be hung there is\nreally something more terrible about\nthis than attaches to tho prospect of\nlynching\nBut th6 great trouble is people say\nthat the laws iro sometimes inade\nquate the punishment provided in not\nsevere enough and especially the\nlawyers can manage to have guilty\nmen escape if there is any money in\ntho cas Now there is somo ground\nfor this view There has been a ten\ndency in recent generations to tone\ndown tho punishment for the lightest\noffenses and to sympathize with or\npity a vile criminal as rather unfor\ntunate than guilty There is a sort ot\nsentimentality abroad in regard to\ncriminals by no means universal but\npretty widely diffused And it can\nnot be denied that some lawyers man\nago to delay a caso until public indig\nnation has subsided and then per\nhaps the guilty man may go free or\nmay encountor only a modified pun\nishment Lawyers aro like tho\nprophets fig the good arc very\ngood and the bad aro very\nbad Somo of tho noblest men\nin tho world are judges and lawyers\nbut those who yiold in early life to\ntemptations of their calling do some\ntimes become very bad men and our\nprocesses of trial designed to save\nmen from hasty condemnation and ex\ncessive ponaltics aro not unfrcquently\nmanipulated in the interest of tiie\nguilty What thou\nSuppose that some horrid crime has\nbeen committed some outrageous\narson or murder or rape Wliou\nhasty spirits propose lynching tho\nplea is always made that tho courts\ncannot bo rolled on Now suppose\nthat instead of lynching a public meet\ning should bo hold in which many\nthoughtful and judicious citizons take\npart Lot tho prima facie facts bo\nstated ia tho meeting lot a largo com\nmittee be appointed representing dif\nferent classes of tho community ac\ncording to the circumstances of tho\ncase and a smaller Executive commit\ntee lot the meeting urge and tho\ncommittee sco to it that the caso shall\nbo pushed along without any unduo\ndelay lot tho lower grado of lawyers\npercoivo that public opinion will not\ntolerate any tricks of delay let a sec\nond meeting bo hold if necessary or\nat any rato lot tho committee mako\nitsolf felt insisting upon promptness I\nff5 ti\n4MLH\nft-\nMMSS3WBB ssfft t w\n-\nand painstaking justice thus tho high\nwrought feelings of tho community\nwould And oxprcssion iu a way tend\ning to speedy results whilo yet tho\nrequisite to civilized justice would bo\nrespected and observed\nI have moutioned that tho law\nsometimes fails to impose adequate\nporialtics Tho most offensive of all\ncrimes tho one that oftencst occasions\nlynching is quite differently punished\nin difforont States By tho help of\nmy friend Judgo W O Harris I offer\ntho following facts as to tho penalty\nof rapo in tho Southorn States not\nstopping to point out tho distinction\nmado in some States according to tho\nago of tho victim In Arkansas\nFlorida Louisiana aud North Caroli\nna it is punished by doatli in Ala\nbama and Kentucky by death or con\nfinement in tho ponitontiary for life iu\ntho discretion of tho jury in Georgia\nMaryland Missouri Tenuesseo Texas\nVirginia and West Virginia by death\nor confinomont in the penitentiary for\na term of years in tho discretion of\ntho jury in Mississippi by confine\nment in tho ponitontiary for lifo in\nSouth Carolina by confinomont in the\npenitentiary for a term of not less than\nton years Now it any think that in\ntheir State the penalty is inadequate\nas I mudt confess I think in regard to\nseveral of tho cases mentioned lot\ntlieni earnestly work with tho next\nLegislature That may do a groat\ndoal and can not do any groat harm\nBesides tho goneral ovil of lynching\nupon which I will not enlarge there\naro two special evils appertaining to\ntho practice in our Southern States\nI write as a Southern man having\nspent my life successively in Virginia\nSouth Carolina and Kentucky We\nSouthorn whito people aro trying to\ndeal with tho most formidable prob\nlem that civilizod mankind over had\nto face Besides a great man ignor\nant white people we have this mighty\nmass of colored people We must not\nforgot that tho negroes differ widely\namong themselves having como from\ndifferent races in Africa and having\nhad very different relations to the\nwhito peoplo while held in slavery\nMany of them aro greatly superior to\nothers in character but tho great mas\nof them belong to a very low grado of\nhumanity Wo have to deal with\nthem as best wo can whilo a largi\nnumber of other whito people stand off\nat a distance and scold us Not a few\nof our fellow citizens at the North feel\nand act very nobly about the matter\nbut the number is sadly great who do\nnothing and seem to care nothing but\nto find fault Now from the very na\nturo of tho case most of tho crime\nwhicli among us have been punished\nin this epidemic of lynching have been\ncrimes committed by negroes Whon\na negro murders a whito man or out\nrages a whito woman or child it ex\ncites in ns a wrath all tho greater bo\ncause of race feeling Every white\nperson in the United States would feol\nthe same way if placed in the same\ncircumstances except somo few who\nfancy they ought not to feel so and\nthen persuade themselves that they do\nnot If a negro brute outrages a white\ngirl maybe a child it stires in us\nwhite men a wrath almost intolerable\nBut hero we are with theso great and\nfrightful difficulties confronting us\naim imperiling civilization Now if\nwe go on lynching and when North\nern newspapers complain wo merely\nrake up cases of Northern lyuchiugs\ntoo and defend ourselves by saying\nyou aro another wo tend to repress\nthe sympathy of the better class of our\nNorthern follow citizons If we can\nstop the lynching and administer\nprompt etern but real and civilized\njustice then tho people at tho North\nwill moro and more sympathize with\nour grave difficulties and in many\nwaya will help us as wo strive to over\ncome them\nBesides thero is tho public opinion\nof tho colored peoplo themselves\nSomo of the educated negroes are\ndemagogues and it would bo wonder\nful If they wore not it would bo a now\nreason for questioning tho unity of tho\nhuman raco if t hoy wero so different\nfrom tho whito tolks But there is a\ngoodly number of intelligent negroes\nwho really tako sound and wholesome\nviews of tho situation If wo con\ntinue to tolorato lynching with black\nmen tho victims in ulno cases out of\nton wo lead theso better nogroet to\nthink that wo aro enemies of all thoir\nraco If wo do not provoke raco con\nflicts wo alionato tho better class from\ntho tho support of justlco and govern\nment and civilization\nNow then I appeal to thoughtful\nmen wherever tho Courier Journal is\nread will you not como out and con\ndemn this business of lynching Will\nyou not openly discourage and oppose\ntV\nii\nthis on duty Ia it not high time\nI respectfully ask editors of other\npapers to publish somo part of this\narticle or to write something about\ntho matter I ask intelligent people\nall over tho South to reflect upon tho\nsubject to tono up public opinion by\ntheir conversation Men and women\ntho thing is wrong and getting wortc\nand tending to bo ruinous L pray\nyou thiuk speak out act in such waj s\nas you deem wisest\nI will not apologize for publishing\nthis respectful appeal Aa a minister\nof religion I tako no part in tho mani\npulations of party politics though\ncareful to vote at every election sinco\nvoting is surely ono of tfio highesi\nduties of an American citizen But\nthis is in no sense a quostion of party\npolitics It is a quostion of justice of\nfundamental right of essential civili\nzation oi humau wclfaro Dr John A\nBroadus in tho Courlor Journal\nLAND STOCK AND CROP\nIn Clark county J B Wilson sold\nhis 100 acro farm near Hunt to Jas\nIteid and Jas Bubank for 3200\nTho prico of wheat is advancing\nslowly and will advance moro rapidly\nas tho money market becomes easier\nAt Flomingsburg court about 200\nmulo colts wero on tho markot rang\ning from 15 to 50 Mountain cattle\nsold at 1 to 2 cents Good horses at\ngood prices\nAt Lancaster court thero was good\ntrade in mule colts but llttlo doing in\nother branches of trade 10 mare\ncolts sold at 55 to 75 a bunch of\nhono colts at 30 to 35\nRobert M Hunter has shipped 1500\nbuBhols of Jessamine clover seed bought\nfor Cincinnati parties eugaged at 5\nper bushel Since the decline in seed\nho has purchased about 500 bushels\nmore at 450 This same seed will\nfind its way back to the county next\nspring at about 750 per bushol\nJessamino Journal\nAt Wichita Kan iu tho district\nCourt Judge Reed declared tho eight\nhour law uuconstitutoinal Judgo Rend\nis the first Disirict Judgo to pass on\nthe act Hs holds it contrary to both\ntho State and the federal Constitutions\naud declares it a restraint on tho liber\nties of action Claims aggregating\nhundreds of thonsauds of dollars for\novertime under this act are ponding\nagainst every county and city in tho\nState\nThe total amount of tho Tribble land\nsale in Madison was 130000 The\nhome place ot 5G61 acres and one of\ntho finest homes in the county sold to\nJ Stono Walker for 6205 Terrell\npuee 295 acres J S Walker 58\nPeter Trioble place 561 acres D M\nChenault 54 Estill placo 198 acres\nJ W Bales 5035 Rayburn placo\n9SJ acres J B Parks 5990 Ballew\nplace 124 acres G W Ballew 2210\nMiller place 335 acres II B\nham 65 placo of 68 acres C D\nChenault at 80\nS F Stone bought of J B Durham\none bunch of cattlo at 1 cents and\none lot of late lambs at i\ncents W J Lacy of near\ntown sold to J W Hughes of\nFlat Creek four 1200 lb feeding steer\nfor 174 Geo A Vice bought\nfor his firm Boyd Hcndrix Co of\nnear Sherburne 9 fat steers averag\ning 950 pounds at 235 por hundred\nof John W Corbin of near\nvillo JJ Crain of Hillsbor\nbought tho following 4 fat oxon of\nJohn B Darnell of White Oak at\n250 and 275 per nundrcd bound\n2 fat axon at Wyoming of Mrs L\nDorian of this place at 3 por 100\npounds Owingsvillo Outlook\nAFTER AWHILE\nAfter awhile\nThe sorrow that pain us will melt in a smile\nAnd tho horse that we bet on will win ererj\nmile\nAfter n whiles\nAfter awhile\nTho Are will blaze and the kettle will bile\nAnd cash will como in like a millionaires\nMr awhile 1\nAfter awhile\nLet no ono tho song I am slngingrerilol\nWell hitch up and gallop to glory in style\nAfter avrhlli\nAtlanta Constitution\nnow sad to our hoarte aro some econcs t our\nchildhood\nAs our recollections present them to viow\nTho use of tho switch that was brought from tho\nwildwood\nAnrijvarious punishments moat of us know\nHut saddest of all is the thought of tho pill box\nThat mother brought out when sho thought\nwe wore ill\n01 tho griping the aching tho twisting and\ntorment\nWrapped up in tho horrlblo old fashioned pill\nBut thats all dono away with To\nregulato tho stomach livor and bowolp\nDr Piorcos PolIotB excel Youll ox-\nporionco no pain nodiscomfortnobad\nand stop It Wo can stop it Is not as peppermint drops\n-\nK11\nfa - 1WifcjJJJJ nTl V l spUV\nfl\nSEND\nFIFTY\nCENTS\nrOR A TRIAL MONTHS SUBSCRIPTION\nTOTMK\nLouisville -Times\nTHE BRIGHTEST AND BEST AfTMHOON\nPAPER IN THE SOUTH\nLatest Market Quotations\nLatest State News\nAll the Local News\nComplete Press Reports\nLARGEST CIRCULATION IN THE SOOTH\n30000 AND OVER DAILY\ncso obiwts a MorvMar\nOr 9soo a Yr by Malt\nJNOi A HALDEMAN Bus Manaoer\nbos Fourth Avenue\nLOUIOVILLE KENTUCKY\nWe handle\nGas H eat\ning an d\nCo o k i n g\nStovesand\nare prepar\ned to do all\nkinds o f\nplumb i n g\nwork\nMt Sterling Gas and Electric Co\nTABLES\nUCKEYE\nOINTMENT\nCURES NOTHING BUT PILES\nA SURE and CERTAIN CURE\nknown for 15 years as tho BEST\nREMEDY FOR PILES\nFrtpirad by ntCJUKDSOS HXD1CIIB CO 8 LOUIS\nyjONUMENTQ\n2 Iyr\nOF EVERY KIN\nMade and set up in all\nW A 1 0\nmm\n43\npart\nthe country\nWrite for Designs\nNo Agents Employed\nminoaoun\nIlroadway Lexington K\nnnn\nASSIGNEES\nQAT1\nV\nAs Asigncc of J C II Gilllspic I will sell on\nWednesday October 11 18KJ at the Jerry North\ncutt plnce about two miles Eat of Plum Lick\nthe following property\n1 bay horse\n1 buggy and set of harness lap robes rain\naprons etc\n1 organ\n1 dicsscr\n1 roller\nlsledd\n1 randttll harrow\n1 buckeye mower\nI corn shelter\n1 cutting box\n1 crois cut saw\nAbout 10 acres of corn in field and placo to\nfeed\nAlso about 10 acres of corn in tho shock and\n10 acres of oats in tho rick on the land of Calvin\nGillispie Sr no place to feed It\nI will also at the same tfmo and place rent 70\nor 80 acres of grass\nAll sums of 10 and under cash in hand\nAmounts over J10 a credit till January 1st 180\nnotowith approved security\nSnlo to begin at 10 a m\nC UiLLSiriKjiu Assignee\nW II Flktoubk Auctioneer 10 Jt\nBROWNS IRON BITTERS\ncures Dyspepsia In\ndigestion Debility\nHORSE AND TRACE\nThe groat trotting meeting of Ken\ntucky begins next Saturday at Lox\niugton\nTho trots at Lexington beginning\nnext Saturday promises to bo ono of\ntho best of tho year Thero is 19 en\ntries in the Transylvania 2000 stake\nwith records from 2 11 to 2 17 Tho\nfree-for-all trot is Jikoly to havo Direc\ntum Alix Pixjoy and such horses and\nwill bo tho raco of tho scasos\nJohn Dlckorson drovo Arlon a half\nin 1 0ii at Torro Haute laBt wcok\nMr A L Hackott loft for tlin\nresults Children tako thorn as readily Sothcrn Baptist Theological Seminary\nMonday to resume his studicp\nft\ng TSBXIKaAPVOOATE TDOESDAJT OCTOBER 3 1893\ndm\nm\nSsSK\naSw\nffiV\na\na-\nilfi\nLa\nMB S1B4M\nINSURE YOURv\nTOBACCO\n0Oeeee80e0see0eeee\nWITH-\nBAIRD WM\nDo you want to make a safe investment\nwifli Uipiiljiuimiflii iiiiMlipiiipwiipniiiipii rnjjrnc\nIn order to have funds to meet my\nindebtedness falling duo will soil\ntor 30 days my entira stock of\nDRY GOODS SHOES CLOAKS\nEtc at\naro requested to make prompt\npayments\ni\ni\nit\ni Cost for Spt Cast i\n-\nV\nWill also soil to my regular custom\nvia uu buiiiu terms s oetoro\nStock full and complete Como\nearly and mako your selection\nAll persons owing July accounts\nrfAiiiiAindlliiiAilmaidAMjtrlmtuiih mJ\nCHOICE SEASONABLE GOODS GOES\nMy Stock consists of Clothing to suit the\ntrade SUITS of CLOTHES which brought a\nprofit early in the season now goes for even less\nthan the cost of manufacturing PANTS COATS\nand VESTS single or together at cut prices\nThen I would ask you to see my novelties in\nShoes They are being sold at what the people\ncall in these times of money stringency baigains\nBmNlBHrN G GOODS\nEverything jdesirable for a little money\nCall at the -\nm york p Annan store\nJACOB GORDON\n24 South Maysville St Mt Sterling Ky\nKSiH\nUpgniMSMsg MMMMmMmmmmm\nJOHN SAMUELS\nEMMSMMS ggggpgggj\nACADEMY FOHlYOUNQ LAO IBS AND LITTLI CURLS MT STMLINO KV\nSMHMMlMMMMMSMMMg\nwiirlbm life mfr ids h fomhJbh\nA\nJIrihJnLiWfiliimiliiiifcnrK\nIf sor put your money\nin the\nNATIONAL HOME\nSaildiiYjj aad Loai jssocitiaiY\nOno of the oldcBt and largest companies in tho world 15000\nnow sold in Mt Sterling Call and examine my plans\nJAMES R WILSON\nTylor Apperson Bldg Mt Sterling Ky\nJ3 v i VJMauuiywiuijii JiuymiuyHigi uimjjiMiFuuianiMiiawuiiiaBjiiu MTBinnii u ifwiiyji g\nMMMfMMmMMsMMM MMMMSMMMMM\nCLOSING SALE OF DRY GOODS\nF\nTho first term will begin Sept t 1893 Academlo and College Proparatorj Courses Primary\nuvjutiiuiuuv ji uiiiiiuu mimucr o uoaraing pupils\nFor terms or other information address Miss 8 M Lewln untl August 15 Hyattsville\nAujuet IBiMt\nutter Sterling Ky\nto 8m\n3\ni\nJfrMl\nv\nIx\ne yKj\nSB\nv',
       u'page': u'',
       u'place': [u'Kentucky--Montgomery--Mount Sterling'],
       u'place_of_publication': u'Mt. Sterling, Ky.',
       u'publisher': u'Harris and Mason',
       u'section_label': u'',
       u'sequence': 8,
       u'start_year': 1890,
       u'state': [u'Kentucky'],
       u'subject': [u'Kentucky--Montgomery County.--fast--(OCoLC)fst01220017',
        u'Kentucky--Mount Sterling.--fast--(OCoLC)fst01220016',
        u'Montgomery County (Ky.)--Newspapers.',
        u'Mount Sterling (Ky.)--Newspapers.'],
       u'title': u'The Mt. Sterling advocate.',
       u'title_normal': u'mt. sterling advocate.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn86069675/1893-10-03/ed-1/seq-8.json'},
      {u'alt_title': [],
       u'batch': u'batch_vi_chopin_ver01',
       u'city': [u'Williamsburg'],
       u'country': u'Virginia',
       u'county': [None],
       u'date': u'19030711',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1929,
       u'frequency': u'Weekly',
       u'id': u'/lccn/sn86071985/1903-07-11/ed-1/seq-7/',
       u'language': [u'English'],
       u'lccn': u'sn86071985',
       u'note': [u'Also issued on microfilm from Virginia State Library.',
        u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.',
        u'Publisher varies: W.C. Johnston, <June 9, 1893-Aug. 4, 1894>; L.S. Cottrell <Aug. 10, 1894-Feb. 29, 1896>; W.C. Johnston, <Mar. 7, 1896-June 28, 1917>; Robert P. Scott, <July 5-Aug. 8, 1917>; Record Publishing Corp., <July 8-Oct. 14, 1920>; W.C. Johnston, <Oct. 28, 1920-Dec. 8, 1921->',
        u'Several issues have supplements.'],
       u'ocr_eng': u'I\nHE KING\n.. ONE\nA\n\'\n- **\nr** ic c\n.\n1\nI\nI\n?\nI\n?\nI\n.\ni\nr\n.\nI\n?\nt\n?\nc\n|\n.\ni\nbal I\nJ .lo."\nit v., : a hollow :iii ti\nik at all. 1.\nimf.\nrat!\ni M i\n. inkle\n.ty o\nil\nthu\n"Hraaapb!\'\n"M\n1 would 1\n?\'Oil, jea, I know," Loring said.\n; or- .\nEY 1: ^D\nCJ^\n?\nIOM.\n?\n\'\n?\n?\n?\nI\nI\n:\nur.\n"1 .\n?\n?? * I\nil3 au\n. nt tbs ti\nI men ua*l beau cuuuecte-l, aa ba ai\ntranaa*.\n?\n?k a ?\n?\n? ?\n\'\n-\n?\n?\n?\nwo ?\nearlier tbau Xokouasaa t.\nMADE A GOOD TALK\n?coker Washington Says lynching I\nmal\ni\nSPF: R V *0 EDITORS\n>r Ad Jr\nlmm-n*e e and Coumeete\n?\nf\n?\ni\n1\n1\niv?\nie\nbb\nTralfw Waraagflr Ri alfa\ni\n*aa Th\nI been semi to Oe narai On*-fee for ec\nm-4o?.* \'tOf?"Lf>\n\'-et taree\n?tm l aa effer of\ne\n-\n.?wm ma..\nTh\ntba .^leeii aaa 90\n)ID DOMINION NEWS.\nai\nTB\nn\n. em\nPi\nDr\n\'\nai\n?aa.\nI lae\n-\n-\nwaa\near s?BasHsMs?sHsisMsHslBBBl\nbmert.\n&\n.',
       u'page': u'',
       u'place': [u'Virginia--Williamsburg'],
       u'place_of_publication': u'Williamsburg, Va.',
       u'publisher': u'Johnston & Mills',
       u'section_label': u'',
       u'sequence': 7,
       u'start_year': 1893,
       u'state': [u'Virginia'],
       u'subject': [u'Virginia--Williamsburg.--fast--(OCoLC)fst01205036',
        u'Williamsburg (Va.)--Newspapers.'],
       u'title': u'Virginia gazette.',
       u'title_normal': u'virginia gazette.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn86071985/1903-07-11/ed-1/seq-7.json'},
      {u'alt_title': [u'Daily Los Angeles herald',
        u'Herald',
        u'Los Angeles Sunday herald'],
       u'batch': u'batch_curiv_oasis_ver01',
       u'city': [u'Los Angeles'],
       u'country': u'California',
       u'county': [u'Los Angeles'],
       u'date': u'19060429',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1911,
       u'frequency': u'Daily',
       u'id': u'/lccn/sn85042462/1906-04-29/ed-1/seq-44/',
       u'language': [u'English'],
       u'lccn': u'sn85042462',
       u'note': [u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.',
        u'Available on microfilm from Custom Microfilm Systems.',
        u'Issue numbering dropped with Apr. 2, 1900 issue; resumed with July 14, 1900 issue.',
        u'Master negatives are available for duplication from:',
        u'Sunday issues include: Illustrated Magazine (title varies), <July 7, 1901-Oct. 19, 1902>.'],
       u'ocr_eng': u'It j/aj George yNO missed m\ne/CfJT c/O/C^/\n"Yo\\ Eph\'um. git right outer dat sun!"\nv; :*Why. mammy?" .,..:.\'.,\u2022\n:;; v ** Case I don 1 want yo\' all gettin\'sunburntP\'-\n. f"Yaas. missy. Ise so old dat 1 expects dey\n, ain\'t got etiuf figger in de alfybet to mek my\n\u2022\u2022What\'s the extra about. little boy?" \'\n\u2022\u25a0\' \u2022\u25a0-.\' "How do I know\u2014 l ain\'t got time to read\npapersl" ;\n"Say, Rastus. here\'s a dandy tree ! Jest go\nan\' steal your ma\'s clothesline an\' you kin play\n\u2022lynching\' with us! 1 \'\n\u25a0 WM^ 0^\nShe: "Pap sez yo* can\'t suppo\'t me In de\nstyle I\'se accustomed to;" :\n: He: "Hun. I guess I kin accustom yo* to de\nstyle yo\'ll live liirv :\n\u25a0 Aisltor: "How many Inhabitants are In this\ntown?"\nSmall native: "Me and a hundred and sixty\nseven others?" \u2022\x84,....\n"Did you leave your job -on account of\ncigarettes?- \u25a0\u2022.".\u2022\'.-:\u2022 ; :\n\'\u2022Yep. De doss wus allus a-borrowla* \'em\noff me!" .\'\u25a0 .-. \\. .\u25a0:.:\u25a0\u25a0 :\u25a0\u25a0;\u25a0 .:.:.;-::\n"We struck for shorter hours\u2014\n"Get \'em?" \' \'\u25a0"\n"Sure! We ain\'t workln^at all. now!\'',
       u'page': u'',
       u'place': [u'California--Los Angeles--Los Angeles'],
       u'place_of_publication': u'Los Angeles [Calif.]',
       u'publisher': u'Herald Pub. Co.',
       u'section_label': u'',
       u'sequence': 44,
       u'start_year': 1900,
       u'state': [u'California'],
       u'subject': [u'California--Los Angeles County.--fast--(OCoLC)fst01205194',
        u'California--Los Angeles.--fast--(OCoLC)fst01204540',
        u'Los Angeles (Calif.)--Newspapers.',
        u'Los Angeles County (Calif.)--Newspapers.'],
       u'title': u'Los Angeles herald.',
       u'title_normal': u'los angeles herald.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn85042462/1906-04-29/ed-1/seq-44.json'},
      {u'alt_title': [u'Daily journal', u'Pensacola news-journal'],
       u'batch': u'batch_fu_fourteen_ver01',
       u'city': [u'Pensacola'],
       u'country': u'Florida',
       u'county': [u'Escambia'],
       u'date': u'19090418',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1985,
       u'frequency': u'Daily',
       u'id': u'/lccn/sn87062268/1909-04-18/ed-1/seq-5/',
       u'language': [u'English'],
       u'lccn': u'sn87062268',
       u'note': [u'Also issued on microfilm from Bell & Howell, Micro Photo Division and the University of Florida.',
        u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.',
        u'Description based on: Vol. 3, no. 147 (Nov. 29, 1900).',
        u"In March 1897, the Pensacola (FL) Journal [LCCN: sn87062268] was started as a weekly by M. Loftin. The Pensacola Journal became a daily in 1898. With competition at home in Pensacola, the newspaper's coverage included a large part of Florida. In 1922, the Pensacola Journal was purchased by John H. Perry, who in 1924 purchased the newspaper's rival, the Pensacola (FL) Evening News. For the next six decades, the Pensacola Journal continued to appear mornings and the Pensacola News evenings. Both newspapers remained extremely competitive. A combined Sunday edition published as the Pensacola (FL) News Journal [LCCN sn00059018] also became available. A consolidated newspaper, also known as the Pensacola (FL) News Journal [LCCN sn87062269] continued operations beginning June 3, 1985. This daily continues to this day (ca. 2008). Pensacola, Florida is the seat of government for Escambia County (FL); the western most city in the western most county of Florida. The city sits on Pensacola Bay, connecting to the Gulf of Mexico. Since the early colonial period, Pensacola had been an important naval port and economic center. By 1889, the city was already one of Florida's four largest cities. In 1890, Pensacola numbered 11,750 souls. The big news of 1898 was the beginning of the Spanish American War. Pensacola's Gulf Coast port, like others in Florida, hosted forces bound for Cuba, among them Teddy Roosevelt who passed through Pensacola on his way to Cuba through Tampa. Throughout the first decade of the 20th century, Pensacola experienced tremendous growth. By 1900, Pensacola had grown to 17,747 citizens. And, it increased another thirty percent by 1910, to 22,982 citizens. During the decade, following the Spanish American War, the United States of America secured the peace it had won over the Spanish in the Caribbean through naval patrols and maneuvers based out of Pensacola. From 1908 onward through the mid-century, Pensacola experienced urban expansion. 1908 saw the completion of a new Spanish Renaissance City Hall. 1910 witnessed the completion of the American National Bank building; at 10 stories, Pensacola's first skyscraper. 1910 also marked the construction of the San Carlos Hotel and a wooden bridge across Bayou Texar. By 1912, Pensacola had 21 miles of paved streets. Pensacola's first modern hospital, Pensacola Hospital, opened in 1915. And, in 1916, the city began operating its first motorized fire truck, and, had replaced all of its horse-drawn firefighting equipment by 1924. Since then through World War II, the history of Pensacola has been one of boom and bust. It underwent renewal and renewed growth following World War II.--E. Kesse, University of Florida Digital Library Center.",
        u'Merged with: Pensacola news and Pensacola news-journal, to form: Pensacola news journal.',
        u'Sunday ed.: Pensacola news-journal.'],
       u'ocr_eng': u'11\nTHE PENSACOLA JOURNAL SUNDAY MORNING APRIL 18 1909 S\nMRS M PAUL\nDDIES OF BURNS\nDRESS CAUGHT FIRE AT HER\nHOME AT FERRY PASS SEV\nERAL DAYS AGO AND SHE WAS\nSO BADLY BURNED THAT\nDEATH RESULTED\nMrs Martha Paul residing at Ferry\nPass died at her home there yester\nday from the effects of burns which\nshe received several days ago Mrs\nPaul was standing near the fire at\nher home when her dress caught fire\nand she was unable to extinguish it\nbefore she was fearfully burned\nThe funeral is to occur at Whit\nmires cemetery this morning at 10\nclockDeath\nDeath of Chas Burgman\nChas Burgman a citizen of Pen\nsacola for the past thirty years died i\nyesterday at his home on East Inten\ndencia street after a lingering illness\nlie had reached the age of 50 years\nand had been confined to his room\nfor several months suffering with\ntuberculosis\nThe funeral services are to be con\nducted at v the family home No 326\nEast Intendencia street this morn\ning at 9 oclock and the interment\nwill be In St Michaels cemetery\nFuneral of Infant\nThe infant son of Walter Bean\ndIed at Myrtle Grove Friday and the\nremains were placed at rest in the\ncemetery there yesterday afternoon\nSenate Held Session\nBut Many Were Absent\nContinued From First Page\ncarried Recess of five minutes was\ntaken\nRoll call then showed eighteen pres\nent To keep quorum Beard moved\nto allow no senator to leave room\nPresident Hudson ruled motion out\nof order\nHenderson moved to waive rules\nand consider local bills only Carried\nSenate bill No 91 Massey validat\ning election held at Oakland March\n29 1909 Adopted\nSenate bill No 61 Humphries vali\ndating paving contracts made by Bra\ndentown Adopted\nNo 60 Humphries legalizing bond\ning election held at Sarasota Dec 1\n1908 Adopted\nHouse bill No 20 Stokes of Es\ncambia relating to improvement by\ncity of Pensacola of its streets alleys\nand public ways Adopted\nThe senate then reverted to regular\norder\nNo 64 Harris authorizing state\nboard of health to employ sanitary\nengineer Adopted\nNo 63 Harris authorizing state\nboard of health to acquire and main\ntain state tuberculosis sanitarium I\nAdopted\nHarris Introduced senate bill No I\n172 for reapportionment\nAdjournment was taken until 10\noclock Monday\nGOOD MEETING\nOF MERCHANTS\nASSOCIATION WILL COOPERATE\nWITH AND ASSIST THE CARNI\nVAL ASSOCIATION IN ITS LAU\nDABLE EFFORTS\nI\nThe Retail Merchants Association\nheld a largely attended regular meet\ning last Thursday night and many\nimportant matters discussed that will\nbe deemed to the citys good\nResolutions were adopted that the\nassociation cooperate ami assist the\nCarnival Association in the benefi\ncial cause The membership is in\ncreasing\nBASEBALL AT FORT\nBARRANCAS THIS AFTERNOON\nTeams from the cruiser Montgom\nery and torpedo boats will play a\ngame of baseball this afternoon at\nFort Barrancas\nThere is considerable rivalry be\ntween the two teams and a good game\nis expected\nHAD PLEASANT VISIT\nThomas Anderson after a pleasant\nvisit of several days with his son\nDeputy Marshal L J Anderson left\nfor his home in Mobile yesterday\nmorning\nInvestigation by Grand\nJury Ends Man Arrested\nContinued From First Pane\ning any of the parties who were ac\ntively engaged in the lynching and\ntherefore we have been unable to re\nturn any true bill in this case\nIn connection with the lynching of\nLeander Shaw we desire to state\nthat he was secured frum the jail\nonly by reason of the sheriff and his\ndeputies being overpowered by a large\nbody of men we feel that it is but\njustice to Sheriff Van Pelt to say that\nI lie did everything in his Dower to pro\ntect his prisoner and to protect the\njail and that it was through no fault\nof his or any lack or care upon his\npart that Shaw was taken from the\njail and lynched\nLYNCHING OF ALEXANDER\nWe also investigated the lynching\nof Dave Alexander which occurred\nin this city on the 5fh day of April\n1909Alexander\nAlexander was confined in the city\njail upon the charge of having killed\na policemanon the Saturday night\nbefore During the day Sunday there\nwas no commotion or excItement over\nJ\n3\nfrI\nj\nI Wi A71 b\nif11i i Jw\nt p19J y\nGREAT MUSLINS\nI S UN D i RWEA R 54 L E\nThis annual Muslin Underwear Sale of ours has grown to such great importance that the public have been waiting and watching\nfor the announcement\nWe have received many inquiries as to when we would have it We have waited until this time so as not to interfere with our Easter\ntrade We Are Ready Now Monday you will find here the following great values\nNIGHT GOWNS 7Sc Underskirts 49G 100 GOWNS 69o Combination Suits\nMade of good cambric trimmed with Made of good Cambric nicely trimmed Made of Nainsook and very fine quality Big assortment of French combination\nembroidery and lace cut very full Regu with embroidery and plain former price of lawn former price 100 Suits made of finest Nainsook and long\nlar 75c\nprice 75 cents cloth prices\nMONDAY 39c MONDAY 49c 1 YOUR CHOICE 198 to 6QQ\n2 GOWNS AT X149 DRAWERS 1G 150 Underskirts 98c 150 HandMade Drawers 98c\nMade of very best Nainsook and long Made of Cambric and long cloth trimmed\ncloth low neck trimmed elegantly in Made of Cambric good soft finish made Good quality of Nainsook circular cut\nembroidery and lace former price 200 nicely and cut full extra good value with lace and embroidery your choice and deep ruffles 150 value\nMONDAY 149 MONDAY 19c MONDAY 9SG MONDAY S3C j\nPlease consider these are only a few of the many bargains to be had in this department We hare mentioned these to give you an idea of the values we are offering for Monday\nMAIL ORDERS OUTFITTERS\nFILLED TO WOMEN\nIE r\nthe affair There were no demonstra\ntions on the part of the public that\nwould indicate that a lynching was\nabout to occur and in fact there was\nno popular uprising ff the public in\nI this case as there was In the Shaw\ncase In fact those who participated\nin this lynching could hardly be call\ned a mob There were not more than\ntwelve nor less than seven people iii\ngaged in this seond lynching accord\ning to the best information that we\nhave been able to get From our in\nvestigation it seems that a body of\nfrom seven to twelve men went to the\n>\nTHE UPTODATE SHOP\nTHIS\nNE p i I\niP\nc1tiri I T\nSTORE\nr 3i\nIs headquarters for the W\nfar famed\nI\nSTEINBLOCH ifrj4 h IW L\nv \xb0\nCLOTHES i I\nI i a\nFor Particular MenThe\nk44\ns j\nThe New Straw HatsThe t r r\nNew iesHoseShirtsEtc iI\nI\nIn fact our assortment will make\na new man of you Theyre better than i\na spring tonic\nA s Hetiry Wh teBro\nCorner Palafox and Intendencia\ns 1 =\ncity jail and overpowered Officer Mur\nphy and Officer Simpson who were\nin charge took possession of the jail\nkey went in and took this man Alex\nander out and hanged him in the\nsquare and then dispersed and left\nafter having shot a few volleys into\nhis body\nNO JUSTIFICATION OR EXCUSE\nWP have investigated this case very\nclosely and very thoroughly because\nin our judgment there was no justi\nfication or excuse in any sense for\nthis lynching The citizenship of this\ncounty were not aroused in this case\nand the lynching was evidently per\npetrated by a few individuals who\nassumed to act nstead of leaving the\nmatter to go ttefore the court We\ncondemn this lynching in the most\nsevere terms There was absolutely\nno excuse whatever for its perpetra\ntion and it is a blot upon the good\nname of Escambia county rnd the city\nof Pensacola that such a thing can\noccur in our midst and we feel that\nit is the duty or every officer of this\ncounty and city and everycitizen as\nwell to lend any aid that they can\nin ferreting out the parties who were\nengaged in this lynching and they\nshould be apprehended prosecuted and\npunished for an offense for which\nthere was no excuse and one wliich\nbrings our city and county and citi\nzenship into disrepute\nTHE COUNTY JAIL\nWo have examined the county jail\nin this county and find that the sani\ntary conditons are not good the plumb\ning is very much out of repair both\ndownstairs and upstairs there are\nnot sufficient bathtubs and accommo\ndations for the great number of pris\noners confined the floors in several\nplaces are of metal and have rusted\nand broken through and are in a dan\ngerous condition We found some ot\nthe cells bunks hammocks and bed\nding in clean and neat conditions\nOthers we found just the reverse and\nfrom our inquiry we came to the con\nclusion that any prisoner by proper\ncare and some idea of cleanliness him\nself could have and did have a clean\nand healthy place to stay in and\nclean sanitary bedding to sleep up\non while those who did not have a\nsufficient interest or pride to keep\ntheir cell and bedding Clean were us\ning soiled bedding and were other\nwise unclean and unsanitary about\ntheir quarters\nWe found that the prisoners were\nwell fed and cared for that a suffi\nciency of good wholesome food is\ngiven them We were at the jail\nwhen their dinner was being prepar\ned and it was clean wholesome and\nplentiful\nWhere a large number or people are\nconfined together as is the case in\nthis jail it is absolutely necessary that\nevery sanitary precaution be taken in\nI order to protect health and to that\nend we desire to make the following\nreccmmendations\nSOME RECOMMENDATIONS\nr That the plumbing and sewerage\nl over the building be fixed at once\nThat sufficient bathrooms or bath\ntubs be provided in order that the\nprisoners may and shall be kept clean\nand healthy\nThat at least two shower baths\none for the whites ana one for the\ncoloreds be installed\nThat they promulgate and enforce a\nrule at the jail that the bedding etc\nof the prisoners be washed at fre\nquent stated periods and\nThat the trusty and misdemeanor\nprisoners be assigned this duty in or\nder that the county be put to no ex\npense therefor\nThat the county commissioners di\nrect the county physician to visit tie\ncounty jail at least once each week\nand investigate its sanitary condi\ntion and see that the jail is kept dis\ninfected and that the rules for health i\ncleanliness and sanitation be enforce\nThere should be in the jail a padded j\ncell for the care of lunatics who are I\nkept there for two or three days ate\na tim necessarily awaiting their\ntransportation to the asylum\nThere is some broken plastering In\nthe jail and this should be repaired\nat once unless the idea which is now\nunder consideration for the erection\nof a new jail is carried into effect\nand in this connection we desire to go\non record as saying that is our judg\nment the present jail quarters are too\nsmall that will take considerable\nmoney to enlarge the pJsent jail and\nthat even with this done a satisfac\ntory jail and quarters could not be\nhad from the present structure we\nare therefore in favor of and recom\nmend that the county commissioners\ncause to be erected on the Jail lot\nand the lot adjacent thereto which\nhas recently been bought by the coun\nt a commodious coun y jail and coun\nty criminal court building they lave\nample lot space and we think that\nthis building when erected should be\nconstructed with a view to the needs I\nof this county not only now but\nIn the future when we know it will\nbe much larger in population and\nwhen there will necessarily be more\nbusiness in the criminal court ami\nmore inmates in jail\nWhen this building is constructed In\nour judgment there should be a com\nmodious jail with separate and dis\ntinct apartments for races and sexes\nthere should be some accommoda\ntions for the jailer and suca help as\nhe may have assisting him and the\ncourt building should be so construct\ned as to furnish an office or quarters\nfor the judge solicitor clerk sher\niff and other officers of tnut court\nand the criminal court room and the\njail should be connected together by\nsome steel bridge or other suitable\nstructure whereby the prisoners could\nbe conveyed from the jail to the court\nroom without taking them In the open\nwhere there is a possibility of es\ncape\nPOOR FARM\nA committee of our body visited\nand inspected the poor farm They\nfound the buildings practically all of\nthem in more or less dilapidated con\ndition In the superintendents quar\nters the paper on the walls Is torn\ndefaced and hanging and the kitchen\nfloor has a hole in it which make it\ndangerous In the quarters of the dif\nferent parties we found the floors of\nthe buildings and the steps rotting\nand In a dangerous condition and the\nwalls need immediate attention the\nsame Is true of the colored female\ndepartment and here also Is a door\nwhica Is practically out of use and\nwhich should be fixed at once We\nfound that this institution was kept\nneat and clean and that the Inmates\nhad a sufficiency of good and whole\nsome food that there is an excel\nlent garden on the place and that\nthey also have some cows which in\nsures to the inmates milk and butter\nand vegetables in addition to the\nthings that are bought and sent there\nfor their sustenance There are about\nfifteen or sixteen inmates in this In\nstitution\nWe desire to thank the court and\ncourt officials including our bailiff\nfor the courtesies showp us and now\nhaving complete dour investigation we\nask to be finally discharged\nWe also request that a copy of this\npresentment be furnished the Pensa\ncola Journal and the Evening News\nand that the same be published in\neach of the said papers\nRespectfully submitted\nFRED D ABBOTT\nForeman\nJOHN C CARO\nClerk\nApril 17 1909\nSubscribe for The Journal\n=\n1\nI\nc y\nLet me put a suite of Pearl Quality Furniture in your\nhomeor if you do not desire a full suitesay several\npiecesa rocker wardrobe chiff robe dining room suite\nor beduse it one week at my expenseif you dont say\nit has distinctive quality and features that yo do not find\nin the ordinary kind of furnituredont keep itif you\ndo find that the Pearl Quality of home furnishings have\na distinctive quality that puts them in a class by them\nselves keep it and pay for itcash or easy payments and\non terms to suit youvie allow you to arrange that to\nsuit yourself\nCall at our store tomorrow at 105 South Palafox\nand let me show you some new patterns which are arriv\ning daily patterns that will attract you and please your\nsense of beautylarge massive pieces that will add class\nto your homefurniture that you will be proud to show\nto your friends at lowest prices\nA call will convince youcome at once before you\ni do anything else and look over my line fJ2 I\n4\nJ\nSec yTreas i\nPearl Furniture Company <\n105 South Palafox Street\nPensacola Florida\n< l\n1 = <',
       u'page': u'5',
       u'place': [u'Florida--Escambia--Pensacola'],
       u'place_of_publication': u'Pensacola, Fla.',
       u'publisher': u'Mayes & Co.',
       u'section_label': u'Section 1',
       u'sequence': 5,
       u'start_year': 1898,
       u'state': [u'Florida'],
       u'subject': [u'Escambia County (Fla.)--Newspapers.',
        u'Florida--Escambia County.--fast--(OCoLC)fst01215180',
        u'Florida--Pensacola.--fast--(OCoLC)fst01206144',
        u'Pensacola (Fla.)--Newspapers.'],
       u'title': u'The Pensacola journal.',
       u'title_normal': u'pensacola journal.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn87062268/1909-04-18/ed-1/seq-5.json'},
      {u'alt_title': [],
       u'batch': u'batch_mthi_fox_ver01',
       u'city': [u'Forsyth'],
       u'country': u'Montana',
       u'county': [u'Rosebud'],
       u'date': u'19030709',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1906,
       u'frequency': u'Weekly',
       u'id': u'/lccn/sn85053117/1903-07-09/ed-1/seq-4/',
       u'language': [u'English'],
       u'lccn': u'sn85053117',
       u'note': [u'Archived issues are available in digital format from the Library of Congress Chronicling America online collection.'],
       u'ocr_eng': u'Gbe URosebud Countv mews\nEDWARD J. DIERKS\nEditor\nW. ELERYJOHNSON\nASSOCIATE EDITOR\nLAURAN F. SMITH\nMANAGER\nEntered In the postoffice Iq Forsyth,\nMont., as second class rhatter.\nSUBSCRIPTION RATES\nOne Year............................ $2.50\nTHURSDAY, JULY 9, 1903.\nThree Months\n.......................75\n!\nOFFICIAL paper of rosebud county\nThe vice presidential\nbe on the spread.\n"Blue Grass Feud"\nnew breakfast food.\nMachen threatens to tell things,\nall means let him tell.\nitch seems to\nis Kentucky\'s\nBy\nKing Peter has ordered a new crown.\nThe old one was too red.\nWhy not run Carne Nation for pres\nident on the "wet" ticket.\nFrom Valley Forge to San Juan the\nUnited States has paid \xa73,000,000,000\nin pensions.\nAn Ohio editor sagely remarks that\nsome people who have an aim in life\nhave nothing to shoot with.\nA British column of troops was wiped\nout a few days ago and England is\nmadder than the Mad Mullah.\nForce of circumstances has compelled\nthe Hon. Marcus Hanna to conclude\nthat the senate is not such a bad place\nafter all.\n"I regret to state," that grand old\nstand-by of the British in South Africa,\nis now doing business overtime in\nSomaliland.\nThe United States Cruiser Galves\nton will have at least one claim to dis\ntinction. It was in hock before it was\nin the water.\nKansas wants 75,000 people to work\nin the harvest fields. Some of our de\nlinquent subscribers better go there\nand get a job.\nThere is at least one comforting piece\nof news comes out of that Servian mess.\nUntil further notice no Servians will be\nallowed to emigrate.\nThe presidont is said to have ordered\nthat officials of the postoffice depart\nment cease talking. That will be hard\nlines for the feminine employes.\nIf Spain does build a new navy, as\nshe is now talking of doing, she will\nprobably make special arrangements\nto keep it out of range of Uncle Sam\'s\nguns.\nThe Canadian minister wjio shared\nhis bed with a negro who was unable\nto get hotel accommodations has not\nas yet received any voluntary contri\nbutions.\nGeneral Miles will retire from the\ncommand of the army next month, and\nwill then have ample time to attend\nsocial functions and wear his gorgeous\nuniforms.\nU. S. Grant, Jr., a son of his father,\nsays the vice presidential nomination\nshould go to the Pacific coast, and the\nson of his father also announced that\nhe is available.\nIf J. Pierpont Morgan\'s watered\ntrust stocks keep on tumbling the old\nman may soon be hanging around the\ncorner grocery looking for a little trust\non his own account.\nThe secret is out at last as to why\nthe president won\'t allow Miss Alice\nto have an automobile. The darned\nthing is too strenuous and Teddy\ndoen\'t want to be the cow\'s tail\nrace.\nin the\nMiss Stone is to make Bulgaria her\nfield of operations again, and the\nnewspaper correspondents are moving\nthitherward en masse. Now save up\nyour nickles, good people, for the next\ncall for ransom.\nA facetious Indiana editor says that\nMorgan Necessary eloped with Pearl\nHoss in Indian Territory and a heart\nless judge disregarded Morgan\'s ne\ncessity and sent him to jail\u2014presum\nably for Hoss stealing.\nJ. Pierpont Morgan\'s prestige has\nsuffered a shrinkage during the last\nfew months which is even more marked\nthan the shrinkage in value of his\nwatered securities. Anil no one has\nany sympathy for the cuss who pro\nmotes the trusts.\nNevada, the least populous state in\nthe Union, has put to shame some of\nher larger and more wealthy brothers.\nShe has appropriated the sum of $125,\n000 for an exhibit at the St. Louis ex\nposition, of which $70,000 will be set\napart for a display cf minerals.\nA man who has worked as a farm\nhand for ten years says whenever he\nlooks after his employer\'s interests as\nthough they were his own he is invari\nably well treated. There is more in\nthis statement than many hired men\nthink.\u2014Cambridge (Pa.) Enterprise.\nThe Mad Mullah apparently has a\nfew sane moments occasionally. At\nany rate he licked a few thousand\nEnglish troops last week and now our\nEnglish cousins are scratching their\nheads and trying to explain how it all\ncame about.\nThe Ship Building trust dosn\'t ap\npear to be able to float itself. It has\nbeen unable to pay any interest on its\nbonds, and stockholders want to know\nthe reason why! Perhaps it is only a\ncase of the "big fish eating up the lit\ntle ones." That\'s the way such\nthings generally work.\u2014 Bellefontaine\n(O.) Republican.\nHere is an obituary printed in a\nKansas paper: "She reared nine\nchildren. She darned 10,000 socks.\nShe cooked 100,000 meals. She washed\n1,000,000 dishes. She died and went\nto heaven." There shouldn\'t be any\ndoubt about the last part of her ex\nperience and no doubt the change was\nwelcomed.\u2014Hannibal (Mo.) Journal.\nHarper\'s Weekly finds a suggestion\nin Booker T. Washington\'s experience\nwith the haughty chambermaid, of val\nue in helping Tuskegee institute solve\nthe great problem of fitting negroes for\nuseful lives. Educate the young women\nto be chambermaids and housekeepers.\nProbably a thousand Tuskegee insti\ntutes would not supply the demand for\nyoung women skilled in the art of\nhousekeeping.\ned\na\nto\nThe Princess Henry\'s prize for war\nship cutters was won at Kiel yesterday\nb_y the jackies of the United States\nCruiser Chicago in competition with\ncrews from the German ships Freya,\nHohenzollen, Frauenlob, Ariadne,\nGlitz and Hagen, thus demonstrating\nthat the kaiser\'s remark about the\n"splendid American sailors" was\nneither hot air nor a stump speech.\u2014\nChicago (111.) Tribune.\nPorto Rico is undoubtedly making a\ngood thing out of its relations with the\nUnited States. Sugar and tobacco\nwere hardly exported at all a few\nyears ago. The island had no chance\nin competition with Cuba. Now the\nisland is sending a good deal of both\ncommodities to this country, besides\ndeveloping a trade in so-called Panama\nhats and in fruit, and Panama hats\nsell for about 15 plunks each.\nTHOSE STOLEN MILLIONS.\nThe Reveille: Because the Amalga\nmated Copper company has bought the\ndaily papers and the news service of\nthe state, it is difficult for the average\nreader to learn ju,st what is going on\nin the state, especially when anything\nwhich concerns the success of that gi\ngantic corporation is at issue.\nThe United States has concluded to\ncollect for the millions of dollars worth\nof timber which the Amalgamated\nstole. The interior and justice de\npartments of the government have been\n.apprised that convictions can be se\ncured against private individuals for\ntrespassing on or appropriating prop\nerty from the public domain. Its at\ntention has been called to the fact that\nsuits amounting to the millions pend\ning against the Amalgamated company\nfor denuding the forests of western\nMontana have been pending for years\nwithout progress.\nIt has learned that if John Smith cut\ntwo trees from the government land to\nhelp him build his log cabin, he was\nprosecuted and fined in the United\nStates court, but that the Amalgamat\ned had devastated hundreds of square\nmiles of forest land, manufactured the\ntimber and shipped it throughout the\nwestern states, flagrantly robbing the\ngovernment of millions of dollars, and\nthat there seemed to be no redress.\nThe news that the government has\ntaken a new step to bring the gigantic\ntrust to time is of not sufficient import\nance to justify a prominent position in\nthe ne\\Vs columns of the daily\' papers,\nyet not one organ of all the muzzled\npress has dared to tell the people that\na special United States attorney had\ntaken the matter in hand and promised\nto see that Montana was amply re\nmunerated for the forest trees which\nhad been stolen.\nA magnificent university might be\nendowed; a commodious asylum built\nfor her decrepit miners, lumber or\nsmeltermen; a thousand charities\nmight be assisted by the millions\nwhich the Amalgamated Copper com\npany has stolen from the hillsides of\nMontana.\nIt remains to be seen whether\nSpecial Attorney\' Maynard will make\nhis promise good and compel the\nAmalgamated Copper company to pay\nback to Montana the millions it has\nstolen.\na\nLYNCHING AT THE NORTH.\nThe Constitution, Atlanta, Ga.\nElesewhere on this page we publish\nsome extracts from the editorials on\nthe Deleware negro-burning printed\nin northern newspapers. We call at\ntention to the fact that not a word of\nsympathy for the negro is expressed\nand that all the condemnation is di\nrected against the spirit of impatience\nwith the law\'s delays that led up to\nthe lynching. And we further call at\ntention to the fact that this showing by\nthe northern press in nowise differs\nfrom the views constantly expressed\nby the press, the officials of states and\nthe better public sentiment of the\nsouth under like circumstances.\nThe Constitution, after vainly argu\ning with its northern contemporaries\nfor many years that human nature in\ntheir territories is much akin to that\nin our section, was naturally curious\nto see how, when their turn came, they\nwould deal with the lynching evil.\nBut we are not satisfied with the\nshowing. It is far short of the hys\nterics and heroics played off on us\nwhen lynchings have happened in\nGeorgia and the south. We want, and\nhave the right to demand, that the\nnorthern press and the people of Del\naware give us the long-awaited object\nlesson of "how to cure the lynching\nhabit!"\nThey Know how to do it, because\nthey have been telling us for twenty\nyears how unnecessary, barbarous and\npeculiarly southern is the practice of\nlynching raping and murdering negro\nbrutes. Now that the crime with its\nhorrors has come home to them and\ntheir people\u2014among whom there is no\n"lower class" and no "negro-hating\nwhite trash"\u2014have acted in Illinois\nand in Delaware quite as some south\nern people have done, we call upon\nthem to produce their remedy for the\ncrime itself and their sure preventative\nof the rage of the mob that ends in\nlynching!\nAt the time of the lynching of Sam\nHose at Newnan, in this state, it was\npublished that the railway company\nsent out a special excursion train to\ncarry cur ious people to see the burn\ning of a human being by a mob! The\nhorror over the alleged incident of\nyears ago has hardly abated in the\nnorth and Sam Hose reappears in\ntheir editorials to this day. Yet the\nNew York Herald reports that in Wil\nmington the trolly lines ran "special\ncars" to carry people to the bonfire in\nwhich White was being roasted, that\nthe roadway was lined with the car\nriages of "respectible citizens" eager\nto be in at the death, and that "one\npretty young girl was brought up to\nthe fire while White was giving his\nlast gasp and looked on until she be\ncame sick and turned away!"\nWhat more? Why, across the full\npage of The Herald\'s account runs a\nblack letter line that reads: "Public\nSentiment in Deleware Seems to Ap\nprove Lynching!"\nThe Constitution has always de\nI s. r. clerk: e I\n\xfc .\xc4 MERCHANT TAILOR g 3\nFINE TAILORING OF \xcf\xcfLL KINDS\nSUITS TO ORDGR FROM $20 UP\n\xa3 Butte : : Mont, S\nTmmmmnmmmmmmmmuimm\nPianos and Organs\n\xc6olians and Pianolas\ni\nMusical Merchandise\n\xa9\nAll the Latest ilusic as Soon as Published\n\xa9\nLow Prices; Easy Terms\n\xa9\nWrite For Catalog and Prices\nMontana Music Co.\n119 N. Hain St. = = = Butte, Mont.\n^iUiUiUiUiumiuiuiuiuiuiUiuiUiUiUiuiUiUiuiuiuiUR\'\nSCv\'\nLewis\nDry Goods\nCo.\nFORMERLY O. K. LEWIS & CO. ESTABLISHED 1883\nDry Goods. Millinery\nMen\'s Furnishings\nButte, /Wont.\nFor Women\n(We do not pay express on these)\nEvery good and pretty and stylish style made in\nthis famous shoe carried in stock\n42 styles in all leathers\n$ 3 * 5 \xb0\nEqual to the best$6.00\nshoes made. All one price\na pair\nSend for handsomely illustrated catalogue\nWrite for samples of anything in dry goods\nCareful attention given to all mail orders\nr *il\nM V V- V\nnounced lynching, has stood for legal\'\nprocedure, and supported the govern\nors in sending state troops anywhere\nin the state to prevent the lawless ven\ngeance of mobs. A sheriff at Birming\nham killed a score of people a few\nyears ago to prevent a lynching, and\na Georgia sheriff who bloodily saved\nhis prisoner from a mob has been\ngiven a place of trust at the federal\nprison in this city\'.\nBut what we want now is a demon\nstration of all that has been alleged\nin the past, towit: That the north is\nbetter cultured, more law-abiding and\nmorally incapable of the lynching\ncrime that has been so assiduously\nerected into "a peculiarly southern in\nstitution" by the press of the north.\nIt is up to the critics now to explain\ndifferences and exemplify "that better\nway!"',
       u'page': u'',
       u'place': [u'Montana--Rosebud--Forsyth'],
       u'place_of_publication': u'Forsyth, Mont.',
       u'publisher': u'A. Buchanan',
       u'section_label': u'',
       u'sequence': 4,
       u'start_year': 1901,
       u'state': [u'Montana'],
       u'subject': [u'Forsyth (Mont.)--Newspapers.',
        u'Montana--Forsyth.--fast--(OCoLC)fst01227183'],
       u'title': u'Rosebud County news.',
       u'title_normal': u'rosebud county news.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn85053117/1903-07-09/ed-1/seq-4.json'},
      {u'alt_title': [],
       u'batch': u'batch_iune_echo_ver01',
       u'city': [u'Chicago'],
       u'country': u'Illinois',
       u'county': [u'Cook County'],
       u'date': u'19120911',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1917,
       u'frequency': u'Daily (except Sunday and holidays)',
       u'id': u'/lccn/sn83045487/1912-09-11/ed-1/seq-10/',
       u'language': [u'English'],
       u'lccn': u'sn83045487',
       u'note': [u'"An adless daily newspaper."',
        u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.',
        u'Available on microfilm;',
        u'Description based on: Nov. 1, 1911.',
        u'Issue for <Nov. 24, 1911> lacks vol., no., and chronological designation.',
        u'Issue for Nov. 4, 1911 erroneously designated as Oct. 4, 1911.',
        u'Issue for v. 3, no. 290 (Sept. 7, 1914) erroneously designated as v. 3, no. 300 (Sept. 7, 1914). The error in numbering continues.',
        u'Issue for v. 5, no. 214 (June 7, 1916) erroneously designated as v. 5, no. 214 (June 6, 1916).',
        u'Issue for v. 5, no. 7 (Oct. 5, 1915) erroneously designated as v. 5, no. 7 (Sept. 5, 1915).',
        u'Issues for <May 7-17, 1915> called also "Moving Picture Edition."',
        u'Issues have no page numbering.',
        u'Saturdays have Noon and Final editions, Dec. 28, 1912-June 21, 1913; Saturdays have Noon and Last editions, June 28, 1913-<Dec. 13, 1913>; began issuing daily Noon and Last editions, Dec. 20, 1913-July 6, 1917.',
        u'Vol. 5, no. 36 (Nov. 6, 1915) issue called also "Garment Workers\' Special Edition."',
        u'Volume numbering begins with Nov. 20, 1911 issue.'],
       u'ocr_eng': u'ii ii w UJWI\nmed\'guaTdnjndetectivaccQnrT\npanies them. Their parents are\nin Maine. Their grandparents\nare in Cleveland.\nLike the report of Mark\nTwain\'s death, which he denied,\nthe Rockefeller black hand story\nseems to have been exaggerated.\no o -\nRACE WAR PROBABLE\nBluefield, W. Va., Sept. 11.\nThe negroes are arming them\nselves today, and a bloody race\nwar may break out at any .time.\nThere has been trouble ever\nsince the lynching of Walter\nJohnson, a negro, last wek.\nJohnson was accused of attack\ning Nita White. It was after\nwards discovered he was inno\ncent. Geo. White, father of the\ngirl, has been arrested for mur\nder in connection with the Iynchi\ning.\nThe knowledge that they lynch\ned the wrong man and the arrest\nof White only inflamed the whites\nmore than ever, ,\nAll negro business \'men on\nRaleigh street got letters last\nnight ordering them to move\nfrom Raleign street by Saturday,\nor their places would be dyna\nmited. The letters were signed\nLynching Committee.\'\nGov. Glasscock has been asked\n10 send troops here by several\ncitizens.\n"If your married life one\ngrand, sweet song?\n"Well, since the kid\'s been born\nit\'s been like an opera, full of\ngrand marches, with loud calls\nforthe author every night."\nVFABOOiMIflFErGUARD RUJjB\nIndianapolis, Ind., Sept. 11.\nThe miners of West Virginia will\nnever again submit to the rule of\nthe mine guards.\nThis was the editorial declara\ntion of the Jpurnal, official organ\nof the United Mine Workers of\nAmerica, today.\n"The "money., tfye mine owners\nof West Virginia spend to keep\ntheir men from organizing-would\nkeep. those ame men contented\nwhen organized," says the edi\ntorial. "Machine guns never mined\nany coal.\' Neither do the mine\nguards,"\no o "\nWELCOME TO OUR CITY1\nSMILE!. .\n\'Cause everybody\'s doin\' it today,\nSince the awful roa$ty weather\'s\ngone away;\n\'Course it may come back tomor\nrow, But no trouble will we borrow,\nJust enjoy the cooler weather\nwhile we may.\nCHEER UP!\nDon\'t say that it\'s too hot for you\nto work,\n\'Cause you cannot blame this\nweather if you shirk;\nJust look out don\'t get the\nsneezes,\nBut inhate the Cooling breezes,\nThat make everybody feel fresh\'\nas a Turk.\nEthel Maud says she uses\nleinon juice for her complexion\nMary I wondered where she got\nthat sour look.\niTiii nirfTriaiimTnTiTiiigiiiiriiffiWrifcBrik\n;vw',
       u'page': u'',
       u'place': [u'Illinois--Cook County--Chicago'],
       u'place_of_publication': u'Chicago, Ill.',
       u'publisher': u'N.D. Cochran',
       u'section_label': u'',
       u'sequence': 10,
       u'start_year': 1911,
       u'state': [u'Illinois'],
       u'subject': [u'Chicago (Ill.)--Newspapers.',
        u'Illinois--Chicago.--fast--(OCoLC)fst01204048'],
       u'title': u'The day book.',
       u'title_normal': u'day book.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn83045487/1912-09-11/ed-1/seq-10.json'},
      {u'alt_title': [u'Weekly dawn'],
       u'batch': u'batch_wa_muddy_ver02',
       u'city': [u'Ellensburg'],
       u'country': u'Washington',
       u'county': [u'Kittitas'],
       u'date': u'18960229',
       u'edition': u'Weekly ed.',
       u'edition_label': u'',
       u'end_year': 1898,
       u'frequency': u'Weekly',
       u'id': u'/lccn/2012252560/1896-02-29/ed-1/seq-1/',
       u'language': [u'English'],
       u'lccn': u'2012252560',
       u'note': [u'"Keep in the middle of the road."',
        u"Also published in a monthly ed., per publisher's statement, Jan. 19, 1895-Feb. 9, 1895.",
        u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.',
        u'Robert A. Turner, editor.'],
       u'ocr_eng': u'OUR MOTTO:\nKeep in ti lo Middle of the\nLad. No fusion with any\npolitical party on earth.\nVOL. 2.\nJEFFERSON\'S MAXIMS.\n\\IHE POPULIST PARTY EX\nDORSES THEM ALE\nThe legal equality of human be\nings.\nThe people tho only source of\npower.\nNo hereditary offices, nor order\nnor title.\nNo taxation beyond actual public\nseeds.\nNo national banks, nor bonds.\nNo costly splendor of administra\ntiOD.\nNo interference with freedom of\nthought or discussion.\nThe civil authority superior to\nthe military.\nNo favored class; no monopolies.\nFree and fair elections; universal\nsuffrage.\nNo public money spent without\nwarrant of law.\nNo mysteries in government hid\nden from the public eye.\nRepresenatives bound by the in\nstruction of their constituents.\nThe constitution of the United\nStates a special giant of powers\nlimited and definite.\nFreedom, sovreignity and inde\npendence of the the respective\nstates\nAbsolute severance of church and\nstate.\nThe Union a compact\u2014net a con\nsolidation nor a centrilization.\nModerate salaries and strict ac\ncountability.\nGold aud silver\u2014currency sup\nplemented by treasury notes bot\ntomed on taxes.\nNo state banks of issue.\nNo expensive navy or diplomatic\nestablishments.\nA progressive or graduated tax\nlaid upon wealth\u2014the tax to grow\nincreasingly heavy as the fortune\ngrow larger.\nNo internal revenue system. A\ncomplete seperation of public mon\neys from bank funds.\nEld. J. F. (formerly and Prof.\nWebb and wife, will begin a series\nof protracted meetings at the Chris\ntian church in this city on Sunday,\nist. at it P. M. You are invited to\nbe present and help swell the throng.\nEld. Gormerly comes highly\nended by all who have heard him,\nand Prof. Webb and wife are among\nthe best vocalists on this coast.\nF\xab Sherman announces to the\nPublic that he deals watches and\njewlery and silverware. Repairing\nof all kinds done in workmanship\norder and satisfaction guaranteed.\nOn Pearl bet, 3rd. & 4. 29tf\nNOTICE Y. S. P. C. E.\nThere will be an executive Com\nmittee meeting at the Christian\nchurch Tuesday at 7 p. m., sharp.\nAll members of the Committee are\nto attend.\nBy Order of the President.\nTHE DAWN.\nELLENSBURG, WASH., SATURDAY, FEB. 29, 1896.\n/ / promise to pay to ROBERT A. TURNER, Editor of THE DAWN, of Ellensburg, Wash., \\\ni ONE DOLLAR, \xa7\nTo become due when Two Hundred persons hare subscribed a like amount, the same to be\n\\ used totvard enlarging THE DAWN to a Six column newspaper, the same to be a strict Populist I\nI pa j\xbb r iii the fullest, sense of the word. P. 0 State, I J\nX\xbb m \' Signed ,X\nm " . m\nThe Official Vote.\nThe following official statement shows the\nPopulist vote of 1892 and 1894-5. In the first\ncolumn is (riven the official returns for 1892\nThe second column gives the latest vote,\nwLethcr it is 1S!\xbb4 or 1898. It shows the\nenormous gain of .pearl) 60 per ct-nt since\n1802. The same official returns show a big\nfalling off in the vote of both old parties\u2014\nwhich, by comparison, would show a train\nof nearly" 100 percent since 1892. StiC\xdf to\nthe ship, and next November the Populist\nparty will not be the third party- and may\nbe first.\u2014Chicago Sentinel.\nstate. . 1892 1894-5\nAlabama j 85,181 88,283\nArkansas 11,831 j 24,541\nCalifornia .... ; 25,313 51,804\nColorado i 53,584 66,712\nConnecticut. .. I 806 1,546\nDelaware 1\nFlorida i 4,843 4,469\nGeorgia i 42,937 96,888\nIdaho ; 10,520 7.121\nIllinoii | 22,207 59,793\nIndiana ! 22.208 29,388\nlowa i 20,595 32,118\nKansas 163,111 118,329\nKentucky 23,508 16,911\nLouisiana 27,960 27,960\nMaine 2,381 5,321\nMaryland ... 796 989\nMassachusetts 3,210 7,786\nMichigan ) 19,892 25,943\nMinnesota .... 29,313 87,931\nMississippi .... 10,118 13,000\nMissouri ...... 41,180 42,463\nMontana 1.270 15,505\nNebraska 83,134 70,566\nNevada 7.2(57 71 1\nNew Hampshire 2,292 832\nNew Jersey.... 969 1,901\nNew York 10,429 11,049\nNorth Carolina. 44,736 144,334\nNorth Dakota 17,700 9,354\nOhio 14,850 52,965\nOregon 29,955 26,033\nPennsylvania 8,714 7..502\nRock Island . . . 227 369\nSouth Carolina. 2,407; 17,278\nSouth Dakota 26,544 26,568\nTennessee 23,447 23,44,\nTexae 91*688 159,224\nVermont 500! 740\nVirginia ... . 12,227 81,229\nWashington... 18,165 24,279\nWest Virginia . 4,166 4,166\nWisconsin 9,909 25,604\nWyoming 7,7221 2\xa376\n1,065,825 1^15,039\nL YNCIIIjYG.\nLynching is indigenous to the\nsoil of the United Htates. It is\nscarcely ever heard of anywhere\nelse. It is not confined to any sect\nion of this country, although most\ncommon in the South, owing to pc- j\nBuliar conditions prevailing there, j\nThere were about 10,000 murders\nin the U. 8., last year. There were\nji-st about 130 legal executions, and\nabout 175 lynchings. More lynch\nings than legal executions! More;\nlegal executions might result in less ;\nlynchings. The only excusable \\\nlynching is the one that follows a\nlegal court trial, where the evidence\nof guilt is plain, and the courts j\nhave corruptly allowed a guilty:\nparty to escape, as in the case of!\nthe Seattle lynching a few years\nsince. Every man is entitled, by\nthe laws of heaven and the consti\ntution of the U. S., to an impartial\ncourt trial by a jury of his peers.\nEllensburg had a lynching "bee"\nlast August, a double header. It\nwas an outrageous, and utterly in\nexcusable affair, simply because the\naccused hand had no trial by the\nproper authorities* The mob that\nhung the Vinsons hung public jus\ntice up before the public\'s very eyes.\nThe occasion of the lynching was a\ndouble murder that occurred in Ub- ;\nalakers saloon on Sunday evening,\nAug, V)th. when that and every other\nsaloon, should have 1 eon closed ac\ncording to the law. The anarchy\nof the mob that hung the Vinsons,\nwas the direct fruit of the prior i\nanarchg of the saloons of the town i\n(t Ellensburg, in keeping open on\nSunday, against the plain and un\nraistakable mandate of the statutes\nof the state of Washington. An\narchy is the proper name for such :\naction! When or where was the\nsaloon ever known to obey the law\nexcept under overwhelming com\npulsion? It is defying the law j\nright here in Ellensburg every sab\nbath day, and our "trusted" Mar\nshal views the situation with the\nUtmost serenity from week to week.\nIf A. P. A. U. is correct that\'s what\nthe saloon-keepers ordered him ap\npointed for.\nWhoever controls the volume\nof money in any country is ab\nsolute master of industry and\ncommerce.\u2014Jas. A. Garfield.\nThe mob that hung the Vinsons and\noutraged public justice last August\nwas drunk. Almost without except\nion every lynching mob everywhere\nis drunk. It was currently report\nas a fact at the time, and this com\nmunity should know it (for\nnone of the papers mentioned it at\nthe time; neither did they make\nany comment on the fact that the\nmurder occurred during the pro\nhibited hours of a sabbath day)\nthat the mob went into the very\nsaloons that were the cause of the\nmurder, and tanked themselves full\nof red licfjuor before they had the\nnerve to break into the jail and\nhang two men up to the public gaze,\nand outrage public justice at the\nsame time. And, (save the mark!\nafter the whole wretched\ntransaction was over the mob was\nsaid to have gone to the saloon,\nstacked their ami\nand tanked some more, before they\nscattered from the public gaze un\nder cover of the darkness! It was\na drunken affair from the double\nmurder on Sunday evening to the\nworse double murder the lynching\nof Wednesday morning. And the\nwhole dreadful affair saddled an\nadditional expense of nearly $1,500\non an already overtaxed commun\nity. And the blood curdling inci\ndent ended in a verdict of acquittal\nfor the lynchers, in the face of over\nwhelming evidence of their guilt.\nIf there had been no saloons in El\nlensburg there would have been na\nmurders and no lynchings last Aug\nust. If the law defying saloons of\nEllensburg had been closed on Sun\nday, according to the law last Aug\nust, there would have been no mur\nders and no lynching for E!lens\nburg to look back upon with shamed\nface. Take whichever horn of the\ndilemena you please. Hang the\ninstitution that will thus saWe mur\nder, and will then proceed, if re\nports be true, to nejve up a\nmob to hang up public justice\nby the neck, by the usurpation of\nthe functions of the courts, in caus\ning untried prisoners to hang] Yet,\nCw<t-:w.ed en JBvd page,\nNO. 29.',
       u'page': u'',
       u'place': [u'Washington--Kittitas--Ellensburg'],
       u'place_of_publication': u'Ellensburg, Wash.',
       u'publisher': u'Dawn Pub. Co.',
       u'section_label': u'',
       u'sequence': 1,
       u'start_year': 1895,
       u'state': [u'Washington'],
       u'subject': [u'Ellensburg (Wash.)--Newspapers.',
        u'Populism--Washington (State)--Newspapers.',
        u'Populism.--fast--(OCoLC)fst01071658',
        u'Washington (State)--Ellensburg.--fast--(OCoLC)fst01205216',
        u'Washington (State)--fast--(OCoLC)fst01204703'],
       u'title': u'The dawn.',
       u'title_normal': u'dawn.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/2012252560/1896-02-29/ed-1/seq-1.json'},
      {u'alt_title': [],
       u'batch': u'batch_scu_irmookrastrut_ver01',
       u'city': [u'Lancaster'],
       u'country': u'South Carolina',
       u'county': [u'Lancaster'],
       u'date': u'19041019',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1905,
       u'frequency': u'Semiweekly',
       u'id': u'/lccn/sn84026900/1904-10-19/ed-1/seq-1/',
       u'language': [u'English'],
       u'lccn': u'sn84026900',
       u'note': [u'Archived issues are available in digital format from the Library of Congress Chronicling America online collection.',
        u'Editors: R.S. Bailey, 1852-<1854>; David J. Carter, <1876>.',
        u'Merged with: Lancaster enterprise, and: Lancaster review, to form: Lancaster news.',
        u'Publishers: R.S. Bailey, 1852-<1854>; W.M. Connors, <1862>; David J. Carter, <1873-1876>.'],
       u'ocr_eng': u'1 *\n, ?\n? - *?? - ~ i \' \' "f\' ..-^yr\'.rx" "-"j*?-*- 1 - "* . \' .< ??^*r\nn?3?*W & OAUTKk i A f*i if% Neuxyaym : rhrffti niiiMiiffiiii vf~\' H <grTmn> J VXSBtte. &J& * %*>.*\n?&ttn?*n? If Mum f } PkssMMmta^^\n**g \' ? - 1 - \' \' w? \' " 1 1 " 1 \' "? ?? -- ~?? -\n"\'xv ly lancaster s. . c.. octobeu is, 1004 established *v.2\n"S ^ \' 11\ni web;\nX\nX ?<J Anno\n0 OUR ANN1\nX - OPEP\n* FRL AND\nx o< MILLir\nX WEDNESDAY\n? WEDNE!\nX WaK.?s?I\nV WW -- ?V\ninvito ?*1\n^ mi m m v * VV/ MfA\n5 to come\n0 OUR GrC\n% LANCASTEI\nX\n%\nJ. E. RUTLEDGE, Dentist,\nLancaster, S. C.\nI\nforking on credit doesn\'t pay, J\nlid . my terms from this time\nhenceforth arestiictly cask.\nReasonable Price*.\n?eld Filling $1.50\nAmalgam Filling 75 cts.\nCement Filling 75 ct\nRUBBSR PLATE,\nPull upper set of teeth $12.00\nUpper and lower set $25.50*\nThese prices are strictly\nfar cash. No work done except\nfor oash or good security.\nJ. E. RUTLEDGE, Dentist.\niiniwcv am mm\nIllUllCiI 1U bUftlv\nI haye made arrangement wit j\nlander* of money in New York \'>ity,\nwith whom I am able to negotiate loans\nsecured by first mortgage on Improved\neottou farms, at 7 per cent, interestrepayable\nin annual Installments of\nflee yearn No brokerage or commission\nchaxgsd Only a reasonable\ncharge for abstract of title.\nR E WYLIE,\nAug 81?Om. Attorney at Law\nGo to the\nLANCASTER MARBLE\nAND\nGRANITE WORKS, I\nFor Good Work and Low Prices j\nA. J. McNincH.\nLANCASTER, S. C. 1\nNever Ask Advice\nWhen you have a cough or cold\ndon\'t oak what \\? good for it and\nget some medicine with little or no i\nmerit and perhaps dangerous. I\nAsk for Foley\'s Honey and Tar, 1\nthe greatest throat and cold rein^ ;\nedy,it curns coughs,colds quickly. I\nSold by Furderburk Pharmacy.\nFoley\'s Money and Tar ,\ntor children,aefr,?ure. No opiate%. 1\n- - ? tj.? ^\nEG TO 1\n. x\nunce is?\nX\nJAL FALL X\nriNG 5\nWINTER 0\nmy, 8\nAND X\nSDAY NIGHT k\ni. it;, i\nM\n?eetivel^\n1 Ladies a\nand see S\n)ODS. X\ny\nIMER.CO. y\n. \' A\n.VV\n**_ n ?i ... - -\nmr. i> narris, president of tbe\nAnderson county Farmers\' Protective\nUnion, hat* issued a call for\na meeting of tbe farmers of tbo\nState to be held in Columbia on\ntbe 26tb. Tbe object is stated as\na strong and concerted effort on\nthe part of tbo farmers to secure\nbetter prices for cotten than those\nat present prevailing.\nA LOVE LETTER\nWould not interest you if you\'re\nlooking for a guaranteed Salve\nfor Sores, Barns or Piles. Otto I\nDodd, of Ponder, Mo., writes: 4tI\nsuffered with an ugly sore for a\n>ear, but a box of Bucklen\'s Arnica\nSalve cured me. It\'s the\nbest Salve on earth. 25c at Crawrford\nBros., J. F. Mackey & Go,,\nand Funderburk PharmacyDrug,\nStores.\n?Attorney general Gunter has\nwritten a letter to Senator Brice\ngiving as his opinion that there\nshall be a sparate box for each\namendmeut at the approaching\nelection.\nCONFESSIONS OF A PKIEST.\nig n.? *\'t i\nAtuT. uuu. w. uui, ut waio,\nArk., writes, 4\'For 12 years I\nsuffered from Yollow Jaundice. 1\nconsulted a number of physicians\nand tried all sorts of medicine, but\ngot no relief. Then 1 began\nthe use cf Electric Bitters and\nfeel that I um now cured of a dis\nea?e that had mo in its prasp for\ntwelve years. If you want a reliable\nmedicine for Liver and Kidney\ntrouble, stomach disorder or\ngeneral debiiity, got Electric\nBitters. Its guaranteed by Crawford\nBros., J. F. Mackey & Co ,\nand Funderburk Pharmacy. Only\n50o.\n.Nolico to the Public.\nI will hold nil inquest* in the\ncounty. Phone to my residence\nat Pleasant Hill for me when\nneeded.\nJ. Montgomery Catkey,\nSept. 20?tf Coroner L C.\nnayes escapes the Death\nTrap.\nGov. Heyward Commutes Sentence\nof Wife Slayer to Life\nImprisonment in Penitentiary \'\nSpecial to Greenville News.\nColumbia, Oct. 13.? Had the ^\nverdict ol the jury been carried\nout Iloyt Hayes would have been\nhanged today at Walhalla. In- .\nstead of this being his day of ^\ndeath, it has been hisdayof hope. j\nA seemingly hopeless fi>?ht for .\nIlf- 1 t\nmo nits ueen won, anil instead of ^\ngoing to the gallows the shadow\nof doubt sends the young man to\nprison for life. ^\nThe case of Hoyt Hayes, the\nyoung farmer-factory operative of\nOconro, has attracted considerable\nattention and interest throughout ...\n0 ui\ntho entire State. The moat intense .\ninterest an.l feeling exists in tho\ncounty of tho killing. A petition\nwith more than a \'.h iU9?nd nmnea ..\n|. to\nis on file asking that Gov. Iley?\nwar 1 spare the life of the pritoner ^\nand half that number are on ie-1 cord\nas protesting against an tr.-l\n1 3 ? i ri\nj torference with the death sontence.\nIt would have been much easier\n0(\nfor Gov. Hey ward to have closed ^\nhi* ears and consented to allow\nw\nHoyt Hayes to bo hanged and ^\nrested upon the verdict "of the ^\njury. No doubt ho felt it his ,\nduty to probe to tho bottom of ^\ntho case, and many a restless hour\n\' J e>\nhas he spent on the case.\n~c t si\njlUU ia?jA. ut iniiiiij, nowovor, ^\nmade no differenco, uud Gov. Hey ^\nward\'s solo aim has been to do\nq?\njustice. ^\nHayes and his wife lived to- ^\ngather in a cottage. /At -4 o\'clock (\non the morning of April 26, the ~\nyoung wire called her husband ^\nand told him there was something\nwrong in the lot. He went out, ^\nand while outside, he claims his j,\nwife committed suicide, and the\n\' m\nStale claims there was no suicide, u\nbut that the husband was the ^\nmurderer.\nar\nThe whole story of the crime\nri\nmore or less hangs upon a note jft\nfound after the killing, which was m\nsigned 4,Lula." ^\n"I am treated well by Hovt.\n- - m\nbut I bad rather die than to have .\nthe pain and sickness of motherhood.\ntherefore I write to let you\n? j u\nknow 1 did it."\nco\nThe prosecution pressed the ^\nidea that Uoyt Hayes wrote this ?\nnote and left it where it would be\nfound, while the defence insisted ^\nthat Lula Hayes wrote the note -n\nand then committed suicide. If\nthe note was not written by the ^\nhusband, then there would be lit- ^\ntie doubt of his guilt. The blood ^\nand brains of the woman were\non\nscattered and went upward in the ^\nrafters, and a bont poker was ^\nfound beside the gun when found.\nThe State showed that part of tlio\nbed was not used, but Hoyt Hayos\nwi\nsays that he slept eloso and on the\noutside of the bed. The more tho ^\n, , , . \\v<\nCftHo wrh studied the more imnor.\n11,1\ntunce was to be uttuchod to tho\n1)13\nnote, and, therefore, Gov. lley% ^\nward decided to submit the all-important\nnote with other samples\nof Lola Hayes\' writing to David\nCarvalbo, the well known expert\nnil\non handwriting. A careful and\ndatailed study was made of the\nhandwriting, and Carvalho says\nthat Mrs. Huyes wrote the nole, ee\nand accepting that opinion. Gov. to\nMeyward commuted the sentence.\na A M N E R &AIVE <n\'\nthe most keeling salve In ? worm\n??hi ? iviiJ LiJUWIIIU^\nI\nillcitor HenryV View Of Kershaw\nCuso.?Report To Governor?\nPertinent Qnes*\ntion Asked Prominent\nPeople.\nSolicitor K. Henry has rented\nto the governor his investiittons\nof the Kershaw lynching\nfew weeks ago. The report\nthe result of his inves^ations\nfor several days and\ntho slang of the dav is "hot\nnff." It is as follows:\nover nor I) C, Hoy ward:\nSir: In obedience to your rclest,\n1 arrived at Kershaw, S.\n, at noon on Monoay, October\nI, to investigate tho lynching of\n>hn T. Morrison for the killing\n1 William Floyd, on the Saur*\nday previous. After couferng\nwith tho Sheriff of Lancaster\n10. L\\ Hunter Fsq , who had\n?ceded mo that morning, I conirre\nI with and intervi ved the\nay??r .some of tho aldermen\nlet iwn marshal, several, of the\ntizons the wife and son of Alorson\nand two of Morrison\'s\njighbors. From thcui I learnl\nthat the uttitude of tho entire\niwn and surrounding country\nas,i?y-iiuii(i or heart, "His blood\ni upon us and our children."\not ono singfo man among them\nit deplore 1 lynching, and c\\-1\nisod this one. "if ever ? ,..s\nccusahlo." Everyone with\nhor.; i cnnversed seemed to lcir\nto impress me with the fact\nat lhis was the most orderly,\nlies, ami sober lynching thut ever\nretired -a real pious lynching\nilh tho prea-\'iors in the back\nound almost audibly saying\nnen. 1 d 1 not get to to see any of\ne locul pn achers; but several\n>oki; to nic aftor leaving Keria\nv, on tho subject of this lynnng,\nand to my entiro astonishent,\nthey voiced the sentiment\nIf there ever was an excusable\nnching this was it." Where\no we going to end up thoso horble\nmidnight murders by lynch\nw on all hands aud public sonti\nent fast heading the same wav?\nO * !\nsense of paralysis name upon\ne with the darkness of Monday\nght.\nOn Tuesday morning I called\nspecial meeting of the town\nuncil. Every member, with\ne town marshal, met with us\nhoriff Hunter and myself) in a\necial meeting. 1 explained\nat the governor had sent me to\nvestigate the lynching; they\nsre sworn officers, like myself\nwanted their help; Morrison had\nen taken from their custody\ney were somewhat responsible\nitbis account; 1 wanted them to\nal with mo in a perfectly honest\nd straight forward manner; I\n>uld try to do the same with\ncm;thero was no use in my\nist\'ng tfeort if they wore in\nm put by with tho lynching or\nmid obstruct or refuse to help\no In tho investigation. My npal\nfor help was in tho interest\ntho law, the name of tho state\n(1 our Maker.\nAfter this 1 asked each anil\nery one ?f thorn the following\nlestio\'iis:\nAio you in sympathy with the\nnching?\nWill you help the States oflirs\nin ferreting ont und bringing\ntrial those lynchers, honestly?.\nWas that lynching don > by\nwn folks or from tho surrounag\ncount> ?\nTo these three questions X hare\nvcrbatu i nsvveres on tile. Two !,\nalderman answered that they!*\nwere indifferent as to the lynch- 1\ning and would not help to ferret i1\n?ut the lynchers The mayor\nand ono aldormad answered that\nthey were not in sympathy with\nthe lynching and would help the\nstate otlicers, provided, it did not\ninterfere with thoir businoss ^bnth\nof these had much business). Ono\nalderman had dono all he could to\nprevent the lynching nnd would\ndo nothing more. The clerk was\nin sympathy with the lynchers and\nwould not help ferret it out, and\nwould cover up evidence if he\nknew any.\nineiovrn mars&al was uot in\nsympathy with the lynching, did\nall he could to prevent it and\nwould help all he could to ferret\nit out; but was busy with collecting\ntaxes.\nAs to the third question, the\nopinion of three of those present\nwas that the lynching was done\nby country people, ami of four\nthat the crowd that did it was\nmixed; but by four that the country\npeople predominated, because\nuobody could be missed from the\ntown after Morrison was taken\nfrom the guard house. This last\nis tho opinion of the great majority\nof the townspeople to whom 1\ntalked. After interviowng the\n4 .vr \'\'"icers,I requested the town\nmurcii&l to go to every business\nplace and announce thnt 1 would\nremain at tho council chamber\nuntil 5 o\'clock p. . m. (taking 30\nminutes for dinner) and wanted to\nconfer with anyone who would\ngive mo auy assistuueo. 1 urged\nthe marshal not to pass by a single\nman. if possible. As a result\nsix persons called on me, one of\nthem a member of tho jury of inquest\non tho lynching of .Morrison.\nThis member of that jury\nhad taken a hand in a lynching\nbco to tho extent of voting to hang\nthe culprit; but this was for the\nusual (?) crime. Of all six none\nknow anything except "They\nsay\'\'\nI tried The Stato\'s correspon\nd?nt, sent word to him and went\nto his oftice. He was out of town.\nI asked for and tried to fiod a\nfriend of the dead man, outside of\nhis own family. If thero was ono\nho would not own it. One man\nspoke kindly of Morrison, and a\nman who had known him longest\nand host.\nThe following are my conclusions:\nWilliam Floyd was a good,\narerage citizen, soher and popular\nand of a popular aud influential\nfamilv. .Ino I\\ Morrison whs\ncross-grained and killed two negroes\n(excusably or inexcusably),\nhad been acquitlod and bad trouble\nwith several other people, drank,\nhad no family or influence. Ilis\nkilling Floyd was an awful murder\nand (he community \'s murder\nof him still tnoro awful; in that\nthe conscience of tho community ,\nis debauched with bis blood which i\nwill not 1)0 wiped out for half aj\ncentury. Morrison was a bad man;\nbut not a bad as painted. The\njury of inquest is from tho country.\nFrom the foregoing facts if\nthe jury does not take it into its\nhead to vindicate the law by ferreting\nit out, the state is powerless.\nThere is nothing, it seems\n4 A ? L- I 1 1 t .. il. J\niu iiio, 1.1 ou uccompiiMiou oy me ,\nstate\'s officers, unless the jury and\ncoroner invite Ihoni back. We\ncan assist the county; but can\'t\ntill*) charge of it. I am tnihting\nthai, after the first shock of tlio\nlynching has passed, the conscieni o\njf the law-abiding element of that\ncounty will revive ami something\nmay lie done to bring these parties\nlo trial. When I hear from you\nL will instruct Itio jury of in<pie*t\nto close up its work. 1 don\'t\nwant to be a party to a farco.\nVery respectfully.\n.1. K. llenry,\nSolicitor Sixth Circuit.\nH HOKK 1N 10 IIIS ;i 10U3E.\nS. Le Quinu of Cavendish, \\ t.,\nwas robbed of his customary health\nby invasion ofChronicConstipation\nWhen L>r. King\'s New Life Fills\nbroke iuto his house, lus trouble\nwas arrested and uow he\'s entirely\ncured. They\'re guaranteed to\neuro. 25c at Crawford Bros.,.).\nF. Mackey & Co., and Funderburk\nPharmacy, Drug Stores.\nHester\'s Weekly Statement.\nNow Orleans, Oct. 14.?Secretary\nHester\'s weekly cotton statement\nissued today shows for the\nC4 days of October an increase\nover last year of 270,000 and an\nincrease over year before labt\nof l s.\'i una tr.w a.. t .> .i- ?\nV w. A ?u ilic: "11 UUJ S\nof tSio sens > n that htivj\nclsip\':o?l the aggn gato is ahead of\nthe sumo day of last year 888,000\nnod nho.id of the same days year\nbefore last "201,000.\nThe O tal movement since Sept.\nI is 2,410,51s, against 1,528,208\nlast. year.\nS \\ VE3 T O FI lO \\1 D E AT 11\n"Our little daughter had au. al *\nmost fatal attack of whooping\ncough and bronchitis,\'\' writes\nMrs. \\V. K. Haviland, of Armonk\nN. Y., "but, when all either roru?\nedies failed, we saved her lifo\nwith Dr. King\'s New Ditcu cry.\nOur niece, who had consumption\nin an advanced st;ige, also used\nthis wonderful medicine and tod ty\nshe is perfectly well." Desperate\nthroat and lung diseases yield to\nDr. Kings New Discovery as to no\nother medicine on earth. Infallible\nfor Coughs and Colds. 50c\nand $1.00 bottles guaranteed by\nCrawford Bros., ?). F. Mackey ?\nCo., anil Funderburlc Pharmacy.\nTrial bottles free.\n"Watch the Kidneys**\n"When tlicy arc atl\'ectcd, life is\nin danger," says Dr. Abcrnethy,\nthe greatest English physician.\nFoley\'s Kidney Curo makes sound\nkidnevs. Sold be FnniWknrlt\nPharmacy.\nSellers And Brown To Hang.\nBladen NegroesureConvice*I of iho\nFiendish Murder and Assault of\nMrs. Packer.\nSpecial to the Observer.\nWilmington, Oct. 15. ? Neil\nSellers and Dave Brown were con\nvictod atElizabothtown today of tho\nmurder uml assault of Air. Geo.\nPacker, near Clarkton, September\nRrd, and scntonced to be\nbanged on November 1Gth. The\njury was out less than tour hours.\nWhen tho verdict was announced\nthe court room was tilled, but\nthoto was no demonstration. In\nless than an hour tho pluco wore\na deserted appcaraneo. Ih.th negroes\nbote up well under circumstances,\nhut they were nervous.\nI\'ho tral lasted three davs and it\nJ\nwhs perhaps the most interesting\never tried in B.adcn county.\nBefore dismissing the jury,\n.fudge Ward took occasion to\n| commend tho peop\'c of Bladen\non their domcan u throughout the\ntrial and the restrain of their passions\nat the lime when their Mood\ni\nfairly boiled with* rage on account\njof tho namro of the hellish ciime.',
       u'page': u'',
       u'place': [u'South Carolina--Lancaster--Lancaster'],
       u'place_of_publication': u'Lancaster, S.C.',
       u'publisher': u'R.S. Bailey',
       u'section_label': u'',
       u'sequence': 1,
       u'start_year': 1852,
       u'state': [u'South Carolina'],
       u'subject': [u'Lancaster (S.C.)--Newspapers.',
        u'Lancaster County (S.C.)--Newspapers.',
        u'South Carolina--Lancaster County.--fast--(OCoLC)fst01216516',
        u'South Carolina--Lancaster.--fast--(OCoLC)fst01209940'],
       u'title': u'The Lancaster ledger.',
       u'title_normal': u'lancaster ledger.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn84026900/1904-10-19/ed-1/seq-1.json'},
      {u'alt_title': [],
       u'batch': u'batch_txdn_audi_ver01',
       u'city': [u'Dallas', u'Houston'],
       u'country': u'Texas',
       u'county': [u'Dallas', u'Harris'],
       u'date': u'19221216',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 9999,
       u'frequency': u'Weekly',
       u'id': u'/lccn/sn83025779/1922-12-16/ed-1/seq-4/',
       u'language': [u'English'],
       u'lccn': u'sn83025779',
       u'note': [u'Also issued on microfilm from the Library of Congress, Photoduplication Service; Micro Photo Div., Bell & Howell Co.',
        u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.',
        u'Description based on: Vol. 7, no. 14 (Jan. 13, 1900).',
        u'Latest issue consulted: 78 years, no. 40 (Oct. 3, 1970).',
        u'Published at Houston, Tex., Nov. 14, 1970-'],
       u'ocr_eng': u'ftl KOI ft\nTHE I) ALL. IS IXFHKSS\nMS.r-ftF.8\nC-\'n\n1 l Ai..VlC\nMfcMBER\nNATIONAL NEGRO PRESS\nASSOCIATION.\n- Publlnhtx! every Saturday moulns\nIn the year at 2600 Swiss Avenue by\nTHE DALLAS FXPHBSS prLISUINO\nCOMPANY.\n(Incorporated)\nDallas. Texaa,\nroitEIQ ADVERTISING BKPRH\n, SENTATITEi\nW. n. Iff Campaay, 0S Soatb Dar\nfcei hlr-r(a hlrnKO, III.\nW. B. 71 ff Company, 404 Marias\nKnllrilnK 11 Rant iaaa Street,\nke lark. N. Y.\nEntered at Poit Office at Dallas,\nVexaB, aa aecond-claaa matter, under\nAct of Congress, March, 187\nIMPORTANT.\nNo eubecrlptlona mailed for a pe\nriod Ibh than three"Thontha. Payment\nfer aame must be 75 cents.\nNOTICE! TO THE POBLSC.\nAny erroneous reflection upon- the\ncharacter, atnnrllnjr or reputation of\nny person, - firm or corporation which\nBiay appear In the columns of The\nPallas Kxpress will be gladly cor\nrsctcd upon its belnir brought to the\nattention of the publishers.\nthe dallas express,\nsubscriptions in adtasce.\nOne Year - \'. ....\nSix Montho\nThree Months\nSingle Copy\n...$.25\nZ..1.25\n... .75\n, .05\nTHE DALLAS EXPRESS\nhas never hoisted the while\nfeather, neither has it been dis\nraced by the yellow streak. It\nis net afflicted witk the flannel\nmouth. It is a plain, every day,\nsensible, conservative newspa\nper, which trims no sail to catch\nthe passing breese; jlitt M\ndoubtful flag: It prt! t\npatriotism as broad . if\ncountry. Its love of .\'. !\ned justice covers all\' the terri\ntory occupied by the human\nrace. This is pretty \'high ground,\nbut we live on it and are pros\npering. Boys of the press come ,\nUp and stand with us. .This\nground is holy. ,,\' \'\nW. E. KING\nIT HAPPENED II, DALLAS.\nShe was little and wrinkled, yet\nsoft of speech. And m tier eyes was\nthe look of those who had seen\nmuch and suffered in silence the\nlack of the things denied them in\nthe days when whlto masters grew\nangry at the thought of education\nfor the\'.r Negro slaves.\nMany times she had gone to the\npost office window to cash the\nmonthly order which her youngest\nchild, a man of thirty-five, sent to\nher from his home in a far off state.\nEach month for more than two years\nshe had made her "Mark", for she\nhud not learned to write in her\nyouth, and in her later yoars the j\ncare of the children had given her\nso time for it. \' !\nBut there came a day when she\nno lontsr waited for the crowd be\nfore the window to thin out. She\nboldly took her turn and in a slow\nbut legible way endorsed her or\nder and gave it to the clerk with a\nsmile of triumph, s\nlie took it without, looking at\nher. But being familiar with her\nname and remembering that she\nhad always "made her maU" he\nlooked at her and in surpr\'se ask\ned: \'.\'Did you write that"? She\nsaid are I did. I\'m nol too old to\nlearn. " The free night schools gsve\nmo the chance that I had never\nhid befoxe, I can read now tro."\n, lie was pleased. But not bo ir icl.\na3 she. It happened in Dallas. That\nwonaa though aged realized that\nshe ended to learn.\nTlere are thousands of others in\nJ Vi\' such need who have not yet\ntaken advantage of the opportunity\nJor Improving themselves which our\nBplendid city is offering tlnui.\nBut the schools are open. It\nIs not yet too late. ,\nEUY XIIAS. SEALS.\nNo race in America aa more\nreasons to lHArt\'ly support the ef\nfort to stanv out tuberculosis\nthan dws ours from which Its year\nly toll Is heaviest. We should wel\ncome any means vsed n its control.\nAt Christmas Jme\' each year\nfunds for carrying on the fight\nagainst this dread disease are raised\nby the sale of Xma seals. All such\nfunds are. expended in tho cam\nIialgn of education of the masses\nfor its prevention and in influencing\nappropriations for sanatork, et., for\nthose afflicted with it.\nV.v.j Xittti tVals this year not\nft ai\'iiily lint with a fr e hand se\ncure in tli knowledr, that by .so\nclo!ng--- U nr" giving evidence of\njoii.r ,;.td will "to the- society of\nv\'.irli y a pre a. part.\nTfi\'j r". ..t-ratd rluhs of the ciiy\n.nre a:"-i.\'i,iv th! vteifnro Boa rd In\nw.iiWn1,\' j-i-in inons tr. the tr.dteat :\ni.n.l pear of our city." .t Is ,1 good\nwill di\'iv in winch cvm-y ciiiz.n\nand on i\' z.-\'ct nr-\'.ilj Join htu-U\n!y. I\'d - - \' .\nPOSSIBILITIES CF A REAL\nIt is often claimed that criticism Is easy but real construction\nis hard. Therefore critics never propose constructive projects.\nLast week the Express took the liberty of calling to the at\ntention of the people of Texai the fact that the State Association\nof Negro Tachers was failing of\nas one of the causes of this failure the fact that its leaders for\nmany years had lacked vision and\npassive rather than active. - - -\n. The truth of this assertion becomes more evident when one\nbegins to cast about for some definite accomplishment of the as\nsociation during the past few years.A serach for such a thing Is\nhopeless. It does not exist. One can point with pride to nothing\ntin its history of recent years\nuable otherwise. -\nBut when on? turns to a consideration of what this body\ncould accomplish under the direction of a leader with a definite\nand constructive policy, the results are different. Its possibilities\nare well nigh limitless. And we feel it not unprofitable to call\nattention to some of the directions to which it might turn its at\ntention with profit. .\nTexas has made wonderful strides in the past few years in\nproviding increased facilities for Negro education but the task\nof encouraging the increased attendance of our children upon them\nhas been left for us. In this, teachers may well lead actively.\nThey can be most effective actirg as a body. Nor are adults to be\nomitted from any scheme which looks toward the decrease of il\nliteracy. Our teachers association having perfected plans for a\ndrive against illiteracy among adults, could do more to create\nand foster the desire for education than any other agency now in\nexistence among us. To actively aid in the decrease of illiteracy\nOur teachers association hnving perfected plans for a drive\nagainst illiteracy among adults, could do more to create and fos\nter the desire for duaction than any other agency now in exis\ntence unong us. To actively aid in the decrease of illiteracy is a\npossibility of the association which not it either does not see or,\nseeing ignores.\nThe \'fraudulent procuring of certificates by teachers is dam\nning our children with crippled intellects and condemning them to\na lower efficiency than is right. Whether or not definite proof of\nspecific cases of this sort are at hand, the lack of ability of a\nlarge percentage of teachers in Negro schools lays them liable\nto such a suspicion and in the absolutely apparent lack of fitness\nfor the positions which they hold is found the justification for\nthe suspicion.\nInefficient teachers degrade the profession and rob their pu\npils. An active policy of repudiation of unfitness by the associa\ntion would ultimate in lasting good to the race and state at large.\nThese two things need to be done by some means in Texas.\nThe State Teachers Association could do them most efficiently\nwere its policy more definitely committed to construction than it\nnow is. Nor are these all. Salaries could be raised, sentiment for\nbetter buildings increased, respect for the profession stimulated\nin many quarters where it does not now exist were it differently\nconducted. r ; . \'\nWhy should this not happen? Further failure only brings\nnearer the utter disregard into which the profession must fall be\ncause of inexcussable inefficiency. \' \'\nTIIE LIBERIAN LOAN.\nThe proposed $5,000,000. loan to Liberia has gone down to\noblivion with the Dyer Bill under the vigorous onslaughts of the\nDemocratic minority. As measures they appear as dead and de\nvoid of chances for speedy resurrection as it is possible for legis\nlation to be. . -\nA filibuster of unprecedented skill the Dyer Bill. , Ridi\ncule was the death of the Liberian Loan plan. But coupled with\nthis ridicule, was a charge of what amounts to graft against fjve\nof our most prominent men which deserves to be repudiated and\nproven as ridiculous as anything ever heard in the Senate.\nSenator Pat Harrison of Mississippi charged that Dr. Em\nmett Scott, James A. Cobb, Wm. L. Houston, W. H. Lewis and\nRev. Earnest Lyons were under\nthe bill passed.\nThese charges have been emphatically denied and hailed as\nridiculous. Well might they be. We feel that such denials are\nsufficient for while it is not altogether unthinkable that such a\nthing might have occurred, it would not help us much at this\ntime to have fee grabbing proven against such of our leaders as\nhave heretofore been free from such suspicions. ;\nLiberia somehow must struggle along without the hoped for\n$5,000,000. If we had proposed a\nthe strength of her hopes of American aid she must either aban\ndon them or seek elsewhere for the finances with which to com\nplete them.\nIn spite of the fact that Liberia has failed signally to make\nprogress commensurate with her opportunity, we could have hoped\nthat America of which she is almost a pail, would have felt the\nurge to help her not cnantably,\nhastened its development to the\nself to a point of competency.\nLiberia will probably seek aid elsewhere. Probably it will re\nceive it for its natural resources are many and abundant. And\nin this seeking elsewhere it will run the risk of an exploitation\nwhich will forever blight its chances to grow substantially and\nwell. While such exploitation would not have been impossible in\nthe case of American aid it would havj been less probable in the\nlight of the history of the two countries and the relationship\nwhich-the founding of Liberia by American Christians brought\nabout.\'" . ;\nTHE THIRD INTERNATIONA\nIt has been reported that - the Third , Internationale has\n,\' dopted a resolution declaring that the United States is destined\n; play an important part i:the struggle of the African race for\nliberty and that inasmuch as America tot\' ay is the centre of the\nNegio culture of the world, it is in that country that the Corr\nmunist campaign to bring freedom to the Nvgroes of all countries\nshould be concentrated.\nThe resolution was draws up by a special commiss\'on of the\nInternationale t pointod to consider the Neja\'o question. One\nmember of the coirmision way an American Negro. v\n- The resolution, which was adopted without a dissenting vote,\ndeclares that the Negro question has become a live issue in con\nnection with efforts toward a world revolution m& that the Com\nmunist Internationale, recognizing it as such, calls attention to it\nas a problem which must be dealt with. \' , . .\nThis appeals to us as only another one of the sporadic at\ntempts which the Communists and Bolshevists of the world ure\ncontinually making first in one way and then in another to pl.nt\nthe seed3 of their insidous doctrines in the minis of American\nNegi\'es.\nIt is our opinion that this attempt like all others of the past\nwill fail. . -\nAmerican Negroes, while thoroughly dissatisfied with some\nof the circumstances attending their American existence are by\nno means willing to embrace any new doctrine which is founded\nupon re olution and anarchy. Their progress, remarkable in the\nextreme, has Leen made under the guidance "nd because of what\nBolshevists choose to call "capitalism." Their educational institu\ntions from the beginning were endowed by funds from such cof\nft. and are still being supported to no small extent from the\nsame sources, v..,. \'.\nThe progress ot Bolshevism in the countrj where it has been\nmo t tenderly embraced Russia has been enough to sicken an V\ndismay possible followers or admirers oi many lands. And if to\nar.y extant American Negroes had looked upon it favorably, and\nve I now that en masse they did not, trte fiasco in that country\n1.vould have beer enough to sicken them.\nIt h pro!;. that he whole of the affair, Resolution and ali\nis traceable to ,!ir.t lerican Negro member of tho Intt rationale\nTHE DALLAS\nTEACHERS\' ASSOCIATION.\nits highest efficiency. It gave\nthat their leadership had been\nbut meetings! social but not val\ncontract to secure $650,000 if\nroad and harbor improvement on\nbut in a way calculated to have\npoint of being able to help her\nLE AND THE NEGRO.\nEXPRESS, DALLAS. TEXAS, SATVHDAY, DECEMBER IC, 1922\nTUB BIGHT\nThe United States Senate is hogtied with a filibuster. The minority\ninsists on invoking every rule, written or unwritten, to delay tbe game and\nmake business impossible. Senator\nof the chaplain\'s prayer is omitted from the journal of the previous day.\nSenator Overman makes a point of "no quorum" and Vice President Cool\nid ge is compelled to order that the roll be called for the \'sixteenth time.\nSenator Harrison demands that the records be amended to show that "Mr.\nCoolldge made his first appearance on Tuesday at 1 p. m. A two-hour\ndebate on this momentous question follows. . \'\nAH this means that the Democrats do not like the Anti-Lynching bill\nand are willing to talk themselves hoarse in order to prevent a vote upon\nit. The situation is rather pathetic, for the Democrats have given up\ntheir traditional position upon States\'\nand again they have favored a strong central Government, but make an\nexception in reserving the right to burn Colored people at the stake.\n. In this last-ditch opposition to lynching the Democrats have furnish\ned a striking illustration of what ails them. They are bound to a section\nalism which should have been dead a generation ago. The solidity of the\nsolid South Is the most hopeless feature of the American political situation.\nUntil :he Democrats abandon the point of view which causes them to fill\nbuster for the right to lynch, they cannot gain the confidence which might\nbo theirs. \' Boston Globe.\nTHE INCONSISTENT SOUTH.\nThe solicitude of Southern Democrats for State rights and their dread\nof centralized power manifested now in connection with the Dyer Anti\nLynching bill, against which a filibuster is in progress in the Senate, seems\nconfined to issues in which the Negro figures. It is not on record that\nany Southern State refused or even hesitated to ratify the Eighteenth\nAmendment, which was the high water mark of Federal encroachment on\nthe police powers of the State. Nor\ncal in opposition to the various schemes by which Federal revenues, large\nly supplied by a few of the richer States, are -parcelled out for purely\nState purposes. So their theoretical objections to the Dyer bill will\' hardly\nb taken seriously bythe rest of the country. \'\nThe bill proposes no constitutional change, but simply designs to\nutilize existing Federal power to check the flagrant denial of equal rights,\naccorded by the .Constitution, involved In tolerated murder by lawless\nmobs, often with the connivance of State and county authorities.\n, . The Senate majority favoring\nsquarely. It may prove to be as good\n8ist, to undertake a too long delayed reform and ,end for all time the\nabuse which enables a minority to block the majority\'s will and hold up\nlegislation by tactics of pure obstruction. Phil. Bulletin.\nBAD BUSINESS.\nThe Democrats of the Senate\nerately enter upon a filibuster to\nAnti-Lynching bill, and thus tie up all the workings of the upper branch\nindefinitely. And we are especially surprised to find so ordinarily sen\nsible and patriotic a man as Senator Underwood, the Democratic leader,\nsaying that the minority would filibuster to the end of the session, if\nnecessajy, to kill the bill, which he seems to think so awful a violation\nof States\', rights Southern States\' rights, that is. ,\nNot all the Senate Democrats, however, are in this conspiracy to de\nfeat a measure for the protection of the legal..: rights of the Colored\nman in the South. Among those who have "put country above party" Is\nWalsh, ot Massachusetts, and there are several more who believe, with\nhim, that a trial, at least,\' should be made of the bill to wipe out one of\nthe standing disgraces of our American civilization. \' Boston Post.\nTHE\nThe chief objection offered to the Dyer antl-lynching bill is that it is\nan invasion of states\' rights. This matter was threshed out when the bill\nappeared before the House.\' Its opponents then went so far as. to state that\nif it. were passed the states would cease to exist except as geographical\nexpressions. . \' \'\nAs a matter of fact, there has long been indecision as to the boun\ndary between Federal and state rights in the exercise of the police pow\ner. This the Dyer bill aims to eliminate. If the state or county fails to\nprevent lynching its officials become accountable to the Federal govern\nment. It is Simply a matter of substituting Federal enforcement for local\nnon-enforcement. The state is under a pledge to protect Americans and\nforeigners within Its borders. If it\nremedy is there than for the Federal government to step in?\nFew Indeed are those persons who attempt to Justify lynching. But\nit is not enough merely to deplore this barbarous practice. Of what good\nare protestations of horror about lynching when these same protestors,\nwho have .the power to put an end to it, are unwilling to support meas\nures designed to achieve this purpose? N. Y. Tribune,\nTHE DYliU\nThere is very, patent determination on the part of opponents. of the\nDyer so-called antl-lynching measurft now being discussed in the Senate,\nto prevent its enactment during the present extra session of Congress. \'\nIt Is a very harmful measure," not only not a preventative against\nlynching but In d\'rect violation of the rights ot the states 10 make and\nadminister their own laws." V\n., Senator Shields of Tennessee has given much study to the matter of\nthe constllitutionality of such a measure as well as its resultant effects.\nHe is leading the fight aainst its enactment and other Senators, notably\nthose from the South, are giving brilliant aid. Of course, it is a filibuster\nadmittedly so, but so grievous an\nstatehood, so outrageous the measure\'s aims, that such oppos\'Mon is thor\noughly Justified.\' -\nMr, Dyer cf MIsbouH, a Republican, is the author of this measue.\nwhich\' has already passed the House.\nuntil Saturday night, it will not pass,\nst-ssion adjourns sine die.\nTO REKINDLE\nNearly. ,32 years The St Se has denounced lynching and lynchers\nLynchers in Sout\'a Carolina U has repeatc.lly described, as "murderers." be\ncause those who take human life without excuse and without Justification\nin the law of the Btate come within\n. Long years the battle has been\nlint. Long years the contraditlons and inconsistencies of its apologists\nhave been exposed. Pulpit and press together with the great body of\nthinking men and wcr. en who know that for a state is ao security out\nside of law have carried on unfalteringly this campaign.\nRemarkable progress has been\nNorth Carolina in io Southern state\ngressively arrayed against lynching\nother hus the crime been so nearly\nthe two Southern Btates In which Ne\nbeen reduced to a point far below\nlargely, composed of immigrants from\nThese things being true and indisputable, it is no comfortable pos\nture into which T;e State Is driven when it opposes a measure that has\neven the color ot intent to prevent\nwho chose himself as "speaker for his race.". And the whole of\nthe discussion which will eventuate from the publication of such\nreports will be caused by him a seeker for publicity of whom\nthe masses have not heard. .\nHe, and all others like him who would thus "speak" for us\nshould be loolred upon with the. utmost disfavor andshowed by no\nuncerta\'n means; thrt his energy might have been more efficiently\ndirected into different channels.\nAmerican Negroo.s do not favor communism and Bolshevism\neven for Russia. As for themselves and their progress m Amer\nica, they feel that other mean3 will produce for them more last\ning and substantial results.\nr tH& MKROfi\nTO LYNCH.\nHarrison wants to know why the text\nrights on aU issues except one. Time\nhave Southerners In Congress been vo\nthe bill should meet the challenge\na time as any. if the filibuster per\nare in bad business when they delib\nprevent the consideration of the Dyer\nANTI-LYNCHING BILL.\ncontinues to fail to do so, what other\nATROCITY.\ninfraction againsf the sovereignly of\nIf action upon it can be held up\nthat being the timo when the tra\nNashville Banner.\nTHE OLD FLAME.\nthe only definition cf murder. \'\nwaged against lynching In Sojth Care\nrecorded. Except in Virginia and\nis Intelligent public opinion so ag\nas in South Carolina and in none\nextirpated. South Carolina la one of\n;roes are a majority, yet tMs evil has\nthat of Florida, whose population Is\nthe North. .\nlynching and that kind of measure\nREPUBLICANS AGREE TO AM-\nDON DYER Ulliii.\n(By A. N. P.)\nWashington, D. C, Dec. 14. The\nDver Anti-Lynching bill is dead.\nThe Senate Republicans capitulat\ned after futile effort to break down\nthe Democratic filibuster which had\ntied up ail business since Tuesday.\nAgreement to accept defeat and\ndiscard the measure was reached at\na atormv two-hour caucus. Senators\nwho took a lead In the attempt to\nforce action in the Senate demanded\nthat the battle should be carried on.\na majority, however, decided other\nwise. IxMlire Admits Defeat. ,\nAnnouncement of . the surrender\nwas made by Senator Lodge, the\nRepublican Leader, "i am author\nized to notify the other side that\nwe will not press the bill at the\npresent session or in the next ses\nsion." he said.\nThe present session\' ends at 12\no\'clock Monday. The next session\nbegins at once and continues until\nnoon, March 4. With the bare mar\ngin of control held by the Repub\nlicans in the next Congress, there\nis no hope that the Dyer bill can\nbe brought out again. It would\nhave to start all over and be paps\ned anew by the House.\nNominations Pile Up.\nThe action of the Republicans\nmarks a complete victory for the\nDemocrats. They started their fill\nbuster early in the week with the\nopen statement that the Senate\nwould not be allowed to transact\nany business whatever, riot even\nconfirmation ot important nomina\ntions, while the anti-lynching meas\nure was agitated.\nPERRY HOWARD DOES HIS BIT\nTO DEFEAT DYER ANTI-LYN\nCHING BILL.\nNew York, N. Y., Deo. 14.\nAstoundlner revelation of the act\ntivity of Perry W. Howard in an\nerrort to rtereat tne iivpr Ann-ivnrn\nlner Bill was made todav at the of\nrice or the National Association for\nthe Advancement of Colored People\nwhich made public the text of a\nletter written by Mr. Howard to\nSenator T. Coleman Du Pont of\nDelaware. This letter was dated Nov.\n23. 1922, just three days after the\nopening of the special session and\nat the time when every "effort was\nbeing made to have the bill brought\nup and pushed. In this letter, Mr.\nHoward declares he has "blood in\nhis eyes" for\' the N. A. A. C. P.,\nwhich he calls a "Negro Democratic\nOrganization" and insists that no\nUgislation ought . to be enacted by\nreason of the N. A. A. C. P., or its\nsympathizers.\nMr. Howard\'s letter is in part as\nfollows): .\nI received your letter of Nov.\n22, upon my return from thn Went\nwhere I have been almost the en-\nlire time since the close, of the cam\npaign. I confess to you that I have blood\nin my eyes for the National As\nsociation for the Advancement of\nColored Peoplle and others who\nused them with sinister designs to\nneieat some or the best friends that\nwe have in particular and the Na\ntion in general.\nThe purpose of this letter is to\ncall attention of you and other out\nstanding statesmen to the fact that\nthe National Assoclat Inn tnr lha\nAdvancement of Colored People is\npureiy a JNegro Democratic organi\nzation and has always been found\non the side of the Democrats in the\nfinal analysis. This organization\nwas used by Bob Nelson and others,\nand you owe your defeat to no oth\ner agency. I corrected the evil in\nWilmington, and if I mihIh hot..\ngone into the Dover neighborhood\non me rouowing Monday, I could\nhave saved the day, but I had an\nengagement to fill for Sneator Fre\nlinghysen and you know It was im\npossible for me to do so.\nNow, I may call attention to the\nfact that whatever legislation or\nwhatever else is done for the Color\ned people of thla rnnntrv nnki\ncertainly to be done and done\npromptly, but I insist that none of\nit ought to be done through or by\nreason of the National Association\nfor the Advancement of Colored\nPeople or any of Its sympathizers.\nI therefore think that it should\nbe the policy of the leaders of the\nparty like you to absolutely ig\nnore and give the back of your\nhand to such\' men as Nelson, Jumes\nWeldon John, W. E. B. DpBols, Rob\nert L. Vann, of Pittsburgh and\noihers of their like. Thbre should\nbe quarters; and while treating\nevery Colored man with fairness\nand looking well; to the Interest of\nour group, the po! leal bolshe\nvists should be annihiliated as it".\nbasest of ingrates.\n(Signed)\nPerry W. Howard. .\nIn Mr. Howard\'s letter to Senator\nDu Point, It was Btated that copies\nvere being sent to Senators Frellng\nhuyscn, McCormlck. Watson and\nMoses also to Attorney General\nLaugherty, who It is to be pres\numed would be interested in the\nnharge by Mr. Howard that Messrs.\nNelsou, DuBols, Jamee Weldon\nJohnson and Vann \' are "political\nKolshevlsts." Copies of Mr. Howard\'s\nletter were furthermore sent to\nCharles Adams, ciialrrnar. of the Re\npublican National Committee and\nwere (circulated among prominent\nRepublican Senators.\nMr. Howard\'s reference to his\nwillingnless to "save the day" -in\nthe Dover neighborhood will in\nterest those Colored readers who\ntl.a Dyer bill is. . -\nThe Dyer bill is not aimed at the suppressing ot mob liw It on\ntemplates no punishment of the, mob of "Ueds." or strikers, or of hired\nthings of a coal operating company. It ignores consideration that lynch\nIng is a crime directly proportionate to the pollcjng of a community or\nlack thereof. It discriminates In favor of Ce city with its army of eqttlp\nped officers that may be mobilized with anm-oved nn.r.in. v\n.pression against the spar-iely settled\n. o4U.0 mi0. ii is a notion alien to the theory and his\ntoric practice of American constitutions bom in the minds of a racial\ngroup egged on by present day represenatis of fanatics who 60 years\nago relied on lynching the constitution to carry their point and openly\nboasted it. Its strength in Congress sPri-Bs out of the fear of the po\nit\'ea . strength of th\'r group in certain doubtful states. It is a sample of\nthe class o measure, without merit ot their own that make progress in\nlegislation in front of the flying wedge of n minority with a single idel\nPassage ot the Dyer Bill would threaten the t-Jcl.g of atffat 1ms\ntarn accomplished to put down lynching m the South. The bill reeks w "b\n(ho germs of the Reconduction shamo. Insistence that fedf.1 courts bo\nlevolutionized fcr Its sympathetic enforcement would toMol Hn e actment\nTat would reopen he old quarrel, rekindling the embers ot Tac anUgo\nnism to consuming flame, - Qol C.) SU e\nrecall that the National Association\nfor the Advancement oi oiorea\ninstrumental in defeating\nDr. Caleb R. Layton, Delaware\'s,\nRepublican representative on . me\nsole ground that he misrepresented\nhis constituents by voting against\nthe Dyer Anti-Lynching Bill in the\nHouse of Representatives.\nThe defeat of Dr. Layton, largely\nthrough the enthusiastic and able\nwork of Mrs. Alice Dunbar Nelson,\nwas regarded among colored people\n. won nn am one white politicians\nas one of the most convincing dem\nonstrations of solidarity and power\never evinced by uoiorea voters in\na marina Delaware became an Ab\nject lesson to the Republican Party\nshowing the str.ong reeling among\nColored voters about the Dyer Bill.\nIt la nrpnnmahlv against this DrOUd\nachievement of Colored voters ac\ncomplished on the sole Issue or tne\nDyer Anti-lynching bill, that1 Mr.\nPerry Howard could have "saved the\nday."\nMr. Howard was appointed to an\noffice in the Department of Justice\nby Preesident Harding. Mr. Howard\nIs also a close personal frlflnd of\nSenator Pat Harrison of Mississippi.\nIt was Senator Harrison who helped\nto defeat hte Liberian Bill by stat\ning that hejiad "information" that\ncertain Colored men were to bene\nfit heavily financially by the pas\nsage of this legislation.\nIn the same tenor as Mr. Perry\nW. Howard\'s letter was a paragraph\nwidely circulated among the Colored\npress asserting that lobbying by the\nN. A. A. C. P.. would hinder enact\nment of the Dyer Antl-lynching bill\nbecause Republican leaders regarded\nthe Association as a "Democratloe\nally with Socialistic tendencies."\nMOSCOW DEMANDS AFRICA FOR\nNEGRO.\nSoviet Russia Reaffirms Her Per.\nRistent Stand AipiliiNt Imperialist\nExploitation.\nOITOSES LAND GOBBLING.\nGeneral Sclf-Detenniiiation Her\nPeace Solution for AVar Wracked\nWorld.\nLaussane, Dec. 14. (Crusader Ser\nvice) Speaking to Press Correspon\ndents here on the action of the Al\nlies in barring his country from\nall conference discussions except\nthose that concern the Straits, M.\nRakoviky, Soviet delegate to the\nLausanne Peace Conference.\n"Russia" he said, "came to Laus\nanne only for free and equal dis\ncussion, taking the position that\nany one affected has the right of\nfull participation, There are .no\nmore superior and Inferior nations.\n"We .have only one solution for\nTurkish and Straits questions. Oth\ners have several and are ready to\nnegotiate. If it is to be a patched\nup peace, any solution is good\nenough, but If it is to be a durable\npeace, ours is the only one which\nwill satisfy all peoples, East and\nWest. -\n"We Want to see. Turkev m-esrtnh\nUsed on the nationalistic, non-ag\ngression basis the Angora Assembly\nannounced. We want to see the\nsouthern frontiers fixed on this basis\nso the Turks can live in neighborly\nequality with the Arabs and other\nneighbors. The question of Mosul,\nwhich is playing such an important\npart in blocking progress, should\nbe treated as a question of pjop\nulatlo\'n, not of oil.\n"The British claim it because\nit is oik. As Turkey\'s friends we\nwant to see the question treated\naccording to population.\n"Our one and only solution is to\nsee Turkey reconstituted strong\nenough to defend the straits, under\nan agreement to let commerce pass\nfreely, but no battleships.\n"That Is the only way to tran\nquilize the Near East. No Western\nPower could then dominate the Eas\ntern Mediterranean nor could Rus\nsia threaten It.\n"The British ought with the oth\ners to accept bur solution. If they\nhad succeeded In giving Constanti\nnople to the old Russia as planned,\nRussia would have the Eastern Med\niterranean and been a constant dan\nger. But with Soviet Russia confined\nto the Black Sea, defending itself\nas we propose and no one with\nthe right to attack us through the\nstraits, the troublesome question Is\nBettled. For details any one of a\nhalf dozen agreements made in the\nlast hundred years would do.\n"We are told our Interest Is sim\nply Jn the straits. Even if that is\nso, we are interested In the is\nlands in the Aegean, which can\ncan control the straits. We are in\nterested in the dispositian of the\nfrontier around Adrian iple, whloh\nis Constantinople\'s fortification. We\nsay, as Ambassador hlld said, that\nthere Is no way to separate one\nTurkish question from another. As\nwe have one fixed principle for a\nettlement we can afford to wait."\nAsked if leaving ! nrkey In com\n,\'lete control of the straights might\nnot easily block Russia, as hnppen\ned at the beginning of the lust\nwar, Rakovsky replied,:\n"It is Impossible to provide\nagainst everything .Ours is a choice\nbetween a small nation controlling\na waterway within its own terri\ntory or leaving It to the strongest\nforeign powr. We prefer Turkey.\nWe want Turkey for the Turks,\nArabia for the Arabs. Africa for\nthe Africans, as Russia is for the\nRussians. Accedental geographical\nfatets like the Dardanelles anU Mosul\noil must conform."\nrural district of a county with one',
       u'page': u'PAGE FOUR',
       u'place': [u'Texas--Dallas--Dallas', u'Texas--Harris--Houston'],
       u'place_of_publication': u'Dallas, Tex.',
       u'publisher': u'W.E. King',
       u'section_label': u'',
       u'sequence': 4,
       u'start_year': 1000,
       u'state': [u'Texas', u'Texas'],
       u'subject': [u'African American newspapers--Texas.',
        u'African American newspapers.--fast--(OCoLC)fst00799278',
        u'African Americans--Texas--Dallas--Newspapers.',
        u'African Americans.--fast--(OCoLC)fst00799558',
        u'Dallas (Tex.)--Newspapers.',
        u'Dallas County (Tex.)--Newspapers.',
        u'Harris County (Tex.)--Newspapers.',
        u'Houston (Tex.)--Newspapers.',
        u'Texas--Dallas County.--fast--(OCoLC)fst01205630',
        u'Texas--Dallas.--fast--(OCoLC)fst01204635',
        u'Texas--Harris County.--fast--(OCoLC)fst01213346',
        u'Texas--Houston.--fast--(OCoLC)fst01205077',
        u'Texas.--fast--(OCoLC)fst01210336'],
       u'title': u'The Dallas express.',
       u'title_normal': u'dallas express.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn83025779/1922-12-16/ed-1/seq-4.json'},
      {u'alt_title': [],
       u'batch': u'batch_ndhi_kramer_ver01',
       u'city': [u'Beach', u'Beach'],
       u'country': u'North Dakota',
       u'county': [u'Billings', u'Golden Valley'],
       u'date': u'19140109',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1916,
       u'frequency': u'Weekly',
       u'id': u'/lccn/sn89074109/1914-01-09/ed-1/seq-6/',
       u'language': [u'English'],
       u'lccn': u'sn89074109',
       u'note': [u'"Official paper of Billings County, " 1908-1910.',
        u'"Official paper of Golden Valley County," [1913].',
        u'"Official paper of the city of Beach," 1910-[1912].',
        u'Archived issues are available in digital format from the Library of Congress Chronicling America online collection.',
        u'Available on microfilm from the State Historical Society of North Dakota.',
        u'Description based on: Vol. 2, no. 8 (Jan. 10, 1907).'],
       u'ocr_eng': u'Page Sis\nit-/::-\nCYilNttLRS\nGas or Steam\nRebored or ground\nPISTONS\nIf\nWhy not get good,\nsensible overalls that are\nboth comfortable and\ndurable?\u2014\nOveralls like Gloverallsl\nYou get more service out of\nGlover Overalls than any others\nbecause there is more material\nand good workmanship in them.\nare cut extra long and extra wide in the\nseat, and extra wide in hips and legs. This\nmeans less strain with more wear and com*\nfort. None others compare with them.\nIt takes more cloth than most manufac*\nturers are willing to give for the money,\nbut it means reputation and a bigger de?\nmand for Glover Overalls.\nWith all the greater comfort and dura*\nbility offered, you also get overalls thai\nare better made and better looking than\nany you probably ever wore before.\nHereafter ask for Gloveralls.\nThey are here. Come and see them.\nModel Clothing Company\nBeach, Sentinel Butte and Belfield\nMandan Wants\nBranch of A. C.\nMay Be Established at Mandan In Con\nnection With Experimental Station.\nMandan, N. D., Jan. 7.\u2014That\nbranch of the North Dakota agricul\ntural college will be started at Man\ndan and operated in connection with\nthe Northern Great Plains Experi\nmental Station, is the firm belief of a\nnumber of local men interested in the\nreport.\nSuperintendent W. A. Peterson of\nBeach Opera House\nthe government experimental station,\nwho left the first of the week for\nWashington, D. C.\xab stopped off at\nFargo and held a conference with\nPresident Worst of the N. D. A.\nand other members d( the faculty.\nThree branches of study are sug\ngested. Animal husbandry, horti\nculture and aborculture.\nBut little is being given out to the\npapers at the present time as the pro\nmotera are hesitant for fear their plans\nmight be awry however, there is\nmore than mere talk in the wind! and\na proposition that will be of state\nwide interest is expected to be an\nnounced in the near future.\nC. F. SMITH, Manager\nHigh Class Motioi Pictures\nFour Changes a Week\nChange days are Sunday,\nMonday, Wednesday\nand Friday\nAdmission 10 and 15c\nA Man Must Eat\nFor short orders and first-class\nservice you will always get it at\nMachine Simp\nWALLS\' EAT SHOP\nOpen Day and Night. Tables for Ladies.\nH. J. WALLS, Prop. Beach, N. Dak.\nMade to order any kind\nRINGS AU sizes\nBRASS FOUNDRY\nAluminum anH\ni"1 Brass Castintr*\nKv\nI Vv\nGive a trial.\nit\n"st\nf\' f\u2022\niaragp Now\nNone Censured\'\nfor Lynching\nAtty. General Miller Turn* in Weak\nkneed Report, Making No Recom\nmendation* Retarding Lynching.\nAn official report of the midnight\nlynching of Steve Culbertson at Wit\nliston, Dec. 15 was presented to Gov.\nHanna yesterday by Attorney General\nMiller.\nNo recommendation i\xab made as to\nthe future action of the state author\nties in the matter and the attorney\ngeneral made no attempt to fix the\nresponsibility for the killing. He sim\nply reported all the circumstances in\nthe case\nto\nthe governor and left fur\xad\nther course of action to his discre\ntion.\nIn his report Mr. Miller describes\nthe conduct of Sheriff Erickson with\nout comment, specifically stating that\nthe governor himself njay decide\nwhether or not the official was re\nsponsible in any way for the occur\nence. He states that Erickson has\nbome an inviable reputation about\nWilliston.\nFollowing is the report:\nIn accordance with your verbal re\nquest under date of December 18,\nA. D., 1913, and the request of\nState\'s Attorney Burdick, of Williams\ncounty, to proceed to Williston to as.\nsist the state\'s attorney and other of\nficers of Williams county in investi\ngating the lynching of Steve Culbert\nson, a prisoner recently convicted of\nmurder in the first degree and sen\ntenced to life imprisonment,-, which\ntook place on the night of the 15th\nand 16th of December, 1913, I im\nmediately proceeded to Williston,\nleaving here on No. 4, December 19,\nand arriving at Williston Saturday\nmorning Dec. 20, over the Great\nNorthern on No. I.\nDuring my stay in Williston, 1 talk\ned with the various county officials\nand citizens generally and examined\nand took the statements of numerous\npersons as to what happened, more\nparticularly the statement of Carl\nErickson, the sheriff, Mrs. Erickson\nhis wife, J. C. Bromberg, driver of\nthe Williston fire department, Dan\nBrennon, Joe Scrivener, Duffy Peter\nson, George A. Bissel and Frank\nLunceiord, a night poHcenwm, oil o(\nwhich statements are hereto attached\nand made a part of this report.\nAs near as I was able to ascertain\nthe fact, the prisoner was found guilty\nby a jury and the sentence fixed ac\nlife imprisonment on the Friday pre\nceding the lynching, which occurred\nabout two o\'clock, Tuesday morning,\nDecember 16th. He was formally\nsentenced by the judge on\nMonday\nmorning, December 15 th, that being\nthe Monday preceding the night of\nthe lynching. The reason that the\nprisoner was not removed to the pen\nitentiary the day he was sentenced\nwas that another charge of murder in\nthe first degree was pending against\nthe prisoner and the state\'s attorney\ndesired the prisoner to be arraigned\non this second charge before he was\nSentinel\nButte\nSaddlery Co.\nWholesale and Retail\nDealers in\nSaddles, Bridles\nCharparajos and\nCowboy Outfits\nHarness & Strap Work\nF. W. WERNER, Mgr.\nShoe Repairing\nOpen\n\xab\'.l cut to wider\nSvstem\n-t-.it\nMetal\n\u2022\u2022\u2022V\n7\n\'"ire\nGOLDEN VALLEY CHRONICLE\nPoker door. 8\u2014Copn out*id of firr\nuroperly enclosed 9\u2014Mai\'\nrd draft. 10\u2014T\' r\xabf ply w~"\ni\xbbh pit and ovc- I\'- T\'\n*r in every oven r. T.-\n1\n.-Mom. \'.r\nconveyed to the penitentiary, and it\nalso appears that the formal papers of\nthe commitment to the penitentiary\nhad not, on Monday, been formally\nsigned, it being the intention of the\nauthorities to arraign the prisoner on\nthis second charge the Monday pre\nceding the lynching, and to convey\nthe prisoner to the penitentiary the\nfollowing day.\nThe jail of Williams county is lo\ncated in the second story of the court\nhouse. The sheriff\'s residence is also\nlocated in the court house. The court\nhouse itself is located immediately\nacross the street from the fire depart\nment and is a substantial, two-story\nbrick building. The outside is pro\ntected by two iron or steel doors, an\noutside heavy grate door protecting\n\u2022\xbbn inside solid steel door. On the\nnight of the lynching this outside\nsolid door was closed and properly\nlocked. The sheriff\'s explanation of\nthe outside steel grate door being\nopen is that this outside door was\nnever kept closed or locked by him\nexcept during the summer time when\nthe inside door would be left open\nfor ventilation. Passing through this\nentrance to get to the jail, you pro\nceed through a corridor to the south\nside of the jail and then up a narrow\nstairway to the second floor. On the\nsecond floor, and immediately to the\neast of the stairway landing, is one\npart of the jail, protected at its en\ntrance by a heavy grate, steel door,\ninside of which are two steel cages\nwith a narrow corridor in front there\nof leading to the east wall of the jail.\nThe prisoner on the night of the\nlynching, was kept in the east cell\nof this part of the jail. To get to\nhim it was necessary to force the\nheavy door guarding the entrance to\nthe corridor and also the heavy steel\ngrate door to.-tke prisoner\'s cell.\nIn the south half of the upper story\nof the court house are two other pri\nsoners\' cells or cages, the entrance to\nwhich is guarded by a more modern\nand heavier, solid steel door, operat\ned by levers enclosed in a solid steel\ncompartment. To effect an entrance\nto this door the steel compartment\nenclosing the levers must be first op\nened and then the door itself opened\nby operation of the levers. After\npassing this entrance you enter a nar\nrow corridor, completely surrounding\nthe two steel cells or cages, each of\nwhich is protected by a modern,\nheavy steel grated door. In this part\nof the jail were kept two prisoners\nserving time on charges of bootleg\nging.\nThere was considerable talk im\nmediately upon the arrest of Culbert\nson and at all times up to the time of\nfend during his trial, of lynching, but\nexcept at the immediate time 6f his\narrest and until immediately after his\ntrial it seems that the officers did\nnot take the rumors of lynching seri\nously. However, on the Sunday morn\ning following his trial it appears that\nabout twenty or twenty-five masked\nmen appeared at tii4 jail at about four\no\'clock in the morning, and aroused\nthe sheriff and wanted the prisoner\nThe sheriff, however, parleyed with\nthe men and advised them that the\nprisoner would not be surrendered,\nand telephoned the city police depart\xad\nIN GOLD TO BE GIVEN AWAY FREE\nTO THE LODGE, CHURCH, CLUB ORGANIZATION OR 1N1VICUAL, FOR SAVING CASH\nCHECKS FROM MERCHANTS ADVERTISING ON THIS PAGE\nFor the benefit of the large number\nof shoppers who use The Chronicle\nas medium in which to find excep\ntional purchasing opportunities, this\npaper offers the following inducements\nto its readers and friends to carefully\nread the advertisements cm this page.\nA grand prize of $75.00 in gold will\nbe given to the individual or any form\n\u201eof organization or institution turning\nin the greatest amount of money\nshown on the cash checks or receipts\nand monthly bills secured at any o#\nthe stores in the Bargain Feature.\nTo the candidate securing the sec\nond largest amount, $15.00 in gold.\nTo the third, $10.00 in gold.\nExplanation.\nTo secure votes in the Chronicle\nBargain Voting Feature, make your\npurchases at the stores advertising in\nthe Bargain Feature, and call for a cash\ncheck or receipt for every purchase.\nTurn these checks in to the Chron\nicle\noffice, where votes will be given\nfor the amount shown on each check.\nFive votes will be given for every 5\ncents shown on the cash check, re\nceipt or monthly bill.\nAll votes will be deposited in the\nvoting box at this office.\nSales checks must represent cash\npurchases or cash paid on credit pur\nchases. All cash checks, monthly bills\nand deposit slips must be turned into\nthis office within 10 days of die pur\nchase date.\nEvery contestant entering the con\ntest is entitled to one nominating cou\npon good for 1000 votes. The only\nexception to the contestants entering\nThe Bargain Feature are those em\nployed at this office or the stores ad\nvertising on this page.\nAt the theatres advertising in The\nBargain Feature call for a cash check\nDon\'t buj a range until you see I lie A CI lACHM\n\'Rings Elstssl lor the home. For sals by -f^s l^fe \xa3L1 HlSUll\nRing\nTHJHI PAH I ICtJI KH I\'OUi IS\ni\u2014High back on all high closets. 2\u2014\nSubstantial nickel plated channel steel\ncloset brackets. 3\u2014Heavy nickel plated\nsteel tea shelves. 4\u2014Three ply wall at\nbark end of (ire box. 5\u2014Ver\xbb holes in\nal\' linings. \u20146\u2014Verv larpe pouch f.-H\nment for assistance. The night po\nliceman, in answer to such call, ap\npeared upon the scene and thereupon\nthe masked men retired and disap\npeared. The sheriff, in his statement,\nsays that at that time he didn\'t con\nsider the matter very seriously and\nwas rather inclined to think that the\nvisit of the masked men was in the\nnature of a joke, and that there was\nat the time, and immediately prior\nthereto had been, rumors of a, pro\nbable lynching, yet he personally\nscouted th4 idea.\nIt appears that about two o\'clock\nTuesday morning following, and while\nthe sheriff was asleep at his residence\nin the court house immediately below\nthe jail, he was aroused by noises out\nside the jail. He immediately arose\nand went to a window and there saw\na crowd of masked men surrounding\nthe jail and court house. That he at\ntempted to remonstrate with them\nbut was cursed and told to get out of\nthe way or they would blow the top\nof his head off. That the men were\narmed, and particularly the man who\nthreatened him was armed with a\nlarge revolver, and that he believed\nthat\nit\n(IF eiern-ai excellence\nduct \'6\u2014Damper in HI\xabI joiut\nhe interfered they would kill\nhim. That thereupon he attempted to\ntelephone to the chief of police for\nhelp but was unable to get central.\nThereupon he instructed his wife to\ntelephone for assistance and he im\nmediately repaired to the upper story\nof the court house in which the jail\nis located and, as he explains it, for\nthe purpose of preventing them from\nobtaining the keys he entered the part\nof the jail located on the south side\nof the building and away from that\npart of the jail in which the prisoner\nwas located locked the doors after\nhim, and remained therein during the\ntime that the masked mob battered\ndown the outside door, entered the\nbuilding and forced the two doors\nleading to the prisoner\'s cell and un\ntil after the men had removed the\nprisoners from the jail. That during\nsuch time he was engaged in trying\nto attract the attention of the general\npublic and in calling for aid by holler\ning and shooting, through a window\non the west side of the jail, but not\nshooting at any of the masked mob.\nThat at the time he was armed with\ntwo automatic revolvers, both loaded,\nand had plenty of ammunition.\nHe did not at any time face the\nmob or threaten them, but did re\nquest that they desist from any at\ntempt to take the prisoner, both\nwhile in his bedroom down stairs and\nduring his own incarceration in the\nsouth part of the jail. Such requests\nwere unheeded except by threats of\nshooting him if he appeared upon the\nscene.\nThe instrument used in forcing the\njail was a large iron water main,\nabout twelve feet long, and a crow\nbar and a sledge, and the period of\ntime about twenty or twenty-five min\nutes. There was so niuch noise made\nby the mob in forcing entrance to the\nprisoner\'s cell that yhe, the sheriff,\nwas unable to distinguish what was\nbeing said, either by the mob or the\nprisoner. According to persons out\nside, among them a trusty serving a\nsentence, who had an opportunity to\nsee the masked mob and the pr:sncr\nWhen we opened this store\nit was our determination to\nserve the people of the Golden\nValley better than they had\never been served, both in qual\nity of merchandise and in store\nservice offered. That otir ef\nforts are appreciated is proven\nour rapidly growi gbusi-\nThe Power\'s\nCL\nDrop door with nickel plated\n\u2022t -Full polished tops on all\n11-\nIf I-\'\nrant\noir\nHeavy copper lined reser-\n-i 20\u2014Arched oven top. 21\nMalleable iron reservoir top. 22\u20141\nI\'d towel rod 23\u2014Heavy\n-ith steel asing. 24\u2014\n1\n\u201e\'S\n1\n-Thr\xab\xbb-\n-.t-v wvM\n\'-S.WW\nwhen he was led from the jaij, it ap\npears that" the prisoner was led from\nthe jail\' with a rope about his neck\nacross the court house lawn, and as\nsisted over the iron rail fence sur\nrounding the court house, a fence\nabout four feet high that in going\nover this fence the -prisoner fell ajsd\nwas hurried and half dragged for\nabout a block east or perhaps a\nblock and a half, to sin automobile in\nwhich he was placed, and from there\ntaken to a bridge about a half or\nthree-quarters of a mile east of the\ntown, and hung to one of the iron\ngirders of the bridge where the road\ncrosses a stream called the "Little\nMuddy." Eleven bullets were also\nfired into his body at this time.\nThe newspaper rumors that the\nprisoner begged piteously for his life\nand was dragged behind an automo\nbile, so far as I was able to discover,\ndo not appear to be true. The only\nstatement made by the prisoner, so\nfar as I have been able to discover,\nwas that at the time he was led across\nthe court house lawn he requested\nthat he was not the man who mur\ndered the Dillons. I have, however,\ntaken the pains to carefully read the\nstenographic report of the trial, and\nam fully convinced beyond any possi\nble doubt that he was the man who\nmurdered the Dillons.\nI am also attaching herewith, for\nyour consideration, a complete steno\ngraphic report of the trial, as well as\na transcript of the ateemerits made\nby the sheriff and the various persons\nI examined. From my investigation\nit would appear to me that Carl\nErickson, the sheriff, is a good citizen\nand stand* high in his community.\nHis honesty and integrity are not\nquestioned by anyone, so far as I was\nable to ascertain. As to his conduct\'\nand actions in respect to the care and\nsafety of the lynched prisoner in his\ncharge, and the extent to which he\nshould be held accountable for the\nlynching, by reason of such conduct,\nyou can best determine for yourself\nfrom the statements of the witnesses\nattached hereto, including his own\nstatement and that of Mrs. Erickson\nhis wife. My personal views on that\nphase of the subject were fairly ex\npressed to the sheriff. So far as 1\nwas able to learn and determine, it\nappears that the state\'s attorney and\nthe sheriff were, and are, busily en\ngaged in good faith in making a thor\nough search for the guilty persons\nwho took part in the lynching.\nRespectfully,\nATTORNEY GENERAL.\nItills Coyote\nWith His Auto\nNew England, N. D., Jan 6.\u2014C. L.\nHarrison ran down and killed a coyote\nwith an auto. He and three compan\nions were in the car when they saw\nthe animal. The other two concealed\nthemselves with the hope of getting a\nshot while Harrison gave chase across\nthe prairie after his wolfship. After\na seven-mile run he overtook and kill\ned the coyote.\n&\n\u2022.\'\nFRIDAY, JAN. 9.1914\nSuffered with\nThroat Trouble.\nMr. B&meg\nused to be\nsheriff of\nW a\ny,\nTennessee.\nFrom ex\nposure to\nthe elements\nhe acquired]\na\nHe p-1\nposed thatl\nhis health\nWas entirely\nruined, in\nspite of all\nthe treat\nment he\ncould pro\ncure.\nA\nusing four\nbottles of\nParana he\nclaims that MR. B. W. D.BARNES,\nhe was en- MrMfamllls T\u2014n\n1 1\nrestored to health.\nCatarrh of the throat is not only au\nannoying disease of itself, but it ex\nposes the victim to many other dis\neases. We are constantly breathinc\ninto our throats numerous atmos\npheric germs. Disease germs of all\nsorts. This cannot be avoided.\nTUP Dip CTADC E- E- DICKINSON\nI ilL DlU OlVSlxE*\nHENRY SUNDERS\nDealer in\nGeneral Merchandise\nComplete line of\nGroceries, Shoes and Dry Goods\nAgency for Fahrney\'s Alpenkranter\nWe are Giving Away a $200 Grafonola\n$60 Sewing Machine\nTwo Gold Watches-\nCAM AT OUR STORE FOR PARTICULARS\nIf\nthe throat is healthy the system is\nprotected from. these poioonoua\ngerms. But if the throat is raw and\npunctured with numerous Uttle ul\ncers, by catarrh, then the disease\ngerms have \xabasy access to the systems\nKeep the throat well and clean.\nThis is -the way to protect yourself\nagainst contagious diseases. Oargte\nthe throat as explained In the new\n"Ills of Life,\'* sent free by the Peruna\nCo., Columbus, Ohio.\nMr. Barnes says: 1 had throat\ntrouble and had three doctors treat\ning me. All failed to do me any\ngood, and pronounced my health (ma\nI concluded to try Peruna, and after\nusing four bottles can say I was en\ntirely cured."\nAsk Your Druggist for Free Per\nuna Lucky Day Almanac for 1914\nCollier\'s\nThe National Weekly\nFirst Time\nin Club\xbb\nCollier*\nUntil this year\nCMn\'t\nhas been\nsold at $5.50. Now\nthe price is $2.50\nand wehave secured\na concession where\nby we can offer it\nat a still further\nreduction in con\nnection with this\npublication.\nial Offer to Onr Readers\nReoocnisins the-greet demand for CMhrV st\nthe aew price, we have msde srrsnsements to\noffer it and our own publicstion esch one yesr\nfor the price of CiShr\'sslone. This is a limited\noffer sad must be\ntaken ad vsntsge of promptly.\nWhat Yon Get in Collier\'s\nr\xbbIir is the one bis, independent, fearless\nweekly of the whole country. Not only is it thi\ngood citizen\'s handbook but it is also\nmagazine tor the whole family. Among the\nthings that a yesr\'s subscription sivcs sre\n1000 Editorials\n600 News Photos\n280 Short Articles\n180 Sheet Stories\n.100 Ohutratad Fastens\n2 Complete Nor.W\nCollier\'s.... $2.50\nChronicle* $1.50\n$100.\nII/E wish to thank our many\npatrons who so willingly\nhelped move our goods during\'our\nHoliday Sale.\nBsfrfcrsJy\n$2.50\nPres. and Manager\na.',
       u'page': u'',
       u'place': [u'North Dakota--Billings--Beach',
        u'North Dakota--Golden Valley--Beach'],
       u'place_of_publication': u'Beach, Billings County, N.D.',
       u'publisher': u'W.A. Young',
       u'section_label': u'',
       u'sequence': 6,
       u'start_year': 1905,
       u'state': [u'North Dakota', u'North Dakota'],
       u'subject': [u'Beach (N.D.)--Newspapers.',
        u'North Dakota--Beach.--fast--(OCoLC)fst01254162'],
       u'title': u'Golden Valley chronicle.',
       u'title_normal': u'golden valley chronicle.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn89074109/1914-01-09/ed-1/seq-6.json'},
      {u'alt_title': [u'Boise Sunday capital news', u'Sunday capital news'],
       u'batch': u'batch_idhi_kingsolver_ver01',
       u'city': [u'Boise'],
       u'country': u'Idaho',
       u'county': [u'Ada'],
       u'date': u'19130101',
       u'edition': None,
       u'edition_label': u'FIRST ANNUAL REVIEW',
       u'end_year': 1927,
       u'frequency': u'Daily',
       u'id': u'/lccn/sn88056024/1913-01-01/ed-1/seq-47/',
       u'language': [u'English'],
       u'lccn': u'sn88056024',
       u'note': [u'Archived issues are available in digital format from the Library of Congress Chronicling America online collection.',
        u'On Sundays published as: Sunday capital news, Feb. 4, 1911-Oct. 10, 1926; Boise Sunday capital news, Oct. 17, 1926-Jan. 19, 1927.',
        u'Weekly eds: Idaho capital news, 1901-1914?; Western farmers review, 1915?-1917? Cf. Ayers, 1915.'],
       u'ocr_eng': u'FACTS ABOUT IDAHO\nSTATE INSTITUTIONS\n(By C. A. Haotings).\nTher% uro olevrn state institutions\nin Idaho which the state maintains.\nComparatively few of the people are\naware of their location and the ex\npense of their maintenance and In pur\nsuance of my view of giving the great\nest publicity appertaining to the peo\nple\'s interest, I herewith present the\nfollowing:\nAcademy of Idaho\u2014Situated at\nPocatello, was established in 1901.\nThe state has issued bonds ior con\nstruction of buildings t-> the amount\nof $1\'.\'7,000 and had an appropriation\nfor maintenance for 1011 and 1912\nof $06,684.60.\nAlbion Normal School - established\n1893 at Albion. Bonds issued to the\namount of $158,600. Had an appropria\ntion for maintenance for 1912 of\n$7\xbb, 500.\nLewiston Normal At Lewiston, es\ntablished 1400. L mis issued for build\nings, $175,750. Had an appropriation\nfor maintenance for 1011 and 1012 of\n$ left,non.\nCniversity of Idaho\u2014Established\n1 SS 9 ;,i Jins,-I w. Bonds issued to date\n$111,raid. .Maintenance for the years\nHill and 1012. $107,000 besides an ap\npropriation of $2 1,000 for agricultural\ncollege werk under supervision of the\nuniversitj but spent in various parts.\nungeneelte * id g a\n\xab llllt\nCAN ADA\n__ \u2022\niS\xe2m\xe0\xeamA\nload\nSTA3B\nLIN DDR\nrf&L\nABLE\nWOOL ETON\nCENTRAL park.\nA\nCoqA\nC \'one/\n& oror sr\nSAXTON\nCANYON\nxvN\nerca Perk\nWa COLUSTER\nL>ro\n4.0\nCPL DWELL\nan c\np7*p<irmorr\nBOSS\nUsDck\nt)c\'S e\nRb .\nFor*\nNEPUMP/y\n* POST\nY\ncf\nT\nI\nDAHO\' INACTION\nCO\n:o?d\nu a a\nOtrTN\nnananaoi\na a n a a a o I 1\nlaaaoDi\ninnnaat\ninoanot\nCO/SC\n7\xb0\nBOISE VALLEY LOOP,\nCITY \xab INTEPUIPBAM LIMES.\nfr.fi yjg fcfig\n\xab\n\'Ll\nID\n<F\na\n*0\nIT*.* Ciftr***.\' dWArt.vt\n/Z C\nCLfrfLBHO, O. - OO/SE. 0**<0,\nTHE INTERURBAN CARS are now running around the loop. Cars over the southern division,\nvia Meridian and Nampa to Caldwell, will leave Boise every hour from 6:30 A. M. to 6:30 P. M.,\nthen 8:00. 9:30 and 1 1:30 P. M., the 1 1:30 o\'clock car going to Nampa only.\nOver the northern division, via Eagle, Star and Middleton to Caldwell, the Cars will leave Boise every\nhour from 7:00 A. M. to 9:00 P. M., then 1 1 ;00 P. M. The 8;00 P. M. car goes as far as Star only.\nTake a ride around the loop and call upon your friends. Round trip tickets between Boise and Cald\nwell are good going over one division and returning over the other.\nof the state. In justice to the univer\nsity he it said that in 1907 the ad\nministration building was destroyed\nby fire which necessitated the erec\ntion of a new building. The insurance\nreceived on account of loss by fire was\n$110,032.23, the main portion of which\nwas used In the construction of now\nbuildings.\nSoldiers\' Home- Located at Boise,\nestablished 1S95. Bonds issued amount\ning to $72,500. Appropriations for 1011\nand 1012. $31,200.\nDeaf, Dumb and Blind Asylum\u2014T.o\ncated at (loading. Established 1001.\nBonds issued, $33,000. Appropriations\nfor maintenance 1011 and 1012, $44,000.\nBlackfoot Insane Asylum\u2014Located\nat Blackfoot. Established 1847. Bonds\nIssued to date $114,150. Appropria\ntions for maintenance, 1911 and 1012,\n$45\n-Local\nNorth Idaho Insane. Asylum\ned at I trofino Established 1005. Bonds\nissued $1 13,000. Appropriations for\nmaintenance 1011 and 1012 $67,000.\nState Penitentiary\u2014Located at Boise.\nBuildings and grounds given to state\nby national government in 1801. Bonds\nissued $121,000. Eor maintenance for\n1011 and 1012. $ 00 . 000 .\nIndustrial Training School\u2014Located\nat St. Anthony. Established 1903.\nRands issued to date $iss,000. Appro\npriation for maintenance 1911 and\n1*912, $76,000.\nIdaho State Sanitarium\u2014Located at\nNampa. Established 1911. Bonds is\nsued, $25,000.\nChildren\'s Aid and Home Finding\nSociety\u2014Located at Boise anti Lewis\nton. While not a state institution,\nBoise had an appropriation for build\nings for 1000 of $20,000, and in 1011\nan additional $7000 and in 1011 $7000\nwas appropriated for the Lewiston\nbuilding.\nIn addition to these amounts appro\npriated there are various revenues\nwhich these institutions have benefit\nof. The national government pays\nfor each soldier toward the mainten\nance of the soldiers\' home f 100 per\nyear, producing an annual revenue of\nabout $10.000. The various educa\ntional, penal and charitable institu\ntions and the public schools receive\nfrom rentals of land and Interns\nrived from loans made on account of\nsale of lands for 1011 of about\n$150,000.\nThe University of Idaho receives ahi\nfrom the national government from\nfunds known as the Morrill, Hatch\nand Plains funds not le^s than $75,\nnoo per year and these funds increase\na little each year. In addition to\nthese Institutions the state has spent\nsince tatehood $301,000 on roads\nand $158,000 on bridges.\nThe slate\'s general expenses since\nstatehood were as follows: that is,\nthe below mentioned amounts are the,\namounts w e raised by general taxa\ntion :\n1801 $239,515\n1-02 ........................220,274\',\n1893 ..\n1895 ..\n1898 . .\n1897 ..\n1899 ..\n1900 ..\n1901 ..\n1902 . .\n1903 ..\n1904 ..\n1905 ..\n1906 ..\n. . . 245,000\n... 245,000\n... 245,000\n... 245,000\n. . . 275.000\n. . . 275,000\n... 350,000\nLYNCHING RECORD\nFOR THE YEAR of 1912\nOnly 52 lynchings, known to bo\nsuch beyond doubt, occurred in the\nUnited States in the year now closing.\nIn previous years the number has been\nmuch larger, 100 having been recorded\nin 1804 and 170 in the year following.\nThe past ten years the number has\nbeen steadily decreasing. Only one\nyear, however, produced fewer lych\nings than 1912. In 1907 the record\nshows 51 lynchings, one less than this\nyear. *\nIn some of the southern states there\nuere fewer lynchings in 1012 than in\nalmost any previous year. The de\n\u2022 reuse is attributed largely to the ef\nforts made by governors and prominent\ncitizens to prevent mob violence. Many\nof the governors have acted promptly\nr\nI\ni\n!\nI\n!\nj\nj\nI\n10117 .......................... 500,000 ]\n1909 600,000\n1910 600,000\n1911 900,000\n1012 .......................... 000,000\nIn addition to those amounts we\nhad deficiencies in excess of $700,000. J\nThe state has invested in buildings\nand land connected with the various\ninstitutions exclusive \xbb>f the capitol j\nbuilding approximately $1,500,000. \\Vc j\nraise by direct taxation for the main- !\ntenanco of these institutions $115,000\na nnually.\nand energetically to prevent lynchings,\nwith the declared policy of giving the\nlaw free opportunity for the punish\nment of all alleged offenders, white or\nblack.\nAgain, as in the record of former\nyears, more lynchings occurred in\nGeorgia than in any other state. Louis\niana and Florida come next in the list.\nSouth Uarollna. whose governor lias\ngone on record as unopposed to lynch\nings In certain cases, had six cases of\nsummary execution this year as against\nonly one in the year 1911. Kentucky,\nwith ciglU lynchings in 1911, had a\nclean slate this year, Oklahoma is an.\nother Mate with a clean record for the\npast 12 months. The ligures for Texas,\nTennessee, Florida and Arkansas show\nj\n;\nI\nfewer lynchings than In 1911. In\nMississippi, Alabama and Louisiana\ntlie number slightly exceeded that of\nlast year.\nThe states outside of the south where\nlynchings occurred In 1912 were West\nVirginia, Wyoming, Montana, North\nDakota and California. By states the\nrecord is as follows: Georgia, 14;\nLouisiana. 8; South Carolina, 6; Flor\nida, 5; Alabama, 4; Arkansas, 3; j\nTexas, 3; Mississippi, 3; Tennessee. 1;\nWyoming, 1; West Virginia, 1; Mon\ntana, 1; California, 1, and North Da- 1\nkota, 1.\nIn the 52 lynchings the victims of all\'\nbut two were negroes. Three negresses\nwere included in the list. Alleged\ncrimes against women or girls and the\nmurder of white persons account for\npractically all of the cases.\nA singular feature is found in tie\nlynching of the two white me n, both of\nwhich occurred in the far north. In\neach case the victim was accused of\nmurder. In the town of Joppa, a short\ndistance from Rosebud. Mont., Harry\nHeffner, alleged slayer of Mrs William\nMerrill, was taken from the county jail\nby a crowd of masked m<*n and hange. |,\ndespite the efforts of the sheriff and\nhis deputy. The .-\u2022com] lynching oc\ncurred at Steele, N, D. win-re George\nBaker, charged with tin* murder of his\nwife and father-in-law, was taken from\nthe jail and hanged by a mob. The\nlynching was the First recorded in\nNorth Dakota in more than a d* cade.\nThe lynching in Wyoming was one\nof the most sensational of the year,\nFrank Wigfa.ll, a negro who confessed\nto having made a criminal attack on\nMrs. Fsther Higgins, an aged woman\nknown as tin* "prisoners\' friend," was\nI\nlynched by the convicts of the state\npenitentiary at Rawlins.\nIn the West Virginia lynching tha\nvictim was Walter Johnston, a negro\naccused of assaulting a young whiti\ngirl in Mercer county.\nCalifornia\'s one lynching was out of\nthe ordinary. It occurred on the des\nert, some 30 miles from the town of\nMojave. The victim was an unknown\nnegro accused of having attacked a\nsix-year-old child. He was hanged by\nmen alleged to be employed on the Los\nAngeles aqueduct.\nThe three negresses who fell victims\nto mob violence were Ann Boston, who\nmurdered the wife of a planter at\nPinch urst, Ga.; Mary Jackson, who was\nlynched in Panola county, Texas, for\nalleged complicity in the murder of a\nwhite man, and an unknown negress\nwho, together with three negroes, was\nhanged b\\ a mob in Harris county,\nGeorgia, for the murder of a farmer.\nTybr, Texas, furnished the only in\nstance of the year where the victim was\nburned at the stake. On May 25 Dan\nDavis, a negro who had confessed to a\ncriminal attack on a young white\nwoman, was burned at the stake In one\nof tin main streets of Tyler in the\npjf .. nc. of a crowd of 2000 persons.\nThe foregoing record does not com\npris. eases of plain murder or case**\nwhere the victim was killed by a posse\nwhile resisting capture.\nHi s Specialty.\n"That runawu.N bull was a natural\ngambler."\n"Gambler?"\n"Yts. didn\'t you see how neatly he\ntossed a copper - "\' -Chicago Tribune.',
       u'page': u'7',
       u'place': [u'Idaho--Ada--Boise'],
       u'place_of_publication': u'Boise, Idaho',
       u'publisher': u'Capital News Pub. Co.',
       u'section_label': u'',
       u'sequence': 47,
       u'start_year': 1901,
       u'state': [u'Idaho'],
       u'subject': [u'Boise (Idaho)--Newspapers.',
        u'Idaho--Boise.--fast--(OCoLC)fst01205276'],
       u'title': u'Evening capital news.',
       u'title_normal': u'evening capital news.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn88056024/1913-01-01/ed-1/seq-47.json'},
      {u'alt_title': [],
       u'batch': u'batch_wa_elm_ver01',
       u'city': [u'Seattle'],
       u'country': u'Washington',
       u'county': [u'King'],
       u'date': u'19201009',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1921,
       u'frequency': u'Weekly',
       u'id': u'/lccn/sn87093353/1920-10-09/ed-1/seq-2/',
       u'language': [u'English'],
       u'lccn': u'sn87093353',
       u'note': [u'"A publication of general information, but in the main voicing the sentiments of Colored Citizens."',
        u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.',
        u'Description based on: Vol. 2, no. 5 (July 14, 1917).',
        u'Volume numbering irregular.'],
       u'ocr_eng': u'or more white citizens hereabouts. In the\nSouth the leading daily papers of that sec\ntion are greatly disturbed lest the twelve\nmillion colored citizens of the United States\nenslave the ninety million white citizens\nand thus is life for the white man of this\ncountry just one damn thing after another.\nAin\'t it sickening 1\nThis thing of patting the entire colored\npopulation down as cocksure Republicans\nas the Republican leaders are doing in this\nstate and county and making no effort to\norganize them, may cost the party the loss\nof a good many votes. No, they will hard\nly vote the Democratic ticket, but many of\nthem have strong leanings toward the\nFarmer-Labor ticket and in the absence of\npersonal workers among them in the in\nterest of the (!. O. I\\ there may be grave\ndanger of many of them wandering after\nstrange political gods.\nFrom the current issue of the Portland\nAdvocate under the caption of Frame Up on\nColored Man. I clipped the following article\nwhich fully speaks for itself and needs no\ncomment from me further than, the same\nthings happen in other places, for example,\nOmaha and Duluth in the North and an\nhundred and one places in the South:\nThe reading public has not so soon for\ngotteH the big scare headlines wliich appeared\nin the local daily papers a week or so ago\ndescribing an alleged assault by a "burly\nnegro" on a "pretty white woman" in one\nof The Dalles, Or., hotels. One paper ran\nthe cut of the alleged "beautiful" victim\nand another paper said editorially: "It\'s\ngood for the negro who attacked the white\nwoman of The Dalles that he was not in\nthe South, or he would have been lynched\nor burned at the stake." The object of\nthese papei\'s was to start a race riot or to\nhave a "necktie" party; but the law-abiding\npeople of The Dalles were not swayed by\nsuch inflammatory utterances of the news\npapers and as a result the colored man was\narrested, placed in jail and a grand jury was\ncalled in to investigate, and their finding\nwas that the whole business was a "frame\nup" on the colored man in order to have him\nmobbed or sent to the penitentiary. The\ncolored man has been set free. Now, what\nabout this "pretty" white woman who help\ned to frame up on this burly colored man\nand her coterie of conspirators? Why didn\'t\nthe daily papers give as much publicity to\nthe vindication of this colored man as they\ndid in trying to have him murdered by\nfastening the crime of rape upon him?\nAnd what about this "pretty" white woman,\nwho is not only a liar but a perjurer and a\nwould-be murderer? Thousands of innocent\ncolored men are lying in their graves today\nthrough jnst such dirty frameups as the one\nin this case.\nTRYING TO SAVE THE SOUTH\nThat efforts are not altogether lacking\non part of both races in the South, to save\nthat section from being: completely sub\nmerged beneath the waves of mob violence\nftnd lynch law is made evident from time\nto time. Despite the fact that but little\nsubstantial result has so far been mani\nfested in response to their appeals, these\ndevoted advocates of justice and the up\nholding of the law continue their efforts to\nsave the civilization of the section with\nunabated zeal.\nConspicuous among those members of the\nrace who have an abiding faith in the de\nsire and ability of the better white element\nof the South to prevail in the struggle\nngainst lynching and mob rule is Mr.\nCharles Banks of Mound Bayou, Miss. Time\nand again he has urged the giving of\npublicity to the position being taken by\nthe white Southerners in addresses and edi\ntorials against lynching, and the inclina\ntion among men of influence and affairs to\ncome out openly and strongly for justice,\n\'<\\ square deal and larger opportunity for\nthe Negro in the South.\nRecognizing the expediency of the policy\nurged by Mr. Banks and the great good\nthat would follow all genuine effort on the\npart of the white South to remedy its in\nternal evils in its own way and through\nits own instruments, The Age has hailed\nwith encouragement every outgiving of\nSouthern press and pulpit pointing in this\ndirection. We take occasion now to wel\ncome and reproduce a significant denuncia\ntion of mob rule by Bishop Theodore Du\nbose Bratton of the Episcopal diocese of\nMississippi, which the Jackson Daily News,\none of the leading dailies of the State en\ndorsed editorially as "an indication of the\nattitude of the sober thinking high-minded\npeople of Mississippi."\nBishop Bratton was quoted as follows:\n"Lynching is an indefensible act of law\nless brutality. It is rebellion against or\nganized society, and its inevitable tendency\nis toward disorder and murder and barbaric\ncruelty. It is, whether realized or not, a\ndisregard of the sacredness of life. As\nresorted to in our midst, it is probably\na practical expression of unbelief in the\nhumanity of the Negro race, and, in the\nChristian (and scientific as well) declara\ntion that "God hath made of one (blood)\nall the nations of men."\n"At one time resorted to for the crime\nof rape, it has now come to be used in the\ncase of many lesser crimes. An example\nin point is the lynching briefly and casually\nnoted in the papers on Sunday, August 29,\nof two Negro prisoners in Corinth for\nknocking theri guard down (not killing\nhim) und attempting their escape\u2014the no\nlice concluding by saying that one of them\nhad no part in the attack upon the guard.\nYet this brutal, barbarous, unlawful lynch\ning was resorted to to avenge a deed which\nevery officer of the law knows may happen\nat the hands of prisoners, and for which\nhe is ever prepared. I have no patience\nwith any newspaper which can publish such\nan atrocity without one syllable of con\ndemnation of it. Even in the extreme case\nof rape, lynching has no defense, and I\ncan never believe that the women of our\nSouth can want such a protection at the\ncost of law and order and the safety of\nsociety and government. It is a protection\nwhich does not protect, but rather imperils\nthe only source of protection which good\npeople, men and women alike, have.\n"It is high time that law-abiding and\nliverty-loving people proclaim their con\ndemnation of this base form of barbarism\nand banish from our borders this disgrace\nupon our Christian civilization. The lust\nof blood is characteristic of the jungle, the\nlust of revenge of the savage."\nNo stronger indictment of the crime of\nmob murder could be framed than this\ndeliverance of a Christian preacher from\nthe pulpit on a Sunday morning.\nNor is there any reservation in the edi\ntorial endorsement by the News. It con\ncedes the justice of the indictment and\npoints out the following facts:\n"The Negro race is a race of human be\nings, entitled to the same humanitarian\nconsiderations as any other race. The Ne\ngro acts our every day life. He is a part\nof us and his degeneration or advancement\nis linked inseparably with the degeneration\nor the advancement of the white man. The\nNegro is brought up under the same en\nvironment in which we live, he is taught\nthe same ideals of living, he reads the same\ntextbooks in our schools, cherishes the same\ntlag, and worships the same flag, and wor\nships Ihe same God.\n"The peaceful Negro has no patience with\nthe lawless element of his race. He is as\nready as the white man to take a deter\nmined stand against those who violate the\nlaws of the state and the laws of decency.\nWhile in considering personally the crime\nfor which many Negroes have been mobbed\nit is difficult, perhaps, for some of us to\nrefuse to countenance mob action, we know\nthat it is inexcusable under any considera\ntion and that swift and sure punishment\nshould be meted out to those who take the\nlaw into their own hands and commit\ncrimes in the name of law and order\nwhich are fitting only to the most savage\nof races."\nIn view of such strong pronouncements\nas the above, and many that have preceded\nthem, it is not surprising that some dis\nappointment should exist among those who\nhave counted upon an immediate popular\nresponse to such sentiments that would re\nveal itself in some active movement to\ncurb the excesses of the mob spirit. Un\ndoubtedly the speakers and writers of ex\npressions against lynching and mob law\nare sincere and earnest in their utterances,\nbut the practical response to their plead\nings is slow to come. Nevertheless, every\neffort on the part of the Southerners of\nboth races to try to save the South to\ncivilization is to be commended.\nThe South should respond to its true\nloaders and purge itself of lynching and\nmob violence.\u2014New York Age.\nTHE WAR IN HAYTI\nIn his speech on Constitution Day Senator\nHarding said in referring to American mili\ntary operations in Santo Domingo and\nHayti:\n\'\'The censorship is no less strict than it\nwas during the secret conferences and con\nspiracies in Paris. Practically all we know\nis that thousands of native Haytians have\nbeen killed by American marines."\nNevertheless it is possible to form an idea\nof the character of this war, which as Mr.\nHarding says, is being waged, though never\ndeclared, through the usurpation by the\nExecutive of powers not only never bestowed\non him but scrupulously withheld by "the\nConstitution.\'\'\nThe Haytians whom the marines are pur\nsuing are known as Cacos. Some are simple\nbandits, some are revolutionists many are\nmen who have been driven to the hills in con\nsequence of a mistake made by the American\nadministrators early in the occupation of the\nisland. The Americans took too seriously an\nold law requiring men to work the roads\nfour days a month, there was a rebellion\nagainst the attempt to enforce it, Haytian\nopposition to the Americans crystallized on\nthis issue and ever since a stream of recruits\nlias been flowing to the bands of outlaws and\nrevolutionists.\n, One of the duties of the American marines\nhas been to hunt down these bands. It\nhas been described as a war of extermina\ntion. Samuel Guy Inman, executive secre\ntary of the Committee on Cooperation in La\n; tin America, who made a survey of the island\nin the summer of 1919 wrote in his report:\n"These outlaws go in bands numbering\nfrom twenty-five to two hundred generally.\nNot more than 20 or 30 per cent are armed,\nand these are very poor shots, so that there\nare few casualties among our men. They\nare now making a systematic drive and clos\ning on the bandits, and in some battles from\ntwenty-five to sixty are killed.\n"It is with great hesitancy that one seem\ningly passes criticism on our American ma\nrines. It is the machine, not the man, that\nis to blame. From the military standpoint\nit is natural to regard all life as cheap."\nAnother observer, Otto Schoenrich, a New\nYork lawyer, the author of a book on Santo\nDomingo and formerly secretary of the\nDominican Minister of Finance, heard \' \'un\nfortunate stories of torture of prisoners by\nwater cure and by application of red hot\nmatchets. Giving prisoners an opportunity\nto escape and shooting them while escaping\nwas also said to have occurred."\nMr. Harding spoke of thousands of native\nHaytians having been killed. James Weldon\nJohnson, field secretary of the National As\nsociation for the Advancement of the Colored\nPeople, who visited Hayti recently says that\n"in the five years of American occupation\nmore than three thousand innocent Haytians\nhave been slaughtered."\nThe immediate provocation of the Ameri\ncan occupation of Hayti was the violation\nby a mob of the French Legation at Port\nau-Prince and the killing of President Guil\nlaume Sam, who had taken refuge there\nafter causing the death of only 160 political\nprisoners. And the purpose of the Ameri\ncan occupation was to improve the govern\nment of Hayti!\u2014New York Herald.',
       u'page': u'',
       u'place': [u'Washington--King--Seattle'],
       u'place_of_publication': u'Seattle, Wash.',
       u'publisher': u'H.R. Cayton',
       u'section_label': u'',
       u'sequence': 2,
       u'start_year': 1916,
       u'state': [u'Washington'],
       u'subject': [u'African Americans--Washington (State)--Seattle--Newspapers.',
        u'African Americans.--fast--(OCoLC)fst00799558',
        u'Seattle (Wash.)--Newspapers.',
        u'Washington (State)--Seattle.--fast--(OCoLC)fst01204940'],
       u'title': u"Cayton's weekly.",
       u'title_normal': u"cayton's weekly.",
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn87093353/1920-10-09/ed-1/seq-2.json'},
      {u'alt_title': [],
       u'batch': u'batch_nbu_carson_ver02',
       u'city': [u'Lincoln'],
       u'country': u'Nebraska',
       u'county': [u'Lancaster'],
       u'date': u'19021219',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1923,
       u'frequency': u'Monthly',
       u'id': u'/lccn/46032385/1902-12-19/ed-1/seq-2/',
       u'language': [u'English'],
       u'lccn': u'46032385',
       u'note': [u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.',
        u'Available on microfilm from New York Public Library.',
        u'Editor and proprietor: William Jennings Bryan.',
        u'Issues for Feb. 21, 1902-1923 called also whole no. 57-768.',
        u'Published in periodical format, Aug. 13, 1913-1923.',
        u'Publisher varies: Charles W. Bryan.'],
       u'ocr_eng': u'II\n1\nI\nyx\n, (\nII l\nA.\nognitio\'n of the rights, of others; botweona meas\nure of greatness1 that estimates a man by what\nhe has absorbed from society and that which\nestimates men worthy\' in proportion as they do\nservice and diffuse .blessings these differences\nsurpass comprehension.\nIf Jesus had left nothing but the Parables,\nHis name would have been imperishable in litera\nture; if He had bequeathed to posterity nothing\nbut the simplicity of his speech and the irresisti\nble logic of His argument, Ho would have had a\npermanent place among the orators of the world;\nif Ho had given to the world nothing but the\ncommandment "Thou shalt loveHhy neighbor as\nthyself," enforced as it was by His own example,\nthis one gift would have been sufficient to out\nweigh all the wealth of all the world; if He had\nleft no record but the Sermon on the Mount,\nit alone would have made His natal day worthy\nof perpetual celebrationbut all these added to\nthe matchless majesty of a perfect life and the in\nspiring influence of an all-pervading love, are\nturning the eyes of an ever-increasing number to\nthe path that Le\'trod from the manger to the\ncross.\nLove was the dominating force of His life\nand love is today the overmastering impulse whose\nebb and flow mark the retreat and advance of\ncivilization.\nAnd love, too, sanctifies the Christmas gift\nWith it the merest trifle swells into an object of\nimportance; without it the most expensive present\n""dwindles into insignificance. Love is the alchemy\nwhich invests with priceless value all that it\ntouches the magic wand that converts the hum\nblest cottage into a palace and gives to earth\'s\npilgrims a glimpse of paradise.\nThe \'Commoner wishes its readers, one and all,\na Christmas made merry by kindly deeds and\ncommunion with ffiendsand\'a NW Year made\n\'happy by the following of a high \'purpose with\n"firm .resolve.\nJJJ\nStopping Gold Coinage.\nDirector of the Mint Roberts in his annual\nreport recommends that we stop the coinage of\ngold. He says that the gold coin of the country is\nnow entering almost entirely into storage and he\ninsists that the coinage of the entire annual de\nposit is unnecessary.. He argues that the $500,\n000,000 of coined gold in the treasury is more than\nwill be called for in a generation and he thinks\nthat it would be better to issue certificates against\ngold bars.\nThis public recommendation, taken in connec\ntion with an objection which the director urged\nprivately against the encouraging of gold produc\ntion by the establishment of new assay offices,\nwould indicate that the financiers and their sym\npathizers are fearful that gold will become too\nplentiful. While many of the advocates of the\ngold, standard suppose that the gold dollar has a\nfixed and unchangable value the great financial\nmagnates know that the purchasing power of a\ndollar depends upon the number of dollars and\nbeing interested in dear money rather than in\nhigher prices, they will oppose the free coinage\n\' of gold as strenuous as they did the free coin\nage of silver whenever they think that the world\'s\nproduction of the yellow metaL is likely to bring\naid to the debtor class. They can look complacent\nly upon an indefinite increase in the purchasing\npower of the dollar, but they become alarmed at\nW mUS ,ra deCreaSe SIX years a a bals\'\nmagazine of London suggested that it might be-\nventtrTry0 HmIt thG COina of gold pre\nvent the dollar from getting cheap and we may ex-\n?rolCHonSUf gC8tl0n t0 b0 acted P thgo,d\nhfinT mUCU larger\' Th0 coin-\n000 durln. drPPed about $38,000,\n. 000 during the past year; $14,452,000 in gold was\nThe Commoner.\nwithdrawn from the treasury for use in the arts,\nand to this must be added tho indefinite\' amount\nsecured by the melting of gold coin. When it is\nremembered that this only represents the \'con\nsumption of one nation it will be seen that a con\nsiderable proportion of the gold production of the\nworld is diverted from coinage to use as merchan\ndise. The total supply of gold coin is also de\npleted by lost \'coins and. by abrasion a loss so\ngreat that the "quantity of gold coin in existence\ntoday in the entire world is considerably less than\nthe gold production since 1848. In other words,\nthe savings of all the ages prior to tho great dis\ncoveries of 1849 have disappeared.\nOver nineteen million silver dollars were\ncoined last year (at the ratio of 16 to 1 without\nwaiting for the aid or consent of any other nation),\nbut the silver bullion in the treasury is now nearly\nexhausted and the coinage of silver dollars will\nsoon cease.\nThe director of the mint suggests that tho\nlimit on subsidiary coinage be removed. He prob\n"ably has in contemplation the passage of the bill\n(already passed by the house of representatives)\nauthorizing the unlimited recoinage of silver dol\nlars into subsidiary coin.\nIf the readers of The Commoner will watch\nthe movements of the financiers they will find evi\ndence of a persistent effort to lessen the volume\nof legal tender money a part of the gold stand\nard plan to make gold the only legal tender and\nbank notes the only credit money.\nJJJ\nPost Check Currency.\n, An important measure now before congress\nis the post check currency bill. This bill pro\nvides for a convenient method of sending small\nsums of money by mail and its passage is beiijg\nurged by newspaper publishers and by merchants,\ngenerally. One of the promoters of this measure\nsays: "A great deal has been written on this sub\nject, and there are few who do not know that this\nmethod is simply an issue of currency that can\nbe circulated as cash and also be safely remitted\nby mail, because when mailed it will be made\npayable only to the person whose name is writ\nten in the blank space that is left for that pur\npose. Under the present money order system safe\nty can be assured only at the sacrifice of conven\nience, and convenience can be consulted only by\ndisregarding the element of safety. The post\ncheck plan is both convenient and safe, and, be\ning so, it is just what the public needs in its line.\nThe business interests and the press favor it with\nvirtual if not actual unanimity, and, in fact, the\nfull measure of popularity it has won is a war\nrant, if not an order, for its adoption by con\ngress." JJJ\nA Rich Man\'s Example.\nThe New York World gives an extended ac\ncount of the action of J. G. Phelps Stokes, a rich\nyoung New Yorker, who has left a luxuriant home\nto dwell and work among the poor of the East\nSide. He has become a resident of University set\ntlement at Eldridge and Rivington streets, and\nspent Thanksgiving Day among his new neigh\nbors. Mr. Stokes is a stalwart young man with an\nabundance of energy, but ho is going to employ\nhis strenuousness in persuasive and self-sacrificing\nmethods rather than in following the boister\nous and bloody paths marked out by the imperial\nists. He has the Bible Idea of conquest that of\n\'overcoming evil with good that of conquering\nwith kindness.\nWhile m it may require, some self-denial and\nmoral courage on the part of Mr. Stokes, who\ndoubts that he, will get more genuine pleasure\n; Vol. a, No. 48.\nout of his labor than the well-dressed dudes and\nself-indulgent sons will obtain from the squander\ning of their fathers\' money?\nJJJ\nMr. Chandler on Lynching.\nEx-Senator Chandler of Now Hampshire, in a\nrecent letter to the New York Sun, remarks: "I\nshould like to learn Mr. Bryan\'s opinion of tho\nbloody and brutal custom of lynching American\nnegroes."\nMr. Bryan\'s opinion has already been ex\npressed on this subject in The Commoner. He is\nopposed to lynching either black people or white\npeople, in the south or in the north, and he is\nstill more opposed to the burning or torture of\ncriminals by mobs in any part of the country.\nHe believes " in law and order, and has always\ncontended that the law should fix the punishment\nand that the officers of the law should administer\nthe punishment But ho is not deceived by the\nmock virtue of partisan republicans who rave\nabout the lynching of a black man in the south,\nbut have nothing to say about the lynching of\nmen, white and black, in the north.\nThere Is a difference between the patriotic\ncondemnation of lawlessness wherever it occurs\nand the attempt to make political capital out of\nsummary punishments when they happen to bo\nenforced by political opponents.\nSenator Chandler, when he is in a talkative\nvein, as he often is, might express his opinion\nabout the crimes which are responsible for most\nof the lynching, and he might explain why tho\n- republican politicians spend so much more time\nfinding fault with the punishment than they do in\ntrying to eradicate the hideous offenses which\nhave led the white people of the south to executo\nsummary vengeance. When he gets through with\nthese explanations he- might add . a. .postscript\nshowing why the lynching and burning of black\nmen in Colorado, Kansas and other northern states\ndisturb republican editors and republican orators\nless than the burning of black men in the south.\nJJJ\nCare For Parents.\nA dispatch from Kokomo, Ind., tells how fifty\nmasked men took an aged, neglected and enfeebled\nold man in a carriage and, driving to the home of\none of the man\'s sons, notified him that he would\nhave to take care of his father or they, the reg\nulators, would take care of the son. It seems that\nthe old man was once well-to-do and gave each of\nhis sons a farm, but now in his old age they have\nleft him destitute to care for himself.\nAn occasional occurrence of this kind re\nminds the world that ingratitude and depravity\nare not yet obsolete, and it also teaches an im\nportant lesson, namely, that a parent ought not to\ndivide his property among his children until he\n\'dies. While in a large proportion of cases such an\nanti-mortem distribution does not result in unfllial\nconduct, it is just as well for the parent to retain\nthe title to enough to keep him from want. The\ncommand, "Honor thy father and thy mother,"\nis a continuing one and Is Intended for adult as\nwell as younger children.\nThe Indiana incident recalls a Btory told to\nenforce this point: A father, noticing his little\nboy working at a trough, asked him what he\nwas making it for? His innocent reply, "To feed\nyou out of when you get old like grandpa," was a\nrebuke that the parent did not forget\nIf there were no higher motive, a father\nshould remember that he Is making provision\nfor his own old age when ho sets before his chil\ndren an example of filial tenderness and devotion.\nJJJ\nn\nTn a battle," said President Roosevelt, "tho\nonly shots that count are the shots that hit." Cor\nrect, and tho trusts never winced a bit\nmsb9BB9\n-, UBMtf\'L\'\nP " T - &m. i',
       u'page': u'2',
       u'place': [u'Nebraska--Lancaster--Lincoln'],
       u'place_of_publication': u'Lincoln, Neb.',
       u'publisher': u'William J. Bryan',
       u'section_label': u'',
       u'sequence': 2,
       u'start_year': 1901,
       u'state': [u'Nebraska'],
       u'subject': [u'Lancaster County (Neb.)--Newspapers.',
        u'Lincoln (Neb.)--Newspapers.',
        u'Middle class--United States.',
        u'Middle class.--fast--(OCoLC)fst01020437',
        u'Nebraska--Lancaster County.--fast--(OCoLC)fst01211912',
        u'Nebraska--Lincoln.--fast--(OCoLC)fst01208321',
        u'Politics and government--fast--(OCoLC)fst01919741',
        u'United States--Politics and government--1865-1933--Newspapers.',
        u'United States.--fast--(OCoLC)fst01204155'],
       u'title': u'The commoner.',
       u'title_normal': u'commoner.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/46032385/1902-12-19/ed-1/seq-2.json'},
      {u'alt_title': [],
       u'batch': u'batch_txdn_audi_ver01',
       u'city': [u'Dallas', u'Houston'],
       u'country': u'Texas',
       u'county': [u'Dallas', u'Harris'],
       u'date': u'19220107',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 9999,
       u'frequency': u'Weekly',
       u'id': u'/lccn/sn83025779/1922-01-07/ed-1/seq-4/',
       u'language': [u'English'],
       u'lccn': u'sn83025779',
       u'note': [u'Also issued on microfilm from the Library of Congress, Photoduplication Service; Micro Photo Div., Bell & Howell Co.',
        u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.',
        u'Description based on: Vol. 7, no. 14 (Jan. 13, 1900).',
        u'Latest issue consulted: 78 years, no. 40 (Oct. 3, 1970).',
        u'Published at Houston, Tex., Nov. 14, 1970-'],
       u'ocr_eng': u'THK MIXA.S EXl\'Itl-SS. JlALUS, TEXAS. SATURDAY, JANUARY 7, 1022.\nrr.", rct n\nif\' 1\nTUB DALLAS EXritKSS\nNATIONAL NEGRO PRESS\nASSOCIATION.\nPublished every Saturday morning\nin the- vcnr at 2t;oi) Swiss Avenue by\nTHK DAI, LA RAI\'UKSS I\'l\nCOMPANY.\n(Incorporated)\nDallas, Texan.\nFoniJia advertising hepiik-\nK.WATIVEi\nW. II. \'.Iff Company, 008 Baulk Dear\nban Mrtrl, ChlfBRO, 111.\nW. II. .Iff Company, 404 Mlrtoi\nHulhllnK lll F.ntil Alum Mreet,\nIVrwr lark, W. 1.\nTil C D A L L A SE X I K l5sS\nsin.stKirxios ix advance.\nOne Year.....\'. 12.25\nSix Months............ 1.25\nThr" Months .75\nSingle Copy - 05\n1SOT1CH TO THE I\'UIII.IC.\nAny erroneous reflection upon the\nrhsrscter, etnmlinR; or reputation of\nany person, firm or corporation which\nmsv appear In the columns of The\nI n It n h Kxpress will h Kindly cor\nrected upon Ita being brought to the\nattention of the publishers.\nEntered at Pout Office at Dallaa,\nTexas, n second-clae"! matter, under\nAct of Congress, March. 1879\nIMI\'OHTANT.\nNo subscriptions mailed for a pe\nriod less thin three month. Payment\nfor aame must be 75 cents.\nTHE DALLAS EXPRESS\nhas never hoisted the white\nfeather, neither has it been dis\ngraced by the yellow streak. It\nis not afflicted with the flannel\nmouth. It is a plain, every day,\nsensible, conservative newspa\nper, which trims no sail to catch\nthe passing breezes flies no\ndoubtful flat! It professes m\npatriotism at broad as our\ncountry, lit love of even hand\'\ned justice covers alt the terri\ntory occupied by the human\nrace. This is pretty high round,\nbut we live on it and art pros\npering. Boys of the press come\nup and stand with us. This\nground Is holy.\nW. E. K1SC.\nI\'AY YOl ll 11)1,1, TAX XQW.\nKvory real citizen who desires\nIhiU his place of dwelling may have\nI hat which Is best, finds pleasure\nIn so situating himself \'lat he may\nexpress his will in regaru to what-\n\'i\'cr may ho proposed.\nThe ballot Is the means by which\nthe preferences of the majority of\nthe members of any community are\nexpressed. The way to the. suc\ncessful UBe of the ballot la by the\npayment of Poll Tax.\nThe time for payment of this\nlax is now at hand and, while the\nthoughtful citizen does not need to\nbo reminded of his duty in this re\ngard, there are many others, less\nthoughtful who through negligence\nallow the time\' for payment to lapse\nand find themselves voteless as a\n.result.\nPay your Toll Tax now and\nguarantee to yourself the ability\nlo express your desires concerning\nall matters submitted for popular\ndecision.\nIt would be well If, as far as\nour group is concerned, a special\ncampaign could be waged which\ncould have as Its object the educa\ntion of our raen and women of vot\ning age as to the power and intent\nof suirrage. There are too few of\nu who the nejd of qualifying\nfor voting. Toe many of us allow\n"the other fellow"\' to do It. The\nresult Is a lack of political co\nherence and dlroctloii which some\ntimes makes itself felt in an un\nfavorable way. -\nLet each of us of voting age,\nmale and female, pay our Poll Tax\nnow and urge others to do the\nmime,\nsHowixti thh hk;ht spirit.\nThe results of the \'mas charity\ndrive is to be commenced and the\nplellon for the benefit of the poor\nof L\' lias were more ratifying than\nin ay former year.\nA casual investigation of the Hot\nof donors discloses the fact that or\nganizations rather than individuals\ncent ro uted largely to Its success.\nThis Is as it should be. Real\nChristian spirit can express itself\nin no better way. it is a fa t that\nself sufficient peoples mak: it a\nprime consideration that they them\nselves tako care of the poor and in\nqignent an."!? them by systematic\nnnd well directed giving. .\nOrganizations need to develop\ntheir charitable side to a greater ex\ntent nnd \'.o show ihmselves more\nkindly disposed toward the system\natic relief of the unfortunate.\nThe result of this drlv? may well\n-erve as an Indication of what may\nbe done in time to come when the\nwhole city will hare become more\naware of its duty toward Its un\nTortunate at times other than Chrtst-\n1M!IH. ,\n: The spirit end snerrry of those\nwho planned and carrio" on the\nd\'lve iB to bt commended anc the\nIncreased response of organizations\nus well as Individuals appeals to us\nhh indicative of the fact that we" do\n!eet kindly dispose to our ov.n un\nfortunates and are genuinely de\nsirous of alleviating their distress.\nLYNCHING AND, THE DYER BILL\nThe American lynching record for 1921 has just been an\nnounced and from a study of it we find that mob murders have\nincreased rather than decreased; that law and authority are\nweaker this year than in former years; that bestial impulses\nhave held greater sway over the American people in the year\njust closed than in 1020.\nThe statement compares the records of 1920 and 1921 thus:\nofficers of the law prevented 56 lynching in 1920; they prevent\ned 72 in 1921. There were 63 actual lynchings in 1921 as com\npared to 61 in 1920. 01 the number of lynchings occuring in\n1021, 02 occurred in the South and 1 in the North. 59 of the vic\ntims were Negroes and 4 were white; of the Negro victims 2\nwere women. 19 or less than 1-3 of the victims were charged\nwith rape. \'\nUnless the American people have retrogressed much they will\ntake small pleasure in the contemplation of this record of barba\nrism which in its fritrhtfulnesa surnasses the barbarities which\nthe early settlers of this country suffered at the hands of Indian\nllioes WHICH UUHKlcu iiu tiviucauuu oiivx uojutcu w iv\nership. It is a record which, when carefully considered together\nwith the thousands of other instances of mob activity which have\noccurred during the past year, should cause true lovers of order-\n. . t n it At 1 -- 1 J 4-It a\nly proceedure to snuaaer ior tne weirare oi America during me\ncoming years.\nAn increase of savairery in the "land of the free and the\nhome of the brave" which since 1889 has done 3,433 persons to\ndeath without due process of law, means an increased speed in\nthe easv descent to a reifm of unbridled nassion and license from\nwhich stntA the ascent to a state of decent livine will be far\nmore laborious. ,\nAnd the wonder of it is that in those communities where\nthis savagery is most pronounced, there is the greatest opposi\ntion to an attempt at its control.\nAlmost coincident with the publication of the lynching record\ncomes the announcement that the Dyer Billjnow pending a vote\nin Congress, is being fought by every known means by represen\ntatives of those states in which lynchings have been most fre\nquent. Representatives of South Carolina, Tennessee, North Caro\nlina. Louisiana and Texas have distinguished themselves by the\nquality of their opposition to this bill aimed at the suppression of\nlynching which\' is slowly but surely rendering the work of the\nfounders of our American civilization void and as of no moment.\nAnd the states which they represent have contributed greatly to\nthe record of savaerery as it has been written in\'. America since\n1889. Since 1889 Texas has lynched 333 persons. Louisiana 326.\nTennessee 199. South Carolina 128. North Carolina 63. Of this\nnumber of persons eleven were women. In the list of states which\nhave been remarkable for their lynching propensities Texas\nstands second, with its 333, being exceeded in this savagery only\nby Georgia which leads the country with a total of 428 mob mur\nders since 1889. .\nIt will indeed be deplorable if the better mind of America\nfails now to assert itself and safeguard American interests from\nthe pillaging hands of her unrestrained citizens who are besmirch\ning her good name and mocking her boasted democracy.\nIf lynching does not stop the orderly processes of law must\nstop.. The two cannot exist. cojointly.\nThe opposition to the Dyer bill seems to be bassed upon the\nassumption that it would allow the federal goverment to en\ncroach upon, the rights of states. and render their sovereignty\nempty. For this reason it has been called dangerous and it has\nbeen prophesied that its passage will be followed by an increase\nin lynching which federal power will find itself powerless to stop\nIt may be successfully argued that such reasoning has been\nlargely responsible for the increase of lynching to the point that\nefforts at. its control are now being made by the federal govern\nment. And it is also true that since 1889 every state in which a\nlynching has occurred has had ample opportunity to exert its\nsovereignty and pass measures which would have rendered fed\neral intervention at this time unnecessary. But none of them\nhave chosen to do this. And, while it is easy to. understand\ntheir fear of a loss of sovereignty to the Federal Government\nwhen we review the history of the last one hundred years\none is prone to feel that now. if ever, they should begin to\nrealize that just as lynching is not purely sectional, neither would\nfederal intervention for its suppression be. And whatever other\nconsiderations might arise, they should realize that without some\nsort of intervention speedily assured, neither their sovereignty\nnor that of the Federal government will be able to maintain itself\nnor guarantee its perpetuation.\nThere must come a time in American life when sectional bit\nternesses and political bickerings will be lost sight of in the de\nsire that the united efforts of all may be centered upon the high\nest good of national accomplishment. A nation of lynchers is not I\nworthy of emulation nor can it hope long to maintain itself. A )\nlynching in any state is an American lynching. Any such bar-!\nbaritv has no place in an enlightened government.\nEvery American state may well realize that such a record for\nbarbarity should be rewritten in terms of orderly proceedure even\nat the expense of federal cotrol. i\nIf! r, ., 1 i A\nf TH& MIRROR\njjgf BUCOPINION\nA CKXSl\'S INJUSTICE.\nFew cities in the country can boast of higher standards of education\nand refinement among the native population than can the fine old Buz\nzards Bay metropolis. New Bedford, In Massachusetts. Yet the remorseless\nfacts assembled and digested by the Census Bureau reveal New Bedford\nwith its 121,217 inhabitants as the most illiterate city in the country.\nNew Bedford men and women resent keenly the fact that their home\nis held up to view in this unenviable plight. This ia- natural. They are\nproud of their beautiful city, proud of its long tradition of intellectual de\nvelopment, which goes with honorable ambitions conserved in families and\nsanely applied. .Now to be branded as statistically the least literate city\nIn the United States is indeed humiliating. That the numerical facta Justi\nfy the stigma is not to be dented. On this ground the census analysis\nmay not lje successfully challenged. In proportion to its population New\nBedford is indisputably the unfortunate possessor of more illiterates than\nany other community in the entire country.\nBut accurate as is the census exhibit in this respect it none the less\ndoes New Bedford a grave injustice. It carries an Implication of laxity\nand indifference to school facilities which is wholly at variance with the\ntruth. New Bedford has exerted Itself as vlgordusly in the promotion of\npublic education as any city in the country. The\' high illiteracy rate is\nthe city\'s misfortune, not Its fault. The census statement should carry\nan explanation to avert the false impression it now indubitably conveys.\nThat explanation New Bedford has, and its newspapers and citizens have\nlost no time In presenting It.\nIt is that the heavy Illiteracy percentage Is due solely to the fact that\nIt has been New Bedford\'s misfortune to be inundated with Immigrants\nmore Ignorant in mass than any others who come to our shores. These\nfor the most part are Portuguese speaking Negroes from the Cape Verde\nIslands. The white Portuguese who come here average well In point of\neducation and readiness to adapt themselves to the standards of America.\nWith the blacks of Portuguese origin it is quite the reverse. Nearly all of\nthem are wholly illiterate when they reach this country and are obdurate\nIn so remaining.\nNew Bedford has an exceptionally large number of them. It is true\nthat nearly half of the city\'s population is of foreign birth. But with the\nexception of the Cape Verde islanders the average of Illiteracy among the\nother nationalities in New Bedford is about the same as \' it is else\nwhere. It is the Colored Cape Verde population which tips the illiteracy\nscale so adversely to the old home of the rugged whaler captains of days\nby. N. Y. Herald.\nrnLJl DR.RKTROTTER LX\nMHiAK PXKl\'.MOMA.\nAs slated in last week\'s Issue\n\'pneumonia is an,, Infectuous disease\nland may after a" reosonable length\ni of. time develope either a favorable\nlor unfavorable condition. It is pos\nsible for the disease to terminate\nin gangrene of the" lung, in an\nabsess, in the purulent infiltration\nof the lung tissue or It may bring\nabout disastrous changes in other\norgans and viscera, i. g., the heart,\nspleen, liver, kidneys, etc. This germ\nin which produces pneumonia Is\nfrequently found in the mouth, nose\nand air passages of healthy Individ\nuals and it is claimed that it can\nbe found in at least 20 per cent of\nthe cases examined and that pneu\nmonia is due to the migration of the\ngerms from the nose or mouth into\nthe pulmonary areoll. It is positive\nly presenl in over 80 per cent of the\ncases of pneumonia and may be de\ntected for months or years after\ndiscovery. By this we may under\nstand why pneumonia may be con\ntracted from one who has or is\nconvalescing. Persons who drink al\ncohol are especially prone to this\ndisease. In -fact any or all habits\nwhich show a. tendency to depress\nthe nervous system will predispose\none to this disease. The first at\ntack usually leaves the system more\nKYKS tlXD THEIR DEFECTS.\nBy F. S. Rogers, O. D.\nThose who lead what is called a\n"strenuous life are at last awak\nening to the fact that unless they\nstop all the little leaks in their sup\nply of nervous energy they will\nsome fine day experience the break\ndown that usually\' presedes the\nbreak-up. ,\nLocomotives tha,t run at high\nspeed have to be kept In perfect\norder, and It is the same way with\nthe human engine. The old law that\n"that which cannot be cured must\nbe endured" applies in relation to\nthe eyes, with the exception that\nwhile eye-strain cannot exactly be\ncured, it can be relieved, at least\nduring the time that glasses are be\ning worn which neutrilze the causes\nthat produce it.\nEye of the present generation\nare born no better, nor worse, than\nthose of previous generations. It\nis simply the advancement that has\nbeen made In the knowledge of op\ntometry, together with the needs\nof the times, which has popularized\nglasses.\nIf you will take the trouble to\ncount the people you pass on the\nstreet, you will find that one-third\nof them of 20 years of age and\nor less weakened so that repeated i over wear glasses. To this number\nattacks (ten or more) sometime oc-lmust be added a considerable num\ncur in the same Individual. I t,pr who WPar glasses only while\nthid, 18 aoS u , Tld T, he i ""The reLor1 for\' this very large\nthis disease. It Is claimed that the . , ,. , .. .,,,,\' ,.,\nmortality from this disease is great- Proportion of the popul ion wear\ner after the twentieth, year and I that ,n ,Kla8Se,8 ls, laJg ? T\n, a u.. ,,,,, strain contracted in youth and not\nit increases. Sex has little influence\nalthough some believe that the dis\nease is more fatal to female than\nI noticed or is overlooked until the\ntime arrives when the eyes refuse\nSTUDY IX CONTRASTS.\nThose who get a delight from drawing contrasts will be charmed by\nthe opportunity presented in circumstances in France and the United\nStates. French literary authorities have Just awarded a much-sought\nprize to a Negro author, and in the United States our statesmen are trying\nto find some way by which Negro citizens may be protectedVn their lives\nby the laws which protect all others. Can anyone imagine the most\nvalued literary prize in the United States supposing we had one going\nto a- Negro author? That would be an imagination that might amaze the\nworld.\nThe author and friends of the Dyer bill, designed to put an end to\nthe lynching of Negroes In America, are trying to find some legal sup\nport for the measure, and at best have advanced no further then discov\nering an academic co-operation. Opponents of the bill assert that it is\nconconstitutional, not specially because It alms at the abolition of lynching,\nhut because it is an attempt by federal authority to invade the privleges\nof the states.\nIt Iss raw form this means that if the state choose to disregard the\nfederal constitution the United States government has no way of com\npelling obedience. Or. in another form, It simply means that the constitu\ntion Is respected by those who choose to be respectful, ,but may be vio\nlated with perfect impunity by those who think differently. In plain\nwords, the retort Is that the people in those states where lynching flour\nishes as a civic pastime cannot be prevented from following their lawless\namusement.\nIn practice it amounts to\' this: That if one man kills another he\nmay forfeit his life to the state, but If 20 men; or 50 or 100, kill a man\nthe law Is powerless to interfere. Even those men and women, of the\nsouth who consistently and uniformly condemn lynching have a fear that\nthe suppression of this form of lawlessness in their states by federal in\nterference might be a worse evil than the refusal of states to protect Ne\ngro citizens. Perhaps the most discouraging feature is that the consti\ntution, that which we have always regarded as sacred, cannot be enforced\nby the very authority which exists only by virtue of Its provisions. Or\nif it can be enforced, it never has been, so far as the fourteenth and fif\nteenth amendments are concerned. . Pittsburgh Leader.\n- -vhe Kpiiit of goodwill might well\nlie maue t.n a\'l year quality.\nMIGRATION,\nThat more than passing attention- is being paid the Negro\ncitizenry of America is attested by the recent release by the cen\nsus department of a statement of the movement of Ngroes during\nthe past decade. .\nThe total number of Negroes born in and living in the U. S.\nis given as 10,381,309 of which number 8,288,492 or 80.1 per cent\nwere living in the same states in which they were bom and 2,-\n054,242 or 19.2 per cent were living in states other than those in\nwhich they were born. From a study of the same release it is\nfound that the per centage of Southern bom Negroes who migra\nted to the North and West was only one-fourth 1 ger than the\nnumber l01n in the North and West who migrated Southward.\nThe f-. J.s established by this release many instances show\nthat the ol.iions too often exp ressed to ihe effect that the Negro\nforms the most restless port! n of the American population, are\nnot well founded. It is not to be doubted but that the greatest\npercentage of migration to the North and East took place during\nwar times and found its rer.son for bMng in the more clvantag\neous economic and educational opportunities offered there. And.\nwhile it is true that a certain percentage of those Negroes who\nmigrated was of tho "drifter" class, reports of the economic pro\ngress of a lp-ge percentage of them slows that a far greatei\nnumber of them belonged to the steady, thrifty class who, hav\ning found chance to improve themselves made use of it, than\notherwise. ,\nThe majority of Negroes however is still in the South and\nhere will remain till some proven field which offeis greater eco\nnomic and political opportunity is opened to them. That thev\nare &teaduy improving themselves in all ways is easily seen rrom\nh study o" their decreased illiteracy, increased home, farm and)\nindustrial ownership and constantly expressed desire to advance?\nthe general well being of their communities. \'\nThey may become a great a"set to their section and Amenca\'\ngenerally if only it may become generally recognized hat thoirj\nwelfare and that of their neighbors is so inseperably linked h"\'\ninjustice or lack of opportunity to one means poverty of sob ft ,\nand lack oi progress to all It is certainly true thai migration I\nhas not lessened the need for a constantly increr-sin" spirit of co\noperation and friendliness among the races Li the South.\nThere mu",t come a time in the history of Ame ica when sec\ntional feeling is made secondary to the desire that law and au\nthorty be mad.! supreme.\nIN-solve to be hopeful during the\nl:olo N;w Yenr. Forget the ties." i\nGood men and respectable citizens are th result of culture\nin environments of education and freedom. Cowards and igno\nrant jien cannot do more than hinder loose of their fellows who\nhave advanced beyond thai stage. i\nSHUT UP; GET BUSY.\nIn by-gone days on the Mississippi River it was noticed of a certain\ndiminutive side-wheeler,\' -which boasted a large brass whistle, much out of\nproportion to the size of the boat, that whenever the whistle blew, which\nwas loud, long and often, the paddle wheels stopped turning, and the\nlittle boat remained stationary the whife. The boilers did not make steam\nenough to blow such a loud whistle and make progress up river at the\nsame time. \'\nThe American black man has so long been blowing his showy brass\nwhistle of empty discussion \'that the paddle wheels of construction have\nbeen stayed.\nHis verbosity is proverbial. His lack of constructive, organized efort\nis apparent.\nHe will stop on street corners, In restaurants, barber shops, , pool\nrooms, drug stores, in fact wherever he can get a hearing and elaborate\naf length the most trivial\' topic. He will consume hours in the most\nMleii\'n conclaves of church, state, fraternity, or society upon a point of\norder, A ?l"Dute over a question of procedure has disrupted many an or\nganization when the point at issue had no relation to the alms or the\npolicy of the body politic.\nHis love for elaborate\' form and ceremony and his weakness for\nhigh-sounding titles and splendid regalia have crowded from his mind the\nimportance of constructive effort.\nThe ringing resonance of his naturally oratorical voice has deceived\nhini into, belief that words are greater than deeds.\nHis splendid physique which enables Mm to wear striking regalia to\nadvantage has (Hied him with deception that appearances mean more\nthan actual values.\nNo race has yet become great by reason of Its orators and entertain\ners. - Such attainments come as the capjtoro, and not as the foundation\nof racial greatness.\nThe boilers of the black man\'s economic engine are not large enough\nto make steam enough to propel him against the even-Increasing resis\ntance oi\' competlon and race prejudice and at the same time blow such\na loud r-histle of ostentatious oratory. His present crying need is fewer\nwords, and more deeds. All of his-steam is needed for the purposes of\npropulsion In the form of intelligent organization.\nIf so much of his strength hau not been thus criiv.lnally wasted, the\nblack men would not be today the Great American Beggar. Instead of\npraying for alms In the voice of a mendicant he could voice his demands\nin unwavering tones.\nIf l.o had been evonomlcally organized, Booker Washington\'s dinner\nwith Th ordore Roosevelt would have been accepted by white America as\na ,confereuce between equals, and not as an instance of gracious condes\ncension on one hand, and grateful acceptance on the other Likewise, the\nthunderlngs of Monroe Trotter and the bitter shrieks of DuBois would\nsound more like the demands of a strong man than the waillngs \'of a\npeevish child. ,\nShut off That Blast of Inane Bickering and Empty Small Talk! Tear\ndown that Glint and Glare of Showy Brass and Tinsel! Direct all Your\nEffort Toward Propulsion!\nThanks be to God, a NEW Type is appearing. An Iconoclast, An Idol\nBreaker, who cares not for sounding brass or tinkling cymbal, who has\nSworn over the Ashes of his Fathers and at the Temples of his Cods, that\ntho days of the Pompous, Strutting Comedian and the Idle, Bickering\nSmall-Talker, ARE NUMBERED. Chicago Whip.\nmale. Once this disease has taken I t0 foT "\'ne!\'l\' and, ln ,n any -hold\non a alcoholic, he or she rarely ie, when too ong neglected, pro\nescapes death. Persons who are ! (i\'lcint ch Blraln on the eye mus\nvery fat or fleshy usualy have hard1"8 Jury to the nerve cen\ntimes. All persons having pnuemonia\nlers that considerable expense and\nshould be isolated in a well aired i discomfort are entailed before the\nroom with fresh air and as much siht .ean be restored, or partially,\nsunshine as possiDle may be breath- 0 normal.\ned by the patient. Fresh air in Depleted accommodation of the\nsuitable quantities will improve the eye muscles Is present in a majority\nappetite, reduced the temperature, of eases and is usually overlooked\npulse and respiration rate and lessen ! until such time that the constant\nthe cough. In fact it has been proven\nthat patients treated In this way\nare less toxic than those who don\'t\nget enough of the fresh air or sun\nshine. It is also very important\nthat they should remain in bed at\nleast one week after the crisis had\npassed. The diet Bhould be given\nboth light and sparingly especially\nwhile there is evidence of fever.\nThe food, usually milk, should be\ngiven at brief intervals and Indefi\nnite quantities. In meeting high\nwearing of glasses is necessary.\nWhen an individual\'s vision be\ncome impaired, it is absolutely safe\nfor him to first consult an optome\ntrist. The optometrist receives an\nexacting and rigorous training in\nthe schools; as a candidate of llcen-.\nsure he is subjected to critical ex\namination; and as an active special\nist )n this field he requires a spe\ncial aptitude for the manipulation\nof instruments and the recognition\nof every variety of eye abnormality.\nfevers, marked nervous symptoms, i j the majority of cass, the chances\ndifficult breathing, etc., hydrother-\naphy may sometimes be used to\ngreat advantages. For high temper\natures, Ice bags over the head, chest,\nor abdomen are useful, cool spong\ning combined with the ice pack\nserve as a substitute to the tub\nbath. It is well to keep the re-\ncepticle for the sputum.\nDiphtheria is an acute infectious\ndisease caused by the klebs, loffler\nbacillus and characterized by sore\nthroat, enlarged glands, chills fol\nlowed by fevers, pain, stiff neck,\nloss of appetite\nare that only correcting glasses, are\nneeded. In the small majority In\nwhich a truly diseased condition\nexisits, the optometrist can be re\nlied upon to recognize the pathol\nogic state and may be trusted to re\nfer the case to a practitioner of\nmedicine.\ndren but young adults and grown\nups are also subject to it. Exposure,\ncolds, sore throat, enlarged tonsils.\nFree tubercular clenic, Morgan\nTrotter \' sanitarium. Tuesdavs and\nThis disease usually attacks chil- j Fridays from 2 to 3 p. m.\nAFRICAN CRUISE DELAYED IN M.Y.;\nEXPPESS CORESPONDENT STUD\nIES LIFE IN-HARLEM.\nT.NWi X.tTIVP. M-:(il(0 KKSIUK.NTS\nIN COW.\n(Continued from page 1).\nlem. Thene people belt the\nAmerican whiten operate the buslnexR\nlines they seem to, die when coining\nto New York. Were it not for tho\nbarber shops, restaurants, preHHlng\nKhopa ari\'.l a few isolated efforts ut\nbuHtnes aloni a few other lines you\ncould write zero after Negro business.\nThere seems to be a lack in Neirro\nsolidarity which would bring togeth\ner groups of men who could put over\noik entni prises, l nave been coming\nto New York once and some times\ntwice every year, but 1 have usually\nbeen quite busy while here and hud\nno time for getting Information, but\nmy aetcntion nas given - me an op\nportunity to study some of the con\nditions, and Inspite of myself the\nquestion presses upon my attention,\nis rvew iorK to tne DiacK man what\nthe Dead Sea is to its tributaries?\nYou know tho string thing about\nthe Dead Sea, nothinic can live l.i\nIts waters. A gentleman from Florida\ntame here bought a building and fitt\ned up the ground flour with the ex\npensive furniture with the view of\nopenin\'; up a Negro bank, but when\nhe nis i application to the authorities\nfor a quarter to do banking business\nit was denied him some say he was\ntold that they did not need any more\nbanks in Hurlem. others sav he whs\nnot able comply with the banking\nlaws of I ho state of New York, lie\nthat as n may, if the Negroes of\nHarlem wno have mnnev and Influ\nence had united behind the project\nthey would have the bank. How long\nwill the Negro ln thiie centers stav\napart and allow the other man get\nevery dollar he makes robbing hi\nown wife and children of bread and\nclothing and turn it jver to others.\nThis enterprising gentleman from Flo\nrida nave men now at work clmnKing\nthe bank building and furniture .nto\na restaurs .\'t.. his aspirations plunged\nInto the "Dead Sea."\nWashington. Jan. 6. January 1,\n1920 the number of Negroes lving In\nConnecticut, who had been born there\nwas 7.XKS. Of Connecticut born Ne\ngroes there were living In other\nstates 3.SII9, making a total of Con\nnecticut born Negroes 11.397. Of Con\nnecticut born Negroes the percentage\nliving In other states in 1900, 23.6 In .\n2S.4 and 1920 30.8 per cent.\nThe total native Negro population\nof the United States January 1. 1920\nwas 10.389.328. The states in which\nmigration was largest were: Missouri,\n81.2; Vermont, 75.1: Utah, 72.6; Ne\nvada, 757: South Dakota, 70:7; Wy\noming 68.8.\nThe- stutes ln which migration was\nsmallest were Texas, 10.6; Florida,\n14.4; Arkansas, 14.1; Georgia, 15.3.\nCOKCiltKNMMKN OltAW COLOR MNB\nl. lllTAl KANT.\nWashington, Jan. 5. Protest against\nthe practice of, permitting Negroes to\npatronize the\' -Hou ot Representa\ntives restaurant In the capital, was\nmade by Representative Aswell, Dem\nocrat, of .Louisiana.\nln a letter, to Chairman Ireland,\nRepublican, of Illinois, and Demo\ncratic member of the committee,\nwhich Is In charge it the restaurant\nmanagement. Mr. Aswell stated four\nNegroes had been noted eating In tho\nrestauran during the last tew days.\nHe asked by whose authority the\nNegroes were admitted and whether\n"this is to be the practice of your\ncommittee under the present Admin\nistration "\n"Ucntlemen of the Hojse.1\' Mr. As\nW" i\'s letter continued, "should have\ntl is Information now so they may\nknow whether to kesp their families,\nfriends and themselves away."\nAssurance is said to have been\ngiven that hereafter the restaurant\nwould be restricted to whites.\nXKftROE OIlfiAXI.R\nRKI.IKP COMMITTEE.\nWORK STARTS NOON OIV TTSKK\nii;i, Mil.KO FKDfeCRAL, IIOM\'ITA!.\nCOXGRKSS TO CURB IACHING.\nAfter the vait effort of the Southern Congressmen to prevent the\nadoption oi a rule to consider the Dyer ntl-Lynching Bill, and the fail\nure of every parliamentary device for delay, It seems manifest that the\nRepublican majority in the Houao of R.prientative will pass that meas-\nWashlngton, Jan. 5. In oraer to as-\nslt Jn relieving the unemployment\nsituation, uoi. rawara uiurord. As\nsistant Secretary of the Treasury,\ndepartment would Immediately under\ntake construction work on various\nhospitals to the amount of 114.762.\nI\'OO. Among the largj.it hospitals on\nwhich work will be begun at once,\nClifford announces, is that to be lo\ncated at Tuskegee for the \' trentuient\nof former Negro soldiers suffering\nfrom tuberculosis and mental dis\neases. This hospital will jost K.\ni\'bO.OOO. i\nPittsburgh, Pa., Jai. 5. Id an ef\nfort to alleviite coniitljns imjiig the\nNegro populace of Pittsburgh, due to\nthe Industrial depression, a Negro\nemergency relief committee has been\norganized under the. auspices of the\nUrban League of Pittsburgh. At a\nmeeting of the committee it was an\nnounced that 16 churches and club\ngroups hnve Joined in a campaign to\nsecure work for the unemployed Ne-\nThe - committee manifested its ap\npreciation of the Negro race oy the\n-n.im.cittieu v.narities, improvemer t of\nthe Pn, r and the Salvation Army.\nTho officers of the relief commit\ntee are p. U. Lswls, president; John\nlarter toblnson, secretary, and Mrs.\nUeorgo Gould, treasurer. The heao\nquarters are at 518 Wylie avenue.\ntire, and mere than piobable that the Senate will pass it. We imagine\nthere is little prospect of a veto. The bill makes crimes under the Fed\neral Jurisdiction what have been cognizable only by State courts.\nAny person who participates in a lynching mny be imprisoned for\nlife or not less than five years. Any officer who permits a prisoner in\nhis custody to be taken by a lynching mob may be imprisoned for five\nyears or fined $5,000. Any county in which a lynching takes place will\nforfeit $10,000, to go to relatives of the victim. .\nThere will be no vote in the House or Representatives until after\nChristmas, but no change ln the situation is likely, and under present\nrvies filibustering is Impracticable. The aims of the meisure are good.\nIn practice, as The Eagle has pointed out, difficulties are certain. Nobody\ncan be convicted of crime, even ln a Federal Court, without the verdict\nor a Jury of the vicinage That is always to be remembered.\nBrooklyn Eagle.\nft\no\nt\nly.\n; , ,',
       u'page': u'PAGE FOUR',
       u'place': [u'Texas--Dallas--Dallas', u'Texas--Harris--Houston'],
       u'place_of_publication': u'Dallas, Tex.',
       u'publisher': u'W.E. King',
       u'section_label': u'',
       u'sequence': 4,
       u'start_year': 1000,
       u'state': [u'Texas', u'Texas'],
       u'subject': [u'African American newspapers--Texas.',
        u'African American newspapers.--fast--(OCoLC)fst00799278',
        u'African Americans--Texas--Dallas--Newspapers.',
        u'African Americans.--fast--(OCoLC)fst00799558',
        u'Dallas (Tex.)--Newspapers.',
        u'Dallas County (Tex.)--Newspapers.',
        u'Harris County (Tex.)--Newspapers.',
        u'Houston (Tex.)--Newspapers.',
        u'Texas--Dallas County.--fast--(OCoLC)fst01205630',
        u'Texas--Dallas.--fast--(OCoLC)fst01204635',
        u'Texas--Harris County.--fast--(OCoLC)fst01213346',
        u'Texas--Houston.--fast--(OCoLC)fst01205077',
        u'Texas.--fast--(OCoLC)fst01210336'],
       u'title': u'The Dallas express.',
       u'title_normal': u'dallas express.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn83025779/1922-01-07/ed-1/seq-4.json'},
      {u'alt_title': [],
       u'batch': u'batch_txdn_audi_ver01',
       u'city': [u'Dallas', u'Houston'],
       u'country': u'Texas',
       u'county': [u'Dallas', u'Harris'],
       u'date': u'19191018',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 9999,
       u'frequency': u'Weekly',
       u'id': u'/lccn/sn83025779/1919-10-18/ed-1/seq-4/',
       u'language': [u'English'],
       u'lccn': u'sn83025779',
       u'note': [u'Also issued on microfilm from the Library of Congress, Photoduplication Service; Micro Photo Div., Bell & Howell Co.',
        u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.',
        u'Description based on: Vol. 7, no. 14 (Jan. 13, 1900).',
        u'Latest issue consulted: 78 years, no. 40 (Oct. 3, 1970).',
        u'Published at Houston, Tex., Nov. 14, 1970-'],
       u'ocr_eng': u'TIIE DALLAS EXPRESS, DALLAS TEXAS, SATURDAY, OCTOBER 18, 1919.\nfHB DALLAS \' IXFBK8S\nMEMBER\nNATIONAL NEGRO PRESS\nASSOCIATION.\nrubllaned every Saturday mornin\nId. the year at 2600 Bwiaa Arenur\nby\nran Dallas rtpbkm rtHi,iiait-\nCOMPANY.\n(Incorporated)\nDalian. Texan.\nKrm Tark Olllct, Frost . Froei\nII N.aiith HtreeL \' ,\ni klfuit OUlcc, Frost Front. Boy\ne building. .\natlanie Frost A Frost, Can\nir HuMdluw.\n.Sathtllle Oilier, Front & Frost, in\n4-pndwnt t.lfn Building;,\nJ. Hs JORDAN, Maaacer.\nEntered at Post Office at Dallas\nCejan, aa eecond-c-lass matter, undei\nart of Conitreaa. March, 17.\nIMPORTANT.\nNo eubecriptlona mailed lor a lea\nvrfiod than three month. Payment\nu aame must be (0 cents.\nst\'HsCIUPTIOKS IN ADVANCK.\nOne Year.\nt\'x Months \'?\nrhree Month\na\'nurl Topy. . . .\nNOTICJ! TO THH PUBLIC.\nAny erroneous reflection upon the\n-haracter. atandlng; or reputation of\nany person, firm or corporation whlan\njav appear In the columns of The\nOallaa fcxpreaa will be gladly cor.\nrected upon Ita being brought to the\nattention of the publishers.\nSATURDAY, OCTOBER 18, 1919\nA PRAYER FOR JUSTICE AND BROTHERHOOD\nBY WALTER RAUSCHENBUSH\nChrist, thou tort bidden ns pray for the coming of thy Father\'s\nkingdom, In which Us righteous will shall be done on earth. We\nhare treasured thy works, but we hare forgotten their meaning, and\nthy great hope has grown dim In thy church. We bless thee for\nthe Inspired souls of all ages who saw afar the shining city of God,\nand by fuith left the profit of the present to follow their vision. We\nrejoice that today the hope of these lonely hearts Is becoming the\nclear faith of millions. Help ns, 0 Lord, In the courage of faith\nto seixe what has now come so near, that the glad day of God may\ndawn at last. As we hate mastered Nature that we might gain\nwealth, help ns now to master the social relations of mankind that\nwe may gain justioe and a world of brothers. For what shall It profit\nour nation if It gain numbers tyid riches, and lose tne sense of the\nliving God and the Joy of human brotherhood! Make us determined\nto lire iby truth and not by lies, to found our common life on the\neternal foundations of righteousness of love, and no longer to prop\nthe tottering honse of wrong by legalUed cruelty and force, Help ns\nto make the welfare of all the supreme law of our land, that so our\ncommonwealth may be built strong and secure on the love of all\nIts citizens. Cast down the throne of Mammon who ever grinds the\nlife of men, and set up thy throne, 0 Christ, for thou didst die that\nmen might live. Show thy erring children at last the way to the\nCity of Love, and fulfill the longings of the prophets of humanity.\nOur Master, once more we make thy faith our prayeri "Thy kingdom\ncornel Thy will be done on earth I"\nTHE MIRROR OF\nPUBLIC OPINION\nSOME MEN ARE SPEAKING OUT.\nThe following Press Symposium is reproduced here in order tint we\nmay receive from it added strength, and determination to continue the fight\nagainst the acknowledBed disgrace of Christian civilization, lynching. As\nthese men are thinking and gaining the courag e to P out fj"?\nof Civilization and Justice others will be constrained to speak and do if\nwe continue as we are doing to make public the need of a e Opinion\nwhich stands for strict Justice. We are indebted for some of these clipp\nings to William A. Aery of Hampton Institute.\n"I have no patience with this wholesale indictment of a race\nv t orimea of bad individuals. The whole\nthing is wrong, eternally wrong, and all of us must do all we can\nto stop it There are bad white men and bad black men, and\ncrime is crime by whomsoever committed, and X could never see\nthe wisdom\' or sense of the newspapers mentioning a man\'s race con\nnection .with his commission of a crime, which is so generally the\npractice where the offender is Colored. There can be no question that\nsuch a practice helps to stir up and keep alive prejudice, and at\nthis time of all times, when all over the world people\'s nerves are\n\' unstrung and we are facing serious situations in our country as\nelsewhere. 1 do wish something could be done to stop this thing\nwhich all sensible men admit is Benseless, wrong and dangerous."\nE. P. SMITH, Mayor of Omaha.\nTEXAS REPUBLICANS BAB NE\nGROES. In last. weeks Issue we published\na statement from the Executive Com\nmittee of the Republican Council of\nTexaa which stated In part :\n"Texas is a white man\'s country\nand government must rest exclusive\nly in the hands of the Caucasian\nrace. The Council is composed of\nwhite republicans and they will dic\ntate the policies of the party. We\nInvite all law-abiding citizens who\naccept the above principles of govern\nment to vote the ticket but the poli\ncies of the Republican narty of Tex\nas are now in the hands of white\nmen and are going to remain there.\n"The Republican party has la times\npist been prostituted by Negroes be\ning allowed to attend conventions as\npawns of white politicians who were\nseeking office or power .within the\nparty. For the past thirty years the\nprogress of the party has been re\ntarded and its development dwaf\ned by black and ten politicians par\nticipating In and attempting to domi\nnate its affairs.\n"This Country must be governed\nby white Americans who are In sym\npathy with American traditions and\ninstitions."\nThis statement is both surprising\nand dissappolntlng to many leaders\nin politics among us for they had\nbegun to plan for a gathering and\nmassing of Republican votes among\nNegroes which would have compelled\nthose chosen by, them to "go over\nthe top." We feel however that since\nevery man Is allowed to choose his\nassociates political and otherwise,\nand since the White Republicans of\nTexas have chosen to discard and re\nfuse the votes of Negro Republicans\nand have told them so in such unmis\ntakable terms, It would show less\nthan the manhood and racial pride\nwhich resides in the soul of every\nblack man If he by apy means what\never sought to affllate himself with the\nRepublican\' Council in \'Texas. Be too\nproud to beg In where you are not\nwanted.\nThere are about 200.000 Negroes\nof voting age in Texas. Let us sug\ngest that plans be made by which\nthey may all become holders of\nnnll to v rocnlntn thnn from all Dres-\nent indications It will be possible to\nmake In Texas a political organiza\ntion which will ally Itself only with\nthose who can realize that its co\noperation Is essential to their suc\ncess. There are prohibitionists, so\nouiiata and others who could ap\npreciate 200,000 votes. We call your\nattention to an article puDiisaea in\nthis paper last, week which declared\nthat th anti.KAlnnn leatrue has de\ncided to cultivate the sentiment\namong Negroes and is planning to\nspend a great part of its yearly bud\nvat In tht rilrAc.tlnn.\nIt may be that we have blinded\nourselves to our own interests in ror-\nuir vsara tiv hllndlv allying OUr-\n\' selves to the Republican party right\nor wrong, lor us or againBi us. iui\nis a fine opportunity to "take tock,"\neducate our voters and\nraake of ourselves an organization\nnot Ukety to ne cast asiae. .\nIn these critical times, saving Is\nserving. - -\nSilk shirts may look well blowing\nin the breeze, but ther do not be\nspeak the good Judgment of tbe\nwearer. usually from his appear\n\' ann.a wa nra riven to fell either that\nIt is his only shirt or that lie Is\ntrying to Impress upon us nis opu\nlence. Good Judgment seems to de\nmand more than one slilrt that is\nof serviceable quality and the expen\nAin nt th halanna on hand In\nImproving conditions more worthy of\nImprovement tnan- one s personal ap\npearance. : .\nIt la a difficult matter for a race\nto acquire culture where the strug\ngle for existence occupies .the whole\n, of n,ume. :\nAMERICA\'S GREAT MISTAKE. .\nThe part that Negro labor has played in the transformation of the\nAmerican wilderness Into productive fields and matchless plantations will\never stand as a monument to its efficiency. When it Is remembered that\nfor 250 years this labor was performed without the incentive of enumer\nation, its efficiency becomes really appalling. This work, together with the\nhardships and heritage which slavery entailed, was the price the Negro paid\nfor his rights and privileges as American citizens. .\n\' It must be conceded that it was a dear price. The Negro, however\ngrumbled not at the price paid. He has witnessed above 29,000,000 foreign\ners receive these same rights and privileges for the paltry consideration\nof having crossed the Atlantic ocean and declared their intentions of be\ncoming American citizens. The Negro has stood uncomplainingly by and\nseen these same foreigners presented with keys that unlocked every Ameri\ncan Institution from the coal mines to the White House, while he himself\nremained an alien in the land of his birth. We say an alien In the land\nof his birth because the Negro has never received that which he so dearly\npurchased, the rights and privileges of an American citizen. It Is true that\nan abbreviated form of-this, most coveted treasure was delivered to him\nby inaccumulatlve effects of the thlrteenthTfourteenth and fifteenth amend\nments to the constitution, yea when he attempted to use his newly acquired\nproperty he found himself hemmed round about by his former masters who\ndeterred him by. means over which he had no control. When reconstruction\nwaB fully completed the Negro began to be despoiled of his newly acquired\nrights, and privileges, one by one, \'until today he stands without a peer\nthe most unprotected class of people In America. These facts can be at\ntested by current history. Judge Lynch .haa held open court. In the largest\nand most nrogressM cities mob violence has terrorized his life nd threat\nened to ostracise him from all remunerative employment. In fact \' the\nNegro seems to be suffering from a serious attack of social pressure. In\norder to" make this attack effective every law guaranteeing protection to\nlaw abiding citizens has been violated. Local authorities have .been un\nable to cope with the situation. Federal authorities have taken no hand\nother than to restore order. No Immediate relief from this social pressure\nis in sight \' The chief obstacle that lies In the way of relieving the tense\nsituation is the apparent impossibility of bringing the better element of\nthe two races together. Each race appears to be suspicious ot the others\'\ngood intentions. The whites fear a Negro uprising as events la Arkansas\nfully demonstrate. The blacks feel keenly the heel of oppression following\nso closely upon the supreme sacrifice made by them in establishing de\nmocracy for the world In the late great war. Indeed these race troubles\nare most unfortunate at this time when the Negro hand been led to expect\nso much from democracy being made safe for the world.\nThese same twenty-nine million foreigners who have Immigrated to\nour shores are the ones responsible largely for the the American labor\ntroubles. They have received every privilege in the gift of American insti\ntutions yet they seek to undermine the properlty of the country. Had\nAmerica treated the Negro as she did the foreigner she would have had a\nlarge conservative element to support the native Americans against those\nhyphenated peoples who threaten the very existence of organized society.\nAmerica may yet find that the Negro is essential to her safety; \' The great\nmistake Amerlcahas made lies in fhe fact that she has not delivered to\nthe Negro all\' the rights and privileges, for\' which he has so dearly paid,\nwhile she has gratuitously delivered the same to such enormous hordes of\nungrateful foreigners. In fact she has asslraulated .these foreigners at such\na rate as to endanger American institutions. - America is no longer America\nfor the Americans. There Is every evidence-to show that In this, America\nhas made the mistake of her life. May she recover and at the" same time\nbe ever mindful cf her neglected duty to her deserving loyal Black Amerl-\nProf. Edwin Mims of Vanderbllt University, says:\n"Lynching," "is unjustifiable under all circumstances. It is\nwrong in the sight of man and God. It is a blot on our National\nescutcheon and la a menace to the whole country. It Is an eco\nnomic peril to the South. It Is Inexpedient, unwise, and a political\nmistake. Above all, It is a community and a National sin.\n"When one set of people sets up a crime for which a lynching\nis Justifiable, there Is nothing to prevent another group of peo\nple from setting up another crime for which it considers lynching\nequally Justifiable. \' A mob in action knows no law. It knows no\nreason. It Is governed only by Its passion at white heat\nIf the community can not stop lynching, then the State can. If the\nState can not stop lynching, then the Nation can and WILL."\n"Lynching does more than rob its vlctimB of their constitutional\nrights and of their lives. It simultaneously lynches law and Justice\nand civilization, and outrages all the finer human sentiment and\nfeelings. The wrong that It does to the wretched victims is almost\nas nothing compared o the injury it does to the lynchers them\nselves, to community, and to society at large. Lynching is a con\ntagious social disease, and as such la of deep concern to every\nAmerican citizen and to every lover of civilization\nCivilization rests on obedience to law, which means the substitution\nof reason and deliberation for impulse, instinct, and passion."\nUniversity Commission on Southern Race Questions.\nWhen will the convervatism of the South and when will the Intelligence\nI of the South assert itself in such a way as to have the punishment of all\ncrimes metea out in a court law wnen win tne eoumern peopia stop uem\ntreylng themselves by lynching Negroes when it is so easy .legally to pun\nish a Negro malefactor? .\nThe lynching business has become such a matter of course and the mob\nseems to sway such an Influence that right-thinking men in a community\nare deterred from protesting. It has even become so that the newspapers\npass lightly over these occurences.\nThe lynching spirit has become so dominant that it is not "good form"\nto protest If anyone does denounce what has been done, there Is often\nan answer that he Is not loyal to the doctrine of race superiority and that .\n,somehow he Is an advocate of Negro equality.\n\' Now we have engaged In a great war to make the world safe for de\nmocracy. Thousands of our young men have died that men may be free.\nTwo million American soldiers went to France o put a stop to the torture\nof women and children, to the burning of homes, and to the pillaging of\ncities. v\nWhile these young men are in France seeking to restore peace and\norder should not our stay-at-homes, too, desist from organized lawlessness?\nThe South will progess only when It becomes known by practice that In\nthis land, crime is punished legally; that crime is not permitted to go\nunpunished legally; and that the law proceeds, unshackled and untrammeled,\nln,all these activities which make human life and human society safe and\nwhich make for tbe refinement as against the coarsing of men. Commercial\nAppeal, Memphis, Tenn.\nTHE MAYOR OF OMAHA.\nIn all the history of America there has been no more disgraceful chroni\ncle ever written than that which will be written as a result of the lawless\nness of some of Its citizens in Omaha a few days ago. We need not dis\ncuss Its horror. We know that well enough because such things are be\ncoming every day occurences now. We do feel that no tribute that tongue\nor pen could pay could ever nearly be made adequate In commendation of\nthe action of that real man, the Mayor, who felt and carried out aa far as\nhis strength was able, his duty aa a public servant Ten millions of people\nwho scarcely ever know the helping hand of frank, open, publicly demon\nstrated friendship and justice have been made to feel by this man, almost a\nmartyr because of his performance of duty, that there Is a sense of Justice\nand fairness left in those who govern and that there are some who dare\nto live and are willing even to die by their determination that law and\njustice must prevail. We honor hla action. May God hasten the day when\nmen generally realize that a sense of duty done is better than all personal\ngain or safety at the expense of Justice. We fully agree with Sumner when\nhe says: , V - \'\n"I honor jUie man who In the conscientious discharge\'\nof his duty dares to stand alone; the world, with Ignorant\nIntolerant Judgement, may- condemn, the countenances of\nrelatives may be averted, and the hearts of friends grow "\ncold, but the sense of duty done shall be sweeter than the , . H\napplause of the world, the countenaces of relatives or the\nhearts of frWsids. .\nIn Omaha, The .World-Herald said editorially:\n"We have felt however briefly, the fetid breath of anarchy on\nour cheeks. We have experienced the cold chill of fear which it\narouses. We have seen as a nightmare its awful possibility. We\nhave learned how frail is the barrier which divides civilization from\nthe primal jungle, and we have been given to see clearly what that\nbarrier Is. It is the law. It is the might of the law wisely and\nfearlessly administered. It is the respect for and obedience to the\nlaw on the part of the members of society. When these fall us, all\n.; things fall. When these are lost, all will be lost Should the\nday ever come when the rule that was In Omaha Sunday night be\ncame dominant rule, the grasses ot the jungle would overspread our\ncivilization, its wfld denizens, human and brute, would make their\nfoul fcaBt on the ruins, and the God who rules over us would turn\nhis face In horror from a world given over to bestiality. May the\nlesson of Sunday night sink deepr" .\nThere is a great deal- said about attracting sturdy Western people to\nthe untenanted acres of the South; but we defeat our own purposes when\nwe sanction contempt of law and resorts to violence. The eWstern people\nhave quite a different idea of what Is good citizenship and they seriously\ndoubt the wisdom of settling in a land where the laws are flouted and no\none Is brought to punishment for so doing. They can not understand that\nlife and property as a general thing, are as safe with us as In any other part\nof the country. They Judge by what they read In the papers adn, so Judging,\ndecline the proposals to move into the South.\nThus H is that lynching does us incalculable harm on the economic\nside. It hurts also since It is Interpreted as the evidence of a low state\nof civilization. The few who are lawless have the power to cast discredit\nupon the whole Southern people.\nThe way to escape is through the creation and establishment of sound\npublic opinion. The thing is possible,- as we see exemplified in Virginia.\nLet self-respecting people speak out against this form of crime- let grand\nJuries indict and the courts try and convict the law-breakers, no longer\nmaking excuses, but recognizing that lynching Is an unmitigated evil and\nmust be blotted out ... t -Journal, Montgomery, Ala.\n1\nf',
       u'page': u'PAGE FOUR',
       u'place': [u'Texas--Dallas--Dallas', u'Texas--Harris--Houston'],
       u'place_of_publication': u'Dallas, Tex.',
       u'publisher': u'W.E. King',
       u'section_label': u'',
       u'sequence': 4,
       u'start_year': 1000,
       u'state': [u'Texas', u'Texas'],
       u'subject': [u'African American newspapers--Texas.',
        u'African American newspapers.--fast--(OCoLC)fst00799278',
        u'African Americans--Texas--Dallas--Newspapers.',
        u'African Americans.--fast--(OCoLC)fst00799558',
        u'Dallas (Tex.)--Newspapers.',
        u'Dallas County (Tex.)--Newspapers.',
        u'Harris County (Tex.)--Newspapers.',
        u'Houston (Tex.)--Newspapers.',
        u'Texas--Dallas County.--fast--(OCoLC)fst01205630',
        u'Texas--Dallas.--fast--(OCoLC)fst01204635',
        u'Texas--Harris County.--fast--(OCoLC)fst01213346',
        u'Texas--Houston.--fast--(OCoLC)fst01205077',
        u'Texas.--fast--(OCoLC)fst01210336'],
       u'title': u'The Dallas express.',
       u'title_normal': u'dallas express.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn83025779/1919-10-18/ed-1/seq-4.json'},
      {u'alt_title': [],
       u'batch': u'batch_mohi_dorothea_ver01',
       u'city': [u'Sedalia'],
       u'country': u'Missouri',
       u'county': [u'Pettis'],
       u'date': u'19030725',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1909,
       u'frequency': u'Weekly',
       u'id': u'/lccn/sn89067598/1903-07-25/ed-1/seq-2/',
       u'language': [u'English'],
       u'lccn': u'sn89067598',
       u'note': [u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.'],
       u'ocr_eng': u'mm"1\' .\nMl-\nha.-\njar -\nSedalia Weekly Conservator. -\n: Rely Upon Self.\nis.:\nI.\nI Dignity of Honest Labor.\nla,\nf\nr\npublished Every Saturday. Office,. 104 E. Main St., Up Stairs.\nEntered June 16, \'OS at Sedalia, Mo., at second class matter un\nder Act of Congress of March 3, \'7.9.\nW. H. HUSTON\nC. M. ENGLISH\nThis Space Will Notify You\nWhen Your Subscription 1 Due\nContributions must be accom\npanied by name as an assurance\nof good faith.\nAll articles for publication must\nbe in by Wednestlay.\nOne Year\nSix Months\nOne Month\nSUBBCniETION,\nThink for Thy. Self ono cooj thonghi;\nAmi know It to be Tblna i)n,\nTf better than a thousand gloarwd\nFrom tleldrt by others Mwn,\nonoru\nEditorials\nm i\n1 \' Bilteflri-fc If\nEDITOR.\nMANAGER\n$33\n75\n5\nWhy rely upon self? Because, most often those from\nwhom we anticipate aid are not willing to grant same. Then\ntoo, the after-knowledge of our over-corn eing difficulties, by\nour own personal labors, is ever sweet reminiscence.\nAsk no more than you expect to repay. Receive no greater\nlavors than you hope to grant, are excellent mottoes for us\nall to adopt. Only the strong and lion-hearted can hope to\nlead m the present race of life.\nOne cannot hope to become strong and active without ox\nertion. Hence, be quick to act; above all things else, be cour\nageous. What other men fear to attempt, when, assisted by\nothers, try yourself. It is far better to attempt a noble under\ntaking and fail than to not try at all.\nWould we have ever had the locomotive, the ocean grey-\nIiound, the electric light, car, ect, the telegraph, telephone,\nand myriad of other useful, and indespensabe inventions, if\nsome one had not made earnest endeavors? Then it might be\nasked, would we have had what we now have, had we not\nmade venturesome attompts attempts, when others said you\nwould fail; attempts, when those from whom you expected aid\nwent back on you? But you placed your trust in self; you\nknow the results\nYoung man, how did you secure that education? You will\nsecure other good things in life by the sama earnest, sacrifi\ncing efforts.\nThere seems to be an erroneous idea prevalent among the\nmasses relative to manual kW, Qd especially is this true a\nmong the younger element. The greater par cent of Ameri-\ncan youth seems to be imbued with the notion that nothing is\nso demoralizing as honest toil. They prefer to linger about\ntlie gaming table, resort to sharp practices and any devico\nconceivable, to save themselves from labor dignified labor.\nThen ,too, the money \'raze has driven many a well mean\ning young man into error. Every thing honor, integrity,\nlntcllcgeuce and all is subordinated to the mighty dollar.\nHence, a young man may enter into one of two classes of em\nployment, uame\'y. one that pays little in cash but which de\nvelops his manhood qualities by laying the foundation of true\ncharacter. This, however, is not the popular class. Why?\nBecause there is not sufficient money paid to maintain his\nstanding in the church and society. And we all know one is\nweighed in society by the Gold Eagle standard.\nThe other choice open to ambitious youth is the one that\naffords an opportunity for securing ample pay to meet the\ndemands of both society and church. At what a cost, tho, are\nthese obtained when entering upon the duties of any position,\none should make its monetary compensation a secondary con\nsideration. And, why? It may be very readily seen that en\nindividual may, from the invironments that encompassed a\nmoneyed position, be brought into close relation with things\nor conditions that will ultimately dwarf their truest qualities\nof dispositiong and character.\nHence, we hear from every progressive source admonish\nments to the youthful of every race to allow their energies to\nbe exerted to action by motives, of genuine worth, based upon\nthe nobler value of life the consciousness that one has per\nformed his duty to his country, his fcllow-limn and to his God.\nThere-fore, let us in our choice of labor be careful that we\ninjure not our character, mind you, this is all you may ever\nhope to retain eternally, for "Fill hy Lucre." Maintain that\nhigh ideal of pure and stainless purpose,- even if;society and\npublic sentiment are endeavoring its over-flow by active opposition.\n1 Beautiful Hammocks\nS8 Soma of the prettl\'Bt hom\nB mookn over exhibited In\nH Sedalia at\ni\nU Glcschen & Viebrocks\nI n6W. Hninst.\nThey must bo socn to bo fully\nappreciated. Tlioy ar nnna-\nsome, aomiortamr, ana auru\nble and retail at from\n40c-$3.25\nned And decoiated no as to tern as oftVct\niro agencies for educating not only the\nehlldr n but tho people as woll in matters\nof lato. The school Is becoming more\nand ruoro a community center and its lar\ngeropportunitiesimposenew obligations.\nSchool buildings should bo attractive as\nwell as belthtul, and tho adioinig grounds\nshould be laid out and planned with ap\npropriateness and beauty.\n7 Disregard for law and its establish\ned modes of procedure is as serious a\ndanger as can mennco a democracy.\nThe restraint of passion by respect for\nlaw is a distinguishing mark of civilized\nbeings, To throw off thatreatraintwheth\ner br appeals to brutal instincts or by\nspecious pleas tot a law of nature which\nIssaporior totho laws of man, h to re\nvrt to barbarism. It is the duty of the\nschools so toltythe foundations of char\naoter la the joung that they will grow\nup with arovoreuccfor the majesty of\nthe law. Any system of school discipline\nwhich disregards this obligation u harm\nful to the cbild nnd dangerous to tho\nstate. A democracy which would endure\nmust bo&s law-abiding as it is liberty-\nloving.\nGeorge R. Smith College,\nRev. I. L. Lowe, D. D., Ph. D., President\nCALENDAR FOR\n190304\nThe SKDAI.I A DAILY CAPITAL\ntakes a manly stand in denudation\nof Lynching. In describing what\nlynching for grave offenses may ul\ntimately lead to it spctks plainly as\nfollows: \'\'William Thacker.a white\nman was taken from jail at Flem-\ningsbnrg, Ky.on Thursday morning\nbeatten senseless and hanged until\ndead.\nThe only excuse given by the mob\nfor this murder was its fear tuat\nThacker would ultimately escape\npunishment for a murder which he\nhad commIUe-I.\nTwo r \'ars ago, Thacker killed Jno.\nGordon in a quarrel. He was ar\nrested, tried twice, and sentenced to\nlife imprisonment. I Ic appealed to\nthe Supreme court, which granted\nhim the third trial. Fortius he was\nwaiting when murdered by the mob\nIn other words, he was lynched\nfor standing upon his constitutional\nright to a trial which the highest\ncourt of Kentucky would regard as\nfair and impartial.\nThat any man, no matter what his\ncrime, should be lynched for insist\ning upon a fair and impartial trial\nfor standing upon his constitutional\nright reduces to as absurdity all\nthe usual excuses for lynching and\nproves that lynching is a habit that\ngrows hy iudulccnce until it ap\nproaches sheer anarchy, says the\nChicago Inter-Ocean.\nThe usual excuse for lynching ne\ngroes is that it is necessary to put\nthose who commita certain crime to\ndeath speedily in order to protect\nwhite women, It must be remem\nbered however, that fully one-half of\nthe negroes lynched in the past ten\nwere not even accused of that crime\nWhat white womau is protected by\nthe lynching of negroes for other of\nfensesf And, what white womau\nwas protected by the lynching such\nwhite men as Thacker?\nA community begins by lynching\na negro for a brutal assault upon a\nwhite woman. Ordinarily iaw-abid\ning men, such is their horror of the\ncrime, condone the lawlessness.\nBut the appetite for lawlessness\ngrown by what it feeds upon. It\npasses on to lynching negroes foro\nther crimes, and even for petty mis\ndemeanors, Then it lynches a white\nFall tenn opens Sept. 22, closes.\nDec. 11. "Winter tenn opens Dec.\n19. Spring term\ncloses April 2S.\n14, closes Feb.\nopens Feb. :\nThe purpose of the College is to give a thorough, practical christian\neducation. It cares for the health and physical training, provides for\nrefined social culture, gives careful attention to morals and manners\nand aims to lead the student to n personal religious life.\nThe work of the College is divided into six geueral departments.\nI, Primary and Grammar Oradcs, providing a thorough drill in\nthe elementary branches.\nII Academy or College Preparatory, withCIassical, Scientific, Bib\nlical English, Normal and Commercial courses.\nIII Art Department Drawing. Painting aud Decorative woik,\nIV Music Department Vocal and Instrumental Music, Theory;\nand Harmony.\nV Industrial Department Sewing, Dressmaking, Cooking, Do\nmestic Kconomy, Mechanical Arts, Agriculture.\nVI College of Liberal Arts Complete elective courses leading to\nthe several academic degrees.\nman for yome grave crime. Then\nit lynches a white man merely he\ncause it is impatient with his obsti\nnacy in standing upon hts constitu\ntional rights.\nThe relapse into barbrism is then\ncomplete. The afflicted communi\nty becomes such a one as Breathht\ncounty, Kentucky a community\nwhere there is no law but the rule\nof the strong hand-a community\nthat has degenerated into compara\ntive anarchy.\nSuch are the.rcsutsofthe lynch\ning habit."\nPresident Allen Honored,\nPres. Allen of Lincoln Inntitute return\ned from Boston Tuusday evening where\nhe attended iheN. K, A., and the faculty\naud teachers of tho summer school tend\nered him an Informal reception at tho\nPresidents mansion, About thirty guests\nwere present and a most enjoyable time\nwaB had. Many short tulis wero made,\nbut the most interesting one was that by\nPresident Alien, in which he told of his\ntrip to tho N. E. A , and the old historic\ncity of Boston, Refreshments wero eerv\ned and Prof. J. S, Moten presided as\ntoast-mastor for tho eveuiug.\nProfessional World-\nTho Thirteen Clock.\nAningoulous timekeeper arrangement\nexists today which was designed by that\nfamous duke of Bridgewater who laced\nSouth Lancashire with canals, nnd diod\nIQO years 11 go,\nThe duko wna a great stickler for punct\nuality, and howas annoyed thattutt work\nmen on his estato at Worsley did not\nreturn to work after dinner as promptly\nas they left off when the clock struck lii.\nWhen ho remonstrated he wni told that\nwhile the workmen always heard tho\nclock strike 12, they oflon failed to hear\ntbo single stroke.\nThe duko of Bridgewaterquhkly found\na remedy for this difliculty. He hd a\nclock mado that would strike thirteen\ntimes an hour after noon, and that clock\nproclaims 1 o\'clock with a baker\'s doeu\nof sonorous strokes to this day. Thc"i3\nplook" is ono of tho curiosities of Worn\nl.oy hall,\nWestminster Gazette.\nnotice!\nq. 0. commandeuy k t.,\nwill have a publio inotallation on\nnext Thursday night, July, 30.\nAdmission 10 ots.\nExpenses.\nBoard and room for four Weeks 18. CO\nTuition L\'.oo\nUso of laundry .60\n.Music, Instrumental or VoorJ for four\nweeks, two lessons per week 2 SO\nOno lesson por wek 1,M\nUso of Instrument per month - .50\nUso of typewriter in Coimnorcial de\npartment, per month .SO\nRooms are lighted, heated, furnished\nwith bedsteads, mattress, pillows, two\nquilts, mirrors, bowl pitcher and lamp.\nStudonts furnish for thotnselves, sheets\nand pillow casos, extra quilt and blsn\nkats, slop bucket, Imp chimneys, mat\nches, soap etu.\nA reduction of 50c per month is nmde\nfrom thu tuition of candidates for tho\nMinistry, and chlldron of Ministers,\nAll hills nre payable in advance tlio\nfirst of each school mouth. Money for\nstudents\' expenses should bo sent di\nrectly to tho President of the College .\nSond hy draft, P. O. order, express;\norder or icgistered letter to\nPres. I. L. Lowe,\nSodalia, Mo.\nWork and Self-help.\nA number of students boarding in the\nCollogoaro permitcd toearn some part\nof their exponsos by rork in the build\niiiK or on the grounds, provided they\nare willing nnd efficient. Liberal pay 1m\nallowed for all work done, but employ\nmcut will not bo continuod tothoio who.\nfnil,to do their work satisfactorily. Most\nstudents earn in this wayl\'J.OOamonthr\nsome earn larger amounts. Application,\nfor work should bo made to the Presi\ndent in advanco of coming.\nA large number of students find em\nployment in homes in tho city, suffl!\ndent to meet expensos of board audi\ntuition. Tho call for yonnsr ladies for\nthese positions is always greater tham\ntho supply. Application for such em\nployment should bo made in advance\nthrough tho President ot the College.\nAs far as possible wo endeavor to\nsafeguard thoso working in tho city,\nbut cannot he fully responsible for those\noutwdo the building. Only young men\nand women of established habsts and\neliaraoter can bo allowed this privilego\nof out sido residence.\nfn caso of minors this may be gran\nted only on tho written request of par\nents or guardians.\nism',
       u'page': u'',
       u'place': [u'Missouri--Pettis--Sedalia'],
       u'place_of_publication': u'Sedalia, Mo.',
       u'publisher': u'W.H. Huston',
       u'section_label': u'',
       u'sequence': 2,
       u'start_year': 1903,
       u'state': [u'Missouri'],
       u'subject': [u'African American newspapers--Missouri.',
        u'African American newspapers.--fast--(OCoLC)fst00799278',
        u'African Americans--Missouri--Sedalia--Newspapers.',
        u'African Americans.--fast--(OCoLC)fst00799558',
        u'Missouri--Pettis County.--fast--(OCoLC)fst01237700',
        u'Missouri--Sedalia.--fast--(OCoLC)fst01220815',
        u'Missouri.--fast--(OCoLC)fst01204724',
        u'Pettis County (Mo.)--Newspapers.',
        u'Sedalia (Mo.)--Newspapers.'],
       u'title': u'Sedalia weekly conservator.',
       u'title_normal': u'sedalia weekly conservator.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn89067598/1903-07-25/ed-1/seq-2.json'},
      {u'alt_title': [],
       u'batch': u'batch_mohi_dorothea_ver01',
       u'city': [u'Sedalia'],
       u'country': u'Missouri',
       u'county': [u'Pettis'],
       u'date': u'19030815',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1909,
       u'frequency': u'Weekly',
       u'id': u'/lccn/sn89067598/1903-08-15/ed-1/seq-1/',
       u'language': [u'English'],
       u'lccn': u'sn89067598',
       u'note': [u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.'],
       u'ocr_eng': u'BtMxa tUccli I Conscttwtotr\nVOL. i.\nSEDAUA, MISSOURI, SATURDAY, AUGUST 15, 1903.\nNO 16\nA. W. LLOYD, OF. ST. LOUIS, GRAND CHANCELLOR OF THE STATE OF MISSOURI.\nNineteenth Annual Sosslon\nK. of P, Lodge ofMissourl,\nConvonod in Moborly, Mo.,\nWodnosdoy, Aug. 0.\nThe Grain! Lodge of Missouri\nvras called toor\'ler yesterday morn,\nsng by A. W. Lloyd, C C. The\nGrand Court of CalanUic or Pythian\nsisters met with the Ginnd Lodge\nin open session. Mayor Dingle was\nto have delivered the welcome ad\ndress, but being unable to attend in\nperson, wai duly represented by\nMr IT. f. Tfflrris. who extended a\ncordial welcomcto the Grand Lodge\ndelegate in very fitting and friendly\nremarks, assuring assuring the vis\nitors that they were perfectly wcl.\ntome to the hospitalities of the good\nneonlc of Moberly. He commend\ned the race for the nwked progacss\nwhich it has made during the past\nquarter of a century of freedom.\nThe address was responded to by\nProf1 D. E.Gordon, of St. Louis in\n3 very appropriate speech.\nAddresses were made by several\n\'delegates ladies and gentlemen\n.Miss Arsania Williams, a delegate\nfrom St. Louis, made the address of\nthe morning in her greetings from\nthe -\'Sisters."\nMusic was furnished by a Pythi\nan Quartette from Paris. The G,\nC. appointed committee on creden\ntials and returns, and the time of\nmeeting, after which the Grand\nLodge adjourned until 7 o\'clock v.\n31. at which time the formal open\ning took place.\nThere are about 1 2 5 delegates and\nvisitors in attendance, among whom\nare the leading educators and pro\nfessional men and women of the\nrace in Missouri, The grand i.odge\nwill be in session three days.\ncept G. M. at A. and G. I. G. whose\nplaces wercfilled by the (1. C. After\nformal ceremonies at opening the\nGrand Lodge degree was conferred\non forty-one candidates by P. G. C,\n1). J. Caruthcrs. The Grand Lodge\nwas then called from labor to ease\nuntil 9 o\'clock this morning.\nNight Sosslon.\nThe night session yesterday open\ned formally at 7 o\'clock with a full\ndelegation from the various subor\ndinate lodges thru out the state\nAll the grand officers are present ex-\nTo-day\'s Session.\nPromptly at 9 o\'clock the Grand\nLodge was called from ease to labor\nby A. W. Loyd, G. G. Journal of\nfirst day\'s proceedings read and ap\nproved. The grand Lodge took up\nroutine busincs. Committee on Cre.\ndentials reported at the night ses\nsion seating one hundred delegates\nand Grand Lodge officers.\nThe first busincs of the morning\nsession was the reception of annual\naddresses and Grand Lodge officers\'\nreports. G. C. Lloyd read his auuu\nal message, which was an able, ichol\narly and comprehensive document;\nit was received and referred to the\nproper committee.\nNext followed thcreports of G. K.\nR. and S. and G. M. of Ex., show\ning total receipts for the year to be\n$1,486.80; disbursements $370.7";\nbalance in hands of G, M. of Ex,\nSi, 106. 10. The secrotary-trcasur-\nerof endowment has received to\ndate between $6,000 and $7,000,\nwith a balance on hands of $5,000.\nThe report of the Grand Lecturer\nwas a very insructive and interest\ning one and showed the subordinate\nlodges in excellent condition, the\nmajority of them having good bank\naccounts and many of them owning\ntheir own halls.\nThe report of committee on For\neign Correspondence by the chair\nman, B. J. Caruthers, showed that\nthe colored Knights ot Pythias is\npermanently founded in nearly ev\n2ry state in the union, S- A , Europe\nAsia, Africa, Australia and the Ha\nwailan Islands and is destined to\nlead all other organizatines among\nthe race. Missouri has had an in\ncrease of 10000 members this year.\nThe Grand Loi\'ge of the ladies\ndepartment of the order is also in\nsefsion here. The election of Grand\nofficers will take place Thursday.\nSeveral of the present officers will be\nallowed to succeed themselves.\n-Moberly Democrat\nRoosevelt Strongly\nDenounces lynching.\nIn a Letter tu dov. Durbin of Indiana\nthe President Commends the oc\ntlon taken against the recent ont\nbreak of lawlessness.\nOvsrr.K 13ay, N. Y., Aug. 9. In a\nletter, the publication of which was\nauthorized to day, President Roose\nyalt commends Gov. Durbin of In\ndinna for the attitude he assumed\nrecently respecting lynching. The\nPresident also embraces the oppor\ntnnity and mob violence generally\npointing out that mob violence is\nmerely one form of anarchy, and\nthat anarchy is the forerunner o\ntyranny. The President vigorous\nly urges that the penalty for crimes\nthat induce a resort to lynching\nshall be applied swiftly and surely\nbut by due process of the courts, so\nthat it may be demonstrated "that\nthe law is adequate to deal with\ncrime by freeing it from every ves\ntige of technicality and delay,1\'\nPresident Roosevelt\'s letter in\nfull to Gov, Durbin follows:\n"Oyster Hay, N. Y., Aug. 0, \'03\nMy Dear Governor Durbin\nPermit me to thank you as an A\nmerican citizen for the admirable\nway in which you have vindicated\nthe majesty of fhe law by your re\ncent action in reference to lynchings\nI feel, my dear sir, that you hav\nmade all men jour debtors, who be\nlieve, as all farseJng men must\nthat the well-being, indeed the very\nexistence of the republic depend\nupon the that spirit of orderly lib,\nerty under the law, which incom\npatible with mob violence as with\nany form of despotism. Of course\nmob violence U simply one form ot\nanarchy, and anarchy as is now, as\nit lias always have been, the hand\nmaiden and forerunner of tyranny.\nReflect onor on State.\n"I feel that you have not only\nreflected honor upon the state, which\nfor its good fortune, has you as its\nhief executive, but upon the whole\nnation. It is incumbent upon every\nman thruout this country not only\nto hold up your hands in the course\non have been following, but to\nshow his realization that the matter\ns one of vital concern tous all.\n\'Althoughtful men must feel the\ngravest alarm over the growth of\nlynching in this country, and espec\nially over the peculiarly hideous\nforms so often taken by mob vio\nlence in which colored men arc the\nvictims, on which occasions the\nmob seems to lay most weight, not\non the crime, but on the color of\nthe criminal. In a certain propor\ntion of these cases the lynched has\nbeen guilty of a crime beyond des\ncriptiona crime so horrible that\nas far as he hinuelf is concerned he\nhas forfieted the right to any kind\nof sympathy whatever. The feel\ning of all good citizens that such a\nhideous crime shall not be hideous\nly punished by mob violence is due\nnot in the least to sympathy for the\ncriminal, but to the very lively\nsense of the train of dreadful conse\nquences which follow t h e course\ntaken by the mob. exacting inhu\nman vengeance for an inhuman\nwrong. In such cases, moreover, it\nis well to remember that the crimi\nnal not merely sins against the hu\ninanity in an inexpiable and unpar\ndonablc fashion, but sins particular\nly against his own race, and does\nthem a wrong far greater than any\nwhite man can do them,\nColored People Paatlculrly\nConcerned,\n"Therefore, in such cases, the\ncolored people thruout th e land\nshould, in every possible way, show\ntheir belief that they, more than all\nothers in the community, are horri\nfied at the commission of such a\ncrime, and are peculiarly concerned\nin taking every possible measure to\nprevent its recurrence and to bring\nthe criminal to immediate justice.\nThe slightest lack of vigor, cither\nin denunciation of the crime, or in\nbring the criminal to justice is itself\nunpardonable.\n"Moreover, every effort should be\nmade, tinder the law, to expedite\nthe proceedings of justice in the\ncase of such nn awful crime. But it\ncannot be necessary in order to ac\ncomplish this to deprive any citizen\nof those fundamental rights to be\nheard in his own defense which are\nso dear to us all, and which lie at\nthe root of our liberty. It certainly\nought to be possible by the proper\nadministration of tho laws, to secure\nswift vengeance upon the criminal;\nand the best and immediate efforts\nof all legislators, judges and citizens\nshould bo addressed to securing\nsuch forms in our legal procedure\nas to leave no vestige of excuse for\nthose misguided men who under\ntake to reap vengeance thru violent\nmethods.\n"Men who have been guilty of a\ncrime like rape or murder, should\nbe visited with swift and certain\npunishment, and the just effort\nmade by the courts to protect them\nin their rights should uuder no cir\ncumstances be perverted into put\nting any mere technicality to avert\nor thwart their punishment. The\nsubstantial rights of a prisoner to a\nfair trial must, of course, begauran\nteed, as you have so Justly Insisted\nthat they should be; but, subject to\nthis gaurantec, the law must work\nswiftly and surely, and all the agents\nof the law should realize the wrong\nthey do when they permit justice to\nbe delayed or thwarted for techni\ncal or insufficient reasons We must\nshow that the law. is adequate to\ndeal with crime by freeing- it from\nevery vestige of technicality andde\nlay.\nInnocent Parties Suffor.\n"But the fullest recognition of\nthe horror of the crime, and the\nmost complete lack of sympathy\nwith the criminal, can not in the\nleast diminish our horror at the way\nin which it has become costumary\nto avenge these crimes, and at the\nconsequence that arc already pro\nceeding therefrom. It is, of courie\ninevitable that where vengeance is\ntaken by a mob it eluuld frequent\nly light on innocent poeple; and the\nwrong done in such n case to the\nindividual is one for which there is\nno remedy. But even where the\nreal criminal is reached, the wrong\ndone by tho mob itself is well-nigh\nas great. Especially is t h i s t r u e\nwhere the lynching is accompauied\nwith torture. There aro certain.\nhideous sights, which, when once\nseen, can never bo wholly erased\nfrom the mental retina. The mere\nfact of having seen them implies\ndegredutiou. Thisis a thousandfold\nstronger when, instead of merely\nseeing the deed, the man has par\nticipated in it. Whoever in any\npart of our country which has taken\npart in lawlessly putting to death a\ncriminal by the dreadful torture of\nfire, the participant must forever\nafter have the awful spectacle of his\nown hand work seared into his brain\naud soul. He can never again be\nthe same man.\nTorture a Prccedont that\nSpreads.\n"This matter of lynching would\nbe a terrible ttiing, even if it stop\nped with the lynching of men guil\nty Of the inhuman and hideous\ncrime 01 rape; but, as a matter of\nfact, lawlessness of this type never\ndoes stop, and never can stop, in\nsuch a fashion. Every violent man\ninthc community is encouraged by\nevery case of lynching in which the\nlynchers go unpunished to take the\nlaw iuto his own hands whenever it\nsuits his convenience. In tho same\nway, the use of torture by the mob\nin certain cases is sure to spread\nuntil it is applied more or less dis\ncriminate in other cases. The\nspirit of lawlessness grows with\nwhat it feeds on. and when mobs\nwith impunity lynch criminals for\none cause, they are certain to begin\nto lynch real or alleged criminals for\nother causes.\nIn the recent cases of lynching,\nover thee-fourths were not for rape\nat all, but for murder, attdmpted\nmurder, and even less hcniousoffeu\nces.\nCan Not Condono Mogul Offon\nces, "Moreover, the history of these\nrecent cases shows the awful fact\nthat when the minds of men are\nhabituated to the use of torture by\nlawless mobs to avenge crimes of\na peculiarly revolting description,\nother lawless bodies will torture in\norder to punish crimes of any ordi\nnary type.\n"Surely, no patriot can fail to see\nthe feariul brutalizatlou and debase\nment which the indulgence of such\na spirit and such practiies inevita\nbly portend, Surely, nil public men\n(Continued oi pige 4.)',
       u'page': u'',
       u'place': [u'Missouri--Pettis--Sedalia'],
       u'place_of_publication': u'Sedalia, Mo.',
       u'publisher': u'W.H. Huston',
       u'section_label': u'',
       u'sequence': 1,
       u'start_year': 1903,
       u'state': [u'Missouri'],
       u'subject': [u'African American newspapers--Missouri.',
        u'African American newspapers.--fast--(OCoLC)fst00799278',
        u'African Americans--Missouri--Sedalia--Newspapers.',
        u'African Americans.--fast--(OCoLC)fst00799558',
        u'Missouri--Pettis County.--fast--(OCoLC)fst01237700',
        u'Missouri--Sedalia.--fast--(OCoLC)fst01220815',
        u'Missouri.--fast--(OCoLC)fst01204724',
        u'Pettis County (Mo.)--Newspapers.',
        u'Sedalia (Mo.)--Newspapers.'],
       u'title': u'Sedalia weekly conservator.',
       u'title_normal': u'sedalia weekly conservator.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn89067598/1903-08-15/ed-1/seq-1.json'},
      {u'alt_title': [],
       u'batch': u'batch_lu_strongguy_ver01',
       u'city': [u'Breaux Bridge', u'Parks', u'Saint Martinville'],
       u'country': u'Louisiana',
       u'county': [u'Saint Martin', u'Saint Martin', u'Saint Martin'],
       u'date': u'19030718',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1948,
       u'frequency': u'Weekly',
       u'id': u'/lccn/sn88064454/1903-07-18/ed-1/seq-2/',
       u'language': [u'English'],
       u'lccn': u'sn88064454',
       u'note': [u'"Official journal ... towns of St. Martinville and Breaux Bridge, and village of Parks."',
        u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.'],
       u'ocr_eng': u'The WOIeekly Messenger, ma\nPUBLsHEID) XavuIY J\x7f(rTnDY A\nST. IARTINVILLE - LA. gt\nbei\nALBRkRT IilE\\V-\'FiU. - Pr..prietor.\nLAIZUIRE HIENVENI\'. - - Manager.\nGnbscriltion _ i.(OM " oa"; r inarindily in advance. mE\n\'ATURI)AY, J, t.1 8. 1903. ru\n(\'AC\';E OF LYNCHING. WE\nes5\nThe cry of stiop lynching of ne- th\ngroes in the solth which always\ncame from the northern states, is th\nnot as o1u11 now as it used to be, wa\nbecause the disease is spreading th\nin those states, and the disease is ev\nfrom the same cause, assaultingof th\nwhite womlen by negroes. ah\nThe lynching c(f negroes all over br\nthe country is always for the as- az\nsault of our white women, and as th\nlong as a negro will assault a cl\nwoman, he will and should be at\nlynched. The lynching of a negro a!\nis a lesser violation than the out- in\nraging of a white woman by a ne- bi\ngro brute. And, as a wise preach- at\ner recently said, lynching can be\nstopped by the negroes them- sE\nselves, by simply stopping their ft\nassault upon our women, and the w\npreaching of the humanitarians in ti\nfavor of the negro brutes, should it\nbe addressed to the negroes in- e(\nstead of the lynchers.\nThe lynching of the negro g\nbrutes, violent as the punishment, w\nhas not stopped their criminal as- v\nsault, and this week we learn that a\na mobtof tto hundred from Frank. d\nlin and the surrounding country\nhad gathered Sunday night in that n\ntown to make an assault upon the g\njail for the purpose of lynching o\nthe negro ex-convict Lovely, who o\nattaclkedl Mrs. Rene Hebert the h\nsame evening.\nHad it not been for the influ- I\nence of Sheriff Sanders, Senator a\nFoster and Judge:. Smith and Al\nlen, who pleaded with the infuria\nted crowd, to allow the law to take\nto course, the negro brute would\nhave been lynched. And who\nwould have been responsible for\nthis lynching, the negro himself.\nLynching can easily be stopped,\n. not by the lynchers, but by the t\nnegro brutes who assault white\nwomen.\n5IGHT WAS HER TERROR.\nounld cough nearly all night\nlon ,\' writes Mrs. Chas. Apple\ngate, of Alexandria, Ind., "and\ncould hardly get any sleep. I\nhad consumption so had that if I\nwalked a block I would cough\nfrightfully and spit blood, but,\nwhen all other me(ticines failed,\nthree $1,00 bottles of Dr. King\'s\nNew Discovery wholly cured me\nand I gained 58 pounds." It\'s ab\nsolutely guaranteed to cure Colds,\nCoughs, LaG(rippe, Bronchitie and\nall Throat and Lung Troubles.\nPrice 50c and 81,X). Trial bot\nties free af T\'. J. ilabe drugstore.\n:ASE BALL.\nWhat provedl to Ie the finest,\nmost intteresting antI exciting game\nof base bl ever play\'doI here, was\nthe one played last Sounday be- I\ntween Opelousas not lthe St. Mar\ntin Juniors.\nThe Opelousas club came hero\non Saturday and played a game\nthe same evening defeating the\nSt. Martin Juniors by a score of\n5 to 3.\nSunday evening an excursion\ntrain arrived from W\\ashington and\nOpelonsas, bringing about four\nhundred excursionists, who had\ncome to back the ()pelouss boys.\nSThe number of people on the\ngrounds was vely large and both\nteans had good supporters and\nsympathizers, and the excitement\nandi rooting was kept pretty lively.\nIn the first inning the Opelou\nus boys made three runs, and in\ndications after several innings were A\nmade was that they were sre\nwinners, but in the course of Ae\ngame the St Martins Juniors made Adle\nthree runs in one inning, the game As I\nbecame closer and more exciting, And\no0&\nthe game progressed with great Who\nexcitement, the St. Martin Juniors And\nmade more runs and reached the sat\nsame number as Opelousas, six nl\nruns, at this time the excitement Am\nwas at its hight, and it was inter\nesting to see the enthusianism of D\n- the supporters of both clubs. The\na When the clubs got to be even, And\ns they both determined that they I101\nwere going to defeat the other, and h\ng they did as fine playing as was b\ne ever seen on a diamond. When And\nf the ninth inning was finished a m\nshower came to spoil the game, And\nr but it lasted only a few minutes, I\ni- and the game was resumed, aid And\nis three innings were played by each soa\na club without a run being made, ret\ne and the playing was so exciting,\no and the players were so interested\nt- in the game, that they saw it would or ti\n,- be impossible to win the game,\ni- and it was declared a tie.\nie It was a game that was worth Di\n- seeing and our visitors who came mor\nir from Washington and Opelousas coun\nhe were fully repaid for their trip, ,s sellii\nin they will perhaps never see ps line,\nId interesting a game as the one.pleo- wate\n0- ed here last Sunday. woul\nAnother pleasing feature of this was\nro game, is that our visitors were not\nit, well entertained, a dance was gi- the i\ns- ven Saturday night in their honor, first\nat and they were well taken care of a go\nk. during the time they were here. in i\nry The umpire who was a gentle- engi\nat man from Opelousas decided the goo\nbe game all over to the satisfaction\nig of players and the public and not the\nbo one single objection was made to engi\nbe his rulings. witt\nThe following is the score: tire,\nu- Inns. .................. 12456789101112. well\nt. Martinville ........O 0 Iioo0i0 0 0 - s\nOr OpelouM ...............$21000000 0 0 0-.\nLI-- _that\na- BRUTALLY TORTURED. giDE\nke A case came to light that for der\npersistent and unmerciful torture hr\nhas perhaps never been equaled. tion\nor Joe Golobick of Colusa, Calif,\nwrites. "For 15 years I endured Thc\ndinsufferable pain from Rheuma.\nhe tism and nothing relieved me any\nte though I tried everything known.\nI came across Electric Bitters and\nit\'s the greatest medicine on earth vty\nfor that trouble. A few bottles of\nit completely relieved and cured\nme." Just as good for Liver and\nKidney troubles and general de\nbility. Only 50c. Satisfaction an\nguaranteed by T. J. Labbe Drug\ngist.\nut,\nPRETTY RECEPTION. we\nR\'s we\nLast Friday a very pretty re. H\nception was held at the home of\nd Mr. and Mrs. Albert Bienvenu in O\nhonor of their guest, Miss Lau\nArence Campbell of Lafayette. t\nA game of eachre was played, in\nre seventeen tables were occupied,\nand the contest was very interest- fire\ning. The result of the game was\nst, in Miss Laurence Campbell win\nrea\nme ning the first prize, a pretty, fan, -\nas Miss Louis Hitter the second prize\nbe- a set of shirt waist pine, Miss Ga\nar- briel Duerest, the booby, an alu- ple\nminum thimble, Mr. Eugene Gui- wit\nsre rard winning first prize, a white pu\nme cravat, Mr. Alfred Ortmire win- In\nthe ning second prize, a pair of suens- Nej\nof penders and Mr. Jack Fournet get- wil\nting the booby, a big eorn cob the\nion pipe. the\nud Nice cakes and refreshments\nor were served to the guests, after the\nad game of euchre. The young peo- Ju\nyp. pIe al enjoyed good music and\nthe sniging.\nth It was midnight when the party\nod broke up, and all seemed to have\nnt had a most enjoyable time.\n>u- --OATS! genuine black oats 46,\nin- bushels $2,50 at Bienvenu & Son. -\ne A Poem of St. Martin.\n. W.B.G.\ne (BY THE STARCH MAN.)\ne Adieo to old St. Martin\ne As I rode so fast away\nAnd that dear romatic spot I\'ll see an\nother day,\nWhere the boys they are so jolly\n\'s And the girls so lovely be.\ne I sat thinking of the place so fair be\nx neath the famous tree.\nIt Am far away from old Kentucky but\njust as happy am\nAs farther and farther I roam through\nDear old Louisiane\nThe mocking birds they sing by day.\n, And the moon it shines by night\nI love to live in Dixie laud it is my\nhearts delight.\nSo adieu to old St. Martin beside the\nbayou Teche.\nIn And all the kindness shown to me in\na my memory fresh,\ne And when I go to Old Kentucky,\nS I will tell the people there of the jolly\nboys of old St. Martin\nd And the pretty maiden fair,\nb 80o adieu to the land I\'ve seen and the\nb, reeting place of Evangeline.\nWritten by\nWillard Halsted Guepner.\nThe White Rabbit Starch Man,\n[ For the Messenger, Juno 6, I:114.\nTHE STEAM ENGINE.\nb During the past few days a ru\nr mor was circulating that the town\nas council contemplated the idea of\n0 selling the steam engine Evange\nps line, thinking, perhaps, that with\n4. water works, the steam engine\nwould not be of much use; but, this\ntis was an error, the steam engine will\nre not be sold, it will be kept, and\ngi. the idea is to always keep it in\nor, first class order, we expect to have\nof a good system of water works, but\nin certain emergency the steam\nle- engine may be called upon to do\nhe good work yet.\non We have the Teche passing in\niot the center of the town, and the\nto engine can be of good service, and\nwith both manner of righting the\ntire, we can feel that we will be\n2. well protected against fire.\n-\' We learn from insurance agents qs\ne."\nthat with water works, steam en\ngiDe, fire company, Hook and lad\nder company, and with two or\nthree hose companies, this town\nwould have first class fire protec\n. tion, and this will considerably\nreduce the rates of fire insurance.\nrThose who think we will not have\nany reduction are in error, as we\nme had some special agents here the\nn past week to look at some proper\nd ty, without making a general re\nvision of the tariff, and the reduc\nof tion was satisfactory.\nWe know of one property rated\nnd at 4,25 that was reduced to 2,75\ndo\nanother rated at 2,75 reduced to\n1,50 and several other similar ca\nug- sea.\nses.\nBut to obtain such reductions\nwe must have good tire conrpanies;\nwe have the Volunteer No 1, and\nHood and Ladder No 1, now we\nwould need at least three hose\no companies in the different sections\nof town, and we should also keep\nau- in good order our two hand pumps\nthat have done such good ser\\vice\nin the past, and whet we can show\nSto the insurance people that our\nest- ire protection is the best in tlhe\ncountry, we are certain to have\nn- reasonable insurance rates.\nEan,\nrize SPECIAL NOTICE.\na- Notice is herehy given to the peo\naln- ple of St. Martinville that all previes\nui- within the town limit be cleaned and\nbite put in good sanitary condition, with\nin, In ten days from this publication. P\nme Neglect to comply with this order,\np will cause the work to be done by\nob the town authorities at the cost of\nthe owners.\nSL. A. DeLaureal, M. D.\nthe Town Health Officer.\n>eo- June 27, \'9o3.\na L.BIENNVENU, \'\nrtv REAL ESTATE AGENT,\nSt. Martinville, La.\nWill take charge of renting or sell- \'\nin yonr property.\n5on. eeeses1nmenus# 1\nK. SCHWARTZ & Co.\nHave just received a large and well se\nlected stock of\nfor Men and Ladies. The; have them\nin all styles.\'\n) -We invite your inspection.1\n"In The Good Old Summer Times." l\nA ride on a good easy running\nSBicycle Is always agreeable....\nThe CRESCENT Bicycle\nMakes every road seem a\nBoulevard .....*... . \xb7 W\nIf you are thinking of buying a Bicycle, buy it from one who know\n. something about a bicycle, it is sometimes good to know where /\nto buy, especially when buying a bicycr.\nPrices: $25, $35, $40, $50, $60, $80.\nL. BIenvenua, Agent. *\nLucien Voorhies & co.,\nDealers In\nHARDWARE, Carriages, Saddlery,\nFurniture, Wheelwright Material.\nPLOWS and Plantation Supplies.\nd 4\nHEADQUARTERS\n.... FOR ....\nSGOOD WINIf, IIPORTED\nLIQUORS,\nAnd CHA IPAGNE.\nPhone65-3. L. F. GARY.\nSAll Lines complete, and our\nprices the lowest.\nSLinen Suits, Summer Goods all Grades.\nE J. B. FERRAN,\niedding Suits ledding Articles of All Kinds',
       u'page': u'',
       u'place': [u'Louisiana--Saint Martin--Breaux Bridge',
        u'Louisiana--Saint Martin--Parks',
        u'Louisiana--Saint Martin--Saint Martinville'],
       u'place_of_publication': u'St. Martinsville [i.e. St. Martinville] La.',
       u'publisher': u'Eastin & Bienvenu',
       u'section_label': u'',
       u'sequence': 2,
       u'start_year': 1886,
       u'state': [u'Louisiana', u'Louisiana', u'Louisiana'],
       u'subject': [u'Breaux Bridge (La.)--Newspapers.',
        u'Louisiana--Breaux Bridge.--fast--(OCoLC)fst01226258',
        u'Louisiana--Saint Martin Parish.--fast--(OCoLC)fst01220862',
        u'Louisiana--Saint Martinville.--fast--(OCoLC)fst01210549',
        u'Saint Martin Parish (La.)--Newspapers.',
        u'Saint Martinville (La.)--Newspapers.'],
       u'title': u'The weekly messenger.',
       u'title_normal': u'weekly messenger.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn88064454/1903-07-18/ed-1/seq-2.json'},
      {u'alt_title': [],
       u'batch': u'batch_wa_elm_ver01',
       u'city': [u'Seattle'],
       u'country': u'Washington',
       u'county': [u'King'],
       u'date': u'19200828',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1921,
       u'frequency': u'Weekly',
       u'id': u'/lccn/sn87093353/1920-08-28/ed-1/seq-3/',
       u'language': [u'English'],
       u'lccn': u'sn87093353',
       u'note': [u'"A publication of general information, but in the main voicing the sentiments of Colored Citizens."',
        u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.',
        u'Description based on: Vol. 2, no. 5 (July 14, 1917).',
        u'Volume numbering irregular.'],
       u'ocr_eng': u'Mrs. John P. Mapps of Spokane spent a\nfew days in the city the past week in the\nadvocacy of one of the many aspiring poli\nticians of the state, who is trying to con\nvince the dear people that he\'s alright.\nMrs. Mapps is a forceful talker and always\nmakes a good impression.\nEDITORIAL PARAGRAPHS\nJim Cox got pinched for speeding and he\nwill get skinned for running.\n"Brew at home and raise hell abroad,"\nwill be the outcome of Bob Saunders\' wet\ndecision.\nRev. Allen of Taeoma visited Seattle last\nThursday evening and talked with the\npoliticians.\nTennessee\'s legislature gave the women\nthe full right of suffrage in this country,\nbut. made a damphool of itself afterwards.\nEvidently the Concrete Trust controlled\nthe late Good Roads Convention as it went\non record endorsing the Carlyon Road Bill.\nMayor Caldwell may fly to Everett and\neven to Olympia, but it will be a cold day\nin August when he flies into the governor\'s\noffice as the state\'s chief executive.\nNo one has as yet aeused William Howard\nTaft of having wheels in his head, and yet\nwe verily believe that his head is so full\nof Avheels that he talks without effort.\nA Xegro is threatening to break into\nCongress from a St. Louis district; yea, he\nis not only threatening, but is taking long\nstrides towards putting his threats into\neffect.\nAn unusual amount of automobile acci\ndents have occurred in and about Seattle\nduring the past ten days and we suspect\nBob Saunders\' "home brew" decision lias\nhad much to do with it.\nThe almost violent shake up in the\nPuget Sound Conference of the A. M. E.\nchurch seems to indicate that Bishop Foun\ntain was of the opinion that there had been\nconsiderable sleeping\' at the switch.\nIt\'s refreshing to read of the opening\nexercises of the Mt. Zion Baptist church\nof this city and while it does not mean the\ncrowning climax of that arduous strui?<?le,\nthe pastor and the members of that con\nFRED C. BROWN\nCandidate for Prosecuting 1 Attorney\ngregation have gone through, yet it does\nmean the beginning of the end.\nThe next legislature of this state should\nmake it a felony for any one to drive an\nautomobile on any street or public highway\nif said driver is under the influence of\neither strong drink or any kind of nar\ncotic. The speed fiend is dangerous enough,\nbut the drunken fiend is a devil undone.\nHowever unfortunate it may be for the\nentire colored voting population in this\ncountry to be forced to vote for one party,\nyet under the circumstances they have to\nand the colored voter that votes the Demo\ncratic ticket this year is as mad as a\nMarch hare and is a fit subject for a mad\nhouse.\nDELUTH\nA splendid example of branch activity and\ncooperation wtih the National Office is given\nin the part played by the St. Paul and\nMinneapolis Branches of the N. A. A. C. P.\nin investigating the recent lynchings at Du\nluth, Minn.\nOn June 16 the press of the country re\nported the lynching of three Negroes at\nDuluth, Minnesota, by a mob of 5,000 per\nsons, who overpowered the police and fire\nmen, took possession of police headquarters\nand seized the Negroes along with three\nothers who were releasd aftr a mock trial\nwas held. All were being held in connec\ntion with an attack said to have been made\nupon a white girl.\nThe National Headquarters of the N. A.\nA. C. P. immediately wired its St. Paul and\nMinneapolis Branches asking that an in\nvestigation be made as to the facts in the\ncase. Governor J. A. Burnquist of Minne\nsota is President of the St. Paul Branch,\nand a direct appeal for the apprehension\nand punishment of the lynchers was made\nto him as Governor of the s tate.\nEach of the branches sent investigators\nto the scene of the lynching and full re\nports have been rendered to the National\nOffice. Among other things, one of the in\nvestigators was able to secure affidavits\nfrom fourteen other colored men who were\nbeing held in jail on the same charge for\nwhich the three were lynchd. This inves\ntigator says, "It is not certain that any of\nthe Negroes lynched or those now under\narrest are guilty of the crime charged. It\nis not even certain that the girl was as\nsaulted." Of the fourteen Negroes being\nheld he says, "Each and every one of them\nprotests his innocence. None of them knew\nany of the men lynched. None of them saw\nthe crime committed, if one was committed.\nThese men are extremely ignorant and 1 do\nnot believe they could tell a lie so consis\ntently that I could not catch them in it.\nThe reports from both branches show\nthat there was greal negligence on the part\nof police officials. They report that early\nin the afternoon of the date of the lynching\ntlie police department was notified by a\nresponsible party that a mob bent on lynch\ning the Negroes was Forming, and that the\nCommissioner of Safety made no substantial\neffort to prevent the Lynching. The report\nsays: "For two hours or more immediately\npreceding the lynching, trucks loaded with\nruffians ran up and down the main streets\npast the Central station with ropes dragging\nbehind the trucks. The ruffians who occu\npied the trucks would stop and make\nspeeches, telling the crowds that the girl\nwas dying, that they were going up to the\npolice station and hang the Negroes and\nthat they invited the crowds to join them.\nTn the trucks they had ropes, hammers, steel\nsaws to cut the bars and large timbers to\njam their way to the prisoners. In the\nmeantime the Commissioner was at the sta\ntion giving strict orders that under no cir\ncumstances were the police to use firearms\nfor fear blood would be shed."\nNot only did the Minneapolis Branch\ninvestigate the facts leading up to the lynch\ning, but while on the ground the President\nof the Branch, who himself made the inves\ntigation, assisted in the organization of the\nDuluth Branch of the National Association\nfor the Advancement of Colored People.\nA recent communication from Governor\nBurnquist gives the encouraging informa\ntion that twelve persona have been indicted\nand arrested on first degree, murder charges\non account of alleged participation in the\nlynchings. The grand jury has not yet com\npleted its investigation. The Governor as\nsures the Association that the State de\npartments are cooperating in every way\npossible.\u2014The Crisis.\nIN THE SUPERIOR COURT OF THE STATE OF\nWashington in and for King County.\nIn the Matter of the Estate of Robert M. McMann,\nDeceased \u2014N.. 27794. Notice to Creditors.\nNotice is hereby given that the undersigned,\nMabel Akers, has been appointed executrix of the\nestate of said deceased, and has qualified as such\nexecutrix, and all creditors of the deceased and all\npersons having claims against the deceased are re\nquired to serve the same on the said executrix or\nher attorneys. Sullivan & Christian, 1507 National\nRealty building, Tacoma, Washington, and file\nwith the Clerk of the above Court, together with\nthe proof of such service within six months after\nthe date of the first publication of this notice,\nto-wit: within six months after the 31st day of\nJuly, 1920.\nMAP.EL, AKERS.\nExecutrix of the Estate of Robert M. McMann,\nDeceased.\nSULJ.IVAN & CHRISTIAN,\nAttorneys for Executrix,\n1507 National Realty P.ldg., Tacoma, Wash.\nJuly 31-August 28, 1920\nTHOMAS DOBSON\nCandidate for County Commissioner',
       u'page': u'',
       u'place': [u'Washington--King--Seattle'],
       u'place_of_publication': u'Seattle, Wash.',
       u'publisher': u'H.R. Cayton',
       u'section_label': u'',
       u'sequence': 3,
       u'start_year': 1916,
       u'state': [u'Washington'],
       u'subject': [u'African Americans--Washington (State)--Seattle--Newspapers.',
        u'African Americans.--fast--(OCoLC)fst00799558',
        u'Seattle (Wash.)--Newspapers.',
        u'Washington (State)--Seattle.--fast--(OCoLC)fst01204940'],
       u'title': u"Cayton's weekly.",
       u'title_normal': u"cayton's weekly.",
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn87093353/1920-08-28/ed-1/seq-3.json'},
      {u'alt_title': [u'National Afro-American newspaper'],
       u'batch': u'batch_mnhi_funkley_ver02',
       u'city': [u'Chicago', u'Minneapolis', u'Saint Paul'],
       u'country': u'Minnesota',
       u'county': [u'Cook', u'Hennepin', u'Ramsey'],
       u'date': u'19221230',
       u'edition': None,
       u'edition_label': u'',
       u'end_year': 1999,
       u'frequency': u'Weekly',
       u'id': u'/lccn/sn83016810/1922-12-30/ed-1/seq-2/',
       u'language': [u'English'],
       u'lccn': u'sn83016810',
       u'note': [u'Archived issues are available in digital format as part of the Library of Congress Chronicling America online collection.',
        u'Available on microfilm from the Minnesota Historical Society, and the Library of Congress, Photoduplication Service.',
        u'Description based on: Vol. 4, no. 39 (Feb. 23, 1889).',
        u'Latest issue consulted: Eighth year (Sept. 10, 1892).',
        u'Merged with: Northwestern bulletin (Saint Paul, Minn.); to form: Northwestern bulletin-appeal.',
        u'Published at Saint Paul, Minn. and Minneapolis, Minn., Jan. 4, 1890-<Nov. 24, 1923>.'],
       u'ocr_eng': u'V-,\n,i\nSUB APPEAL\nAN AMERICAN NEWSPAPER\nISSUED WEEKLY\nJ. .ADAMS, EDITOR AND PUBLISHER\ni\n8T. PAUL OFFICE\nNo. 301-2 Court Block, 24 E. 4th at\nM. 4. ADAMS, Manager.\nPHONE: N. W. CEDAR 5649.\nMINNEAPOLIS OFFICE\nNo. 2812 Tenth Avenue South\nJ. N. BBULLBRS, Manager.\natered at the Peatofllce In St. Pawl.\nMlaaemta, a aeeond-claaa mall\ntatter, Jaae 6, 1885, under\nAct Canmreaa,\nMarch 51. 1A70.\nTERMS, STRICTLY IN ADVANCE:\nINflrLE COPY, One Year $2.40\n81NOLE COPY, Six Months 1.25\nINGLE COPY, Three Months.. .65\nSATURDAY, DECEMBER 30, 1922.\nHAPPY NEW YEAR\nBefore another issue appears the\nyear 1923 will be on, and so THE\nAPPEAL takes this time to wish its\nreaders and contemporaries a happy\nand prosperous New Year.\nWith this issue, THE APPEAL\ncloses its thirty-eighth year of\nservice to the colored population of\nthe Twin Cities. The events of those\nyears have been faithfully mirrored\nin its columns the wrongs and in\njustices of those years have been\nfearlessly and vigorously denounced\nin its editorials. Always has it en\ndeavored to serve the best interests\nof this community and of the colored\npeople in general.\nInto the thirty-ninth year of its\nexistence THE APPEAL is prepared\nto put even greater effort and service\nto the end that the people may pros\nper and progress and continue to\nevidence appreciation and support.\nMR. HOWARD\'S REPLY\nThis week THE APPEAL publishes\nPerry Howard\'s reply to certain\ncharges in its entirety only because\nthe charges had been so published.\nHis attack on the National Associa\ntion heads we leave to that body, ex\ncept to say in passing that any pub\nlic speaker must see in Mr. Howard\'s\n"case" the old debate tricks of argu\nment ad hominem and ad populum.\nThe real issue has scarcely been\ntouched.\nIt is in his argument on the sup\nport and defeat of so-called friends\nof the race that Mr. Howard slips\ninto a fallacy that is not so obvious\nas the one name above. He, with\nmany others, assumes that anybody\nbearing a Republican label is by that\ntoken a "friend" of the colored man.\nUpon this false premise he builds the\nargument that a non-political organ\nization which fights Republican oppo\nnents of its policies becomes political\nand democratic! In the heat of his\nletter the defeat of seven Republi\ncan "friends" is laid to the N. A.\nA. C. P. This is misleading in the\nextreme. Of at least two of the de\nfeated ones, Townsend and Kellogg,\neveryone knows the facts. Townsend\nwas beaten on the Newberry ques\ntion and Kellogg was defeated be\ncause of Newberryism and corpora\ntion legislation.\nOne of these days Mr. Howard will\nwake up and see that what the party\ndid in 1865 carries little or no weight\nwith the voter of 1922. We hope he\nwill live to see the day when black\nvoters will repudiate parties and\nmen who pass out one or two $5,000\njobs and hoodwink the mass of col\nored citizens out of money and rights\nby maladministration of government\nbusiness.\nA HORSE O ANOTHER COLOR\nThe double mob murder at Mer\nRouge, La. is a "ghastly incident"\nand an "atrocity" according to the\neditorial writers of the St. Paul Dis\npatch. It is gratifying to know\ndefinitely what the Dispatch thinks\nof lynching, but it is rather odd that\nits writers should have waited this\nlong to express themselves. The\nmystery is now solved.\nCompared to any one of a dozen\nlynchings readily called to mind the\nMer Rouge episode fails to measure\nup as an "atrocity." Georgia, Ala\nbama and Texas mobs have set the\natricity standard too high. The two\nmen were merely murdered and\nthrown into a lake. Hundreds of\ncolored men have been shot, burned\nand disembowelled without eliciting\na peep from the Dispatch. When two\ncolored men were lynched within a\nweek in Florida the Dispatch did not\nsolemnly affirm that Florida "will be\nin disgrace^ with the sisterhood of\nstates until It. vindicates justice and\nitself."\nPerhaps it will be contended that\nthe nature of the colored men\'s\nalleged crimes makes the difference\nin comment. But nofrom its pin\nnacle of righteousness the Dispatcji\nmakes a pronouncement that elimi\nnates this excuse. It says: "The\ncharacter of the offense charged\nis, with the question of guilt or in\nnocence, entirely outside considera\ntion government under law has\nbeen challengeid."\nThis leaves but one cause for the\nsudden indignation of our big daily,\nthe victims were white. The paper\neven ventures that if Louisiana fails\nin this case there will be a demand\nthat the central government take a\nhand in the exercise of the police\npower of the states. And this, too,\nfollowing an editorial denouncing the\nDyer bill as an usurper of the police\npower of the states!\nOpinion of this sort is of little\nvalue except as filler. If the Dis\npatch, or any other paper wishes to\nbe believed sincere by thoughtful\nreaders, black and white, it will\ncondemn all lynchings, regardless of\nthe color of the victim, for the sole\nreason that all mobs challenge gov\nernment under law and "set the reign\nof law at naught."\nWizard Evans of the Koo Koos\nsays, "it makes little or no difference\nwhat "Gov. Alien says and what he\nmeans even less." Alexander Howatt,\nnow enjoying the hospitality of a\nKansas jail, once had the same idea\nas Wiz Evans.\nPerry Howard says he read "with\namusement his letter to Senator Du\npont. That\'s just how the darned\nthing struck us when we read it.\nPittsburgh Courier.\nLYNCHING EVERY\n48 HOURS AFTER\nDYER BILLDEATH\nNational Association Places Blame For\nMob Outbreaks Squarely on\nSenators\nThe National Asosciation for the\nAdvancement of Colored People, 70\nFifth avenue, New York, today made\npublic an "open letter to every Unit\ned States senator," setting forth that\nfour lynchings have occurred in the\neight days from Monday, December\n4, when the Senate dropped the Dyer\nanti-lynching bill, to December 12.\nThe letter charges the Southern\nsenators with being directly responsi\nble for the "outbreak of barbarism,\nanarchy and degenerate bestiality"\nwhich followed the abandonment of\nthe Dyer anti-ly|iching bill, and ex\npresses the hope that there will prove\nto be sufficient statesmanship in the\ncongress of the United States to cope\nwith this threat to civilization. The\nletter follows:\nAn Open Letter to Every Senator of\nthe United States.\nDecember 13, 1922.\nSir: From December 4, the day\nthe United States Senate abandoned\nthe Dyer anti-lynching bill, to Decem\nber 12 there have been four lynch\nings in this country, one for each two\ndays, one of the victims being pub\nlicly tortured and burned at the\nstake.\nThis outbreak of barbarism, an\narchy and degenerate bestiality and\nthe blood of the victims rest upon the\nheads of those Southern senators who\nhave obstructed even, discussion of\nthe measure designed to remedy this\nvery condition. And the responsi\nbility rests equally with the Republi\ncan majority, who surrendered with\nhardly a struggle to the lynching tac\ntics of the Democrats.\nNew York, N. Y., Dec. 29.The\nHerald of this city, like the Tribune\nof Chicago, seems to have discovered\nthat the red rays of Moscow are be\nginning to shine on the race in the\nUnited States. The paper declares\nthat the bolsheviki, ever on the alert\nto plant the seeds of revolution in\nfertile spil, have seized on the race\nin America.\nThe Herald continues by claiming\nthat Moscow tried to incite the race\nto rebellion. The only thing that\nsaved the day, according to his pa\nper, was tl|e race\'s dislike for for\neigners.\n"Had the black man," complains\nthe Herald, "of the United States\nbeen as. inflammable as the Russian\nreds thought he was blood would\nhave run freely in many communi\nties, for there would have been race\nwar."\nThe Herald says that after the race\nriots of 1919 thg reds rushed secret\nagents into this country and the\nAfrican Blood Brotherhood was\nformed. The Herald avers further\nthat the A. B. B. is a bolshevist or\nganization with all the bolshevist\ntrappings and propaganda. What is\nclaimed to be the program of the A.\nB. B. is quoted at great length. The\narticle closes with a statement that\na number of educated race men have\njoined the movement and have been\npreaching violence ever since. The\nHerald concludes:\n"During the first year of the or\nganized activities by the communists\namong the Negroes a number of Edu\ncated Negroes, most of them from\nHarvard, were found sufficiently dis\ncontented and sufficiently excited to\nmake good communists. They were\nenlisted in the work and from that\ntime have been preaching violence on\nevery occasion to the Negro element."\nStill Hope For Dyer Bill\nWashington, C., Dec. 29.Presi-\ndent Harding still hopes to do some\nthing for the Dyer Bill, according to\nthe following letter sent to William\nMonroe Trotter, of Boston, by George\nB. Christian, secretary to the Presi\ndent,, The letter reads:\n"I have before me your message of\nDecember first, to the President. As\nyou know, the President has made\nevery effort possible in behalf of this\nmeasure. Its present status is not a\npromising one, but it is still .hoped\nthat something may be accomplished\nalong this line inltihe^near future."\nMississipptan, in Long Letter, Loses\nBitter Attack on Du Bois, John\nson and N. A. A. C. P.\nWashington, D. C, Dec. 29.I have\nread with amazement the release\nfrom the N. A. A. C. P., under date\nof December 8, charging that I de\nfeated the anti-lynching bill.\nI want to denounce the perfidy of\nthose responsible for this release for\ndisjointing my letter and Jeaving out\ncertain parts, which was intended to\nmake it distasteful. No honorable\nman would do this and I challenge\nthem to publish the letter in its en\ntirety.\nFirst. I want to say that any\nstatement or intimation that I in any\nwise, directly or indirectly, defeated\nor contributed to the defeat of the\nanti-lynching bill is a lie, and there\nis not a sane man in the country who\nRelieves this, including the man or\nmen who released the article so\ncharging.\nSecond. The reference to the Li\nerian loan is innuendo, and, there\nfore, unworthy of notice.\nIt is well known by the author of\n^his release that for two years I\nhave worked incessantly, together\nwith other friends, to get an anti\nlynching bill on the federal statutes\nand instead of appreciation and co\nperation on the part of Mr. James\nWeldon Johnson, our efforts were re\nsented by him because it was inter\nfering with his one aim, and that is\nto exploit his importance and keep in\nthe limelight. I have every reason to\nbelieve that he would prefer that the\nbill be not passed if it will in any\nwise dim the luster of his vanity or\nself-exploitation.\nOur activities for the anti-lynch\ning bill, or any other movement, have\nbeen consistently opposed by Mr.\nXchnson and Mr. DuBois with his\n"Crisis," which he has freely used to\nbelittle and minify our activities in\nevery way.\nI cannot see what places upon\nMessVs. Johnson and DuBois the re\nsponsibility of passing the anti\nlynching bill unless it be a popular\ncard to \'fill the coffers of the N. A.\nA. C. P., and give them increased\nsalaries for they are the most lux\nuriously salaried gentlemen who are\ndoing charity work, whom I know.\nknow of no colored man perhaps\nin the country who would not be\n?la or delighted to have their places,\nwhich require no sacrifice, but which,\non. the other hand, furnish every fa\ncility for ease and comfortMr.\nJohnston to sit in the gallery of the\nSenate and look on at the activities,\nprepared at any moment, to step\n^own one flight and indulge, in the\nluxurious pastime of eating canvas\nback duck or quail on toast, which\nMr. DuBois strokes his Chesterfel\ndian beard and incidentally takes his\n"Crisis" in hand and fires a desul\ntory shot at a supposed enemy. Of\ncourse, their release will fce pub\nlished in "The. Crisis," but no word\nin response will ever get on the pages\nof Mr. DuBois\' magazine.\nI have no word of censure for the\nSues For $10,000\nSettles For $10\nJacksonville, Fla.. Dec 29.The\nmother of little Arthur Mack, aged\n8 years, who was run over by L. H.\nHodge\'s automobile last November,\nmust have needed Christmas money\nvery badly when she isv alleged to\nhave settled a $10,000 damage suit\nout of court for the paltry sum of\n$10.\nHodge, of Scrivens, GaV was on his\nway to J5aytona, Ga., on business it\nis alleged when he ran over the small\nlad at a curve on the state highway\nnear here. He went to the magis-\n^w\nPerry Howard Denies Charge\nThat Blocked Dyer Bill\nNational Association for the Ad\nvancement of Colored People per se\nand its earnest members who mean\nwell and are doing their best but I\ndo object to these autocrats exploit\ning themselves at the expense of the\nassociation and. seeking to belittle\nthose who do not pay homage to\nthem.\nIt has been presumed from the be\nginning that the National Association\nfor the Advancement of Colored Peo\nple was a non-political organization.\nBut true to my political faith I can\nbut resent the present leaders in\nvariably lining up otherwise and mis\nleading their! followers into killing\noff some of the few friends we had\nin the Senate. I resent this with all\nmy soul. I especially resent their\naction towards Senator Du Pont, who\nwas defeated through the activities of\nthe N. A. A. ,C. P. and whose friend\nship for and fidelity to us have never\nbeen questioned, and in his stead they\nelected a. senator who already has\nvoted with the Democrats, on every\nroll call where any matter of inter\nest to the Negro was a stake.\nWhen the special session of con\ngress was convened there had been\ndefeated Senators Du Pont, Freling\nhuysen, Townsend, Kellogg, New\nFrance and .Calder. This gives us\nseven less votes for the Dyer bill.\nThen, may I ask, by what process\nof reasoning can we conclude that\nwe are expediting the passage of the\nanti-lynching bill by defeating those\nwho would vote for it and electing\nhostile ones in their stead?\nI have no apology to make for\nstanding by, the men of my party\nwho have always stood for those\nthings which will benefit our group.\nWhat I have done and am trying to\ndo has been at a sacrifice but I chal\nlenge any leader of the N. A. A. C.\nP. to show one thing he has done\nwithout reward or the hope of re\nward in fabulous salaries.\nThe public will well remember that\nwhen I came to Washington Mr. Du\nBois with his "Crisis," without in\nvestigation or otherwise, undertook\nto. minify the importance of mv po\nsition, forgetting that he had.humi\nliated the association by wearing out\nthe back steps of Woodrow Wilson\nseeking a captaincynot to fight,\nbut to join the other dilettantes in\nthe intelligence bureaunot to men\ntion his unfortunate "Close Ranks"\narticle.\nMay I ask what credit does Mr.\nJohnson give-to the struggling col\nored newspaoers throughout the\ncountry, which, in season and out\nlabored for an anti-lynching bill?\nTn face of the fact that these papers\nhave worked in season and out for\nthe passage of this bill, when the\nN.. A. A. C. ,P. had $6,000 to spend\nfor advertising, I should certainly\nnever have bpen guilty\'of sending it\ndown to The Atlanta Constitution\nand other white dailies which did not\nneed it. when some of our press boys\nare crying for bread.\nCertainlv it was not right for nly\nTriwid, Walter White of the N. A, A.\nC. P.. through\' the influence of my\nfriend Bob. Nelson, whose paper\nSenator Du Pont says he has helped\ntrate\'s office.jand put up $100 forfeit\nfor his appearance at the hearing and\nwas released to continue his journey.\nThe accident occured on Nevember 14.\nAt the hearing last Tuesday^ after\nnoon before Justice of the Peace\nGreenberg, Mr. Hodge was exoner\nated on the charge of careless driving.\nIt was stated that the mother of the\nboy had entered civil suit for $10,000\ndamages against Hodge. The matter\nwas said to have been settled $ut of\nthe court when Hodge and his at\ntorneys toJdLthe woman, how much\ntrouble itwejfld be to conduct a case\nof this kind and the enormous cost\ninvolved and that she the money\nfor competent lawyers, would have\nto spend a lot of money for competent*\nlawyers, who would finally get most\nor: practically all of the best thing\nfor. her to d$ Would be to settle the\ncase out of court and get it without\nto the tune of $15,000 to keep it in\nexistence, to import Democratic lead\ners to Wilmington to defeat Senator\nDu Pont.\nI said no word for Layton, who\nvoted against the anti-lynching bill.\nBut any thinking member of ouf\ngroup would a thousand times pre\nfer to have Layton back in the House\nfor two years and make certain of\nDu Pont in the Senate for six years\nwhen we know that Du Pont has\nstood pat on every roll call where\nthe interest of the race was at stake,\ninstead of having in his stead a man\nwho can be depended upon to vote\nagainst us on every roll call despite\nhis protestations of what he would\ndo if elected.\nI feel that Jim ought to go to\nthese Democrats, whom they helped\nto elect, and ask them to help pass\nthe anti-lynching bills since they have\nembarrassed us Republicans who\nwere doing the best we could. I\nthink that I am in position to say\nthat the bill would have fared far\nbetter if there had been less of John\nson and his bunglesome tactics.\nJohnson and others used the peo\nple\'s money lavishly under the assur\nance that this bill would pass, and,\nupon its failure, he seeks a "goat."\nInstead of contributing to the de\nfeat of this bill, I was the author of\nthe Galuv bill, introduced by Con\ngressman Gahn of Cleveland, Ohio,\nto whom I was introduced by Mr. N.\nD. Brascher, and had the Gahn bill\nintroduced with a view to forcing the\nproponents of the Dyer bill to en\ncouch certain fundamental features\ninto their bill to give it teeth. Mr.\nDyer accepted these when we went\nbefore the judiciary committee of the\nHouse for a hearing.\n"Jim" further knows that when\nthe sixty-seventh congress recessed\nin September, on the last dav there\nof he met Henry Lincoln "Johnson\nand myself in Senator Lodge\'s office\nwhere we -were all hustling and\nscurrying trying to get action on this\nbill before adjournment. And it was\nSenators Du Pont. France, Lodge,\nNew. and others who were busy on\nthis proposition.\n"Jim" knew all this when he let\nloose this diabolical and malicious\nrelease, which was solely for dis\ncrediting my friends and me and\nbolstering up himself and when he\nsays that there are astounding-reve\nlations of my activities to defeat this\nbill he takes himself out of the pale\nand category of decent men in ut\ntering such an infamous falsehood,\nand I defy him to -produce one scin\ntilla of proof to sustain his malicious\nallegations.\nThe only difference betwen "Jim"\nand me on the anti-lynching bill is\nthat he is growing rich while I am\ngrowing poorer in trying to put it\nthrough.\nRegardless of whether in the\nchange of changes bad persons, la\nbeled Republicans, crop into office,\nstill I am a Republican without\napologies, and if t&is be treason,\nthen make the most of it.\nPERRY W. HOWARD.\ntrouble and noteriety. So the woman\nsaid that $10 would suit her since it\nwould put an end to all the bother and\ntrouble.\nBLIND MAN MADE\nWEALTHY BY OIL LANDS\nWright Rock of Louisiana Becomes\nNear Millionaire Over\nSheveport, La., Dec 29.Sitting\nin the blackness of eternal night, for\nmany years ago he lost the sight of\nboth, eyes Wright, Rock of De Soto\nparish, 75 years old, on whose land\noil was discovered recently, takes\ncalmly the sudden change in his\nfortune which has elevated him to\nthe ranks of the financially independ\nend. \'j\n"Uncle Wright" is of the old school\nand hardly realizes that the two Rock\nwells in section 23-12-11 completed\nas large oil wells on his farm by A.\nH. Tarver, white, are bringing him\ndaily more money than he ever hoped\nto make as a return from\' the truck\nof his little farm. He is the father\nof fifteen children, all but two of\nwhom long ago left the paternal roof.\n"I guess they\'ll come back now," he\nsaid simply.\nWhen informed of his wealth upon\ncompletion of the first well, and asked\nwhat he was going to da with the\nmoney, the old man replied: "Well,\nI guess I\'ll just buy me a few clothes\nand sumpthin\' for to eat."\nBesides the original lease money\nwhich he received for the eighty-acre\ntract on which the Tarver wells are\nlocated, the aged ex-slave receives a\none-eighth royalty from the produc\ntion and other wells will be drilled,\nit is said. The wells already produc\ning are making an aggregate of\n2,500 barrels.\nKU KLUX CALLED\nINSULTTO WHITES\nWhite Southern Edlitor Says Soldier\nDead in France Vindicate Ameri\ncanism of Colored Man.\nThe Ku Klux Klan is being repudi\nated in the South, according to\nextracts from an editorial of the\nGreensboro, North Carolina, Daily\nNews, of December 18th, made public\nby the National Association for the\nAdvancement of Colored People, 70\nFifth avenue, New York.\n"The existence of the Ku Klux is\nan insult to the white race," says the\neditorial. "It is an admission that\nwe cannot defend ourselves except by\na coward\'s weapons.\n"And when that order preaches the\npoisonous doctrine that the colored\npeople are not entitled to the rights of\na fair trial in open court and to the\nequal protection of the laws, because\nhe is not 100 per cent American, we\nthink of those huddled crosses a the\nmoonlight by one of the rivers of\nFrance. And when officials of the\ngovernment ally themselves with that\norder, nay, when a judge on the bench\ncharged with the sacred administra\ntion of justice, will not deny that he\nis the head of that reptilian order, we\nthink of those graves in France. And\nour impulse is not one of pity for the\ncolored people, for the colored people\nhave vindicated themselves. Our\nfear is for the nation that threatens\nto abandon its own dead. God help a\ncountry that could be so vile!"\nChicago Woman Named\nAssistant Prosecutor\nChicago, HI., Dec. 29.Chicago\nmay point to itself with pride as\nthe city having the first colored\nwoman as assistant prosecuting at\ntorney. Miss Violette N. Anderson\nhas received the appointment, to take\neffect January 1.\nThe honor to Miss Anderson comes\nin direct line with the pronounced\npart women of both races are playing\nin political and civic affairs through\nout the country. One woman has\nbeen elected to a state supreme court,\nanother has been appointed a federal\ndistrict attorney by Attorney General\nDaugherty. In the recent elections a\ncolored woman was a candidate on\nthe Farmer-Labor ticket for secre\ntary of state, and just last week a\nwoman who campaigned only two\ndays came within six votes of being\nelected an alderman in the city of\nMound Bayou, Miss.\nMiss Anderson conducted a court\nreporting agency for a number of\nvears. This work took her into va\nrious courts of the state. She began\nthe practice of law in 1919. She is\ntoday the first and only woman of\nher race in active practice before the\nIllinois bar.\nMany Colored People\nIn Missouri Industries\nJefferson City, Mo., Dec. 29.A\nsurvey of colored employment condi\ntions made by Secretary R. S. Cobb\nof the Missouri Industrial Commis\nsion, now being completed shows that\n86,780 men and women are employed\nin various industries, representing\napproximately 48 per cent of the to\ntal 178,241 colored people of the\nstate of the number 57,984 are men\nand 28,796 are women.\nIn the classifications of colored\nworkers, that of servants leads with\n10,660 women and 2,942 men. The\nreport lists, "12,980 laundresses and\nthe next class is agricultural work,\n11,892 men and 394 women. Among\nother classes are barbers and hair\ndressers, 767 men and 268 women\nporters, 4,332 waiters, 1?191 men\nand 243 women. Among occupation\nother than industries are included\nphysicians, 120 clergymen, 476\ntrained nurses, 36 school teachers,\n,W2 musicians and music teachers,\n159. PRISONERS SPIRITED\nFROM UNGUARDED JAIL\nPilot Point, Tex., Dec. 29.-Two\ncolored men detained here in connec\ntion with the theft of two horses\nwere missing from jail at the roll\ncall. An unsigned note was found\non the door of a local newspaper\noffice, which read: "Both Negroes\ngot what was coming to them. Let\nthis be a warning to all Negroe loaf\ners get a job or leave town." Two\ncolored men disappeared from the\nPilot Point jail in a similar manner\nseveral months ago and nothing has\nbeen heard of them. The jail is lo\ncated in a backwoods and unguarded\nat night Colored people here blame\nthe Ku Klux Klan. Since the men\nhave been kidnaped, scores of men,\nwomen and children have left the\nvicinity. There are not many col\nored people here and opportunities\nfor employment are scarce.\nSUBSTITUTE FOR\nDYER BILL HELD\nNOT ACCEPTABLE\nSenators Told Commission to Investi\ngate Lynchings Is Inadvisable\nand Unnecessary.\nESSENTIAL FACTS KNOWN\nN. A. A. C. P. Holds Out for Revision\nof Senate Rules and Passage\nof Dyer Measure.\nNew York, Dec. 29.Proposal by\nRepublican senators to institute a\ncommission for the purpose of inves\ntigating lynching in the United\nStates has elicited a sharp letter to\nSenator Frelinghuysen, author of the\nresolution, from James Weldon\nJohnson, secretary of the National\nAssociation for the Advancement of\nColored People, in which notice is\nserved that a substitute for the Dyer\nanti-lynching bill will not be accept\nable to our people.\nFacts Are Known\nMr. Johnson\'s letter asserts that\nthe essential facts concerning lynch\ning in America are known through\n|Out the world that a commission to\n"investigate" will be interpreted only\nas a plan to delay action and to "lull\nthe nation and the Negro with false\nhopes." The letter in full is as fol\nlows:\n"I have carefully examined your\nproposed joint resolution establishing\na commission for the purpose of con\nducting a general inquiry into the\nsubject of lynchings in the United\nStates, including the number occur\nring and the causes thereof.\n"We would interpose no active op\nposition to any steps leading toward\nthe abolishment of the crime of\nlynching, but the National Associa\ntion for the Advancement of Colored\nPeople in this case as in a half dozen\nsimilar proposals in the past is un\nable to see ny necessity whatsoever\nfor such a commission, nor can we\nindorse the proposed measure as a\nsubstitute for the Dyer anti-lynching\nbill. Such a commission as you pro\npose could gather only statistics and\nfacts such as have already been pre\nsented by this association and other\nagencies and which have been re\npeatedly and at great length read\ninto the Congressional Record, where\nthey are available to the United\nStates Senate as well as to other\ncitizens.\nCommission Ineffective\n"The essential facts in regard to\nlynching are well known and undis\nputed. The gathering of such facts\nfor an additional year would have no\nappreciable effect upon the question.\nWe would, therefore, view this com\nmission plan, with its year of inves\ntigation, only as a further delay to\nthe one thing we believe to be essen\ntial in the circumstances, and that\nis legislation giving the federal gov\nernment jurisdiction where states\nfail, as they have failed in the past\n85 years, to secure to persons ac\ncused of crime trial by due process\nof law, or to prosecute and punish\nmembers of mobs.\n"This commission plan therefore,\nseems calculated only to delay action\nor lull the nation and the Negro with\nfalse hopes.\n"The National Association for the\nAdvancement of Colored People will\nnot be actively interested in a com\nmission to investigate lynching.\nRather, we insist up a revision of\n*he Senate\'s\nrulese\nof procedure\nhe passage of th Dyer anti-lynchdan\ning bill."\nSfna+or Mpdpl McCormick of Illi\nnois had a similar\nmeasurte\nbefore\nbe Senate, but withdrew i pending\nthe vote and committee action on the\nDyer bin. His bill authorized the\nappointment of a committee of five,\ncomposed of both races, to handle the\ninvestigations on mob violence.\nCOLORE MEN EAT TOO\nMUCMETTING FAT\nChicago, 111., Dec. 29.Colored men\nare getting fatter to the detriment\nof vest buttons and longevity, ac\ncording to a survey just completed,\nby a local medical organization.\nThe expansion of the waist line,\naccording to this survey, is d#e to\novereating and indugence in sweets.\n"Men who used to treat guests,\nfriends and customers at bars now\nentertain them at meals, buying\nfood, pastries and sweets," the report\nsaid.\nExcept in the rarest instances, col\nored men who live to ripe old age are\nthin. CHICAGO RIOT TO\nCOST CITY $500,000\nThe Citv of Chicago must dig up a\nhalf million dollars to pay the ex\npenses of the race riot there in 1919.\nEighteen death claims aggregating\n$31,000 were approved yesterday by\n*:he city\'s finance committee.\nThe city previously had paid $20,-\n800 for five ether deaths. Fifteen\ndeath claims remained unsettltd.\nDamage to property was not in\ncluded in the estimated figure of\n$500,000.\nDuring the riots, according to an\ninvestigating commission, 543 persons\nwere injured178 white, 348 colored\npeople and 17 of undetermined race.\nI STAND FOR THESE THINGS\nBy E. W. Gilles.\nI stand for the entire Bible, though\nI cannot understand or explain it in\nmany of its parts as I would like.\nI stand for the Trinity and for the\ntrinitarian gaspel. It is the message\nin the name of God the Father, and\nGod the Son, and God the Holy Ghost,\nthat reaches the hearts and lives of\nmen.\nI stand for the Bible doctrines as\nthe average man would understand\nthem in reading the Bible under the\ndirection of the Holy Ghost.\nI stand for the properties and cour\ntesies of the Christian life:\nI stand for either sympathetic co\noperation, with others or withdrawn\nI\niSLU',
       u'page': u'',
       u'place': [u'Illinois--Cook--Chicago',
        u'Minnesota--Hennepin--Minneapolis',
        u'Minnesota--Ramsey--Saint Paul'],
       u'place_of_publication': u'Saint Paul, Minn. ;',
       u'publisher': u'Northwestern Pub. Co.',
       u'section_label': u'',
       u'sequence': 2,
       u'start_year': 1889,
       u'state': [u'Illinois', u'Minnesota', u'Minnesota'],
       u'subject': [u'African American newspapers--Illinois.',
        u'African American newspapers--Minnesota.',
        u'African American newspapers.--fast--(OCoLC)fst00799278',
        u'African Americans--Illinois--Newspapers.',
        u'African Americans--Minnesota--Newspapers.',
        u'African Americans.--fast--(OCoLC)fst00799558',
        u'Chicago (Ill.)--Newspapers.',
        u'Illinois--Chicago.--fast--(OCoLC)fst01204048',
        u'Illinois.--fast--(OCoLC)fst01205143',
        u'Minneapolis (Minn.)--Newspapers.',
        u'Minnesota--Minneapolis.--fast--(OCoLC)fst01204260',
        u'Minnesota--Saint Paul.--fast--(OCoLC)fst01212130',
        u'Minnesota.--fast--(OCoLC)fst01204560',
        u'Saint Paul (Minn.)--Newspapers.'],
       u'title': u'The Appeal.',
       u'title_normal': u'appeal.',
       u'type': u'page',
       u'url': u'http://chroniclingamerica.loc.gov/lccn/sn83016810/1922-12-30/ed-1/seq-2.json'}],
     u'itemsPerPage': 20,
     u'startIndex': 21,
     u'totalItems': 725987}



Most of the APIs of interest to social scientists weren't designed for our use. They are primarily for web or mobile app developers who want to include the content on their pages. So while I might use the [MapQuest](http://developer.mapquest.com) API to look at how often intra-city trips involve highways, the target audience is business owners trying to help people get to their store. Similarly, scores of researchers have used data from [Twitter APIs](https://dev.twitter.com/docs/api/1.1) to study politics, but it was developed so that you could put a custom Twitter widget on your home page. 

The good news is that since these services want you to use their data, the APIs are often well documented, especially for languages like Python that are popular in Silicon Valley. The bad news is that APIs don't always make available the parts of the service, like historical data, that are of most interest to researchers. The worst news is research using APIs frequently violates the providers Terms of Service, so it can be an ethical grey zone. 

When you sign up as a developer to use an API, you usually agree to only use the API to facilitate other people using the service (e.g. customer's finding their way to your store) and that you won't store the data. API providers usually enforce this through rate limiting, meaning you can only access the service so many times per minute or per day. For example, you can only search status updates 180 times every 15 minutes according to [Twitter guidelines](https://dev.twitter.com/docs/rate-limiting/1.1/limits). [Yelp](http://www.yelp.com/developers/documentation/faq) limits you to 10,000 calls per day. If you go over your limit, you won't be able to access the service for a bit. You will also get in trouble if you redistribute the data, so don't plan on doing that. 

Two of the major reasons that web services require API authentication is so that they know who you are and so they can make sure that you don't go over their rate limits. Since you shouldn't be giving your password to random people on the internet, API authentication works a little bit differently. Like many other places, in order to use the Yelp API you have to sign up as [developer](http://www.yelp.com/developers). After telling them a little bit about what you plan to do--feel free to be honest; they aren't going to deny you access if you put "research on food cultures" as the purpose--you will get a Consumer Key, Consumer Secret, Token, and Token Secret. Copy and paste them somewhere special. 

Using the Yelp API goes something like this. First, you tell Yelp who you are and what you want. Assuming you are authorized to have this information, they respond with a URL where you can retrieve the data. The coding for this in practice is a little bit complicated, so there are often single use tools for accessing APIs, like [Tweepy](http://tweepy.github.io) for Twitter. 

There's no module to install for the Yelp API, but Yelp does provide some [sample Python code](https://github.com/Yelp/yelp-api/tree/master/v2/python). I've slightly modified the code below to show a sample search for restaurants near Chapel Hill, NC, sorted by distance. You can find more options in the search [documentation](http://www.yelp.com/developers/documentation/v2/search_api). The API's search options include things like location and type of business, and allows you to sort either by distance or popularity.


```python

```
