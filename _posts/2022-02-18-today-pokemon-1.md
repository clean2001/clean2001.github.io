---
layout: single
title: "[Today's Pokemon] Today's Pokemon 개발 일지"
categories: PyQt5
tags: pyqt5 gui python pokemon pokebase pokeAPI
---

졸프로 펩타이드 스펙트럼과 관련된 gui 개발을 하게되었다. gui도 펩타이드 스펙트럼도 1도 몰라서 ~~심지어 펩타이드 스펙트럼은 첨들어봄~~ 정윤언니랑 열심히 공부중이다.
근데 또 막상 공부하니까 프로테오믹스도 PyQt5도 재미있는 것 같다.

인터넷에 무료로 공개된 책이랑 공식문서 봐가면서 기본적인 PyQt5 공부를 어느정도 마쳤는데, PyQt5에 익숙해질겸 간단한 개인 플젝을 하나 해보고 싶었다.
그래서 오늘 'Today's Pokemon(오늘의 포켓몬은?)'이라는 개인 프로젝트를 시작했다.


### Today's Pokemon은 어떤 프로그램?
그냥 하루에 한번 랜덤한 포켓몬을 뽑을 수 있는(?) 프로그램이다. 
1도 쓸모없지만 진짜 내가 좋아서 만드는 프로그램이다..ㅋㅋㅋㅋㅋ
포켓몬 애니메이션에서 오박사가 "오늘의 포켓몬은 뭘~까요?" 하면서 래버를 내리면 랜덤하게 포켓몬이 나오고 그 포켓몬에 대해 설명해주는 장면에서 떠올렸다.

기능은 
- 버튼을 누르면 랜덤한 포켓몬이 나옴
- detail 버튼을 누르면 포켓몬의 정보를 볼 수 있음 (dialog)
- 띠부띠부씰을 모으듯이 collection 기능
- 버튼은 24시간에 한번 누를 수 있게 함

라고 계획중인데 밑에 2개는 할 수 있으련지 모르겠다.


### 개발일지

오늘은 pokeAPI를 이용해서 창을 껐다 켤때마다 랜덤한 포켓몬이 뜨게 만들었다.

#### main window (main.py)

```python
# 오늘의 포켓몬 메인 윈도우
import sys, os, urllib, urllib.request
from PyQt5.QtWidgets import *
from PyQt5.QtCore import QtMsgType, Qt
from PyQt5.QtGui import QFontDatabase, QFont, QPixmap

import custom_modules as cm



class TodayPokemon(QWidget):

    def __init__(self):
        super().__init__()
        self.initWindow()
    
    def initWindow(self):
        self.vbox = QVBoxLayout()

        title_label = QLabel('오늘의 포켓몬은?', self)
        title_font = title_label.font()
        title_font.setPointSize(20)
        title_font.setFamily('KoPub돋움체 Bold')

        ## 기본 이미지
        cur_dir = cm.cur_dir()
        self.pixmap = QPixmap(cur_dir + '/image/파치리스.jpg')
        self.lbl_img = QLabel()
        self.lbl_img.setPixmap(self.pixmap.scaled(200, 200))

        ## 초기 name_label (빈 텍스트)
        self.name_label = QLabel('', self)

        self.vbox.setAlignment(Qt.AlignCenter)
        self.vbox.addWidget(title_label)
        self.vbox.addWidget(self.render_random_pokemon())
        self.vbox.addWidget(self.name_label)

        self.setLayout(self.vbox) # 이걸 넣어줘야 됐었구나

        self.render_random_pokemon()

        # size, locaion setting
        self.setWindowTitle("Today's Pokemon")
        self.setGeometry(300, 300, 500, 500) # 앞이 위치 뒤가 크기
        self.show()


    def render_basic_image(self):
        #pixmap을 이용해서 이미지 렌더링
        cur_dir = cm.cur_dir()
        self.pixmap = QPixmap(cur_dir + '/image/파치리스.jpg')
        self.lbl_img = QLabel()
        
        self.lbl_img.setPixmap(self.pixmap.scaled(200, 200))
        return self.lbl_img

    def render_random_pokemon(self):
        [pokemon, image] = cm.get_random_pokemon()
        self.name_label.setText(str(pokemon.name))
        data = urllib.request.urlopen(str(image.url)).read()
        self.pixmap.loadFromData(data)
        self.lbl_img = QLabel()
        self.lbl_img.setPixmap(self.pixmap.scaled(200, 200))
        print(str(image.url))
        return self.lbl_img

        
        
if __name__ == '__main__':
    app = QApplication(sys.argv)
    ex = TodayPokemon()
    sys.exit(app.exec_())
```

이게 전체코드이고

```python
        ## 기본 이미지
        cur_dir = cm.cur_dir()
        self.pixmap = QPixmap(cur_dir + '/image/파치리스.jpg')
        self.lbl_img = QLabel()
        self.lbl_img.setPixmap(self.pixmap.scaled(200, 200))
```

이부분에서 기본이미지를 띄운다.
하지만 현재 코드에서는 랜덤 포켓몬을 띄우는 함수가 실행된 후 창이 열리므로 기본이미지는 볼 수 없다.
나중에 버튼을 누르면 랜덤 포켓몬을 띄우도록 바꿔야한다.

```python
    def render_random_pokemon(self):
        [pokemon, image] = cm.get_random_pokemon()
        self.name_label.setText(str(pokemon.name))
        data = urllib.request.urlopen(str(image.url)).read()
        self.pixmap.loadFromData(data)
        self.lbl_img = QLabel()
        self.lbl_img.setPixmap(self.pixmap.scaled(200, 200))
        print(str(image.url))
        return self.lbl_img
```

이 함수에서 랜덤 포켓몬 이미지와 이름을 띄운다.

좀 헤맸던 곳은, str(image.url)로 하면 사진이 있는 url이 반환되니까

```python
self.pixmap = QPixmap(str(image.url))
```
이렇게 하면 될거라고 생각했는데 그렇지 않았다.

urllib.request의 urlopen으로 이미지 데이터를 만들어서 loadFromDate로 열어야한다고 함. ([출처](https://stackoverflow.com/questions/11073972/pyqt-set-qlabel-image-from-url))


#### Pokebase

PokeAPI를 호출해서 랜덤함 포켓몬 정보를 얻어오는 코드는 다른 파일에 따로 정의했다.

어제 알았는데 포켓몬 도감 정보를 무료로 공개한 [PokeAPI](https://pokeapi.co/)가 있다는 것을 처음 알았다..!

심지어 포켓몬뿐만 아니라 열매 정보 같은 것도 있는 것 같음... 뭐 더 있을 거 같은데 뭐가 있는진 제대로 보지 않아서 잘 모르겠다.

심지어 사람들이 여러 언어에서 편하게 쓸 수 있도록 [라이브러리](https://pokeapi.co/docs/v2)를 다 만들어 놨다.

나는 지금 파이썬을 쓰고 있으니까 [Pokebase](https://github.com/PokeAPI/pokebase)를 이용했다.

이런 라이브러리는 어떻게 만드는거지 진짜 존경스럽다...

아무튼 pokebase를 쓰려면 'pip install pokebase'로 설치하고, import pokebase를 한 뒤 쓸 수 있다.

```python
import sys, os
import random as rd
import pokebase as pb

limit = 151

def cur_dir():
    return os.path.dirname(os.path.realpath(__file__))

def gen_rand_num():
    return rd.randint(1, limit+1)

def get_random_pokemon():
    number = gen_rand_num()
    pokemon = pb.pokemon(number)
    image = pb.SpriteResource('pokemon', number)
    return [pokemon, image]
```

pokemon = pb.pokemon(number)로 포켓몬 정보(이름, 타입 등등), image = pb.SpriteResourse('pokemon', number)로 사진을 가져올 수 있다.

진짜 간단하다...

지금은 limit = 151이므로 1~151번인 1세대 포켓몬만 볼 수 있다. 나중에 완성되면 limit을 못해도 4세대까지는 늘릴것이다.


### 결과

첫번째 실행

![첫번째](/assets/images/2023/pyqt/2023-02-18-1.png)


두번째 실행

![두번째](/assets/images/2023/pyqt/2023-02-18-2.png)

이렇게 창을 껐다 켤 때마다 다른 포켓몬을 볼 수 있다.

점점 발전시켜서 디자인도 예쁘게 21세기 UI로 진화시켜야겠다.

끗