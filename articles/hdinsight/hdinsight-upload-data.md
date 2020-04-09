---
title: Enviar dados para empregos apache Hadoop no HDInsight
description: Saiba como carregar e aceder a dados para trabalhos apache hadoop no HDInsight. Utilize o clássico CLI, Azure Storage Explorer, Azure PowerShell, a linha de comando Hadoop ou Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdiseo17may2017
ms.date: 04/07/2020
ms.openlocfilehash: c862633245e75613f9e4f9956486f872b96239f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875015"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Enviar dados para empregos apache Hadoop no HDInsight

O HDInsight fornece um sistema de ficheiros distribuído seletiva (HDFS) sobre o Armazenamento Azure e o Armazenamento do Lago Azure Data. Este armazenamento inclui Gen1 e Gen2. O Armazenamento Azure e data Lake Storage Gen1 e Gen2 são projetados como extensões HDFS. Permitem que todo o conjunto de componentes no ambiente Hadoop funcione diretamente nos dados que gere. O Armazenamento Azure, data lake storage Gen1 e Gen2 são sistemas de ficheiros distintos. Os sistemas estão otimizados para armazenamento de dados e cálculos sobre esses dados. Para obter informações sobre os benefícios da utilização do Armazenamento Azure, consulte [Use Azure Storage com HDInsight](hdinsight-hadoop-use-blob-storage.md). Consulte também, [use data Lake Storage Gen1 com HDInsight](hdinsight-hadoop-use-data-lake-store.md), e use data lake storage [Gen2 com HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Pré-requisitos

Tenha em atenção os seguintes requisitos antes de começar:

* Um cluster do Azure HDInsight. Para obter instruções, consulte [Iniciar com clusters Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) ou [Create HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Conhecimento dos seguintes artigos:
    * [Utilize o Armazenamento Azure com HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Use data Lake Storage Gen1 com HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Utilize data lake storage Gen2 com HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Enviar dados para o Armazenamento Azure

## <a name="utilities"></a>Utilitários

A Microsoft fornece os seguintes utilitários para trabalhar com o Armazenamento Azure:

| Ferramenta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Comando hadoop](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> O comando Hadoop só está disponível no cluster HDInsight. O comando apenas permite o carregamento de dados do sistema de ficheiros local para o Armazenamento Azure.  

## <a name="hadoop-command-line"></a>Linha de comando hadoop

A linha de comando Hadoop só é útil para armazenar dados em blob de armazenamento Azure quando os dados já estão presentes no nó da cabeça do cluster.

Para utilizar o comando Hadoop, primeiro deve ligar-se ao nódoo utilizando [SSH ou PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Uma vez ligado, pode utilizar a seguinte sintaxe para fazer o upload de um ficheiro para armazenamento.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Por exemplo, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Uma vez que o sistema de ficheiros predefinido para o HDInsight está no Armazenamento Azure, /exemplo/data/data.txt está na verdade no Armazenamento Azure. Também pode consultar o ficheiro como:

    wasbs:///example/data/data.txt

ou

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para uma lista de outros comandos Hadoop que trabalham com ficheiros, consulte[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Nos clusters Apache HBase, o tamanho do bloco predefinido utilizado ao escrever dados é de 256 KB. Embora isto funcione bem ao utilizar APIs de `hadoop` Base `hdfs dfs` HBase ou APIs REST, usar os ou comandos para escrever dados superiores a ~12 GB resulta num erro. Para mais informações, consulte a exceção de armazenamento para escrever na secção [blob](#storage-exception-for-write-on-blob) neste artigo.

## <a name="graphical-clients"></a>Clientes gráficos

Existem também várias aplicações que fornecem uma interface gráfica para trabalhar com o Armazenamento Azure. A tabela seguinte é uma lista de algumas destas aplicações:

| Cliente | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Ferramentas do Estúdio Visual microsoft para HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Explorador do Storage do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer para o Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Ciberpato](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Armazenamento do Monte Azure como unidade local

Consulte o [Armazenamento do Monte Azure como Unidade Local](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Upload usando serviços

### <a name="azure-data-factory"></a>Azure Data Factory

O serviço Azure Data Factory é um serviço totalmente gerido para a composição de dados: serviços de armazenamento, processamento e movimento em gasodutos de produção de dados simplificados, adaptáveis e fiáveis.

|Tipo de armazenamento|Documentação|
|----|----|
|Armazenamento de Blobs do Azure|[Copiar dados de ou para o armazenamento da Blob Azure utilizando a Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Armazenamento do Azure Data Lake Ger1|[Copiar dados de ou para azure Data Lake Storage Gen1 utilizando a Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Armazenamento do Azure Data Lake Ger2 |[Carregue dados no Armazenamento de Lagos Azure Data Gen2 com fábrica de dados azure](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop é uma ferramenta projetada para transferir dados entre Hadoop e bases de dados relacionais. Utilize-os para importar dados de um sistema de gestão relacional de bases de dados (RDBMS), como O Servidor SQL, MySQL ou Oracle. Em seguida, no sistema de ficheiros distribuídos hadoop (HDFS). Transforme os dados em Hadoop com MapReduce ou Hive e, em seguida, exporte os dados de volta para um RDBMS.

Para mais informações, consulte [Use Sqoop com HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>SDKs de desenvolvimento

O Armazenamento Azure também pode ser acedido usando um SDK Azure a partir dos seguintes idiomas de programação:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obter mais informações sobre a instalação dos SDKs Azure, consulte [downloads do Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="storage-exception-for-write-on-blob"></a>Exceção de armazenamento para escrita em blob

**Sintomas**: Ao `hadoop` `hdfs dfs` utilizar os comandos ou comandos para escrever ficheiros que sejam ~12 GB ou maiores num cluster HBase, pode deparar-se com o seguinte erro:

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

**Causa**: HBase em clusters HDInsight padrão para um tamanho de bloco de 256 KB ao escrever para armazenamento Azure. Enquanto funciona para APIs de Base HBase ou APIs `hadoop` `hdfs dfs` REST, resulta num erro ao utilizar os utilitários ou linha de comando.

**Resolução** `fs.azure.write.request.size` : Utilize para especificar um tamanho maior do bloco. Pode fazer esta modificação por utilização `-D` utilizando o parâmetro. O seguinte comando é um exemplo `hadoop` utilizando este parâmetro com o comando:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Também pode aumentar o `fs.azure.write.request.size` valor global usando Apache Ambari. Os seguintes passos podem ser utilizados para alterar o valor na UI Web ambari:

1. No seu navegador, vá à Ambari Web UI para o seu cluster. O URL `https://CLUSTERNAME.azurehdinsight.net`é, onde `CLUSTERNAME` está o nome do seu cluster. Quando solicitado, introduza o nome administrativo e a palavra-passe para o cluster.
2. Do lado esquerdo do ecrã, selecione **HDFS**e, em seguida, selecione o separador **Configs.**
3. No **campo do Filtro...** entra. `fs.azure.write.request.size`
4. Mude o valor de 262144 (256 KB) para o novo valor. Por exemplo, 4194304 (4 MB).

    ![Imagem de alteração do valor através da Ambari Web UI](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Para obter mais informações sobre a utilização de Ambari, consulte [Gerir os clusters HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Passos seguintes

Agora que entende como obter dados no HDInsight, leia os seguintes artigos para aprender a análise:

* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Submeta os empregos apache Hadoop programáticamente](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Use o Porco Apache com HDInsight](./use-pig.md)
