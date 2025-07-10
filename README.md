# Black Friday 데이터 분석 프로젝트

## 📌 프로젝트 개요
이 프로젝트는 [Analytics Vidhya: Black Friday 해커톤](https://datahack.analyticsvidhya.com/contest/black-friday/) 데이터를 활용하여  
고객의 구매 행동을 분석하고, 유의미한 피처를 도출하여 구매 금액을 예측하는 것이 목표입니다.

EDA부터 Feature Engineering, 다양한 모델 학습 및 앙상블까지의 과정을 수행했습니다.

---

## 🧭 분석 흐름 요약

1. **문제 정의**  
   - 예측 목표: 고객의 `Purchase` (구매 금액)  
   - 회귀(Regression) 문제로 접근

2. **데이터 이해 및 전처리**  
   - 변수 유형, 결측치, 분포 확인
   - 결측치 대체 및 파생 결측 변수 생성
   - 범주형 변수 인코딩 (Label Encoding, Ordinal Encoding 등)

3. **Feature Engineering**  
   - 다양한 유저/제품 기반 파생 변수 도출
   - 구매 패턴, 소비 성향, 카테고리 조합 등 분석 반영

---

## 🧩 주요 Feature Engineering 목록

| Feature 명 | 설명 |
|-----------|------|
| `Gender` | 성별 (F=0, M=1) |
| `Age` | 연령대 (Ordinal 형태로 인코딩) |
| `City_Category` | 도시 유형 (A/B/C → 숫자 라벨 인코딩) |
| `Stay_In_Current_City_Years` | 현재 도시 거주 연수 (텍스트 → 숫자 변환) |
| `Product_Category_1~3` | 제품 카테고리 변수 |
| `Product_Category_2_NA`, `Product_Category_3_NA` | 결측 여부를 나타내는 바이너리 변수 |
| `Total_Products` | 해당 구매에서 선택된 제품 카테고리 개수 |
| `Product_Popularity` | 제품 판매 빈도를 반영한 인기 점수 |
| `User_Total_Purchase`, `User_Avg_Purchase`, `User_Num_Purchases` | 유저별 총 구매액, 평균 단가, 구매 횟수 |
| `Product_Total_Sales`, `Product_Avg_Purchase`, `Product_Num_Sales` | 제품별 총 매출, 단가, 판매 횟수 |
| `User_Spend_Score`, `SpendScore_bin` | 유저 소비 점수 및 분위 그룹 |
| `Product_Spend_Score`, `ProductScore_bin` | 제품 매출 기여도 점수 및 분위 그룹 |
| `User_Product_Interaction`, `InteractionGroup` | 유저-제품 상호작용 스코어 및 분위 그룹 |
| `Category_Combo`, `Multi_Category` | 제품 카테고리 조합 및 복합 구매 여부 |

---

## 📊 주요 분석 결과

### 1. 성별·연령대별 제품 선호도 분석
- 연령대 및 성별에 따라 가장 많이 구매한 제품 카테고리가 다름
  - 남성(0~55세): 카테고리 **5번** 선호
  - 여성(0~55세): 카테고리 **1번** 선호
  - 고연령층(55+) 남녀 공통: 카테고리 **8번** 선호

### 2. 제품 카테고리 조합 분석
- 대부분의 구매는 **단일 카테고리(예: 5-0-0)** 형태
- **복합 카테고리(3개 다 포함)** 구매는 적지만 평균 구매액이 더 높음  
  (단일: 약 8,200원 / 복합: 약 11,600원)

### 3. 유저별 소비 성향 분석
- `User_Spend_Score`를 바탕으로 5분위로 구분
  - Q1: 평균 단가 높지만 구매 횟수 적은 유저
  - Q4: 자주 사지만 단가는 낮은 유저

### 4. 제품별 매출 기여도 분석
- 대부분의 제품은 매출 기여도 낮음 (롱테일 구조)
- 상위 분위 그룹(Q4)은 인기도, 단가 모두 높은 고기여 제품

### 5. 유저-제품 상호작용 분석
- `User_Product_Interaction` 도입
- 상호작용이 높을수록 평균 구매액 증가
  - Group 4: 평균 구매액 약 16,000원 → 고가 상품을 자주 구매하는 VIP 유저

### 6. 재구매 분석
- **카테고리 5, 1, 8번** 제품에서 재구매율 높음
- 도시별로는 **B도시** 유저가 가장 높은 재구매율을 보임  
  (일관되게 B > C > A 순서)

---

## 🤖 모델링

### 1. 기본 모델 비교 (회귀)
| 모델 | RMSE | MAE |
|------|------|-----|
| Linear Regression | 2510.80 | 1847.96 |
| RandomForestRegressor | 2473.34 | 1815.20 |
| XGBRegressor | **2432.14** | 1783.62 |

→ XGBoost가 가장 좋은 성능을 보임

### 2. XGBoost 하이퍼파라미터 튜닝
- RandomizedSearchCV + 3-Fold 교차검증
- 최적 파라미터:  
  `max_depth=9`, `n_estimators=343`, `learning_rate=0.0499`, `subsample=0.83` 등  
- 튜닝 후 RMSE: **2383.05**

### 3. 앙상블
- Linear, RF, XGB 예측값 평균  
- 앙상블 RMSE: 2428.24 → **XGBoost 단독이 더 우수**

✅ **최종 모델**: 튜닝된 XGBoost 모델  
✅ **대회 제출 점수**: **2489.97 (RMSE 기준)**

---

## 📁 주요 파일

| 파일명 | 설명 |
|--------|------|
| `black_friday_EDA_final.ipynb` | 전체 분석 + 모델링 포함한 Jupyter Notebook |

---

## 🧷 데이터 출처
- [Analytics Vidhya: Black Friday Hackathon](https://datahack.analyticsvidhya.com/contest/black-friday/)

---

## ⚙️ 사용 라이브러리
- Python 3.x
- pandas, numpy, seaborn, matplotlib
- scikit-learn, xgboost

---

## 🙋‍♀️ 작성자
- 정희진 (GitHub: [heejintv](https://github.com/heejintv))

---

> 📌 본 프로젝트는 데이터사이언스 과목의 기말 과제로 수행되었으며,
> 소비자 행동 기반 회귀 문제 해결을 목표로 다양한 분석 기법을 적용하여 실제 비즈니스 인사이트와 회귀 모델링 역량을 실습하였습니다.

