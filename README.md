# 1. Trending
- The **Trending API** and **Topic API** is derived by an emulater named [网易MuMu模拟器](https://mumu.163.com/), and [Charles Proxy](https://www.charlesproxy.com/);
- Here I use the scraped API of CSDN user [考古学家lx](https://me.csdn.net/weixin_43582101) directly;
- It is not an official API [[Reference]](https://blog.csdn.net/weixin_43582101/article/details/103791795). 

# 2. Video
- With functions as follows, we can get variabels of description, account name, verify, share count, forward count, like count, comment count, and download count of each video;
- And download original videos
- Core function:
```python
def scraper(topic):
    generate_path('./'+topic)
    topic_api='https://aweme-hl.snssdk.com/aweme/v1/hot/search/video/list/?hotword='
    re=requests.get(topic_api+topic)
    soup=bs.BeautifulSoup(re.content,'html.parser')
    data = json.loads(soup.text)
    data = data['aweme_list']
    desc = [info['desc'] for info in data]
    time_stamp = [info['create_time'] for info in data]
    create_time = [time(info['create_time']) for info in data]
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
        try:
            video_url.append([i for i in info['video']['download_addr']['url_list'] if 'default' in i][0])
        except:
            video_url.append(None)
    df=pd.DataFrame({'desc':desc,'nickname':nickname,'verify':verify,'time_stamp':time_stamp,
                     'create_time':create_time,'share_count':share_count,'forward_count':forward_count,
                    'like_count':like_count,'comment_count':comment_count,
                     'download_count':download_count,'video_url':video_url,
                    'cover_visual':cover_visual})
    df.to_csv('./'+topic+'/'+topic+'.csv',encoding='utf-8-sig',index=False)
    for num in range(0,len(data)):
        try:
            video(df['video_url'][num],'./'+topic+'/'+str(df['time_stamp'][num])+'.mp4')
            
            print('topic: '+topic+', video #'+str(num)+': '+str(df['time_stamp'][num])+'......Succeeded')
        except:
            print('topic: '+topic+', video #'+str(num)+': '+str(df['time_stamp'][num])+'......Failed')
            continue
```

# 3. Use the Function
Test with one single topic, remember to type in the topic
```python
douyin_topic('中国科研团队发现新冠病毒已突变')
```
Use only one line of codes to get all data and download all videos of hot trending rank
```python
douyin_trend()
```
The complete source code could be found in **Douyin.ipynb**, if you use this code, please follow the form of citation and give me a star ⭐⭐⭐:

Jin, Xin. (2020) Douyin Hot Trending Data Scraper and Video Downloader (Version 1.1) [Source Code]. https://github.com/xjincomm/Douyin  
# 4. Update Log and Notes
__3 Mar 2020__:  
This set of code was completed and tested on Mac OS environment which may be a little bit different from Windows. Some reminders are as follows:
- For the core function, just delete or # the line of  
```df.to_html('./'+topic+'/'+topic+'.html',escape=False)```
- For the part of __1. Trending__ of the original Jupyter Notebook code, please delete or # the line of ```trend.to_html('./trend/trend_'+last_update+'.html', escape=False)```
----
Xin Jin  
Senior Research Assistant  
Dept. Media and Communication, City Univ. HK  
[About](www.xjin.tech) | [LinkedIn](linkedin.com/in/xjin613/) | [Twitter](https://twitter.com/xjin_comm) | xin.jin@cityu.edu.hk
