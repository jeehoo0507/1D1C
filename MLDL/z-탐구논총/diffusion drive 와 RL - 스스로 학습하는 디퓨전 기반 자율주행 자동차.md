현재 자율주행에 사용되는 기존 방법들은 주로 단일 모드 경로를 생성하는 데 집중하고 있습니다. 
이러한 방법은 복잡한 교통 상황에서의 다양성과 불확실성을 충분히 포괄하지 못하는 문제점이 있습니다.
또한, 기존의 확산 모델을 차량의 실제 주행에 적용하려고 할 때, 
많은 비노이즈 단계가 필요한데 이로 인해 실시간 속도에서의 성능이 저하되는 문제가 발생합니다.



그렇기에 기존의 많은 단계의 노이즈 제거 과정을 가지는 디퓨전 모델을 경량화 하여
노이즈 단계를 2개 이하로 줄인 자율주행 기술인 디퓨전 드라이브를 사용합니다


$$
   q(\tau_i | \tau_0) = N(\tau_i; \sqrt{\bar{\alpha}_i} \tau_0, \sqrt{1 - \bar{\alpha}_i} I)
   $$
Loss
$$
   L = \sum_{k=1}^{N_{\text{anchor}}} \left[y_k L_{\text{rec}}(\hat{\tau}_k, \tau_{\text{gt}}) + \lambda BCE(\hat{s}_k, y_k)\right]
   $$
   
   DiffusionDrive는 NAVSIM 데이터셋에서 88.1의 PDMS 점수를 달성했으며, 이는 이전의 최첨단 방법들보다월가의 평균보다 월등한 성과를 보였습니다. 이 모델은 45 FPS의 실시간 속도로 주행을 가능하게 하여 매우 효과적인 자율 주행을 실현합니다.
-diffusiondrive -


기존의 자율주행에서 강화학습을 채용하여 가상환경에서의 시뮬레이션과 실제 상황을 앙상블합니다.

$$J(\pi_{RL}) = \mathbb{E}_{s, \tau} \left[ R_{sim}(s, \tau) - \beta D_{KL}(\pi_{RL}(\tau|s) || p_{diff}(\tau|s)) \right]$$
$$\tau_{final} = \frac{\Sigma_{RL}}{\Sigma_{diff} + \Sigma_{RL}} \tau_{diff} + \frac{\Sigma_{diff}}{\Sigma_{diff} + \Sigma_{RL}} \tau_{RL}$$
$$L_{total} = L_{DiffusionDrive} + \gamma L_{TD}(\pi_{RL})$$

