---
layout: post
title:  "Pygame으로 게임 만들기 - 6. 번개 오브젝트 추가하기"
date:   2024-11-28 00:10:40 +0900
categories: [pygame]
tags: [pygame]
author: choi
---

# ✨속도가 증가하는 번개 오브젝트를 추가해보자✨

교수님께서 게임이 밋밋하다는 말씀과 함께 과제를 던져주셨다… 그 중 내가 맡은 부분은 번개 오브젝트 추가이다. 번개를 먹으면 속도가 조금씩 영구적으로 증가한다. 폭탄의 속도가 시간이 지날 수록 빨라지기때문에 굉장히 필요한 오브젝트라는 생각이 들었다.

솔직히 말하면 지난번에 만든 하트 오브젝트와 굉장히 비슷했다! 충돌시 발생하는 기능만 조금 바꾸면 되는 부분이었다. 이미지만 구해오면 됐다. 어떤 이미지로 할지 고민이 많았는데 마침 딱 맞는 번개 이미지를 찾게되었다. 

👩‍💻 if not fast_spawned and (elapsed_time - last_fast_time) >= 13000: ~

하트 오브젝트와 같다!! 떨어지는 시간 간격만 13초로 조정하였다. 

👩‍💻 offset = (fast.left - person.left, fast.top - person.top) ~

하트 오브젝트와 같다!! 충돌시 방향키를 눌렀을 때의 변화하는 크기 변수를 증가시켜준다. 

### ⚙️ 코드

```python
import pygame  # pygame 라이브러리 선언
import random
import os

pygame.init()  # pygame 초기화

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

#배경 이미지 로드
background_img = pygame.image.load('bomb_game/img/background.jpg')

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

# 번개 이미지 로드
fast_image = pygame.image.load('bomb_game/img/fast.png').convert_alpha()

# 마스크 생성 함수
def create_mask(image):
    surface = pygame.Surface(image.get_size(), pygame.SRCALPHA)
    surface.fill((0, 0, 0, 0))  # 투명한 배경 설정
    surface.blit(image, (0, 0))
    return pygame.mask.from_surface(surface)

# 충돌 검사 함수
def check_collision(person_mask, obj_mask, offset):
    return person_mask.overlap(obj_mask, offset)

# 캐릭터 마스크 생성 (맞춤형 히트박스)
person_idle_mask = create_mask(person_idle_image)
person_left_masks = [create_mask(img) for img in person_left_images]
person_right_masks = [create_mask(img) for img in person_right_images]

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

#번개 마스크 생성
fast_mask = pygame.mask.from_surface(fast_image)

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
    global heart_spawned, last_heart_time, person_speed, fast_spawned, last_fast_time

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

    fast_spawned = False # 화면에 번개가 존재하는가
    last_fast_time = 0 # 마지막 번개 생성 시간 기록

    heart_image = pygame.image.load('bomb_game/img/heart.png')
    heart_image = pygame.transform.scale(heart_image, (70, 70)) #생명 이미지 크기를 조절
    heart = pygame.Rect(heart_image.get_rect())

    fast_image = pygame.image.load('bomb_game/img/fast.png')
    fast_image = pygame.transform.scale(fast_image, (70, 91)) #번개 이미지 크기를 조절
    fast = pygame.Rect(fast_image.get_rect())

    bomb_image = pygame.image.load('bomb_game/img/bomb.png')  # 폭탄 이미지 파일을 불러옴
    bomb_image = pygame.transform.scale(bomb_image, (70, 120))  # 폭탄 이미지 크기를 70x120으로 조절
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
    person_speed = 5 # 캐릭터 이동속도 변수
    moving = False

    font = pygame.font.SysFont(None, 75)  # 게임오버 텍스트를 위한 폰트 설정
    life_font = pygame.font.SysFont(None, 50)  # 목숨 표시를 위한 폰트 설정

    game_over_time = None #게임 오버 시간을 저장하기 위한 변수

    while not done:
        clock.tick(30)  # 초당 30프레임 설정
        screen.blit(background_img,(0,0)) # 화면을 배경이미지로 채움

        # 키 이벤트 처리
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                done = True  # 게임 종료
                break
            elif event.type == pygame.KEYDOWN and not game_over:
                if event.key == pygame.K_LEFT:
                    person_dx = - person_speed  # 왼쪽 키를 누르면 왼쪽으로 이동
                    moving = True
                elif event.key == pygame.K_RIGHT:
                    person_dx = person_speed  # 오른쪽 키를 누르면 오른쪽으로 이동
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

            #10초마다 떨어지는 생명 추가
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

            #13초마다 떨어지는 번개 추가
            if not fast_spawned and (elapsed_time - last_fast_time) >= 13000:  # 13초마다 번개 생성
                last_fast_time = elapsed_time  # 마지막 생성 시간 업데이트
                fast = pygame.Rect(fast_image.get_rect())
                fast.left = random.randint(0, size[0])  # 번개의 x좌표를 무작위로 설정
                fast.top = -150  # 번개의 초기 y좌표 설정
                fast_dy = random.randint(3 + elapsed_time // 2000, 9 + elapsed_time // 2000)  # 낙하 속도 설정
                fast_spawned = True

            if fast_spawned:
                fast.top += fast_dy  # 번개를 아래로 이동
                if fast.top > size[1]:  # 화면 아래로 나가면
                    fast_spawned = False  # 다시 생성 가능하도록 설정
                    fast.top = -150

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

            #번개 충돌 검사 및 속도 증가
            offset = (fast.left - person.left, fast.top - person.top)
            if person_dx == 0:
                collision = check_collision(person_idle_mask, fast_mask, offset)
            elif person_dx < 0:
                collision = check_collision(person_left_masks[animation_index], fast_mask, offset)
            else:
                collision = check_collision(person_right_masks[animation_index], fast_mask, offset)
            
            if collision:
                print(f"Person position: {person.left}, {person.top}")
                print(f"Fast position: {fast.left}, {fast.top}")
                print(f"Offset: {offset}")
                print(f"Collision detected: {collision}")
                person_speed += 3
                fast_spawned = False
                fast.top = -150

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
                # lives -= 1

                if lives <= 0:
                    pygame.mixer.music.stop()  # 기존 배경음악 정지
                    pygame.mixer.music.load(bgm_3)  # 게임 종료 음악 로드
                    pygame.mixer.music.play()  # 게임 종료 음악 재생
                    pygame.mixer.music.set_volume(1.5)
                    pygame.mixer.music.queue(bgm_4)  # bgm_3 종료 후 bgm_4 재생 대기열에 추가
                    pygame.mixer.music.set_volume(0.5)
                    game_over = True
                    game_over_time = (pygame.time.get_ticks() - start_ticks) / 1000
                    bombs.clear()   # 게임 오버 시 모든 폭탄 제거
                    heart.top = -150 # 게임 오버 시 떨어지고 있는 생명 제거
                    heart_spawned = False
                    fast.top = -150 # 게임 오버 시 떨어지고 있는 번개 제거
                    fast_spawned = False
            
            #생명그리기
            if heart_spawned == True:
                screen.blit(heart_image, heart)

            #번개그리기
            if fast_spawned == True:
                screen.blit(fast_image, fast)

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
        screen.blit(background_img,(0,0))
        
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