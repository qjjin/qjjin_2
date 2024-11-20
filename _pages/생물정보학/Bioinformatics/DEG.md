---
title: "DEG(Differential Expression Gene)"
tags:
    - Statistics
    - NGS
    - Differentially Expressed Gene
date: "2024-11-19"
---

# DEG(Differential Expression Gene)
---

DEG 분석은 2개 이상의 샘플에서 전사체 시퀀싱을 수행한 후 샘플 간 유전자 발현의 차이를 비교 분석하는 기법입니다. 시료 준비 과정에서나 시퀀싱 과정 중에 발생할 수 있는 실험 오차를 통계적으로 바로잡을 수 있으며, 대조군과 비교군 간에 발현의 유의미를 찾을 수 있습니다. 기본적으로 DEG를 구한다는 것은 주어진 데이터셋을 이분법적으로 바라본다는 것을 전제로 하며, DEG의 기준은 크게 두 가지, Fold Change(FC)와 Adjusted P value를 이용합니다.

## Fold change

Fold change라는 것은 측정값과 후속 측정값 사이에 양이 얼마나 변하는지를 설명하는 측정값이다. 두 수량 사이의 비율로 정의된다.

생명과학 연구에서 RNA-Seq, Microarray, qPCR등 유전체학(일반적으로 생물정보학)에서 유전자 발현량을 두 조건에 대하여 서로 비교하고자 할 때 fold change를 많이 이용한다. 이 수치는 특히 DEG(differentially expressed gene)을 찾는 데 매우 중요한 의미가 있다. Fold change의 정의는 비교 조건(treatment)의 값을 기준 조건(control)의 값으로 나누는 것이다.
예를 들어, Sample A 와 B 의 경우 A 에 대한 B 의 fold change 값은 B / A 이다. 즉, 30에서 60으로의 변경은 2의 fold change 값으로 정의된다. 이를 "1 fold increase"라고도 한다. 유사하게, 30에서 15로의 변화를 "0.5-fold decrease"라고 한다. Fold change 값은 해석이 쉬우므로 다른 시간(Time)에 수행된 생물학적 시스템의 다중 측정을 분석할 때 용이하게 사용된다.

## log2 Fold change

단점을 보완하는 방법으로 Fold change에 로그를 처리하는 방법이 있다. 단 Fold change = 0인 것은 제거하거나 전처리 과정을 거치는 것이 용이하다. 일반적으로는 fold Change에 밑이 2인 log2를 씌워서 몹시 큰 값들을 (값 간의 순서를 바꾸지 않고) 몹시 작은 값으로 내릴 수 있다. 따라서 양수 값은 증가, 음수값은 감소라고 쉽게 판단할 수 있다.

## Adjusted P value(Q-value)
P value는, 귀무가설(null hypothesis, H0)이 맞는다는 전제하에, 통곗값(statistics)이 실제로 관측된 값 이상일 확률을 의미한다. p value는 결괏값이 여러 개가 있으면 이중 우연히 결과가 통계적으로 의미 있는 값으로 나올 확률이 높아진다. 따라서 이런 가능성을 줄이는 방법이 p값 조정(Adjusted P value)이다. p값을 조정하는 순서는 다음과 같다.

1. 단순 조정 - 가정 검토 없이 단순히 p값을 조정하는 과정이다. 이 조정 방법에는 Bonferroni와 Sidak 조정이 있다.
2. 단계 조정 - 순차적으로 p값을 조정하는 것이다. 이 조정 밥법에는 step down bonferroni (Holm), step-down Sidak, Hochberg's step-up, Hommels's가 있다.
3. 재 표본 추출 검정 - 일정 크기의 표본 샘플을 뽑아서 다시 분석하는 방법이다. 이 조정 방법에는 bootstrap방법과 대체 없는 permutation test가 있다.
4. [False Discovery Rate(FDR)](https://www.incodom.kr/False_discovery_rate)

