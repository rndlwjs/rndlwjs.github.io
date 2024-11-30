---
layout: single
title: '[paper] E-BRANCHFORMER: BRANCHFORMER WITH ENHANCED MERGING FOR SPEECH RECOGNITION'
categories: 'ASR'
use_math: true
---

E-Branchformer[Kim22]는 음성인식 분야 SOTA모델 Conformer와 견주어 비교되는 모델이다.

ASR 분야 여러가지 트랜스포머 변형 모델을 알아보는 것도 흥미롭겠지만, CMU 대용량 음성모델 OWSM3.1 기본 구조로 사용되었다고 하여 호기심을 불러 일으켰다.

*ICASSP2024 현장에서 세션을 너무 재밌게 들은 이유도 크다.*

> 논문은 merging method, stacking additional point-wise module을 도입한 점에 기여를 하였다고 한다.

## 핵심요약
1. Branchformer[Peng22]의 Conformer[Gulati20]와 차이점은 local, global branch를 parallel 하게 구조화한 점이다.
    - Conformer의 sequential한 구조는 interpret, modify 하기 어려운 점이 있다.
    - 어텐션과 컨볼루션의 위치가 고정되었지만(fixed), 꼬여있는(interleaving) 패턴을 가지는 것은 바람직하지 않다고 한다.
    - 어텐션은 이차(quadratic) 배수의 복잡도를 보인다. w.r.t. the sequence length
2. 이번 논문은 Branchformer[Peng22]의 merging module을 개선시킨 연구이다.
    - Depth-wise 컨볼루션을 브랜치 병합에 활용하여 인접한 (adjacent) 특징을 반영하였다.
    - SE block을 도입하여 브랜치 병합 중, global 정보를 반영하고자 하였다.
3. AED 모델 성능이 뛰어나며, CTC, transducer는 차후 적용한다고 한다.

### BRANCHFORMER
branchformer는 세 가지 구성이 포함된다. global extractor branch, local extractor branch, merge module.

global extractor branch
$Y_{G}=Dropout(MHSA(LN(X)))$

local extractor branch
$Z = GELU(LN(X)U)$

$[A B] = Z$

$\tilde{Z} = CSGU(Z) = A \odot DwConv(LN(B))$

$Y_{L} = Dropout(\tilde{Z}V)$

merge module
$Y_{Merge} = Concat(Y_{G}, Y_{L})W$

### E-BRANCHFORMER
Modifications of the merging module that take temporal information into account. (self-attention and convlution to be combined sequentially and in parallel)

#### Depth-wise convolution
Depth-wise 컨볼루션은 인접한 특징 (adjacent feature)를 반영하면서, 연산량 혹은 속도의 큰 차이를 보이지 않는다.

기존 Branchformer는 linear projection을 통과하여 채널 기준으로 병합되지만(fused), depth-wise 컨볼루션을 활용하면 공간 정보를 보완할 수 있다.

$Y_{C} = Concat(Y_{G}, Y_{L})$

$Y_{D} = DwConv(Y_{C})$

$Y_{Merge} = (Y_{C} + Y_{D})W$

#### Squeeze-and-Excitation
SE-block은 global한 정보를 활용하는 모듈이다. 병합전, $\bar{Y}_{D}$ 에 SE-Block을 적용시켰다고 한다.

[//]: # $\bar{y}_{D} = \frac{1}{T}\sum\limits_{t=1}^T y_{Dt}$

[//]: # $g = \sigma(MLP(\bar{y}_{D})) = \sigma(Swich(\bar{Y}_{D}W_{1})W_{2})$

[//]: # $Y_{D_i}' = g_{i} \odot Y_{D_{i}} \forall i \in \{1, \ldots, d\}$

[//]: # $Y_{Merge} = (Y_{C} + Y_{D}')W$

일반 SE block과의 차이점은 $Sigmoid$가 아닌 $Swish$ 활성함수를 사용했다는 점이다.

#### Point-wise FFN
Branchformer는 2개 cgMLP가 있지만, Transformer와 다르게 FFN은 사용하지 않는다. FFN은 시간 (temporal) 정보 aggregation 이후 개별적인 특징을 (pointwise) 정제하는 특징이 있다.

트랜스포머는 MSHA와 FFN을 스택으로 특징을 처리하며, interleaving 패턴으로 쌓아올리는데, [55]에 따르면 random하게 하는게 낫다고 한다. 

CMU에서 OpenAI whisper를 OWSM3.1이라는 모델로 재구현할 때 사용된 기본구조라고 한다.
