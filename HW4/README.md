# hw4
## 針對文章作文本分析--以BBC news為例

近期世界各地發生多起傷人事件
故我想針對槍枝、戰爭等報導進行文本分析
### 第一篇文章--關於德州隨機槍擊案
![](https:// "title")
### 第二篇文章--關於蘇丹首都的戰事
![](https:// "title")

## 文字清洗
由於文章是直接從外網上抓下來，故有許多影響分析的內容，如`IMAGE SOURCE,EPA-EFE/REX/SHUTTERSTOCK
Image caption,`等關於圖片的資訊，或是如`\n`換行符，故我使用openAI的API進行重點整理及文字清洗，範例如下:   
```
def chatgptfn(sub_list):
    result = ''
    print(sub_list)
    response = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "You are an assistant"},
            {"role": "user", "content": f"{sub_list} :give me a summary"}
        ]
    )
    for choice in response.choices:
    result += choice.message.content
    return result
```
將文本讀成字串並導入式子，
```
# 資料清洗
data1 = [chatgptfn(itemlist1[i:i+3200]) for i in range(0, len(itemlist1), 3200)]
data2 = [chatgptfn(itemlist2[i:i+3200]) for i in range(0, len(itemlist2), 3200)]
```
即可得到乾淨的文本
> "Fighting has resumed in Sudan's capital, Khartoum, despite an extension to a recent ceasefire, sparking concerns that the conflict will escalate. The violence has erupted between the regular army and paramilitary rivals, with more than 500 people reportedly killed since 15 April. Millions remain trapped in the capital, facing food shortages, with foreign nations evacuating their citizens.(僅部分內容)

## 創建詞袋
再來我將文檔的內容單次逐個進行分析，範例如下:
```
# 創建詞袋
texts = [[word for word in document.lower().split()] for document in papers]
dictionary = corpora.Dictionary(texts)
corpus = [dictionary.doc2bow(text) for text in texts]
```
可發現兩篇文章的詞都被分離出來並放入各自的list
> [['fighting',
  'has',
  'resumed',
  'in',
  "sudan's",
  'capital,',
  'khartoum,',
  'despite',
  'an',
  'extension',
  'to',
  'a',
  'recent',
  'ceasefire,',
  'sparking',
  'concerns',
  'that',
  'the',
  'conflict',
  'will',
  'escalate.',
  'the',
  'violence',
  'has',
  'erupted',
...
  'and',
  'injured',
  'in',
  'the',
  'attack.']] (僅部分內容)

## wordcloud製作
因為是對英文文本做文字雲，故使用nltk作為單詞分割的工具，並導入WordCloud套件:
```
import nltk
from wordcloud import WordCloud
text = open('01.txt', 'r', encoding='utf-8').read()
text = ' '.join(nltk.word_tokenize(text))
cloud = WordCloud().generate(text)
cloud.to_file('output01.png')
text = open('02.txt', 'r', encoding='utf-8').read()
text = ' '.join(nltk.word_tokenize(text))
cloud = WordCloud().generate(text)
cloud.to_file('output02.png')
```
成品如下:  
![image](https://github.com/flin1206/PL/blob/main/HW4/output01.png)  
![image](https://github.com/flin1206/PL/blob/main/HW4/output02.png)
