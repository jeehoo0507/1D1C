### 1. 개요 및 문제점

기존 농작물 상태 파악 방식은 범용 모델을 사용하거나 개인 환경에 최적화된 별도의 학습 과정을 거쳐야 한다. 하지만 이러한 방식은 데이터 라벨링과 학습에 많은 비용이 소모되어 실무 적용에 불편함이 크다. 본 프로젝트는 이러한 과정을 간소화하고, 누구나 쉽게 접근할 수 있는 농작물 탐색 알고리즘을 구축하는 것을 목표로 한다.

![[자료 사진.png|664]]

---
### 2. 이미지 세그먼트 전략

이미지 세그먼트 분야에서는 Meta의 SAM(Segment Anything Model)이 높은 정확도를 보이지만, 밭과 같이 일관된 형태를 가진 환경에서는 불필요하게 무겁다. 따라서 본 시스템은 단순하면서도 효율적인 슬라이딩 윈도우(Sliding Window) 방식을 채택하여 최저 오차 지점을 탐색한다.

#### 사용 모델: ResNet-18 (Pre-trained)

전 세계적으로 검증된 ResNet-18 모델을 특징 추출기(Feature Extractor)로 사용한다.

- 특징 추출: 농작물 특유의 패턴을 512차원의 고유한 벡터(Vector)로 변환하여 인식의 정확도를 높인다.


#### 작동 방식: 멀티 스케일 슬라이딩 윈도우

사람이 돋보기를 들고 밭을 검사하는 과정을 자동화한 구조이다.

1. 기준 벡터 생성: 샘플 사진에서 AI가 고유한 특징(Feature)을 추출하여 저장한다.
2. 다중 크기 스캔 (Multi-scale): 전체 사진 위에서 슬라이딩 방식으로 크롭 사이즈별 유사 영역을 탐색한다. 밭이라는 특수한 환경에서는 SAM이나 DINO-CNN 같은 무거운 모델보다 이 방식이 효율적이다.
3. 정밀 비교: 추출된 모든 조각을 모델에 입력하여 유사도 점수를 매긴다. 단순 밝기가 아닌 형태적 유사성을 판단하므로 흰색 비닐 등 배경 노이즈 제거에 탁월하다.
4. 중복 제거 (NMS): 한 객체 위에 중복된 박스가 생성될 경우, 점수가 가장 높은 박스만 남기고 정리한다.

$$\text{IoU}(M, B_i) = \frac{\text{area}(M \cap B_i)}{\text{area}(M \cup B_i)}$$
$$s_i = \begin{cases} s_i, & \text{if } \text{IoU}(M, B_i) < N_t \\ 0, & \text{if } \text{IoU}(M, B_i) \ge N_t \end{cases}$$


가장 신뢰도가 높은 box를 채택한다

![[MLDL/1-img/Figure_1.png]]



---

### 3. DINO 기반 농작물 분류 및 차원 증폭

여러 농작물이 섞여 있는 환경에서 각 작물을 정확히 분류하기 위해 DINO(Self-distillation with no labels) 기반의 학습법을 적용한다.

![[Figure_2.png]]




#### DINO의 핵심 메커니즘

DINO는 스튜던트(Student)와 티쳐(Teacher) 네트워크 간의 증류(Distillation)를 기반으로 한다.

- 확률 분포 산출: $P(x)^{(i)} = \frac{\exp(z^{(i)} / \tau)}{\sum_{j=1}^K \exp(z^{(j)} / \tau)}$
- 손실 함수: $\min_{\theta_s} H(P_t(x), P_s(x)) = - \sum_{i=1}^K P_t(x)^{(i)} \log P_s(x)^{(i)}$
- 파라미터 업데이트(EMA): $\theta_t \leftarrow \lambda \theta_t + (1 - \lambda) \theta_s$


---

"Second, these features are also excellent k-NN classifiers, reaching 78.3% top-1 on ImageNet with a small ViT."

"Self-supervised ViT features perform particularly well with a basic nearest neighbors classifier (k-NN) without any finetuning, linear classifier nor data augmentation, achieving 78.3% top-1 accuracy on ImageNet."

-DINO v1 논문 인용-

---


### 4. 시스템의 강점

- 정확성: 사전 학습된 AI의 판단력을 활용하여 인식 오류를 최소화한다.
- 유연성: 기준 사진만 교체하면 양배추, 무, 감자 등 다양한 작물에 즉시 적용이 가능하다.
- 신뢰도: 배경과 작물을 형태적으로 분석하여 명확히 구분한다.
- 효율성: 동일 해상도 기준에서 테스트 시간이 일정하며 오차율이 적다.

---

![[Pasted image 20260306020013.png]]

이 과정을 통해 세그먼트된 사진들의 정교한 벡터값을 얻을 수 있다. 비슷한 사진 집단 내에서 변별력을 높이기 위해, 트랜스포머의 피드 포워드(Feed Forward) 구조를 활용하여 차원을 증폭시킨 후 k-NN(k-Nearest Neighbors) 분류를 진행한다.

$$\text{FFN}(x) = \max(0, xW_1 + b_1)W_2 + b_2$$

---
### 5. 농작물 분류 과정

1.  나이브 세그먼트 알고리즘을 통해 농작물 사진을 세그먼트 한다. (색깔이 비슷한 잡초 등 노이즈 데이터가 같이 세그먼트 되기도 함)

2. 사진 중 기준이 되는 사진을 선택하고 그것을 기준으로 dino 벡터 분포에 집어넣는다
3. 들어온 새 이미지와 기존의 이미지의 가중치를 두고(이건 아마도 실험적으로)
디퓨전 모델처럼 노이즈를 사용하여 벡터 생성 분포의 하한값 즉 허용 범위를 찾는다
4. 허용 범위 기준 벡터를 FFN 신경망(?)을 통해 차원 증폭을 진행한다
5. k-nn을 사용하여 이미지를 묶는다.

이 연구는
기본적으로 학습하지 않은 모델에 대한 정확성과 높지 않는 성능의 경량화를 통해 높은 성능을 유도한다.