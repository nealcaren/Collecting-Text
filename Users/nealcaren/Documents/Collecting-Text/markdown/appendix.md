
### Quantitaitve analysis of texxt

[*If this is just the appendix, maybe dumb this down so it focuses more specifically on Python.*]

In September of 2011, Science magazine printed an [article](http://www.sciencemag.org/content/333/6051/1878) by Cornell sociologists Scott Golder and Michael Macy that examined how trends in positive and negative attitudes varied over the day and the week. To do this, they collected 500 million Tweets produced by more than two million people. They found fascinating daily and weekly trends in attitudes. It’s a great example of the sort of interesting things social scientists can do with online social network data. More generally, the growth of what computer scientists call “big data” presents social scientists with unique opportunities for researching old questions, along with empowering us to ask new questions. While some of this big data is exclusively quantitative, much of the data also consists of text at some point in the research process. Sociologists have long had tools to assist us in coding and analyzing dozens or even hundreds of text documents, but many of these tools are less useful when the number of documents is in the tens of thousands or millions. Every sociology professor, graduate student and undergraduate in the United States working together couldn’t code even the 1% daily sample of Tweets that Twitter provides free to researchers. Luckily, computer scientists have been working for quite a while on exactly this data problem–how do we collect, categorize and understand massive text databases.

It turns out that while the volume of data in a study such as Golder and Macy’s is intimidating, doing a project of this sort isn’t that complicated for the typical social scientist. The major challenges are (1) collecting and managing the data, (2) turning the text into numbers of some sort, and (3) analyzing the numbers. The third step involves techniques familiar to many quantitative researchers. Based on their [supplementary file](http://www.sciencemag.org/content/333/6051/1878/suppl/DC1), it appears Golder and Macy used Stata to analyze the data.

Getting the Twitter data isn’t that difficult, although it does involve dealing with the Twitter Application Programming Interface, or API, a task most social scientist have not been trained to do. If you’re wondering, Facebook also has an API and you can use what are called “web scraping” techniques to gather data from blogs and other websites too. I’ll discuss these topics in other chapters.

In this introductory chapter, I’ll walk through the basics of a popular way to convert text into meaningful numbers, using the same analytic strategy that Golder and Macy used. While you can do this sort of analysis using one of several different programs or languages, one commonly used for this sort of quantitative text analysis is Python. It is free, used by millions (so there are lots of resources available), and relatively straightforward to learn. If you have a Mac, it’s already on your computer. There are no pull down menus in Python, though, so learning by fumbling around isn’t the best option. That’s what led me to write this. Most Python texts are aimed at people who want to learn programming, and the few that cover topics of interest to sociologists generally assume a computer science background.

This initial tutorial is aimed at social scientists who may be familiar with some statistical package like SPSS, Stata or SAS, but haven’t used Python. It walks through the basics of one type of text analysis using some sample text data, but swapping in your own data once you’ve got this up and running isn’t much harder.

Firing up Python
-----
For the purposes of this walkthrough, I’m going to assume you are using a Mac. If you aren’t (and even if you are), great places to get Python are either [Anaconda](https://store.continuum.io/cshop/anaconda/) (free).

[*section here about Python datastack and stuff like that.*]

This text is written using using [Jupyter Notebook](http://jupyter.org) which is a pretty nifty way to combine code and text. Assuming you have installed it through either of the above mechanism, you start iPython on a Mac by running Terminal and typing `jupyter notebook`. This will open up a browser window where you can enter your code and see the results on one page.

Strings
-------

As a general rule, I think its best to think of a big task, like analyzing a million tweets, as a series of simple problems. Develop, through trial and error, solutions to simplified versions of each of the problems.  Link the solutions together, and then, when its working for all your cases, add the required complexity to each of the steps. For this project, the simplest place to start is with analyzing one fake tweet with small sentiment dictionary. After that is up and running, scaling up the number of tweets, including a full sentiment dictionary and using a more sophisticated analytic method, is relatively straighforward.

Pretend that the first tweet you wanted to analyze was, “We have some delightful new food in the cafeteria. Awesome!!!.” To tell Python about your tweet, type:


```python
tweet = 'We have some delightful new food in the cafeteria. Awesome!!!'
```

If you are in Python or iPython, hit the return or enter key after you type the line. If you are in iPython Notebook, hit Shift-Return to run the cell. To fix an error or otherwise change and exisiting line of code in an iPython Notebook, double click on the cell you want to change, edit  the text, and then press Shift-Return to run the revised code line. 

The text is surrounded by a single quote (i.e. `'`) on each side. You can also use double quotes (i.e. `"`) or even triple single quotes (i.e. `'''`), but single quotes are the default Python style for entering a string. 
To make sure that you typed the tweet correctly, you can type:


```python
tweet
```




    'We have some delightful new food in the cafeteria. Awesome!!!'



Typing the name of a string in Python all by itself prints out the contents. You can get almost the same response using a `print` statement:


```python
print(tweet)
```

    We have some delightful new food in the cafeteria. Awesome!!!


The only difference is that the first response was wrapped in single quotes and the second wasn’t. As a side note, the single quotes weren’t because you put them there. If you used double quotes, you would get the same thing:



```python
tweet = "We have some delightful new food in the cafeteria. Awesome!!!"
tweet
```




    'We have some delightful new food in the cafeteria. Awesome!!!'



Lists
----

Now, following Golder and Macy, we need to decide if this is a positive or negative opinion. If we had a large sample of the Tweets already coded by sentiment, would could try and figure out which words appeared more often in Tweets we considered positive, and which words appeared more often in Tweets we considered negative. In sociology, we might think about this in a regression framework. We want to predict whether the sentence is positive, negative, or neither, and we could use the presence or absence of words as predictors. In computer science, this would be considered a supervised learning classification problem. But we don’t have a sample precoded, so let’s save classification for another day.

One straightforward way to approach the problem is to count the proportion of words that usually have a positive connotation and the proportion of words that have a negative connotation. This is a common analytic strategy in many fields, especially psychology. Golder and Macy’s Twitter study used the lists of positive and negative words that are part of the [Linguistic Inquiry and Word Count (LIWC)](http://liwc.net/) project. This data is only available commercially, so I won’t include it this tutorial. There’s a [similar dictionary](http://www.cs.pitt.edu/mpqa/) that’s freely available, but we won’t use that just yet.

For now, you can just make your own list of positive words. We’ll swap in the official list before we are done. Off the top of my head, the words "awesome", "good", "nice", "super", and "fun" are words that I use when I’m trying to be positive. To put this list into Python:


```python
positive_words = ['awesome', 'good', 'nice', 'super', 'fun']
```

`positive_words` is the name of our list. I just made up that name right now. There are only a few restrictions on what you can name your list (e.g., it can’t start with a number or have spaces). Feel free to make the name as long and as informative as necessary; there is no reason to name your list `x34` or `list1` because you won't know what they do hours or months later. 

To tell Python that we are creating a list, you put `['everything','in','brackets','separated','by','commas.']` Since the items in the list are strings, each goes in single quotes. 

If you wanted to add an item to your list, you append an item to the list:


```python
positive_words.append('delightful')
```

In this case, you start with the list name, followed by `.append`, and then in parenthesis write the item that you want to add to your list. No `=` required. If you are adding a string, you put it in quotes. Otherwise, if you enter a string Python will think you are referencing something.


```python
positive_words.append(like)
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-9-d07de71c07d9> in <module>()
    ----> 1 positive_words.append(like)
    

    NameError: name 'like' is not defined



```python
new_word_to_add = 'like'
positive_words.append(new_word_to_add)
```

After I got the error message, I created a new string call `new_word_to_add` which contained the word that I wanted to append. I then added the word “like” to our list by using the string’s name, `new_word_to_add`. This is a pretty inefficient way to do things in this case, but useful in many other situations when you are looping over values.

Lists are much more flexible than represented above. Items can be longer than a single word (e.g. `'Super fun'`); strings and numbers can be in the same list (e.g. `[3, 'swell']`); and you can even put additional lists inside your list (e.g. `[[1,2,3],['sociology','political science']]`.

If we want to see what was in the list we created above, we can print it:


```python
print(positive_words)
```

    ['awesome', 'good', 'nice', 'super', 'fun', 'delightful', 'like']


The brackets remind you that this is a list, and the items in the list are separated by commas.

Note: if you see sample code in a book or on the internet where someone writes `print(positive_words)` they are using Python 3.x. Python 2 code and Python 3 code don't play well together. Since some of the modules of interest to sociologist aren't avaialbe yet for Python 3 (despite Python 3 being released in 2009), I would advise sticking with Python 2, which usually means Python 2.7, the most recent release.  

Now create a list of negative words:


```python
negative_words = ['awful','lame','horrible','bad']
print(negative_words)
```

    ['awful', 'lame', 'horrible', 'bad']


If you wanted to measure whether or not any emotion was expressed, you might create one list that combines all the positive and negative words. Rather than retyping them, you can combine the lists with a plus sign:


```python
emotional_words = negative_words + positive_words
print(emotional_words)
```

    ['awful', 'lame', 'horrible', 'bad', 'awesome', 'good', 'nice', 'super', 'fun', 'delightful', 'like']


You might have noticed that the negative words are listed first, in the order that they were entered, followed by the postive words, also in the ordered that they were entered. The appended words were placed at the end of the list. In Python, lists remember their order, and you can leverage this fact when you are joining lists or looping over values. In contrast, other ways of storing data in Python, like dictionaries, don't have a fixed order.

From Strings to Lists
-----

Later on, we’ll create a better list of positive and negative words, but for now let’s return to the original tweet. The default strategy for this sort of analysis is to examine each word in the sentence on its own, regardless of word ordering. This is called a "bag of words" model. It has some obvious drawbacks (e.g. "This was not fun." will show up as positive because of the presence of the word "fun" unless you somehow model it’s negation.), but, with a few tweaks, these models can be about as good at classification as an undergraduate RA.

Since our unit of analysis is the word and not the sentence, we want to split our sentence into words. We can do that by using `split()`:


```python
words = tweet.split()
print(words)
```

    ['We', 'have', 'some', 'delightful', 'new', 'food', 'in', 'the', 'cafeteria.', 'Awesome!!!']


Here, we’ve split our string `tweet` making a cut every time there was a space. This new object is stored as `words`.  As you can see from the results of the print command, the new object is displayed in brackets, so Python has created `words` as a list. In order to see how many words are in the sentence, you use the `len` function, which will return the number of objects.


```python
len(words)
```




    10



Counting the length and getting the word count only works because we’ve split the sentence into a list of words. If we ask for the length of the original tweet, we get something different:


```python
len(tweet)
```




    61



Python doesn’t know that you only care about words, so it defaulted to counting the number of characters.

By default `split()` divides based on white spaces. To split by something else, put your string inside the parentheses: 


```python
print tweet.split('.')
```

    ['We have some delightful new food in the cafeteria', ' Awesome!!!']


The resulting list has two elements--you could confirm this with `len(tweet.split('.'))`. This is a horrible way to split a text by sentences, by the way, because it ignores all other punctation and would start a new sentence after every "Ms.".

Loops
---
Our first goal is to go through our list of words and see if any of them show up in our list of positive words. For starters, we can loop over each of the words in our sentence with a `for` loop:


```python
for word in words:
    print(word)
```

    We
    have
    some
    delightful
    new
    food
    in
    the
    cafeteria.
    Awesome!!!


The `for` tells Python that we are going to cycle through each elements of the list. `word` is the name that I just made up that will hold each of the words. `in words` tells Python which list we want to iterate through, and the colon ends a line that declares a loop. Note that the second line is indented. I used a tab; others put four spaces. Actually, that's a lie. Since I'm using an iPython Notebook, the indentation was created automatically which is awfully convient. Your text editor might do something similar. But anytime a line ends in a `:` Python will expect that the next line be indented.

If you don’t indent, Python will report an error:


```python
for word in words:
print(word)
```


      File "<ipython-input-17-d57cbd4eaf01>", line 2
        print(word)
            ^
    IndentationError: expected an indented block



Since you often need to manipulate each item in a list, you can end up with lots of loops in Python. Loops can be a slow way to process data and multiple nested loops can be difficult to debug or read, so always be on the look out for techinques that minimize them. 

Conditionals
------

While this loop prints out each word (when the second line is appropriately tabbed), what we actually want to do is see if that word is the list of positive (or negative) words.


```python
for word in words:
    if word in positive_words:
        print(word)
```

    delightful


Here, we include a conditional: Python only evaluates the `print word` statement if the value of `word` is in our list of positive words. So, the first time the loop cycles through and sees the value of `word` is "The", so the loop skips the `print word` line. Here, the `if` line ends in a colon and the lines that should only occur if the conditions are met are doubled indented–once as a result of the `for` and once because of the `if`. 

We can mix things up by printing a plus sign every time a word in `positive_words` and a minus sign if the word is in `negative_words`.


```python
tweet_2 = "Food is lame today. I don't like it at all."
words_2 = tweet_2.split()

for word in words_2:
    if word in positive_words:
        print('+')
    elif word in negative_words:
        print('-')
```

    -
    +


The first few lines of the loop are quite similar to the original version. We are analyzing a different tweet, so we loop over `words_2` instead of `words`. Instead of printing `word`, we print a plus sign which is in quotes, because that is what we literally want to print.

The second time I wanted to evaluated whether `word` was i a list or not, I used `elif` instead of `if`.  `elif` is short for "else if", and Python will only evaluate it if the original `if` statement is false.

Note that since there wasn't any negative words in the original example, so I created a new string, `tweet_2` that used both positive and negative words. Since I won't be using `tweet_2` or `words_2` anywhere else, I could have bypassed their creation entirely and jumped right to the loop:


```python
for word in "Food is lame today. I don't like it at all.".split():
    if word in positive_words:
        print('+')
    elif word in negative_words:
        print('-')
```

    -
    +


This is pretty ugly, so I wouldn't recomend it, but doesn't highlight how Python can perform multiple operations in the same line. That is, we didn't need to create a list ahead of time, we can just create it as part of the `for` statement. 

You could use `if` again instead of `elif`, but that wastes processesing time and could lead to unitential mistakes. Additionally, you know that I'm done with the original `if` conditional because I've unindented the `elif`. 


```python
number_list  = [132, 25]

print("Dangers of multiple ifs")
for number in number_list:
    print number
    if number > 100:
        print('Big number')
    if number > 10:
        print('Medium number')

print('')
print("Benefit of elif")
for number in number_list:
    print number
    if number > 100:
        print('Big number')
    elif number > 10:
        print('Medium number')
        
```

    Dangers of multiple ifs
    132
    Big number
    Medium number
    25
    Medium number
    
    Benefit of elif
    132
    Big number
    25
    Medium number


You can also use `if not` for when you want to do something when and item doesn't mean the condition, or `else` after and `if` or `elif` if you want an action to happen when none of the other conditions are met. 

Going back to our original loop, we can make it little bit more informative by adding some text that explains why it is randomly printing out the word, "delightful" or a plus sign.


```python
for word in words:
    if word in positive_words:
        print(word + ' is a positive word.')
```

    delightful is a positive word.


When Python sees `+` it attempts to combine the items. In this case, since both `word` and "is a positive word." are strings, the result is a longer string. This is the same logic that we used above to combine the two lists of words to create a longer list. 

This also works for combining two or more numbers: 


```python
print(3 + 3) 
```

    6


You can’t use this strategy to combine a string and a number:


```python
print(3 + ' is a number.')
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-24-1283d9876494> in <module>()
    ----> 1 print(3 + ' is a number.')
    

    TypeError: unsupported operand type(s) for +: 'int' and 'str'


Again, a pretty informative error message. Python won't let you combine an interger (`int`) with a string (`str`). But you can tell Python to treat `3` like a string, either by putting it in quotes or by telling Python to treat it like a sting.


```python
print('3' + ' is a number.')

print(str(3) + ' is a number.')

some_number = 3
print(str(some_number) + ' is a number.')
```

    3 is a number.
    3 is a number.
    3 is a number.


You aren’t limited to combining just two items, any number of like objects can be put together with the +. And it isn't just for printing. You can use this method to create new variables whichg you can be manipulated.


```python
output = str(some_number) + ' is a number.'

print(output.split())
```

    ['3', 'is', 'a', 'number.']


Preprocessing
=========

You might have noticed that while our loop matched “delightful”, it didn’t find “awesome”.  Looking back at the list of words that printed when we printed every word in our tweet might provide some clues as to why this occurred. While we have “awesome” in our positive words list, we don’t have “Awesome!!!” and Python is looking for an exact match. In order to get the two versions to match, we would need to make the “A” lower case and remove the exclamation marks. This is called pre-processing or cleaning the data. Shifting everything to lower case and stripping punctuation are the most common pre-processing tasks in natural language processing. Other common things to do are stemming words, which attempts to find the root of the word (e.g. “running” and “runs” both get reduced to “run”) and removing little words like “the”, “and”, or “if”, which are known as stop words.

Since removing capitalization and punctuation involves throwing away potentially meaningful variation, you should proceed with caution. For example, you might think that the “Awesome!!!” is different from “awesome”, that “WOW” is different from ”wow”, or that “Cool!” is different from “Cool?”. In the machine learning tradition (a set of techniques I will discuss in more detail at a later point), this is part of the art of “feature selection”. Social scientists have independent or explanatory variables that they use to explain their models, while computer scientists try to find the “features” with the most predictive power. In natural language processing, features can be more than the absence or presence of specific words. Word count, presence of parts of words, sentence complexity, use of the passive voice, presence of emoticons, or any other text attribute that can be expressed as a number can be included as a feature. I’m a fan of starting with just the words to get a baseline model, and then seeing if you can improve on it. And in this case, we don’t have punctuation or non-lower cases words coded in our list of emotional words, so the decision is made for us.

However, making strings lower case in Python is simple:


```python
print(tweet.lower())
```

    we have some delightful new food in the cafeteria. awesome!!!


But we can't do it with a list of things.


```python
print(words.lower())
```


    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-29-2ce3427a2f9e> in <module>()
    ----> 1 print(words.lower())
    

    AttributeError: 'list' object has no attribute 'lower'



```python
from string import punctuation
```


```python
'Awesome!!!!'.strip(punctuation)
```




    'Awesome'



So we either have to make it lower case when it is a full sentence, or we can do it to each individual word:


```python
for word in words:
    print(word.lower())
```

    we
    have
    some
    delightful
    new
    food
    in
    the
    cafeteria.
    awesome!!!


Updating our loop, we still don’t find awesome yet:


```python
for word in words:
    word_lower = word.lower()
    if word_lower in positive_words:
        print(word_lower + ' is a positive word.')
```

    delightful is a positive word.


This is because we have not removed the exclamation marks. Python correctly evalutes "awesome!!!" to be different from "awesome." 

One solution would be remove the trailing "!". Python has a number of built in ways of manipulating text strings, and in this case we can use `strip()` which removes trailing and leading characters. Like `split()` the default is a whitespace, but it will remove any characters you put in the parentheses. 


```python
print('awesome!!!'.strip('!'))
```

    awesome


`split()' can process more than one character, which makes it even more useful:


```python
print('awesome.'.strip('!.,?'))
```

    awesome



```python
print('awesome!?'.strip('!.,?'))
```

    awesome


Since strip only looks at the end of string, we could use `replace()` if we wanted to remove problem characters from all of the string.


```python
print 'awesome!!! party'.strip('!')
print 'awesome!!! party'.replace('!','')
```

    awesome!!! party
    awesome party


`replace()` takes two options, while `strip()` only takes one. The first is what you are looking for–in this case, the exclamation mark. The second is what you want to replace it with–in this case, nothing. As always, strings should be in quotation marks.

If you wanted to remove the puncation from a entire sentence, you could use replace because, sometimes, words in the middle of a sentence will have punctuation. Hyphens and other dashes are a tricky case, because sometimes they are used like a colon or comma and you want the removed, and replaced with a space, and other times they are in the middle of word and you either want them kept, or replaced with no space. 

We can use `strip()`, or `replace()`, to create a new string, which could even have the same name as your old string:


```python
word = 'awesome!!!'.strip('!.,?')
print word
```

    awesome


We’ve lost the original `word`, so this isn’t always the best policy because you might need the orginial word at a later stage in the analysis. You might want to store your original string away some place for safe keeping, or create a new string name, such as `word_processed` that you update with each of your different preprocessing steps.

More than one string operation can be included in the same statement, so we could remove the punctuation from the weet with something like:


```python
word = 'awesome!!!'
word_processed = word.strip('!').lower()
print word_processed
```

    awesome


Combing multiple operations onto one line sometimes can save space and time, but may become convulated, so it sometimes better to split it across different lines.


```python
word_processed = word.strip('!')
word_processed = word_processed.lower()
print word_processed
```

    awesome


The first line creates a new string `word_processed` that holds our original word minus the punctuation. Note that the second line has `word_processed` on both sides of the equal sign. If you kept `word.lower()` on the right hand side you would just be throwing away the punctuation stripping that you did in the first line.

While removing the exclamation mark works for this word, it isn’t a very good general solution, because it ignores the 31(!) other punctuation marks that could be used in a sentence. Since we want to develop a script that works more generally, we want to use a technique that can be flexible enough to handle more than exclamation marks.

Importing Modules
=====

Python has built-in all the punctuation you need to account for in all cases. You can access them by typing: 


```python
from string import punctuation

print punctuation
```

    !"#$%&'()*+,-./:;<=>?@[\]^_`{|}~


Most of Python’s usefulness isn’t available to you when you start up the program. You need to selectively bring modules into memory. In this case, we are accessing the `string` module, which comes with your Python. Other modules are available either prebuilt or from the internet, and to do anything interesting with natural language processing, you’ll have to download and set some of them up, unless you are using something like the Enthought Python Distribution, which has many useful modules already available.

As noted above, `strip()` will remove more than one character, so we can update our loop: 


```python
for word in words:
    word_processed = word.lower()
    word_processed = word_processed.strip(punctuation)
    if word_processed in positive_words:
        print word + ' is a positive word'
```

    delightful is a positive word
    Awesome!!! is a positive word


It worked!!!



Putting it all together
----
The original quantity of interest was the fraction of positive words in the sentence. That is, the number of positive words divided by the total number of words. We already computed the denominator of the fraction when we computed the length of the string words using the `len` function. One straightforward way to compute the numerator is with a counter that starts at zero and increases by one each time the loop finds a positive word.


```python
positive_counter=0
for word in words:
    word_processed = word.lower()
    word_processed = word_processed.strip(punctuation)
    if word_processed in positive_words:
        positive_counter = positive_counter + 1

print positive_counter
print positive_counter / len(words)
```

    2
    0.2


Well, that half worked. The positive counter was what we expected, but 2 divided by 10 should be .2, not 0. Python default for division is to round down to the nearest integer when the all figures involved are whole numbers. While frustrating, it doesn’t actually impact you much because it has an easy fix: importing a different division calculator from the built in `future` module.


```python
from __future__ import division
positive_counter/len(words)
```




    0.2



Note that future has two underscores in front of it and two underscores behind it. Like other modules, you only have to import it once. 


```python
from IPython.core.display import HTML
def css_styling():
    styles = open("styles/custom.css", "r").read()
    return HTML(styles)
css_styling()
```




<style>
    @font-face {
        font-family: "Computer Modern";
        src: url('http://mirrors.ctan.org/fonts/cm-unicode/fonts/otf/cmunss.otf');
    }
    div.cell{
        width:800px;
        margin-left:auto;
        margin-right:auto;
    }
    h1 {
        font-family: "Computer Modern";
        
    }
    h4{
        margin-top:12px;
        margin-bottom: 3px;
        
       }
    div.text_cell_render{
        font-family: Computer Modern, "Helvetica Neue", Arial, Helvetica, Geneva, sans-serif;
        line-height: 145%;
        font-size: 130%;
        width:800px;
        margin-left:auto;
        margin-right:auto;
                color: #413839;

    }
    .CodeMirror{
            font-family: "Source Code Pro", source-code-pro,Consolas, monospace;
            
    }
    .prompt{
        display: None;
    }
    .text_cell_render h5 {
        font-weight: 300;
        font-size: 16pt;
        color: #4057A1;
        font-style: italic;
        margin-bottom: .5em;
        margin-top: 0.5em;
        display: block;
    }

    .warning{
        color: rgb( 240, 20, 20 )
        }

</style>



