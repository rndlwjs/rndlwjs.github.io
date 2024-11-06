---
layout: single
title: '[Review] Studies of Speaker Diarization by Quan Wang'
categories: 'Speaker'
---

본 포스트는 **Udemy** 유료강의 **A Tutorial on Speaker Diarization**을 보고 정리한 내용이다. 2017-2020년 전후 내용을 다루고 있어 최신 연구와 거리가 있을 수 있지만, 기본적인 이해를 위해 포스트를 정리한다.

```Speaker diarization을 한마디로 표현하면 "Who Spoke When"을 검출하는 기법이다.```

여러 화자(speaker)가 포함된 음성 속, 화자의 발화 포인트를 시간 기준으로 나누는 기술이다.

따라서 False Alarm (FA), Missed Speech (MS), 그리고 Confusion (speaker error)가 중요한 지표가 된다. 이를 종합한 Diarization error rate (DER)은 논문 및 챌린지에서 사용되는 기준이다.

$DER = (FA + MS + Confusion) / reference time$

흔히 Speech Separation, Speaker change detection과 혼용될 수 있지만, 엄연히 다른 task이다. (위 task들은 각각 frequency domain, deltas에 중요도를 더 둔다.)

### Unsupervised methods

Unsupervised 방법으로 online, offline clustering으로 구분된다. online은 음성이 끝나기 전에 화자 정보를 실시간으로 배정(assign)하지만, offline은 음성이 끝나고 배정을 진행한다.

Online으로는 naive clustering, links clustering 기법이 있다.

Offline으로는 Agglomerative Hierarchical clustering (AHC), K-means++, 그리고 spectral clustering 기법이 있다.

Wang은 AHC 방법을 일반적인 방법이라 소개하였다. AHC는 max-heap 구조를 갖게 되며, $O(N^2logN)$으로 처리할 수 있다고 한다. (canopy clustering 등 사전에 threshold 군집화를 한번 진행하면, 계산량을 줄일 수 있다고 제안하였다.)

Spectral clustering을 가장 좋은 unsupervised 방법이라 소개한다. 이는 graph-cut 문제와 유사하며, (Wang et al., 2017)은 4가지 절차를 도입한 방법을 제안하고 있다.

```
# input의 모양은 (n__samples, n_feature)이다. 
# 다음은 400개 음성 collar, 192차원 임베딩을 가정한 예시이다.
matrix = np.array([[1.0] * 192] * 400)

labels = configs.icassp2018_clusterer.predict(matrix)
expected = np.array([0] * 100 + [1] * 100 + [2] * 100 + [3] * 100)

print(np.testing.assert_equal(expected, labels))
```

Wang et al. 에서 주장한 군집화 방법, *icassp2018_clusterer.predict()* 은 다음과 같은 순서로 진행된다.

```
1. affinity matrix
2. refinement operations
3. eigen decomposition of Laplacian matrix
4. Determine number of clusters via eigen-gap
5. K-means on eigen-vectors
```