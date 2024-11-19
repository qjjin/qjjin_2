---
title: "R"
tags:
    - Statistics
    - NGS
    - Differentially Expressed Gene
date: "2024-11-19"
---

# R
---

**R**은 통계 계산과 그래픽을 위한 프로그래밍 언어이자 소프트웨어 환경으로, 오픈소스로 쓰여졌으며 무료입니다. 통계 소프트웨어 개발과 자료 분석에 널리 사용되고 있으며, 패키지 개발이 용이해 통계 소프트웨어 개발에 많이 쓰이고 있습니다.
[R](https://www.r-project.org/)


저번 RNA sequencing 포스팅에서 언급한 적이 있었는데 NGS data의 처리를 R를 이용하는 경우가 많습니다. 

## Bioconductor

[Bioconductor](https://www.bioconductor.org/)

Bioconductor는 R 언어를 기반으로 생물정보학 및 생물학 데이터를 분석하는 데 특화된 소프트웨어 패키지 모음입니다. 주로 유전체학, 전사체학, 단백질체학 등 고차원 생물학 데이터를 처리하고 분석하는 데 사용됩니다.

3,000개 이상의 패키지가 있으며, 데이터 분석의 다양한 단계(데이터 전처리, 품질 관리, 통계 분석, 시각화 등)를 지원합니다.

```
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
BiocManager::install()  # Bioconductor 기본 설치
```

특정 패키지를 설치하려면:
```
BiocManager::install("패키지이름")
```

예: DESeq2 패키지 설치
```
BiocManager::install("DESeq2")
```


Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.