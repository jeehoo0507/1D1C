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