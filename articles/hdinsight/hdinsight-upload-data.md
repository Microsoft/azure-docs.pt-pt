---
title: Upload de dados para empregos apache Hadoop em HDInsight
description: Saiba como carregar e aceder a dados para empregos apache Hadoop em HDInsight. Use O Clássico CLI, Azure Storage Explorer, Azure PowerShell, a linha de comando Hadoop ou Sqoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: a8d21b88298b6092c869b89d48fe4c259c2365b6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931349"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Upload de dados para empregos apache Hadoop em HDInsight

O HDInsight fornece um sistema de ficheiros distribuídos hadoop (HDFS) sobre o Azure Storage e o Azure Data Lake Storage. Este armazenamento inclui a Gen1 e a Gen2. Azure Storage and Data Lake Storage Gen1 e Gen2 são projetados como extensões HDFS. Permitem que todo o conjunto de componentes no ambiente Hadoop funcione diretamente nos dados que gere. Azure Storage, Data Lake Storage Gen1 e Gen2 são sistemas de ficheiros distintos. Os sistemas estão otimizados para armazenamento de dados e cálculos nesses dados. Para obter informações sobre os benefícios da utilização do Azure Storage, consulte [use Azure Storage com HDInsight](hdinsight-hadoop-use-blob-storage.md). Consulte também, [Use Data Lake Storage Gen1 com HDInsight,](hdinsight-hadoop-use-data-lake-storage-gen1.md)e Use Data Lake Storage [Gen2 com HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Pré-requisitos

Observe os seguintes requisitos antes de começar:

* Um cluster do Azure HDInsight. Para obter instruções, consulte [Começar com Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Conhecimento dos seguintes artigos:
    * [Use o armazenamento Azure com HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Use data lake storage Gen1 com HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [Use data lake storage Gen2 com HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Faça upload de dados para o Azure Storage

### <a name="utilities"></a>Utilitários

A Microsoft fornece os seguintes utilitários para trabalhar com o Azure Storage:

| Ferramenta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Comando Hadoop](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> O comando Hadoop só está disponível no cluster HDInsight. O comando só permite o carregamento de dados do sistema de ficheiros local para o Azure Storage.  

### <a name="hadoop-command-line"></a>Linha de comando Hadoop

A linha de comando Hadoop só é útil para armazenar dados na bolha de armazenamento Azure quando os dados já estão presentes no nó da cabeça do cluster.

Para utilizar o comando Hadoop, tem primeiro de ligar ao cabeçano com [sSH ou PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Uma vez ligado, pode utilizar a seguinte sintaxe para carregar um ficheiro para armazenamento.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Por exemplo, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Como o sistema de ficheiros predefinido para HDInsight está no Azure Storage, /exemplo/dados/data.txt está na verdade no Azure Storage. Também pode consultar o ficheiro como:

`wasbs:///example/data/data.txt`

ou

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

Para uma lista de outros comandos Hadoop que funcionam com ficheiros, ver [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Nos clusters Apache HBase, o tamanho do bloco predefinido utilizado ao escrever dados é de 256 KB. Embora isto funcione bem quando se utiliza APIs HBase ou REST APIs, usar os `hadoop` `hdfs dfs` ou comandos para escrever dados maiores do que ~12 GB resulta num erro. Para obter mais informações, consulte [a exceção de armazenamento para escrever no blob](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob).

### <a name="graphical-clients"></a>Clientes gráficos

Existem também várias aplicações que fornecem uma interface gráfica para trabalhar com o Azure Storage. Segue-se uma lista de algumas destas candidaturas:

| Cliente | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Ferramentas de estúdio visual da Microsoft para HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Explorador do Storage do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer para Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Armazenamento do Monte Azure como Unidade Local

Consulte [o Armazenamento do Monte Azure como Unidade Local](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive).

## <a name="upload-using-services"></a>Upload usando serviços

### <a name="azure-data-factory"></a>Azure Data Factory

O serviço Azure Data Factory é um serviço totalmente gerido para a composição de dados: serviços de armazenamento, processamento e movimento em oleodutos de produção de dados simplificados, adaptáveis e fiáveis.

|Tipo de armazenamento|Documentação|
|----|----|
|Armazenamento de Blobs do Azure|[Copiar dados de/para o Armazenamento de blobs do Azure com o Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Armazenamento do Azure Data Lake Ger1|[Copiar dados para ou a partir da Azure Data Lake Storage Gen1 utilizando a Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Armazenamento do Azure Data Lake Ger2 |[Carregue os dados no Azure Data Lake Storage Gen2 com a Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop é uma ferramenta projetada para transferir dados entre Hadoop e bases de dados relacionais. Utilize-os para importar dados de um sistema de gestão de bases de dados relacionais (RDBMS), tais como SQL Server, MySQL ou Oracle. Em seguida, no sistema de ficheiros distribuídos Hadoop (HDFS). Transforme os dados em Hadoop com MapReduce ou Hive e, em seguida, exporte os dados de volta para um RDBMS.

Para obter mais informações, consulte [Use Sqoop com HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>SDKs de desenvolvimento

O Azure Storage também pode ser acedido através de um Azure SDK a partir das seguintes linguagens de programação:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obter mais informações sobre a instalação dos Azure SDKs, consulte [os downloads do Azure](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>Próximos passos

Agora que entende como obter dados em HDInsight, leia os seguintes artigos para aprender a análise:

* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Submeta empregos apache Hadoop programáticamente](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)