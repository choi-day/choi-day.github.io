---
layout: post
title:  "Pygame으로 게임 만들기 - 1. 기획"
date:   2024-11-27 00:00:40 +0900
categories: [pygame]
tags: [pygame]
author: choi
---

# ✨PYGAME을 이용하여 게임 제작 프로젝트를 시작!✨
&nbsp;

### 1. 기반이 되는 코드 고르기

 pygame이 처음이기도 하고 어떻게 시작할 지 막막하여 이미 실행이 되도록 제작이 된 코드를 더 발전시켜보는 방향으로 진행하기로 했다. 코드가 굉장히 많았는데, 상의 끝에 폭탄 피하기 게임을 제작하기로 했다. 

코드는 https://ai-creator.tistory.com/529 에서 참고했다.

```python
import pygame  # 1. pygame 선언
import random
import os

pygame.init()  # 2. pygame 초기화

# 3. pygame에 사용되는 전역변수 선언

BLACK = (0, 0, 0)
size = [600, 800]
screen = pygame.display.set_mode(size)

done = False
clock = pygame.time.Clock()

def runGame():
    bomb_image = pygame.image.load('bomb.png')
    bomb_image = pygame.transform.scale(bomb_image, (50, 50))
    bombs = []

    for i in range(5):
        rect = pygame.Rect(bomb_image.get_rect())
        rect.left = random.randint(0, size[0])
        rect.top = -100
        dy = random.randint(3, 9)
        bombs.append({'rect': rect, 'dy': dy})

    person_image = pygame.image.load('person.png')
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

        pygame.display.update()

runGame()
pygame.quit()
```
&nbsp;
&nbsp;
### 2. 코드 분석하기

👩‍💻 init(), runGame(), quit()

: 우선 pygame을 초기화 해준 다음 게임 진행 코드가 들어있는 runGame을 실행한 뒤, quit로 종료시켜준다

👩‍💻 rect()

: 사각형 영역을 정의하여 객체를 생성, 활용할 때 사용된다. 위치 및 크기 속성을 사용할 수 있다.

| 속성이름  | 설명 |
| --- | --- |
| rect.left | 사각형의 왼쪽X 좌표 |
| rect.right | 사각형의 오른쪽 X 좌표 |
| rect.top | 사각형의 위쪽 Y 좌표 |
| rect.bottom | 사각형의 아래쪽 Y 좌표 |
| rect.center | 사각형의 중심 (x, y) 좌표 |
| rect.topleft | 사각형의 왼쪽 위 모서리 (left, top) |
| rect.bottomright | 사각형의 오른쪽 아래 모서리 (right, bottom) |

👩‍💻 while not done

: while문으로 특정 종료 조건이 아니라면 코드를 계속 반복하며 게임 화면을 그린다

👩‍💻 for event in pygame.event.get(): ~

: 방향키를 누르면 캐릭터가 이동하는 것을 구현하는 부분이다. 한 번에 5픽셀씩 이동한다

👩‍💻 bomb in bombs: ~

: 폭탄들이 들어있는 리스트를 위에서 만들었다. 한 번에 화면에 보이는 폭탄 개수만큼 폭탄을 생성하여 리스트에 넣고 각 폭탄들의 x위치와 떨어지는 속도를 랜덤하게 정의하여 떨어뜨린다. 폭탄이 화면을 벗어나면 리스트에서 제거하고 재생성한다.

👩‍💻 colliderect()

: 두 객체의 충돌 여부 확인, rect로 그린 사각형이 겹치면 충돌로 감지하고 게임을 끝낸다
&nbsp;
&nbsp;
&nbsp;
> 이제 앞으로 여러 기능도 추가하고 디자인도 바꿔보면서 게임을 더 다채롭게 만들어갈 예정이다. 첫번째 개발 과제는 타이머를 배경에 띄우도록 하는 것이다. 자세한 개발 과정은 다음 게시글로 작성하겠다.👋