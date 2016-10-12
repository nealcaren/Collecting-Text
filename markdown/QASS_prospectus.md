### *Collecting Digital Texts* by Neal Caren  
#### Book Proposal for Sage's Quantitative Applications in the Social Sciences series (QASS)

### PROJECT OVERVIEW
*Collecting Digital Texts* will be a thorough introduction to the practice of gathering and storing text data for social scientists. Overly the last decade, the variety and types of text data available to researchers have exploded. This includes both contemporary data, such as that from website and social media platforms, but also historical data, from digitized interviews to 19th century newspapers. While social scientists have begun to analyzed and publish this data in our top political science, psychology, and sociology journals, to date, their is text available to instruct current students and faculty members on how to start collecting and managing this information. Instead, researchers have been forced to cobble together materials primarily designed for computer programers and translate that themselves. This text propose to be the definitive guide for social scientists interested in collecting their own digital text corpus.

This book will provide a sophisticated introduction to the sources and methods available to social scientists for collecting textual data. This includes scraping web sites, accessing APIs, and working with CSV, JSON and XML files. The book will have an applied approach, with each chapter introducing the topic and then working through several examples of increasing complexity. The book will also include a discussion of the legal and ethical issues associated with harvesting text. The code in the book, and available separately on a website, will all be in Python, which has several advantages. First, Python is a free, open-source, well-established computer programing language. Second, Python is by far the most common language for web scraping and collecting text, and for data science more generally. Finally, Python code is fairly is interpretable, even for those who are not familiar with the language. Therefore, the examples in the book can be easily ported to other languages. The book will include a brief appendix that introduces users to Python. 
### MARKET & COURSE BACKGROUND
The primary market for *Collecting Digital Texts* is graduate courses in computational social science or the digital humanities. It would also be appropriate as recommended books in research methods course, or as  required book as fields increasingly view digital texts as a core source of quantitative data. Finally, the book would be appropriate for researchers attempting to collect text as data outside of course work. As a relatively novel data source, many advanced graduate students or faculty members have an interest in the area, but no current overview text. Major social science fields that are using text data including political science and sociology, along with subfields within pyschology. Because the text focuses on the collection and management of texts, rather than analysis, it will be relevant to non-social scientists, particularly those in the digital humanities. 

### THE BOOK
The goal of this book is to provide the reader with the knowledge and tools to collect his or her own digital text data. This text seeks to introduce graduate students and other scholars interested in analyzing a text corpus the necessary methods for collecting and organizing this data. This books seeks to expand our research method toolkit, as graduate research methods training in the social science ignores the collection of text data. The books seeks to be an comprehensive introduction to the field, such that scholars will be able to use it order to collect data that will be analyzed in scholarly articles and manuscripts. 

The book will serve as a practical introduction to the field of digital data collection. As such each chapter will both explain the type of data and method being used and provide detailed examples of how the technique works. Chapters will provide both code snippets to introduce and explain and then demonstrate how the snippets can be linked together in a more complex, real-life example to show the entire data collection workflow. This book will be unique in that it is targeted toward social scientists and digital humanities scholars. In practice, this means that not only will the examples be relevant, but also that book will focus on the methods most relevant for producing scientific data; the language will be pitched at those with some statistical training but little computer science background; and with the understanding that the goal is to produce a dataset for subsequent scientific analysis. 

I anticipate that the book will be 130-150 pages. Each chapter will include a few screen shots along with numerous code examples.  


There will be an accompanying website with all of the code available as Jupyter notebooks. This notebooks, which can include both explanatory text and code, can be viewed on the web and downloaded. When viewed, they include both the code and the returned results. When downloaded, the code can be modified and run by the user. Additionally, I will construct a handful of sample HTML pages that can be scraped. This ensures that the scraping code provided will not break as third-party websites are likely to change their code over time.  



### Competition
Several books are currently available that 

* Mitchell, Ryan. 2015. *Web Scraping with Python: Collecting Data from the Modern Web* O'Reilly Media, Inc. 255 pages.  
	This is an excellent book that covers much of the same material as the proposed book. It covers gathering data from the web and APIs, including both basic and more advanced techniques. *Web Scraping with Python* also covers some topics not in the proposed manuscript, such as SQL databases and text analysis. The central different between this book and the proposed book is audience and assumed knowledge. This book is primarily aimed at the Python developer community and not academic researchers. As such, it includes chapters such as, "Testing Your Website with Scrapers." More importantly, the book rushes through the details of some things that programers are likely to be familiar with, such as CSS, HTML, JavaScript, and lambda expressions, but that the average social scientist is unfamiliar. As such, it is unlikely to be adopted by instructors in the social science. 
     
* Russell, Matthew A. 2013. *Mining the Social Web: Data Mining Facebook, Twitter, LinkedIn, Google+, GitHub, and More, 2nd Edition.* O'Reilly Media, Inc.  
	Like *Web Scraping with Python*, this is an O'Reilly book on collected and analyzing web data. This book is primarily for corporate users with a 'basic programming background' (page xiii). It also has a strong emphasis on analysis, with the majority of chapters focusing on the multiple ways that online data can be analyzed. 

* Munzert, Simon, Christian Rubba, and Peter Meißner. 2015. *Automated Data Collection with R: A Practical Guide to Web Scraping and Text Mining* Wiley.  
	This is an excellent volume on using R to collect and analyze digital text data. As suggested by the title, the book's primary focus is teach R users which commands are appropriate for gathering data from the web.   
	
###Table of Contents

1. **Collecting Text**  
    The first chapter will highlight recent, interesting and exciting work that scholars have done with text. It will provide an overview of the major sources of textual data. Finally, it will include a preview of the book chapters and explain why scholars should work with the Python computing language. 
     
2. **Importing and working with text**  
	This brief chapter will provide an introduction to working with text. This includes reading text locally from a CSV file, a single text file, or an entire directory. It will also introduce basic text manipulations and text encodings. Finally, it will explain how to save text data for later use in Python or in statistical packages. 
	
3. **APIs: When they Want to give you the data.**
	Web application programming interfaces (APIs) are a popular method for providing information over the web. This chapter will introduce APIs along with the basic methods for making APIs calls. I will also cover the major ways that data are provided through APIs, including JSON and XML file formats. Finally, I will give examples of more complex API calls, including authentication and streaming data.  
 
4. **Scraping Web Pages**  
	This chapter introduces the basics of scraping data from web pages. This includes an introduction to the structure of modern web pages, including HTML and CSS. The chapter will introduce scraping data from tables and by HTML tags. It will also cover techniques for uncovering which URL has the data of interest, looping over multiple pages, and best practices for saving and exporting the data. 

5. **Advanced Scraping techniques**  
	This chapter expands on the previous chapter to introduce a number of ways to scrape more complicated pages and create a more robust scraping code. This includes handling cookies, redirects, header spoofing, POSTing, and authenticating. Additionally, I will cover ways to find the data when it missing from the HTML and ways to make code robust to the variety of errors likely to occur.
	
6. **Other Sources of Data**
	Text data can also be imported from non-web sources, such as PDFs and Microsoft Word documents. This chapter discusses techniques for importing text from DOCX and PDF files, including optical character reading (OCRing). It also reviews ways to batch process documents. 
	
6. **Ethics, Method and Reproducibility**  
	The final chapter will review the ethical and legal issues associated with web scraping. This includes API agreements, robots.txt files, along with the rights of publishers and writers. It will also discuss when IRB approval is likely.  The chapter will also review important methodological questions scholars need to think about, including the data generating process, sampling and case selection. Finally, the chapter will review best practices for making the code and data accessible to other scholars.  

    **Appendix. Basics of installing and working with Python***  
    A short appendix details the ways to install and update Python, including all the requisite python libraries. It will also provide a quick introduction to the Python programming language.  


     
### Biographical Information
Neal Caren is an Associate Professor of Sociology at the University of North Carolina, Chapel Hill. His research interests center on the quantitative analysis of protest and social movements... His work has been published in the _American Sociological Review_, _Social Forces_, _Social Problems_, and the _Annual Review of Sociolgy_.  The data in many of his publications has been either scraped from the web, downloaded using APIs, or otherwise involved collected and analyzing texts. He is the author of a well-used publicly available script for converting Lexis-Nexis article downloads into  a CSV file. 
 For several years, he has run a graduate workshop on computational social science and digital data collection, has given external workshops on the topic, and has many several tutorials available online. He is also the editor of the social movements journal _Mobilization_.
