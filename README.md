# Credit_Card_FDS: 신용카드 거래 데이터를 통한 사기 탐지

---
### 프로젝트 배경:
금융 산업의 디지털화와 신용카드 결제 규모의 급증에 따라, 고도화된 부정 결제 및 사기 거래(Fraud) 패턴을 실시간으로 감지하는 사기 탐지 시스템(FDS, Fraud Detection System)
의 중요성이 나날이 커지고 있다. 금융 정형 데이터는 정상 거래가 압도적으로 많고 사기 거래는 극소수에 불과한 극심한 '클래스 불균형(Imbalanced Data)' 특성을 가진다. 이러한 
환경에서는 단순 기계적인 분류 모델 학습만으로는 실무에서 사용 가능한 수준의 안정적인 사기 탐지 성능을 확보하기 어렵다.

따라서 본 프로젝트에서는 실제 카드 거래 데이터를 기반으로 철저한 EDA를 수행하여 노이즈를 클렌징하고, 해석 가능성이 높은 선형 분류기 모델과 
다변량 확률 분포 기반의 고급 이상치 탐지(Anomaly Detection) 알고리즘을 결합 및 상호 비교하여 금융 데이터 환경에 최적화된 FDS Baseline 파이프라인을 구축하고자 한다.

### 프로젝트 목표:
- 신용카드 거래 데이터 기반의 FDS 전처리 및 분석 파이프라인 수립
- EDA를 통한 비현실적 데이터 분포 탐색 및 극단적 이상치(Outlier) 정제
- 정상 대비 사기 비율이 1% 미만인 금융 도메인 특성을 반영한 불균형 데이터 처리 학습
- 해석력이 뛰어난 Logistic Regression 기반의 분류 Baseline 모델 구축
- Copula 기반의 고급 비지도 이상치 탐지 알고리즘(COPOD) 확장 및 상호 성능 비교
- 금융 FDS의 핵심 지표인 ROC-AUC, PR-AUC 및 EER(Equal Error Rate)의 정량적 평가 및 결과 도출

### 사용 데이터:
사용 데이터: https://www.kaggle.com/datasets/elakiyasekar/card-transdata (샘플링을 통해 사기 비율을 1% 미만으로 통제)
- 최종 분석 데이터셋 규모: 총 50,400개 (정상 거래 50,000개 : 사기 거래 400개 / 사기 거래 비율 약 0.79%)

### 사용 기술 및 모델
- Python
- Pandas, NumPy (데이터 프레임 워크플로우 및 데이터 전처리)
- Matplotlib, Seaborn (EDA 분포 탐색 및 평가 커브 시각화)
- Scikit-learn (Standard Scaling, 데이터 분할 및 Logistic Regression 모델링)
- PyOD (Copula-Based Outlier Detection - COPOD 모델링)

---
### 모델별 성능 평가 및 결과 해석 (Evaluation & Interpretation)

금융 FDS 시스템에서는 정상 거래를 사기로 오인하는 비용보다, 실제 사기 거래를 놓쳐 발생하는 금융 손실 비용(FRR, False Rejection Rate 관리)이 훨씬 
치명적이기 때문에 탐지율의 척도인 `ROC-AUC`, 불균형 데이터에 특화된 `PR-AUC`, 그리고 오탐률과 미탐률이 균형을 이루는 최적의 임계값 지표인 `EER`을 기준으로 분석

#### 1. Stage 1: Logistic Regression (Interpretation Baseline)
* **ROC-AUC:** 0.9780
* **PR-AUC (Average Precision):** 0.1970
* **EER (Equal Error Rate):** 0.0706
* **결과 해석:** 극심한 클래스 불균형 환경을 고려하여 class_weight='balanced' 옵션을 적용한 Logistic Regression 기반 지도학습 모델을 구축하였다. ROC-AUC 0.9780을 기록하여 정상 거래와 사기 거래를 효과적으로 구분하는 우수한 분류 성능을 확인했으며, PR-AUC 0.1970과 EER 0.0706을 통해 불균형 데이터에서도 안정적인 탐지 성능을 보였다. 또한 각 변수의 계수(Coefficient)를 분석하여 사기 거래 탐지에 영향을 미치는 주요 금융 변수들을 해석할 수 있는 기준 모델(Baseline)로 활용하였다.

#### 2. Stage 2: COPOD (Copula-Based Outlier Detection)
* **ROC-AUC:** 0.8413
* **PR-AUC (Average Precision):** 0.0333
* **EER (Equal Error Rate):** 0.2564
* **결과 해석:** COPOD(Copula-Based Outlier Detection)는 다변량 데이터의 꼬리 분포(Tail Dependence)와 변수 간 연관 구조를 활용하는 비지도 이상탐지 기법이다. 라벨 정보 없이 이상거래를 탐지할 수 있다는 장점을 확인하였으나, 본 실험에서는 ROC-AUC와 PR-AUC 모두 Logistic Regression보다 낮은 성능을 보였다. 이를 통해 충분한 라벨 데이터가 확보된 환경에서는 지도학습 기반 모델이 탐지 성능 측면에서 우수하며, COPOD는 라벨이 부족하거나 신규 이상 패턴 탐색이 필요한 상황에서 보조적인 이상탐지 모델로 활용 가능함을 확인하였다.

#### 3. 종합 결론
본 프로젝트에서는 지도학습(Logistic Regression)과 비지도학습(COPOD) 기반 이상거래 탐지 모델을 동일한 금융 데이터셋에서 비교 평가하였다. 실험 결과, Logistic Regression은 ROC-AUC 0.9780과 EER 0.0706을 기록하며 전반적으로 우수한 탐지 성능을 보였고, COPOD는 라벨 없이 이상거래를 탐지할 수 있는 비지도학습 기법의 특성을 확인할 수 있었다.

이를 통해 충분한 라벨 데이터가 존재하는 환경에서는 지도학습 모델이 높은 탐지 성능을 제공하며, 비지도학습 모델은 신규 이상 패턴 탐색이나 라벨이 부족한 환경에서 보완적인 역할을 수행할 수 있음을 확인하였다. 향후 실제 FDS 환경에서는 지도학습 모델과 비지도 이상탐지 모델을 함께 활용하여 알려진 사기 패턴과 미지의 이상 패턴을 동시에 탐지하는 하이브리드 탐지 시스템으로 확장할 수 있을 것으로 기대된다.

---

### 파일 설명
- `Logistic Regression.ipynb`: EDA, 극단적 이상치 정제, Train/Test 데이터 분할, 스케일링 파이프라인, 그리고 로지스틱 회귀 Baseline 모델 학습과 성능 지표 도출 및 다각도 커브 시각화를 수행하는 메인 분석 코드 파일
- `COPOD.ipynb`: 다변량 변수 결합 구조를 반영하여 Copula 기반 이상치 탐지 알고리즘을 구현하고, 어블레이션 연구(Ablation Study)를 통해 변수 조합별 금융 FDS 평가지표를 연계 검증한 고급 모델링 파일
