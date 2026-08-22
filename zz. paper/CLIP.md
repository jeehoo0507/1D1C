---
title: Learning Transferable Visual Models From Natural Language Supervision
authors: Radford et al.
year: "2021"
url: https://arxiv.org/abs/2103.00020
tags:
  - 멀티모달
  - 비지도학습
  - openai
share_link: https://share.note.sx/d9oybeba#J88w072bENFxBWMlYvHk3A
share_updated: 2026-08-20T22:58:20+09:00
---
# 요약
---

![[Pasted image 20260820141528.png]]

CLIP은 멀티모달의 기초이다.
NLP의 성공을 통해 기존 많은 이미지 모델들이 채택하던 군중 데이터셋 대신 인터넷에 방대한 4억 개 데이터셋을 학습에 사용하며 이미지 인코더, 텍스트 인코더의 결과 벡터의 코사인 유사도를 높이는 것을 통해 이미지-텍스트 짝의 관계성을 학습시킨다. 단순히 유사도를 올리는 방식이 아닌 여러 데이터들의 인코더 출력 벡터를 쌓고 이를 행렬 처럼 각 인덱스들의 코사인 유사도를 구하면서 대각선의 softmax 결과값을 높이도록 학습한다.
CLIP에서의 배치는 이처럼 다른 이미지 간의 차이 또한 학습하여 성능을 높인다는 것이 핵심이다.

**장점**

CLIP 새로운 이미지에 대해서도 zero-shot이 가능하며 few-shot 또한 성능이 상당히 높다.

**단점**

- 세부 묘사에 약하다. (결국 데이터셋에 자주 등장하는 이미지를 명확히 분류한다.)
- 도메인 쉬프트에 약하다. (인터넷의 사진에서 위성 사진 의료 사진은 적다보니 전문 도메인에 약하다.)
- 텍스트 인코더 병목 (이미지 데이셋이 아무리 좋아도 텍스트 인코더가 transformer/BERT 수준이다 보니 복잡한 추론 단계를 가지기 힘들다.)

# Abstract
----
기존 최고 수준의 CV 모델들은 미리 라벨링된 고정된 클래스만 예측하도록 훈련되어 새로운 개념을 인식하려면 추가 라벨 데이터가 필요하다는 범용성의 한계를 가진다. 
CLIP은 자연어를 지도 신호로 활용하는 대조 학습(Contrastive Learning)을 통해 4억 개의 (이미지, 텍스트) 쌍을 처음부터 학습하는 것이 효율적이고 확장 가능함을 입증한다. 

그 결과 ImageNet의 128만 개 훈련 예제를 전혀 사용하지 않고도 원래 ResNet-50의 정확도를 제로샷(Zero-shot)으로 일치시키는 데 성공했다. 

코드 및 모델 : https://github.com/OpenAI/CLIP

# Introduction
---
CLIP 이전 NLP 분야는 GPT-3와 같은 원시 텍스트 기반 사전 학습 덕분에 데이터셋별 조정 없이도 범용적이고 경쟁력 있는 성능을 보여주며 혁명을 이루었다. 

그러나 컴퓨터 비전은 여전히 ImageNet과 같은 군중 라벨링 데이터에 의존하고 있다. 
과거에도 이미지-텍스트 쌍을 활용하려는 시도가 있었으나 10만~20만 장의 소규모 데이터와 고정된 분류기(Static Classifier)의 한계로 인해 실용적인 성능(ImageNet 11.5%)을 내지 못했다.
이번 연구는 4억 개의 대규모 데이터에서 자연어 감독(supervision)을 통해 학습한 CLIP이 이러한 한계를 뛰어넘는 잠재력을 입증한다.

# Method 
---
#### Natural Language Supervision

CLIP은 자연어를 이미지 학습의 **supervision** 으로 학습하는 것이다.
자연어를 이용한 학습은 기존 숫자를 학습의 **supervision** 으로 삼았을 때 대비 좋은 이점이 존재한다.

1. 인터넷에는 수많은 이미지와 이에 대응하는 캡션이 있기 때문에 scale이 가능하다.
2. 자연어와 이미지와의 관계를 학습하면서 이미지를 더 깊게 이해 가능하다. 
3. 기존 비전 모델들이 잘 하지 못한 zero-shot-prediction 이 가능해진다.

zero-shot transfer : 학습 과정 중 본 적 없는 데이터의 class도 예측을 할 수 있는 능력

#### Dataset

기존 연구들은 주로 세 가지 데이터셋을 사용한다.

| Dataset       | 설명                                                                |
| ------------- | ----------------------------------------------------------------- |
| MS-COCO       | 높은 품질의 crowd-labeled dataset이지만 10만개 정도의 이미지                      |
| Visual Genome | 높은 품질의 crowd-labeled dataset이지만 10만개 정도의 이미지                      |
| YFCC100M      | 다양한 품질의 1억장의 이미지 → (쓸만 한 것을) 필터링하면 600 ~ 1500만개 정도 (ImageNet과 비슷) |
다른 Computer Vision system은 최대 35억 개의 인스타그램 사진[(paper)](https://arxiv.org/abs/1805.00932)에 대해 훈련되는 것을 고려하였을때 위의 데이터셋들은 그 크기가 너무 작다.


![[Pasted image 20260820171948.png]]

**Natural language supervision** 의 장점은 인터넷에 올라온 수많은 데이터의 양으로부터 온다.
이 논문에서는 기존 데이터셋 대신 인터넷에서 수집한 4억 개의 (image, text) 쌍으로 구성된
새로운 데이터셋인 **WIT**(WebImageText) 를 제시한다.



#### Pre-training

최고 수준의 컴퓨터 비전 모델은 매우 큰 연산량을 필요로 한다.
따라서 CLIP은 상당히 많은 데이터를 학습하기 때문에 학습 효율성이 중요하다.

CLIP의 초기 접근 방식은 VirTex와 비슷하다.
~~~
이미지에 적힌 캡션을 한 글자도 틀리지 않고 예측하는 것
~~~

![[Pasted image 20260820174903.png|438]]

하지만 이 방식에서 사용된 트랜스포머가 더 간단한 방식보다 3배 정도 느렸다.

그래서 CLIP에서는 Contrastive Learning을 채택하였다.
이는 자가지도 학습(self-supervised learning) 분야에서 많이 활용되는 방식이다.
매칭된 feature들끼리의 코사인 유사도를 커지게 학습하는 것이다.
아래 그림의 색칠된 대각선 값들을 크게 한다고 보면 된다.


![[Pasted image 20260820171938.png]]



CLIP의 image encoder 모델로는 5가지 종류의 **ResNet**과 3가지 종류의 **ViT**를 사용하여 비교했다. Text encoder 모델로는 **Transformer**를 사용했다.

모델 크기를 키우기 위해서 image encoder는 width, depth, resolution을 모두 동일한 정도로 scale 했으며 text encoder는 width만 scale 했다.



# Experiments
---
zero-shot transfer





# Results
---

# Reproduction
---
