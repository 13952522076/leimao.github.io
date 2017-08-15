---
layout: post
title: WikiMidas and MoeGirlMidas
excerpt: "MediaWiki Webpage Crawler"
modified: 2017-08-15T14:17:25-04:00
categories: project
tags: [Data Crawling]
image:
  teaser: 
comments: true
share: true
---

### Introduction

[WikiMidas](https://github.com/leimao/WikiMidas) is a Python spidering API wrapper program specifically designed for data crawling from [MediaWiki APIs](https://www.mediawiki.org/). It could be used to retrieve information from [Wikipedia](https://en.wikipedia.org/wiki/Main_Page) and [MoeGirl](https://zh.moegirl.org/Mainpage) which uses MediaWiki as their APIs. MoeGirl is more focused on the articles in the finctional characters' in anime. [MoeGirlMidas](https://github.com/leimao/MoeGirlMidas), developed based on WikiMidas, are designed specifically to retrieve characters' data from MoeGirl.

<br />

I named the two programs after "midas". Because I like the "feel" that you touch something and something becomes useful.

<figure>
  <img src="{{ site.url }}/images/projects/2017-08-15-WikiMidas-MoeGirlMidas/midas.gif"/>
</figure>

### Learning Experience

I have little experience in writing a program to retrieve data from internet by myself. So, In addition to some practice program experience in MOOC courses ([Using Python to Access Web Data](https://www.coursera.org/learn/python-network-data)), this is the first time I formally write a data crawler by myself, and I did suffer a lot of problems in order to make it work.

To achieve the goal of data crawler, there are several aspects that one need to be at least familiar with:
* html language
* website API usage
* regular expression
* some third-party data crawler helper libraries

However, I have little prior knowledge about html language, except the basic concept that the html language wraps information in brackets, and have little experience in communicating with the website APIs. I also learned that I would need to use regular expression, a important practical concept in which I only have little knowlege without too much practice, to extract useful informations. The whole process is a little bit painful. But I think I have at least gained some practical experience in data crawling.

### Useful Materials and Tools

#### [wiki-api](https://github.com/richardasaurus/wiki-api)
This is a very good data crawler learning material for me. It had a small problem in getting the summary from Wikipedia articles, which was fixed in my own program.

#### [re](https://docs.python.org/2/library/re.html)
This libray allows user to use regular expression to do matching operations to extract useful information from texts. The usage of this library might be easy. But the key is to understand regular expression and use the correct regular expression to match the thing you are looking for.

```python
# To compile a message to regular expression
empty_regex = re.compile(message)
# To remove a string that matches certain regular expression
text = re.sub(regex, '', text, flags)
```

#### [pyquery](https://pythonhosted.org/pyquery/)
This library is a very power tool find certain nodes containing information of interests in the html file.

```python
# Make PyQuery object from a html file
# 'response_content' is a html file
html = pq(response_content)
# Query certain node
html('body')
# Query certain class
html('.mw-content-ltr')
# Query certain id
html('#firstHeading')
# Find all the subnodes
html('body').find('h2')
html('body').find('h2, p')
# Get attributs
html('body').find('.image').find('img').attr('src')
# Get text
html('#firstHeading').text()
```

#### [requests](http://docs.python-requests.org/en/master/)

This is a libray which is used to communicate with website APIs in the Python program.

```python
# To work with website API
# 'url' is the website API url and 'params' is the parameters for certain API function
response = requests.get(url, params)
# the response might be in different formats that the user specified
# the response is in json format
response_json = response.json()

# To get the html content of the webpage
# 'url' is the webpage url
response = requests.get(url)
response_content = response.content
```

### Choices during Development

* How to retrieve webpage content? API or Webpage Html?
The MediaWiki provides API to retrieve webpage content using its internal parser tools, such as [MediaWiki TextExtracts](https://www.mediawiki.org/wiki/Extension:TextExtracts). So this raises a question. Should we use the API internal tools or parse the content by ourselves?
<br />
<br />
I think my answer might be the latter one, parsing the content by ourselves. For websites using MediaWiki API, the optional parser tools might not be installed. Even if it is installed, from my preliminary test of the tool, I found it not flexible enough to achieve my relatively more sophisticated purposes, such as getting image urls, getting data from tables, etc.
<br />
Parsing by ourselves, although might be labor entensive, could be flexible to extract almost any data of interest using a combination of tools.

### MoeGirlMidas Demo


MoeGirlMidas was wrapped into a class object called "MoeGirlAPI". To use MoeGirlMidas in your Python program:

```python
from moegirlmidas import MoeGirlAPI
moegirl = MoeGirlAPI()
# To search for certain query in MoeGirl
# term is the keyword you are interested in; limit is the maximum number of returns
# the return is a list of relavant article titles found
search_result = moegirl.search(term = '���ѻ�֯', limit = 50)
for item in search_result:
    print(item)
```
```
>>>
���ѻ�֯
```
```python
# To retrieve article from MoeGirl
# title is the title of the article you want to retrieve from MoeGirl
# the return is article-class object containing 'heading', 'summary', 'content', 'properties', 'image', 'references' and 'url'
article = moegirl.retrieve(title = '���ѻ�֯')
# To view the article content
print article.heading
```
```
>>>
���ѻ�֯
```
```python
print article.image
```
```
>>> 
https://img.moegirl.org/common/thumb/f/fd/Kanzaki_Kaori.jpg/250px-Kanzaki_Kaori.jpg
```
```python
print article.summary
```
```
>>>
���ѻ�֯����С˵��ħ������Ŀ¼��������������������������Ϸ����Ʒ�ĵǳ���ɫ��
```
```python
print article.content
```
```
>>>
���ѻ�֯

���ѻ�֯ ����С˵��ħ������Ŀ¼��������������������������Ϸ����Ʒ�ĵǳ���ɫ��

Ŀ¼

������Ƭ

ְҵ��ħ��ʦ���׶�����ǰ5��

ħ������Salvere000�������⣺���޷�����֮�����Ԯ�֣�

������

Ӣ����̱�Ҫ֮��̻��ħ��ʦ��18�꣬�׶�����ǰ���ħ��ʦ�������ϲ���20λ��ʥ��֮һ�����ʽʮ����̵� Ů�̻� ����T-shirt��ţ�п�ĸ�� ���� ��Ů����ɱ���� ��ǧԪ ���������͡�Ψ������

��Ϊ�����Լ�ǿ����������Ὣ�޹������˾��룬һ��ѡ���뿪���ʽ��ӵ���š������������������֮�֡��Ĳ������񣬴Ӳ�ɱ�ˣ�Ҳ�����޷������������Լ���ǰ��ɱ��

���� ��β �� ��ɫ���� �������ʡ���ĸ��Ƥ��������Ů�����˺�����ò���ڵ����������ŹŴ� ��͸��� �ľ����Ը�����ע������������ȫ��ѹ�ڳ����侲�ı���֮�¡�

���ϴ��������������T������ʱҲ������ţ�в������ף��������Ǵ���ţ�п㡣���������ֱ۵Ĳ��������һ���жϣ��෴�أ�ţ�п����Ǵ����ȸ�����ʼ��������е��� ���������� �� ���ߵ� �������ѵķ�װ�ܡ�ɫ�项��ʵ���ϣ����Ҳ��Գ�����Ϊ������ʽ����ϵ���Ҫ�����Ѳ����ǹ���Ҫ��¶�������ߡ� ������Ϊ��ǧԪ������������ˣ��º��ʾҪ�����岹���� �ڴ�������֮ǰ��鿴ħ������Ŀ¼���ص�

����֮��

����������Ĳ����������������һ���ӳ�����ʼ���൱���˵��ˡ�����ǰ�ͱ�ָ��Ϊ�����ʽʮ����̡���Ů�̻ʣ�ӵ�м�ʹ����Ŭ��Ҳ���Ի�óɹ��Ĳ��ܣ�ʲô���������Ϳ��Եõ�λ��Ⱥ�����ĵ��������ÿ�춼�ڷ��������Լ������ϲ����ľ�ϲ�������⵽��ɱ����Ҳ���Խ��Ҵ��ӵ���������ɵ���ƫ������ը������߱�ը��Ҳ�����漣�Ƶغ������ˡ�

���ǣ������޷�ԭ���Լ�����Ϊ��Χ���������ҡ��ġ����ˡ���������Ϊ�����Լ�ǿ����������Ὣ�޹������˾��룬һ��ѡ���뿪���ʽ��Ϊ�˱�����ߵ��ˣ�����ֻ��ѹ��ס���������һ������飬�Լ�ѡ���˹¶���

ħ����ʽ

����

���Ѳ���������˿��ʩչ����ն�����Կ��������ٶ�˺�Ѷ��֣�����ǰ��һ��ȫ�����������ѣ�����˵�Ƿǳ��ֲ�����ʽ������ʱ���ѵİε�����Ϊ�������β��ݸ�˿���֣���ʵ�������Ѹ���û�аε���ֻ�ǽ����ӵ�������΢�γ���Ȼ�����ƻ�ȥ���ˡ����ٶȿ�����һ˲��ɱ���ߴΣ��ʱ�������˲ɱ����
���á��������ĸ�˿����������޶ȵ�չ����ͨ�����ѿ��������Խ��и߿�����ʱ�ļ��١�

Ψ��

����ʹ�á������ߵ����İε�������ʮ�̵ֽ���ʽ���еĻ����÷����ʽ�������ʽ���еĻ����������ʽ�������ʽ���еĻ�����ʮ�ֽ���ʽ  ���������������ʵ������˴˵����㣬���ӳ���ȫ�ƻ����Ķ�һ�޶�������ʽ���ǡ�Ψ���������ض��ڽ��ػ�ʹ�������������ʽ������������һ��������ʹҲ���жϡ�
��Ψ��������˲��ʹ�á���֮�ӡ���������ʽ����Ϊ��ʥ�ˡ����ѿ���ʹ�ò��ֵġ���֮�ӡ�����������Ψ��������ʱ�����ѱ�ǿ���������������ᳬ�������ܲ��ݵ��˶��������Բ�����������״̬�³�ʱ�����ս����������ˣ����ѵġ�Ψ������Ҫ������һ�����ܾ�ʤ���İε���������Դ���ʹ����֮�����Ӱ��кͺ�֮ˮ��ʱ�򣬶���Ϊ��ʱ��ʹ����Ϊ��ʥ�ˡ���������ʹ������ٱ�����Ե  ��

�ⲿ������ע��

�����ģ� �ٷ�������ܣ� http://www.project-index.net/contents/hp0006/index00840000.html

�����˵�

```
```python
for key in article.properties.keys():
    print key, ':', article.properties[key], '\n'
```
```
>>>
��ɫ : �ڷ� 

���Χ : ������� 

ͫɫ : ��ͫ 

���� : ���پ� 

�������� : Ӣ����̱�Ҫ֮��̻ᣬ���ʽʮ����� 

relatives : �������� �� ��ٿ�˿ �� ������˹ͼ���� �� ʷ��������Ŭ˹ 

������� : �ձ� 

��� : ���ͷ 

���� : ���� �� ���󤶤� �� �� �� ������ �� ��Kanzaki Kaori�� 

���� : 18�� 

��� : 175cm���� 

�ȵ� : ����β �� ���� �� ���� ��ʥ�ˡ�Ů���ߡ� ��͸��� �������� 
```

The Python script could also be run directly in the shell (the shell might have conflicts with Chinese characters):

To search article
```shell
python moegirlmidas.py -s "���ѻ�֯"
```

To retrieve article
```shell
python moegirlmidas.py -r "���ѻ�֯"
```
The sample codes above could also be run in a Jupyter Notebook. See the run results [here](https://github.com/leimao/MoeGirlMidas/blob/master/sample_code.ipynb).

