---
title: "ChIP Sequencing"
tags:
    - omics
    - NGS
    - epigenetics
date: "2024-10-30"
thumbnail: "/assets/img/thumbnail/sample.png"
bookmark: true
---

# ChIP이란?
---

**Ch**romatin **I**mmuno**P**recipitation followed by sequencing (ChIP-Seq)으로 NGS 기술을 활용한 세포내 전사조절인자 규명을 위해 이용된다. 특정 단백질의 세포내 전자 조절인자로서의 기능을 알고자 할때 특정 단백질이 binding하는 DNA 서열을 NGS로 시퀀싱하여 유전체 전체에서 binding 가능한 motif 를 search 한다.

![ChIP1](https://media.cellsignal.com/www/images/resources/applications/chip-educational/chip-protocol-steps.png)
![ChIP2](https://www.merckmillipore.com/INTERSHOP/static/WFS/Merck-KR-Site/-/Merck/ko_KR/Freestyle/BI-Bioscience/Genomic-Analysis/Epigenetics/chip-chip-workflow.jpg)

DNA에 binding한 protein 혹은 histone modification을 검출하기 위한 분석방법

---

# ChIP Seq 간략한 소개
---

- ChIP Seq은 다음과 같은 단계로 진행
1. (Cross-linking) Tissue 혹은 cell에서 formaldehyde를 이용하여 target protein과 DNA간 결합을 고정시킨다.
2. Genomic DNA를 추출한 후, sonication등의 방법을 이용해 추출한 DNA를 조각낸다.
3. (IP)Target protein에 specific한 항체를 붙여 DNA와 protein이 결합한 부위, protein이 결합되지 않은 DNA를 분리한다.
4. (Reverse-cross linking) Cross-linking을 풀고 DNA를 정제하여 protein이 결합되어있던 부위의 시퀀싱을 진행한다.

- ChIP-seq 데이터를 분석하는 방법
1. Quality control : FastQC등의 툴을 사용하여 시퀀싱 리드들의 quality를 확인하고, TrimGalore, Trimmomatic등을 이용하여 시퀀싱에 사용된 adapter들과 시퀀싱 quality가 낮은 서열들을 제거한다
2. Alignment : Bowtie, BWA등의 툴을 사용하여 reference genome에 mapping을 진행한다. 이 때, 중복된 리드인 PCR duplicate와 mapping quality가 낮은 서열들을 제거해주는 과정이 필요하다
3. Peak calling : MACS, homer, SICER등의 툴을 사용하여 단백질이 결합된 DNA영역인 peak를 식별한다
4. Downstream analysis : Peak calling 이후, annotation, clustering, motif analysis등의 분석을 진행한다. 
    - Annotation은 peak과 관련된 유전자 정보, enhancer 정보 등을 분석한다. 
    - Motif analysis는 peak 내의 DNA서열을 이용하여 특이적인 motif 들을 찾아낸다.

# ChIP Seq 분석
---

### 시작하기 전 용어 정리
$threads -> threads
$genome -> reference fasta file(genome index)
$fq -> fastq file
$align_out -> unsorted.sam
$align_bam -> unsorted.bam
$align_sorted -> sorted.bam
$align_filtered -> rmdup.bam



### Trimming(QC)
[TrimGalore](https://github.com/FelixKrueger/TrimGalore)

```
$ trim_galore -j 4 \ #사용할 쓰레드 수 지정(hotspot = 4)
              --fastqc \ #quality check
              -o(--output_dir) /path/to/TrimGalore/output/directory \
              /path/to/forward/reads \
              /path/to/reverse/reads #if single end, only forward reads
```

### Mapping
[Bowtie2](https://bowtie-bio.sourceforge.net/bowtie2/index.shtml)

> Reference Genome 필요!
>
> fasta(염기서열) + gtf(유전자 주석정보)
[Reference Genome data Download](https://www.gencodegenes.org/)

> bowtie2-Build

```
$ bowtie2-build Desktop/ChIP-Seq/fasta/GRCh38.p13.genome.fa GRCh38
```

> bowtie2

```
bowtie2 -p $threads -q -x $genome -U $fq -S $align_out
```

example
```
$ bowtie2 -p 8 -x Desktop/ChIP-Seq/bowtie2/reference/GRCh38 file.fastq > file.sam
```

[samtools](https://www.htslib.org/)

```
$ samtools view -h -S -b -@ $threads -o $align_bam $align_out
```
example
```
$ samtools view -b -S file.sam > file.bam
```

### Sorting
[sambamba](https://github.com/biod/sambamba)
> Sort BAM file by genomic coordinates

```
sambamba sort -p -t $threads -o $align_sorted $align_bam
```
example
```
$ sambamba sort -p -t 8 -o file.sorted.bam file.bam
```

> Filter out duplicates

```
$ sambamba view -p -h -t $threads -f bam -F "[XS] == null and not unmapped and not duplicate" $align_sorted > $align_filtered
```

> Index BAM

```
$ samtools index file.rmdup.bam
```

### Peak calling
[HOMER](http://homer.ucsd.edu/homer/)

> [설치](https://www.notion.so/ChIP-Seq-7521d94f358d46b3a92a378b54edf18f?pvs=4#dad827534ef44fb6b7807f1557fdfa9b)

```
$ perl configureHomer.pl -install
```

> Creating tag directory

```
$ makeTagDirectory ${tagDirectory} ${filteredBAM}
```

> Peak calling - [HOMER](http://homer.ucsd.edu/homer/), [BEDtools](https://bedtools.readthedocs.io/en/latest/)

```
$ findPeaks ${tagDirectory} -style factor -o auto -i ${control_tagDirectory}
$ pos2bed.pl ${tagDirectory}/peaks.txt > ${output}.bed
```

> Replicates가 존재할 경우 각각의 tag directory와 peak calling을 진행, BEDtools를 이용하여 합치기

```
$ findPeaks ${tagDirectory_rep1} -style factor -o auto -i ${control_tagDirectory_rep1}
$ findPeaks ${tagDirectory_rep2} -style factor -o auto -i ${control_tagDirectory_rep2}
$ pos2bed.pl ${tagDirectory_rep1}/peaks.txt > ${rep1}.bed
$ pos2bed.pl ${tagDirectory_rep2}/peaks.txt > ${rep2}.bed
$ bedtools intersect -a ${rep1}.bed -b ${rep2}.bed > ${common}.bed
```


### Downstream Analysis
Peak visualization, Motif analysis, Annotating peaks, Peak clustering, Gene Ontology(GO), Heatmap and density plot

> Peak visualization - [deepTools](https://deeptools.readthedocs.io/en/develop/)

```
$ bamCoverage -b ${filteredBAM} -o ${output}.bw --binSize 20
```

> Motif analysis - HOMER

```
$ findMotifsGenome.pl ${common}.bed hg38 ${outputDirectory} -size 150 -mask
```

> Merging replicates tag directory - HOMER

```
$ makeTagDirectory ${tagDirectory_merged} -d ${tagDirectory_rep1} ${tagDirectory_rep2}
```

> Annotating peaks - HOMER

```
$ annotatePeaks.pl ${common}.bed hg38 -m ${top2_motif}.motif -d ${tagDirectory_merged} > ${annotation}.txt
```

> Peak clustering - HOMER

```
$ mergePeaks ${GM12878_common}.bed ${HepG2_common}.bed ${K562_common}.bed ${MCF7_common}.bed > all_merged.peaks
$ notatePeaks.pl all_merged.peaks hg38 -m ${top2_motif}.motif -d ${GM12878_merged_tagDir} ${HepG2_merged_tagDir} ${K562_merged_tagDir} ${MCF7_merged_tagDir} > merged_peaks_ann.txt 
```

> Gene Ontology(GO) - [metascape](https://metascape.org/gp/index.html#/main/step1)
Using top 500 binding sites sorted by the density of ChIP-seq reads

> Heatmap and density plot - deepTools

```
computeMatrix reference-point -S ${all bigwig files} -R ${clustered all peak files}.bed \ 
-a 2000 -b 2000 --referencePoint "center" --skipZeros --missingDataAsZero\
-o matrix_cluter.gz --outFileSortedRegions matrix_cluster.bed --outFileNameMatrix matrix_cluster.tab
```
```
plotHeatmap -m MCF-7.gz -o MCF-7_name.png \
--colorList "white, blue" --interpolationMethod "nearest" \
--whatToShow "heatmap and colorbar" --samplesLabel MCF-7 "Control" \
--regionsLabel MCF-7
```
```
plotProfile -m T47D.gz -o T47D_density.png --samplesLabel T47D "Control" \
--regionsLabel T47D --perGroup
```