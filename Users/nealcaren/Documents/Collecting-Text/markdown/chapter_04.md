

```python
import requests
from bs4 import BeautifulSoup
```


```python
url = 'https://raw.githubusercontent.com/nealcaren/Collecting-Text/master/markdown/scrape_list.html'
```


```python
html = requests.get(url).text
```


```python
html
```




    u'<!DOCTYPE html>\n<html>\n\n<head>\n\n<meta charset="utf-8">\n<title>scrape_list</title>\n\n\n<style type="text/css">\nbody {\n  font-family: Helvetica, arial, sans-serif;\n  font-size: 14px;\n  line-height: 1.6;\n  padding-top: 10px;\n  padding-bottom: 10px;\n  background-color: white;\n  padding: 30px; }\n\nbody > *:first-child {\n  margin-top: 0 !important; }\nbody > *:last-child {\n  margin-bottom: 0 !important; }\n\na {\n  color: #4183C4; }\na.absent {\n  color: #cc0000; }\na.anchor {\n  display: block;\n  padding-left: 30px;\n  margin-left: -30px;\n  cursor: pointer;\n  position: absolute;\n  top: 0;\n  left: 0;\n  bottom: 0; }\n\nh1, h2, h3, h4, h5, h6 {\n  margin: 20px 0 10px;\n  padding: 0;\n  font-weight: bold;\n  -webkit-font-smoothing: antialiased;\n  cursor: text;\n  position: relative; }\n\nh1:hover a.anchor, h2:hover a.anchor, h3:hover a.anchor, h4:hover a.anchor, h5:hover a.anchor, h6:hover a.anchor {\n  background: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAA09pVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuMy1jMDExIDY2LjE0NTY2MSwgMjAxMi8wMi8wNi0xNDo1NjoyNyAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENTNiAoMTMuMCAyMDEyMDMwNS5tLjQxNSAyMDEyLzAzLzA1OjIxOjAwOjAwKSAgKE1hY2ludG9zaCkiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6OUM2NjlDQjI4ODBGMTFFMTg1ODlEODNERDJBRjUwQTQiIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6OUM2NjlDQjM4ODBGMTFFMTg1ODlEODNERDJBRjUwQTQiPiA8eG1wTU06RGVyaXZlZEZyb20gc3RSZWY6aW5zdGFuY2VJRD0ieG1wLmlpZDo5QzY2OUNCMDg4MEYxMUUxODU4OUQ4M0REMkFGNTBBNCIgc3RSZWY6ZG9jdW1lbnRJRD0ieG1wLmRpZDo5QzY2OUNCMTg4MEYxMUUxODU4OUQ4M0REMkFGNTBBNCIvPiA8L3JkZjpEZXNjcmlwdGlvbj4gPC9yZGY6UkRGPiA8L3g6eG1wbWV0YT4gPD94cGFja2V0IGVuZD0iciI/PsQhXeAAAABfSURBVHjaYvz//z8DJYCRUgMYQAbAMBQIAvEqkBQWXI6sHqwHiwG70TTBxGaiWwjCTGgOUgJiF1J8wMRAIUA34B4Q76HUBelAfJYSA0CuMIEaRP8wGIkGMA54bgQIMACAmkXJi0hKJQAAAABJRU5ErkJggg==) no-repeat 10px center;\n  text-decoration: none; }\n\nh1 tt, h1 code {\n  font-size: inherit; }\n\nh2 tt, h2 code {\n  font-size: inherit; }\n\nh3 tt, h3 code {\n  font-size: inherit; }\n\nh4 tt, h4 code {\n  font-size: inherit; }\n\nh5 tt, h5 code {\n  font-size: inherit; }\n\nh6 tt, h6 code {\n  font-size: inherit; }\n\nh1 {\n  font-size: 28px;\n  color: black; }\n\nh2 {\n  font-size: 24px;\n  border-bottom: 1px solid #cccccc;\n  color: black; }\n\nh3 {\n  font-size: 18px; }\n\nh4 {\n  font-size: 16px; }\n\nh5 {\n  font-size: 14px; }\n\nh6 {\n  color: #777777;\n  font-size: 14px; }\n\np, blockquote, ul, ol, dl, li, table, pre {\n  margin: 15px 0; }\n\nhr {\n  background: transparent url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAYAAAAECAYAAACtBE5DAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyJpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuMC1jMDYwIDYxLjEzNDc3NywgMjAxMC8wMi8xMi0xNzozMjowMCAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENTNSBNYWNpbnRvc2giIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6OENDRjNBN0E2NTZBMTFFMEI3QjRBODM4NzJDMjlGNDgiIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6OENDRjNBN0I2NTZBMTFFMEI3QjRBODM4NzJDMjlGNDgiPiA8eG1wTU06RGVyaXZlZEZyb20gc3RSZWY6aW5zdGFuY2VJRD0ieG1wLmlpZDo4Q0NGM0E3ODY1NkExMUUwQjdCNEE4Mzg3MkMyOUY0OCIgc3RSZWY6ZG9jdW1lbnRJRD0ieG1wLmRpZDo4Q0NGM0E3OTY1NkExMUUwQjdCNEE4Mzg3MkMyOUY0OCIvPiA8L3JkZjpEZXNjcmlwdGlvbj4gPC9yZGY6UkRGPiA8L3g6eG1wbWV0YT4gPD94cGFja2V0IGVuZD0iciI/PqqezsUAAAAfSURBVHjaYmRABcYwBiM2QSA4y4hNEKYDQxAEAAIMAHNGAzhkPOlYAAAAAElFTkSuQmCC) repeat-x 0 0;\n  border: 0 none;\n  color: #cccccc;\n  height: 4px;\n  padding: 0;\n}\n\nbody > h2:first-child {\n  margin-top: 0;\n  padding-top: 0; }\nbody > h1:first-child {\n  margin-top: 0;\n  padding-top: 0; }\n  body > h1:first-child + h2 {\n    margin-top: 0;\n    padding-top: 0; }\nbody > h3:first-child, body > h4:first-child, body > h5:first-child, body > h6:first-child {\n  margin-top: 0;\n  padding-top: 0; }\n\na:first-child h1, a:first-child h2, a:first-child h3, a:first-child h4, a:first-child h5, a:first-child h6 {\n  margin-top: 0;\n  padding-top: 0; }\n\nh1 p, h2 p, h3 p, h4 p, h5 p, h6 p {\n  margin-top: 0; }\n\nli p.first {\n  display: inline-block; }\nli {\n  margin: 0; }\nul, ol {\n  padding-left: 30px; }\n\nul :first-child, ol :first-child {\n  margin-top: 0; }\n\ndl {\n  padding: 0; }\n  dl dt {\n    font-size: 14px;\n    font-weight: bold;\n    font-style: italic;\n    padding: 0;\n    margin: 15px 0 5px; }\n    dl dt:first-child {\n      padding: 0; }\n    dl dt > :first-child {\n      margin-top: 0; }\n    dl dt > :last-child {\n      margin-bottom: 0; }\n  dl dd {\n    margin: 0 0 15px;\n    padding: 0 15px; }\n    dl dd > :first-child {\n      margin-top: 0; }\n    dl dd > :last-child {\n      margin-bottom: 0; }\n\nblockquote {\n  border-left: 4px solid #dddddd;\n  padding: 0 15px;\n  color: #777777; }\n  blockquote > :first-child {\n    margin-top: 0; }\n  blockquote > :last-child {\n    margin-bottom: 0; }\n\ntable {\n  padding: 0;border-collapse: collapse; }\n  table tr {\n    border-top: 1px solid #cccccc;\n    background-color: white;\n    margin: 0;\n    padding: 0; }\n    table tr:nth-child(2n) {\n      background-color: #f8f8f8; }\n    table tr th {\n      font-weight: bold;\n      border: 1px solid #cccccc;\n      margin: 0;\n      padding: 6px 13px; }\n    table tr td {\n      border: 1px solid #cccccc;\n      margin: 0;\n      padding: 6px 13px; }\n    table tr th :first-child, table tr td :first-child {\n      margin-top: 0; }\n    table tr th :last-child, table tr td :last-child {\n      margin-bottom: 0; }\n\nimg {\n  max-width: 100%; }\n\nspan.frame {\n  display: block;\n  overflow: hidden; }\n  span.frame > span {\n    border: 1px solid #dddddd;\n    display: block;\n    float: left;\n    overflow: hidden;\n    margin: 13px 0 0;\n    padding: 7px;\n    width: auto; }\n  span.frame span img {\n    display: block;\n    float: left; }\n  span.frame span span {\n    clear: both;\n    color: #333333;\n    display: block;\n    padding: 5px 0 0; }\nspan.align-center {\n  display: block;\n  overflow: hidden;\n  clear: both; }\n  span.align-center > span {\n    display: block;\n    overflow: hidden;\n    margin: 13px auto 0;\n    text-align: center; }\n  span.align-center span img {\n    margin: 0 auto;\n    text-align: center; }\nspan.align-right {\n  display: block;\n  overflow: hidden;\n  clear: both; }\n  span.align-right > span {\n    display: block;\n    overflow: hidden;\n    margin: 13px 0 0;\n    text-align: right; }\n  span.align-right span img {\n    margin: 0;\n    text-align: right; }\nspan.float-left {\n  display: block;\n  margin-right: 13px;\n  overflow: hidden;\n  float: left; }\n  span.float-left span {\n    margin: 13px 0 0; }\nspan.float-right {\n  display: block;\n  margin-left: 13px;\n  overflow: hidden;\n  float: right; }\n  span.float-right > span {\n    display: block;\n    overflow: hidden;\n    margin: 13px auto 0;\n    text-align: right; }\n\ncode, tt {\n  margin: 0 2px;\n  padding: 0 5px;\n  white-space: nowrap;\n  border: 1px solid #eaeaea;\n  background-color: #f8f8f8;\n  border-radius: 3px; }\n\npre code {\n  margin: 0;\n  padding: 0;\n  white-space: pre;\n  border: none;\n  background: transparent; }\n\n.highlight pre {\n  background-color: #f8f8f8;\n  border: 1px solid #cccccc;\n  font-size: 13px;\n  line-height: 19px;\n  overflow: auto;\n  padding: 6px 10px;\n  border-radius: 3px; }\n\npre {\n  background-color: #f8f8f8;\n  border: 1px solid #cccccc;\n  font-size: 13px;\n  line-height: 19px;\n  overflow: auto;\n  padding: 6px 10px;\n  border-radius: 3px; }\n  pre code, pre tt {\n    background-color: transparent;\n    border: none; }\n\nsup {\n    font-size: 0.83em;\n    vertical-align: super;\n    line-height: 0;\n}\n* {\n\t-webkit-print-color-adjust: exact;\n}\n@media screen and (min-width: 914px) {\n    body {\n        width: 854px;\n        margin:0 auto;\n    }\n}\n@media print {\n\ttable, pre {\n\t\tpage-break-inside: avoid;\n\t}\n\tpre {\n\t\tword-wrap: break-word;\n\t}\n}\n</style>\n\n\n</head>\n\n<body>\n\n<p><head>\n<title>&quot;My list of sociology journals&quot;</title>\n</head></p>\n\n<h2 id="toc_0">Sociology Journals</h2>\n\n<ul>\n<li><a href="http://asr.sagepub.com">American Sociological Review</a></li>\n<li><a href="http://www.journals.uchicago.edu/toc/ajs/current">American Journal of Sociology</a></li>\n<li><a href="https://sf.oxfordjournals.org">Social Forces</a></li>\n<li><a href="http://socpro.oxfordjournals.org">Social Problems</a></li>\n</ul>\n\n\n\n\n</body>\n\n</html>\n'




```python
from IPython.display import HTML as display_HTML

display_HTML(html)
```




<!DOCTYPE html>
<html>

<head>

<meta charset="utf-8">
<title>scrape_list</title>


<style type="text/css">
body {
  font-family: Helvetica, arial, sans-serif;
  font-size: 14px;
  line-height: 1.6;
  padding-top: 10px;
  padding-bottom: 10px;
  background-color: white;
  padding: 30px; }

body > *:first-child {
  margin-top: 0 !important; }
body > *:last-child {
  margin-bottom: 0 !important; }

a {
  color: #4183C4; }
a.absent {
  color: #cc0000; }
a.anchor {
  display: block;
  padding-left: 30px;
  margin-left: -30px;
  cursor: pointer;
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0; }

h1, h2, h3, h4, h5, h6 {
  margin: 20px 0 10px;
  padding: 0;
  font-weight: bold;
  -webkit-font-smoothing: antialiased;
  cursor: text;
  position: relative; }

h1:hover a.anchor, h2:hover a.anchor, h3:hover a.anchor, h4:hover a.anchor, h5:hover a.anchor, h6:hover a.anchor {
  background: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAA09pVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuMy1jMDExIDY2LjE0NTY2MSwgMjAxMi8wMi8wNi0xNDo1NjoyNyAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENTNiAoMTMuMCAyMDEyMDMwNS5tLjQxNSAyMDEyLzAzLzA1OjIxOjAwOjAwKSAgKE1hY2ludG9zaCkiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6OUM2NjlDQjI4ODBGMTFFMTg1ODlEODNERDJBRjUwQTQiIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6OUM2NjlDQjM4ODBGMTFFMTg1ODlEODNERDJBRjUwQTQiPiA8eG1wTU06RGVyaXZlZEZyb20gc3RSZWY6aW5zdGFuY2VJRD0ieG1wLmlpZDo5QzY2OUNCMDg4MEYxMUUxODU4OUQ4M0REMkFGNTBBNCIgc3RSZWY6ZG9jdW1lbnRJRD0ieG1wLmRpZDo5QzY2OUNCMTg4MEYxMUUxODU4OUQ4M0REMkFGNTBBNCIvPiA8L3JkZjpEZXNjcmlwdGlvbj4gPC9yZGY6UkRGPiA8L3g6eG1wbWV0YT4gPD94cGFja2V0IGVuZD0iciI/PsQhXeAAAABfSURBVHjaYvz//z8DJYCRUgMYQAbAMBQIAvEqkBQWXI6sHqwHiwG70TTBxGaiWwjCTGgOUgJiF1J8wMRAIUA34B4Q76HUBelAfJYSA0CuMIEaRP8wGIkGMA54bgQIMACAmkXJi0hKJQAAAABJRU5ErkJggg==) no-repeat 10px center;
  text-decoration: none; }

h1 tt, h1 code {
  font-size: inherit; }

h2 tt, h2 code {
  font-size: inherit; }

h3 tt, h3 code {
  font-size: inherit; }

h4 tt, h4 code {
  font-size: inherit; }

h5 tt, h5 code {
  font-size: inherit; }

h6 tt, h6 code {
  font-size: inherit; }

h1 {
  font-size: 28px;
  color: black; }

h2 {
  font-size: 24px;
  border-bottom: 1px solid #cccccc;
  color: black; }

h3 {
  font-size: 18px; }

h4 {
  font-size: 16px; }

h5 {
  font-size: 14px; }

h6 {
  color: #777777;
  font-size: 14px; }

p, blockquote, ul, ol, dl, li, table, pre {
  margin: 15px 0; }

hr {
  background: transparent url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAYAAAAECAYAAACtBE5DAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyJpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuMC1jMDYwIDYxLjEzNDc3NywgMjAxMC8wMi8xMi0xNzozMjowMCAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENTNSBNYWNpbnRvc2giIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6OENDRjNBN0E2NTZBMTFFMEI3QjRBODM4NzJDMjlGNDgiIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6OENDRjNBN0I2NTZBMTFFMEI3QjRBODM4NzJDMjlGNDgiPiA8eG1wTU06RGVyaXZlZEZyb20gc3RSZWY6aW5zdGFuY2VJRD0ieG1wLmlpZDo4Q0NGM0E3ODY1NkExMUUwQjdCNEE4Mzg3MkMyOUY0OCIgc3RSZWY6ZG9jdW1lbnRJRD0ieG1wLmRpZDo4Q0NGM0E3OTY1NkExMUUwQjdCNEE4Mzg3MkMyOUY0OCIvPiA8L3JkZjpEZXNjcmlwdGlvbj4gPC9yZGY6UkRGPiA8L3g6eG1wbWV0YT4gPD94cGFja2V0IGVuZD0iciI/PqqezsUAAAAfSURBVHjaYmRABcYwBiM2QSA4y4hNEKYDQxAEAAIMAHNGAzhkPOlYAAAAAElFTkSuQmCC) repeat-x 0 0;
  border: 0 none;
  color: #cccccc;
  height: 4px;
  padding: 0;
}

body > h2:first-child {
  margin-top: 0;
  padding-top: 0; }
body > h1:first-child {
  margin-top: 0;
  padding-top: 0; }
  body > h1:first-child + h2 {
    margin-top: 0;
    padding-top: 0; }
body > h3:first-child, body > h4:first-child, body > h5:first-child, body > h6:first-child {
  margin-top: 0;
  padding-top: 0; }

a:first-child h1, a:first-child h2, a:first-child h3, a:first-child h4, a:first-child h5, a:first-child h6 {
  margin-top: 0;
  padding-top: 0; }

h1 p, h2 p, h3 p, h4 p, h5 p, h6 p {
  margin-top: 0; }

li p.first {
  display: inline-block; }
li {
  margin: 0; }
ul, ol {
  padding-left: 30px; }

ul :first-child, ol :first-child {
  margin-top: 0; }

dl {
  padding: 0; }
  dl dt {
    font-size: 14px;
    font-weight: bold;
    font-style: italic;
    padding: 0;
    margin: 15px 0 5px; }
    dl dt:first-child {
      padding: 0; }
    dl dt > :first-child {
      margin-top: 0; }
    dl dt > :last-child {
      margin-bottom: 0; }
  dl dd {
    margin: 0 0 15px;
    padding: 0 15px; }
    dl dd > :first-child {
      margin-top: 0; }
    dl dd > :last-child {
      margin-bottom: 0; }

blockquote {
  border-left: 4px solid #dddddd;
  padding: 0 15px;
  color: #777777; }
  blockquote > :first-child {
    margin-top: 0; }
  blockquote > :last-child {
    margin-bottom: 0; }

table {
  padding: 0;border-collapse: collapse; }
  table tr {
    border-top: 1px solid #cccccc;
    background-color: white;
    margin: 0;
    padding: 0; }
    table tr:nth-child(2n) {
      background-color: #f8f8f8; }
    table tr th {
      font-weight: bold;
      border: 1px solid #cccccc;
      margin: 0;
      padding: 6px 13px; }
    table tr td {
      border: 1px solid #cccccc;
      margin: 0;
      padding: 6px 13px; }
    table tr th :first-child, table tr td :first-child {
      margin-top: 0; }
    table tr th :last-child, table tr td :last-child {
      margin-bottom: 0; }

img {
  max-width: 100%; }

span.frame {
  display: block;
  overflow: hidden; }
  span.frame > span {
    border: 1px solid #dddddd;
    display: block;
    float: left;
    overflow: hidden;
    margin: 13px 0 0;
    padding: 7px;
    width: auto; }
  span.frame span img {
    display: block;
    float: left; }
  span.frame span span {
    clear: both;
    color: #333333;
    display: block;
    padding: 5px 0 0; }
span.align-center {
  display: block;
  overflow: hidden;
  clear: both; }
  span.align-center > span {
    display: block;
    overflow: hidden;
    margin: 13px auto 0;
    text-align: center; }
  span.align-center span img {
    margin: 0 auto;
    text-align: center; }
span.align-right {
  display: block;
  overflow: hidden;
  clear: both; }
  span.align-right > span {
    display: block;
    overflow: hidden;
    margin: 13px 0 0;
    text-align: right; }
  span.align-right span img {
    margin: 0;
    text-align: right; }
span.float-left {
  display: block;
  margin-right: 13px;
  overflow: hidden;
  float: left; }
  span.float-left span {
    margin: 13px 0 0; }
span.float-right {
  display: block;
  margin-left: 13px;
  overflow: hidden;
  float: right; }
  span.float-right > span {
    display: block;
    overflow: hidden;
    margin: 13px auto 0;
    text-align: right; }

code, tt {
  margin: 0 2px;
  padding: 0 5px;
  white-space: nowrap;
  border: 1px solid #eaeaea;
  background-color: #f8f8f8;
  border-radius: 3px; }

pre code {
  margin: 0;
  padding: 0;
  white-space: pre;
  border: none;
  background: transparent; }

.highlight pre {
  background-color: #f8f8f8;
  border: 1px solid #cccccc;
  font-size: 13px;
  line-height: 19px;
  overflow: auto;
  padding: 6px 10px;
  border-radius: 3px; }

pre {
  background-color: #f8f8f8;
  border: 1px solid #cccccc;
  font-size: 13px;
  line-height: 19px;
  overflow: auto;
  padding: 6px 10px;
  border-radius: 3px; }
  pre code, pre tt {
    background-color: transparent;
    border: none; }

sup {
    font-size: 0.83em;
    vertical-align: super;
    line-height: 0;
}
* {
	-webkit-print-color-adjust: exact;
}
@media screen and (min-width: 914px) {
    body {
        width: 854px;
        margin:0 auto;
    }
}
@media print {
	table, pre {
		page-break-inside: avoid;
	}
	pre {
		word-wrap: break-word;
	}
}
</style>


</head>

<body>

<p><title>&quot;My list of sociology journals&quot;</title></p>

<h2 id="toc_0">Sociology Journals</h2>

<ul>
<li><a href="http://asr.sagepub.com">American Sociological Review</a></li>
<li><a href="http://www.journals.uchicago.edu/toc/ajs/current">American Journal of Sociology</a></li>
<li><a href="https://sf.oxfordjournals.org">Social Forces</a></li>
<li><a href="http://socpro.oxfordjournals.org">Social Problems</a></li>
</ul>




</body>

</html>





```python
soup = BeautifulSoup(html)
```


```python
soup.findAll('li')
```




    [<li><a href="http://asr.sagepub.com">American Sociological Review</a></li>,
     <li><a href="http://www.journals.uchicago.edu/toc/ajs/current">American Journal of Sociology</a></li>,
     <li><a href="https://sf.oxfordjournals.org">Social Forces</a></li>,
     <li><a href="http://socpro.oxfordjournals.org">Social Problems</a></li>]




```python
for item in soup.findAll('li'):
    print item.get_text()
    print item.a.get('href')
```

    American Sociological Review
    http://asr.sagepub.com
    American Journal of Sociology
    http://www.journals.uchicago.edu/toc/ajs/current
    Social Forces
    https://sf.oxfordjournals.org
    Social Problems
    http://socpro.oxfordjournals.org



```python
def save_list_item(item):
    item_dict = {}
    item_dict['text'] = item.get_text()
    item_dict['link'] = item.a.get('href')
    return item_dict
```


```python
items = []
for item in soup.findAll('li'):
    items.append(save_list_item(item))
```


```python
import pandas as pd
```


```python
pd.DataFrame(items)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>link</th>
      <th>text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>http://asr.sagepub.com</td>
      <td>American Sociological Review</td>
    </tr>
    <tr>
      <th>1</th>
      <td>http://www.journals.uchicago.edu/toc/ajs/current</td>
      <td>American Journal of Sociology</td>
    </tr>
    <tr>
      <th>2</th>
      <td>https://sf.oxfordjournals.org</td>
      <td>Social Forces</td>
    </tr>
    <tr>
      <th>3</th>
      <td>http://socpro.oxfordjournals.org</td>
      <td>Social Problems</td>
    </tr>
  </tbody>
</table>
</div>




```python
soup.title
```




    <title>scrape_list</title>




```python
tib_url = 'http://thisibelieve.org/themes/'
```


```python
tib_html = requests.get(tib_url).text
```


```python
tib_soup = BeautifulSoup(tib_html)
```


```python
tib_soup.find_all('li')
```




    [<li class="nav-explore"><h4>Explore</h4>\n<ul>\n<li><a href="http://thisibelieve.org/essays/featured/">Featured Essays</a></li>\n<li><a href="http://thisibelieve.org/feature/">Special Features</a></li>\n<li><a href="http://thisibelieve.org/essays/fifties/">1950s Essays</a></li>\n<li><a href="http://thisibelieve.org/themes/">Browse by Theme</a></li>\n<li><a href="http://thisibelieve.org/podcasts/">Podcast Sign-Up</a></li>\n<li><a href="http://thisibelieve.org/search/">Essay Search</a></li>\n</ul>\n</li>,
     <li><a href="http://thisibelieve.org/essays/featured/">Featured Essays</a></li>,
     <li><a href="http://thisibelieve.org/feature/">Special Features</a></li>,
     <li><a href="http://thisibelieve.org/essays/fifties/">1950s Essays</a></li>,
     <li><a href="http://thisibelieve.org/themes/">Browse by Theme</a></li>,
     <li><a href="http://thisibelieve.org/podcasts/">Podcast Sign-Up</a></li>,
     <li><a href="http://thisibelieve.org/search/">Essay Search</a></li>,
     <li class="nav-participate"><h4>Participate</h4>\n<ul>\n<li><a href="http://thisibelieve.org/in-your-community/">Community Activities</a></li>\n<li><a href="http://thisibelieve.org/communities/worship/">Houses of Worship</a></li>\n<li><a href="http://thisibelieve.org/communities/">Community Resources</a></li>\n<li><a href="http://thisibelieve.org/guidelines/">Essay Writing Guidelines</a></li>\n</ul>\n</li>,
     <li><a href="http://thisibelieve.org/in-your-community/">Community Activities</a></li>,
     <li><a href="http://thisibelieve.org/communities/worship/">Houses of Worship</a></li>,
     <li><a href="http://thisibelieve.org/communities/">Community Resources</a></li>,
     <li><a href="http://thisibelieve.org/guidelines/">Essay Writing Guidelines</a></li>,
     <li class="nav-donate"><h4>Donate</h4>\n<ul>\n<li><a href="https://thisibelieve.org/store/support/">Make a Donation</a></li>\n<li><a href="http://thisibelieve.org/thankyou/">Friends of This I Believe</a></li>\n<li><a href="http://thisibelieve.org/sponsorship/">Become a Sponsor</a></li>\n</ul>\n</li>,
     <li><a href="https://thisibelieve.org/store/support/">Make a Donation</a></li>,
     <li><a href="http://thisibelieve.org/thankyou/">Friends of This I Believe</a></li>,
     <li><a href="http://thisibelieve.org/sponsorship/">Become a Sponsor</a></li>,
     <li class="nav-shop"><h4>Shop</h4>\n<ul>\n<li><a href="https://thisibelieve.org/store/">Store</a></li>\n<li><a href="https://thisibelieve.org/store/account/">My Account/Log Out</a></li>\n<li><a href="https://thisibelieve.org/store/tracking/">Order Tracking</a></li>\n<li><a href="https://thisibelieve.org/store/cart/">Shopping Cart</a></li>\n<li><a href="https://thisibelieve.org/store/checkout/">Checkout</a></li>\n<li><a href="https://thisibelieve.org/store/category/audio-downloads/">Audio Downloads</a></li>\n</ul>\n</li>,
     <li><a href="https://thisibelieve.org/store/">Store</a></li>,
     <li><a href="https://thisibelieve.org/store/account/">My Account/Log Out</a></li>,
     <li><a href="https://thisibelieve.org/store/tracking/">Order Tracking</a></li>,
     <li><a href="https://thisibelieve.org/store/cart/">Shopping Cart</a></li>,
     <li><a href="https://thisibelieve.org/store/checkout/">Checkout</a></li>,
     <li><a href="https://thisibelieve.org/store/category/audio-downloads/">Audio Downloads</a></li>,
     <li class="nav-educators"><h4>Educators</h4>\n<ul>\n<li><a href="http://thisibelieve.org/educators/">Educator Resources</a></li>\n<li><a href="http://thisibelieve.org/common-reading-programs/">Common Reading Programs</a></li>\n<li><a href="http://thisibelieve.org/educators/educator-faq/">Educator FAQ</a></li>\n<li><a href="http://thisibelieve.org/guidelines/">This I Believe Essay-Writing Guidelines</a></li>\n</ul>\n</li>,
     <li><a href="http://thisibelieve.org/educators/">Educator Resources</a></li>,
     <li><a href="http://thisibelieve.org/common-reading-programs/">Common Reading Programs</a></li>,
     <li><a href="http://thisibelieve.org/educators/educator-faq/">Educator FAQ</a></li>,
     <li><a href="http://thisibelieve.org/guidelines/">This I Believe Essay-Writing Guidelines</a></li>,
     <li class="nav-about"><h4>About</h4>\n<ul>\n<li><a href="http://thisibelieve.org/about/">About Us</a></li>\n<li><a href="http://thisibelieve.org/contact/">Contact Us</a></li>\n<li><a href="http://thisibelieve.org/credits/">Credits</a></li>\n<li><a href="http://thisibelieve.org/board-of-directors/">Board of Directors</a></li>\n<li><a href="http://thisibelieve.org/sponsorship/">Become a Sponsor</a></li>\n<li><a href="http://thisibelieve.org/speaking/">Public Speaking</a></li>\n<li><a href="http://thisibelieve.org/faq/">FAQ</a></li>\n<li><a href="http://thisibelieve.org/history/">This  I Believe History</a></li>\n</ul>\n</li>,
     <li><a href="http://thisibelieve.org/about/">About Us</a></li>,
     <li><a href="http://thisibelieve.org/contact/">Contact Us</a></li>,
     <li><a href="http://thisibelieve.org/credits/">Credits</a></li>,
     <li><a href="http://thisibelieve.org/board-of-directors/">Board of Directors</a></li>,
     <li><a href="http://thisibelieve.org/sponsorship/">Become a Sponsor</a></li>,
     <li><a href="http://thisibelieve.org/speaking/">Public Speaking</a></li>,
     <li><a href="http://thisibelieve.org/faq/">FAQ</a></li>,
     <li><a href="http://thisibelieve.org/history/">This  I Believe History</a></li>,
     <li><a class="addiction" href="/theme/addiction/" title="addiction Theme">addiction</a> (1108)</li>,
     <li><a class="afterlife" href="/theme/afterlife/" title="afterlife Theme">afterlife</a> (526)</li>,
     <li><a class="america-patriotism" href="/theme/america-patriotism/" title="America &amp; patriotism Theme">America &amp; patriotism</a> (1674)</li>,
     <li><a class="atheism" href="/theme/atheism/" title="atheism Theme">atheism</a> (272)</li>,
     <li><a class="birth" href="/theme/birth/" title="birth Theme">birth</a> (448)</li>,
     <li><a class="brotherhood-friendship" href="/theme/brotherhood-friendship/" title="brotherhood &amp; friendship Theme">brotherhood &amp; friendship</a> (4466)</li>,
     <li><a class="carpe-diem" href="/theme/carpe-diem/" title="carpe diem Theme">carpe diem</a> (3558)</li>,
     <li><a class="change" href="/theme/change/" title="change Theme">change</a> (3857)</li>,
     <li><a class="charity-service" href="/theme/charity-service/" title="charity &amp; service Theme">charity &amp; service</a> (1723)</li>,
     <li><a class="children" href="/theme/children/" title="children Theme">children</a> (2552)</li>,
     <li><a class="christianity" href="/theme/christianity/" title="Christianity Theme">Christianity</a> (1421)</li>,
     <li><a class="citizenship" href="/theme/citizenship/" title="citizenship Theme">citizenship</a> (512)</li>,
     <li><a class="community" href="/theme/community/" title="community Theme">community</a> (2228)</li>,
     <li><a class="courage" href="/theme/courage/" title="courage Theme">courage</a> (2535)</li>,
     <li><a class="creativity" href="/theme/creativity/" title="creativity Theme">creativity</a> (4660)</li>,
     <li><a class="death" href="/theme/death/" title="death Theme">death</a> (3588)</li>,
     <li><a class="democracy" href="/theme/democracy/" title="democracy Theme">democracy</a> (333)</li>,
     <li><a class="disability" href="/theme/disability/" title="disability Theme">disability</a> (709)</li>,
     <li><a class="discrimination" href="/theme/discrimination/" title="discrimination Theme">discrimination</a> (891)</li>,
     <li><a class="education-knowledge" href="/theme/education-knowledge/" title="education &amp; knowledge Theme">education &amp; knowledge</a> (4558)</li>,
     <li><a class="empathy-compassion" href="/theme/empathy-compassion/" title="empathy &amp; compassion Theme">empathy &amp; compassion</a> (2750)</li>,
     <li><a class="environment" href="/theme/environment/" title="environment Theme">environment</a> (1080)</li>,
     <li><a class="equality" href="/theme/equality/" title="equality Theme">equality</a> (1706)</li>,
     <li><a class="faith-religion" href="/theme/faith-religion/" title="faith &amp; religion Theme">faith &amp; religion</a> (5204)</li>,
     <li><a class="family" href="/theme/family/" title="family Theme">family</a> (13240)</li>,
     <li><a class="fear" href="/theme/fear/" title="fear Theme">fear</a> (1075)</li>,
     <li><a class="forgiveness" href="/theme/forgiveness/" title="forgiveness Theme">forgiveness</a> (1436)</li>,
     <li><a class="freedom" href="/theme/freedom/" title="freedom Theme">freedom</a> (1100)</li>,
     <li><a class="golden-rule" href="/theme/golden-rule/" title="golden rule Theme">golden rule</a> (1170)</li>,
     <li><a class="good-evil" href="/theme/good-evil/" title="good &amp; evil Theme">good &amp; evil</a> (1959)</li>,
     <li><a class="goodness-kindness" href="/theme/goodness-kindness/" title="goodness &amp; kindness Theme">goodness &amp; kindness</a> (3496)</li>,
     <li><a class="government-constitution" href="/theme/government-constitution/" title="government &amp; constitution Theme">government &amp; constitution</a> (570)</li>,
     <li><a class="gratitude" href="/theme/gratitude/" title="gratitude Theme">gratitude</a> (2963)</li>,
     <li><a class="holocaust" href="/theme/holocaust/" title="Holocaust Theme">Holocaust</a> (68)</li>,
     <li><a class="hope" href="/theme/hope/" title="hope Theme">hope</a> (3660)</li>,
     <li><a class="humanism" href="/theme/humanism/" title="humanism Theme">humanism</a> (943)</li>,
     <li><a class="humility" href="/theme/humility/" title="humility Theme">humility</a> (544)</li>,
     <li><a class="humor" href="/theme/humor/" title="humor &amp; laughter Theme">humor &amp; laughter</a> (1553)</li>,
     <li><a class="humor-laughter" href="/theme/humor-laughter/" title="humor &amp; laughter Theme">humor &amp; laughter</a> (1)</li>,
     <li><a class="illness" href="/theme/illness/" title="illness Theme">illness</a> (2215)</li>,
     <li><a class="immigrant" href="/theme/immigrant/" title="immigrant Theme">immigrant</a> (850)</li>,
     <li><a class="injustice" href="/theme/injustice/" title="injustice Theme">injustice</a> (506)</li>,
     <li><a class="integrity" href="/theme/integrity/" title="integrity Theme">integrity</a> (1849)</li>,
     <li><a class="legacy" href="/theme/legacy/" title="legacy Theme">legacy</a> (1709)</li>,
     <li><a class="love" href="/theme/love/" title="love Theme">love</a> (6461)</li>,
     <li><a class="morality" href="/theme/morality/" title="morality Theme">morality</a> (705)</li>,
     <li><a class="music" href="/theme/music/" title="music Theme">music</a> (1379)</li>,
     <li><a class="nature" href="/theme/nature/" title="nature Theme">nature</a> (1771)</li>,
     <li><a class="parenthood" href="/theme/parenthood/" title="parenthood Theme">parenthood</a> (1940)</li>,
     <li><a class="patriotism" href="/theme/patriotism/" title="patriotism Theme">patriotism</a> (1726)</li>,
     <li><a class="peace" href="/theme/peace/" title="peace Theme">peace</a> (820)</li>,
     <li><a class="pets-animals" href="/theme/pets-animals/" title="pets &amp; animals Theme">pets &amp; animals</a> (1459)</li>,
     <li><a class="place" href="/theme/place/" title="place Theme">place</a> (1015)</li>,
     <li><a class="pleasure" href="/theme/pleasure/" title="pleasure Theme">pleasure</a> (2454)</li>,
     <li><a class="popular-culture" href="/theme/popular-culture/" title="popular culture Theme">popular culture</a> (2449)</li>,
     <li><a class="prejudice" href="/theme/prejudice/" title="prejudice Theme">prejudice</a> (753)</li>,
     <li><a class="purpose" href="/theme/purpose/" title="purpose Theme">purpose</a> (2975)</li>,
     <li><a class="question" href="/theme/question/" title="question Theme">question</a> (2338)</li>,
     <li><a class="race" href="/theme/race/" title="race Theme">race</a> (728)</li>,
     <li><a class="respect" href="/theme/respect/" title="respect Theme">respect</a> (2065)</li>,
     <li><a class="responsibility" href="/theme/responsibility/" title="responsibility Theme">responsibility</a> (2345)</li>,
     <li><a class="science" href="/theme/science/" title="science Theme">science</a> (874)</li>,
     <li><a class="self-determination" href="/theme/self-determination/" title="self-determination Theme">self-determination</a> (10797)</li>,
     <li><a class="self-knowledge" href="/theme/self-knowledge/" title="self-knowledge Theme">self-knowledge</a> (15710)</li>,
     <li><a class="setbacks" href="/theme/setbacks/" title="setbacks Theme">setbacks</a> (5325)</li>,
     <li><a class="social-justice" href="/theme/social-justice/" title="social justice Theme">social justice</a> (890)</li>,
     <li><a class="sports" href="/theme/sports/" title="sports Theme">sports</a> (3863)</li>,
     <li><a class="tolerance" href="/theme/tolerance/" title="tolerance Theme">tolerance</a> (1572)</li>,
     <li><a class="values-spirituality" href="/theme/values-spirituality/" title="values &amp; spirituality Theme">values &amp; spirituality</a> (6085)</li>,
     <li><a class="war" href="/theme/war/" title="war Theme">war</a> (840)</li>,
     <li><a class="work" href="/theme/work/" title="work Theme">work</a> (4888)</li>,
     <li class="widgetcontainer donate-widget" id="donate-widget-2"> <h2 class="donate"><a href="/support/">Donate</a></h2>\n<p>The work of <em>This I Believe</em> is made possible by individuals like you.  Please consider making your <a href="/support/">tax-deductible contribution</a> today.</p>\n</li>,
     <li class="widgetcontainer widget_text" id="text-454168392"><h3 class="widgettitle">Top 100 Essays USB Drive</h3>\n<div class="textwidget"><img alt="" class="alignleft" height="75" src="/wp-content/images/USB-Drive-Photo.jpg" width="75"/>This USB drive contains 100 of the top This I Believe audio broadcasts of the last ten years, plus some favorites from Edward R. Murrow's radio series of the 1950s. It's perfect for personal or classroom use! <a href="/store/product/usb-drive/">Click here to learn more.</a></div>\n</li>,
     <li class="widgetcontainer widget_text" id="text-454168416"><h3 class="widgettitle">This week\u2019s essay</h3>\n<div class="textwidget"><a href="/essay/21254/"><img alt="width=" class="alignleft" height="75" src="/images/Essayists/TIBphoto_Powers.jpg"/></a>Mountain climbers learn how to rest-step \u2013 that is, to pause between each step to catch one\u2019s breath at high altitudes. Climber Phil Powers believes this technique of pacing is valuable in everyday life. <a href="/essay/21254/">Click here</a> to read his essay.</div>\n</li>,
     <li class="right_responsive"><script src="//ap.lijit.com/www/delivery/fpi.js?z=343731&amp;u=thisibelieve&amp;width=160&amp;height=600" type="text/javascript"></script></li>,
     <li class="widgetcontainer podcasts-widget" id="podcasts-widget-7"> <h2 class="podcasts"><a href="/podcasts/">Podcasts</a></h2>\n<p>Sign up for our free, weekly podcast of <a href="/podcasts/">featured essays</a>. You can download recent episodes individually, or subscribe to automatically receive each podcast. <a href="/podcasts/">Learn more</a>.</p>\n</li>,
     <li class="widgetcontainer faq-widget" id="faq-widget-2"> <h2 class="faq"><a href="/faq/">FAQ</a></h2>\n<p><a href="/faq/">Frequently asked questions</a> about the This I Believe project, educational opportunities and more...</p>\n</li>,
     <li class="widgetcontainer tib-social-widget" id="tib-social-widget-3"> <h3>Find Us On Social Media</h3>\n<ul class="social-list">\n<li id="findtwitter"><a href="http://twitter.com/thisibelieveinc"></a></li>\n<li id="findpinterest"><a href="http://www.pinterest.com/thisibelieveinc/"></a></li>\n<li id="findfacebook"><a href="http://www.facebook.com/thisibelieveinc"></a></li>\n</ul>\n</li>,
     <li id="findtwitter"><a href="http://twitter.com/thisibelieveinc"></a></li>,
     <li id="findpinterest"><a href="http://www.pinterest.com/thisibelieveinc/"></a></li>,
     <li id="findfacebook"><a href="http://www.facebook.com/thisibelieveinc"></a></li>,
     <li class="nav-explore"><h4>Explore</h4>\n<ul>\n<li><a href="http://thisibelieve.org/essays/featured/">Featured Essays</a></li>\n<li><a href="http://thisibelieve.org/feature/">Special Features</a></li>\n<li><a href="http://thisibelieve.org/essays/fifties/">1950s Essays</a></li>\n<li><a href="http://thisibelieve.org/themes/">Browse by Theme</a></li>\n<li><a href="http://thisibelieve.org/podcasts/">Podcast Sign-Up</a></li>\n<li><a href="http://thisibelieve.org/search/">Essay Search</a></li>\n</ul>\n</li>,
     <li><a href="http://thisibelieve.org/essays/featured/">Featured Essays</a></li>,
     <li><a href="http://thisibelieve.org/feature/">Special Features</a></li>,
     <li><a href="http://thisibelieve.org/essays/fifties/">1950s Essays</a></li>,
     <li><a href="http://thisibelieve.org/themes/">Browse by Theme</a></li>,
     <li><a href="http://thisibelieve.org/podcasts/">Podcast Sign-Up</a></li>,
     <li><a href="http://thisibelieve.org/search/">Essay Search</a></li>,
     <li class="nav-participate"><h4>Participate</h4>\n<ul>\n<li><a href="http://thisibelieve.org/in-your-community/">Community Activities</a></li>\n<li><a href="http://thisibelieve.org/communities/worship/">Houses of Worship</a></li>\n<li><a href="http://thisibelieve.org/communities/">Community Resources</a></li>\n<li><a href="http://thisibelieve.org/guidelines/">Essay Writing Guidelines</a></li>\n</ul>\n</li>,
     <li><a href="http://thisibelieve.org/in-your-community/">Community Activities</a></li>,
     <li><a href="http://thisibelieve.org/communities/worship/">Houses of Worship</a></li>,
     <li><a href="http://thisibelieve.org/communities/">Community Resources</a></li>,
     <li><a href="http://thisibelieve.org/guidelines/">Essay Writing Guidelines</a></li>,
     <li class="nav-donate"><h4>Donate</h4>\n<ul>\n<li><a href="https://thisibelieve.org/store/support/">Make a Donation</a></li>\n<li><a href="http://thisibelieve.org/thankyou/">Friends of This I Believe</a></li>\n<li><a href="http://thisibelieve.org/sponsorship/">Become a Sponsor</a></li>\n</ul>\n</li>,
     <li><a href="https://thisibelieve.org/store/support/">Make a Donation</a></li>,
     <li><a href="http://thisibelieve.org/thankyou/">Friends of This I Believe</a></li>,
     <li><a href="http://thisibelieve.org/sponsorship/">Become a Sponsor</a></li>,
     <li class="nav-shop"><h4>Shop</h4>\n<ul>\n<li><a href="https://thisibelieve.org/store/">Store</a></li>\n<li><a href="https://thisibelieve.org/store/account/">My Account/Log Out</a></li>\n<li><a href="https://thisibelieve.org/store/tracking/">Order Tracking</a></li>\n<li><a href="https://thisibelieve.org/store/cart/">Shopping Cart</a></li>\n<li><a href="https://thisibelieve.org/store/checkout/">Checkout</a></li>\n<li><a href="https://thisibelieve.org/store/category/audio-downloads/">Audio Downloads</a></li>\n</ul>\n</li>,
     <li><a href="https://thisibelieve.org/store/">Store</a></li>,
     <li><a href="https://thisibelieve.org/store/account/">My Account/Log Out</a></li>,
     <li><a href="https://thisibelieve.org/store/tracking/">Order Tracking</a></li>,
     <li><a href="https://thisibelieve.org/store/cart/">Shopping Cart</a></li>,
     <li><a href="https://thisibelieve.org/store/checkout/">Checkout</a></li>,
     <li><a href="https://thisibelieve.org/store/category/audio-downloads/">Audio Downloads</a></li>,
     <li class="nav-educators"><h4>Educators</h4>\n<ul>\n<li><a href="http://thisibelieve.org/educators/">Educator Resources</a></li>\n<li><a href="http://thisibelieve.org/common-reading-programs/">Common Reading Programs</a></li>\n<li><a href="http://thisibelieve.org/educators/educator-faq/">Educator FAQ</a></li>\n<li><a href="http://thisibelieve.org/guidelines/">This I Believe Essay-Writing Guidelines</a></li>\n</ul>\n</li>,
     <li><a href="http://thisibelieve.org/educators/">Educator Resources</a></li>,
     <li><a href="http://thisibelieve.org/common-reading-programs/">Common Reading Programs</a></li>,
     <li><a href="http://thisibelieve.org/educators/educator-faq/">Educator FAQ</a></li>,
     <li><a href="http://thisibelieve.org/guidelines/">This I Believe Essay-Writing Guidelines</a></li>,
     <li class="nav-about"><h4>About</h4>\n<ul>\n<li><a href="http://thisibelieve.org/about/">About Us</a></li>\n<li><a href="http://thisibelieve.org/contact/">Contact Us</a></li>\n<li><a href="http://thisibelieve.org/credits/">Credits</a></li>\n<li><a href="http://thisibelieve.org/board-of-directors/">Board of Directors</a></li>\n<li><a href="http://thisibelieve.org/sponsorship/">Become a Sponsor</a></li>\n<li><a href="http://thisibelieve.org/speaking/">Public Speaking</a></li>\n<li><a href="http://thisibelieve.org/faq/">FAQ</a></li>\n<li><a href="http://thisibelieve.org/history/">This  I Believe History</a></li>\n</ul>\n</li>,
     <li><a href="http://thisibelieve.org/about/">About Us</a></li>,
     <li><a href="http://thisibelieve.org/contact/">Contact Us</a></li>,
     <li><a href="http://thisibelieve.org/credits/">Credits</a></li>,
     <li><a href="http://thisibelieve.org/board-of-directors/">Board of Directors</a></li>,
     <li><a href="http://thisibelieve.org/sponsorship/">Become a Sponsor</a></li>,
     <li><a href="http://thisibelieve.org/speaking/">Public Speaking</a></li>,
     <li><a href="http://thisibelieve.org/faq/">FAQ</a></li>,
     <li><a href="http://thisibelieve.org/history/">This  I Believe History</a></li>]




```python
url = 'https://raw.githubusercontent.com/nealcaren/Collecting-Text/master/markdown/scrape_list_div.html'
```


```python
html2 = requests.get(url).text
```


```python
soup2 = BeautifulSoup(html2)
```

    //anaconda/lib/python2.7/site-packages/bs4/__init__.py:181: UserWarning: No parser was explicitly specified, so I'm using the best available HTML parser for this system ("lxml"). This usually isn't a problem, but if you run this code on another system, or in a different virtual environment, it may use a different parser and behave differently.
    
    The code that caused this warning is on line 174 of the file //anaconda/lib/python2.7/runpy.py. To get rid of this warning, change code that looks like this:
    
     BeautifulSoup([your markup])
    
    to this:
    
     BeautifulSoup([your markup], "lxml")
    
      markup_type=markup_type))



```python
soup2.find_all('div')
```




    [<div class="journal_names">\n<h1>Sociology Journals</h1>\n<ul>\n<li><a href="http://asr.sagepub.com">American Sociological Review</a></li>\n<li><a href="http://www.journals.uchicago.edu/toc/ajs/current">American Journal of Sociology</a></li>\n<li><a href="https://sf.oxfordjournals.org">Social Forces</a></li>\n<li><a href="http://socpro.oxfordjournals.org">Social Problems</a></li></ul>\n</div>,
     <div class="contact">\n<h1>Contact Information</h1>\n<ul>\n<li><a href="mailto:neal.caren@unc.edu">email</a></li>\n<li><a href="http://nealcaren.unc.edu">website</a></li>\n</ul>\n</div>]




```python
soup2.find_all('div', "journal_names")
```




    [<div class="journal_names">\n<h1>Sociology Journals</h1>\n<ul>\n<li><a href="http://asr.sagepub.com">American Sociological Review</a></li>\n<li><a href="http://www.journals.uchicago.edu/toc/ajs/current">American Journal of Sociology</a></li>\n<li><a href="https://sf.oxfordjournals.org">Social Forces</a></li>\n<li><a href="http://socpro.oxfordjournals.org">Social Problems</a></li></ul>\n</div>]




```python

```
