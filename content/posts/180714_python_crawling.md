---
title: "python 크롤링 + 텔레그램 봇 예제 따라해보기"
date: 2018-07-14T16:09:55+09:00
categories: [dev]
tags: [python, telegram, crawling]
---

파이썬 개발환경을 어떻게 할 지 고민하다 [이 글](https://spoqa.github.io/2017/10/06/python-env-managers.html)을 보았음. pyvenv + PyCharm으로 개발환경을 셋팅하기로 결정.

python 3.x를 다운로드 받고 설치.  
[설치 과정 참고](http://pythonstudy.xyz/python/article/2-Python-%EC%84%A4%EC%B9%98)  
[이 블로그](https://blog.outsider.ne.kr/1324)도 참조하면 좋을 것 같다.

가상 환경 생성은 설치 과정에서 참고한 [블로그](http://pythonstudy.xyz/python/article/302-%EA%B0%80%EC%83%81-%ED%99%98%EA%B2%BD)의 다른 항목에서 참고했다.

PyCharm과 가상환경 연결 시키는건 [이 블로그](http://whatisthenext.tistory.com/117)를 참조했다.  
나 같은 경우엔 bin 폴더가 없고... Script 폴더 안에 있었다.

크롤러 만드는 과정은 [이 블로그](https://beomi.github.io/gb-crawling/posts/2017-01-20-HowToMakeWebCrawler.html)를 따라했다.  
하다가 서버가 필요하다는 것을 느꼈다. 그래서 bothub.studio라는 곳도 가입해보고... 했다가 github student pack으로 받은 digital ocean 50$ 쿠폰을 사용하기로 결정  
50$면 10개월동안 쓸 수 있으니 취준 포트폴리오용으로 가지고 놀아야지... 라고 생각했다.

텔레그램 봇 관리 [참고](http://softworld.tistory.com/category/?page=1)  
텔레그램 봇 409 에러 뜰 때 [참고](https://github.com/lukefx/hubot-telegram/issues/9)

```py
import requests
from bs4 import BeautifulSoup
import os

import telegram

bot = telegram.Bot(token='012345467:TESTtestTestteStTEst')
chat_id = bot.getUpdates()[-1].message.chat.id

# 파일의 위치
BASE_DIR = os.path.dirname(os.path.abspath(__file__))

req = requests.get('http://m.ssu.ac.kr/html/themes/m/html/notice_univ_list.jsp')
req.encoding = 'utf-8'

html = req.text
soup = BeautifulSoup(html, 'html.parser')
posts = soup.select('li.first-child > a')
address = posts[0].get('href')
latest = posts[0].text

with open(os.path.join(BASE_DIR, 'latest_soongsil.txt'), 'r+') as f_read:
    before = f_read.readline()
    f_read.close()
    if before != latest+address:
        bot.sendMessage(chat_id=chat_id, text='학교 공지사항에 새로운 글')
        bot.sendMessage(chat_id=chat_id, text=latest)
        bot.sendMessage(chat_id=chat_id, text='http://m.ssu.ac.kr'+address)
        with open(os.path.join(BASE_DIR, 'latest_soongsil.txt'), 'w+') as f_write:
            f_write.write(latest)
            f_write.write(address)
            f_write.close()

req_cse_job = requests.get('http://cse.ssu.ac.kr/05_sub/05_sub.htm')
req_cse_job.encoding = 'utf-8'

html_cse_job = req_cse_job.text
soup_cse_job = BeautifulSoup(html_cse_job, 'html.parser')
posts_cse_job = soup_cse_job.select('tr > td > a')
address_cse_job = posts_cse_job[0].get('href')
latest_cse_job = posts_cse_job[0].text

with open(os.path.join(BASE_DIR, 'latest_cse_job.txt'), 'r+') as f_read:
    before_cse_job = f_read.readline()
    f_read.close()
    if before_cse_job != latest_cse_job+address_cse_job:
        bot.sendMessage(chat_id=chat_id, text='학부 취업 게시판에 새로운 글')
        bot.sendMessage(chat_id=chat_id, text=latest_cse_job)
        bot.sendMessage(chat_id=chat_id, text='http://cse.ssu.ac.kr/05_sub/05_sub.htm'+address_cse_job)
        with open(os.path.join(BASE_DIR, 'latest_cse_job.txt'), 'w+') as f_write:
            f_write.write(latest_cse_job)
            f_write.write(address_cse_job)
            f_write.close()

req_cse = requests.get('http://cse.ssu.ac.kr/main/index.htm')
req_cse.encoding = 'utf-8'

html_cse = req_cse.text
soup_cse = BeautifulSoup(html_cse, 'html.parser')

posts_cse = soup_cse.select('ul > li > span > a')
address_onclick = posts_cse[0].get('onclick')
# 문자열 자르기로 게시글 숫자만 빼기
address_cse = address_onclick[13:17]
latest_cse = posts_cse[0].text

with open(os.path.join(BASE_DIR, 'latest_cse.txt'), 'r+') as f_read:
    before_cse = f_read.readline()
    f_read.close()
    if before_cse != latest_cse+address_cse:
        bot.sendMessage(chat_id=chat_id, text=latest_cse)
        bot.sendMessage(chat_id=chat_id, text='http://cse.ssu.ac.kr/03_sub/01_sub.htm?no='+address_cse+'&bbs_cmd=view')
        with open(os.path.join(BASE_DIR, 'latest_cse.txt'), 'w+') as f_write:
            f_write.write(latest_cse)
            f_write.write(address_cse)
            f_write.close()
```

학부 홈페이지가 너무 난잡하게 되어있어 학부 홈페이지 공지사항 가져오는 부분을 수정했다 (2018-01-13)

주말 지나고 확인하니 안 돌아가고 있음. 참고한 블로그에서도 indexerror 뜨면 봇한테 메세지 보내라고 했으니...  
그래서 내 chat_id를 알아내서 내 chat_id로만 보내게 설정했다. [링크](https://ncube.net/13541) 참조. 이걸로 해결 되려나? (2018-01-15)  
나중에 다중 사용자가 사용하는 봇을 만들때는 어떻게 해야할지 생각해야할 것 같다!

학교 경력개발센터에 있는 데이터를 크롤링 하려고 했으나 https 문제도 그렇고 따오기가 쉽지 않아서 포기 (2018-01-16)

사용하다가 작동이 안 되는 경우에는 