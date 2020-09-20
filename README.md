# 20202010 이덕규 방탈출

## 세팅
라이브러리는 bangtal과 time을 import 해주었습니다.
```python
from bangtal import *
import time
```
## 게임 시작
scene0에서 게임을 시작합니다.
```python
startGame(scene0)
```

## 장면
초기 씬을 설정하고 게임을 시작하기위한 플레이 버튼을 오브젝트로 생성합니다.
```python
scene0=Scene("", "Images/썸네일.png")

play=Object("Images/플레이.png")
play.locate(scene0, 85, 450)
play.show()
```

플레이 버튼을 클릭하면 효과음과 함께 다음씬으로 넘어갑니다.
```python
def play_onMouseAction(x, y, action):
    scene1.enter()
    click.play(False)
play.onMouseAction=play_onMouseAction
```

## 사운드
씬마다 배경음악을 넣어주었습니다.
배경음악은 해당 씬에 들어오면 재생하고 다음 씬으로 넘어가면 자동으로 중지되어 다음 씬의 배경음악을 재생합니다.
```python
bgm0=Sound("Images/시작브금.mp3")
def scene0_onEnter():
    bgm0.play(True)
scene0.onEnter=scene0_onEnter
def scene0_onLeave():
    bgm0.stop()
scene0.onLeave=scene0_onLeave
```

이외에도 오브젝트 클릭시, 문 열림 시, 아이템 발견 시 효과음을 넣어주었습니다.
```python
ding=Sound("Images/ding.mp3")
def Object_onMouseActionDefault(object, x, y, action):
    ding.play(False)
Object.onMouseActionDefault=Object_onMouseActionDefault

open_door=Sound("Images/open.mp3")

click=Sound("Images/click.mp3")
```
```python
drop=Sound("Images/drop.mp3")
def s1_EasterEgg_onMouseAction(x, y, action):
    wand.show()
    click.play(False)
s1_EasterEgg.onMouseAction=s1_EasterEgg_onMouseAction
```
## 미션
각 씬 마다 다양한 미션이 존재합니다. 힌트를 찾고 미션을 해결해야 다음 씬으로 가기위한 문이 활성화 됩니다.
```python
def s1_recyclebin_onMouseAction(x, y, action):
    s1_hint.show()
    s1_password.show()
    click.play(False)
s1_recycle_bin.onMouseAction=s1_recyclebin_onMouseAction

def password1_onMouseAction(x, y, action):
    showKeypad("스포방지", s1_door)
    click.play(False)
s1_password.onMouseAction=password1_onMouseAction

def s1_door_onKeypad():
    s1_door.setImage("Images/문_열림.png")
    s1_door.locked=False
    open_door.play(False)
    showMessage("문이 열렸다!")
s1_door.onKeypad=s1_door_onKeypad

s1_door.locked=True
def s1_door_onMouseAction(x, y, action):
    if s1_door.locked:
        showMessage("문이 잠겨있습니다.\n쓰레기통에서 힌트를 찾아 문을 여세요.")
        error.play(False)
    else:
        scene2.enter()
        click.play(False)
s1_door.onMouseAction=s1_door_onMouseAction
```

## time 라이브러리를 이용한 블루스크린 이벤트
time 라이브러리의 .sleep(sec)함수를 이용하여 몇초간의 정지를 통해 블루스크린 이벤트를 구현하였습니다.
```python
error=Sound("Images/error.mp3")
def s2_easter_onMouseAction(x, y, action):
    bluescreen.enter()
    time.sleep(10)
    error.play(False)
    B_return.show()
s2_easter.onMouseAction=s2_easter_onMouseAction
```

## 돌림판 미션
회전 버튼을 클릭하면 3*2로 배열된 6개의 타일이 시계방향으로 한 칸 씩 이동해 옆에 제시된 타일의 순서와
같아지도록 돌려야 문이열리는 돌림판 미션을 구현하였습니다.

우선 6개의 칸의 위치를 각각 함수로 선언합니다.
```python
location1_x=850
location1_y=515

location2_x=935
location2_y=515

location3_x=935
location3_y=430

location4_x=935
location4_y=345

location5_x=850
location5_y=345

location6_x=850
location6_y=430
```
이제 회전 버튼을 클릭 시 각각 타일의 x, y값에 다음 위치의 x, y값을 불러와 저장이 되고, .locate(scene, x, y)함수를 이용하여 새로운 위치에 위치시킵니다.

마지막으로 타일 1의 위치가 정답과 같아지면 문이 나타납니다.
```python
def turn_onMouseAction(x, y, action):
    global location6_x
    global location5_x
    global location4_x
    global location3_x
    global location2_x
    global location1_x
    global location6_y
    global location5_y
    global location4_y
    global location3_y
    global location2_y
    global location1_y
    
    save_x=location6_x
    location6_x=location1_x
    location1_x=location2_x
    location2_x=location3_x
    location3_x=location4_x
    location4_x=location5_x
    location5_x=save_x
    save_y=location6_y
    location6_y=location1_y
    location1_y=location2_y
    location2_y=location3_y
    location3_y=location4_y
    location4_y=location5_y
    location5_y=save_y

    chrome.locate(scene3, location1_x, location1_y)
    sticky.locate(scene3, location2_x, location2_y)
    photoshop.locate(scene3, location3_x, location3_y)
    word.locate(scene3, location4_x, location4_y)
    excel.locate(scene3, location5_x, location5_y)
    memo.locate(scene3, location6_x, location6_y)
    
    click.play(False)
    
    if location1_x==850 and location1_y==515:
        s3_door.show()
        showMessage("앗! 문이 나타났다!!")
        open_door.play(False)
turn.onMouseAction=turn_onMouseAction
```

## 게임 종료
마지막 씬에 도달하면 endame()함수와 연결된 버튼을 클릭하여 게임을 종료할 수 있습니다.
```python
###_____________장면4_____________###
scene4=Scene("윈도우10", "Images/윈도우10_배경.jpg")

bgm4=Sound("Images/엔딩브금.mp3")
def scene4_onEnter():
    bgm4.play(False)
scene4.onEnter=scene4_onEnter
def scene4_onLeave():
    bgm4.stop()
scene4.onLeave=scene4_onLeave

#___오브젝트___#
endgame=Object("Images/엔드게임.png")
endgame.locate(scene4, 40, 10)
endgame.show()

#___이벤트___#
def endgame_onMouseAction(x, y, action):
    click.play(False)
    endGame()
endgame.onMouseAction=endgame_onMouseAction
```
