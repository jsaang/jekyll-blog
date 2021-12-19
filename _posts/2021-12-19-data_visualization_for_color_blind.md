---
title: Color Blind를 위한 데이터 시각화
author:
  name: Jsang
  link: https://github.com/jsaang
date: 2021-12-18 00:30:00
categories: [Data, Visualization]
tags: [data visualization, color blind, pap]
---

<br />

색(Color)은 데이터 시각화의 가장 핵심적인 요소 중 하나다. 전달하고자 하는 메시지와 데이터의 형태에 따라 색을 사용하는 방식이 달라진다. 색을 통해 우리는 범주형(Qualitative) 데이터를 구분할 수 있고, 일방향 또는 양방향으로 변수의 연속성(Sequential, Diverging)을 표현할 수 있다. 흑백으로 인쇄된 데이터 서적에서 차트를 뚫어져라 살펴본 경험이 있다면, 데이터 시각화에서 색의 중요성은 더 말해야 입아플 뿐이다. 

<br />

![](/assets/posts/data_visualization_for_color_blind/fig_1.png)
_출처 : [https://github.com/OrdnanceSurvey/GeoDataViz-Toolkit/tree/master/Colours](https://github.com/OrdnanceSurvey/GeoDataViz-Toolkit/tree/master/Colours)_

<br />

그런데 이토록 중요한 색이란 놈이, **모두에게 동일하게 보이지 않는다**는 사실을 최근에 인지하게 되었다. 코로나 예방 차원에서 엘리베이터 버튼 위에 붙인 항균 필름 때문에, 시각 장애인들이 점자를 읽는데 어려움을 겪는다는 [기사](https://www.khan.co.kr/national/national-general/article/202009221108001)를 읽은 적이 있다. 우리는 쉽게 이런 실수를 한다. 경험해보지 못한 삶에 대해 잘 상상하지 못하기 때문이다. 

<br />

색각 이상(색약과 색맹)이 있는 사람은 세계 남성 인구 중 8%, 여성 인구 중 0.5%라고 한다. 국내에선 남성 인구의 6%, 여성 인구의 0.4% 정도로 알려져 있다. 또한 [Colblindor](https://www.color-blindness.com/50-facts-about-color-blindness/)에 따르면 색맹이 있는 사람의 99%는 적색이나 녹색을 구분하지 못하는 적녹색맹이다. (TMI로 색각 이상은 X 염색체에 문제가 있으면 발현되기 때문에 여성에 비해 남성의 비율이 훨씬 높다.)

<br />

즉, 100명이 당신의 차트나 대시보드를 본다면 그 중 최소 한 두명 이상은 색각 이상자(특히 적녹색맹)일 확률이 굉장히 높다는 뜻이다! 만약 전사적으로 공유하는 대시보드나 대외적으로 불특정 다수에게 공개되어야 하는 시각화 자료를 만들고 있다면 기억해둘만한 사실이다. **우리가 만든 차트를 보는 사람 중 일부는 색각 이상자다.**

<br />
<br />
<br />
<br />

![](/assets/posts/data_visualization_for_color_blind/fig_2.png)

<br />

왼쪽 상단에 있는 차트는, R 시각화 라이브러리 `ggplot2`의 디폴트 팔레트를 사용하여 그린 Scatter Plot이다. R을 즐겨 사용하는 분석가라면 익숙히 보았을만한 색상들이다. 나머지 차트는 색각 이상이 있는 사람의 눈으로 본 동일한 Scatter Plot이다. 내 눈에선 뚜렷이 구분되었던 원래의 차트가, 색각 이상이 있는 사람들의 눈에는 구분감이 약해진다. 특히 청색맹인 경우 파란점과 녹색점을 완전히 구분할 수 없게 된다. 

<br />

데이터를 시각적으로 표현할 때 가장 중요한 덕목은 독자의 수준을 이해하는 것이다. 데이터에 대한 이해도가 낮은 독자에게는 Scatter Plot이나 Box Plot 같은 간단한 시각화도 난해하게 느껴질 수 있다. 독자의 수준에는 데이터 리터러시 뿐 아니라, 색각 이상과 같은 신체적 조건도 포함될 수 있음을 인지해야 한다. 공들여 뽑아낸 멋진 시각화가, 누군가에겐 구분할 수 없는 점과 선의 나열일 수 있다.

<br />

그렇다면 색각 이상이 있는 독자를 배려할 수 있는 방법엔 어떤 것이 있을까?

<br />

- Selecting color blind friendly palettes
- Avoiding problematic color combinations
- Using different textures and patterns to highlight important information–not just color
- Using symbols and icons to supplement color-coded messages, warnings and alerts
- Using highly contrasted color combinations
- Adopting minimalistic design to help avoid unnecessary confusion

<br />

[How to Use Color Blind Friendly Palettes to Make Your Charts Accessible](https://venngage.com/blog/color-blind-friendly-palette/) 라는 글에선 위와 같이 조언한다. 하지만 현실적으로 매번 모든 항목을 고려하긴 어려우니 아래 내용들을 위주로 기억해두려고 한다.

<br />
<br />
<br />
<br />

## 1. 시각화가 색각 이상자에게 어떻게 보이는지 확인하자!

---

<br />

![](/assets/posts/data_visualization_for_color_blind/fig_3.png)
_구글에 검색해보면 다양한 Color Blind Simulator들을 찾을 수 있다_

<br />

우선은 내 시각화가 색각 이상자에게 어떻게 보이는지 확인해보자. 굳이 문제가 없는데 개선할 필요는 없을테니! 아래 사이트에서 각 색각 이상의 종류별로 이미지가 어떻게 보이는지 시뮬레이션해볼 수 있다. 만약 구분이 너무 어려운 부분을 발견한다면 그곳부터 개선해나가자.

<br />

- [https://www.color-blindness.com/coblis-color-blindness-simulator/](https://www.color-blindness.com/coblis-color-blindness-simulator/)
- [https://pilestone.com/pages/color-blindness-simulator-1](https://pilestone.com/pages/color-blindness-simulator-1)

<br />
<br />
<br />
<br />

## 2. 색각 이상자도 구분할 수 있는 색조합을 사용하자!

---

<br />

![](/assets/posts/data_visualization_for_color_blind/fig_4.png)

<br />

색각 이상자의 99%는 적녹색맹이기 때문에, 이들이 구분하기 어려운 색 조합 사용을 지양하는 것이 가장 효율적이 방법이 아닐까 싶다. 위와 같이 빨강, 초록을 함께 사용하면 적녹색맹(deuteranomaly, protanomaly)들은 구분하기 매우 어렵다. 빨강과 초록을 함께 사용해야만 하는 상황이라면, 빨강은 Vermillon(#D55E00), 초록은 Bluish Green(#009E73)으로 대체해보자.

<br />

![](/assets/posts/data_visualization_for_color_blind/fig_5.png)
_[Color Blind Friendly Palette 1](https://coolors.co/ffbe86-ffe156-ffe9ce-ffb5c2-3777ff)_

<br />

![](/assets/posts/data_visualization_for_color_blind/fig_6.png)
_[Color Blind Friendly Palette 1](https://coolors.co/4059ad-6b9ac4-97d8c4-eff2f1-f4b942)_

<br />

![](/assets/posts/data_visualization_for_color_blind/fig_7.png)
_Okabe Palette_

<br />

구글에 `color palette for color blind`를 검색하면 색각 이상자도 구분할 수 있는 다양한 색조합을 찾을 수 있다. 그 중에서도 Okabe라 불리는 위 팔레트는 모든 색각 이상자가 구분할 수 있는 범주형 색상 조합이라고 한다. 다양한 색상을 사용해보며, 색각 이상자까지 고려한 나만의 컬러 팔레트를 만들게 된다면 정말 뿌듯하지 않을까? 색상 조합을 결정했다면 [이 사이트](https://projects.susielu.com/viz-palette)에서 각종 차트별로 시뮬레이션을 해볼 수 있으니 참고하자.

<br />
<br />
<br />
<br />

## 3. 색이 아닌 시각화 요소도 활용해보자!

---

<br />

![](/assets/posts/data_visualization_for_color_blind/fig_8.png)
_직접 만든 조잡한 예시 : 색 이외에도 점, 선의 모양, 레이블로 그룹을 구분할 수 있게 표현하였다._

<br />

색 이외에도 모양, 패턴, 레이블 같은 다양한 데이터 시각화 요소들이 존재한다. 색만으로 한계가 느껴진다면 적극 활용해보도록 하자. 다만 너무 많은 시각화 요소의 사용은 복잡성을 높일 수 있으므로 주의해야 한다. 하나의 변수는 하나의 시각화 요소로 Mapping 한다는 원칙을 어기진 말자. 사실 위 차트들은 이 원칙을 어겼다.😂 (그룹이라는 한 변수가 색 + 모양, 레이블이라는 두가지 시각적 요소로 Mapping 되었다.)

<br />
<br />
<br />
<br />

![](/assets/posts/data_visualization_for_color_blind/fig_9.jpeg)

<br />

어쩌면 별 것 아닌 내용일 수 있다. 그리 확률 높지 않은 잠재적 독자들을 위해 이렇게까지 신경써야 하나? 싶은 생각이 들 수도 있다. 하지만 당신의 회사 대표님이 적색맹이라면 이 글을 다시 찾게 될 것이다. 또 프로와 아마추어의 차이는 디테일이라고 하지 않던가? 시각화는 데이터 분석가의 가장 강력한 무기 중 하나다. 이 무기를 예리하게 갈고 닦아 프로 데이터 분석가에 한걸음 다가서보자!

<br />
<br />
<br />
<br />

## 글을 쓰며 참고한 자료들

---

### 1) Articles

- [https://venngage.com/blog/color-blind-friendly-palette/](https://venngage.com/blog/color-blind-friendly-palette/)
- [https://brunch.co.kr/@suyoung/10](https://brunch.co.kr/@suyoung/10)
- [https://nuli.navercorp.com/community/article/1132656](https://nuli.navercorp.com/community/article/1132656)
- [https://clauswilke.com/dataviz/](https://clauswilke.com/dataviz/)

### 2) Services

- [https://projects.susielu.com/viz-palette](https://projects.susielu.com/viz-palette)
- [https://colorhunt.co/](https://colorhunt.co/)
- [https://www.color-hex.com/color-palettes/popular.php](https://www.color-hex.com/color-palettes/popular.php)
- [https://color.adobe.com/ko/create/color-wheel](https://color.adobe.com/ko/create/color-wheel)
- [https://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3](https://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3)
- [https://www.color-blindness.com/coblis-color-blindness-simulator/](https://www.color-blindness.com/coblis-color-blindness-simulator/)
- [https://pilestone.com/pages/color-blindness-simulator-1](https://pilestone.com/pages/color-blindness-simulator-1)