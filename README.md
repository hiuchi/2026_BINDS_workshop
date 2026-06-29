# BINDS発現・機能解析インシリコ解析融合ユニット講習会

## 1. 概要
### 1.1 対象のデータ
- [Schneider, Kai Markus et al. Cell, Volume 186, Issue 13, 2823-2838.e20](https://doi.org/10.1016/j.cell.2023.05.001)
- 心理的ストレスを与えるために 7 日間拘束したマウスの結腸組織におけるバルク RNA-seq データです。control 群と stress 群がそれぞれ n=5 ずつ存在します。
### 1.2 講習内容
- 環境構築
- リファレンスファイルのダウンロード
- nf-core/rnaseq による定量
- nf-core/differentialabundance による発現変動解析
### 1.3 検証環境
- MacBook Air (M4, 13-inch, 2024)
  - CPU : 10コア
  - メモリ : 16GB
  - ストレージ : 256GB
  - OS : macOS Tahoe 26.0.1
- `/Users/binds/workshop`で解析を行います。

---

## 2. 環境構築
### 2.1 Docker のインストール
[公式サイト](https://www.docker.com)から公式ドキュメントに従って Docker Desktop をインストールします。
### 2.2 Homebrew のインストール
Homebrew は macOS 用のパッケージマネージャです。
[公式サイト](https://brew.sh)に記述されているコマンドを実行後、パスを通します。
  ```zsh
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
 ```
 ```zsh
  echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/binds/.zprofile
  eval "$(/opt/homebrew/bin/brew shellenv)"
  ```
### 2.3 Nextflow のインストール
Homebrew を使って Java と Nextflow をインストールします。
  ```zsh
  brew install openjdk@21
  brew install nextflow
  ```
---

## 3. リファレンスファイルのダウンロード
### 3.1 各データについて
#### 3.1.1 FASTQ ファイル
シーケンサーから出力される塩基配列とそのクオリティスコアが記述されたファイルです。
```
@SRR24350711.1 NB551353:21:HYMVTBGX9:1:11101:19311:1044 length=76
AACAANTCCAGCCCCCACTGCGTGTGGCGTTCCAGCACCTCAAACTGATCCCACAACTCGGTACCCCAATCCATGC
+SRR24350711.1 NB551353:21:HYMVTBGX9:1:11101:19311:1044 length=76
AAAA6#/EAE/EAEEA/EE/EEE/EEE<E<//A/E</EAEE///A/EE//EA//<//EEE<</<E<<<</6<AAAA
@SRR24350711.2 NB551353:21:HYMVTBGX9:1:11101:13087:1052 length=76
CTGTANAACACCTTCACCTTTAACATCTAGACATTCGCTTTTCTTCTGTGTTCTCCAGTGTTTACTGTAATCTCCC
+SRR24350711.2 NB551353:21:HYMVTBGX9:1:11101:13087:1052 length=76
AAAAA#EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEAEEEEEEEEEEEEEEEEEEAEEE<EAEEAEEEEEEAEA<<
```
#### 3.1.2 FASTA ファイル
対象生物の転写産物が記述されたファイルです。
```
>ENSMUST00000193812.2|ENSMUSG00000102693.2|OTTMUSG00000049935.1|OTTMUST00000127109.1|4933401J01Rik-201|4933401J01Rik|1070|TEC|
AAGGAAAGAGGATAACACTTGAAATGTAAATAAAGAAAATACCTAATAAAAATAAATAAA
AACATGCTTTCAAAGGAAATAAAAAGTTGGATTCAAAAATTTAACTTTTGCTCATTTGGT
ATAATCAAGGAAAAGACCTTTGCATATAAAATATATTTTGAATAAAATTCAGTGGAAGAA
TGGAATAGAAATATAAGTTTAATGCTAAGTATAAGTACCAGTAAAAGAATAATAAAAAGA
AATATAAGTTGGGTATACAGTTATTTGCCAGCACAAAGCCTTGGGTATGGTTCTTAGCAC
```
#### 3.1.3 GTF ファイル
遺伝子アノテーション情報が記述されたファイルです。
```
chr1	HAVANA	gene	3143476	3144545	.	+	.	gene_id "ENSMUSG00000102693.2"; gene_type "TEC"; gene_name "4933401J01Rik"; level 2; mgi_id "MGI:1918292"; havana_gene "OTTMUSG00000049935.1";
chr1	HAVANA	transcript	3143476	3144545	.	+	.	gene_id "ENSMUSG00000102693.2"; transcript_id "ENSMUST00000193812.2"; gene_type "TEC"; gene_name "4933401J01Rik"; transcript_type "TEC"; transcript_name "4933401J01Rik-201"; level 2; transcript_support_level "NA"; mgi_id "MGI:1918292"; tag "basic"; tag "Ensembl_canonical"; tag "GENCODE_Primary"; havana_gene "OTTMUSG00000049935.1"; havana_transcript "OTTMUST00000127109.1";
chr1	HAVANA	exon	3143476	3144545	.	+	.	gene_id "ENSMUSG00000102693.2"; transcript_id "ENSMUST00000193812.2"; gene_type "TEC"; gene_name "4933401J01Rik"; transcript_type "TEC"; transcript_name "4933401J01Rik-201"; exon_number 1; exon_id "ENSMUSE00001343744.2"; level 2; transcript_support_level "NA"; mgi_id "MGI:1918292"; tag "basic"; tag "Ensembl_canonical"; tag "GENCODE_Primary"; havana_gene "OTTMUSG00000049935.1"; havana_transcript "OTTMUST00000127109.1";
chr1	ENSEMBL	gene	3172239	3172348	.	+	.	gene_id "ENSMUSG00000064842.3"; gene_type "snRNA"; gene_name "Gm26206"; level 3; mgi_id "MGI:5455983";
chr1	ENSEMBL	transcript	3172239	3172348	.	+	.	gene_id "ENSMUSG00000064842.3"; transcript_id "ENSMUST00000082908.3"; gene_type "snRNA"; gene_name "Gm26206"; transcript_type "snRNA"; transcript_name "Gm26206-201"; level 3; transcript_support_level "NA"; mgi_id "MGI:5455983"; tag "basic"; tag "Ensembl_canonical"; tag "GENCODE_Primary";
```
### 3.2 各データのダウンロード（今回はスキップ）
#### 3.2.1 FASTQ ファイルのダウンロード
オンサイト講習会では、FASTQ ファイルは使用する Mac にあらかじめダウンロードしておきます。自分でダウンロードする場合は、[PRJNA963162](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE229320)のデータを ENA から`/Users/binds/workshop/fastq`にダウンロードします。

```zsh
brew install wget coreutils

mkdir -p /Users/binds/workshop/fastq

wget -O /Users/binds/workshop/fastq/PRJNA963162_ena_fastq.tsv \
  'https://www.ebi.ac.uk/ena/portal/api/filereport?accession=PRJNA963162&result=read_run&fields=run_accession,fastq_ftp,fastq_md5,fastq_bytes&format=tsv&download=true'

awk 'NR > 1 {print "https://"$2}' /Users/binds/workshop/fastq/PRJNA963162_ena_fastq.tsv \
  > /Users/binds/workshop/fastq/fastq_urls.txt

wget -P /Users/binds/workshop/fastq -i /Users/binds/workshop/fastq/fastq_urls.txt

awk 'NR > 1 {n = split($2, path, "/"); print $3 "  /Users/binds/workshop/fastq/" path[n]}' \
  /Users/binds/workshop/fastq/PRJNA963162_ena_fastq.tsv \
  > /Users/binds/workshop/fastq/MD5SUMS

gmd5sum -c /Users/binds/workshop/fastq/MD5SUMS
```

#### 3.2.2 リファレンスファイル（GRCm39, GENCODE Release M39）を [GENCODE](https://www.gencodegenes.org/mouse/) からダウンロード
  - GTF ファイル : Comprehensive gene annotation (All) を`/Users/binds/workshop/ref`にダウンロードします。
  - FASTA ファイル : Transcript sequences (ALL) を`/Users/binds/workshop/ref`にダウンロードします。
  - BED ファイル : GTF ファイルから`/Users/binds/workshop/ref`に作成します。

```zsh
mkdir -p /Users/binds/workshop/ref

wget -P /Users/binds/workshop/ref \
  https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_mouse/release_M39/gencode.vM39.chr_patch_hapl_scaff.annotation.gtf.gz

wget -P /Users/binds/workshop/ref \
  https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_mouse/release_M39/gencode.vM39.transcripts.fa.gz

nextflow pull nf-core/rnaseq -r 3.26.0

/usr/bin/perl /Users/binds/.nextflow/assets/nf-core/rnaseq/bin/gtf2bed \
  /Users/binds/workshop/ref/gencode.vM39.chr_patch_hapl_scaff.annotation.gtf.gz \
  > /Users/binds/workshop/ref/gencode.vM39.chr_patch_hapl_scaff.annotation.bed
```

---

## 4. nf-core/rnaseq による定量
### 4.1 サンプルシートの作成
サンプル名と FASTQ ファイルへのパスの対応を記述し、`/Users/binds/workshop/samplesheet_rnaseq.csv`として保存します。
```csv
sample,fastq_1,strandedness
stress1,/Users/binds/workshop/fastq/SRR24350715.fastq.gz,auto
stress2,/Users/binds/workshop/fastq/SRR24350714.fastq.gz,auto
stress3,/Users/binds/workshop/fastq/SRR24350713.fastq.gz,auto
stress4,/Users/binds/workshop/fastq/SRR24350712.fastq.gz,auto
stress5,/Users/binds/workshop/fastq/SRR24350711.fastq.gz,auto
control1,/Users/binds/workshop/fastq/SRR24350720.fastq.gz,auto
control2,/Users/binds/workshop/fastq/SRR24350719.fastq.gz,auto
control3,/Users/binds/workshop/fastq/SRR24350718.fastq.gz,auto
control4,/Users/binds/workshop/fastq/SRR24350717.fastq.gz,auto
control5,/Users/binds/workshop/fastq/SRR24350716.fastq.gz,auto
```
### 4.2 Salmon による定量
Salmon によって遺伝子産物の定量を行います。
```
nextflow run nf-core/rnaseq \
-r 3.26.0 \
-profile docker \
--input /Users/binds/workshop/samplesheet_rnaseq.csv \
--transcript_fasta /Users/binds/workshop/ref/gencode.vM39.transcripts.fa.gz \
--gtf /Users/binds/workshop/ref/gencode.vM39.chr_patch_hapl_scaff.annotation.gtf.gz \
--gene_bed /Users/binds/workshop/ref/gencode.vM39.chr_patch_hapl_scaff.annotation.bed \
--gencode \
--skip_trimming \
--skip_alignment \
--pseudo_aligner salmon \
--outdir /Users/binds/workshop/results
```
---

## 5. nf-core/differentialabundance による発現変動解析
### 5.1 サンプルシートとコントラストファイルの作成

下記の内容を`/Users/binds/workshop/samplesheet_da.csv`として保存します。

```csv
sample,condition
control1,control
control2,control
control3,control
control4,control
control5,control
stress1,stress
stress2,stress
stress3,stress
stress4,stress
stress5,stress
```

下記のコマンドで`/Users/binds/workshop/contrasts.csv`を作成します。
```
echo -e "id,variable,reference,target,blocking\nstress_vs_control,condition,control,stress," > /Users/binds/workshop/contrasts.csv
```

`/Users/binds/workshop/contrasts.csv`の中身は下記のようになっています。
```
id,variable,reference,target,blocking
stress_vs_control,condition,control,stress
```

### 5.2 nf-core/differentialabundance による発現変動解析
```
nextflow run nf-core/differentialabundance \
-r 1.5.0 \
-profile docker \
--input /Users/binds/workshop/samplesheet_da.csv \
--contrasts /Users/binds/workshop/contrasts.csv \
--matrix /Users/binds/workshop/results/salmon/salmon.merged.gene_counts.tsv \
--transcript_length_matrix /Users/binds/workshop/results/salmon/salmon.merged.gene_lengths.tsv \
--gtf /Users/binds/workshop/ref/gencode.vM39.chr_patch_hapl_scaff.annotation.gtf.gz \
--outdir /Users/binds/workshop/DEG
```

---
