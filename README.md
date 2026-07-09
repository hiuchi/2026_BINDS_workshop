# BINDS発現・機能解析インシリコ解析融合ユニット講習会

このリポジトリは、Nextflow を用いた RNA-seq 解析ワークショップの資料です。
初心者向けの基礎知識と、実際にコマンドを実行する実践手順を分けて掲載しています。

## 今日やることの全体像

今回の実習では、マウスの結腸組織の bulk RNA-seq データを使います。
control 群と stress 群を比較し、心理的ストレスによって発現が変化した遺伝子を調べます。

大まかな流れは次の通りです。

```text
FASTQ ファイル
  ↓
nf-core/rnaseq
  ↓
遺伝子ごとの発現量テーブル
  ↓
nf-core/differentialabundance
  ↓
stress 群で発現が変化した遺伝子の一覧
```

## 資料

1. [RNA-seq 解析の基礎知識](basics.md)
   - ターミナル、`pwd`, `ls`, `cd`, `mkdir`
   - 絶対パス
   - Homebrew、Docker、Nextflow、nf-core
   - FASTQ、FASTA、GTF、BED、CSV ファイルの役割

2. [実践：Nextflow による RNA-seq 解析](practice.md)
   - 環境構築
   - 入力ファイルの確認
   - `nf-core/rnaseq` による定量
   - `nf-core/differentialabundance` による発現変動解析
