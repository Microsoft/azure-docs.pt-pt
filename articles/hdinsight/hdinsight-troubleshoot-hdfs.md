---
title: Sessão de problemas HDFS em Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre trabalhar com hDFS e Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: 6de9e31c3e79f6d704ef8b4749d41329dcc0bddb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190690"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Resolver problemas do HDFS do Apache Hadoop com o Azure HDInsight

Aprenda as principais questões e resoluções ao trabalhar com o Sistema de Ficheiros Distribuídos Hadoop (HDFS). Para obter uma lista completa de comandos, consulte o Guia de [Comandos HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) e o Guia de [Conchado do Sistema](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)de Ficheiros .

## <a name="how-do-i-access-the-local-hdfs-from-inside-a-cluster"></a><a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Como posso aceder ao HDFS local de dentro de um aglomerado?

### <a name="issue"></a>Problema

Aceda ao HDFS local a partir da linha de comando e código de aplicação em vez de utilizar o armazenamento de Azure Blob ou o Armazenamento de Lagos De Dados Azure a partir do interior do cluster HDInsight.

### <a name="resolution-steps"></a>Passos de resolução

1. No pedido de `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` comando, use literalmente, como no seguinte comando:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. A partir do código `hdfs://mycluster/` fonte, utilize o URI literalmente, como na seguinte aplicação da amostra:

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

3. Executar o ficheiro .jar compilado (por `java-unit-tests-1.0.jar`exemplo, um ficheiro nomeado ) no cluster HDInsight com o seguinte comando:

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="storage-exception-for-write-on-blob"></a>Exceção de armazenamento para escrita em blob

### <a name="issue"></a>Problema

Ao utilizar `hadoop` `hdfs dfs` os comandos ou comandos para escrever ficheiros que sejam de ~12 GB ou maiores num cluster HBase, pode deparar-se com o seguinte erro:

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

HBase em clusters HDInsight padrão para um tamanho de bloco de 256 KB ao escrever para armazenamento Azure. Enquanto funciona para APIs de Base HBase ou APIs `hadoop` `hdfs dfs` REST, resulta num erro ao utilizar os utilitários ou linha de comando.

### <a name="resolution"></a>Resolução

Utilize `fs.azure.write.request.size` para especificar um tamanho maior do bloco. Pode fazer esta modificação por utilização `-D` utilizando o parâmetro. O seguinte comando é um exemplo `hadoop` utilizando este parâmetro com o comando:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Também pode aumentar o `fs.azure.write.request.size` valor global usando Apache Ambari. Os seguintes passos podem ser utilizados para alterar o valor na UI Web ambari:

1. No seu navegador, vá à Ambari Web UI para o seu cluster. O URL `https://CLUSTERNAME.azurehdinsight.net`é, onde `CLUSTERNAME` está o nome do seu cluster. Quando solicitado, introduza o nome administrativo e a palavra-passe para o cluster.
2. Do lado esquerdo do ecrã, selecione **HDFS**e, em seguida, selecione o separador **Configs.**
3. No **campo do Filtro...** entra. `fs.azure.write.request.size`
4. Mude o valor de 262144 (256 KB) para o novo valor. Por exemplo, 4194304 (4 MB).

    ![Imagem de alteração do valor através da Ambari Web UI](./media/hdinsight-troubleshoot-hdfs/hbase-change-block-write-size.png)

Para obter mais informações sobre a utilização de Ambari, consulte [Gerir os clusters HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

## <a name="du"></a>du

O [`-du`](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du) comando exibe tamanhos de ficheiros e diretórios contidos no determinado diretório ou o comprimento de um ficheiro no caso de ser apenas um ficheiro.

A `-s` opção produz um resumo agregado de comprimentos de ficheiro que estão a ser apresentados.  
A `-h` opção formata os tamanhos dos ficheiros.

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

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
