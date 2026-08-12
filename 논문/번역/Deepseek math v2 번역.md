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




## Method

### 2.1. 증명 검증 (Proof Verification)

#### 2.1.1. 문제점을 식별하고 증명을 채점하는 검증기 훈련 (Training a Verifier to Identify Issues and Score Proofs)

우리는 수학 전문가의 평가 과정을 본떠, 검증기가 이 루브릭에 따라 증명을 평가하도록 훈련하는 것을 목표로 증명 평가용 고수준 루브릭 I_v를 개발했다 (부록 A.2 참조).

###### We developed high-level rubrics I𝑣 for proof evaluation (see Appendix A.2) with the goal of training a verifier to evaluate proofs according to these rubrics, mirroring mathematical experts' assessment process.

구체적으로, 문제 X와 증명 Y가 주어졌을 때 검증기 π_φ(·|X, Y, I_v)는 먼저 식별된 문제점(있다면)을 요약하고 이어서 세 단계 중 하나로 점수를 부여하는 증명 분석을 생성하도록 설계되었다: 모든 논리 단계가 명확히 정당화된 완전하고 엄밀한 증명은 1점, 전체 논리는 타당하나 사소한 오류나 세부 생략이 있는 증명은 0.5점, 치명적 논리 오류나 결정적 공백을 포함한 근본적으로 결함 있는 증명은 0점이다.

###### Specifically, given a problem 𝑋 and a proof 𝑌, the verifier 𝜋𝜑(·|𝑋, 𝑌, I𝑣) is designed to produce a proof analysis that first summarizes identified issues (if any) and then assigns a score based on three levels: 1 for complete and rigorous proofs with all logical steps clearly justified; 0.5 for proofs with sound overall logic but minor errors or omitted details; and 0 for fundamentally flawed proofs containing fatal logical errors or critical gaps.

---

**콜드 스타트 RL 데이터 구축 (Curating Cold Start RL Data)**

우리는 다음 과정을 통해 초기 훈련 데이터를 구성했다:

###### We constructed our initial training data through the following process:

1. 우리는 Art of Problem Solving(AoPS) 대회 문제들을 크롤링했으며, 수학 올림피아드·대표 선발전·증명을 명시적으로 요구하는 2010년 이후 문제를 우선시하여 총 17,503개 문제를 수집했다. 이 문제 집합을 D_p로 표기한다.

###### 1. We crawled problems from Art of Problem Solving (AoPS) contests, prioritizing math olympiads, team selection tests, and post-2010 problems explicitly requiring proofs, totaling 17,503 problems. This problem set is denoted as D𝑝.

2. 우리는 DeepSeek-V3.2-Exp-Thinking의 변형 모델을 사용해 후보 증명을 생성했다. 이 모델은 정리 증명에 최적화되어 있지 않아 간결하지만 오류가 많은 출력을 내는 경향이 있었으므로, 포괄성과 엄밀성을 높이기 위해 여러 라운드에 걸쳐 증명을 반복적으로 정제하도록 프롬프팅했다.

###### 2. We generated candidate proofs using a variant of DeepSeek-V3.2-Exp-Thinking. As this model was not optimized for theorem proving and tended to produce concise but error-prone outputs, we prompted it to iteratively refine its proofs over multiple rounds to improve comprehensiveness and rigor.

3. 우리는 다양한 문제 유형(예: 대수, 정수론)에 걸쳐 증명을 무작위로 표집하고, 수학 전문가들이 위에서 설명한 평가 루브릭에 따라 각 증명을 채점하도록 했다.

###### 3. We randomly sampled proofs across diverse problem types (e.g., algebra and number theory) and had mathematical experts score each proof according to the evaluation rubrics described above.

이 과정을 통해 초기 RL 데이터셋 D_v = {(X_i, Y_i, s_i)}가 만들어졌으며, 각 항목은 문제 X_i, 증명 Y_i, 그리고 전체 증명 점수 s_i ∈ {0, 0.5, 1}로 구성된다.

###### This process yielded an initial RL dataset D𝑣 = {(𝑋𝑖, 𝑌𝑖, 𝑠𝑖)}, where each item consists of a problem 𝑋𝑖, a proof 𝑌𝑖, and an overall proof score 𝑠𝑖 ∈ {0, 0.5, 1}.

---

**RL 목적함수 (RL Objective)**

수학 및 코드 관련 추론 데이터로 지도 미세조정된 DeepSeek-V3.2-Exp-SFT 버전을 기반으로, 우리는 두 가지 보상 요소를 사용해 강화학습으로 모델이 증명 분석을 생성하도록 훈련했다:

###### Building on a version of DeepSeek-V3.2-Exp-SFT which was supervised finetuned on reasoning data related to mathematics and code, we trained the model with reinforcement learning to produce proof analyses using two reward components:

• **형식 보상 R_format**: 모델이 식별된 문제점의 요약과 증명 점수를 모두 생성하도록 강제하는 지시 함수로, 최종 응답에 "Here is my evaluation of the solution:"이라는 핵심 문구와, "Based on my evaluation, the final overall score should be:" 뒤에 \boxed{} 안의 점수가 포함되어 있는지를 확인한다.

###### • Format reward 𝑅format: An indicator function that enforces the model to generate both a summary of identified issues and a proof score, by checking whether the final response contains the key phrase "Here is my evaluation of the solution:" as well as a score within \boxed{} following "Based on my evaluation, the final overall score should be:".

• **점수 보상 R_score**: 예측된 점수 s'_i와 주석된 점수 s_i 사이의 근접도에 기반한 보상:

###### • Score reward 𝑅score: Rewards based on proximity between predicted score 𝑠′𝑖 and annotated score 𝑠𝑖:

$$R_{score}(s'_i, s_i) = 1 - |s'_i - s_i| \tag{1}$$

검증기 훈련을 위한 RL 목적함수는 다음과 같다:

###### The RL objective for training the verifier is:

$$\max_{\pi_\varphi} \mathbb{E}_{(X_i,Y_i,s_i)\sim D_v,\ (V'_i,s'_i)\sim\pi_\varphi(\cdot|X_i,Y_i)}\left[R_{format}(V'_i) \cdot R_{score}(s'_i, s_i)\right] \tag{2}$$

여기서 V'_i는 검증기의 최종 응답을 나타내고, s'_i는 거기서 추출된 증명 점수다.

###### where 𝑉′𝑖 denotes the verifier's final response and 𝑠′𝑖 is the proof score extracted from it.

---

### 2.1.2. 증명 분석을 검토하기 위한 메타 검증 도입 (Introducing Meta-Verification to Review Proof Analyses)

2.1.1절에서 설명한 접근법은 예측된 증명 점수를 전문가 주석에 맞추도록 RL로 증명 검증을 훈련하지만, 식별된 문제점 자체에 대해서는 직접적인 지도(supervision)를 제공하지 않는다.

###### The approach described in Section 2.1.1 trains proof verification through RL to align predicted proof scores with expert annotations, but provides no direct supervision on the identified issues themselves.

<<<<<<< HEAD
이는 치명적인 취약점을 만든다: 훈련 중 결함 있는 증명(s_i < 1)을 평가할 때, 검증기는 존재하지 않는 문제점을 지어내면서도 정확한 점수를 예측함으로써 온전한 보상을 받 ㅡ,,,,,,,,,,,,,---------------을 수 있으며, 이는 검증기의 신뢰성을 훼손한다.
=======
이는 치명적인 취약점을 만든다: 훈련 중 결함 있는 증명(s_i < 1)을 평가할 때, 검증기는 존재하지 않는 문제점을 지어내면서도 정확한 점수를 예측함으로써 온전한 보상을 받을 수 있으며, 이는 검증기의 신뢰성을 훼손한다.
>>>>>>> 2b107de7f97b70b26578202730c5a7fcb0759d17

###### This creates a critical vulnerability: when evaluating flawed proofs (where 𝑠𝑖 < 1) during training, the verifier can receive full reward by predicting the correct scores while hallucinating non-existent issues, undermining its trustworthiness.

이 문제를 해결하기 위해 우리는 메타 검증을 도입한다: 검증기가 식별한 문제점이 실제로 존재하는지, 그리고 그 문제점들이 평가 루브릭 I_v에 따라 예측된 증명 점수를 논리적으로 정당화하는지를 평가하는 2차 평가 과정이다.

###### To address this problem, we introduce meta-verification: a secondary evaluation process that assesses whether issues identified by the verifier indeed exist and whether these issues logically justify the predicted proof score according to the evaluation rubrics I𝑣.

완전한 메타 검증 루브릭 I_mv는 부록 A.3에 상세히 기술되어 있다.

###### The complete meta-verification rubrics I𝑚𝑣 are detailed in Appendix A.3.

우리는 이 평가를 수행할 전용 메타 검증기를 RL로 훈련했다. 메타 검증기의 피드백을 검증기 훈련에 통합함으로써, 검증기가 문제점을 식별할 때의 충실성(faithfulness)을 개선할 수 있다.

###### We trained a dedicated meta-verifier using RL to perform this evaluation. By incorporating the meta-verifier's feedback into verifier training, we can improve the faithfulness of the verifier's issue identification.

---

**메타 검증기 훈련 과정 (Meta-Verifier Training Process)**

1. 우리는 2.1.1절에 따라 초기 검증기 π_φ를 얻었다.

###### 1. We obtained an initial verifier 𝜋𝜑 following Section 2.1.1.

2. 수학 전문가들이 I_mv에 따라 검증기 응답의 품질을 채점하여 데이터셋 D_mv = {(X_i, Y_i, V_i, ms_i)}를 만들었다. 여기서 V_i는 증명 Y_i에 대한 분석이고, ms_i ∈ {0, 0.5, 1}은 전문가가 주석한 품질 점수다.

###### 2. Mathematical experts scored the quality of verifier responses according to I𝑚𝑣, creating dataset D𝑚𝑣 = {(𝑋𝑖, 𝑌𝑖,𝑉𝑖, 𝑚𝑠𝑖)}, where 𝑉𝑖 is the analysis of proof 𝑌𝑖 and 𝑚𝑠𝑖 ∈ {0, 0.5, 1} is the expert-annotated quality score.

3. 우리는 검증기의 증명 분석 V를 분석하도록 메타 검증기 π_η(·|X, Y, V, I_mv)를 훈련했다. 메타 검증기는 분석 자체에서 발견된 문제점의 요약을 생성하고, 이어서 검증기의 분석이 얼마나 정확하고 정당한지를 측정하는 품질 점수를 부여한다. RL 목적함수는 형식 보상과 점수 보상을 사용하는, 검증기 훈련과 동일한 구조를 따른다.

###### 3. We trained a meta-verifier 𝜋𝜂(·|𝑋, 𝑌, 𝑉, I𝑚𝑣) to analyze the verifier's proof analysis 𝑉. The meta-verifier produces a summary of issues found in the analysis itself, followed by a quality score measuring how accurate and justified the verifier's analysis is. The RL objective follows the same structure as the verifier training, with format and score rewards.

---

훈련된 메타 검증기 π_η를 사용하여, 우리는 메타 검증 피드백을 보상 함수에 통합함으로써 검증기 훈련을 강화했다:

###### Using the trained meta-verifier 𝜋𝜂, we enhanced the verifier training by integrating meta-verification feedback into the reward function:

$$R_V = R_{format} \cdot R_{score} \cdot R_{meta} \tag{3}$$

여기서 R_meta는 메타 검증기가 부여한 품질 점수다.

###### where 𝑅meta is the quality score from the meta-verifier.

우리는 강화된 검증기를 검증 데이터셋 D_v와 메타 검증 데이터셋 D_mv 양쪽 모두로 훈련했으며, D_mv에는 메타 검증기 훈련에 사용한 것과 동일한 보상 메커니즘을 적용했다.

###### We trained the enhanced verifier on both the verification dataset D𝑣 and the meta-verification dataset D𝑚𝑣, using the same reward mechanism on D𝑚𝑣 as used for training the meta-verifier.

그 결과 모델은 증명 검증과 메타 검증 과제를 모두 수행할 수 있다.

###### The resulting model can perform both proof verification and meta-verification tasks.

D_v의 검증 분할에서, 메타 검증기가 평가한 검증기 증명 분석의 평균 품질 점수는 0.85에서 0.96으로 향상되었으며, 증명 점수 예측 정확도는 동일하게 유지되었다.

###### On a validation split of D𝑣, the average quality score of the verifier's proof analyses – as evaluated by the meta-verifier – improved from 0.85 to 0.96, while maintaining the same accuracy in proof score prediction.

---

