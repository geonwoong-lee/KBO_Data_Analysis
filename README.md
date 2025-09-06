# KBO_Data_Analysis

## 1. 분석할 데이터에 대해 설명하고, 해당 데이터 분석의 목적을 서술하시오.
 2018년부터 2024년까지의 Korea Baseball Organization(KBO) 타자 기록 데이터를 기반으로 분석을 진행한다. 

 이 데이터는 kaggle에서 가져온 데이터로, 선수별 시즌 기록, 신체 조건(키, 몸무게), 타격 지표(출루율, 장타율, OPS 등)를 포함하며, 
 
 해당 데이터를 활용하여 OPS 예측을 위한 변수 영향력 분석 및 회귀 모델 비교, 타자 유형 분류 및 팀별 전략적 특성을 시각화하고, 선수 이적 전/후의 OPS 변화를 분석하고 OPS 일관성 점수를 통한 안정적인 성과를 내는 선수를 탐색하고자 하였다.

## 2. 위 데이터를 분석하기 위한 파이썬 코드를 작성하고 해당 코드 전체 동작에 대해 간략히 설명하시오.
 전체 분석은 데이터 전처리 -> OPS 예측 모델 -> 타자 유형 군집화 -> OPS 상승 여부 분류 뫃델 -> 이적 전/후 OPS 분석 -> OPS 일관성 분석의 순서로 구성된다.

 결측치를 제거하고 inch로 작성되어 있는 height 컬럼을 cm 단위로 변환하고, 다음 시즌 OPS(next_ops) 및 OPS 변화량(delta_ops) 파생 변수를 생성하였다.

 선형회귀와 RandomForestRegressor를 사용해 OPS를 예측하였고 R2 점수 및 cross validation score를 통해 예측 성능을 비교하였다.

 주요 타격 지표(HomeRun, Base on Balls, Strike Out, Stolen Base, OPS)를 기반으로 KMeans 알고리즘을 사용하고, PCA를 통해 군집을 2차원 시각화 후, 팀별 군집 비율을 시각화하였다.

 ops_up이라는 이진 변수 생성 후 RandomForestClassifier로 분류하였고, 성능 평가지표로 classification_report, confusion_matrix를 활용하였다.

 선수별 시즌별 팀 이동을 추적하여 이적 전/후 OPS 변화량을 시각화하고, 이적 후 팀별 평균 OPS 변화도 분석하였다.

 (평균/표준편차)를 기준으로 일관성 점수를 생성하였고 3시즌 이상 기록한 선수 중 상위 10명을 시각화하였다.

## 3. 파이썬 코드를 통해 실행한 결과들을 바탕으로 데이터 분석 결과를 서술하시오.
 OPS를 예측하는 모델로 LinearRegression과 RandomForestRegressor를 사용하였다. 

 예측 성능(R2)은 아래의 표와 같이 도출되었다. 선형 회귀 분석을 통해 OPS를 예측한 결과, R2 점수는 약 0.28로 나타났다. 
 
 이는 모델이 데이터의 분산 중 약 28%만을 설명할 수 있음을 의미한다. 이는 타격 지표와 OPS 간의 관계가 단순 선형이 아닌 복잡한 비선형 구조를 가진다는 것을 말한다. 
 
 이러한 한계를 보완하기 위해 비선형 모델인 RandomForestRegressor를 사용하였다.

<img width="606" height="361" alt="image" src="https://github.com/user-attachments/assets/041de4f4-b3fc-4f50-b1ed-46d9ce7e0362" />

TB(총루타), H(안타) 등이 OPS에 큰 영향을 주는 변수로 나타났다.

### 타자 유형 군집화 결과
HR, BB, SO, SB, OPS 변수로 비지도 학습을 이용하여 차원을 축소하여 시각화하였다. 

각 점은 한 명의 타자에 해당하고, 색깔은 해당 타자의 군집 소속이다.

<img width="481" height="378" alt="image" src="https://github.com/user-attachments/assets/356bb137-da5a-4265-9b5d-14b303522d4f" />

cluster 0은 pca1이 음수인 쪽에 밀집되어 있으며, 출루형 또는 기동형 타자 (대주자와 같은) 로 해석된다.

cluster 1은 중앙에 넓게 분포하며, 일반적인 타자로 해석된다. 

cluster 2는 pca1이 양수인 쪽에 퍼져있어, 장타력이 높은 장타형 타자들로 해석된다.

아래 시각화 자료는 각 팀별 타자 특성 비율이다.

<img width="817" height="249" alt="image" src="https://github.com/user-attachments/assets/0f48dffa-e0bf-4453-9d5d-a9a068d82595" />

삼성 라이온즈가 장타형 비율이 높고, 롯데 자이언츠가 출루형 선수 비율이 높았다. 

대부분의 팀들이 장타형 타자 비율을 높게 가져가는 것을 확인할 수 있었다.

### OPS 상승 여부 분류 (이진 분류)
 OPS 상승 여부를 예측하는 분류 모델은 클래스 0(상승하지 않음)에 대해 모델은 높은 재현율과 f1-score을 기록한 반면에 클래스 1(상승함) 예측에는 재현율이 0.14로 매우 낮게 나타났다. 

 이는 모델이 OPS가 상승할 선수를 정확하게 찾아내는 데 어려움이 있음을 보여준다.

 threshold를 0.3으로 낮춘 결과, 클래스 1의 재현율이 0.14에서 0.59로 향상되었다. 

 반면에 클래스 0의 정밀도나 정확도는 낮아지게 되었고, 전체 정확도도 낮아졌다. 
 
 모델이 OPS 상승할 가능성이 있는 선수를 더 많이 잡아내는데, 잘못 잡는 경우도 늘었다는 것을 의미한다. 

 이처럼 threshold를 조정하면서 성능 균형을 조절할 수 있다.

<img width="513" height="518" alt="image" src="https://github.com/user-attachments/assets/26c19d66-f44d-4324-8755-4a0bb682fcd6" />

### 이적 전/후 OPS 변화 분석
이적한 선수들에 대해서 이적 전/후의 OPS 변화량을 시각화하였다.

<img width="440" height="303" alt="image" src="https://github.com/user-attachments/assets/547f6fc9-7b33-476d-afd0-4796561e2ccc" />

이동 선수 수는 166명이고, 이적한 선수들에 대해 이적 전/후 시즌의 OPS 차이를 계산한 결과, 평균적으로 -0.0058만큼 약간 하락하였다.

이적이 성과 유지나 향상으로 반드시 이어지지 않고, 팀워크와 팀 간의 전력 구조가 큰 영향을 줄 수 있음을 알아낼 수 있다.

<img width="656" height="391" alt="image" src="https://github.com/user-attachments/assets/825e7a75-79b9-48cc-9b85-92bd3ffbc8c2" />

위 그래프는 이적 후 소속팀 기준 OPS 평균 변화량이다.

팀별로 평균 OPS 증가/감소에 차이가 있었으며, 두산 베어스나 kt 위즈 이적 시 성과가 긍정적인 반면에, LG 트윈스와 같은 일부 팀에서는 성과 하락이 나타났다. 

팀의 선수 관리 차원, 타 구장 효과 (각 팀간 구장별로 타자 친화적이거나 투수 친화적이기도 함) 등 외부 요인이 선수의 성과에 영향을 줄 수 있음을 확인할 수 있다. 

### OPS 일관성 분석
각 선수의 시즌별 OPS 평균과 표준편차를 기반으로 일관성 점수(평균 / 표준편차)를 계산하였다.

<img width="587" height="270" alt="image" src="https://github.com/user-attachments/assets/0f634e37-e565-4b70-9335-0661912bd448" />

2018~2024 기간동안 OPS 평균이 높고 표준편차가 낮은, 꾸준히 OPS를 유지한 선수 TOP 10이다. 

일관성 있는 선수는 팀 전력과 안정성과 직결된다. 이 일관성 점수는 스카우트, 스토브리그, 연봉협상에 활용 가능하다.


## 4. 위 파이썬 코드에서, 수업시간에 실습한 library code 및 내용들이 어떻게 활용되고 반영되었는지 설명하시오.
 기본적인 pandas, numpy, matplotlib, seaborn 등의 라이브러리를 활용하여 데이터를 불러오고 탐색하고 시각화하였다.

 groupby() 등을 활용하여 선수별 시즌 통계 정보를 탐색하며 초기 EDA를 수행하였다.

 LinearRegression 모델을 사용하여 OPS를 예측하는 내용을 기반으로 데이터셋을 통해 실제 선수들의 OPS를 회귀 예측하였다.

 r2_score (R2)을 통해 예측 성능을 수치화 하였다. 그 결과로 R2의 점수가 낮게 나와 단순 선형 모델의 한계도 실제로 확인할 수 있었다.

 다음 시즌 OPS 상승 여부를 이진 분류 문제로 정의하고, RandomForestClassifier를 통해 분류 모델을 학습하였다. 수업 시간에 활용한 confusion_matrix등을 활용하여 정밀도, 재현율, F1-score 등 지표를 도출하였고 threshold도 기본적으로 설정된 0.5에서 조정하여 모델의 민감도를 개선하는 기법도 사용하였다.

 모델 성능의 일반화 가능성을 검증하기 위해 corss validation score를 통해 교차검증을 수행하였고 RandomForestRegressor의 feature_importances_를 통해 변수 중요도 해석을 수행하고 이를 기반으로 OPS 예측에 가장 기여한 변수를 bar plot으로 시각화했다.

 RandomForestRegressor와 RandomForestClassifier를 사용하여 선형 회귀의 한계를 보완하는 비선형 예측 및 분류 모델을 적용하였다.

 비선형 모델이 성능 면에서 선형보다 더 좋은 결과를 만들어 낸다는 것을 확인할 수 있었다. 

 OPS 상승 여부 예측에서도 실제로 classification_report, confusion_matrix 등을 통해 예측력을 평가하였다.

 RandomForest는 Bagging 기반 Ensemble 모델로서, Ensemble 기법이 성능 향상에 기여한다는 내용을 알고, 단일 모델과 비교하여 성능 향상을 정량적으로 비교하였다.

 KMeans 알고리즘을 통해 타자들을 3가지 군집으로 분류했다. (cluster 0, 1, 2) 

 군집화에 사용된 변수는 다차원 공간에서 타자들의 특성을 반영하기 때문에 PCA를 통해 2차원 공간으로 축소한 결과 주성분1(가장 많은 정보를 설명할 수 있는 방향)과 주성분2(두 번째로 많은 정보를 설명하는 방향)를 기준으로 2차원 좌표상에 하나의 점으로 시각화하였다. 

 군집별 특성을 해석하고 이를 팀별로 어떤 유형의 타자가 많이 분포하는지도 시각화하여 팀의 성향을 비교했다.
