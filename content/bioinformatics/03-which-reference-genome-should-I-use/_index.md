---
title: "어떤 reference genome을 사용해야 할까?"
date: 2021-02-17T17:59:31+09:00
draft: false
weight: 3
---

### 들어가기 전에

- 이 포스팅은 [Tutorial: Which human reference genome should I use?](https://www.biostars.org/p/342482/) 에 있는 포스팅을 번역한 내용이 포함되어있습니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후에 정정하겠습니다.
- Original source of this posting is form [Tutorial: Which human reference genome should I use?](https://www.biostars.org/p/342482/) If the original author requests deletion, it will be deleted immediately.

---

"어떤 reference genome을 선택해야 하는가?"는 biostas에서 나오는 상위권 질문들중 하나입니다. 이 질문에 대한 답은 꽤 쉬워보입니다: 가장 최신의 major 버전(GRCh38)을 가져오는 것입니다.([Genome Reference Consortium](https://www.ncbi.nlm.nih.gov/grc/human))

그러나 genome 파일을 다운로드 하러가기 전에 다음 질문들이 명확해야 합니다:

1. 이전 버전을 사용해야 하는 이유는 무엇인가?
2. reference fasta 에는 어떤 서열정보가 포함되어 있는가?
3. 최신 마이너 버전의 사용은 어떤가?
4. 정렬(alignment)을 위해 reference fasta를 준비하려면 어떤 단계들이 필요한가?

## 1. 이전 버전을 사용해야 하는 이유는 무엇인가?

시작하기 전에 실험에 사용할 데이터베이스를 확인합니다. 최근 assembly에 대한 데이터 접근이 가능한지? 해당 데이터를 아직 사용할 수 없는 경우 데이터를 최신 버전으로 옮기려면 얼마나 큰 노력을 기울여야 하는지?

## 2. reference fasta 에는 어떤 서열정보가 포함되어 있는가?

최신 메이저 릴리즈 파일은 다음과 같습니다:
[ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/000/001/405/GCA_000001405.28_GRCh38.p13](ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/000/001/405/GCA_000001405.28_GRCh38.p13) (<u>2020/01/14 기준</u>)

서열은 `GCA_000001405.28_GRCh38_genomic.fna.gz` 에 있습니다.

이 파일은 다음의 서열정보를 포함합니다.

1. chromosomes 1-22, X, Y
2. sequences that can be asigned to one chromosome, but not to an exact position or orientation (**unlocalized sequences**)
3. sequences that cannot be assigned to any chromosome (**unplaced sequences**)
4. the mitochondrial genome
5. sequences that provides an alternate representation of a locus (**alernate locus**)

(1)-(3) build together the so-called **Primary Assembly**.

## 3. 최신 마이너 버전의 사용은 어떤가?

`.p<version-number>` 라고 나타나는 마이너 버전을 알 수 있을 것입니다. 최신 버전은 GRCh38.p13.입니다. (2020/01/14 기준)  그런데 제가 왜 이 버전의 링크를 제공하지 않았을까요?

마이너 버전의 서열들은 메이저 버전에서 untouch 되었습니다. 대신에 **패치**라고 불리는 서열정보가 추가되었습니다(여기서 `p`가 유래됨.). 이런 패치는 기존 서열의 수정일 수 있으며 다음 메이저 릴리즈 버전의 기본 assembly로 통합될 것입니다. 또는 다음 메이저 릴리즈에서 "alternate loci"라고 하는 새로운 서열을 나타내기도 합니다.

이 [FAQ](https://www.ncbi.nlm.nih.gov/grc/help/patches) 에서 패치에 대한 자세한 내용을 읽을 수 있습니다.

따라서 우리가 특별한 이유가 있지 않은 한, 우리는 정렬(alignment)를 위해 primary assembly 및 mitochondrial genome의 서열을 사용해야 합니다. 그 이유는 다음 챕터에서 말해드리겠습니다.

## 4. 정렬(alignment)을 위해 reference 패스트를 준비하려면 어떤 단계들이 필요한가?

>  이 부분에 설명하는 대부분의 내용들은 [Heng Li](http://lh3.github.io/2017/11/13/which-human-reference-genome-to-use) 의 블로그 포스트에서 영감을 받았습니다. (Heng Li는 우리가 잘 알고있는 `bwa` 와 `samtools` 의 개발자입니다.)

위에 링크된 reference sequence는 다음 세 가지 이유로 정렬(alignment)에 사용할 준비가 되어있지 않습니다:

1. loci의 alternate represnetation이 포함됩니다. 이것들은 primary assembly와 매우 유사한데, 그렇기 때문에 대부분의 정렬(alignment)에서는 reads 위치를 알지 못하고 mapping 품질이 매우 낮습니다(variant calling에는 좋지 않다는 걸 의미합니다.).
2. 염색체 Y에는 유사 염색체 X에 위치한 영역의 복제인 pseudo-autosomal region(PAR)이 있습니다. 이것 또한 정렬(alignment)에서 read의 위치를 알지 못합니다.
3. 서열들이 `1` 이라는 이름 대신에 `CM000663.2` 라는 GeneBank Accesion Numbers 를 가지고 있습니다.

(1)에서 보여지는 불분명한 mapping은 (2)에서 패치 서열에서도 유효합니다. 이건 우리가 정렬(alignment)을 할 때 최신 마이너 버전을 사용할 필요가 없는 이유입니다.

그래서 우리는 이렇게 해야합니다:

1. GRC로부터 제공되는 서열들의 서브셋을 구축
2. 염색체 Y의 PAR을 표시
3. 서열 이름 바꾸기

이러한 일들을 하기 위해서 우리는 몇가지 파일들이 필요합니다:

- 서브셋을 구축하고 서열 이름을 바꾸기 위해
  [GCA_000001405.28_GRCh38.p13_assembly_report.txt](ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/000/001/405/GCA_000001405.28_GRCh38.p13/GCA_000001405.28_GRCh38.p13_assembly_report.txt) 
- PAR을 표시하기 위해
  [par_align.gff](ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/000/001/405/GCA_000001405.28_GRCh38.p13/GCA_000001405.28_GRCh38.p13_assembly_structure/Primary_Assembly/pseudoautosomal_region/par_align.gff)

### GRC로부터 제공되는 서열들의 서브셋 구축

[reference sequence](ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/000/001/405/GCA_000001405.28_GRCh38.p13/GCA_000001405.28_GRCh38.p13_genomic.fna.gz) 를 다운로드 한 후에 압축을 풀고 `samtools faidx` 로 인덱스한다:

```bash
$ samtools faidx GCA_000001405.28_GRCh38.p13_genomic.fna
```

`GCA_000001405.28_GRCh38.p13_assembly_report.txt` 에서 primary assembly들과 mitochondrial genome의 이름들을 추출해야 합니다. 많은 bioinformatic tool들이 서열들이 정리되었다 여깁니다. 따라서 현재 이름을 나중에 사용하려는 서열 이름으로 정렬해야 합니다.

```bash
$ sort -k1,1V GCA_000001405.28_GRCh38.p13_assembly_report.txt|awk -v FS="\t" '$8 == "Primary Assembly" || $8 == "non-nuclear" {print $5}' > subset_ids.txt
```

이 목록을 사용하여 서열의 서브셋을 구축할 수 있습니다.

### 염색체 Y의 PAR을 표시

`par_align.gff` 에는 PAR이 X염색체에 있고 그것에 맞먹는 것들이 Y 염색체에 있다는 정보가 들어있습니다. 파일을 열고 각 라인에서 `Target` 키워드를 찾아보세요. 값은 우리가 마스크하고 싶은 Y 염색체의 위치입니다. 이렇게 하려면 이러한 값을 가진 `bed` 파일을 우리가 만들어야 합니다(**주의** : `gff` 는 1-based position이며 `bed` 는 0-based입니다).
우리는 이렇게 하면 됩니다.

```bash
$ cat parY.bed
CM000686.2  10000   2781479
CM000686.2  56887902    57217415
```

`bed` 파일을 가져오는 한 줄짜리 코드:

```bash
$ sed -E 's/.*Target=([^;]+).*/\1/g' par_align.gff|awk -v OFS="\t" '$0 !~ "^#" {print $1, $2-1, $3}'  > parY.bed
```

`bedtools` 를 가지고 표시할 수도 있습니다:

```bash
$ bedtools maskfasta -fi GRCh38_subset.fa -bed parY.bed -fo GRCh38_subset_masked.fa
```

### 서열 이름 바꾸기

최근에는 각각의 서열들의 헤더는 다음과 같습니다:

```bash
> CM000663.2
```

우리가 좋아하는 것은 이렇게 하는 겁니다:

```bash
>1 CM000663.2 NC_000001.11 chr1
```

첫 공백까지 오는 건 모두 ID가 됩니다. 그 뒤에 오는건 정보가 됩니다.

```bash
$ awk -v FS="\t" 'NR==FNR {header[">"$5] = ">"$1" "$5" "$7" "$10; next} $0 ~ "^>" {$0 = header[$0]}1' GCA_000001405.28_GRCh38.p13_assembly_report.txt GRCh38_subset_masked.fa > GRCh38_alignment.fa
```

### 인덱싱

이제 `GRCh38_alignment.fa` 는 정렬에 사용할 준비가 되었습니다. 다른 tool들이 필요로 하기 때문에, 저는 이 파일을 `samtools faidx` 로 인덱스 하기를 추천합니다. 또한 사용하려는 alignment tool에 설명된 방식으로 인덱싱 해야합니다.
예를 들어 `bwa` 라면 : `bwa index GRCh38_alignment.ga`

---

