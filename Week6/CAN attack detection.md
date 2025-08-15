ML-based CAN attack detection
===


## CAN-Bus Attack Detection With Deep Learning, IEEE Transactions on Intelligent Transportation Systems, 2021

### 1. Background

CAN protocol packet을 feature vector로 NN/MLP를 사용하여 차량에 대한 공격을 탐지하는 연구

### 2. Dataset

OBD-II log를 통해 수집된 데이터 셋. Dos, fuzzing, gear(spoofing), rpm(spoofing) 네가지 공격 유형으로 총 16M개의 CAN packet으로 이루어져 있음.

### 3. Methodology

주어진 데이터셋의 90%를 training, 10%를 testing에 사용
입력된 CAN protocol packet을 
- (단일) 공격 or 정상 메세지인지 판별
- (다중) 정상, Dos, Fuzz, Gear, Rpm 5가지 메세지 중 어디에 해당하는지 classification

Mann-Whitney, Kolmogorov-Smirnov, Wald-Wolfowitz test로 hypothesis check.

모델로는 NN과 MLP 사용.

### 4. Result
- (단일) MLP는 모든 상황에 대해 0.98 이상의 정확도를 보임.
- (단일) NN의 경우 높은 정확도를 보이나 MLP보다 낮음
- (다중) 1~3 layer MLP의 경우 높은 precision, recall을 보임. 하지만 layer가 많아지면 성능이 급락

---

## CICIoV2024: Advancing Realistic IDS Approaches Against DoS and Spoofing Attack in IoV CAN Bus

### 1. Background

### 2. Dataset

기존 car-hacking dataset의 맹점을 보완하기 위해 새롭게 dataset 구성

- DoS: 기존의 dataset은 0x000을 일정 주기로 보내는 것으로 제한. Malicious packet의 종류를 다양화(0x000 외의 신호도 보냄)하고 주기를 불규칙하게 보내도록 바꿈
- Spoofing: 정상값에서 크게 변동하는 신호 뿐 아니라, 일정하게 변화하는 형태의 malicious packet 추가.
- 정상: 정상 상황에서의 packet 종류 다양화. 일반적인 상황에서의 noise 추가.

결과적으로 Label, 8byte signal만 제공했던 기존 dataset과 비교해, Timestamp, DLC, CID를 추가적으로 제공.

기존 dataset이 laboratory condition에서 주기적인 신호만을 보냈다면, 불규칙적이고 랜덤한 signal을 통해 새롭게 검증에 더 적합한 dataset을 제시함.

### 3. Methodology
CID, CAN message, 주기 등에서 feature extraction.

(ML)Decision Tree, Random Forest, Gradient Boosting, (Network)CNN, MLP, LSTM model로 evaluate.

Evaluation에는 Accuracy, Precision, Recall, F1-score, ROC-AUC가 지표로 사용됨.

### 4. Result
- LSTM과 RF가 좋은 성능을 보임.
- 보다 Evaluation에 적합한 데이터셋을 구성했다고 할 수는 있으나, 다양한 공격 중 Dos와 Spoofing에 coverage가 제한되고, 여전히 실제 차량에서 생성된 데이터가 아니라 laboratory made dataset이라는 한계가 있음.

---

## MDHP-Net: Detecting an Emerging Time-exciting Threat in IVN

### 1. Background

기존의 CAN bus를 타겟으로 한 공격들은 단시간에 뚜렷하게 패턴이 나타나는 공격을 시행함. 연구팀은 Time-Exciting Threat라고 하는 새로운 형태의 공격을 제시하고, 이 새로운 형태의 공격을 탐지할 수 있는 형태의 모델을 제시한다.

### 2. Dataset

Time-Exciting Threat: 단일로는 영향이 없지만, 점진적으로 누적시켰을 때 차량 이상을 유발시킬 수 있는 공격의 형태.
- Gradial Spoofing: 차량의 RPM 계기를 10씩 5초 주기로 상승시키면, 수 분뒤에 실제 RPM과 큰 차이가 나며 원하지 않는 행동을 유발 시킬 수 있음.
- Intermitten Injection: 차량의 평균 속력에 +5% 정도의 속도를 주기적으로 injection하면, 평균 속력 등에 영향을 미치면서 의도되지 않은 결과를 유발시킴.

이러한 time-exciting threat를 포함한 STEIA9 Dataset를 제안해 연구에서 사용함.

### 3. Methodology

MDHP-Net(Multi-Dimensional Hawkes Process) 이라는 새로운 형태의 모델 제시
- Hawkes Process: 과거 사건에 의해 향후 사건 발생 확률이 영향을 받는 통계 모델
- Preprocessing block: temporal encoder + message encoder
- Sequence mapping block
- MDHP-LSTM block: LSTM구조에 MDHP parameter 세개를 추가한 형태
- RSA block
- MLP

### 4. Result
- MDHP-Net이 Accuracy, Precision, Recall, F1 등 모든 평가 지표에서 기존 GRU, CNN, DNN보다 우수
- 그러나, 특정 dataset에 대해 편향적으로 optimize한 것으로 추정. 해당 STEIA9 dataset이 얼마나 general한 표본인지 불명확.

---

## Multi-Stage Knowledge-Distilled VGAE and GAT for Robust Controller-Area-Network Intrusion Detection

### 1. Background

기존 detection 논문들과 유사.

### 2. Dataset

- HCRL Car-Hacking (DoS, Fuzzy, RPM, Gear)
- HCRL Survival Analysis (Flooding, Fuzzy, Spoofing)
- can-train-and-test

### 3. Methodology

- VGAE(Variational Graph Autoencoder)로 구조적 이상 탐지 + 데이터 불균형 완화
- GAT(Graph Attention Network)로 정교한 공격 분류
- Knowledge Distillation으로 가벼운 IDS(Intruder Detection System) 모델 구현.

### 4. Result

- VGAE: 구조적 이상 감지 + 데이터 선택 필터링
- GAT: 그래프 구조에서의 문맥·관계 기반 분류
- KD: 임베디드 차량 환경에서도 실시간 탐지 가능
- 다중 데이터셋에서 높은 일반화 성능 유지
- 그러나, 여전히 unbalance한 정도가 높은 dataset에서는 균일하지 못한 성능을 보임
- 추가적인 fusion method의 성능 향상 가능성이 적음
