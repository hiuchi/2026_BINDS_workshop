# BINDS発現・機能解析インシリコ解析融合ユニット講習会

このリポジトリは、Nextflow を用いた RNA-seq 解析ワークショップの資料です。
初心者向けの基礎知識と、実際にコマンドを実行する実践手順を分けて掲載しています。

## 資料

1. [基礎知識：ターミナルと RNA-seq 解析の全体像](basics.md)
   - ターミナル、`pwd`, `ls`, `cd`, `mkdir`
   - 絶対パス
   - Homebrew、Docker、Nextflow、nf-core
   - FASTQ、FASTA、GTF、BED、CSV ファイルの役割

2. [実践：Nextflow による RNA-seq 解析](practice.md)
   - 環境構築
   - 入力ファイルの確認
   - `nf-core/rnaseq` による定量
   - `nf-core/differentialabundance` による発現変動解析

## 実習の概要

- 対象データ: 心理的ストレスを与えたマウス結腸組織の bulk RNA-seq データ
- 比較: control 群と stress 群
- 定量: `nf-core/rnaseq`
- 発現変動解析: `nf-core/differentialabundance`
- 参加者用アカウント: `binds2026`
- 作業ディレクトリ: `/Users/binds2026/workshop`

## 進め方

はじめてターミナルやバイオインフォマティクス解析に触れる場合は、先に [基礎知識](basics.md) を読んでください。
その後、[実践手順](practice.md) に沿って解析を進めます。
