---
title: "Ubuntu Server NAS 구축기"
date: 2018-07-14T17:39:55+09:00
categories: [dev]
tags: [nas, docker]
---


# NAS

몽골로 떠나기 1주일전쯤에, 놀고 있던 구형 노트북을 어떻게 쓸지 생각해보다가 이걸 NAS로 써야겠다는 생각이 들었다. 어차피 집 구석에서 놀고 있을 노트북이고, 내가 사용하고 있는 서피스는 SSD의 가용 용량이 적어서 뭔가 데이터를 보관하기 애매했으니까.

그래서 [링크](http://psychoria.tistory.com/m/488) 를 참조해서 Ubuntu Server 16.04 LTS를 깔고... 그런데 설치 중에도 문제가 생겼다. 파티션이 꼬이는 문제. Universal-USB-Installer-1.9.7.8로 설치하려고 해서 그런걸까? 그래서 윈도우 설치 USB로 드라이브를 다 날려버렸다. 그 후 rufus로 다시 우분투 설치 USB를 만들어 설치했다. 설치를 다 하고, 인터넷을 잡으려는데 잡히지 않는다. 왜지? 왜 그런가 했더니 내장 랜카드 자체가 인식이 안됐다. 이 문제를 어떻게 해결하지? 다행히 기가비트를 사용하기 위해서 사뒀던 USB 랜카드도 있고, 무선랜도 작동하는걸 확인했다.

그래서 [Linsoo 님의 블로그](https://linsoo.co.kr/archives/15553) 를 참조해서 USB 랜카드를 설정했다. 위와 같이

    sudo apt-get install wireless-tool wpasupplicant

를 실행한다. 그 후

    sudo lshw -C Network

를 입력하면 랜카드 정보가 나온다. 여기서 뜨는 정보 중에서 logical name이 랜카드 인터페이스 이름이다. 만약 DISABLE 되어있다면,

    sudo ifconfig "랜카드 인터페이스 이름" up

을 입력해서 켜준다.

무선랜 같은 경우에는 SSID와 ESSID를 확인해 봐야 한다. 확인하는 방법은 sudo iwlist "랜카드 인터페이스 이름" scan | more 으로 확인할 수 있다.

부팅시 자동 활성화 하는 방법은 다음과 같다.

    sudo vi /etc/network/interfaces

로 설정 파일에 진입해서

    auto "무선랜 인터페이스 이름"
    iface "무선랜 인터페이스 이름" inet dhcp
    wpa-ssid "접속할SSID"
    wpa-key_mgmt WPA-PSK "접속할ESSID"
    wpa-psk "무선랜암호"

이 5 Line을 추가해서 무선랜을 부팅시 자동 활성화 할 수 있다.

USB 랜카드를 부팅시 자동활성화 하려면

    auto "USB 랜카드 인터페이스 이름"
    iface "USB 랜카드 인터페이스 이름" inet dhcp

이 두 Line을 추가해주면 된다.

설정이 완료 됐다면

    sudo /etc/init.d/networking restart

를 입력하여 네트워크를 다시 시작한다. ifconfig시 랜카드 정보에 아이피가 잡혀있다면 성공이다.

설치 후 패키지를 최신으로 업데이트 하기 위해서

    sudo apt-get update
    sudo apt-get dist-upgrade

이 두 Line을 실행했다.

서버로 사용할 것이기때문에 절전모드를 해체할 필요가 있다. [링크](http://forum.falinux.com/zbxe/index.php?document_srl=877661&mid=lecture_tip) 를 참조해서 노트북 절전모드를 중지했다.

    sudo vi /etc/systemd/logind.conf

파일에 진입해서 HandleLidSwitch=ignore 로 수정하고 저장했다.

HDD 절전 모드를 설정해둘 필요가 있다고 생각했다. [링크](http://klkl0.tistory.com/78) 를 참조하여

    sudo vi /etc/hdparm.conf

마지막 줄에

    /dev/sda/ {
        spindown_time = 240
    }

240은 20분이다.

나는 이왕 Ubuntu로 NAS를 구축하면서 Docker에 대해서 공부해볼겸 Docker를 적극 활용해서 만들기로 결정했다. 일단 참조한 블로그에서 업로드한 것처럼 먼저 NextCloud를 설치했다.

내가 NextCloud를 설치하기 위해 사용한 Docker 이미지의 [링크](https://hub.docker.com/_/nextcloud/)

    docker run --name nextcloud --link mariadb:mariadb -v /home/nextcloud:/var/www/html/data -d -p 8080:80 nextcloud:latest

설치 후 권한을 변경할 필요가 있다.

    sudo chown -R 내계정:내계정 디렉토리경로
    sudo usermod -a -G 내계정 www-data
    sudo chmod -R 775 디렉토리경로

http://내_나스_주소:8080 을 통하여 진입하여 설정을 완료한다. 데이터 폴더 입력칸에는 위에 /home/nextcloud: 뒤에 썼던 /var/www/html/data 를 입력해준다.

데이터베이스 설정에서는 MySQL/MariaDB를 선택 후 아이디 비밀번호 nextcloud mariadb 이렇게 순서대로 입력한다.

내 서피스에서 편하게 접근할 수 있도록 Samba를 이용해서 파일서버 설정을 했다. [링크](http://ejklike.github.io/2016/11/27/connect-two-ubuntu-pcs-without-internet-samba-setting.html) 를 참조해서 설정했다.

    sudo apt-get install samba (설치)
    sudo -add user1 #user 이름은 원하는 대로 (사용자 추가)
    sudo smbpasswd -a user1 (사용자 등록 및 패스워드 설정) 

    sudo vi /etc/samba/smb.conf (samba 설정 파일 진입)

    #user1에게 공유할 폴더 위치 및 권한 설정
    [user1_home]
        comment= shared folder (local) #공유 폴더 설명
        path = /home/asdf/share #공유할 폴더 위치 (당연히, 폴더가 존재해야 함)
        read only = no #읽기 권한만 부여할 지 여부
        writable = yes #읽기/쓰기 권한 부여할 지 여부
        browseabale = yes # 해당 폴더를 윈도우 '내 네트워크 환경' 목록에 노출시킬 지 여부
        guest ok = no # 누구나 접근 가능 여부
        valid users = user1 #접속 가능 유저

    # global setting에 아래 두 내용 추가
    [global]
    create mask = 0644  #새로 생성한 파일에 대한 권한 설정
    directory mask = 0755 #새로 생성한 폴더에 대한 권한 설정

그 후 재시작을 한다

    sudo /etc/init.d/samba reload

서버가 켜질때 docker 컨테이너를 다시 시작할 필요가 있다. [링크](http://www.whatwant.com/497) 를 참조해서 설정했다. /etc/rc.local 를 수정했다.