# DeepSeekMath-V2
https://github.com/deepseek-ai/DeepSeek-Math-V2

## Abstract
---
대규모 언어 모델은 수학적 추론에서 상당한 진전을 이루었으며, 이는 AI의 중요한 시험대 역할을 하고 더 발전할 경우 과학 연구에도 영향을 미칠 수 있다.  
###### Large language models have made significant progress in mathematical reasoning, which serves as an important testbed for AI and could impact scientific research if further advanced.

정답을 맞히면 보상을 주는 강화학습으로 추론을 확장함으로써, LLM은 1년 만에 AIME와 HMMT 같은 정량적 추론 대회에서 저조한 성능에서 포화 수준까지 향상되었다.
###### By scaling reasoning with reinforcement learning that rewards correct final answers, LLMs have improved from poor performance to saturating quantitative reasoning competitions like AIME and HMMT in one year.

그러나 이 접근법은 근본적인 한계에 직면한다.  
###### However, this approach faces fundamental limitations.

최종 정답 정확도를 높이는 것만으로는 핵심 문제를 해결하지 못한다. 즉, 정답이 맞다고 해서 추론이 옳다는 보장은 없다.  
###### Pursuing higher final answer accuracy doesn't address a key issue: correct answers don't guarantee correct reasoning.

또한 정리 증명과 같은 많은 수학 과제는 수치적 답이 아니라 엄밀한 단계별 유도를 요구하므로, 최종 정답 보상을 적용할 수 없다.  
###### Moreover, many mathematical tasks like theorem proving require rigorous step-by-step derivation rather than numerical answers, making final answer rewards inapplicable.

깊은 추론의 한계를 밀어붙이려면, 수학적 추론의 포괄성과 엄밀성을 검증하는 것이 필요하다고 우리는 믿는다.  
###### To push the limits of deep reasoning, we believe it is necessary to verify the comprehensiveness and rigor of mathematical reasoning. 

자기 검증은 테스트 시점 연산을 확장하는 데, 특히 알려진 해법이 없는 미해결 문제에서 특히 중요하다.  
###### Self-verification is particularly important for scaling test-time compute, especially for open problems without known solutions.

자기 검증 가능한 수학적 추론을 위해, 우리는 정리 증명을 위한 정확하고 충실한 LLM 기반 검증기를 어떻게 훈련할지 연구한다.  
###### Towards self-verifiable mathematical reasoning, we investigate how to train an accurate and faithful LLM-based verifier for theorem proving.

그다음 우리는 이 검증기를 보상 모델로 삼아 증명 생성기를 훈련하고, 생성기가 증명을 최종 확정하기 전에 스스로의 증명에서 가능한 한 많은 문제를 발견하고 해결하도록 유도한다.  
###### We then train a proof generator using the verifier as the reward model, and incentivize the generator to identify and resolve as many issues as possible in their own proofs before finalizing them.

생성기가 강해짐에 따라 생성-검증 격차를 유지하기 위해, 우리는 검증 연산을 확장하여 검증하기 어려운 새로운 증명에 자동으로 라벨을 붙이고, 이를 통해 검증기를 더욱 개선할 훈련 데이터를 만들 것을 제안한다.  
###### To maintain the generation-verification gap as the generator becomes stronger, we propose to scale verification compute to automatically label new hard-to-verify proofs, creating training data to further improve the verifier.

그 결과물인 DeepSeekMath-V2 모델은 강력한 정리 증명 능력을 보이며, IMO 2025와 CMO 2024에서 금메달 수준의 점수를, 그리고 테스트 시점 연산을 확장했을 때 Putnam 2024에서 120점 만점에 118점이라는 거의 완벽한 점수를 달성했다.  
###### Our resulting model, DeepSeekMath-V2, demonstrates strong theorem-proving capabilities, achieving gold-level scores on IMO 2025 and CMO 2024 and a near-perfect 118/120 on Putnam 2024 with scaled test-time compute.

아직 할 일이 많이 남아 있지만, 이러한 결과는 자기 검증 가능한 수학적 추론이 실현 가능한 연구 방향이며 더 뛰어난 수학 AI 시스템을 개발하는 데 도움이 될 수 있음을 시사한다.  
###### While much work remains, these results suggest that self-verifiable mathematical reasoning is a feasible research direction that may help develop more capable mathematical AI systems.


## Introduction
---
수학적 추론을 위한 강화학습(RL)의 전통적 접근법은, 정량적 추론 문제에 대해 대규모 언어 모델(LLM)이 예측한 최종 답이 정답과 일치하는지에 따라 보상을 주는 것이다 (Guo et al., 2025).

###### The conventional approach to reinforcement learning (RL) for mathematical reasoning involves rewarding large language models (LLMs) based on whether their predicted final answers to quantitative reasoning problems match ground-truth answers (Guo et al., 2025).

이 방법론은 주로 최종 답만 평가하는 AIME나 HMMT 같은 수학 대회를 최전선 LLM들이 포화시키기에는 충분하다.

###### This methodology suffices to allow frontier LLMs to saturate mathematical competitions that primarily evaluate final answers, such as AIME and HMMT.

그러나 이 보상 메커니즘에는 두 가지 근본적인 한계가 있다.

###### However, this reward mechanism has two fundamental limitations.

첫째, 이는 추론의 정확성에 대한 신뢰할 수 없는 대리 지표다 — 모델은 결함 있는 논리나 운 좋은 오류를 통해서도 정답에 도달할 수 있다.

###### First, it serves as an unreliable proxy for reasoning correctness – a model can arrive at the correct answer through flawed logic or fortunate errors.

둘째, 이는 정리 증명 과제에는 적용할 수 없는데, 이러한 문제들은 수치적 최종 답을 요구하지 않을 수 있고 엄밀한 유도가 주된 목표이기 때문이다.

###### Second, it is inapplicable to theorem proving tasks, where problems may not require producing numerical final answers and rigorous derivation is the primary objective.

그 결과, 이러한 최종 답 보상으로 정량적 추론 문제에 대해 훈련된 LLM은 여전히 수학적으로 무효하거나 논리적으로 일관되지 않은 자연어 증명을 자주 생성한다.

###### Consequently, LLMs trained on quantitative reasoning problems with such final answer reward still frequently produce mathematically invalid or logically inconsistent natural-language proofs.

더욱이 이 훈련 방식은 모델의 증명 타당성 검증 능력을 자연스럽게 발달시키지 못한다 — 이들은 높은 위양성률을 보이며, 명백한 논리적 결함이 있는 틀린 증명조차 유효하다고 주장하는 경우가 많다.

###### Moreover, this training approach does not naturally develop the models' ability to verify proof validity – they exhibit high false-positive rates, often claiming incorrect proofs are valid even when they contain obvious logical flaws.

자연어 정리 증명에서 생성-검증 격차의 부재는 추가적인 개선을 가로막는다.

###### The lack of a generation-verification gap in natural-language theorem proving hinders further improvement.

이를 해결하기 위해, 우리는 LLM에 증명 검증 능력을 개발하는 것을 제안한다.

###### To address this, we propose developing proof verification capabilities in LLMs.

우리의 접근법은 다음의 몇 가지 핵심 관찰에서 출발한다:

###### Our approach is motivated by several key observations:

- 인간은 참조 해답 없이도 증명에서 문제점을 식별할 수 있다 — 미해결 문제를 다룰 때 결정적으로 중요한 능력이다.

###### • Humans can identify issues in proofs even without reference solutions – a crucial ability when tackling open problems.

- 검증 노력을 확장했음에도 아무런 문제점이 발견되지 않는다면, 그 증명은 유효할 가능성이 더 높다.

###### • A proof is more likely to be valid when no issues can be identified despite scaled verification efforts.

- 유효한 문제점을 찾아내는 데 드는 노력은 증명 품질의 대리 지표로 쓰일 수 있으며, 이는 증명 생성을 최적화하는 데 활용될 수 있다.

###### • The efforts required to identify valid issues can serve as a proxy for proof quality, which can be exploited to optimize proof generation.

우리는 LLM이 참조 해답 없이도 증명의 문제점을 식별하도록 훈련될 수 있다고 믿는다.

###### We believe that LLMs can be trained to identify proof issues without reference solutions.

그러한 검증기는 다음과 같은 반복적 개선 순환을 가능하게 한다: (1) 검증 피드백을 사용해 증명 생성을 최적화하고, (2) 검증 연산을 확장해 검증하기 어려운 새 증명에 자동으로 라벨을 붙여 검증기 자체를 개선할 훈련 데이터를 만들며, (3) 이렇게 향상된 검증기로 증명 생성을 더욱 최적화한다.

###### Such a verifier would enable an iterative improvement cycle: (1) using verification feedback to optimize proof generation, (2) scaling verification compute to auto-label hard-to-verify new proofs, thereby creating the training data to improve the verifier itself, and (3) using this enhanced verifier to further optimize proof generation.

더 나아가, 신뢰할 수 있는 증명 검증기는 증명 생성기가 검증기처럼 증명을 평가하도록 가르치는 것을 가능하게 한다.

###### Moreover, a reliable proof verifier enables us to teach proof generators to evaluate proofs as the verifier does.

이를 통해 증명 생성기는 더 이상 어떤 문제점도 식별하거나 해결할 수 없을 때까지 자신의 증명을 반복적으로 정제할 수 있다.

###### This allows a proof generator to iteratively refine its proofs until it can no longer identify or resolve any issues.

본질적으로, 우리는 모델이 자신의 보상 함수를 명시적으로 인식하게 만들고, 맹목적인 시행착오가 아니라 의도적인 추론을 통해 그 보상을 최대화할 수 있게 한다.

###### In essence, we make the model explicitly aware of its reward function and enable it to maximize this reward through deliberate reasoning rather than blind trial-and-error.

DeepSeek-V3.2-Exp-Base (DeepSeek-AI, 2025)를 기반으로, 우리는 자기 검증 가능한 수학적 추론을 보여주는, 자연어 정리 증명에 최적화된 대규모 언어 모델 DeepSeekMath-V2를 개발했다.

###### Built on DeepSeek-V3.2-Exp-Base (DeepSeek-AI, 2025), we developed DeepSeekMath-V2, a large language model optimized for natural-language theorem proving that demonstrates self-verifiable mathematical reasoning.

우리 모델은 자신의 증명을 평가하고 반복적으로 개선할 수 있으며, IMO 2025와 CMO 2024를 포함한 최상위 고등학교 수학 대회에서 금메달 수준의 성능을 달성했다.

###### Our model can assess and iteratively improve its own proofs, achieving gold-level performance in premier high-school mathematics competitions including IMO 2025 and CMO 2024.

Putnam 2024 학부 대회에서는 120점 만점에 118점을 기록하여, 인간 참가자가 얻은 최고 점수인 90점을 넘어섰다.

###### On the Putnam 2024 undergraduate competition, it scored 118/120, exceeding the highest score of 90 obtained by human participants.