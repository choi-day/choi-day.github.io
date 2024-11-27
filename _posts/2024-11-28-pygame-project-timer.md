---
layout: post
title:  "Pygame으로 게임 만들기 - 2. 타이머구현"
date:   2024-11-27 00:00:40 +0900
categories: [pygame]
tags: [pygame]
author: choi
---

# ✨게임 배경에 타이머를 띄우도록 구현해보자✨

화면에 꽉차게 흘러가는 시간을 ``초 : 밀리초`` 형식으로 표시하기로 했다. 어떻게 구현해봐야할까

### 1. 타이머 구현

👩‍💻 pygame.time.get_ticks
: 게임이 시작된 이후의 경과시간을 밀리초로 반환한다. 

시작 시간과 게임이 끝난 시간을 이 함수로 얻어 저장한 뒤 둘의 차(elapsed_time)를 구하면 게임 플레이 시간을 얻을 수 있다.

### 2. 화면에 띄우기

👩‍💻 game_font.render
: 화면에 띄울 글씨를 만들어주는 함수이다.

초 : 밀리초 형식으로 띄울 것이기 때문에 elapsed_time을 1000으로 나눈 몫 , ‘:’, elapsed_time을 1000으로 나눈 나머지 형식으로 문자열로 변환하여 인수를 넣는다. 

👩‍💻 screen.blit

: 객체를 화면에 보이도록 만들어주는 함수이다. 어떤 글씨를 화면의 어디에 띄울지 인수를 넣어준다. 

예를 들어 timer객체를 (10,10)의 좌표에 넣으러면 screen.blit(timer, (10,10))이다. 좌표는 객체의 왼쪽 끝을 기준으로 한다.

### ⚙️ 코드

```python
import pygame  # 1. pygame 선언
import random
import os

pygame.init()  # 2. pygame 초기화

# 3. pygame에 사용되는 전역변수 선언

BLACK = (0, 0, 0)
size = [600, 800]
screen = pygame.display.set_mode(size)

#화면에 글자를 띄우기 위한 폰트
game_font = pygame.font.Font(None, 200)

done = False
clock = pygame.time.Clock()

#시간 정보
start_ticks = pygame.time.get_ticks()

def runGame():
    bomb_image = pygame.image.load('/Users/cho/2024/오픈소스 SW개발/delta/pygame-delta-avoiding-filth/bomb_game/bomb.png')
    bomb_image = pygame.transform.scale(bomb_image, (50, 50))
    bombs = []

    for i in range(5):
        rect = pygame.Rect(bomb_image.get_rect())
        rect.left = random.randint(0, size[0])
        rect.top = -100
        dy = random.randint(3, 9)
        bombs.append({'rect': rect, 'dy': dy})

    person_image = pygame.image.load('/Users/cho/2024/오픈소스 SW개발/delta/pygame-delta-avoiding-filth/bomb_game/person.png')
    person_image = pygame.transform.scale(person_image, (100, 100))
    person = pygame.Rect(person_image.get_rect())
    person.left = size[0] // 2 - person.width // 2
    person.top = size[1] - person.height
    person_dx = 0
    person_dy = 0

    global done
    while not done:
        clock.tick(30)
        screen.fill(BLACK)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                done = True
                break
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    person_dx = -5
                elif event.key == pygame.K_RIGHT:
                    person_dx = 5
            elif event.type == pygame.KEYUP:
                if event.key == pygame.K_LEFT:
                    person_dx = 0
                elif event.key == pygame.K_RIGHT:
                    person_dx = 0

        for bomb in bombs:
            bomb['rect'].top += bomb['dy']
            if bomb['rect'].top > size[1]:
                bombs.remove(bomb)
                rect = pygame.Rect(bomb_image.get_rect())
                rect.left = random.randint(0, size[0])
                rect.top = -100
                dy = random.randint(3, 9)
                bombs.append({'rect': rect, 'dy': dy})

        person.left = person.left + person_dx

        if person.left < 0:
            person.left = 0
        elif person.left > size[0] - person.width:
            person.left = size[0] - person.width

        screen.blit(person_image, person)

        for bomb in bombs:
            if bomb['rect'].colliderect(person):
                done = True
            screen.blit(bomb_image, bomb['rect'])

        #경과시간 계산
        elapsed_time = (pygame.time.get_ticks() - start_ticks)

        #타이머 화면 출력
        timer = game_font.render(str(int(elapsed_time // 1000)) + ' : ' + str(int(elapsed_time % 1000)), True, (255,255,255))

        #시간 화면에 뜨게
        screen.blit(timer, (size[0]//2 - (timer.get_width() //2), size[1]//2 - (timer.get_height()//2)))

        pygame.display.update()

runGame()
pygame.quit()
```