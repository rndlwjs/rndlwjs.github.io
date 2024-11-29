---
layout: single
title: '[실습] VITS 모델(TTS) 중국어 데이터 훈련 및 평가 실습'
categories: 'TTS'
use_math: true
---

이번에는 VITS 모델(TTS)을 중국어 데이터로 훈련한 과정을 기록한 포스트이다.

VITS[Kim+ `21] 모델은 E2E 방식으로 발화속도를(rhythm) 자연스럽게 표현할 수 있게 설계된 모델이다.
이전 포스트에 VITS 논문 정리한게 있으니, 궁금한 내용이 있다면 참고하면 좋을거 같다.

> 글쓴이는 중국에서 8년 유학 경험이 있으며, 전직장에서 중국어(CN) TTS 평가 업무를 맡았다.

VITS 모델 기반 CN-TTS에 대한 오픈소스가 있어, 이를 직접 훈련시키고 평가해보고자 했다. 엑셀로 평가를 진행하였으며, 자세한 내용은 [TTS 결과](https://drive.google.com/drive/folders/1osLfIye7XqybgXssSfZQYU4OQKzeRcTY?usp=sharing) 에서 확인할 수 있다.

### 데이터셋

|Dataset|
|:---|
|Chinese Mandarin Female Database|
|12 hours|
|wav format with 48KHz 16bit|
|[데이터셋 링크](https://en.data-baker.com/datasets/freeDatasets/)|

### 훈련 결과

|Result|
|:---|
|100 epochs|
|Titan-Xp GPU 기준, 40min/epoch|
|[TTS 결과](https://drive.google.com/drive/folders/1osLfIye7XqybgXssSfZQYU4OQKzeRcTY?usp=sharing)|

<audio controls> <source src='/assets/files/2024-11-22-test.wav'> </audio>

### 평가 기준
TTS 평가는 subjective human evaluation (Mean Opinion Score (MOS))으로 이루어진다.
정량 평가는 논문에 기록되어 있기 때문에, 이번 포스트는 글쓴이의 정성 평가 결과를 공유하고자 한다.

일반적으로 TTS 결과물의 **Naturalness**, **intelligibility**에 대한 평가가 이뤄진다.

1. ChatGPT를 활용하여 CN-TTS 평가 데이터를 **100개** 문장 구축했다.
	- TTS가 활용되는 환경을 고려하여, 5가지 상황에 대한 prompt로 중국어 텍스트를 생성했다.
	- ex. tongue twister (잿말 놀이), 게임, 영화대사, 일상표현, 뉴스 (각 20개)

2. Naturalness에 대한 객관적인 평가를 위해 다음과 같은 가이드라인을 세웠다.
	- 기계음, 노이즈 등이 포함되어 부자연스럽지는 않은지
	- 중국어 발음, 성조 오류가 포함되지 않은지
	- 발화 속도가 자연스러운지

3. Intelligibility는 CN-ASR pretrained 모델로 평가를 진행하였다.
	- conformer-cn CER

### Naturalness 평가 결과
Naturalness는 5점 만점을 기준으로 하였다.

#### 도메인별 Naturalness 평가

| |잿말 놀이|게임|영화대사|일상표현|뉴스|**총평균**|
|:---|:---|:---|:---|:---|:---|:---|
|5점|7ea|11ea|11ea|9ea|2ea|-|
|4점|10ea|8ea|9ea|10ea|13ea|-|
|3점|3ea|1ea|-|1ea|5ea|-|
|2점|-|-|-|-|-|-|
|1점|-|-|-|-|-|-|
|**MOS**|4.2|4.5|4.5|4.4|3.5|**4.3**|

- Naturalness 순위는 **게임, 영화, 일상 표현 > 잿말 놀이 > 뉴스** 순서이다.
	- 도메인을 문장 20개로 대표할 수는 없지만, 빈도수 높은 표현이 자연스럽다는 것을 추측할 수 있다.
- **뉴스** 도메인 평가가 낮은 이유는 **긴 문장을 입력**으로 주었기 때문이라 추측된다. 
	- 프롬프트에서 긴 문장을 추출하도록 유도하였으며, 특별하게 어려운 어휘는 발견되지 않았다. 
- 중국어 텍스트 특징 중, `,`는 있지만 `_` 띄어쓰기는 없다.
	- "29_game_program_009.wav"는 `,` break 구간을 무시해서 발생한 오류이다.
	- VITS를 통해 자연스러운 발화를 추출하고 싶다면, 공백을 별도로 처리하는 방법을 고안해야 할 거 같다.

#### 5점 미만 유형 분석
오류유형1: 기계음

|기계음|4점|3점|
|:---|:---|:---|
|노이즈|34ea|5ea|
|끊김|4ea|-|

오류유형2: 발음 오류

|발음|4점|3점|
|:---|:---|:---|
|발음 오류|3ea|2ea|
|성조 오류|2ea|-|

오류유형3: 속도

|속도|4점|3점|
|:---|:---|:---|
|break 부재|5ea|2ea|
|break 남용|2ea|1ea|

### 번외로 느낀점
- E2E (one-stage) TTS는 학습 측면에서 편리하지만, 여러명의 화자가 필요한 상황에서는 보코더를 활용한 two-stage 모델이 더 유리할거라 생각된다.
- 차후 유사한 모델 평가 시, 언어학적으로 리듬을 평가하는 기준을 정리하여, Tonal system (iambic, trochaic), Prosody 등 분석을 통해, 발화 속도에 대해 좀 더 객관적인 평가를 해보면 좋을거 같다 생각이 든다.
- TTS, VC 결과의 "자동 평가" 연구도 흥미로운 주제이다. ex.[SHEET](https://github.com/unilight/sheet) 추측컨대 CMU WavLab에서도 발음평가에 대한 연구를 진행하는듯 하다.