# BINDS発現・機能解析インシリコ解析融合ユニット講習会

## 1. 概要
### 1.1 対象のデータ
- [Schneider, Kai Markus et al. Cell, Volume 186, Issue 13, 2823-2838.e20](https://doi.org/10.1016/j.cell.2023.05.001)
- 心理的ストレスを与えるために 7 日間拘束したマウスの結腸組織におけるバルク RNA-seq データです。control 群と stress 群がそれぞれ n=5 ずつ存在します。
### 1.2 講習内容
- 環境構築
- 入力ファイルの確認
- nf-core/rnaseq による定量
- nf-core/differentialabundance による発現変動解析
### 1.3 検証環境
- MacBook Air (M4, 13-inch, 2024)
  - CPU : 10コア
  - メモリ : 16GB
  - ストレージ : 256GB
  - OS : macOS Tahoe 26.0.1
- 講習会参加者用アカウントは `binds2026` を使用します。
- `/Users/binds2026/workshop`で解析を行います。

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
  echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/binds2026/.zprofile
  eval "$(/opt/homebrew/bin/brew shellenv)"
  ```
### 2.3 Nextflow のインストール
Homebrew を使って Java と Nextflow をインストールします。
  ```zsh
  brew install openjdk@21
  brew install nextflow
  ```
---

## 3. 入力ファイルの確認
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
オンサイト講習会では、FASTQ ファイルは使用する Mac にあらかじめダウンロードしておきます。自分でダウンロードする場合は、[PRJNA963162](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE229320)のデータを ENA から`/Users/binds2026/fastq`にダウンロードします。

```zsh
brew install wget coreutils

mkdir -p /Users/binds2026/fastq

wget -O /Users/binds2026/fastq/PRJNA963162_ena_fastq.tsv \
  'https://www.ebi.ac.uk/ena/portal/api/filereport?accession=PRJNA963162&result=read_run&fields=run_accession,fastq_ftp,fastq_md5,fastq_bytes&format=tsv&download=true'

awk 'NR > 1 {print "https://"$2}' /Users/binds2026/fastq/PRJNA963162_ena_fastq.tsv \
  > /Users/binds2026/fastq/fastq_urls.txt

wget -P /Users/binds2026/fastq -i /Users/binds2026/fastq/fastq_urls.txt

awk 'NR > 1 {n = split($2, path, "/"); print $3 "  /Users/binds2026/fastq/" path[n]}' \
  /Users/binds2026/fastq/PRJNA963162_ena_fastq.tsv \
  > /Users/binds2026/fastq/MD5SUMS

gmd5sum -c /Users/binds2026/fastq/MD5SUMS
```

#### 3.2.2 リファレンスファイル（GRCm39, GENCODE Release M39）
リファレンスファイルは、オンサイト講習会で使用する Mac にあらかじめ配置してあります。解析では下記のファイルを使用します。

- [`/Users/binds/workshop/ref/gencode.vM39.transcripts.fa.gz`](https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_mouse/release_M39/gencode.vM39.transcripts.fa.gz)
- [`/Users/binds/workshop/ref/gencode.vM39.annotation.gtf.gz`](https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_mouse/release_M39/gencode.vM39.annotation.gtf.gz)

---

## 4. nf-core/rnaseq による定量
### 4.1 サンプルシートの作成
サンプル名、FASTQ ファイルへのパス、ライブラリの向き、実験条件を記述し、`$HOME/Library/CloudStorage/Dropbox/samplesheet.csv`として保存します。
`condition`列は nf-core/differentialabundance で使用します。
```zsh
cat > "$HOME/Library/CloudStorage/Dropbox/samplesheet.csv" <<'EOF'
sample,fastq_1,strandedness,condition
control1,/Users/hiuchi/Dropbox/workshop/fastq/SRR24350720.fastq.gz,auto,control
control2,/Users/hiuchi/Dropbox/workshop/fastq/SRR24350719.fastq.gz,auto,control
control3,/Users/hiuchi/Dropbox/workshop/fastq/SRR24350718.fastq.gz,auto,control
stress1,/Users/hiuchi/Dropbox/workshop/fastq/SRR24350715.fastq.gz,auto,stress
stress2,/Users/hiuchi/Dropbox/workshop/fastq/SRR24350714.fastq.gz,auto,stress
stress3,/Users/hiuchi/Dropbox/workshop/fastq/SRR24350713.fastq.gz,auto,stress
EOF
```
### 4.2 Salmon による定量
Salmon によって遺伝子産物の定量を行います。
```
nextflow run nf-core/rnaseq \
-r 3.26.0 \
-profile docker \
--input "$HOME/Library/CloudStorage/Dropbox/samplesheet.csv" \
--transcript_fasta /Users/binds/workshop/ref/gencode.vM39.transcripts.fa.gz \
--gtf /Users/binds/workshop/ref/gencode.vM39.annotation.gtf.gz \
--gencode \
--skip_trimming \
--skip_alignment \
--pseudo_aligner salmon \
--outdir /Users/binds2026/workshop/results
```
---

## 5. nf-core/differentialabundance による発現変動解析
### 5.1 コントラストファイルの作成

サンプルシートは 4.1 で作成した`$HOME/Library/CloudStorage/Dropbox/samplesheet.csv`を使用します。
`variable`にはサンプルシートの列名、`reference`には比較の基準群、`target`には比較したい群を指定します。
今回は`condition`列の`control`群を基準にして、`stress`群との発現量の違いを調べます。
`blocking`列は使用しないため空欄にします。

下記のコマンドで`/Users/binds2026/workshop/contrasts.csv`を作成します。
```
printf "id,variable,reference,target,blocking\nstress_vs_control,condition,control,stress,\n" > /Users/binds2026/workshop/contrasts.csv
```

`/Users/binds2026/workshop/contrasts.csv`の中身は下記のようになっています。
```
id,variable,reference,target,blocking
stress_vs_control,condition,control,stress,
```

### 5.2 nf-core/differentialabundance による発現変動解析
```
nextflow run nf-core/differentialabundance \
-r 1.5.0 \
-profile docker \
--input "$HOME/Library/CloudStorage/Dropbox/samplesheet.csv" \
--contrasts /Users/binds2026/workshop/contrasts.csv \
--matrix /Users/binds2026/workshop/results/salmon/salmon.merged.gene_counts.tsv \
--transcript_length_matrix /Users/binds2026/workshop/results/salmon/salmon.merged.gene_lengths.tsv \
--gtf /Users/binds/workshop/ref/gencode.vM39.annotation.gtf.gz \
--outdir /Users/binds2026/workshop/DEG
```

---
