---
title: "RNA Sequencing"
tags:
    - omics
    - NGS
    - transcriptome
date: "2024-10-29"
thumbnail: "https://i.ibb.co/xj20N3N/card.webp"
bookmark: true
---

오늘은 RNA sequencing에 대해 알아봅시다.
제가 생각할 때에는 bulk RNA seq이 bioinformatics를 입문자에게는 가장 적합하지 않을까? 생각을 했습니다.
RNA-seq의 경우 GEO에 넘쳐나는데 이 데이터를 어떻게 분석을 해야하나 힘드셨을 겁니다.
또한 NGS를 진행했는데 raw data만 받아서 파일을 사용하지도 못하고 있는 분도 계실겁니다.
리눅스를 다룰 줄 모르거나 적절한 사양의 서버가 없어서 등 다양한 이유가 있을텐데 오늘 제가 알아볼 내용은 리눅스 운영체제의 컴퓨터가 필요해요 ㅠㅠ
다음번에는 리눅스 명령어 없이, 서버 없이 RNA-seq 데이터를 raw-data 부터 분석하는 법을 말씀드릴 겁니다!

# GEO datasets
---

## 그럼 분석을 시작해볼까요?

가장 먼저 해야할 일은 데이터를 얻는 방법입니다.
원하시는 GEO series 페이지에 들어가시면, 페이지의 아래쪽에서 SRP 정보를 찾으실 수 있습니다.
![geo](https://github.com/user-attachments/assets/f07957b2-0892-4051-981a-1dd017aa69e2)

```
MYBL2 발현이 폐 선암종 세포에서 transcriptional activator 역할
MYBL2와 FOXM1 → 프로모토에 결합하여 세포 주기 조절
FOXM1의 inhibitor인 FDI-6(forkhead domain inhibitor 6) → 
MYBL2, FOXM1 활성 억제, 세포 사멸 및 세포주기 유전자 조절하여 lung cancer cell 증식을 억제

MYBL2와 FOXM1이 세포 주기 유전자를 함께 활성화하여 폐 선암종 세포에서 발암성 전사 인자로 작용하고 이들이 질병에 대한 잠재적인 치료 표적임을 시사
```
<html>
<body>
<!--StartFragment-->
SampleName | Group | SRR number
-- | -- | --
siControl rep1 | control | SRR18885343
siControl rep2 | control | SRR18885342
siControl rep3 | control | SRR18885341
siControl rep4 | control | SRR18885340
siFOXM1 rep1 | test | SRR18885347
siFOXM1 rep2 | test | SRR18885346
siFOXM1 rep3 | test | SRR18885345
siFOXM1 rep4 | test | SRR18885344
siMYBL2 rep1 | test2 | SRR18885351
siMYBL2 rep2 | test2 | SRR18885350
siMYBL2 rep3 | test2 | SRR18885349
siMYBL2 rep4 | test2 | SRR18885348

<!--EndFragment-->
</body>
</html>


## Download Data

1. Gene Expression Omnibus 에서 GEO DataSets 키워드 검색 
2. Study type을 Expression profiling by high throughput sequencing으로 설정
3. 분석하고자 하는 연구 클릭
4. Organism 확인 (human or mouse or...)
5. 하단 samples의 GSM샘플 클릭
6. 하단 Relations의 SRA클릭
7. RNAseq, transcriptomic, cDNA, 분석장비, paired or single 확인
8. SRR number 확인 → [fastq-dump manual](https://github.com/ncbi/sra-tools/wiki/HowTo:-fasterq-dump)를 사용하여 다운로드


fastq-dump설치 과정은 위 사이트를 참고해주세요!
```
$ fastq-dump --gzip \ #gz압축파일로 다운로드
						 --split-files \ #for paired-end studies
						 SRR<number>

# or can use fasterq-dump(fasterq-dump is the faster one)

$ fasterq-dump -p \
               --split-3 \ # for both single and paired-end
               SRR<number>
```

```
fasterq-dump -p --split-3 SRR18885340 ~ SRR18885351
```

## Data Preprocessing

FASTQ raw reads → [TrimGalore](https://github.com/FelixKrueger/TrimGalore/blob/master/Docs/Trim_Galore_User_Guide.md) → FASTQ [trimmed](https://incodom.kr/Trimming)

NGS 분석의 pre-processing 단계에서, contamination 되거나 낮은 퀄리티의 데이터를 제거하는 작업을 말한다. 즉, 시퀀싱한 raw 데이터는 trimming 과정을 거쳐 clean 데이터로 assembly나 alignment (mapping)을 수행할 수 있다.

```
$ trim_galore -j 4 \ #사용할 쓰레드 수 지정(hotspot = 4)
              --fastqc \ #quality check
              --paired \ #for paired end studies
              -o(--output_dir) /path/to/TrimGalore/output/directory \
              /path/to/forward/reads \
              /path/to/reverse/reads #if single end, only forward reads
```
Input data files (FASTQ raw)

- *fastq.gz (raw reads)

Output data files (FASTQ trimmed)

- *fastq.gz (trimmed reads)
- *trimming_report.txt (trimming report)


Make sure `Allow adding visitor counts on your website` is checked:

![](https://i.ibb.co/R7TKCmy/2024-01-13-043651.png){:class="img-md"}

