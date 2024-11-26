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

3-1. phenotype data
갑작스럽게 phenotype.csv 파일을 불러왔어요. '저건 갑자기 무슨 파일이야?' 하실텐데
[phenotype](https://github.com/qjjin/qjjin_2/raw/main/assets/img/phenotype.csv)
위 파일을 참고해주세요.


<details>
<summary>데이터 전처리</summary>
<div markdown="1">

데이터 전처리는 선택입니다.
```
# siFOXM1
countData <- countData[, c(1,2,3,4,5,6,7,8)]
colData <- colData[c(1,2,3,4,5,6,7,8), ]

# siMYBL2
countData <- countData[, c(1,2,3,4,9,10,11,12)]
colData <- colData[c(1,2,3,4,9,10,11,12), ]
```
</div>
</details>

4. 데이터 확인
countData와 colData를 서로 매칭하는 단계입니다.
```
colnames(countData) <- colData$SRR_number
all(colData$SRR_number %in% colnames(countData)) 
countData <- countData[, colData$SRR_number]
all(colData$SRR_number == colnames(countData))
sum(is.na(countData))
countData <- na.omit(countData)
```
5. DEG 분석 

본격적인 DEG를 실시하는 과정입니다.
```
dds <- DESeqDataSetFromMatrix(countData = countData, colData = colData, design = ~ phenotype)
keep <- rowMeans(counts(dds)) >= 1 
dds <- dds[keep,] 
dds$phenotype <- factor(dds$phenotype, levels = c("control","test", "test2")) # control / test 순서 - control이 먼저 와야함
dds$phenotype
dds$phenotype <- relevel(dds$phenotype, ref = "control")
dds <- DESeq(dds)
```
rowMeans(counts(dds)) >= 1은 DESeq2 분석에서 표현되는 유전자 필터링 단계를 의미합니다. 구체적으로, 각 유전자의 평균 발현량(즉, rowMeans)이 1 이상인 유전자들만 선택하는 과정입니다. 
이 조건은 분석에서 의미 있는 수준의 발현을 보이지 않는, 즉 거의 발현되지 않는 유전자들을 제거하기 위해 사용됩니다. 
이렇게 하면 낮은 발현 수준으로 인해 생길 수 있는 잡음을 줄이고, 이후 차등 발현 분석의 신뢰성을 높일 수 있습니다.
따라서, 이 필터링 기준을 설정함으로써 발현 수준이 거의 없는 유전자는 제거되고, 발현량이 충분히 높은 유전자들만 남게 됩니다.

```
cbind(resultsNames(dds))
```
위 코드를 실행시켰을 때 

     [,1]                        
[1,] "Intercept"                 
[2,] "phenotype_test_vs_control" 
[3,] "phenotype_test2_vs_control"

위와 같은 output이 나옵니다.

```
siFOXM1 <- results(dds, name = "phenotype_test_vs_control", alpha = 0.05)
siMYBL2 <- results(dds, name = "phenotype_test2_vs_control", alpha = 0.05)
```
이름에 맞게 정리!

```
siFOXM1 <- siFOXM1[order(siFOXM1$padj), ]
siFOXM1
write.csv(as.data.frame(siFOXM1),
          file="siFOXM1.csv")

siMYBL2 <- siMYBL2[order(siMYBL2$padj), ]
siMYBL2
write.csv(as.data.frame(siMYBL2),
          file="siMYBL2.csv")
```
adjusted p-value로 정리하여 output file로 출력하는 코드입니다.
여기서 padj의 경우 여러 개의 가설 검정을 수행할 때 발생하는 다중 검정 문제를 보정한 값입니다.
DESeq2와 같은 통계적 방법에서 다중 가설 검정을 진행할 때 각 테스트마다 유의미한 결과를 얻을 확률이 커지기 때문에, 이 문제를 해결하기 위해 p-value를 보정해야 합니다.
False Discovery Rate (FDR) 보정 방법을 사용하여 계산된 값으로, FDR 보정은 전체 테스트에서 거짓 양성(즉, 실제로 차이가 없는데도 통계적으로 유의하다고 잘못 판단된 결과)을 줄이는 것을 목표로 합니다. 
DESeq2에서는 일반적으로 Benjamini-Hochberg 방법을 사용하여 FDR을 보정하게 됩니다.
즉, padj가 작을수록 해당 유전자의 차등 발현이 통계적으로 더 유의미하다는 것을 의미하며, 일반적으로 0.05 이하의 값을 유의한 차등 발현으로 해석합니다.
order(siFOXM1$padj)는 이러한 조정된 p-value(padj)를 기준으로 결과를 오름차순으로 정렬하여, 가장 유의미한 차등 발현 유전자가 먼저 나타나도록 한 내용입니다.
