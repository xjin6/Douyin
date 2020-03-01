# 1. Trending
- [Insert images in DataFrame with HTML format](https://stackoverflow.com/questions/53468558/adding-image-to-pandas-dataframe/53469293)
```
import os
import json
import requests
import bs4 as bs
import numpy as np
import pandas as pd
import urllib.request
from IPython.core.display import HTML
```
To present the process simply, here one API scraped by CSDN user [考古学家lx](https://blog.csdn.net/weixin_43582101/article/details/103791795)
```
trending_api = 'Your API Here' #but in fact, the API should be derived by Mobile-PC interaction
re=requests.get(trending_api)
soup=bs.BeautifulSoup(re.content,'html.parser')
last_update = json.loads(soup.text)['data']['active_time']
trending_data = json.loads(soup.text)['data']['word_list']
trend = pd.DataFrame(trending_data)
date=last_update.split(' ')[0]
print('The last update time: '+last_update)
Word_cover=[]
for i in trend['word_cover']:
    if type(i)==dict:
        Word_cover.append(i['url_list'][0])
    else:
        Word_cover.append(None)
```
# 2. Function Package
```
def douyin(topic):
    os.mkdir('./'+topic)
    topic_api='https://aweme-hl.snssdk.com/aweme/v1/hot/search/video/list/?hotword='
    re=requests.get(topic_api+topic)
    soup=bs.BeautifulSoup(re.content,'html.parser')
    data = json.loads(soup.text)
    data = data['aweme_list']
    desc = [info['desc'] for info in data]
    nickname = [info['author']['nickname'] for info in data]
    verify = [info['author']['custom_verify'] for info in data]
    share_count = [info['statistics']['share_count'] for info in data]
    forward_count = [info['statistics']['forward_count'] for info in data]
    like_count = [info['statistics']['digg_count'] for info in data]
    comment_count = [info['statistics']['comment_count'] for info in data]
    download_count = [info['statistics']['download_count'] for info in data]
    cover_url = [info['video']['cover']['url_list'][0] for info in data]
    cover_visual = ['<img src="'+ url + '" width="100" >' for url in cover_url]
    video_url = []
    for info in data:
        video_url.append([i for i in info['video']['download_addr']['url_list'] if 'default' in i][0])
    df=pd.DataFrame({'desc':desc,'nickname':nickname,'verify':verify,
                    'share_count':share_count,'forward_count':forward_count,
                    'like_count':like_count,'comment_count':comment_count,
                    'download_count':download_count,'video_url':video_url,
                    'cover_visual':cover_visual})
    df.to_csv('./'+topic+'/'+topic+'.csv',encoding='utf-8-sig',index=False)
    df.to_html('./'+topic+'/'+topic+'.html',escape=False)
    video_visual = HTML(df.to_html(escape=False ,formatters=df['cover_visual']))
    #def cover(cover_url,file_name):
        #return urllib.request.urlretrieve(cover_url,file_name)
    def video(video_url,file_name):
        return urllib.request.urlretrieve(video_url,file_name)
    num=0
    for video_url in df['video_url']:
        video(video_url,'./'+topic+'/'+str(num)+'.mp4')
        num = num+1
```
# 3. Use the Function
If you use this code, please follow the form of citation:
Jin, Xin. (2020) How to Scrape the Hot Trending Data of Douyin and Download the Videos and Cover Images (Version 1.0)[Source Code]
