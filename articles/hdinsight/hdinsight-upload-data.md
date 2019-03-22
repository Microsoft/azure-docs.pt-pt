---
title: Carregar dados para as tarefas do Apache Hadoop no HDInsight
description: Saiba como carregar e aceder a dados para as tarefas do Apache Hadoop no HDInsight com a CLI clássica do Azure, o Explorador de armazenamento do Azure, Azure PowerShell, a linha de comandos do Hadoop ou Sqoop.
keywords: hadoop de ETL, colocar dados no hadoop, dados de carga do hadoop
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/08/2019
ms.openlocfilehash: 820bb22fee75e5c1159ba90f23b478339f6345b5
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201930"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Carregar dados para as tarefas do Apache Hadoop no HDInsight

O Azure HDInsight fornece um sistema de ficheiros do distribuído do Hadoop de completo (HDFS) ao longo do armazenamento do Azure e o armazenamento do Azure Data Lake (Gen1 e Gen2). Armazenamento do Azure e Gen1 de armazenamento do Data Lake e geração 2 são projetadas como extensões HDFS, fornecer uma experiência totalmente integrada aos clientes. Permitem que o conjunto completo de componentes no ecossistema do Hadoop para operar diretamente em dados que gere. Armazenamento do Azure, Gen1 de armazenamento do Data Lake e geração 2 são sistemas de ficheiros distintos que estão otimizados para armazenamento de dados e computações nesses dados. Para obter informações sobre os benefícios da utilização do armazenamento do Azure, consulte [utilizar o armazenamento do Azure com o HDInsight][hdinsight-storage], [Use Data Lake armazenamento Gen1 com HDInsight](hdinsight-hadoop-use-data-lake-store.md), e [ Utilizar o Data Lake Storage Gen2 com o HDInsight](../storage/blobs/data-lake-storage-use-hdi-cluster.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, tenha em atenção os seguintes requisitos:

* Um cluster do Azure HDInsight. Para obter instruções, consulte [introdução ao Azure HDInsight] [ hdinsight-get-started] ou [clusters do HDInsight criar](hdinsight-hadoop-provision-linux-clusters.md).
* Dados de conhecimento dos seguintes artigos:

    - [Utilizar o armazenamento do Azure com o HDInsight][hdinsight-storage]
    - [Utilizar o Data Lake Storage Gen1 com o HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    - [Utilizar o Data Lake Storage Gen2 com o HDInsight](../storage/blobs/data-lake-storage-use-hdi-cluster.md)  

## <a name="upload-data-to-azure-storage"></a>Carregar dados para o armazenamento do Azure

## <a name="utilities"></a>Serviços Públicos
A Microsoft fornece os seguintes utilitários para trabalhar com o armazenamento do Azure:

| Ferramenta | Linux | SO X | Windows |
| --- |:---:|:---:|:---:|
| [Portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AZCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Comandos de Hadoop](#commandline) |✔ |✔ |✔ |


> [!NOTE]  
> O comando de Hadoop só está disponível no cluster do HDInsight. O comando só permite o carregamento de dados do sistema de ficheiros local para o armazenamento do Azure.  


## <a id="commandline"></a>Linha de comandos do Hadoop
A linha de comandos do Hadoop só é útil para armazenar dados em BLOBs de armazenamento do Azure quando os dados já estão presentes no nó principal do cluster.

Para utilizar o comando de Hadoop, primeiro tem de ligar para o nó principal, com [SSH ou PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Assim que estiver ligado, pode utilizar a sintaxe seguinte para carregar um ficheiro para o armazenamento.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Por exemplo, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Uma vez que é o sistema de ficheiros predefinido para o HDInsight no armazenamento do Azure, /example/data.txt é, na verdade, no armazenamento do Azure. Também pode consultar o ficheiro como:

    wasb:///example/data/data.txt

ou

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para obter uma lista de outros comandos do Hadoop que trabalhar com arquivos, consulte [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Nos clusters do Apache HBase, a predefinição bloquear utilizado quando a escrita de dados é 256 KB de tamanho. Embora isso funciona bem quando utilizar APIs de HBase ou de REST APIs, usando o `hadoop` ou `hdfs dfs` comandos para escrever mais de ~ 12 GB de dados resulta num erro. Para obter mais informações, consulte a [exceção de armazenamento para escrita no blob](#storageexception) secção deste artigo.

## <a name="graphical-clients"></a>Clientes de gráficos
Também existem vários aplicativos que fornecem uma interface gráfica para trabalhar com o armazenamento do Azure. A tabela seguinte é uma lista de alguns desses aplicativos:

| Cliente | Linux | SO X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools para o HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Explorador do Armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Explorador de cloudBerry para o Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |


## <a name="mount-azure-storage-as-local-drive"></a>Montar o armazenamento do Azure como unidade Local
Ver [armazenamento do Azure de montagem como unidade Local](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Carregar com serviços
### <a name="azure-data-factory"></a>Azure Data Factory
O serviço Azure Data Factory é um serviço totalmente gerido para compor serviços de movimento de dados, processamento de dados e armazenamento de dados em pipelines de produção de dados simplificada, dimensionáveis e fiáveis.

|Tipo de armazenamento|Documentação|
|----|----|
|Armazenamento de Blobs do Azure|[Copiar dados de ou para armazenamento de Blobs do Azure com o Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Armazenamento do Azure Data Lake Ger1|[Copiar dados de ou para a geração 1 de armazenamento do Azure Data Lake com o Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Armazenamento do Azure Data Lake Ger2 |[Carregar dados para Gen2 de armazenamento do Azure Data Lake com o Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop
Sqoop é uma ferramenta projetada para transferir dados entre o Hadoop e bases de dados relacionais. Pode usá-lo para importar dados de um sistema de gerenciamento de banco de dados relacional (RDBMS), como SQL Server, MySQL ou Oracle para o sistema de ficheiros distribuído Hadoop (HDFS), transformar os dados no Hadoop com o MapReduce ou Hive e, em seguida, exportar os dados de volta para um RDBMS.

Para obter mais informações, consulte [utilização Sqoop com o HDInsight][hdinsight-use-sqoop].

### <a name="development-sdks"></a>SDKs de desenvolvimento
O armazenamento do Azure também pode ser acessado com um SDK de linguagens de programação seguintes:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obter mais informações sobre como instalar os SDKs do Azure, consulte [transferências do Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Resolução de problemas
### <a id="storageexception"></a>Exceção de armazenamento para escrita em BLOBs
**Os sintomas**: Ao utilizar o `hadoop` ou `hdfs dfs` comandos para escrever ficheiros de ~ 12 GB ou superior num cluster do HBase, pode encontrar o erro seguinte:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Motivo**: HBase no HDInsight clusters predefinido para um tamanho de bloco de 256 KB ao escrever no armazenamento do Azure. Enquanto ele trabalha para APIs do HBase ou de REST APIs, que resulta num erro ao utilizar o `hadoop` ou `hdfs dfs` utilitários da linha de comandos.

**Resolução**: Utilize `fs.azure.write.request.size` para especificar um tamanho de bloco maior. Pode fazer isso numa base por utilização com o `-D` parâmetro. O comando seguinte é um exemplo que usa este parâmetro com o `hadoop` comando:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Também pode aumentar o valor de `fs.azure.write.request.size` globalmente ao utilizar o Apache Ambari. Os passos seguintes podem ser utilizados para alterar o valor na IU da Web do Ambari:

1. No seu browser, aceda à IU da Web do Ambari para o seu cluster. Isto é https://CLUSTERNAME.azurehdinsight.net, onde **CLUSTERNAME** é o nome do seu cluster.

    Quando lhe for pedido, introduza o nome de administrador e a palavra-passe para o cluster.
2. Do lado esquerdo do ecrã, selecione **HDFS**e, em seguida, selecione a **configurações** separador.
3. Na **filtro...**  , insira `fs.azure.write.request.size`. Esta ação apresenta o campo e o valor atual no meio da página.
4. Altere o valor de 262144 (256 KB) para o novo valor. Por exemplo, 4194304 (4 MB).

![Imagem da alteração do valor por meio da interface do Usuário de Web de Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Para obter mais informações sobre a utilização do Ambari, consulte [clusters do HDInsight gerir com a interface do Usuário do Apache Ambari Web](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Passos Seguintes
Agora que compreende como obter dados no HDInsight, leia os artigos seguintes para saber como executar uma análise:

* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)][hdinsight-get-started]
* [Submeter tarefas do Apache Hadoop por meio de programação][hdinsight-submit-jobs]
* [Utilizar o Apache Hive com o HDInsight][hdinsight-use-hive]
* [Utilizar o Apache Pig com o HDInsight][hdinsight-use-pig]

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
