---
title: 翠鸟（kingfisher）公共测序数据下载利器
date: 2021-08-14 15:36:58
tags: 工具
categories: 工具
---

## 写在前面

一般在进行大规模组学研究的时候，都需要批量下载一定数量的测序原始数据来做进一步的分析。从实际工作中发现，下载数据时，往往会遇到网速限制或下载链接不可能等情况，当某个数据库的目标数据无法下载时，其实可以去其它公共数据库，比如NCBI SRA、EBI ENA、DDBJ SRA等下载。此外，还有一些大公司云服务器的资源可供使用，比如AWS Open Data和Google Cloud等。最重要的是，这些数据库之前都是实时共享的，保证了数据的有效性。

目前主流的测序数据下载工具是NCBI提供的工具[SRAToolkit](https://trace.ncbi.nlm.nih.gov/Traces/sra/sra.cgi?view=software)，使用其中的`fastq-dump`直接下载SRR文件，并转换为FASTQ文件。但是，`fastq-dump`让人诟病的地方是效率不高，只支持单个线程，所以速度特别慢。此外，`fastq-dump`并不支持同时从多个数据源中尝试下载目标数据，因此下载失败的可能性很高。

## Kingfisher简介

**[Kingfisher](https://github.com/wwood/kingfisher-download)**是近期公开的一个快速灵活的测序数据下载软件，支持从**European Nucleotide Archive (ENA)**, **NCBI SRA**, **Amazon AWS**和**Google Cloud**这四个数据源中下载数据，并且同时支持通过Run accession（比如DRR001970）和BioProject accession（比如PRJNA621514和SRP260223）来批量下载数据。**Kingfisher会尝试从一系列的数据源进行数据下载，直到某个源能够成功下载为止。**

此外，还能根据用户的需求**将下载数据直接输出为SRA、Fastq、Fasta或Gzip等格式**，非常方便，不需要自己再对SRA数据通过fastq-dump进行拆分转换。

<img src="kingfisher公共测序数据下载利器/kingfisher_logo.png" alt="Kingfisher logo" style="zoom: 67%;"/>

## Kingfisher安装与使用

### 安装

如下所示，Kingfisher可以从通过conda软件包管理系统直接安装，注意在conda中添加`conda-forge`和`bioconda`这两个源。

```bash
conda create -c conda-forge -c bioconda -n kingfisher pigz python extern curl sra-tools pandas requests aria2
conda activate kingfisher
pip install bird_tool_utils'>='0.2.17
git clone https://github.com/wwood/kingfisher-download
cd kingfisher-download/bin
export PATH=$PWD:$PATH
kingfisher -h
```

此外，如果需要支持`ena-ascp`方法，需要额外安装IBM提供的Aspera软件。

Aspera下载链接为：

https://www.ibm.com/aspera/connect/

下载Linux版本安装包，然后使用`tar zxvf`命令进行解压，里面有一个安装的bash脚本，直接运行即可进行安装。

安装完成后，可以在如下默认位置找到可执行文件和配置文件：

1. 可执行文件，将其加入环境变量中。

   ```bash
   $HOME/.aspera/connect/bin/ascp
   ```

2. openssh文件：

   ```bash
   $HOME/.aspera/connect/etc/asperaweb_id_dsa.openssh
   ```

### 使用



**注意**：如果只想下载某个确定的SRA数据，则使用`-r`参数，提供SRR Number即可，如SRR12042866；若是想**批量下载某个BioProject中的所有数据**，则可以使用`-p`参数，提供BioProject Number，如PRJNA640275或SRP267791。
