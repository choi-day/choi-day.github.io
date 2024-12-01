---
layout: post
title:  "Pygame으로 게임 만들기 - 7. 디자인 수정하기 (배경삽입, 로고 이미지 삽입)"
date:   2024-11-30 00:07:40 +0900
categories: [pygame]
tags: [pygame]
author: choi
---

# ✨전체적인 디자인을 수정해보자✨

 기능도 굉장히 중요하지만 눈에 보이는 요소도 중요하다고 생각한다. 눈에 쏙 들어오지 않으면 기능이 아무리 좋아도 반감될 것이다. 만족감을 주는 디자인은 게임을 지속적으로 이용하는데도 중요한 역할을 한다. 이제부터 디자인을 하나씩 바꿔나가보자!

### 🎨 배경 바꾸기

 현재까지는 screen.fill()을 이용하여 전체를 검은 색으로 채웠다. 이것을 지우고 blit함수로 배경을 채운다. 

시작점은 (0,0)이고 이미지 크기가 화면만큼 커야 전체를 채울 수 있다.

### 🎨 gameover, main 로고 삽입

 현재 시작 화면에는 시작 버튼, 종료 버튼만 있다. 뭔가 허전한 느낌이 들어 생각해보니 게임 로고가 없다! 게임 로고를 제작했다.

canva를 이용했다. <https://www.canva.com/> 

&nbsp;
![칸바 메인](/assets/img/canva/canva_main.png)
creat design을 눌러준다 

&nbsp;
![칸바 프로젝트 시작](/assets/img/canva/canva_edit.png)
위와 같은 화면이 뜨는데 

&nbsp;
![로고 프로젝트 작업환경](/assets/img/canva/canva_prj.png)
logo를 선택하면 정사각형의 작업환경이 나온다

&nbsp;
![왼쪽 레이어](/assets/img/canva/canva_element.png)
왼편에 여러 요소가 뜨는데, element에서는 아이콘을, text에서는 글씨를 삽입할 수 있다. 

&nbsp;
![text](/assets/img/canva/canva_text.png)
특히 text에선 디자인 된 글씨도 사용할 수 있는데, 원하는 디자인을 고르고 텍스트만 바꿔주면 된다. canva를 사용한 가장 큰 이유! 

&nbsp;
이걸 배경 제거 후 저장하려면 돈을 내야한다… 그래서 일단 그냥 저장하고 다른 사이트를 찾아보았다. 저장할 때 배경색을 로고와 가장 반대되는 색상을 사용하면 배경 제거에 도움이 된다. 구글에 검색하면 여러 사이트가 나오는데 글씨 중간중간에도 구멍이 있어서 그런지 완벽하게 제거되는 사이트를 찾기 어려웠다. 

&nbsp;
그나마 가장 괜찮았던 곳은 <https://www.iloveimg.com/ko/remove-background> 이었다. 

&nbsp;
> 사진마다 다를 수 있으므로 제대로 제거가 되지 않는 다면 다른 사이트를 이용해보면 될 것 같다. 굉장히 많다.

&nbsp;
![로고](/assets/img/canva/mainlogo.png)
이렇게 완성된 로고다.

&nbsp;
![gameover](/assets/img/canva/gameover.png)
&nbsp;

같은 방법으로 game over 문구도 제작했다.