# 유저 Trip 분석으로 구독 전환 추천 모델

이용빈도가 잦은 편은 아니지만 단건 요금으로 구독 상품과 비슷하게 지불하는 고객에게 구독 추천하여 장기 고객으로 전환시킨다.

# 가설
- 짧은 시간을 여러번 이용하는 사람은 구독 고객과 비슷하다.
- 먼 거리를 이동하는 사람은 구독 고객과 비슷하다.
- 주말만 이용하는 사람은 일반 고객일 것이다. 

# EDA
- 구독과 출발 정류소로 되돌아오는지 유무 kendal-tau 상관분석
  - tau : -0.28, p-value : 0
- 구독과 다른 도시로 이동하는지 유무 kendal-tau 상관분석
  - tau : 0.04, p-value : 0 -> 상관 거의 없음
- 구독과 duration(이동시간) pearson 상관분석
  - 상관계수 : -0.51, p-value : 0 -> 이동시간이 작을수록 Subscriber

# 데이터 전처리
- target column imbalanced -> `SMOTENC` oversampling
- haversine package -> `distance columns` feature engineering
- right skewed columns -> log transformation

# 모델링
- oversampling 비교
  |  f1_score| original |random over |smotenc|
  |----|:---|:----|:----:|
  |logistic |0.94 |0.90   |  0.85 |  
  |lgbmC | 0.95 |0.93  |  0.92  | 
- encoder 비교
  |  f1_score |onehot |target | ordinal |
  |----|:---|:----|:----:|
  |logistic |0.943 |0.943   |   |  
  |lgbmC | 0.953 |0.953  |   | 
  |rfC | 0.936 |0.941  |  0.943  | 
  
- 튜닝 및 교차검증 후 최종 lgbmC의 f1 score : 0.9535
  
  

# 해석
- Permutation importance 결과 
<img width="232" alt="image" src="https://user-images.githubusercontent.com/94156708/199659120-fdfa18aa-766f-4763-85ac-cb165335b144.png">

- 이용시간이 짧을수록 구독자일 가능성이 높다 -> 가설 일치
- 이용거리가 길수록 구독자일 가능성이 높다 -> 가설 일치
- 평일에 이용할수록 구독자일 가능성이 높다 -> 가설 기각

# More than
- oversampling 후 f1 score 가 떨어졌는데 무슨 이유일까?
- 고객 정보와 가격 데이터가 있었다면 더 정확한 추천이 가능할 것이다. 
- 매출이 비슷하더라도 구독으로 장기고객 전환하는 게 비즈니스에 도움 될까?
