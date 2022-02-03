# 2022.02.04 Residual Connection

## 풀네임
- Deep Residual Learning for Image Recognition
- https://arxiv.org/pdf/1512.03385.pdf


## 요약
- DNN은 학습하기 어려움 -> Residual Connection을 사용하자.
- 깊어져도 accuracy를 얻기 쉬워졌음
- ImagNet Data -> VGG보다 8배 깊음에도 3.57% erorr를 냄

## 서론
- DNN은 주로 low/middle/high level의 특징들을 잡아냄
    - 여기서 level은 depth에 의해서 싸임
    - 따라서 "매우 깊은" 모델을 만드는게 도전 과제가 되었음

### 의문점  "깊게 쌓으면 좋은 모델일까?"

문제 1 ) vanishing / exploding gradient 문제 발생
- 의미 ) gradient가 사라지거나 explode되는 현상
- 효과 ) 시작부터 loss의 수렴을 방해함
- 대책 ) 이를 Normaliation과 Intermediate normalization을 이용해서 억제함
- 결과 ) 이로써 수십개의 layer도 SGD를 이용해서 학습할 수 있게 됨
- Reference ) Batch Normalization , etc...

문제 2 ) Degradation 문제 발생
- 의미 ) 모델이 깊어질수록 accuracy의 진전이 없으며, 추후 정확도가 낮아지는 현상 
- 주의 ) overfitting이면 training acc는 증가해야 하나, degradation에서는 training acc도 감소하므로 서로 다른 의미
- 효과 ) 모델을 깊이 쌓아도 의미가 없다고 생각하게 됨

해결법 2-1 ) 
- 대책 ) identity mapping을 이용한 layer와 shallower model로 구성
- 결과 ) 더 깊은 모델을 만들 수 있게 됨

해결법 2-2 )
- 대책 ) Shortcut Connection 구성
- 방법 ) 
![Residual Connection](https://production-media.paperswithcode.com/methods/resnet-e1548261477164.png)
- 이런 방법으로 residual mapping을 적용하자.
- 결과 ) 얻어갈게 없으면 원본 데이터를 가져올 수 있고, 잔차를 0으로 만들어 학습할 수 있다.

Shoftcut Connection의 특징
- 추가적인 파라미터 필요 X
- 추가적인 계산 복잡성 필요 X
- end-to-end로 학습 가능
- Image Net에서는 깊어질수록 효과가 좋아짐

## 구조
### Residual Learning
변수
- x = input
- H(x) : 소규모 다층 layer 의 output
- id(x) : Identity mapping으로, x를 반환함

학습법
- 기존 : F(x) := H(x)가 되도록 학습
- 변경 : F(x) := H(x) - x 가 되도록 학습
    - 그러면 H(x)가 F(x) + x와 유사해 짐

효과
- degradtion problem이 identity mapping의 어려움에서 비롯됨
- 이 과정으로 identity mapping을 선택적으로 수행할 수 있게 됨
- 즉, 소규모 다층의 비선형 변환을 한 weight가 0이 될 수 있다.
- 실제로 0이 되는 경우는 거의 없지만, 사전 차단에 효과가 있을 것이다.

### 모델 구조

Residual Network
- 차원에 따라서 2가지 Option이 존재
- 1) 그대로 연결
- 2) 1x1 fiber를 이용해서 차원수를 조정 후 연결



