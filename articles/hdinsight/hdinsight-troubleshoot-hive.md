---
title: Solucionar problemas do hive usando o Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre como trabalhar com o Apache Hive e o Azure HDInsight.
keywords: Azure HDInsight, Hive, perguntas frequentes, guia de solução de problemas, perguntas comuns
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 02247adb9852a72b386feb2ef0924b0f1b3d6277
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895225"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Solucionar problemas Apache Hive usando o Azure HDInsight

Saiba mais sobre as principais perguntas e suas resoluções ao trabalhar com cargas de Apache Hive no Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Como fazer exportar um metastore do Hive e importá-lo em outro cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Ligar ao cluster do HDInsight com um cliente Secure Shell (SSH). Para obter mais informações, consulte [leitura adicional](#additional-reading-end).

2. Execute o seguinte comando no cluster HDInsight do qual você deseja exportar o metastore:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Esse comando gera um arquivo chamado allatables. Sql.

3. Copie o arquivo TableName. SQL para o novo cluster HDInsight e, em seguida, execute o seguinte comando:

    ```apache
    hive -f alltables.sql
    ```

O código nas etapas de resolução pressupõe que os caminhos de dados no novo cluster são iguais aos caminhos de dados no cluster antigo. Se os caminhos de dados forem diferentes, você poderá editar manualmente o arquivo de `alltables.sql` gerado para refletir as alterações.

### <a name="additional-reading"></a>Leitura adicional

- [Conectar-se a um cluster HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Como fazer localizar logs do hive em um cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Conecte-se ao cluster HDInsight usando SSH. Para obter mais informações, consulte **leitura adicional**.

2. Para exibir os logs do cliente do hive, use o seguinte comando:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Para exibir os logs de metastore do Hive, use o seguinte comando:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Para exibir os logs do servidor do hive, use o seguinte comando:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Leitura adicional

- [Conectar-se a um cluster HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Como fazer iniciar o Shell do hive com configurações específicas em um cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Especifique um par chave-valor de configuração ao iniciar o Shell do hive. Para obter mais informações, consulte [leitura adicional](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Para listar todas as configurações efetivas no Shell do hive, use o seguinte comando:

   ```apache
   hive> set;
   ```

   Por exemplo, use o seguinte comando para iniciar o Shell do hive com o log de depuração habilitado no console do:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Leitura adicional

- [Propriedades de configuração do hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Como fazer analisar Apache Tez dados do DAG em um caminho crítico do cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Para analisar Apache Tez um grafo de acíclico direcionado (DAG) em um grafo de cluster crítico, conecte-se ao cluster HDInsight usando SSH. Para obter mais informações, consulte [leitura adicional](#additional-reading-end).

2. Em um prompt de comando, execute o seguinte comando:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Para listar outros analisadores que podem ser usados para analisar tez DAG, use o seguinte comando:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Você deve fornecer um programa de exemplo como o primeiro argumento.

   Os nomes de programa válidos incluem:
    - **ContainerReuseAnalyzer**: imprimir detalhes de reutilização de contêiner em um DAG
    - **CriticalPath**: localizar o caminho crítico de um DAG
    - **LocalityAnalyzer**: imprimir detalhes de localidade em um DAG
    - **ShuffleTimeAnalyzer**: analisar os detalhes de tempo de embaralhamento em um DAG
    - **SkewAnalyzer**: analisar os detalhes de distorção em um DAG
    - **SlowNodeAnalyzer**: imprimir detalhes do nó em um DAG
    - **SlowTaskIdentifier**: imprimir detalhes da tarefa lenta em um DAG
    - **SlowestVertexAnalyzer**: imprimir detalhes de vértice mais lentos em um DAG
    - **SpillAnalyzer**: imprimir detalhes de despejo em um DAG
    - **TaskConcurrencyAnalyzer**: imprimir os detalhes de simultaneidade da tarefa em um DAG
    - **VertexLevelCriticalPathAnalyzer**: localizar o caminho crítico no nível de vértice em um DAG

### <a name="additional-reading"></a>Leitura adicional

- [Conectar-se a um cluster HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Como fazer baixar dados do tez DAG de um cluster?

#### <a name="resolution-steps"></a>Passos de resolução

Há duas maneiras de coletar os dados do tez DAG:

- Da linha de comandos:

    Conecte-se ao cluster HDInsight usando SSH. No prompt de comando, execute o seguinte comando:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Use a exibição Ambari tez:

  1. Vá para Ambari.
  2. Acesse a exibição tez (sob o ícone de blocos no canto superior direito).
  3. Selecione o DAG que você deseja exibir.
  4. Selecione **baixar dados**.

### <a name="additional-reading-end"></a>Leitura adicional

[Conectar-se a um cluster HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

- Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
