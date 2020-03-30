---
title: Sessão de problemas HDFS em Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre trabalhar com hDFS e Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/30/2019
ms.custom: seodec18
ms.openlocfilehash: 6b0a81a2f3af10a1e5ad60c6c33357a6e906ee47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895252"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Resolver problemas do HDFS do Apache Hadoop com o Azure HDInsight

Conheça os principais problemas e as suas resoluções ao trabalhar com as cargas do Hadoop Distributed File System (HDFS) em Apache Ambari. Para obter uma lista completa de comandos, consulte o Guia de [Comandos HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) e o Guia de [Conchado do Sistema](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)de Ficheiros .

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

## <a name="du"></a>du

O comando [-du](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du) exibe tamanhos de ficheiros e diretórios contidos no determinado diretório ou o comprimento de um ficheiro no caso de ser apenas um ficheiro.

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
