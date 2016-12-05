
### Intro to Python for Social Sciencists

In September of 2011, Science magazine printed an [article](http://www.sciencemag.org/content/333/6051/1878) by Cornell sociologists Scott Golder and Michael Macy that examined how trends in positive and negative attitudes varied over the day and the week. To do this, they collected 500 million Tweets produced by more than two million people. They found fascinating daily and weekly patterns in attitudes. It’s a great example of the sort of interesting things social scientists can do with online social network data. More generally, the growth of “big data” presents social scientists with unique opportunities for researching old questions, along with empowering us to ask new questions. 

Along with these new sources of data, social scienctists are also beginning to import new methods of analysis into their research program. This includes both novel ways of understanding texts, such as topic models, to complex methods for analzying quantitive data, such as ensemble methods like random forests. Traditional closed-source statistical packages, such as [Stata](http://www.stata.com), have been quite slow in adopting these new techniques. In contrasts, R users are much less disadvantaged, particluarly with new packages like [`rvest`](https://github.com/hadley/rvest) for web scraping and several sophisticated pacages for [text analysis](https://cran.r-project.org/web/packages/tm/index.html) and [machine learning](https://cran.r-project.org/web/packages/randomForest/randomForest.pdf). That said, the linqu franca of computer science is Python, likely because the teach it in Stanford's CS 101 course. As such, there is a particularly well developed ecosytem for doing data science with Python with a particular strenght in the collection of data from the internet and the management and processing of text data. I wouldn't recommend a 1st year graduate student *only* learn Python, but I  think there is a tremendous advantage for quantitaitve analysists to be polyglots. There is likely to be a way to collect or analyze data that you can't do with your primary computer statistical package, and not knowing how to do it won't convince Reviewer 2 very well. 

This tutorial is designed to introduce social scientists to using Python. It does this by working through an example of how to collect and analyze data in the style of Golder and Macy's study. It assumes that the reader is general familiar with social science research, but has little to know experience with Python. Most Python texts are aimed at people who want to learn programming (historically, the only a small minority of Python users were data scientists), and the few that cover topics of interest to sociologists generally assume a computer science background.

Firing up Python
-----

I highly recommend installing the [Anaconda](https://store.continuum.io/cshop/anaconda/) Python distribution. In addition to Python (which you might already have on your computer), it bundles a couple of incredibluy useful things: a suite of libraries for doing data science; Jupyter for creating notebooks; and `conda` for installing additional packages. Prior to Anaconda, getting Python up and running for data science involved walking up hill in the snow both ways. 

This text is written using using [Jupyter Notebook](http://jupyter.org) which is a pretty nifty way to combine code and text. Assuming you have installed it through either of the above mechanism, you start iPython on a Mac by running Terminal and typing `jupyter notebook`. This will open up a browser window. From here, you can create, modify or run notebooks, which are stored with an `.ipynb` extension  (previously called IPython notebooks).  Pro-tip: a Google search for whatever you are trying to figure out plus `filetype:ipynb` will return only notebook results, which are often a great way to see code samples that might solve your problem.  

Getting started with strings
-------

As a general rule, I think its best to think of a big task, like analyzing a million tweets, as a series of simple problems. Develop, through trial and error, solutions to simplified versions of each of the problems.  Link the solutions together, and then, when its working for all your cases, add the required complexity to each of the steps. For this project, the simplest place to start is with analyzing one fake tweet with small sentiment dictionary. After that is up and running, scaling up the number of tweets, including a full sentiment dictionary and using a more sophisticated analytic method, is relatively straighforward.

Pretend that the first tweet you wanted to analyze was, “We have some delightful new food in the cafeteria. Awesome!!!.” To tell Python about your tweet, type:


```python
tweet = 'We have some delightful new food in the cafeteria. Awesome!!!'
```

If you are in Python or iPython, hit the return or enter key after you type the line. If you are in iPython Notebook, hit Shift-Return to run the cell. To fix an error or otherwise change and exisiting line of code in an iPython Notebook, double click on the cell you want to change, edit  the text, and then press Shift-Return to run the revised code line. 

The text is surrounded by a single quote (*i.e.* `'`) on each side. You can also use double quotes (*i.e.* `"`) or even triple single quotes (*i.e.* `'''`), but single quotes are the default Python style for entering a string. 
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



If you are using Python 2.7, which is the default on the Anaconda version although all the cool kids are using Python 3, you don't necessarily need the parenthesis around the thing you want printed.


```python
print 'Python 2.7 forever!'
```

    Python 2.7 forever!


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

In this case, you start with the list name, followed by `.append`, and then in parenthesis write the item that you want to add to your list. No `=` required. In Python, lists are considered *mutable* because they be changed, such as by adding or deleting an item. In contrast, strings are *immutable* and can't be changed.



```python
s = 'Strings are '
s + 'immutable'
```




    'Strings are immutable'



Above, `immutable` was displayed after `s` but not added to it. 


```python
print(s)
```

    Strings are 


If you wanted to append text, you would have to create a new variable, although the new variable could have the same name.


```python
s = s + 'immutable'
print(s)
```

    Strings are immutable


Back to appending our list. If you are adding a new text, you put it in quotes. If you enter a string without quotation marks, Python will think you are referencing something. 


```python
positive_words.append(like)
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-11-d07de71c07d9> in <module>()
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

Later on, we’ll create a better list of positive and negative words, but for now let’s return to the original tweet. The default strategy for this sort of analysis is to examine each word in the sentence on its own, regardless of word ordering. This is called a "bag of words" model. It has some obvious drawbacks (e.g. "This was not fun." will show up as positive because of the presence of the word "fun".), but, with a few tweaks, these models can be about as good at classification as an undergraduate RA.

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


      File "<ipython-input-21-d57cbd4eaf01>", line 2
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


This is pretty ugly, so I wouldn't recomend it, but it highlights how Python can perform multiple operations in the same line. That is, we didn't need to create a list ahead of time, we can just create it as part of the `for` statement. 

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

    <ipython-input-28-1283d9876494> in <module>()
    ----> 1 print(3 + ' is a number.')
    

    TypeError: unsupported operand type(s) for +: 'int' and 'str'


Again, a pretty informative error message. Python won't let you combine an interger (`int`) with a string (`str`). But you can tell Python to treat `3` like a string, either by putting it in quotes or by telling Python to convert it to a string.


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


But we can't do it with a list.


```python
print(words.lower())
```


    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-32-2ce3427a2f9e> in <module>()
    ----> 1 print(words.lower())
    

    AttributeError: 'list' object has no attribute 'lower'


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

One hallmark of Python programming is the creating of lots of small functions. These make your code a lot easier to read and lot easier to debug. For example, we could define a function that strips the punctation from a word and makes it lower case. 


```python
def clean_word(word):
    '''
    Helper function to process a word. 
    Strips punctuation.
    Lower cases
    '''
    word = word.lower()
    word = word.strip('!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~')
    return word
```

On the first line, the `def` begins the function definition. `clean_word` is the somewhat informative title for it and `(word)` defines the input. In this case, it will accept anything and, within the function, reference that input as `word`.  The three sentences between the single quotes are comments to my future self, and any other potential users of the function, about what I'm trying to do. Whatever is on these lines ignored by Python when processing the function. The next two lines are familiar, although I'm striping  significantly more punctiation. Finally, `return` defines what is being sent back. In this case, it is the string `word` after the two transformation. 

This new function can be used like any other Python function. 


```python
clean_word('Awesome!!')
```




    'awesome'



The results can also be sent back to a new variable.


```python
cw = clean_word('Awesome!!')
print(cw)
```

    awesome


The loop from above can now be simplified.


```python
for word in tweet.split():
    print(clean_word(word))
```

    we
    have
    some
    delightful
    new
    food
    in
    the
    cafeteria
    awesome


Since we start with the whole sentence and we are likely to process many of sentences, I created a second function that takes a raw sentence, splits it by word, cleans each word, and then returns a list of cleaned words. 


```python
def clean_sentence(sentence):
    '''
    Helper function to prepocess a sentence.
    Returns a list of cleaned words.
    '''
    words = sentence.split()
    word_list = []
    for word in words:
        word_list.append(clean_word(word))
    return word_list
```

Above, I created an empty list `word_list = []` to store each of the words. One of the advantatges of Python is that you don't have to declare a variable to be of a certain type before you use it, but you do have to define it before you modify it, by, for example, appending something to it.


```python
clean_sentence(tweet)
```




    ['we',
     'have',
     'some',
     'delightful',
     'new',
     'food',
     'in',
     'the',
     'cafeteria',
     'awesome']



The function can be made tidier (and faster) by using list comprehension. To my eye, list comprehension are fairly akward to read and understand. You don't have to use them, but they do make your code look clearner and execute approximately 10x faster than a loop. Finally, even if you don't use them, you'll see them a lot in looking at other code, so understanding them is fairly important skill.

List comprehension allows you to replace:


```python
letters = ['a','b','c']

new_list = []
for letter in letters:
    new_list.append(letter.upper())
print new_list
```

    ['A', 'B', 'C']



```python
new_list = [letter.upper() for letter in letters]

print new_list
```

    ['A', 'B', 'C']


The syntax is `[` *modified list item* `for` *list item* `in` *list name*`]`. In the above example, I wanted to upper case each letter from the list `letters`. The variable `letter` is defined as part of the list comprehension and then discarded (well not really, it still hold the value of the last item of the list, but, in practice, you never use it.)

Using list comprehension, the sentence cleaning function can be rewritten. 


```python
def clean_sentence(sentence):
    '''
    Helper function to prepocess a sentence.
    Returns a list of cleaned words.
    '''
    words = sentence.split()
    word_list = [clean_word(word) for word in words]
    return word_list

clean_sentence(tweet)
```




    ['we',
     'have',
     'some',
     'delightful',
     'new',
     'food',
     'in',
     'the',
     'cafeteria',
     'awesome']




The original quantity of interest was the fraction of positive words in the sentence. That is, the number of positive words divided by the total number of words. We already computed the denominator of the fraction when we computed the length of the string words using the `len` function. One straightforward way to compute the numerator is with a counter that starts at zero and increases by one each time the loop finds a positive word.


```python
positive_counter=0

tweet_words = clean_sentence(tweet)

for word in tweet_words:
    if word in positive_words:
        positive_counter = positive_counter + 1

print positive_counter

```

    2


This gets the job done, but it can be improved by making it a function and using some list comprehension. In this case, I add the `if` conditional to create a new list that consists of only the words that are in `words` and `positive_words`. Alternatively, Python also has a `set` datatype, and if I converted both lists to sets, the intersection of the two (i.e. `set(words).intersection(set(positive_words))`) would yield the same result. 


```python
def count_positive(sentence):
    '''
    Returns the count of positive words in a sentence. 
    '''
    words = clean_sentence(tweet)
    pos_s_words = [w for w in words if w in positive_words]
    return len(pos_s_words)

print count_positive(tweet)
```

    2


That function worked. With that accomplished, I can now begin to scale up the analysis. This means 1) counting both postive and negative sentiments; 2) loading a larger sentiment dictionary; and 3) applying the function to a large list of tweets.

Putting it all together
----

Files with data, or things that you want to turn into data, are often made available on the interent. While it is often possible to download them from the internet using your browswer, it is better to download them in Python. At a minimum, this ensures that you are storing the URL you retrieved the data from when you or someone else wants to replicate your analysis. 

Python have several libraries for accessing the internet, including `urllib` and `urllib2`. The are both useful but [`requests`](http://docs.python-requests.org/en/master/) is simpler and more powerful, especially for web scraping. If you aren't using the Anaconda Python Distribution, you likely have to install `requests`.

Most of Python’s usefulness isn’t available to you when you start up the program. You need to selectively bring modules into memory using `import`.


```python
import requests
```

Like many commands, Python won’t return anything unless something went wrong. In this case, it should just respond with `>>>` or nothing in a notebook, which means that the module was successfully brought into memory. Next, store the web address that you want to access in a string. You don’t have to do this, but it’s the type of thing that makes your code easier to read and allows you to scale up quickly when you want to download thousands of urls.


```python
url = 'http://www.unc.edu/~ncaren/haphazard/obama_tweets.txt'
```

Next, download the file and store the text in memory.


```python
tweets = requests.get(url).text
```


```python
tweets[:1000]
```




    u'Obama has called the GOP budget social Darwinism. Nice try, but they believe in social creationism.\nIn his teen years, Obama has been known to use marijuana and cocaine.\nIPA Congratulates President Barack Obama for Leadership Regarding JOBS Act: WASHINGTON, Apr 05, 2012 (BUSINESS W... http://t.co/8le3DC8E\nRT @Professor_Why: #WhatsRomneyHiding - his connection to supporters of Critical Race Theory.... Oh wait, that was Obama, not Romney...\nRT @wardollarshome: Obama has approved more targeted assassinations than any modern US prez; READ & RT: http://t.co/bfC4gbBW\nVideo shows federal officials joking about cost of lavish conference http://t.co/2i4SmoPM #obama #crime #p2 #news #tcot #teaparty\none Chicago kid who says "Obama is my man" tells Jesse Watters that the gun violence in Chicago is like "World War 17"\nRT @ohgirlphrase: American kid "You\'re from the UK? Ohhh cool, So do you have tea with the Queen?". British kid: "Do you like, go to Mcdonalds with Obama?\nA valid explanation for why '



Since the file has one Tweet on each line, we can turn it into a list of tweets by splitting it at the end of line character.


```python
tweets_list = tweets.splitlines()
```

As always, you can check how many items are in the list:


```python
print(len(tweets_list))
```

    1381



```python
print(tweets_list[:5])
```

    [u'Obama has called the GOP budget social Darwinism. Nice try, but they believe in social creationism.', u'In his teen years, Obama has been known to use marijuana and cocaine.', u'IPA Congratulates President Barack Obama for Leadership Regarding JOBS Act: WASHINGTON, Apr 05, 2012 (BUSINESS W... http://t.co/8le3DC8E', u'RT @Professor_Why: #WhatsRomneyHiding - his connection to supporters of Critical Race Theory.... Oh wait, that was Obama, not Romney...', u'RT @wardollarshome: Obama has approved more targeted assassinations than any modern US prez; READ & RT: http://t.co/bfC4gbBW']


Note the new `[0:5]` after  `tweets_list`. This is for selecting a slice of the list. The first number tells Python where to make the first cut in the list. The potentially counterintuitive part is that this number doesn’t reference an actual item in the list, but rather a position between each item in the list–think about where the comma goes when lists are created or printed. Adding to the confusion, the position at the start of the list is 0. So, in this case, we are telling Python we want to slice our list starting before the beginning and continuing until the fifth comma, which is after the fifth item in the list.

If you wanted to just print the second item in the list, you could type:


```python
print(tweets_list[1:2])
```

    [u'In his teen years, Obama has been known to use marijuana and cocaine.']


This slices the list from the first comma to the second comma, so the result is a list with just the second item in `tweets_list`. Unless you have a computer science background, this may be confusing as it’s not the common way to think of items in lists.

As a shorthand, you can leave out the first number in the pair if you want to start at the very beginning or leave out the last number if you want to go until the end. So, if you want to print out the first five tweets, you could just type `print(tweet_list[:5]). 

Alternatively, you could retrieve the second item directly:


```python
print(tweets_list[1])
```

    In his teen years, Obama has been known to use marijuana and cocaine.


The first item is index at 0, so the second item is at 1. Note also that this is a string, not a one-item list.



Earlier, we used a very short and haphazard list of words to measure positive sentiment. While the study in Science used the commercial LIWC dictionary, an alternative, freely-available sentiment dictionary is produced by Theresa Wilson, Janyce Wiebe, and Paul Hoffmann at the University of Pittsburgh. In both cases, the sentiment dictionaries are used in a fairly straightforward way: the more positive words in the text, the higher the text scores on the positive sentiment scale. While this has some drawbacks, the method is quite popular: the LIWC database has over 1,000 cites in Google Scholar, and the Wilson et al. database has more than 600.

Since the Wislon et al. list combines negative and positive polarity words in one list, and includes both words and word stems, I cleaned it up a little bit. 

As before, I use `requests` to download the file and then turn it into a list. 


```python
url='http://www.unc.edu/~ncaren/haphazard/negative.txt'
negative = requests.get(url).text
negative_words = negative.splitlines()
print(len(negative_words))
print(negative_words[50:55])
```

    3905
    [u'adamant', u'adamantly', u'addict', u'addiction', u'admonition']


Since I made the file, I know that the each line has a different word, so I can use `splitlines()` again to make the list. If, on other hand, the list of words was seperated by commons, I could use `split()`:


```python
negative_string = 'adamant,adamantly,addict,addiction,admonition'
negative_string.split(',')
```




    ['adamant', 'adamantly', 'addict', 'addiction', 'admonition']



Note that even though `negative_string` sort of looks like a Python list, it is a string beacuse it begins and ends with quotation marks rather than brackets.

I can duplicate the downloading process for the positive words. If I were downloading any more lists, I would likely make a function out of it.


```python
url='http://www.unc.edu/~ncaren/haphazard/positive.txt'
postive = requests.get(url).text
postive_words = postive.splitlines()
print(len(postive_words))
print(postive_words[50:55])
```

    2230
    [u'adoring', u'adoringly', u'adroit', u'adroitly', u'adulatory']


`postive_words` was the name of our the original list of words, but it has now be overwritten with the newer, longer list of words. This means that the `count_positive()` function, which used this name, doesn't have to be changed. It will simply use the the new `positive_words` to compare against. 

For fun though, I'll make a new function `count_sentiment` that takes both a sentence and word list. This way, I don't have seperate functions for the positive and negative lists.


```python
def count_sentiment(sentence, word_list):
    '''
    Returns the count of words in a sentence that are in a supplied list.
    '''
    words = clean_sentence(tweet)
    s_words = [w for w in words if w in word_list]
    return len(s_words)

print count_sentiment(tweet, positive_words)
print count_sentiment(tweet, negative_words)
```

    2
    0


The final stage is cycling through the entire list of tweets and producing a usable dataset for additional analysis. There are several straties for doing this, but let's assume that we want to only loop over the entire dataset once. In that case, a strategy would be to write a new function(!) that provides all the relveant information you would want, including positive and negative sentiment and the word count.


```python
def analyze_sentence(tweet):
    words = clean_sentence(tweet)
    word_count = len(words)
    
    postive_count  = count_sentiment(tweet, positive_words)
    negative_count = count_sentiment(tweet, negative_words)
    
    attributes = {'text'        : tweet,
                  'word count' : word_count,
                  'positive'   : postive_count,
                  'negative'   : negative_count}
    return attributes

analyze_sentence(tweet)
```




    {'negative': 0,
     'positive': 2,
     'text': 'We have some delightful new food in the cafeteria. Awesome!!!',
     'word count': 10}



Instead of returning a string or a number, `analyze_sentence` returns a Python dictionary. A dictionary has a set of keys (e.g. `'negative'` and `'word count'`) and values (`0` and `10`. One way to think about dictionaries is as dataset for just one case. The keys are the variable names and the values are the values. Keys are usually strings but the values can be any type, including numbers, strings, lists or even other dictionaries. Unlike lists, the sort order of dictionaries keys is not stable. For example, `text` was the first item I added and the third item returned. So instead of refering to the objects by their position, like in a list, you access the values through they key. 


```python
student = {'name' : 'Neal Caren',
           'GPA' : 2.8}

student['GPA']
```




    2.8



This can also be used to add items.


```python
student['Year'] = 'Junior'

print student
```

    {'Year': 'Junior', 'name': 'Neal Caren', 'GPA': 2.8}


Note the curly brackets and colons were used to define the dictionary, but then straight brackets and equal sign were used subsequently. 

To cycle through all the tweets, I can either create an empty list to store the results, appending the 


```python
tweet_statistics = []
for tweet in tweets_list:
    s =  analyze_sentence(tweet)
    tweet_statistics.append(s)
```

Or do it one line with list comprehsion. 


```python
tweet_statistics = [analyze_sentence(tweet) for tweet in tweets_list]
```


```python
tweet_statistics[:3]
```




    [{'negative': 0,
      'positive': 1,
      'text': u'Obama has called the GOP budget social Darwinism. Nice try, but they believe in social creationism.',
      'word count': 16},
     {'negative': 0,
      'positive': 0,
      'text': u'In his teen years, Obama has been known to use marijuana and cocaine.',
      'word count': 13},
     {'negative': 0,
      'positive': 0,
      'text': u'IPA Congratulates President Barack Obama for Leadership Regarding JOBS Act: WASHINGTON, Apr 05, 2012 (BUSINESS W... http://t.co/8le3DC8E',
      'word count': 17}]



Looks fine, although coding the first tweet as positive (based on "nice") raises some questions about the value of this sort of analysis. 

Finally, I use the incredibly powerful `pandas` library to quickly create the csv. `pandas` is the most important Python library for managing data. One major reason for this is tha it introduces the dataframe type. Unlike lists or dictionaries, dataframes closely resemble the way social scientists are used to manipulating data, with cases in rows and variables in columns. 

Rather than do anything sophistipated with the data in `pandas`, here I just want to put `tweet_statistics` into a new dataframe, called by convention `df`, and then write it to file as the csv format. `pandas` is fairly sophisticated in the way that it imports data, so it will automatically convert a list of dictionairies, into a dataframe.  


```python
import pandas as pd

df = pd.DataFrame(tweet_statistics)

df.to_csv('tweet_statistics.csv', index=False)
```

To check that it worked, I use the command line command `head` to peak into the first few lines of the newly created csv file. In IPython, terminal commands are prefaced with a `!`.


```python
!head 'tweet_statistics.csv'
```

    negative,positive,text,word count
    0,1,"Obama has called the GOP budget social Darwinism. Nice try, but they believe in social creationism.",16
    0,0,"In his teen years, Obama has been known to use marijuana and cocaine.",13
    0,0,"IPA Congratulates President Barack Obama for Leadership Regarding JOBS Act: WASHINGTON, Apr 05, 2012 (BUSINESS W... http://t.co/8le3DC8E",17
    0,0,"RT @Professor_Why: #WhatsRomneyHiding - his connection to supporters of Critical Race Theory.... Oh wait, that was Obama, not Romney...",19
    0,0,RT @wardollarshome: Obama has approved more targeted assassinations than any modern US prez; READ & RT: http://t.co/bfC4gbBW,17
    1,0,Video shows federal officials joking about cost of lavish conference http://t.co/2i4SmoPM #obama #crime #p2 #news #tcot #teaparty,17
    1,1,"one Chicago kid who says ""Obama is my man"" tells Jesse Watters that the gun violence in Chicago is like ""World War 17""",23
    0,1,"RT @ohgirlphrase: American kid ""You're from the UK? Ohhh cool, So do you have tea with the Queen?"". British kid: ""Do you like, go to Mcdonalds with Obama?",28
    0,0,A valid explanation for why Obama won't let women on the golf course.   #WhatsRomneyHiding,14


That was somewhat of a whirlwind tour of Python for social scientists. The primary goal was not to have the reader proficient in Python, but rather to provide an introductory text so that other Python texts, which are rarely written for social scientists, are more easily interpretable. 

***Bonus***

Analyzing tweets about President Obama might seem dated. What about Trump? Buzzfeed made all of Trump's tweets available as CSV file, so below I reuse the code to quickly analyze the sentiment of Trumps tweets.


```python
# Any thing after the # is ignored, so use # to comment your code

trump_url = 'http://buzzfeed-openlab.github.io/big-picture/2016/trump-tweets/tweets_realdonaldtrump.csv'
```


```python
# Pandas can read CSV files directly from the internet.

trump_df = pd.read_csv(trump_url)
trump_df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id_str</th>
      <th>created_at</th>
      <th>text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>610704372415229952</td>
      <td>Tue Jun 16 07:04:03 +0000 2015</td>
      <td>"@realJoeMurray: Hopefully tomorrow is the day...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>610704441872883712</td>
      <td>Tue Jun 16 07:04:19 +0000 2015</td>
      <td>"@brentcfritz: Today is the day America become...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>610710557285556224</td>
      <td>Tue Jun 16 07:28:37 +0000 2015</td>
      <td>"@insuraider: @realDonaldTrump is going to mak...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>610710626269306880</td>
      <td>Tue Jun 16 07:28:54 +0000 2015</td>
      <td>"@DONJUBBER: @realDonaldTrump Shock the world!...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>610710697140441088</td>
      <td>Tue Jun 16 07:29:11 +0000 2015</td>
      <td>"@PianoBecca: @realDonaldTrump No Amercian, re...</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Pandas has a command (.apply()) to directly apply a function to a column,
# but instead I'm just going to convert the column into a list. 

trump_tweets = trump_df['text'].tolist()
```


```python
trump_statistics = [analyze_sentence(tweet) for tweet in trump_tweets]
```

    /anaconda/lib/python2.7/site-packages/ipykernel/__main__.py:6: UnicodeWarning: Unicode equal comparison failed to convert both arguments to Unicode - interpreting them as being unequal



```python
# The warning message in salmon suggests that something is off with text encodings. 
# You would want to investigate this.

tdf = pd.DataFrame(trump_statistics)

tdf.to_csv('trump_statistics.csv', index=False)
!head 'trump_statistics.csv'
```

    negative,positive,text,word count
    0,0,"""@realJoeMurray: Hopefully tomorrow is the day we start to make America great again! Looking forward to it, @realDonaldTrump. #Trump2016",19
    1,0,"""@brentcfritz: Today is the day America becomes great again. @realDonaldTrump has some huge news that will shock the world #Trump4President""",20
    0,0,"""@insuraider: @realDonaldTrump is going to make Hillary's head spin tomorrow morning. Presidential Debate? Yes, please. #Trump2016""",16
    1,0,"""@DONJUBBER: @realDonaldTrump Shock the world!#Trump4President""",5
    1,0,"""@PianoBecca: @realDonaldTrump No Amercian, really, can impact such staggering influence. Stay True! Great and savvy bizman! America: GREAT!",16
    0,2,"""@isabelsimon80: @realDonaldTrump @brentcfritz Donald has as good of a chance as anyone else.. 👍People like him...NO MORE POLITICIAN'S...""",18
    0,0,"""@Knight276: @realDonaldTrump @realJoeMurray America can be great again with trump at the helm. Confidence counts.""",15
    1,0,"""@_Snurk: @realDonaldTrump Love it! Always respect FIGHTERS over overrated loser POLITITIANS!! Inspire GREATNESS! #TRUMP #2016""",14
    1,0,"""@rg_radical99: Counting down the hours to @realDonaldTrump announcement! Ready for America to become great again. Supporting you Mr. Trump.",19


Everthing looks 👍. 
