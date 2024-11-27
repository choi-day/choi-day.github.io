---
layout: post
title:  "Pygame으로 게임 만들기 - 4. 먹으면 생명이 늘어나는 오브젝트 만들기"
date:   2024-11-27 00:05:40 +0900
categories: [pygame]
tags: [pygame]
author: choi
---

# ✨먹으면 생명이 늘어나는 오브젝트를 추가해보자✨

위에서 폭탄만 떨어지니 난이도도 어렵고 다양성도 떨어진다는 생각을 했다. 게임을 더 재미있게 만들기 위해서는 어떻게 해야할까? 하다가 먹으면 생명이 늘어나는 하트 객체를 구현하기로 했다. 

다른 팀원이 충돌 정확성을 높이기 위해 캐릭터끼리의 부딪힘을 판단하는 방법을 mask를 이용한 방법으로 바꾸었다. 그래서 하트 객체 또한 생성 후 mask를 만들어 씌운 뒤 화면에 띄우고, 부딪혔을 땐 생명을 구현하는 코드를 작성해야한다. 고고싱… 

👩‍💻 pygame.image.load

경로를 지정하여 이미지파일을 불러올 수 있다. 하트 객체 또한 시각적으로 보이고 상호작용하는 객체이므로 하트 이미지가 필요하다. 

이미지는 ``freepik``이라는 사이트에서 구했다. 아마 이번 프로젝트의 대부분의 이미지는 이 사이트에서 구해왔는데, 이미지 소스도 많고, 무료이미지도 많고, 저작권 지키는 법도 간단하여 애용했다.

👇👇👇

<https://www.freepik.com/>

👩‍💻 create_heart_mask

이미지의 사이즈를 얻어 배경을 투명으로 설정한 surface를 얻어 마스크를 생성하고 씌운다. 사각형(rect)보다 더 정확히 이미지에 들어맞는 mask를 사용하여 충돌판단을 자연스럽게 할 수 있다. 

사실 이 코드는 다른 객체의 마스크 생성시에도 이미지파일만 다를 뿐 완전히 동일하다는 것을 깨닫고 추후에 함수화하여 구현하게된다.

👩‍💻 if not heart_spawned and (elapsed_time - last_heart_time) >= 10000: ~

하트가 화면에 있는지를 표시하는 heart_spawned 변수를 정의하고 판단한다. 화면에 하트가 없고 지난번에 하트가 떨어진 시간이 현재 시간과의 차가 10초 이상이라면 하트를 떨어뜨린다.

폭탄과 마찬가지로 x좌표, 떨어지는 속도는 무작위이다. 하트가 화면을 벗어나면 제거하고 heart_spawned를 false로 업데이트한다. 

👩‍💻 offset = (heart.left - person.left, heart.top - person.top)~

하트 객체와 캐릭터 객체의 거리를 계산하는 offset객체를 가져온다. 이 offset을 check_collision에 인수로 전달하여 충돌을 확인한다. 만약 충돌하면 생명을 1증가시키고 하트 객체를 화면에서 지운다. 

만약 지우지 않으면 하트 객체가 계속하여 부딪한다고 판단되어 생명이 엄청나게 빠른 속도로 늘어나기 시작한다… 

👩‍💻 screen.blit(heart_image, heart)

아무리 기능을 정의해도 blit함수로 화면에 그려주지 않으면 화면에 뜨지 않는다.

pygame은 blit한 순서대로 화면에 쌓인다. 따라서 배경요소는 가장 먼저 blit해주어야 한다. 

### ⚙️ 코드

```python
import pygame  # pygame 라이브러리 선언
import random
import os

pygame.init()  # pygame 초기화
pygame.mixer.init() # pygame mixer 초기화 (배경 음악)

try:
    pygame.mixer.init() # pygame mixer 초기화 (배경 음악)
    pygame.mixer.music.set_volume(0.3)
except pygame.error as e:
    print("재생 장치 관련 오류로 인해 BGM이 나오지 않습니다.")
    print(f"{e}")

# 게임 화면 크기 및 색상 설정
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
size = [600, 800]
screen = pygame.display.set_mode(size)

# 배경 음악 파일 로드
bgm_1 = 'bomb_game/sound/BGM1.wav'
bgm_2 = 'bomb_game/sound/BGM2.wav'
bgm_3 = 'bomb_game/sound/BGM3.wav'
bgm_4 = 'bomb_game/sound/BGM4.wav'

# 배경 음악 소리 조절
pygame.mixer.music.set_volume(0.3)

# 캐릭터 애니메이션 이미지 로드
person_idle_image = pygame.image.load('bomb_game/img/person_idle.png').convert_alpha()  # 알파 채널 활성화
person_left_images = [
    pygame.image.load('bomb_game/img/person_left1.png').convert_alpha(),
    pygame.image.load('bomb_game/img/person_left2.png').convert_alpha()
]
person_right_images = [
    pygame.image.load('bomb_game/img/person_right1.png').convert_alpha(),
    pygame.image.load('bomb_game/img/person_right2.png').convert_alpha()
]

# 폭탄 이미지 로드
bomb_image = pygame.image.load('bomb_game/img/bomb.png').convert_alpha()  # 알파 채널 활성화

# 생명 이미지 로드
heart_image = pygame.image.load('bomb_game/img/heart.png').convert_alpha()

# 캐릭터 마스크 생성 함수
def create_mask(image):
    surface = pygame.Surface(image.get_size(), pygame.SRCALPHA)
    surface.fill((0, 0, 0, 0))  # 투명한 배경 설정
    surface.blit(image, (0, 0))
    return pygame.mask.from_surface(surface)

# 캐릭터 마스크 생성 (맞춤형 히트박스)
person_idle_mask = create_mask(person_idle_image)
person_left_masks = [create_mask(img) for img in person_left_images]
person_right_masks = [create_mask(img) for img in person_right_images]

# 폭탄 마스크 생성 함수
def create_bomb_mask(bomb_image):
    surface = pygame.Surface(bomb_image.get_size(), pygame.SRCALPHA)
    surface.fill((0, 0, 0, 0))  # 투명한 배경 설정
    # 폭탄의 충돌 영역을 설정 (예: 폭탄 중앙 부분만 충돌)
    surface.blit(bomb_image, (0, 0))
    return pygame.mask.from_surface(surface)

# 폭탄 마스크 생성
bomb_mask = create_bomb_mask(bomb_image)

#생명 마스크 생성 함수
def create_heart_mask(heart_image):
    surface = pygame.Surface(heart_image.get_size(), pygame.SRCALPHA)
    surface.fill((0, 0, 0, 0))
    surface.blit(heart_image, (0, 0))
    return pygame.mask.from_surface(surface)

#생명 마스크 생성
heart_mask = create_heart_mask(heart_image)

# 폭탄 충돌 검사 함수
def check_collision(person_mask, bomb_mask, offset):
    return person_mask.overlap(bomb_mask, offset)

#하트 충돌 검사 함수
def check_get_heart(person_mask, heart_mask, offset):
    return person_mask.overlap(heart_mask, offset)

# 모든 이미지 크기 조정
person_idle_image = pygame.transform.scale(person_idle_image, (100, 100))
person_left_images = [pygame.transform.scale(img, (100, 100)) for img in person_left_images]
person_right_images = [pygame.transform.scale(img, (100, 100)) for img in person_right_images]

# 캐릭터 마스크 생성
person_idle_mask = pygame.mask.from_surface(person_idle_image)
person_left_masks = [pygame.mask.from_surface(img) for img in person_left_images]
person_right_masks = [pygame.mask.from_surface(img) for img in person_right_images]

# 폭탄 마스크 생성
bomb_mask = pygame.mask.from_surface(bomb_image)

# 생명 마스크 생성
heart_mask = pygame.mask.from_surface(heart_image)

# 애니메이션 관련 변수
animation_index = 0  # 현재 애니메이션 프레임 인덱스
animation_speed = 0.2  # 애니메이션 속도 (작을수록 빠름)
animation_timer = 0  # 프레임 전환을 위한 시간 누적
person_image = person_idle_image  # 초기 이미지는 가만히 있는 상태

# 타이머에 사용할 폰트 설정
game_font = pygame.font.Font(None, 200)

def reset(): # 게임 상태와 관련된 변수 초기화 함수
    global done, clock, start_ticks, game_over, lives, elapsed_time
    done = False # 게임 루프를 제어하는 변수
    clock = pygame.time.Clock() # 프레임 속도 제어를 위한 시계 객체
    start_ticks = pygame.time.get_ticks() # 시작 시간 기록
    game_over = False # 게임 오버 상태를 나타내는 변수
    lives = 3 # 초기 목숨 개수 설정
    elapsed_time = 0 # 초기화 추가

def text_objects(text, font): # START버튼 
    textSurface = font.render(text, True, WHITE)
    return textSurface, textSurface.get_rect()

def button(msg,x,y,w,h,action=None,fcolor=WHITE): # START버튼 상세
    mouse = pygame.mouse.get_pos()
    click = pygame.mouse.get_pressed()

    if x+w>mouse[0]> x and y+h > mouse[1] > y:
        pygame.draw.rect(screen, WHITE, (x,y,w,h) )
        fcolor=BLACK

        if click[0] == 1 and action !=None:
            return True
    else:
        pygame.draw.rect(screen, BLACK, (x,y,w,h))
        fcolor=WHITE

    smallTEXT = pygame.font.SysFont("malgungothic", 30)
    textSurf = smallTEXT.render(msg, True, fcolor)
    textRect = textSurf.get_rect()
    textRect.center = ((x+(w/2)),(y+(h/2)))
    screen.blit(textSurf, textRect)

# 게임 실행 함수 정의
def runGame(): 

    global done, game_over, lives, start_ticks, elapsed_time, animation_index, animation_timer
    global heart_spawned, last_heart_time

    try:
        pygame.mixer.music.load(bgm_1)  # 첫 번째 음악을 로드
        pygame.mixer.music.queue(bgm_2) # 두 번째 음악을 대기열에 추가
        pygame.mixer.music.play()  # 첫 번째 음악 재생
    except pygame.error as e:
        print("재생 장치 관련 오류로 인해 BGM이 나오지 않습니다.")
        print(f"{e}")
        
    reset()

    heart_spawned = False # 화면에 하트가 존재하는가
    last_heart_time = 0 # 마지막 하트 생성 시간 기록

    heart_image = pygame.image.load('bomb_game/img/heart.png')
    heart_image = pygame.transform.scale(heart_image, (70, 70)) #생명 이미지 크기를 조절
    heart = pygame.Rect(heart_image.get_rect())

    bomb_image = pygame.image.load('bomb_game/img/bomb.png')  # 폭탄 이미지 파일을 불러옴
    bomb_image = pygame.transform.scale(bomb_image, (70, 120))  # 폭탄 이미지 크기를 50x75으로 조절
    bombs = []  # 폭탄 정보를 담을 리스트 초기화

    # 초기 폭탄 5개 생성
    for i in range(5):
        rect = pygame.Rect(bomb_image.get_rect())  # 폭탄 이미지 크기와 위치 설정
        rect.left = random.randint(0, size[0])  # 폭탄의 x 좌표를 무작위로 설정
        rect.top = -100  # 폭탄의 초기 y 좌표 설정
        dy = random.randint(3 + elapsed_time // 2000, 9 + elapsed_time // 2000)  # 폭탄 낙하 속도를 무작위로 설정
        bombs.append({'rect': rect, 'dy': dy})  # 폭탄 정보를 리스트에 추가

    # 캐릭터 이미지 불러오기 및 초기 위치 설정
    person_image = pygame.image.load('bomb_game/img/person_idle.png')
    person_image = pygame.transform.scale(person_image, (100, 100))
    person = pygame.Rect(person_image.get_rect())
    person.left = size[0] // 2 - person.width // 2  # 캐릭터를 화면 중앙에 배치
    person.top = size[1] - person.height  # 캐릭터를 화면 하단에 배치
    person_dx = 0  # 캐릭터의 초기 이동 속도 설정
    moving = False

    font = pygame.font.SysFont(None, 75)  # 게임오버 텍스트를 위한 폰트 설정
    life_font = pygame.font.SysFont(None, 50)  # 목숨 표시를 위한 폰트 설정

    game_over_time = None #게임 오버 시간을 저장하기 위한 변수

    while not done:
        clock.tick(30)  # 초당 30프레임 설정
        screen.fill(BLACK)  # 화면을 검은색으로 채움

        # 키 이벤트 처리
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                done = True  # 게임 종료
                break
            elif event.type == pygame.KEYDOWN and not game_over:
                if event.key == pygame.K_LEFT:
                    person_dx = -5  # 왼쪽 키를 누르면 왼쪽으로 이동
                    moving = True
                elif event.key == pygame.K_RIGHT:
                    person_dx = 5  # 오른쪽 키를 누르면 오른쪽으로 이동
                    moving = True
            elif event.type == pygame.KEYUP:
                if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                    person_dx = 0  # 키를 떼면 이동 정지
                    moving = False

        # 게임 오버가 아닐 때 게임 로직 실행
        if not game_over:
            # 경과 시간 계산 및 표시
            elapsed_time = pygame.time.get_ticks() - start_ticks
            timer = game_font.render(f"{elapsed_time // 1000} : {elapsed_time % 100:02d}", True, WHITE).convert_alpha()
            timer.set_alpha(90) #투명도 설정 0~255
            screen.blit(timer, (size[0] // 2 - timer.get_width() // 2, size[1] // 2 - timer.get_height() // 2))

            # 폭탄 이동 및 화면을 벗어난 폭탄 제거 후 새 폭탄 추가
            for bomb in bombs:
                bomb['rect'].top += bomb['dy']  # 폭탄의 y 좌표에 속도를 더하여 아래로 이동
                if bomb['rect'].top > size[1]:  # 폭탄이 화면 하단을 벗어났을 경우
                    bombs.remove(bomb)
                    rect = pygame.Rect(bomb_image.get_rect())
                    rect.left = random.randint(0, size[0])  # 새 폭탄의 x 좌표를 무작위로 설정
                    rect.top = -100  # 새 폭탄의 y 좌표 초기화
                    dy = random.randint(3 + elapsed_time // 2000, 9 + elapsed_time // 2000)  # 폭탄 낙하 속도를 무작위로 설정
                    bombs.append({'rect': rect, 'dy': dy})  # 새 폭탄 추가

            #20초마다 떨어지는 생명 추가
            if not heart_spawned and (elapsed_time - last_heart_time) >= 10000:  # 10초마다 하트 생성
                last_heart_time = elapsed_time  # 마지막 생성 시간 업데이트
                heart = pygame.Rect(heart_image.get_rect())
                heart.left = random.randint(0, size[0])  # 하트의 x좌표를 무작위로 설정
                heart.top = -150  # 하트의 초기 y좌표 설정
                heart_dy = random.randint(3 + elapsed_time // 2000, 9 + elapsed_time // 2000)  # 낙하 속도 설정
                heart_spawned = True

            if heart_spawned:
                heart.top += heart_dy  # 하트를 아래로 이동
                if heart.top > size[1]:  # 화면 아래로 나가면
                    heart_spawned = False  # 다시 생성 가능하도록 설정
                    heart.top = -150

            # 캐릭터 이동 처리
            person.left += person_dx  # 이동 속도를 현재 위치에 더함
            if person.left < 0:  # 화면 왼쪽을 넘어가지 않도록
                person.left = 0
            elif person.left > size[0] - person.width:  # 화면 오른쪽을 넘어가지 않도록
                person.left = size[0] - person.width
                
            if moving:
                animation_timer += animation_speed
                if animation_timer >= 1:
                    animation_timer = 0
                    animation_index = (animation_index + 1) % len(person_left_images)
                if person_dx < 0:  # 왼쪽으로 이동 중
                    person_image = person_left_images[animation_index]
                elif person_dx > 0:  # 오른쪽으로 이동 중
                    person_image = person_right_images[animation_index]
            else:
                person_image = person_idle_image  # 이동하지 않을 때는 기본 이미지

            screen.blit(person_image, person)  # 캐릭터를 화면에 그림

            #생명 충돌 검사 및 생명 증가
            offset = (heart.left - person.left, heart.top - person.top)
            if person_dx == 0:
                collision = check_collision(person_idle_mask, heart_mask, offset)
            elif person_dx < 0:
                collision = check_collision(person_left_masks[animation_index], heart_mask, offset)
            else:
                collision = check_collision(person_right_masks[animation_index], heart_mask, offset)
            
            if collision:
                lives += 1
                heart_spawned = False
                heart.top = -150

        # 폭탄 충돌 검사 및 목숨 감소
        for bomb in bombs[:]:
            offset = (bomb['rect'].left - person.left, bomb['rect'].top - person.top)
            if person_dx == 0:
                collision = check_collision(person_idle_mask, bomb_mask, offset)
            elif person_dx < 0:
                collision = check_collision(person_left_masks[animation_index], bomb_mask, offset)
            else:
                collision = check_collision(person_right_masks[animation_index], bomb_mask, offset)
            
            if collision:
                bombs.remove(bomb)
                rect = pygame.Rect(bomb_image.get_rect())
                rect.left = random.randint(0, size[0])
                rect.top = -100
                dy = random.randint(3 + elapsed_time // 2000, 9 + elapsed_time // 2000)
                bombs.append({'rect': rect, 'dy': dy})
                lives -= 1

                if lives <= 0:
                    game_over = True
                    game_over_time = (pygame.time.get_ticks() - start_ticks) / 1000
                    bombs.clear()   # 게임 오버 시 모든 폭탄 제거
                    heart.top = -150 # 게임 오버 시 떨어지고 있는 생명 제거
                    heart_spawned = False
            
            #생명그리기
            if heart_spawned == True:
                screen.blit(heart_image, heart)
            
            # 폭탄 그리기
            screen.blit(bomb_image, bomb['rect'])

            # 목숨 개수 표시
            lives_text = life_font.render(f"Lives: {lives}", True, WHITE)
            screen.blit(lives_text, (10, 10))

        # 게임 오버 시 메시지 출력
        if game_over:
            game_over_text = font.render("Game Over", True, WHITE)
            game_over_time_text = font.render(f"Time: {game_over_time:.2f} sec", True, WHITE)
            screen.blit(game_over_text, (size[0] // 2 - game_over_text.get_width() // 2, size[1] // 2 - game_over_text.get_height() // 2))
            screen.blit(game_over_time_text, (size[0] // 2 - game_over_time_text.get_width() // 2, size[1] // 2 + game_over_text.get_height()))
            endBtn=button("Quit", 100,525,400,100,action=True,fcolor=WHITE)
            if endBtn == True:
                return pygame.quit()
            reBtn=button("RE?", 100,650,400,100,action=True,fcolor=WHITE)
            if reBtn == True:
                return runGame()
            
            pygame.display.update()

        pygame.display.update()  # 화면 업데이트

def intro():
    intro = True

    while intro:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()

        strBtn=button("Start",100,525,400,100,action=True,fcolor=WHITE)
        if strBtn == True:
            return runGame()
        endBtn=button("Quit", 100,650,400,100,action=True,fcolor=WHITE)
        if endBtn == True:
            return pygame.quit()
        pygame.display.update()        

intro()

pygame.quit()  # 게임 종료
```