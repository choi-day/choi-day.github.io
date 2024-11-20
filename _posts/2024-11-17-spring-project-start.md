---
layout: post
title:  "스프링 없는 순수한 자바로 개발해보기 - 프로젝트 시작"
date:   2024-11-17 00:00:40 +0900
categories: [Spring study, 스프링 핵심 원리 - 기본편]
author: choi
---
스프링의 도움 없이 자바로만 개발을 진행해보자.
프로젝트 환경설정을 편리하게 하기 위해 스프링 부트를 사용한다.

# 스프링 프로젝트 시작하기

1. 아래 링크 접속
<https://start.spring.io/>

2. 프로젝트 기본 설정
![](/assets/img/springStart/springstart1.png)
- gradle - groovy 선택
- language java 선택
- spring boot 버전 선택(snapshot등 뒤에 무언가가 붙은 건 불안정할 수 있음)
- group과 artifact작성
- java 버전 선택

3. 프로젝트 다운받기
cmd+enter 혹은 generate를 누르면 압축파일이 다운받아진다. 적당한 폴더에 받고 압축을 해제해준다

4. inteliJ로 프로젝트 열기
![](/assets/img/springStart/springstart2.png)
압축 해제한 폴더로 들어가서 build.gradle을 inteliJ로 열어주기

5. 프로젝트 확인
![](/assets/img/springStart/springstart3.png)
프로젝트가 잘 열리는지 확인해준다. 처음 들어가면 gradle설정 다운로드 등의 처리때문에 시간이 좀 걸림. 나는 빨간색으로 오류가 떴는데 자바를 찾을 수 없어서라고 하길래 다시 설정해주었다... 다행히 버전이 안 맞는 부분이라 재설치 했더니 잘 돌아감.

6. gradle 살펴보기
![](/assets/img/springStart/springstart8.png)
코끼리 아이콘이 있는 gradle파일을 열어본다. 자바 버전, 스프링 버전 등이 기록되어있다.

7. 프로젝트 실행해보기
![](/assets/img/springStart/springstart9.png)
src > main > java > hello.core > CoreApplication클릭
프로젝트 이름마다 java아래로는 경로명이 다를 것이다.
![](/assets/img/springStart/springstart4.png)
public class 옆에 화살표를 누른다
![](/assets/img/springStart/springstart5.png)
제대로 작동하는 것을 콘솔에서 확인 아무것도 하지 않았기때문에 아무것도 안 뜨고 바로 종료된다.

8. 빌드 도구 바꾸기
속도를 향상시키기 위해 빌드 도구를 inteliJ로 바꿔준다.
![](/assets/img/springStart/springstart6.png)
preferences클릭(나는 한글버전 패치를 했더니 설정이라고 뜬다...)
![](/assets/img/springStart/springstart7.png)
gradel검색하여 다음을 사용하여 빌드 및 실행, 테스트 실행 부분을 모두 inteliJ IDEA로 바꿔준다.