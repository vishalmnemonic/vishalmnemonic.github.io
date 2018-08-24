---
title:  "Word frequency in Moby Dick"
date:   2018-08-11
tags: [python]
excerpt: "datacamp-project"
---

## 1. Tools for text processing
<p><img style="float: right ; margin: 5px 20px 5px 10px; width: 45%" src="https://s3.amazonaws.com/assets.datacamp.com/production/project_38/img/Moby_Dick_p510_illustration.jpg"> </p>
<p>What are the most frequent words in Herman Melville's novel Moby Dick and how often do they occur?</p>
<p>In this notebook, we'll scrape the novel <em>Moby Dick</em> from the website <a href="https://www.gutenberg.org/">Project Gutenberg</a> (which contains a large corpus of books) using the Python package <code>requests</code>. Then we'll extract words from this web data using <code>BeautifulSoup</code>. Finally, we'll dive into analyzing the distribution of words using the Natural Language ToolKit (<code>nltk</code>). </p>
<p>The <em>Data Science pipeline</em> we'll build in this notebook can be used to visualize the word frequency distributions of any novel that you can find on Project Gutenberg. The natural language processing tools used here apply to much of the data that data scientists encounter as a vast proportion of the world's data is unstructured data and includes a great deal of text.</p>
<p>Let's start by loading in the three main python packages we are going to use.</p>


```python
# Importing requests, BeautifulSoup and nltk
from bs4 import BeautifulSoup
import requests,nltk
```

## 2. Request Moby Dick
<p>To analyze Moby Dick, we need to get the contents of Moby Dick from <em>somewhere</em>. Luckily, the text is freely available online at Project Gutenberg as an HTML file: https://www.gutenberg.org/files/2701/2701-h/2701-h.htm .</p>
<p><strong>Note</strong> that HTML stands for Hypertext Markup Language and is the standard markup language for the web.</p>
<p>To fetch the HTML file with Moby Dick we're going to use the <code>request</code> package to make a <code>GET</code> request for the website, which means we're <em>getting</em> data from it. This is what you're doing through a browser when visiting a webpage, but now we're getting the requested page directly into python instead. </p>


```python
# Getting the Moby Dick HTML
r = requests.get('https://s3.amazonaws.com/assets.datacamp.com/production/project_147/datasets/2701-h.htm')

# Setting the correct text encoding of the HTML page
r.encoding = 'utf-8'

# Extracting the HTML from the request object
html = r.text

# Printing the first 2000 characters in html
html[:2000]
```




    '<?xml version="1.0" encoding="utf-8"?>\r\n\r\n<!DOCTYPE html\r\n   PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"\r\n   "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd" >\r\n\r\n<html xmlns="http://www.w3.org/1999/xhtml" lang="en">\r\n  <head>\r\n    <title>\r\n      Moby Dick; Or the Whale, by Herman Melville\r\n    </title>\r\n    <style type="text/css" xml:space="preserve">\r\n\r\n    body { background:#faebd0; color:black; margin-left:15%; margin-right:15%; text-align:justify }\r\n    P { text-indent: 1em; margin-top: .25em; margin-bottom: .25em; }\r\n    H1,H2,H3,H4,H5,H6 { text-align: center; margin-left: 15%; margin-right: 15%; }\r\n    hr  { width: 50%; text-align: center;}\r\n    .foot { margin-left: 20%; margin-right: 20%; text-align: justify; text-indent: -3em; font-size: 90%; }\r\n    blockquote {font-size: 100%; margin-left: 0%; margin-right: 0%;}\r\n    .mynote    {background-color: #DDE; color: #000; padding: .5em; margin-left: 10%; margin-right: 10%; font-family: sans-serif; font-size: 95%;}\r\n    .toc       { margin-left: 10%; margin-bottom: .75em;}\r\n    .toc2      { margin-left: 20%;}\r\n    div.fig    { display:block; margin:0 auto; text-align:center; }\r\n    div.middle { margin-left: 20%; margin-right: 20%; text-align: justify; }\r\n    .figleft   {float: left; margin-left: 0%; margin-right: 1%;}\r\n    .figright  {float: right; margin-right: 0%; margin-left: 1%;}\r\n    .pagenum   {display:inline; font-size: 70%; font-style:normal;\r\n               margin: 0; padding: 0; position: absolute; right: 1%;\r\n               text-align: right;}\r\n    pre        { font-family: times new roman; font-size: 100%; margin-left: 10%;}\r\n\r\n    table      {margin-left: 10%;}\r\n\r\na:link {color:blue;\r\n\t\ttext-decoration:none}\r\nlink {color:blue;\r\n\t\ttext-decoration:none}\r\na:visited {color:blue;\r\n\t\ttext-decoration:none}\r\na:hover {color:red}\r\n\r\n</style>\r\n  </head>\r\n  <body>\r\n<pre xml:space="preserve">\r\n\r\nThe Project Gutenberg EBook of Moby Dick; or The Whale, by Herman Melville\r\n\r\nThis eBook is for the use of anyone anywh'



## 3. Get the text from the HTML
<p>This HTML is not quite what we want. However, it does <em>contain</em> what we want: the text of <em>Moby Dick</em>. What we need to do now is <em>wrangle</em> this HTML to extract the text of the novel. For this we'll use the package <code>BeautifulSoup</code>.</p>
<p>Firstly, a word on the name of the package: Beautiful Soup? In web development, the term "tag soup" refers to structurally or syntactically incorrect HTML code written for a web page. What Beautiful Soup does best is to make tag soup beautiful again and to extract information from it with ease! In fact, the main object created and queried when using this package is called <code>BeautifulSoup</code>. After creating the soup, we can use its <code>.get_text()</code> method to extract the text.</p>


```python
# Creating a BeautifulSoup object from the HTML
soup = BeautifulSoup(html,'html.parser')

# Getting the text out of the soup
text = soup.get_text()

# Printing out text between characters 32000 and 34000
text[32000:34000]
```




    'r which the beech tree\r\n        extended its branches.” —Darwin’s Voyage of a Naturalist.\r\n      \n\r\n        “‘Stern all!’ exclaimed the mate, as upon turning his head, he saw the\r\n        distended jaws of a large Sperm Whale close to the head of the boat,\r\n        threatening it with instant destruction;—‘Stern all, for your\r\n        lives!’” —Wharton the Whale Killer.\r\n      \n\r\n        “So be cheery, my lads, let your hearts never fail, While the bold\r\n        harpooneer is striking the whale!” —Nantucket Song.\r\n      \n\r\n     “Oh, the rare old Whale, mid storm and gale\r\n     In his ocean home will be\r\n     A giant in might, where might is right,\r\n     And King of the boundless sea.”\r\n      —Whale Song.\r\n\n\n\n\n\n \n\n\n\n\n\r\n      CHAPTER 1. Loomings.\r\n    \n\r\n      Call me Ishmael. Some years ago—never mind how long precisely—having\r\n      little or no money in my purse, and nothing particular to interest me on\r\n      shore, I thought I would sail about a little and see the watery part of\r\n      the world. It is a way I have of driving off the spleen and regulating the\r\n      circulation. Whenever I find myself growing grim about the mouth; whenever\r\n      it is a damp, drizzly November in my soul; whenever I find myself\r\n      involuntarily pausing before coffin warehouses, and bringing up the rear\r\n      of every funeral I meet; and especially whenever my hypos get such an\r\n      upper hand of me, that it requires a strong moral principle to prevent me\r\n      from deliberately stepping into the street, and methodically knocking\r\n      people’s hats off—then, I account it high time to get to sea as soon\r\n      as I can. This is my substitute for pistol and ball. With a philosophical\r\n      flourish Cato throws himself upon his sword; I quietly take to the ship.\r\n      There is nothing surprising in this. If they but knew it, almost all men\r\n      in their degree, some time or other, cherish very nearly the same feelings\r\n      towards the ocean with me.\r\n    \n\r\n      Ther'



## 4. Extract the words
<p>We now have the text of the novel! There is some unwanted stuff at the start and some unwanted stuff at the end. We could remove it, but this content is so much smaller in amount than the text of Moby Dick that, to a first approximation, it is okay to leave it in.</p>
<p>Now that we have the text of interest, it's time to count how many times each word appears, and for this we'll use <code>nltk</code> – the Natural Language Toolkit. We'll start by tokenizing the text, that is, remove everything that isn't a word (whitespace, punctuation, etc.) and then split the text into a list of words.</p>


```python
# Creating a tokenizer
from nltk.tokenize import RegexpTokenizer
tokenizer = RegexpTokenizer('\w+|\$[\d\.]+|\S+')

# Tokenizing the text
tokens = tokenizer.tokenize(text)

# Printing out the first 8 words / tokens
tokens[:8]
```




    ['Moby', 'Dick', ';', 'Or', 'the', 'Whale', ',', 'by']



## 5. Make the words lowercase
<p>OK! We're nearly there. Note that in the above 'Or' has a capital 'O' and that in other places it may not, but both 'Or' and 'or' should be counted as the same word. For this reason, we should build a list of all words in <em>Moby Dick</em> in which all capital letters have been made lower case.</p>


```python
# A new list to hold the lowercased words
words = []

# Looping through the tokens and make them lower case
for word in tokens:
    words.append(word.lower())
# Printing out the first 8 words / tokens
words[:8]
```




    ['moby', 'dick', ';', 'or', 'the', 'whale', ',', 'by']



## 6. Load in stop words
<p>It is common practice to remove words that appear a lot in the English language such as 'the', 'of' and 'a' because they're not so interesting. Such words are known as <em>stop words</em>. The package <code>nltk</code> includes a good list of stop words in English that we can use.</p>


```python
# Getting the English stop words from nltk
from nltk.corpus import stopwords
sw=stopwords.words('english')

# Printing out the first eight stop words
sw[:8]
```




    ['i', 'me', 'my', 'myself', 'we', 'our', 'ours', 'ourselves']



## 7. Remove stop words in Moby Dick
<p>We now want to create a new list with all <code>words</code> in Moby Dick, except those that are stop words (that is, those words listed in <code>sw</code>). One way to get this list is to loop over all elements of <code>words</code> and add each word to a new list if they are <em>not</em> in <code>sw</code>.</p>


```python
# A new list to hold Moby Dick with No Stop words
words_ns = []

# Appending to words_ns all words that are in words but not in sw
for word in words:
    if word not in sw:
        words_ns.append(word)

# Printing the first 5 words_ns to check that stop words are gone
words_ns[:5]
```




    ['moby', 'dick', ';', 'whale', ',']



## 8. We have the answer
<p>Our original question was:</p>
<blockquote>
  <p>What are the most frequent words in Herman Melville's novel Moby Dick and how often do they occur?</p>
</blockquote>
<p>We are now ready to answer that! Let's create a word frequency distribution plot using <code>nltk</code>. </p>


```python
# This command display figures inline
%matplotlib inline
from nltk import FreqDist


# Creating the word frequency distribution
freqdist = FreqDist(words_ns)


# Plotting the word frequency distribution
freqdist.plot(50, cumulative=True)
```


![png](/rajkstats.github.io/images/jupyter/ipynb/notebook_15_0.png)


## 9. The most common word
<p>Nice! The frequency distribution plot above is the answer to our question. </p>
<p>The natural language processing skills we used in this notebook are also applicable to much of the data that Data Scientists encounter as the vast proportion of the world's data is unstructured data and includes a great deal of text. </p>
<p>So, what word turned out to (<em>not surprisingly</em>) be the most common word in Moby Dick?</p>


```python
# What's the most common word in Moby Dick?
most_common_word = 'whale'
```
