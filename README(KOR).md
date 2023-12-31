<h1>:cherry_blossom:OpenCV 없이 JavaScript로 구현하는 영상처리 소프트웨어</h1>

<hr>
<span>
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=JavaScript&logoColor=black"/>
  <img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=HTML5&logoColor=black"/>
  <img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=CSS3&logoColor=black"/>
</span>
<br>
<img src="https://img.shields.io/badge/visualstudiocode-007ACC?style=for-the-badge&logo=visualstudiocode&logoColor=white"/>

:calendar: 2023.04.11 ~ 04.16<br>
:raising_hand: 개인 미니 프로젝트<br>


<hr>

영상처리 프로그램은 대부분 오픈소스로 공개되어 있는 OpenCV를 사용한다. OpenCV는 Open Source Computer Vision의 약자로 다양한 영상/동영상 처리에 사용할 수 있는 오픈소스 라이브러리로, 인텔이 컴퓨터 비전과 인공지능을 발달시키고자 하는 바람으로 출시한 것이다. 하지만 이번 프로젝트에서는 OpenCV를 사용하지 않고, JavaScript, HTML5, CSS으로 영상처리 알고리즘을 이해하고 코드화하여 영상처리 소프트웨어를 만들어보려고 한다.

자세한 프로젝트 설명은 <a href="https://hanav.tistory.com/147">▶여기◀</a>를 클릭!


<h2>Step 1: 간단한 난수 배열로 영상처리 알고리즘 이해하고 적용해보기</h2>
영상처리에 어떤 알고리즘들이 사용되는지 이전에 배운 적이 없으므로, 바로 이미지 파일에 적용하기 전에 단순한 난수 배열에 적용해보면서 이해했다.

<h2>Step2: RAW 이미지 파일에 영상처리 알고리즘 적용해보기</h2>
이제 RAW 이미지 파일에 적용해보았는데, 이미지 파일은 난수 배열보다 복잡하기 때문에 고려해야할 점이 있다.

1. 이미지 파일은 RGB값과 Alpha 값이 묶여서 하나의 픽셀로 나타난다. 난수 배열에서는 하나의 픽셀이 하나의 숫자였다면, 이미지 파일에서는 (r, g, b, alpha) 가 하나의 픽셀이 된다. 여기서 만약 칼라 사진을 적용시킨다면 RGB값이 모두 달라 삼차원 배열을 설정해야 하겠지만, 이번 프로젝트에서는 간단하게 GrayScale 이미지를 사용할 것이다. GrayScale 이미지는 R=G=B (Alpha 값도 255, 투명도가 없는 이미지 파일을 사용)이므로 이차원 배열만 있어도 구현할 수 있다.
2. HTML5 캔버스는 선과 면은 표현 가능하지만 점은 표현하지 못한다. 그래서 픽셀값 하나하나를 불러와 모여 있는 이미지를 표현할 수 없는데, 이는 픽셀 값을 모아서 임의의 종이에 먼저 붙이고, 종이를 캔버스에 붙이는 방식으로 해결하기로 했다.


<h2>Step3: 영상처리 심화 알고리즘 학습하고 적용해보기</h2>
<h3>1) 화소 점 처리</h3>
화소 점 처리란, 주변 화소 값에 상관없이 단순히 그 값만 변하는 것을 말한다.

-더하기 = 밝게 하기<br>
-빼기 = 어둡게 하기<br>
-곱하기 = 밝게하는데 밝은 곳은 더 밝아지고, 어두운 곳은 덜 밝아지게 하기<br>
-나누기 = 어둡게 하는데 어두운 곳은 더 어두워지고, 밝은 곳은 덜 어둡게 하기<br>

더하기와 빼기, 곱하기와 나누기는 결국 같으므로 같이 조절할 수 있게 설정하였다. 그리고 0부터 255까지의 값만 가능하므로, 0이나 255를 벗어나는 경우가 발생하면 각각 0과 255로 받도록 제한조건도 설정해주었다.
```javascript
if (inImage[i][k] + value < 0 )
    outImage[i][k] = 0;
else if (inImage[i][k] + value > 255 )
    outImage[i][k] = 255;
else 
    outImage[i][k] = inImage[i][k]+value;
outImage[i][k] = inImage[i][k] * value;
```
또한, 색상을 반전시키는 것도 최대 명도값인 255에서 이전 픽셀값을 빼주면 되기 때문에 매우 간단하게 표현 가능하다.
```javascript
outImage[i][k] = 255 - inImage[i][k];
```

명도가 변할 때 자연스럽게 이어지지 않고 경계가 지면서 미국 카툰 느낌이 나도록 보정할 수도 있다. 
0~255까지 범위를 정해 일정 범위는 같은 픽셀값으로 설정해주면 된다.


<h3>2) 히스토그램 처리</h3>

히스토그램 처리를 통해 히스토그램으로 명도의 분포를 파악할 수 있고, 이를 잘 사용하면 명도 대비에 변화를 줄  수 있다.

히스토그램을 이용할 수 있는 제일 간단한 예는 이미지를 오직 255 또는 0으로 나타내는 고대비 이미지를 만들 때이다. 그냥 편하게 255와 0의 중간값인 127.5를 기준으로 할 수도 있지만, 만약 사진 자체가 밝아서 모두 128보다 큰 명도값을 갖는다면 전체가 하얀색으로 될 것이다. 이 때, 히스토그램을 활용해서 전체 이미지의 명도 분포를 바탕으로 기준값을 설정해주면 더 안전하고 고르게 고대비 효과를 줄 수 있다.

127.5를 기준으로 고대비 처리
```javascript
if (inImage[i][k] > (0 + 255)/2) {
    outImage[i][k] =  255;
} else {
    outImage[i][k] = 0;
}
```                            
히스토그램 평균을 기준으로 고대비 처리
```javascript
avgValue = hapValue / (inH*inW);

for(var i=0; i<inH; i++) {
    for (var k=0; k<inW; k++) {
        if (inImage[i][k] <= avgValue)
            outImage[i][k] = 0;
        else
            outImage[i][k] = 255;
        }
    }
```       
히스토그램 중간값을 기준으로 고대비 처리
```javascript
oneAry.sort();
centerValue = oneAry[parseInt((inH*inW)/2)];

for(var i=0; i<inH; i++) {
    for (var k=0; k<inW; k++) {
        if (inImage[i][k] <= centerValue)
            outImage[i][k] = 0;
        else
            outImage[i][k] = 255;
    }
 ```

이 외에도 심화 이론을 적용해서 히스토그램의 명암 분포를 늘림으로써 낮은 명암대비를 보이는 영상의 화질을 향상시킬 수도 있다. 


<h3>3) 기하학적 변환</h3>
기하학적 변환은 화소의 공간적 위치를 재배치하는 것이다. 즉, 명도값이 변하는 것이 아니라 위치만 변한다. 제일 단순한 것은 좌우반전과 상하반전이다. 생각하기도 쉽고 코드도 단순하다.<br>

상하반전
```javascript
 outImage[i][k] = inImage[outH - i - 1][k];
```

좌우반전
```javascript
outImage[i][k] = inImage[i][outW - k -1];
```
이미지를 회전시키거나 확대/축소시키는 것도 픽셀의 위치와 값에 변화를 주면 된다.

하지만 더 잘 축소하고 확대하려면 알고리즘이 더 복잡해진다. 나는 아래와 같이 적용해보았다.


화질 개선을 고려하지 않은 축소
```javascript
outImage[parseInt(i*scale)][parseInt(k*scale)] = inImage[i][k];
```

화질 개선을 고려한 축소
```javascript
outImage[i][k] = (inImage[i*2+1][k*2+1] +inImage[i*2+1][k*2]+inImage[i*2][k*2+1]+inImage[i*2][k*2])/4;
```

2배 확대도 마찬가지이다. 화질을 고려하지 않고 그냥 인접한 4개의 픽셀을 같은 값으로 할 수 있는데, 이는 픽셀 형태가 2배로 늘어나기 때문에 경계면이 매끄럽지 못하다. 따라서 고안한 방법은 원시 화소의 값의 가중치를 제일 높게 잡은 상태에서 주변 화소들의 값을 고려하여 출력 픽셀값을 받는 것이었다. (자세한 내용은 위 링크)



<h3>4) 화소 영역 처리</h3>
화소 영역 처리는 주변 화소의 값도 같이 고려하는 공간 영역을 계산한다. 이 처리를 하기 위해서는 주변 픽셀에 가중치를 곱하여 계산하는데, 이 때 마스크를 사용한다. 

화소 영역 처리는 마스크를 사용하는 기본적인 원리는 다 같고, 주변 화소와 원시 화소간의 가중치에 따라 효과가 다르게 나온다. 

더 나아가, 가장 기본적인 블러 효과를 조절할 수 있도록 코드를 짜보았다. 마스크의 크기가 커질수록 블러 효과는 점점 더 강해지기 때문에, 마스크의 크기를 value라 두고 값을 입력받았다. 
하지만 가변행렬을 코드로 짜기에는 어려워보였고, 단순 평균 계산이기 때문에 어렵지 않게 이중 for문 안에 또 이중 for문이 반복되는 형태로 나타낸다면 충분히 구현할 수 있을 것이라고 생각했다.





<h2>Step4: UI 개선하기</h2>
기본적인 알고리즘은 다 구현이 되었으면 UI도 중요하기 때문에 많이 수정을 하였다.

1. select option 태그 -> list와 submenu를 새로 만듦

2. 효과 중복 적용 기능 추가

3. 오류 메세지 추가

-효과를 적용하지 않았는데 적용 버튼을 눌렀을 때

-원하는 각도로 회전 효과에 0~360 사이의 값이 아닌 숫자를 입력했을 때

-첫 이미지보다 더 큰 이미지로 만드는 효과를 적용시킬 때
