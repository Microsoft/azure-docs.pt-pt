---
title: Carregar dados para trabalhos de Apache Hadoop no HDInsight
description: Saiba como carregar e acessar dados para Apache Hadoop trabalhos no HDInsight usando a CLI clássica do Azure, Gerenciador de Armazenamento do Azure, Azure PowerShell, a linha de comando do Hadoop ou Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdiseo17may2017
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 7eb1f7e1ce02a30f84cb520438f60fcbcfa3a965
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100137"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Carregar dados para trabalhos de Apache Hadoop no HDInsight

O Azure HDInsight fornece um HDFS (sistema de arquivos distribuído Hadoop) completo no armazenamento do Azure e Azure Data Lake Storage (Gen1 e Gen2). O armazenamento do Azure e o Data Lake Storage Gen1 e o Gen2 são projetados como extensões HDFS para fornecer uma experiência direta aos clientes. Eles permitem que o conjunto completo de componentes no ecossistema do Hadoop opere diretamente nos dados que ele gerencia. O armazenamento do Azure, Data Lake Storage Gen1 e Gen2 são sistemas de arquivos distintos que são otimizados para armazenamento de dados e computações nesses dados. Para obter informações sobre os benefícios de usar o armazenamento do Azure, consulte [usar o armazenamento do Azure com o hdinsight](hdinsight-hadoop-use-blob-storage.md), [usar data Lake Storage Gen1 com o hdinsight](hdinsight-hadoop-use-data-lake-store.md)e usar o [Data Lake Storage Gen2 com o hdinsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Pré-requisitos

Observe os seguintes requisitos antes de começar:

* Um cluster do Azure HDInsight. Para obter instruções, consulte Introdução [ao Azure hdinsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) ou [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Conhecimento dos seguintes artigos:
    * [Usar o armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Usar o Data Lake Storage Gen1 com o HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Usar o Data Lake Storage Gen2 com o HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Carregar dados no armazenamento do Azure

## <a name="utilities"></a>Serviços Públicos

A Microsoft fornece os seguintes utilitários para trabalhar com o armazenamento do Azure:

| Ferramenta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [O Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AZCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Comando do Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> O comando do Hadoop só está disponível no cluster HDInsight. O comando só permite carregar dados do sistema de arquivos local no armazenamento do Azure.  

## <a id="commandline"></a>Linha de comando do Hadoop

A linha de comando do Hadoop só será útil para armazenar dados no blob de armazenamento do Azure quando os dados já estiverem presentes no nó principal do cluster.

Para usar o comando do Hadoop, primeiro você deve se conectar ao cabeçalho usando [SSH ou](hdinsight-hadoop-linux-use-ssh-unix.md)de saída.

Uma vez conectado, você pode usar a sintaxe a seguir para carregar um arquivo para armazenamento.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Por exemplo, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Como o sistema de arquivos padrão do HDInsight está no armazenamento do Azure, o/example/data/data.txt está na verdade no armazenamento do Azure. Você também pode se referir ao arquivo como:

    wasbs:///example/data/data.txt

ou

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para obter uma lista de outros comandos do Hadoop que funcionam com arquivos, consulte [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Em clusters do Apache HBase, o tamanho de bloco padrão usado ao gravar dados é de 256 KB. Embora isso funcione bem ao usar APIs do HBase ou APIs REST, o uso dos comandos `hadoop` ou `hdfs dfs` para gravar dados com mais de cerca de 12 GB resultará em um erro. Para obter mais informações, consulte a seção [exceção de armazenamento para gravar no blob](#storageexception) neste artigo.

## <a name="graphical-clients"></a>Clientes gráficos

Também há vários aplicativos que fornecem uma interface gráfica para trabalhar com o armazenamento do Azure. A tabela a seguir é uma lista de alguns desses aplicativos:

| Cliente | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Ferramentas de Microsoft Visual Studio para HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Explorador do Armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer para Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Montar o armazenamento do Azure como uma unidade local

Consulte [montar o armazenamento do Azure como uma unidade local](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Carregar usando serviços

### <a name="azure-data-factory"></a>Azure Data Factory

O serviço de Azure Data Factory é um serviço totalmente gerenciado para compor serviços de armazenamento de dados, processamento de dados e movimentação de dados em pipelines de produção de dados simplificados, escalonáveis e confiáveis.

|Tipo de armazenamento|Documentação|
|----|----|
|Armazenamento de Blobs do Azure|[Copiar dados de ou para o armazenamento de BLOBs do Azure usando Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Armazenamento Ger1 do Azure Data Lake|[Copiar dados de ou para Azure Data Lake Storage Gen1 usando Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Ger2 de Armazenamento do Azure Data Lake |[Carregar dados em Azure Data Lake Storage Gen2 com Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop

O Sqoop é uma ferramenta projetada para transferir dados entre o Hadoop e bancos de dados relacionais. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacional), como SQL Server, MySQL ou Oracle no HDFS (sistema de arquivos distribuído do Hadoop), transformar os dados no Hadoop com MapReduce ou Hive e, em seguida, exportar os dados de volta para um RDBMS.

Para obter mais informações, consulte [usar o Sqoop com o HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>SDKs de desenvolvimento

O armazenamento do Azure também pode ser acessado usando um SDK do Azure das seguintes linguagens de programação:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obter mais informações sobre como instalar os SDKs do Azure, consulte [downloads do Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Resolução de problemas

### <a id="storageexception"></a>Exceção de armazenamento para gravação no blob

**Sintomas**: ao usar os comandos `hadoop` ou `hdfs dfs` para gravar arquivos que tenham aproximadamente 12 GB ou mais em um cluster HBase, você pode encontrar o seguinte erro:

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

**Causa**: o HBase em clusters HDInsight assume como padrão um tamanho de bloco de 256 KB ao gravar no armazenamento do Azure. Enquanto ele funciona para APIs do HBase ou APIs REST, ele resulta em um erro ao usar o `hadoop` ou `hdfs dfs` utilitários de linha de comando.

**Resolução**: Use `fs.azure.write.request.size` para especificar um tamanho de bloco maior. Você pode fazer isso de acordo com cada uso usando o parâmetro `-D`. O comando a seguir é um exemplo que usa esse parâmetro com o comando `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Você também pode aumentar o valor de `fs.azure.write.request.size` globalmente usando o Apache Ambari. As etapas a seguir podem ser usadas para alterar o valor na interface do usuário da Web do amAmbari:

1. No navegador, vá para a interface do usuário da Web do amAmbari para seu cluster. Isso é `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

    Quando solicitado, insira o nome do administrador e a senha para o cluster.
2. No lado esquerdo da tela, selecione **HDFS**e, em seguida, selecione a guia **configurações** .
3. No campo **Filtrar...** , insira `fs.azure.write.request.size`. Isso exibe o campo e o valor atual no meio da página.
4. Altere o valor de 262144 (256 KB) para o novo valor. Por exemplo, 4194304 (4 MB).

    ![Imagem da alteração do valor por meio da interface do usuário da Web do amAmbari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Para obter mais informações sobre como usar o Ambari, consulte [gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Passos seguintes

Agora que você entende como obter dados no HDInsight, leia os artigos a seguir para saber como executar a análise:

* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Enviar trabalhos de Apache Hadoop programaticamente](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
