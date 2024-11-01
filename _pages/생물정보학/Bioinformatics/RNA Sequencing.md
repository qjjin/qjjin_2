---
title: "RNA Sequencing"
tags:
    - omics
    - NGS
    - transcriptome
date: "2024-10-29"
thumbnail: "/assets/img/thumbnail/Summary_of_RNA-Seq.png"
bookmark: true
---

오늘은 RNA sequencing에 대해 알아봅시다.
제가 생각할 때에는 bulk RNA seq이 bioinformatics를 입문자에게는 가장 적합하지 않을까? 생각을 했습니다.
RNA-seq의 경우 GEO에 넘쳐나는데 이 데이터를 어떻게 분석을 해야하나 힘드셨을 겁니다.
또한 NGS를 진행했는데 raw data만 받아서 파일을 사용하지도 못하고 있는 분도 계실겁니다.
리눅스를 다룰 줄 모르거나 적절한 사양의 서버가 없어서 등 다양한 이유가 있을텐데 오늘 제가 알아볼 내용은 리눅스 운영체제의 컴퓨터가 필요해요 ㅠㅠ
다음번에는 리눅스 명령어 없이, 서버 없이 RNA-seq 데이터를 raw-data 부터 분석하는 법을 말씀드릴 겁니다! 
---

## GEO datasets

가장 먼저 해야할 일은 데이터를 얻는 방법입니다.
원하시는 GEO series 페이지에 들어가시면, 페이지의 아래쪽에서 SRP 정보를 찾으실 수 있습니다.
![geo](https://github.com/user-attachments/assets/f07957b2-0892-4051-981a-1dd017aa69e2)

[GSE201272](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE201272)
NCBI 사이트 첨부했습니다. 데이터에 대한 내용 확인하시면 됩니다.

<table>
  <thead>
    <tr>
      <th>SampleName</th>
      <th>Group</th>
      <th>SRR number</th>
    </tr>
  </thead>
  <tbody>
    <tr style="background-color: #f0f8ff;">
      <td>siControl rep1</td>
      <td>control</td>
      <td>SRR18885343</td>
    </tr>
    <tr style="background-color: #f0f8ff;">
      <td>siControl rep2</td>
      <td>control</td>
      <td>SRR18885342</td>
    </tr>
    <tr style="background-color: #f0f8ff;">
      <td>siControl rep3</td>
      <td>control</td>
      <td>SRR18885341</td>
    </tr>
    <tr style="background-color: #f0f8ff;">
      <td>siControl rep4</td>
      <td>control</td>
      <td>SRR18885340</td>
    </tr>
    <tr style="background-color: #ffe4e1;">
      <td>siFOXM1 rep1</td>
      <td>test</td>
      <td>SRR18885347</td>
    </tr>
    <tr style="background-color: #ffe4e1;">
      <td>siFOXM1 rep2</td>
      <td>test</td>
      <td>SRR18885346</td>
    </tr>
    <tr style="background-color: #ffe4e1;">
      <td>siFOXM1 rep3</td>
      <td>test</td>
      <td>SRR18885345</td>
    </tr>
    <tr style="background-color: #ffe4e1;">
      <td>siFOXM1 rep4</td>
      <td>test</td>
      <td>SRR18885344</td>
    </tr>
    <tr style="background-color: #f5f5dc;">
      <td>siMYBL2 rep1</td>
      <td>test2</td>
      <td>SRR18885351</td>
    </tr>
    <tr style="background-color: #f5f5dc;">
      <td>siMYBL2 rep2</td>
      <td>test2</td>
      <td>SRR18885350</td>
    </tr>
    <tr style="background-color: #f5f5dc;">
      <td>siMYBL2 rep3</td>
      <td>test2</td>
      <td>SRR18885349</td>
    </tr>
    <tr style="background-color: #f5f5dc;">
      <td>siMYBL2 rep4</td>
      <td>test2</td>
      <td>SRR18885348</td>
    </tr>
  </tbody>
</table>


위 표는 RNA-seq 데이터를 정리한 표입니다

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

**Example**
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

**파일의 용량이 큽니다.** 
혹시나 저장공간이 부족하다하면 Control 파일 3개 / FOXM1 3개 혹은 MYBL2 3개 이런식으로 다운받아주세요.

**Control 3 / FOXM1 3**
**Control 3 / MYBL2 3**

이런식으로 다운받으시면 되겠죠?
3개의 파일을 다운받는 이유는 통계적 신뢰성을 확보하기 위함입니다.

자 마지막으로!! 이 파일은 **Paired-end** 파일입니다. 그래서 **SRR1888534X**가 **SRR1888534X_1.fastq**, **SRR1888534X_2.fastq** 이렇게 두개의 파일이 다운로드 됩니다!

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
Input data files -> *fastq.gz (raw reads)
Output data files ->  *fastq.gz (trimmed reads), *trimming_report.txt (trimming report)

<details>
<summary>하나하나 치기 귀찮을 때</summary>
<div markdown="1">

```
# TrimGalore
ls Desktop/RNA-seq/lung/ |grep _1.fastq | sed 's/_1.fastq//g' | while read line
> do
> trim_galore -j 4 --fastqc --paired -o Desktop/RNA-seq/output/TrimGalore/ Desktop/RNA-seq/lung/$line"_1.fastq" Desktop/RNA-seq/lung/$line"_2.fastq"
> done
```
while 함수를 이용한 반복문을 만든 것으로 반복문을 사용하실 때 **매우 중요한 사항**으로는 **Directory 입력**이 틀리면 안됩니다!!

저는 SRR파일을 Desktop에 RNA-seq 폴더가 있구요. 이 RNA-seq 폴더 안에 lung 폴더가 또 있는 구조로 lung폴더에 **SRR1888534X_1.fastq**, **SRR1888534X_2.fastq**가 있는거에요!

</div>
</details>



## Data Mapping

1. Download FASTA, GTF file 
   - [GENCODE](https://www.gencodegenes.org/)에서 **human** **mouse** 해당하는 항목 클릭
   - GTF file은 main annotation file 다운로드 (직접 다운로드가 안될 시 터미널에서 `wget` 이용)
   - FASTA file은 Genome sequence file 다운로드 (직접 다운로드가 안될 시 터미널에서 `wget` 이용)

2. Build STAR Reference  
   - FASTA + GTF ⇒ STAR reference

```bash
$ STAR --runThreadN 24 \ #사용할 쓰레드 수 지정
       --runMode genomeGenerate \ 
       --genomeDir /path/to/STAR/genome/directory \ #output directory 지정
       --genomeFastaFiles /path/to/genome/fasta/file \ #FASTA file directory 입력
       --sjdbGTFfile /path/to/annotation/gtf/file  #GTF file directory 입력
```

| **Input data files** | **Description**                   |
|----------------------|-----------------------------------|
| .fasta or .fa        | Genome sequences                 |
| .gtf                 | Genome annotation                |

| **Output data files**     | **Description**                   |
|---------------------------|-----------------------------------|
| chrLength.txt             |                                   |
| chrNameLength.txt         |                                   |
| chrName.txt               |                                   |
| chrStart.txt              |                                   |
| exonGeTrInfo.tab          |                                   |
| exonInfo.tab              |                                   |
| geneInfo.tab              |                                   |
| Genome                    |                                   |
| genomeParameters.txt      |                                   |
| SA                        |                                   |
| SAindex                   |                                   |
| sjdbInfo.txt              |                                   |
| sjdbList.fromGTF.out.tab  |                                   |
| sjdbList.out.tab          |                                   |
| transcriptInfo.tab        |                                   |


3. STAR Map Reads  
   - STAR reference + FASTQ trimmed → STAR ⇒ BAM

```bash
$ STAR --genomeDir /path/to/STAR/genome/directory \ #STAR reference directory 입력
       --alignMatesGapMax 1000000 \ # only needed for PE studies
       --outFilterType BySJout \ 
       --readFilesCommand zcat \ #gz압축파일을 input하기 때문
       --runThreadN 8 \ #사용할 쓰레드 수 지정
       --outSAMtype BAM SortedByCoordinate \ 
       --quantMode TranscriptomeSAM \ #gene 단위가 아닌 transcriptome 단위로 받음. 이 결과로 나온 파일을 다음 단계인 RSEM에 사용
       --outFileNamePrefix /path/to/STAR-output/directory/<sample_name> \ #output 경로 지정
       --readFilesIn /path/to/trimmed_forward_reads \ #FASTQ trimmed file directory 입력
       /path/to/trimmed_reverse_reads  #only needed for PE studies
```

| **Input data files**         | **Description**                     |
|------------------------------|-------------------------------------|
| STAR index directory         | STAR reference directory           |
| *fastq.gz (trimmed reads)    | Trimmed reads in FASTQ format      |

| **Output data files**                     | **Description**                             |
|-------------------------------------------|---------------------------------------------|
| *Aligned.sortedByCoord.out.bam            | Sorted mapping to genome                   |
| *Aligned.toTranscriptome.out.bam          | Sorted mapping to transcriptome            |
| *Log.final.out                            | Read mapping summary                       |
| *Log.out                                  | STAR log file                              |
| *Log.progress.out                         | STAR progress log                          |
| *SJ.out.tab                               | Splice junction information                |


## PreRead Counting

1. [samtools](https://www.htslib.org/)

   ```bash
   samtools index Desktop/data/output/STAR/SRR23085774.bam
   ```

2. [RSeQC](https://rseqc.sourceforge.net/)

   ```bash
   infer_experiment.py
   ```

RNA-seq 시퀀싱이 가닥 특이적(stranded)인지 비가닥 특이적(non-stranded)인지 파악하기 위해 "읽기의 가닥(strandness of reads)"과 "전사체의 가닥(strandness of transcripts)"을 비교하는 프로그램입니다.

"strandness of reads": RNA-seq 데이터의 읽기(reads)가 어느 가닥에 매핑되었는지 정렬(alignment) 과정에서 확인합니다.
"strandness of transcripts": 주석 파일(annotation file)을 통해 전사체가 어느 가닥에 위치하는지 파악합니다.

이 프로그램은 RNA 시퀀싱 프로토콜의 정보를 사전에 알 필요 없이, RNA 시퀀싱 읽기(reads)를 비가닥 특이적인 것처럼 mapping한 후 가닥 정보를 추정할 수 있습니다.

**For pair-end RNA-seq, there are two different ways to strand reads (such as Illumina ScriptSeq protocol):**

1++,1–,2+-,2-+

read1 mapped to ‘+’ strand indicates parental gene on ‘+’ strand

read1 mapped to ‘-’ strand indicates parental gene on ‘-’ strand

read2 mapped to ‘+’ strand indicates parental gene on ‘-’ strand

read2 mapped to ‘-’ strand indicates parental gene on ‘+’ strand

1+-,1-+,2++,2–

read1 mapped to ‘+’ strand indicates parental gene on ‘-’ strand

read1 mapped to ‘-’ strand indicates parental gene on ‘+’ strand

read2 mapped to ‘+’ strand indicates parental gene on ‘+’ strand

read2 mapped to ‘-’ strand indicates parental gene on ‘-’ strand

**For single-end RNA-seq, there are also two different ways to strand reads:**

++,–

read mapped to ‘+’ strand indicates parental gene on ‘+’ strand

read mapped to ‘-’ strand indicates parental gene on ‘-’ strand

+-,-+

read mapped to ‘+’ strand indicates parental gene on ‘-’ strand

read mapped to ‘-’ strand indicates parental gene on ‘+’ strand

**Example 1 : Pair-end non strand specific**

```
infer_experiment.py -r hg19.refseq.bed12 -i Pairend_nonStrandSpecific_36mer_Human_hg19.bam

#Output::

This is PairEnd Data
Fraction of reads failed to determine: 0.0172
Fraction of reads explained by "1++,1--,2+-,2-+": 0.4903
Fraction of reads explained by "1+-,1-+,2++,2--": 0.4925
```
Interpretation: 1.72% of total reads were mapped to genome regions that we cannot determine the “standness of transcripts” (such as regions that having both strands transcribed). For the remaining 98.28% (1 - 0.0172 = 0.9828) of reads, half can be explained by “1++,1–,2+-,2-+”, while the other half can be explained by “1+-,1-+,2++,2–“. We conclude that this is NOT a strand specific dataset because “strandness of reads” was independent of “standness of transcripts”

**Example 2 : Pair-end strand specific**

```
infer_experiment.py -r hg19.refseq.bed12 -i Pairend_StrandSpecific_51mer_Human_hg19.bam

#Output::

This is PairEnd Data
Fraction of reads failed to determine: 0.0072
Fraction of reads explained by "1++,1--,2+-,2-+": 0.9441
Fraction of reads explained by "1+-,1-+,2++,2--": 0.0487
```
Interpretation: 0.72% of total reads were mapped to genome regions that we cannot determine the “standness of transcripts” (such as regions that having both strands transcribed). For the remaining 99.28% (1 - 0.0072 = 0.9928) of reads, the vast majority was explained by “1++,1–,2+-,2-+”, suggesting a strand-specific dataset.


**Example 3 : Single-end strand specific**오전 10:34 2024-10-30

```
infer_experiment.py -r hg19.refseq.bed12 -i SingleEnd_StrandSpecific_36mer_Human_hg19.bam

#Output::

This is SingleEnd Data
Fraction of reads failed to determine: 0.0170
Fraction of reads explained by "++,--": 0.9669
Fraction of reads explained by "+-,-+": 0.0161
```
Interpretation: This is single-end, strand specific RNA-seq data. Strandness of reads are concordant with strandness of reference gene.

```
# infer_experiment(Strandness 판단)
infer_experiment.py -r Desktop/data/reference/hg38_GENCODE_V42_Comprehensive.bed -i Desktop/data/output/STAR/BT20-SW-1Aligned.sortedByCoord.out.bam

#This is PairEnd Data
Fraction of reads failed to determine: 0.0342
Fraction of reads explained by "1++,1--,2+-,2-+": 0.0151
Fraction of reads explained by "1+-,1-+,2++,2--": 0.9507
```

위 예시는 **1+-,1-+,2++,2--** 의 수치가  **1++,1--,2+-,2-+** 보다 큰 경우입니다.
이 경우는 Pair-end strand specific한 경우겠죠?
이렇게  infer_experiment.py의 정보가 필요한 이유는 이후 과정인 Read Quantification에서 StringTie라는 tool을 사용해야하기 떄문이죠.

StringTie의 옵션의 경우 

--rf	Assumes a stranded library fr-firststrand 
--fr	Assumes a stranded library fr-secondstrand.

두가지의 옵션에서 선택을 해줘야하기 때문이에요.
위 예시는 **1+-,1-+,2++,2--** 의 수치가  **1++,1--,2+-,2-+** 보다 큰 경우이기에 --rf 옵션을 선택해야겠죠?

## Read Quantification

1. [StringTie](https://ccb.jhu.edu/software/stringtie/)
[sample_list.txt](https://github.com/user-attachments/files/17597009/sample_list.txt)
위 파일을 확인하시고 list파일을 만들어 주셔야합니다.

```
# stringtie
stringtie -e -B --rf -G ~/Desktop/RNA-seq/GTF/gencode.v42.basic.annotation.gtf -p 20 -o 3.StringTie/sample.gtf 1.STAR/sample.sotredByCoord.out.bam

# prepDE(DESeq2 돌리기 전 data 정리)
prepDE.py3 -i sample_list.txt
# sample list
SRR18885340 /home/gyujin/Desktop/data/StringTie/SRR18885340Aligned.gtf
SRR18885341 /home/gyujin/Desktop/data/StringTie/SRR18885341Aligned.gtf
SRR18885342 /home/gyujin/Desktop/data/StringTie/SRR18885342Aligned.gtf
SRR18885343 /home/gyujin/Desktop/data/StringTie/SRR18885343Aligned.gtf
SRR18885344 /home/gyujin/Desktop/data/StringTie/SRR18885344Aligned.gtf
SRR18885345 /home/gyujin/Desktop/data/StringTie/SRR18885345Aligned.gtf
SRR18885346 /home/gyujin/Desktop/data/StringTie/SRR18885346Aligned.gtf
SRR18885347 /home/gyujin/Desktop/data/StringTie/SRR18885347Aligned.gtf
SRR18885348 /home/gyujin/Desktop/data/StringTie/SRR18885348Aligned.gtf
SRR18885349 /home/gyujin/Desktop/data/StringTie/SRR18885349Aligned.gtf
SRR18885350 /home/gyujin/Desktop/data/StringTie/SRR18885350Aligned.gtf
SRR18885351 /home/gyujin/Desktop/data/StringTie/SRR18885351Aligned.gtf

```
<details>
<summary>하나하나 치기 귀찮을 때</summary>
<div markdown="1">

```
# StringTie
ls Desktop/RNA-seq/output/BAM/ |grep .sorted |sed 's/.sortedByCoord.out.bam//g' |while read line; do stringtie -e -B -G ~/Desktop/RNA-seq/GTF/gencode.v42.basic.annotation.gtf -P 8 -o Desktop/RNA-seq/output/StringTie/$line'.gtf' Desktop/RNA-seq/output/BAM/$line'.sortedByCoord.out.bam'; done
```

</div>
</details>

[gene_count_matrix.csv](https://prod-files-secure.s3.us-west-2.amazonaws.com/5c61eb85-82c5-4041-84cc-50d85e7a95c0/953ef0e5-2f6f-4fb7-927f-1414a512e001/gene_count_matrix.csv)](https://prod-files-secure.s3.us-west-2.amazonaws.com/5c61eb85-82c5-4041-84cc-50d85e7a95c0/953ef0e5-2f6f-4fb7-927f-1414a512e001/gene_count_matrix.csv)

여기까지가 Read Quantification 과정이었으며, 이제 excel로 확인이 가능한 csv파일을 생성했습니다.
이제 우리는 이 csv파일을 가지고 DEG(Differential expressed gene) 과정을 진행할겁니다.
DEG부터는 리눅스 과정이 아닌 통계프로그램(R)을 이용하여 만들어볼 예정입니다~