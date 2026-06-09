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
* **ROC-AUC:** 0.9824
* **PR-AUC (Average Precision):** 0.7645
* **EER (Equal Error Rate):** 0.0412
* **결과 해석:** 극심한 불균형 데이터 보완을 위해 `class_weight='balanced'` 패널티 가중치 옵션을 부여하여 선형 모델 구축.  
ROC-AUC가 0.98을 상회하여 전반적인 탐지 잠재력이 우수함을 입증했으며, 각 변수의 계수(Coefficient) 분석을 통해 사기 결제에 직접적인 가중치를 부여하는
금융 핵심 피처를 투명하게 해석할 수 있는 기준점(Baseline)을 제시함. 다만, 불균형 데이터의 실제 정밀도를 대변하는 PR-AUC 측면에서는 보완의 여지가 확인됨.

#### 2. Stage 2: COPOD (Copula-Based Outlier Detection)
* **ROC-AUC:** 0.9411
* **PR-AUC (Average Precision):** 0.8122
* **EER (Equal Error Rate):** 0.0687
* **결과 해석:**
    다변량 데이터의 꼬리 분포(Tail Dependence)와 연관 구조를 포착하는 Copula 확률 분석 기반의 비지도 학습 이상치 탐지 기법.  
전체적인 탐지 대역폭(ROC-AUC)은 Logistic Regression에 비해 소폭 낮으나, 사기 데이터 자체의 밀도를 예리하게 타겟팅하여 **PR-AUC가 0.8122로 크게 향상**됨.
이는 실제 탐지된 이상치 중 진짜 사기 거래가 포함되어 있을 확률(정밀도)이 선형 모델보다 향상되었음을 의미하며, 복잡한 사기 패턴을 정교하게 제어할 수 있는 가능성을 증명.

#### 3. 종합 결론
해석 가능성이 중요한 기초 심사 룰 세팅에는 **Logistic Regression**이 뛰어난 통제력을 보이지만, 불균형 노이즈가 심하고 복잡한 다변량 이상치 탐지 환경에서는 
**COPOD** 알고리즘이 탐지 정밀도 면에서 훌륭한 대안이 될 수 있음을 확인함.   
실무 FDS 구축 시 두 모델을 앙상블하거나 소프트 보팅 형태로 결합한다면, 
오탐률은 낮추고 미탐률을 방어하는 상호 보완적인 고도화 시스템 구현이 가능할 것으로 기대됨.

---

### 파일 설명
- `Logistic Regression.ipynb`: EDA, 극단적 이상치 정제, Train/Test 데이터 분할, 스케일링 파이프라인, 그리고 로지스틱 회귀 Baseline 모델 학습과 성능 지표 도출 및 다각도 커브 시각화를 수행하는 메인 분석 코드 파일
- `COPOD.ipynb`: 다변량 변수 결합 구조를 반영하여 Copula 기반 이상치 탐지 알고리즘을 구현하고, 어블레이션 연구(Ablation Study)를 통해 변수 조합별 금융 FDS 평가지표를 연계 검증한 고급 모델링 파일
