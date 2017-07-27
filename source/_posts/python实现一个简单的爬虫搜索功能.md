---
title: python实现一个简单的爬虫搜索功能
date: 2015-07-26 15:39:32
tags: python
---

精简的爬虫搜索
<!--more-->

``` python
from html.parser import HTMLParser  
from urllib.request import urlopen  
from urllib import parse
class LinkParser(HTMLParser):

    def handle_starttag(self, tag, attrs):
        if tag == 'a':
            for (key, value) in attrs:
                if key == 'href':

                    newUrl = parse.urljoin(self.baseUrl, value)
                    self.links = self.links + [newUrl]

    def getLinks(self, url):
        self.links = []
        self.baseUrl = url
        response = urlopen(url)
        if response.getheader('Content-Type')=='text/html; charset=UTF-8':
            htmlBytes = response.read()
            htmlString = htmlBytes.decode("utf-8")
            self.feed(htmlString)
            return htmlString, self.links
        else:
            return "",[]

def spider(url, word, maxPages):  
    pagesToVisit = [url]
    numberVisited = 0
    foundWord = 4
    while numberVisited < maxPages and pagesToVisit != [] and not foundWord:
        numberVisited = numberVisited +1
        url = pagesToVisit[0]
        pagesToVisit = pagesToVisit[1:]
        try:
            print(numberVisited, "搜索页:", url)
            parser = LinkParser()
            data, links = parser.getLinks(url)
            #print("data:",links)
            pagesToVisit = pagesToVisit + links
            if data.find(word)>-1:
                foundWord = True
                pagesToVisit = pagesToVisit + links
                print(" **成功!**")
        except:
            print(" **错误!**")
    if foundWord:
        print("该关键字", word, "搜索失败", url)
    else:
        print("没有找到任何有关的网页")
spider("http://yuedu.fm/","夏洛特",100)
```
