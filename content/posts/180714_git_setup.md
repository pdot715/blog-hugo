---
title: "Git 설치 과정"
date: 2018-07-14T17:51:55+09:00
categories: [dev]
tags: [git]
---


새로이 개발환경을 다시 세팅하고자 Visual Studio Code를 설치하고 Git을 다운로드 받았다. 사실 블로그를 github pages를 이용해서 구축할까했지만 요즘 다들 이 방식으로 만들고 있기 때문에 github 컨트리뷰션 칸 채우는 것말고는 큰 의미가 없다 싶었다. 기껏해봐야 마크다운? 정도일텐데 어느정도 마크다운 기본 문법을 알고 있어서 더 필요가 없다 싶다. 이 글을 쓰게된 동기는 Git 설치로 구글에 검색하면 처음 나오는 글에서... TrueType 글꼴을 '올바른 글꼴'이라고 써놔서다.

(근데 결국 마크다운 파일로 관리하는걸로 갈아탔다. 소스코드 깨지는게 더 짜증남...)

git 홈페이지에 들어가 설치 파일을 다운로드 받고 실행하면 

*   아이콘 추가
    *   바탕화면에 추가
*   탐색기와 통합 (아이콘 오른쪽 클릭시 메뉴)
    *   Git Bash 추가
    *   Git GUI 추가
*   Git LFS (Large File Support) (LFS에 대해서는 [https://blog.outsider.ne.kr/1147](https://blog.outsider.ne.kr/1147) 글을 참조)
*   .git* 설정 파일을 기본 텍스트 에디터와 연결
*   .sh 파일을 Bash와 연결
*   모든 콘솔 윈도우에서 트루타입 글자 사용
*   Git 업데이트 매일 체크

나는 LFS도 필요 없을 것 같고, Visual Studio Code에서 타이핑 하면서 작업 할 생각이라서 굳이 체크 할 필요가 없을 것 같아 다 체크 해제.

기본 에디터를 고를 수 있는데 나는 Visual Studio Code를 사용하니까 Visual Studio Code 선택. Visual Studio Code Insider도 있는데 이건 안정버전이 아니고 실시간으로 업데이트 되는 버전 같다.

나머지는 기본 설정으로 설치.

설치 후에는 git 사용자 정보를 설정해야한다.

    git config --global user.name "사용자명"
    git config --global user.email 사용자명@example.com

git을 열심히 사용하면서 터미널 작업과 커맨드라인에 익숙해져야겠다 git을 사용하기 가장 좋은 툴은 커맨드라인이라고 하니까,,,