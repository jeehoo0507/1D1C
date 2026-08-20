![[Pasted image 20260729004904.png]]
### 개요
---
2026 카글 놀이터 시리즈에 오신 것을 환영합니다!
우리는 이전 놀이터의 정신을 계속하여 커뮤니티가 기계 학습 기술을 연습하고 매월 경쟁을 예상 할 수 있는 흥미롭고 접근하기 쉬운 데이터 세트를 제공 할 계획입니다.

**당신의 목표 :** 학생의 건강 위험을 예측합니다

![[Pasted image 20260729004957.png]]

### 평가
---
제출은 예측된 클래스와 관측된 목표 사이의 [균형 잡힌 정확도로](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.balanced_accuracy_score.html) 평가됩니다.

#### 제출 파일

테스트 세트의 각 ID에 대해 health_condition 변수에 대한 레이블(위험, 건강에 좋지 않음, 적합)을 예측해야 합니다. 파일에는 헤더가 포함되어야하며 다음 형식이 있어야합니다.

```applescript
id,health_condition
690088,at-risk
690089,at-risk
690090,at-risk
etc.
```
