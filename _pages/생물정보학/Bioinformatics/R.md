---
title: "R"
tags:
    - Statistics
    - NGS
    - Differentially Expressed Gene
date: "2024-11-19"
thumbnail: "/assets/img/thumbnail/R.png"
bookmark: true
---

# R
---

**R**은 통계 계산과 그래픽을 위한 프로그래밍 언어이자 소프트웨어 환경으로, 오픈소스로 쓰여졌으며 무료입니다. 통계 소프트웨어 개발과 자료 분석에 널리 사용되고 있으며, 패키지 개발이 용이해 통계 소프트웨어 개발에 많이 쓰이고 있습니다.
[R](https://www.r-project.org/)

저번 RNA sequencing 포스팅에서 언급한 적이 있었는데 NGS data의 처리를 R를 이용하는 경우가 많습니다. 
오늘은 RNA sequencing에서 read quantification까지 끝난 데이터를 DEG하는 법을 알아보겠습니다.
[data file](https://github.com/qjjin/qjjin_2/raw/main/assets/img/gene_count_matrix.csv)

DEG가 궁금하신 분은 블로그의 DEG post를 참고해주세요~

## Bioconductor
---

[Bioconductor](https://www.bioconductor.org/)는 R 언어를 기반으로 생물정보학 및 생물학 데이터를 분석하는 데 특화된 소프트웨어 패키지 모음입니다. 주로 유전체학, 전사체학, 단백질체학 등 고차원 생물학 데이터를 처리하고 분석하는 데 사용됩니다.
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


## DESeq2
---

1. DESeq2 패키지 설치
```
BiocManager::install("DESeq2")
```

오류없이 패키지 설치가 완료되었다면 DESeq2를 R에서 불러와야합니다.

2. DESeq2 불러오기
```
library(DESeq2)
library(tidyverse)
```
[tidyverse](https://www.tidyverse.org/)는 갑자기 무엇이길래 library 하라는거야! 생각이 드실겁니다.
RNA seq 데이터를 분석하고 이것을 보기 좋게 시각화하여 표현하는 것이 좋겠죠?
R 프로그래밍 언어를 사용하는 데이터 분석 및 시각화 작업을 단순화하고 향상시키기 위한 패키지 모음입니다.
**tidyverse도 DESeq2 패키지 설치와 동일하게 "" 사이에 tidyverse를 입력하면 됩니다!**

3. 데이터 불러오기
library 과정을 거쳤다면 데이터를 불러와서 분석을 진행해야겠죠?
```
setwd("/Users/사용자/Downloads")
countData <- as.matrix(read.csv("gene_count_matrix.csv", row.names = "gene_id"))
colData <- read.csv("phenotype.csv", sep=",")
```
저 같은 경우 setwd를 많이 사용하는 편인데 디렉토리는 당연히 본인에게 맞는 디렉토리가 필요해요.
data file를 다운받아서 파일을 열어보면 저는 gene_id로 입력을 해놨어요.

4. phenotype data
갑작스럽게 phenotype.csv 파일을 불러왔어요. '저건 갑자기 무슨 파일이야?' 하실텐데
[phenotype]((https://github.com/qjjin/qjjin_2/raw/main/assets/img/phenotype.csv)
위 파일을 참고해주세요.
