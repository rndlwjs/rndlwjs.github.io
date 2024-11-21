---
layout: single
title: '[실습] VITS 모델(TTS) 중국어 데이터 훈련 및 평가 실습'
categories: 'TTS'
use_math: true
---

<간단한 모델 소개>
VITS (Kim, 2021)는 

<데이터셋 소개>
Chinese Mandarin Female Database
12 hours
wav format with 48KHz 16bit

https://en.data-baker.com/datasets/freeDatasets/

TTS 평가는 subjective human evaluation (Mean Opinion Score (MOS))으로 이루어진다. 모델 평가는 논문에서 제안되었기에, 이번 글에서는 1인 정성 평가를 진행하고자 한다.

객관적인 모델 평가를 위해 다음과 같은 기준을 세워 평가를 진행하고자 한다.

100문장 평가를 진행 (약 3시간 소요)
다양한 표현 진행 (chatGPT 추출)


MOS를 Naturalness, intelligibility 기준으로 평가를 한다.

Naturalness

-중국어는 성조(tone)이 중요하다. (1-4성)
-Tonal system (iambic, trochaic)
-동음이의어 (같은 음, 다른 성조)
-Prosody 확인해야 한다.

Intelligibility

-conformer-cn WER
-conformer-cn CER

Sheet처럼 자동 평가를 진행해주는 연구도 진행중이다. 추측컨대 CMU에서도 발음평가에 대한 연구를 진행하는 것으로 알고 있다.