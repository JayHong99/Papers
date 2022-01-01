# 2021.12.29 Batch Normalization

## Article 풀네임
- Batch Normalization : Accelerating Deep Neural Network Training by Reducing Internal Covariate Shift
- https://arxiv.org/abs/1502.03167
- 자세한 요약 : https://jayhong1999.notion.site/2021-12-29-Batch-Normalization-c58d6f0d7578410ba1015413b148122a

## 등장배경
### 기존의 문제점

- Covariance Shift
    - parameter의 작은 변화가 모델이 깊어질 수록 큰 변화를 만든다.
    - 이를 해결하기 위해서는 Distribution을 고정시킬 필요가 있다 = Normalization이 필요하다.

- Saturated Regime
    - Non-linear Activation을 거칠 대, 가중치가 업데이트 되지 않는 상황
    - 파라미터의 추정치가 극값을 갈 경우, sigmoid와 같은 값은 activation에서는 미분 계수가 0이 되어 파라미터 업데이트가 되지 않는다.
    - Covariance Shift에 더불어 약간의 차이가 파라미터 업데이트에 부정적 영향을 끼칠 수 있다.

### 기존의 해결책
- Whitening
    - 정의
        - 평균이 0, 분산이 1이 되고, 서로 비상관성있게 만든다.
    - 특징
        - 현 layer의 input은 이전 layer의 output으로, 모든 layer에 whitening을 적용하면 좋다.
        - Covariance Shift의 해결책인 fixed distribution을 가지게 도와줌
    - 문제점
        - Whitening만 적용하면, bias 파라미터의 학습을 없앰
        - 또한 bias에 대한 parameter 값의 영향도 없어져 아무렇게나 업데이트 함
        - 업데이트 할 때도 미분 계수의 수식이 복잡하고, 많은 양을 계산해야 함
- Whitening의 문제점을 보완하자 -> Batch Normalization 등장

## Batch Normalization
### Idea
- input과 output을 동시에 normalize하지 말고, scalar를 독립적으로 normalize하자.
- 전체 data에 대한 평균과 분산의 추정치는 mini-batch의 평균과 분산의 추정치를 따른다.
- 이를 이용해 학습 속도와 수렴 속도를 증진시킬 수 있다.

### Batch Norm의 문제점 및 해결책
- 문제점 및 해결책 -1
    - activation을 거친 값이 linear하게 될 수 있음
    - Normalize하면 값이 평균이 0이고 표준편차가 1이되다보니 sigmoid 함수같은 activation에서는 linear distribution을 가질 수 있음
    - 평균과 표준편차도 함께 학습해서 non-linear하게 조정
- 문제점 및 해결책 -2 
    - 모든 데이터를 normalize하는 것은 stochastic의 관점에서 비효율적
    - mini-batch 계산에서 평균과 분산의 추정량을 만들자
    - 그리고 이를 이용해서 Inference에서 사용

### Batch Norm 과정
- 모든 mini-batch마다 normalize하고, 감마와 베타에 대해서도 파라미터를 학습하고 로그를 기록해 둔다.
- 추론 단계에서는, 학습 단계에서 기록된 로그를 바탕으로, train input들의 비편향 추정으로 이동평균을 계산해 normalize 및 scale & shift를 한다.

### FC와 CNN에서 적용
- Wu+b 직후 Activation layer를 통과하기 전에 Normalize 한다.
=> 나는 이에 대해서 반대하고, notion에 정리해 두었다.
- CNN에서는 미니배치 m, 채널 n, 특성맵이 pxq이라고 하자.
    - 각 채널에 대해서 mxpxq개의 평균과 분산을 구해서 BN을 적용한다.
    - Convolution Kernel 하나는 동일 파라미터 람다와 베타를 공유한다.
    - 즉, Batch, Height, Width에 대해 평균과 분산을 구해 Channel 별로 Normalization을 함

### Batch Norm 결론
- 높은 Learning rate 설정이 가능 => 학습 속도 향상
- Deterministic한 결과 생성 -> Regularization과 Dropout이 없어도 됨
- Learning Rate Decay를 느리게 설정 가능
- Covariance Shift 해소
- Mini-batch 단위 처리로 인한 병렬 학습 기능 -> 학습 속도 향상

### Batch Norm 한계
- Batch 크기에 영향을 많이 받음
- Batch size가 작으면 잘 작동하지 않음
    - 개별 샘플이 평균이 0이고 표준 편차를 1로 만들어 버릴 수 있음
    - 이는 큰수의 법칙 or 중심 극한 이론을 만족 못 시킴
    - RNN이나 Transformer 등, 모델이 크면 계산량이 많아 많은 Batch를 사용하기 힘듦
- 반대로 Batch Size가 너무 커도 잘 작동하지 않음
    - 적절한 정규 분포를 따르게 되지만, Multi Modal 형태의 gausian mixture와 같은 형태를 띌 수 있음 (여러개의 봉우리)
    - 병렬 연산하기 어려움
- 개선 논문 : Weight Normalization Layer Normalization

### 
