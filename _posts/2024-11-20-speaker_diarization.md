---
layout: single
title: '[implementation] ECAPA-TDNN 모델을 활용한 화자분할 실험'
categories: 'Speaker'
use_math: true
---

### Raw waveform 불러오기
```
audio, sr = librosa.load("an4_diarize_test.wav", sr=16000)
```

1. Speaker-Uniform Segmentation
2. Speaker-Change-point Segmentation

### vad 기반 화자 분할
```
# WiraDKP
def zcr_vad(y, shift=0.025, win_len=2048, hop_len=1024, threshold=0.005) -> list:
    zcr = librosa.feature.zero_crossing_rate(y + shift, win_len, hop_len)[0]
    activity = gaussian_filter1d(zcr, 1) > threshold
    activity = np.repeat(activity, len(y) // len(activity) + 1)
    activity = activity[:len(y)]
    return activity

activity = zcr_vad(audio)
```

### collar 기반 화자 분할
```
# time 길이 단위로 segmentation을 진행한 후, feature를 뽑는 기법
# Spectral Clustering, Titanet은 collar 기반 분할을 사용함
# time = [250, 500, 750, 1000] #millisecond

def divide_time(audio, sr, time=0.5):
    time_stamp = [(i, min(i + int(sr*0.5), len(audio))) 
                  for i in range(0, len(audio), int(sr*0.5))]
    return time_stamp
    
for i, j in divide_time(audio, sr):
    extract_embedding(audio[i:j], sr)
```

액션파워 블로그에 따르면, Speaker-change-point Segmentation, Speaker Feature Extraction, Offline clustering 서비스를 제공한다고 한다.

### Mini Experiment
0.5s Collar, ECAPA-TDNN 임베딩, offline clustering 세팅으로 toy 실험을 진행하였다.

AHC와 Spectral Clustering 각 방법을 진행해보니, 어느 정도 분별이 된다는 점을 확인할 수 있었다. 해당 방법은 코사인 유사도 기반으로 군집화를 진행하였다. (당연한 이야기일 수 있지만 K-means 군집화는 효과가 없는 것을 확인하였다)

AHC의 단점은 n_cluster를 지정해줘야 한다는 점이다.

Accuracy는 다음과 같다.

DER은 다음과 같다.