---
title: Resolução de problemas HDFS em Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre trabalhar com HDFS e Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: c725e7b4e7af33181cb616d2bce2ae19521a5e7d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871882"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Resolver problemas do HDFS do Apache Hadoop com o Azure HDInsight

Aprenda os principais problemas e resoluções ao trabalhar com o Hadoop Distributed File System (HDFS). Para obter uma lista completa de comandos, consulte o Guia de [Comandos HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) e o Guia de [Conchas do Sistema de Ficheiros](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html).

## <a name="how-do-i-access-the-local-hdfs-from-inside-a-cluster"></a><a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Como acesso o HDFS local de dentro de um aglomerado?

### <a name="issue"></a>Problema

Aceda ao HDFS local a partir da linha de comando e código de aplicação em vez de utilizar o armazenamento Azure Blob ou o Armazenamento do Lago de Dados Azure a partir de dentro do cluster HDInsight.

### <a name="resolution-steps"></a>Passos de resolução

1. Na origem do comando, use `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` literalmente, como no seguinte comando:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. A partir do código-fonte, utilize o URI `hdfs://mycluster/` literalmente, como na seguinte aplicação da amostra:

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Executar o ficheiro de .jar compilado (por exemplo, um ficheiro `java-unit-tests-1.0.jar` nomeado) no cluster HDInsight com o seguinte comando:

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="storage-exception-for-write-on-blob"></a>Exceção de armazenamento para escrita no blob

### <a name="issue"></a>Problema

Ao utilizar os `hadoop` `hdfs dfs` ou comandos para escrever ficheiros que sejam ~12 GB ou maiores num cluster HBase, poderá encontrar o seguinte erro:

```error
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
```

### <a name="cause"></a>Causa

HBase em clusters HDInsight predefinidos a um tamanho de bloco de 256 KB ao escrever para armazenamento Azure. Embora funcione para APIs HBase ou REST APIs, resulta num erro ao utilizar os `hadoop` utilitários ou `hdfs dfs` serviços de linha de comando.

### <a name="resolution"></a>Resolução

Utilize `fs.azure.write.request.size` para especificar um tamanho de bloco maior. Pode fazer esta modificação por utilização utilizando o `-D` parâmetro. O seguinte comando é um exemplo utilizando este parâmetro com o `hadoop` comando:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Você também pode aumentar o valor de `fs.azure.write.request.size` globalmente usando Apache Ambari. Os seguintes passos podem ser usados para alterar o valor na UI web Ambari:

1. No seu navegador, vá ao Ambari Web UI para o seu cluster. A URL `https://CLUSTERNAME.azurehdinsight.net` é, onde `CLUSTERNAME` está o nome do seu aglomerado. Quando solicitado, insira o nome de administração e a palavra-passe para o cluster.
2. Do lado esquerdo do ecrã, selecione **HDFS** e, em seguida, selecione o **separador Configs.**
3. No **campo Filtro...** `fs.azure.write.request.size` enter, enter.
4. Altere o valor de 262144 (256 KB) para o novo valor. Por exemplo, 4194304 (4 MB).

    :::image type="content" source="./media/hdinsight-troubleshoot-hdfs/hbase-change-block-write-size.png" alt-text="Imagem de mudar o valor através da Ambari Web UI" border="false":::

Para obter mais informações sobre a utilização de Ambari, consulte [gerir os clusters HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

## <a name="du"></a>du

O [`-du`](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du) comando exibe tamanhos de ficheiros e diretórios contidos no diretório dado ou no comprimento de um ficheiro no caso de ser apenas um ficheiro.

A `-s` opção produz um resumo agregado dos comprimentos dos ficheiros que estão a ser apresentados.  
A `-h` opção forma os tamanhos dos ficheiros.

Exemplo:

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>rm

O comando [-rm](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) elimina ficheiros especificados como argumentos.

Exemplo:

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]