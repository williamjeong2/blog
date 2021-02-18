---
title: "FastQ 란?"
date: 2021-02-16T18:25:31+09:00
draft: true
weight: 4
---
> FastQ 포맷에 대한 공식 문서는 [여기](http://maq.sourceforge.net/fastq.shtml)에서 볼 수 있습니다.

## FastQ format 무엇인가
**FastQ**(이하 fastq) 포맷은 시퀀스 분석에서 널리 사용되는 포맷입니다. 많은 분석 툴에서 FastA 보다 더 많은 정보를 포함하기 때문에 fastq 포맷을 요구합니다. 

fasta포맷과 유사한 포맷이지만 quality score같은  syntax가 다릅니다.

1. 첫 번째 라인은 `@`로 시작합니다. (`>`가 아님!)
   - 이하 자세한 내용은 아래에서..
2. 두 번째 라인은 시퀀스를 나타냅니다.
3. 세 번째 라인은 `+` 로 시작되며, 똑같은 시퀀스 identifier를 가지지만 더이상 사용하지 않습니다. 따라서 `+` 만 남습니다.
4. 네 번째 라인은 각 시퀀스에 대한 Quality score를 의미합니다.

첫 번째 라인의 fastq 시퀀스 identifier는 보통 각각의 포맷이 붙습니다. 즉, 첫 번째 라인 속 각각의 정보들이 시퀀스와 위치정보를 의미합니다.

```
@HW-ST911:111:C0N4WACSS:5:1101:2249:2216:1:N:0:TTAGGC CGATC:@@@FF
```

- HW-ST911
  - the unique instrument name
- 111
  - the run id
- C0N4WACSS
  - the flowcell id
- 5
  - flowcell lane
- 1101
  - tile number within the flowcell lane
- 2249
  - 'x'-coordinate of the cluster within the tile
- 2216
  - 'y'-coordinate of the cluster within the tile
- 1
  - the member of a pair, 1 or 2 (paired-end or mate-pair reads only)
- N
  - Y if the read is filtered, N otherwise
- 0
  - 0 when none of the control bits are on
- TTAGGC CGATC
  - index sequence

## FastQ 포맷을 사용하는 소프트웨어?

대표적인 예는 아래와 같습니다.

- Aligners
  - Bowtie, Tophat2
- Assemblers
  - Velvet, Spades
- QC tools
  - Trimmomatic, FastQC

## FastQ 파일이 어떻게 만들어질까?

Sequencers는 FastQ 포맷을 표준으로 만들어집니다. 또한, 몇몇 다른 파일 포맷(BAM, SFF, HDF5)으로부터 만들어지기도 하지만 어떤 시점에서는 모두 fastq 형태였습니다.

---
##  Reference

- https://learn.gencore.bio.nyu.edu/ngs-file-formats/fastq-format/

