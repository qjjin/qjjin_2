---
title: "Bioinformatics"
tags:
    - omics
    - NGS


date: "2024-10-08"
thumbnail: "/assets/img/thumbnail/sample.png"
bookmark: true
---

놀고먹은 대학원생의 전공지식..ㅎ

# Bioinformatics
---
**Bioinformatics** is an interdisciplinary field that develops methods and software tools for understanding **biological data**, in particular when the data sets are large and complex. As an interdisciplinary field of science, bioinformatics combines **biology**, **computer science**, **information engineering**, **mathematics and statistics** to analyze and interpret the **biological data**. Bioinformatics has been used for *in silico* analyses of biological queries using **mathematical and statistical techniques**. 

**생물정보학은 생물학, 컴퓨터 과학, 정보 공학, 수학 및 통계학을 결합하여 생물학적 데이터를 분석하고 해석하는 학제 간 과학 분야이다.**
생물정보학은 **수학적** 및 **통계적** 기법을 사용하여 *in silco* 분석에 사용

![Novaseq](https://media.wired.com/photos/633459a4036b54662b51064e/master/w_1920,c_limit/Wide-Front-Apollo-01_science.jpg)
The human genome is made of more than 6 billion letters, and each person has a unique configuration of A, C, G, and T the molecular building blocks that make up DNA. Determining the sequence of all those letters used to take vast amounts of money, time, and effort.

Illumina’s sequencers use a method called “sequencing by synthesis” to decipher DNA. This process first requires that DNA strands, which are usually in double-helix form, be split into single strands. The DNA is then broken into short fragments that are spread onto a flow cell—a glass surface about the size of a smartphone. When a flow cell is loaded into the sequencer, the machine attaches color-coded fluorescent tags to each base: A, C, G, and T. For instance, blue might correspond to the letter A. Each of the DNA fragments gets copied one base at a time, and a matching strand of DNA is gradually made, or synthesized. A laser scans the bases one by one while a camera records the color coding for each letter. The process is repeated until every fragment is sequenced.

![NOvaseq1](/assets/img/novaseq1.png)
이러한 과정을 통해서 대용량의 데이터를 2~3일 이내로 컴퓨터의 데이터로 변환되어 확인이 가능하다.

# 파일 형식(FASTA, FASTQ, BAM, VCF)
---
Bioinformatics를 공부하면서 가장 많이 접하는 파일 형식으로는 .csv 일거라고 생각합니다.
.csv파일을 열어보면 흔히 excel에서 열 수 있는 표 형태의 파일일 것 입니다!
이는 텍스트 파일로 tsv는 tab으로 구분이 되는 tab separated value의 줄임말, csv는 comma로 구분이 되는 comma separated value의 줄임말입니다.
![csv](/assets/img/csv.png)
*csv 파일 형식*

이렇듯 직관적으로 누구나 쉽게 데이터를 만질 수 있는 형태로 되어 있다면 bioinformatics라는 새로운 학문으로 만들어지지 않았겠죠? ㅠㅠ

NGS를 막 돌리고 나온 데이터의 파일 형식은 .fastq 일 것 입니다(압축 유무에 따라 **.gz**).

**FASTA**

FASTA파일은 sequnce에 대한 정보를 담고 있는 파일 형식입니다. 
DNA sequence 뿐만 아니라 Protein sequence도 저장합니다.
아래와 같이 다양한 서로 다른 파일 형식을 갖습니다.
generic FASTA의 의미인 .fasta, .fa
nucleic acid FASTA의 의미인 .fna
non-coding RNA FASTA의 의미인 .frn

파일의 형태는 두가지의 반복되는 형식으로 이름/서열 입니다.
![fasta](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbqwXtz%2Fbtq7deEZ8l5%2F7gHWosQCgKYwDNF2XVDVsk%2Fimg.png)
파일의 처음은 >(꺽쇠; Header)로 시작하고 그 뒤로 서열(sequence)이 나오게 됩니다.

**FASTQ**

FASTA+Quality, 즉 FASTA 파일의 정보 (header+sequence)와 함께 quality 정보를 함께 담는 파일의 형식입니다.
![fastq](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbl1Bh1%2Fbtq67RX5nyR%2F10OnQYj7rXYpl1BVEkD1z0%2Fimg.png)

Line 1: Header
Line 2: Sequence
Line 3: '+' character(sequence ID를 넣거나 설명을 넣는 부분)
Line 4: Quality values (Phred score) with ASCII characters(각 염기서열이 얼마나 정확히 읽혔는지를 Phred quality score로 나타낸 값)

![phred score](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbQFkIO%2FbtqL1plFgoe%2F2LYGBGb8j2Iv6tjvmWHYpK%2Fimg.png)

**SAM/BAM**
Sequence Alignment Map (**SAM**)은 alignment(흔히 mapping)정보를 담는 파일 형식 (read(fastq)들이 reference genome의 어디에 mapping(align)되는지, quality scores는 얼마나 되는지)이다.
Binary Alignment Map (**BAM**)은 SAM 파일의 이진형태로, 컴퓨터의 언어인 0,1로 변환된다고 보시면 됩니다. 
컴퓨터에서는 굳이 사람이 볼 수 있는 형태의 sam형식보다는 데이터의 크기가 작은 bam파일을 이후 과정에 사용하는게 시간적으로 절약이 되겠죠?
samtools tool을 이용하여 서로 변환도 쉽게 진행할 수 있습니다.
 

**VCF**
Variant Call Format의 줄임말로, 유전체 변이정보를 담은 텍스트 파일입니다.
주로 WGS, WES의 genomic 분석에서 자주 다루는 파일 포맷입니다.
---

오늘은 이만 여기까지만 작성해보고 다음 작성에는 RNA sequencing workflow부터 제가 주로 사용했던 pipeline에 대해 작성해보겠습니다.
```