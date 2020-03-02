# 1. Trending
- The **Trending API** and **Topic API** is derived by an emulater named [网易MuMu模拟器](https://mumu.163.com/), and [Charles Proxy](https://www.charlesproxy.com/);
- Here I use the scraped API of CSDN user [考古学家lx](https://me.csdn.net/weixin_43582101) directly;
- It is not an official API [[Reference]](https://blog.csdn.net/weixin_43582101/article/details/103791795). 

# 2. Video
- With functions as follows, we can get variabels of description, account name, verify, share count, forward count, like count, comment count, and download count of each video;
- And download original videos
- Core function:
```
def douyin(topic):
    generate_path('./'+topic)
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
    num=0
    for video_url in df['video_url']:
        video(video_url,'./'+topic+'/'+str(num)+'.mp4')
        num = num+1
```

# 3. Use the Function
```
# Test with one single topic
douyin('Your topic here')
# Scrape the whole data of all topics
for i in trend['word']:
    douyin(i)
```
The complete source code could be found in **Douyin.ipynb**, if you use this code, please follow the form of citation and give me a star:

Jin, Xin. (2020) How to Scrape the Hot Trending Data of Douyin and Download the Videos and Cover Images (Version 1.1)[Source Code]. https://github.com/xjincomm/Douyin
