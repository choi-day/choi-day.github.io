---
layout: post
title:  "Mac에서 스프링 공부 사전 준비 시작하기(java11, inteliJ 설치)"
date:   2024-11-17 00:00:40 +0900
categories: [Spring study]
tags: [Spring, InteliJ]
author: choi
---
# java 21 설치하기
hombrew가 설치되어있다는 가정 하에 시작한다. 제대로 작동하지 않으면 Update시도해보기.
1. java version 확인
```zsh
java --version
```

2. 설치하기
```zsh
brew install openjdk@21
```

3. 설치한 java경로 인식해주기
```zsh
sudo ln -sfn $(brew --prefix openjdk@11)/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-21.jdk
```
중간중간 password를 입력하라는 문구가 뜨는데, 자신이 설정한 맥 비번을 입력하면 됨. 보안상의 이유로 작성해도 아무것도 뜨지 않으니 당황하지 않고 그냥 쓰면 된다.

4. 환경변수 등록하기
```zsh
source ~/.zshrc  # Zsh인 경우
source ~/.bash_profile  # Bash인 경우
```
사용 중인 쉘에 맞춰 편집기를 열어준다

```
export JAVA_HOME=$(brew --prefix openjdk@21)/libexec/openjdk.jdk/Contents/Home
export PATH=$JAVA_HOME/bin:$PATH
```
위 내용을 입력하고 ^x, y, enter를 눌러 저장 후 닫아줌

5. 저장 후 닫기
```zsh
source ~/.zshrc  # Zsh인 경우
source ~/.bash_profile  # Bash인 경우
```

6. 설치 확인
```zsh
java --version
```
버전이 11이 아니거나 뜨지 않는다면 다시 시도해야한다. 

7. 환경 변수 확인
```zsh
echo $JAVA_HOME
```


# inteliJ 설치하기
Eclipse를 사용해도 상관 없지만 수강중인 강의에서 inteliJ로 강의를 진행한다고 했으므로 설치하기로 함

### inteliJ 다운받기

1. 아래 사이트 접속
<https://www.jetbrains.com/idea/download/?section=mac>

2. 다운로드 클릭
![](/assets/img/intelijDownload/intelij1.png)
ultimate는 유료버전이고 무료 버전은 community버전이 따로 있다
나는 학생 계정을 이용하여 유료 버전을 사용할 계획이기떄문에 ultimate를 다운받음
- .dmg(Intel): m1이 탑재 되기 전 맥
- .dmg(apple silicon): 그 이후 맥
자신에게 맞는 버전을 선택하여 다운 받는다

3. inteliJ 설치
![](/assets/img/intelijDownload/intelij2.png)
설치 받은 파일을 열면 위와 같이 뜬다. 드래그 하면 됨.

### inteliJ시작하기
1. 약관 동의 등등... 
![](/assets/img/intelijDownload/intelij3.png)
![](/assets/img/intelijDownload/intelij4.png)
자신에 맞게 해준다 나는 데이터공유는 보내지 않음으로 함

2. 요금제 적용(ultimate)
![](/assets/img/intelijDownload/intelij5.png)
학생 요금제로 적용할 예정이라 어떤 요금제를 적용하면 되는지 오른쪽 하단의 **요금제 및 가격 정책**을 클릭해봄
![](/assets/img/intelijDownload/intelij6.png)
github 학생 인증 계정도 가능하다고 하여 git으로 연동하기로 함
스크롤을 내리면 지금 신청하기가 있다 클릭
![](/assets/img/intelijDownload/intelij7.png)
github로 로그인
![](/assets/img/intelijDownload/intelij8.png)
Authorize JetBrains 클릭
![](/assets/img/intelijDownload/intelij9.png)
맨 위에 Accept누르고 아래 Apply for a free student or teacher ~ 눌러줌
![](/assets/img/intelijDownload/intelij10.png)
Apply now 클릭
![](/assets/img/intelijDownload/intelij11.png)
github로 인증 클릭
![](/assets/img/intelijDownload/intelij12.png)
로그인하여 인증 한 뒤에 위와 같은 화면이 뜬다. 빈 칸을 채워주면 된다.
![](/assets/img/intelijDownload/intelij13.png)
메일이 발송되었다길래 구글 메일함을 들어가보니 와있음. 인증 링크를 클릭.
![](/assets/img/intelijDownload/intelij14.png)
파란색 버튼 클릭.
![](/assets/img/intelijDownload/intelij15.png)
동의해주기
![](/assets/img/intelijDownload/intelij16.png)
완료되었다는 메세지가 뜬다!!!!
![](/assets/img/intelijDownload/intelij17.png)
다시 설치한 intelij로 돌아와서 계정 로그인 해주기.

3. inteliJ 설정하기
![](/assets/img/intelijDownload/intelij18.png)
난 이미 visual studio code가 설치되어있어서 그런지 설정 가져오기가 뜸. 필요 없는 사람은 아래 건너뛰기를 누르면 될 것 같다.
![](/assets/img/intelijDownload/intelij19.png)
가져올 설정 선택.
![](/assets/img/intelijDownload/intelij20.png)
완료되었다! 