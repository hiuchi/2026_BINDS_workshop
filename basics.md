# RNA-seq 解析の基礎知識

このページでは、RNA-seq 解析に必要な基礎を説明します。
バイオインフォマティクスやターミナル操作に慣れていない人でも、実習の操作やコマンドの意味がわかることを目標にします。

## 1. ターミナルとディレクトリ構造

### 1.1 ディレクトリの構造

ターミナルでは、フォルダのことをディレクトリと呼ぶことが多いです。
ファイルは、ディレクトリの中に階層的に置かれています。

macOS では、一番上の階層を `/` と書きます。
これはルートディレクトリと呼ばれます。

今回の実習で使う場所は、次のような階層になっています。

```text
/
└── Users
    └── binds2026
        ├── fastq
        ├── ref
        └── workshop
            └── samplesheet.csv
```

この例では、`Users` は `/` の中にあるディレクトリです。
`binds2026` は `Users` の中にあるディレクトリです。
`fastq`、`ref`、`workshop` は `binds2026` の中にあるディレクトリです。
`samplesheet.csv` は `workshop` の中にあるファイルです。

`/Users/binds2026/workshop/samplesheet.csv` のように `/` で区切って書くと、ファイルやディレクトリの場所を表せます。
ターミナルでは常に「今いるディレクトリ」があります。
`pwd` で今いる場所を確認し、`cd` で別の場所へ移動し、`ls` で中身を確認します。

### 1.2 Finder とターミナルの対応

普段は Finder でファイルやフォルダを開きます。
ターミナルでは、同じことを文字のコマンドで行います。

| Finder での操作 | ターミナルでの操作 |
| --- | --- |
| フォルダを開く | `cd` で移動する |
| 中身を見る | `ls` で一覧を見る |
| フォルダを作る | `mkdir` で作る |
| 今どこにいるか見る | `pwd` で確認する |

## 2. 最低限使うコマンド

### 2.1 今いる場所を確認する：`pwd`

`pwd` は、今ターミナルがいる場所を表示します。

```zsh
pwd
```

たとえば次のように表示されたら、今は `/Users/binds2026` にいるという意味です。

```text
/Users/binds2026
```

実習中に迷ったら、まず `pwd` を実行します。

### 2.2 ファイルやフォルダの一覧を見る：`ls`

`ls` は、今いる場所にあるファイルやフォルダを表示します。

```zsh
ls
```

特定の場所の中身を見ることもできます。

```zsh
ls /Users/binds2026
ls /Users/binds2026/fastq
ls /Users/binds2026/ref
```

### 2.3 フォルダを移動する：`cd`

`cd` は、作業する場所を移動するコマンドです。

```zsh
cd /Users/binds2026/workshop
```

このコマンドを実行すると、作業場所が `/Users/binds2026/workshop` に変わります。
移動できたか確認するときは、`pwd` を使います。

```zsh
pwd
```

### 2.4 フォルダを作る：`mkdir`

`mkdir` は、新しいフォルダを作るコマンドです。

```zsh
mkdir /Users/binds2026/workshop
```

すでに上の階層があるとは限らない場合は、`-p` を付けます。

```zsh
mkdir -p /Users/binds2026/workshop
```

`-p` は、必要な途中のフォルダもまとめて作る指定です。

## 3. パスとは何か

パスは、ファイルやフォルダの住所です。

今回の実習では、基本的に絶対パスを使います。
絶対パスは `/` から始まる完全な住所です。

```text
/Users/binds2026/workshop/samplesheet.csv
```

これは、次の意味です。

```text
/
└── Users
    └── binds2026
        └── workshop
            └── samplesheet.csv
```

絶対パスを使うと、今ターミナルがどこにいても同じファイルを指定できます。

## 4. Homebrew について

Homebrew は、macOS にソフトウェアを入れるための道具です。

たとえば Nextflow を使うには、Java と Nextflow が必要です。
それぞれの公式サイトから手作業で探してインストールする代わりに、Homebrew を使うと次のようにインストールできます。

```zsh
brew install openjdk@21
brew install nextflow
```

`brew install` は「このソフトウェアをインストールしてください」という意味です。

## 5. Docker について

Docker は、解析に必要なソフトウェア一式をまとめた環境を使うための仕組みです。

RNA-seq 解析では、多くのソフトウェアを使います。
参加者それぞれの Mac に全部を同じ状態で入れるのは大変です。
Docker を使うと、解析に必要なソフトウェアが入った環境をまとめて使えます。

この実習では、Nextflow が Docker を使って必要な解析ソフトウェアを呼び出します。
参加者が個別に Salmon などの解析ソフトを手作業で入れる必要はありません。

## 6. Nextflow と nf-core について

Nextflow は、複数の解析手順を順番に実行してくれる仕組みです。

RNA-seq 解析では、入力ファイルの確認、定量、結果の集計など、複数の処理が必要です。
これらを手作業で1つずつ実行すると、ミスが起きやすくなります。
Nextflow を使うと、決められた手順に従って解析を進められます。

nf-core は、Nextflow で作られた公開解析パイプラインの集まりです。
今回使うパイプラインは2つです。

| パイプライン | 役割 |
| --- | --- |
| `nf-core/rnaseq` | FASTQ から発現量を定量する |
| `nf-core/differentialabundance` | 群間で発現が変化した遺伝子を調べる |

コマンドの先頭は次の形になります。

```zsh
nextflow run nf-core/rnaseq
```

これは「Nextflow で `nf-core/rnaseq` を実行する」という意味です。

### 6.1 nf-core/rnaseq コマンドの読み方

Nextflow のコマンドは、基本的に次の形で読みます。

```zsh
nextflow run パイプライン名 オプション
```

今回の `nf-core/rnaseq` では、たとえば次のようなコマンドを実行します。

```zsh
nextflow run nf-core/rnaseq \
  -r 3.26.0 \
  -profile docker \
  --input /Users/binds2026/workshop/samplesheet.csv \
  --outdir /Users/binds2026/workshop/results
```

主な部分の意味は次の通りです。

| 部分 | 意味 |
| --- | --- |
| `nextflow` | Nextflow を使う |
| `run` | パイプラインを実行する |
| `nf-core/rnaseq` | 実行するパイプラインの名前 |
| `-r 3.26.0` | 使うパイプラインのバージョンを指定する |
| `-profile docker` | Docker を使って解析を実行する |
| `--input` | 入力ファイルを指定する |
| `--outdir` | 解析結果の出力先を指定する |

実践ページでは、長いコマンドを読みやすくするために複数行に分けて書いています。
各行の最後にあるバックスラッシュは、コマンドが次の行に続くことを表します。
1行で書いても、内容は同じです。

`-r` や `-profile` のように `-` で始まるものは、Nextflow 全体に対する指定です。
`--input` や `--outdir` のように `--` で始まるものは、実行するパイプラインに渡す指定です。

### 6.2 nf-core/differentialabundance コマンドの読み方

`nf-core/differentialabundance` は、`nf-core/rnaseq` で作成した発現量テーブルと、サンプル情報、比較条件を使って発現変動解析を行うパイプラインです。
今回の実習では、control 群を基準にして stress 群で発現が変化した遺伝子を調べます。

```zsh
nextflow run nf-core/differentialabundance \
  -r 2.0.0 \
  -profile docker \
  --input /Users/binds2026/workshop/samplesheet.csv \
  --contrasts /Users/binds2026/workshop/contrasts.csv \
  --matrix /Users/binds2026/workshop/results/salmon/salmon.merged.gene_counts.tsv \
  --feature_length_matrix /Users/binds2026/workshop/results/salmon/salmon.merged.gene_lengths.tsv \
  --gtf /Users/binds2026/ref/gencode.vM39.annotation.gtf.gz \
  --outdir /Users/binds2026/workshop/DEG
```

主な部分の意味は次の通りです。

| 部分 | 意味 |
| --- | --- |
| `nf-core/differentialabundance` | 発現変動解析のパイプライン名 |
| `-r 2.0.0` | 使うパイプラインのバージョンを指定する |
| `-profile docker` | Docker を使って解析を実行する |
| `--input` | サンプル情報を記述したファイルを指定する |
| `--contrasts` | 比較条件を記述したファイルを指定する |
| `--matrix` | 遺伝子ごとのカウントテーブルを指定する |
| `--feature_length_matrix` | 遺伝子長のテーブルを指定する |
| `--gtf` | 遺伝子アノテーションファイルを指定する |
| `--outdir` | 解析結果の出力先を指定する |

`--matrix` と `--feature_length_matrix` には、`nf-core/rnaseq` の結果として作られたファイルを指定します。
つまり、発現変動解析は RNA-seq 定量の結果を入力として実行します。

解析が途中で止まった場合は、同じコマンドに `-resume` を付けて再開できます。

```zsh
-resume
```

## 7. RNA-seq 解析で使う入力ファイル

### 7.1 入力データの中身を見る

RNA-seq 解析では、いくつかの種類のファイルを入力として使います。
ファイル全体を人が読む必要はありませんが、どのような形式のファイルを使っているのかを知っておくと、実習中のコマンドの意味が理解しやすくなります。

FASTQ ファイルは、1つのリードを4行で表します。
1行目はリード名、2行目は塩基配列、3行目は区切り、4行目はクオリティスコアです。

```text
@SRR24350711.1 NB551353:21:HYMVTBGX9:1:11101:19311:1044 length=76
AACAANTCCAGCCCCCACTGCGTGTGGCGTTCCAGCACCTCAAACTGATCCCACAACTCGGTACCCCAATCCATGC
+SRR24350711.1 NB551353:21:HYMVTBGX9:1:11101:19311:1044 length=76
AAAA6#/EAE/EAEEA/EE/EEE/EEE<E<//A/E</EAEE///A/EE//EA//<//EEE<</<E<<<</6<AAAA
```

FASTA ファイルは、`>` で始まる行に配列名や説明が書かれ、その下に塩基配列が続きます。
今回の解析では、マウスの転写産物配列を参照するために使います。

```text
>ENSMUST00000193812.2|ENSMUSG00000102693.2|4933401J01Rik-201|4933401J01Rik|1070|TEC|
AAGGAAAGAGGATAACACTTGAAATGTAAATAAAGAAAATACCTAATAAAAATAAATAAA
AACATGCTTTCAAAGGAAATAAAAAGTTGGATTCAAAAATTTAACTTTTGCTCATTTGGT
```

GTF ファイルは、遺伝子や転写産物の位置、向き、名前などを記録したタブ区切りのファイルです。
下の例では、`chr1` 上の遺伝子について、開始位置、終了位置、遺伝子名などが書かれています。

```text
chr1	HAVANA	gene	3143476	3144545	.	+	.	gene_id "ENSMUSG00000102693.2"; gene_name "4933401J01Rik";
chr1	HAVANA	transcript	3143476	3144545	.	+	.	gene_id "ENSMUSG00000102693.2"; transcript_id "ENSMUST00000193812.2"; gene_name "4933401J01Rik";
```

### 7.2 FASTQ ファイル

FASTQ は、シーケンサーから出てくる配列データです。
RNA-seq 解析の出発点です。

今回の FASTQ ファイルは、あらかじめ次の場所に置いてあります。

```text
/Users/binds2026/fastq
```

### 7.3 FASTA ファイル

FASTA は、塩基配列を記録するためのファイル形式です。
今回の解析では、マウスの転写産物配列を参照するために使います。

```text
/Users/binds2026/ref/gencode.vM39.transcripts.fa.gz
```

### 7.4 GTF ファイル

GTF は、遺伝子や転写産物の注釈情報を記録したファイルです。
どの遺伝子がどこにあるか、遺伝子名は何か、といった情報が入っています。

```text
/Users/binds2026/ref/gencode.vM39.annotation.gtf.gz
```

### 7.5 BED ファイル

BED は、ゲノム上の領域を表すファイルです。
今回の実習では、あらかじめ作成したファイルを使います。

```text
/Users/binds2026/ref/gencode.vM39.annotation.bed
```

### 7.6 サンプルシート

サンプルシートは、どのサンプルがどの FASTQ ファイルに対応するかをまとめた CSV ファイルです。

```text
/Users/binds2026/workshop/samplesheet.csv
```

今回のサンプルシートには、サンプル名、FASTQ ファイルの場所、ライブラリの向き、実験条件を書きます。

```csv
sample,fastq_1,strandedness,condition
control1,/Users/binds2026/fastq/SRR24350720.fastq.gz,auto,control
stress1,/Users/binds2026/fastq/SRR24350715.fastq.gz,auto,stress
```

### 7.7 コントラストファイル

コントラストファイルは、どの群とどの群を比較するかを指定する CSV ファイルです。

```text
/Users/binds2026/workshop/contrasts.csv
```

今回の比較は、control 群を基準にして stress 群を見る比較です。

```csv
id,variable,reference,target,blocking
stress_vs_control,condition,control,stress,
```

## 8. 今回のフォルダ構成

実習では、主に次の場所を使います。

```text
/Users/binds2026
├── fastq
│   ├── SRR24350720.fastq.gz
│   ├── SRR24350719.fastq.gz
│   └── ...
├── ref
│   ├── gencode.vM39.transcripts.fa.gz
│   ├── gencode.vM39.annotation.gtf.gz
│   └── gencode.vM39.annotation.bed
└── workshop
    ├── samplesheet.csv
    ├── rnaseq.config
    ├── contrasts.csv
    ├── results
    └── DEG
```

自分で作る主なファイルは次の3つです。

| ファイル | 役割 |
| --- | --- |
| `/Users/binds2026/workshop/samplesheet.csv` | サンプル情報を指定する |
| `/Users/binds2026/workshop/rnaseq.config` | RNA-seq 解析の設定を指定する |
| `/Users/binds2026/workshop/contrasts.csv` | 発現変動解析の比較条件を指定する |

解析結果は、主に次の場所に出力されます。

| 出力先 | 内容 |
| --- | --- |
| `/Users/binds2026/workshop/results` | `nf-core/rnaseq` の結果 |
| `/Users/binds2026/workshop/DEG` | `nf-core/differentialabundance` の結果 |

## 9. 実習に入る前の確認

実習を始める前に、次のことを確認します。

```zsh
pwd
ls /Users/binds2026
ls /Users/binds2026/fastq
ls /Users/binds2026/ref
mkdir -p /Users/binds2026/workshop
cd /Users/binds2026/workshop
pwd
```

ここまで確認できれば、実習で使う準備はできています。

この後は、サンプルシートを作成し、`nf-core/rnaseq` で定量を行い、`nf-core/differentialabundance` で発現変動解析を行います。
