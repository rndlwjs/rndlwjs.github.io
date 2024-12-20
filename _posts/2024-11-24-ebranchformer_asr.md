---
layout: single
title: '[실습] E-branchformer, Conformer ASR 훈련 비교'
categories: 'ASR'
tag: '실습'
use_math: true
author_profile: false
---

포스트 준비 중

이번 포스트는 librispeech 100h (ASR) 데이터로 E-branchformer, Conformer를 훈련하며 비교하는 글이다.

E-branchformer와 Conformer 인코더의 큰 차이는 local과 global feature를 처리하는 구조가 어떻게 다르게 배치되는지이다. E-branchformer는 Conformer의 일렬 구조보다 병렬 구조가 더 낫다고 주장한다.

두 모델의 차이는 다음 사진과 같다.

