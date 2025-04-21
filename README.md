# Titanic - EDA to Prediction (DieTanic) 필사

> 본 프로젝트는 Kaggle Titanic 대회의 대표 노트북인 [EDA to Prediction(DieTanic) by Ash316](https://www.kaggle.com/code/ash316/eda-to-prediction-dietanic)을 필사하며
> Titanic 생존자 예측을 위한 데이터 분석 및 모델링 전 과정을 학습한 기록입니다.
> 

<br>

## 개요

- Titanic 탑승객의 생존 여부를 다양한 특성을 기반으로 예측
- 데이터 분석의 기본 흐름인 **EDA → Feature Engineering → Modeling → Evaluation**을 실습 중심으로 경험
- 각 셀의 코드와 마크다운을 그대로 따라 필사하고, 의문점이나 분석 포인트는 직접 실험하며 해소

<br>

## 프로세스

### Part 1: Exploratory Data Analysis (EDA)

- 생존율 분포 및 각 피처별 통계적 경향 분석
- 주요 변수별 시각화 (`Sex`, `Age`, `Pclass`, `Embarked`, `SibSp`, `Fare`)
- 변수 간 상관관계 분석 (heatmap)

### Part 2: Feature Engineering & Data Cleaning

- 나이, 요금 범주화 (`Age_band`, `Fare_cat`)
- 가족 관련 변수 생성:
    
    `Family_Size = Parch + SibSp`
    
    `Alone = 1 if Family_Size == 0 else 0`
    
- 이름에서 `Initial` 추출 및 정규화
- 문자열 변수 인코딩 및 불필요한 피처 제거

### Part 3: Predictive Modeling

- 다양한 모델 학습 및 평가:
    - SVM (Linear & RBF), Logistic Regression, Decision Tree, KNN, Naive Bayes, Random Forest
- 교차 검증 (`cross_val_score`)을 통한 정확도 비교
- 앙상블 기법: Voting, Bagging, Boosting (AdaBoost, GradientBoosting, XGBoost)
- 하이퍼파라미터 튜닝 (`GridSearchCV`)
- Confusion Matrix 해석
- Feature Importance 시각화 및 분석

<br>

## 주요 파생 피처

| Feature | 설명 |
| --- | --- |
| `Sex` | 성별. 생존율 차이가 큰 핵심 변수 |
| `Pclass` | 객실 등급. 생존율과 음의 상관관계 존재 |
| `Age_band` | 연령 구간화. 생존율에 따른 연령별 경향 파악 가능 |
| `Fare_cat` | 요금 구간화. 고급 객실일수록 높은 생존율과 연결됨 |
| `Family_Size` | `SibSp + Parch`. **자신을 제외한 가족 수**를 표현 |
| `Alone` | `Family_Size == 0`일 때 1. 혼자인 승객 여부 |
| `Initial` | 이름에서 추출한 호칭 (Mr, Mrs, Miss 등). 나이·성별을 간접적으로 포함 |
| `Embarked` | 탑승 항구. C(셰르부르), Q(퀸즈타운), S(사우스햄튼) |

[참고] 대부분의 튜토리얼에서는 `Family_Size = SibSp + Parch + 1`로 자기 자신까지 포함시키지만,  
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;본 노트북에서는 자신을 제외한 가족 수로 정의되었기 때문에 해석에 유의해야 한다.

<br>

## 모델 평가 결과

- 가장 높은 정확도를 기록한 모델: &nbsp;앙상블 Voting Classifier
- 단일 모델 중 성능 우수: &nbsp;RandomForestClassifier
- 모든 모델에서 공통적으로 중요한 변수는 `Initial`, `Fare_cat`, `Pclass` 등이었음

<br>
<br>

## 인사이트

#### 1. `Sex`가 덜 중요하게 나타난 이유는?

- 원문에서는 "`Sex`는 대부분의 모델에서 중요하지 않다"고 분석
- 실제 확인 결과: RandomForest에서는 `Sex`가 상위 중요 피처였으나, AdaBoost, GradientBoosting, XGBoost에서는 거의 무시됨
- 그 이유는 `Initial` 피처가 성별 정보를 포함하고 있어 중복 정보 처리로 인해 `Sex`의 중요도가 상대적으로 낮아졌기 때문
  → 파생 변수와 기존 변수 간 관계에 대한 통찰 없이 결과만 보면 잘못된 해석을 할 수 있음

#### 2. Feature Importance 결과는 모델 및 버전에 따라 달라질 수 있다

- 특히 XGBoost의 경우, `importance_type`이 기본적으로 `weight` 기준이며,
  `gain`, `cover` 등의 기준을 선택하면 중요도 순위가 크게 달라질 수 있음
- 또한 `random_state`, `use_label_encoder`, `eval_metric` 설정 여부와 XGBoost의 버전 차이도 출력 결과에 중대한 영향을 미침
  → 동일한 코드를 실행하더라도 결과를 재현하려면 환경과 설정을 세심하게 통제해야 함

#### 3. 코드 구조를 해석하고 확장하는 힘은 필사를 통해 길러진다

- 단순히 코드의 출력만 보는 것이 아니라, 왜 이런 흐름으로 구성되어 있는지, 어떤 변수 간 상관관계가 작동하는지를 추적하다 보면,  
  자연스럽게 분석 설계의 흐름과 문제 해결 방식을 내 것으로 체화할 수 있다.
- 이후 다른 캐글 노트북을 필사하거나 새로운 문제에 적용할 때, 코드를 단순히 "따라치는" 것이 아니라 비판적으로 해석하고 개선하는 능력이 길러질 것이다.

    

<br>
<br>

## 필사 후기 

이 노트북은 단순한 모델링 예제가 아니라, 데이터 분석의 흐름과 목적, 해석, 결과 도출까지 모든 과정을 하나하나 따라가며 익힐 수 있는 훌륭한 실습 자료였다. 특히 결과가 기대와 다르게 나왔을 때, 직접 디버깅하고 변수 간 상관관계나 모델별 차이를 비교하는 과정은 필사의 진짜 의미를 이해하는 계기가 되었다.

"필사"를 단순한 복사-붙여넣기로 오해하기 쉽지만, 필사의 본질은 ‘왜 이 코드를 썼는가’를 파악하고, ‘내가 이 문제를 만났다면 어떤 구조로 접근했을까’를 고민하는 과정이라고 생각한다.

이 과정을 통해 단순히 코드 작성 능력뿐 아니라 문제를 구조화하고 분석 흐름을 설계하는 사고력을 기를 수 있었다. 필사를 하면서 쌓은 기반을 바탕으로, 이 코드들을 조금씩 변형해보며 더 많은 것을 실험하고 배우고 싶다.

예를 들어,
- 피처 엔지니어링 방식을 바꿔서 생존율 변화 분석하기
- 모델의 하이퍼파라미터를 탐색적으로 조정해보기
- `Initial` 없이도 `Sex`만으로 생존율을 높일 수 있는 방법이 있을지 실험해보기

앞으로도 다양한 Kaggle 노트북을 필사하고 그 코드를 변형해 보며 문제를 보는 시야와 분석 능력을 향상시킬 것이다.


<br>

## 참고 자료

- Kaggle Titanic 대회: https://www.kaggle.com/competitions/titanic
- 원본 노트북: https://www.kaggle.com/code/ash316/eda-to-prediction-dietanic

<br>
