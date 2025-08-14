ML을 사용한 CAN attack detection
===

### CAN-Bus Attack Detection With Deep Learning, IEEE Transactions on Intelligent Transportation Systems, 2021

#### 1. Background

CAN protocol packet을 feature vector로 NN/MLP를 사용하여 차량에 대한 공격을 탐지하는 연구

#### 2. Dataset

OBD-II log를 통해 수집된 데이터 셋. Dos, fuzzing, gear(spoofing), rpm(spoofing) 네가지 공격 유형으로 총 16M개의 CAN packet으로 이루어져 있음.

#### 3. Methodology

주어진 데이터셋의 90%를 training, 10%를 testing에 사용
입력된 CAN protocol packet을 
- (단일) 공격 or 정상 메세지인지 판별
- (다중) 정상, Dos, Fuzz, Gear, Rpm 5가지 메세지 중 어디에 해당하는지 classification

Mann-Whitney, Kolmogorov-Smirnov, Wald-Wolfowitz test로 hypothesis check.

모델로는 NN과 MLP 사용.

#### 4. Result
- (단일) MLP는 모든 상황에 대해 0.98 이상의 정확도를 보임.
- (단일) NN의 경우 높은 정확도를 보이나 MLP보다 낮음
- (다중) 1~3 layer MLP의 경우 높은 precision, recall을 보임. 하지만 layer가 많아지면 성능이 급락
