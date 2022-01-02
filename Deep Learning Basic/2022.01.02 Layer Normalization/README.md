# 2022.01.02 Layer Normalization

## 풀네임
- Layer Normalization
- https://arxiv.org/abs/1607.06450
- 자세한 요약 : https://jayhong1999.notion.site/2022-01-02-Layer-Normalization-4c2c12297f534bd1883fe79b488ea5f0

### 배경지식
- 이전 Batch Norm 배경지식과 유사
- Batch Normalization은 RNN 기반의 모델 학습에서 방법 제시가 적절하지 않음

### 방법
- 모든 summed inputs에 대해서 평균과 표준편차를 이용해 normalization 적용
    - batch-size에 영향을 받지 않고 normalization 적용 가능
- RNN 적용
    - batch norm : 
        - time-step마다 batch 단위로 계산하고 적용해야 했음
        => test sequence가 train의 최대보다 길면, 적용이 불가능한 사태 발생
    - Layer norm : 
        - 현재 layer의 input * input weight matrix + 이전 layer의 hidden state * hidden weight matrix로 구하고
        - inputs 에 대한 평균 값을 빼주고
        - 표준편차로 나누며 gain을 곱해주고, bias를 더해준다.

### Layer Norm의 불변성
- Layer Norm은 새로운 데이터가 들어오더라도, normalization이 크게 변하지 않는다.
- 기존의 다른 연구는 weight normalization을 통해서 기존과는 다른 parmeter를 가지게 하는게 목적이었다면
- 본 연구는 parameter의 변동성을 매우 작게 하는데 목적을 둔 것임

### 기하학적 관점
- Riemannian Metric & GLM 관점
    - Fisher의 정보 행렬 기반과 유사함
    - input과 parameter scale에 크게 영향을 받으나, normalized 되었기에 빠르게 찾아갈 수 있음
- 문제점
    - weight norm 의 값이 크면, gradient descent의 방향성 전환이 어려움
=> 이 지점은 좀 더 공부를 해야할 듯 싶다. 아직은 내용이 많이 어렵다.