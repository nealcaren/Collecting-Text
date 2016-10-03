
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


```python

```

Most of the APIs of interest to social scientists weren't designed for our use. They are primarily for web or mobile app developers who want to include the content on their pages. So while I might use the [MapQuest](http://developer.mapquest.com) API to look at how often intra-city trips involve highways, the target audience is business owners trying to help people get to their store. Similarly, scores of researchers have used data from [Twitter APIs](https://dev.twitter.com/docs/api/1.1) to study politics, but it was developed so that you could put a custom Twitter widget on your home page. 

The good news is that since these services want you to use their data, the APIs are often well documented, especially for languages like Python that are popular in Silicon Valley. The bad news is that APIs don't always make available the parts of the service, like historical data, that are of most interest to researchers. The worst news is research using APIs frequently violates the providers Terms of Service, so it can be an ethical grey zone. 

When you sign up as a developer to use an API, you usually agree to only use the API to facilitate other people using the service (e.g. customer's finding their way to your store) and that you won't store the data. API providers usually enforce this through rate limiting, meaning you can only access the service so many times per minute or per day. For example, you can only search status updates 180 times every 15 minutes according to [Twitter guidelines](https://dev.twitter.com/docs/rate-limiting/1.1/limits). [Yelp](http://www.yelp.com/developers/documentation/faq) limits you to 10,000 calls per day. If you go over your limit, you won't be able to access the service for a bit. You will also get in trouble if you redistribute the data, so don't plan on doing that. 

Two of the major reasons that web services require API authentication is so that they know who you are and so they can make sure that you don't go over their rate limits. Since you shouldn't be giving your password to random people on the internet, API authentication works a little bit differently. Like many other places, in order to use the Yelp API you have to sign up as [developer](http://www.yelp.com/developers). After telling them a little bit about what you plan to do--feel free to be honest; they aren't going to deny you access if you put "research on food cultures" as the purpose--you will get a Consumer Key, Consumer Secret, Token, and Token Secret. Copy and paste them somewhere special. 

Using the Yelp API goes something like this. First, you tell Yelp who you are and what you want. Assuming you are authorized to have this information, they respond with a URL where you can retrieve the data. The coding for this in practice is a little bit complicated, so there are often single use tools for accessing APIs, like [Tweepy](http://tweepy.github.io) for Twitter. 

There's no module to install for the Yelp API, but Yelp does provide some [sample Python code](https://github.com/Yelp/yelp-api/tree/master/v2/python). I've slightly modified the code below to show a sample search for restaurants near Chapel Hill, NC, sorted by distance. You can find more options in the search [documentation](http://www.yelp.com/developers/documentation/v2/search_api). The API's search options include things like location and type of business, and allows you to sort either by distance or popularity.


```python

```
