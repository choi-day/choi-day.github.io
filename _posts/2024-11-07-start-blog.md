---
layout: post
title:  "맥OS에서 Jekyll을 이용하여 Github Page 기반 블로그 제작하기"
date:   2024-11-07 11:52:40 +0900
categories: [jekyll study]
author: choi
pin: true
---
&nbsp;
## ✨맥OS에서 Jekyll을 이용하여 Github Page기반 블로그를 제작해보자 Feat. Chirpy✨
&nbsp;
&nbsp;
## Git Page 생성하기
* * *

### 1. 레포지토리 생성
- git의 메인 화면에서 우측 상단에 있는 자신의 프로필 사진을 클릭
- `your repositorie` 클릭
- 초록색 버튼 `new` 클릭
    + username은 자신의 git username입니당
- public체크
- Add a README file체크

### 2. github page 설정하기
- 생성한 레포지토리로 이동, 우측 상단 `Settings`클릭
- 좌측 메뉴 중 `Pages`클릭
- Source 를 `Deploy from a branch`로 설정
- 사이트 접속 확인 (예시: `https://username.github.io`)

## VS Code 활용
* * *

### 1. 리포지토리 클론
- VS Code 열기 -> F1 키 입력 -> git clone 검색 -> Git: Clone 선택
- 리포지토리 주소 입력 -> 클론할 위치 선택

### 2. 로컬 변경사항 적용
- 클론한 리포지토리 열기 (`README.md` 파일 확인)
- `index.html` 파일 생성
``` html
<html>
	<body>
		Hello! This is the first page!
	</body>
</html>
```
- 좌측 **Source Control** 메뉴 선택
- `+` 버튼을 클릭하여 변경사항 추가
- 커밋 메시지 입력, 커밋 & 푸시
- 사이트 반영 확인 (예시: `https://username.github.io`)

## 로컬 개발 환경 구축
* * *

### 1. Ruby 설치
> homebrew가 설치되어있어야 합니다.
- bash 터미널로 이동
``` 
chsh -s/bin/zsh
```
-> 이동하면 $로 변경됩니다
- rebenv 이용하여 ruby설치
맥북은 이미 ruby가 설치되어있습니다. rbenv를 이용하여 ruby버전을 관리하는 것이 추천됩니다.
```bash
brew install rbenv
```
- ruby 버전 확인 및 설치
```bash
rbenv install 버전
rbenv rehash #설치 후 재실행
```
-> 현재(2024.11.10 기준) 3.1.2가 최신버전입니다
- 환경 설정 파일 추가
``` bash
cd  #root로 이동
nano .bash_profile 

#열린 파일에 다음 내용을 복사-붙여넣기 합니다
[[ -d ~/.rbenv  ]] && \
  export PATH=${HOME}/.rbenv/bin:${PATH} && \
  eval "$(rbenv init -)"

#control+x -> y-> enter누르면 저장
``` 
- 적용된 ruby버전 확인
```bash
rbenv versions
```
- 버전을 잘못 다운 받았을 때 삭제하기
``` bash
rbenv uninstall 다운받은 버전
```
- jekyll, bundler, webrick 설치
```shell
gem install jekyll bundler
gem install webrick
```
- 설치 확인
```shell
jekyll -v
bundler -v
```

### 2. Jekyll 서버 구축

- jekyll 생성
```shell
jekyll new ./
```
- bundle install
```shell
bundle install
```
- http://127.0.0.1:4000/ 또는 http://localhost:4000/ 접속 확인

## Jekyll 테마 적용
* * * 

### 1. 테마 선택
- http://jekyllthemes.org
- https://jekyllthemes.io/free
- https://themes.jekyllrc.org
- https://github.com/topics/jekyll-theme

### 2. chirpy 테마 적용
- [공식 홈페이지](https://github.com/cotes2020/jekyll-theme-chirpy)에서 압축파일 다운로드
- 압축을 해제한 뒤, 모든 파일과 폴더를 로컬 리포지토리로 복사
- bundle install
```shell
bundle install
```
- jekyll 서버 실행
```shell
bundle exec jekyll serve
```
- http://127.0.0.1:4000/ 또는 http://localhost:4000/ 접속 확인
- 모든 변경사항 **커밋 및 푸시**


### 3. Github Action 적용
- 원격 리포지토리의 상단 **Settings** 클릭
- 좌측 **Pages** 클릭
- **Source**를 `Github Actions`로 설정
- `Configure` 클릭
- `Commit changes...` 클릭
- 로컬 리포지토리에서 pull

### 4. Node.js 설치
#### nvm 설치
nvm은 node버전을 관리해주는 기능을 한다. 바로 node를 설치하는 것보다는 nvm을 통해 설치하는 것이 권장된다.
```shell
brew install nvm
```
환경변수 설정
```shell
vim ~/.bash_profile
```
편집기 들어가서 i를 눌러 insert모드로 전환
```shell
export NVM_DIR="$HOME/.nvm"
[ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"  # This loads nvm
[ -s "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm"  # This loads nvm bash_completion
```
입력 후 esc누른 후 :wq를 눌러 저장 후 종료한다
```shell
source ~/.bash_profile
```
환경변수 적용을 위해 입력
설치 및 버전을 확인해본다
```shell
nvm -v
```
#### node 설치
특정 버전을 설치하고 싶다면
``` shell
nvm install v버전
```
서버에서 장기적으로 안정적 지원을 제공하는 버전을 설치하고 싶다면
```shell
nvm install --lts
```
설치 및 버전 확인
```shell
node -v
npm -v
```
[공식 홈페이지](https://nodejs.org/en/)에서 최신버전 다운로드 및 설치
- Ruby 프롬프트에서 아래 명령어 실행
```shell
npm install && npm run build
```

### 5. 테마 상세 설정
- `.gitignore` 파일 하단 수정
```shell
# Misc
# _sass/dist
# assets/js/dist
```
- `_config.yml` 파일 수정
```shell
timezone: Asia/Seoul

url: "https://karina.github.io"

github:
  username: karina
```
- 모든 변경사항 커밋 및 푸시
- 커밋 메시지 주의
- 사이트 반영 확인 (예시: `https://karina.github.io`)

* * *

# 에러가 나는 경우
설치하면서 제가 겪었던 문제를 정리했습니다 오류가 뜰 때 해당하는 사항이 있는 지 확인하면 좋을 것 같습니다
### brew터미널인지 확인
터미널 상단의 이름이나 커서 앞에 기호가 $인지로 구분할 수 있습니다. 만약 brew터미널이 아니라면 brew터미널로 이동해야 합니다. 이동하는 방법은 1. Ruby설치에 작성되어있습니다
### brew가 최신 버전이 아님
brew가 최신버전이 아니어서 오류가 뜰 수도 있습니다. 업데이트 해주면 됩니다.
```shell
brew update
```
### Ruby가 최신 버전이 아님
Ruby가 최신 버전이 아니어서 오류가 뜰 수도 있습니다. 최신버전 확인 후 업데이트 해주면 됩니다.
```shell
rebenv install 버전
```
* * *

# 참고한 글
<https://cmjunghoon.github.io/posts/Install_Ruby/#1-rbenv%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-ruby-%EC%84%A4%EC%B9%98>
<https://deku.posstree.com/ko/jekyll/installation/>
<https://renee.tistory.com/46>
