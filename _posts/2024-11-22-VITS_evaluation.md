---
layout: single
title: '[실습] VITS 모델(TTS) 중국어 데이터 훈련 및 평가 실습'
categories: 'TTS'
use_math: true
---

이번에는 VITS 모델(TTS)을 중국어 데이터로 훈련하는 과정을 기록한 포스트이다.

VITS[Kim21] 모델은 E2E 방식으로 발화속도를(rhythm) 자연스럽게 표현할 수 있게 설계된 모델이다.
이전 포스트에 VITS 논문 정리한게 있으니, 궁금한 내용이 있다면 참고하면 좋을거 같다.

> 글쓴이는 중국에서 8년 유학 경험이 있으며, 전직장에서 중국어(CN) TTS 평가 업무를 맡았다.

VITS 모델 기반 CN-TTS에 대한 오픈소스가 있어, 이를 직접 훈련시키고 평가해보고자 했다.

### 데이터셋
Chinese Mandarin Female Database
12 hours
wav format with 48KHz 16bit
[데이터셋 링크](https://en.data-baker.com/datasets/freeDatasets/)

### 훈련 결과
100 epochs
Titan-Xp GPU 기준, 1h/epoch

<audio controls>
    <source src='/assets/files/2024-11-22-test.wav'>
</audio>

[TTS 결과](https://drive.google.com/drive/folders/1osLfIye7XqybgXssSfZQYU4OQKzeRcTY?usp=sharing)

### 평가
TTS 평가는 subjective human evaluation (Mean Opinion Score (MOS))으로 이루어진다.
정량 평가는 논문에 기록되어 있기 때문에, 이번 포스트는 글쓴이의 정성 평가 결과를 공유하고자 한다.

일반적으로 TTS 결과물의 Naturalness, intelligibility에 대한 평가가 이뤄진다.

1. ChatGPT를 활용하여 CN-TTS 평가 데이터를 100개 문장 구축했다.
	- TTS가 활용되는 환경을 고려하여, 5가지 상황에 대한 prompt로 중국어 텍스트를 생성했다.
	- ex. tongue twister, 게임, 영화대사, 일상표현, 뉴스. 

2. Naturalness에 대한 객관적인 평가를 위해 다음과 같은 가이드라인을 세웠다.
	- 중국어는 성조(tone)이 중요하다. (1-4성)
	- Tonal system (iambic, trochaic)
	- 동음이의어 (같은 음, 다른 성조)
	- Prosody 확인해야 한다.

3. Intelligibility는 CN-ASR pretrained 모델로 평가를 진행하였다.
	- conformer-cn WER
	- conformer-cn CER


- E2E (one-stage) TTS는 학습 측면에서 편리하지만, 여러명의 화자가 필요한 상황에서는 보코더를 활용한 two-stage 모델이 더 유리할거라 생각된다.
- Sheet처럼 자동 평가를 진행해주는 연구도 진행중이다. 추측컨대 CMU에서도 발음평가에 대한 연구를 진행하는 것으로 알고 있다.

[^scala] \[^scala]: https://en.data-baker.com/datasets/freeDatasets/
