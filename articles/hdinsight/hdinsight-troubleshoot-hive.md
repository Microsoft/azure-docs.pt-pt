---
title: Resolver problemas do Hive com o Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre como trabalhar com o Apache Hive e o Azure HDInsight.
keywords: FAQ do Azure HDInsight, Hive, guia, as perguntas mais comuns de resolução de problemas
services: hdinsight
ms.service: hdinsight
author: dharmeshkakadia
ms.author: dharmeshkakadia
ms.topic: conceptual
ms.date: 11/2/2017
ms.openlocfilehash: 6d75bf86dab8775e77efb21ecc3b0d60063a9823
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088966"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Resolver problemas do Apache Hive com o Azure HDInsight

Saiba mais sobre as principais perguntas e resolução ao trabalhar com o Apache Hive payloads no Apache Ambari.


## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Como exportar um metastore Hive e importá-lo noutro cluster?


### <a name="resolution-steps"></a>Passos de resolução

1. Ligar ao cluster do HDInsight com um cliente Secure Shell (SSH). Para obter mais informações, consulte [leitura adicional](#additional-reading-end).

2. Execute o seguinte comando no cluster do HDInsight a partir do qual pretende exportar o metastore:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Este comando gera um arquivo chamado allatables.sql.

3. Copie alltables.sql o ficheiro para o novo cluster do HDInsight e, em seguida, execute o seguinte comando:

   ```apache
   hive -f alltables.sql
   ```

O código nos passos de resolução pressupõe que os caminhos de dados no novo cluster são os mesmos que os caminhos de dados no cluster antigo. Se os caminhos de dados forem diferentes, pode editar manualmente o ficheiro de alltables.sql gerado para refletir as alterações.

### <a name="additional-reading"></a>Leitura adicional

- [Ligar a um cluster do HDInsight com SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Como faço para localizar os registos de Hive num cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Ligar ao cluster do HDInsight com SSH. Para obter mais informações, consulte **leitura adicional**.

2. Para ver registos de cliente do ramo de registo, utilize o seguinte comando:

   ```apache
   /tmp/<username>/hive.log 
   ```

3. Para ver registos de metastore Hive, utilize o seguinte comando:

   ```apache
   /var/log/hive/hivemetastore.log 
   ```

4. Para ver registos do Hiveserver, utilize o seguinte comando:

   ```apache
   /var/log/hive/hiveserver2.log 
   ```

### <a name="additional-reading"></a>Leitura adicional

- [Ligar a um cluster do HDInsight com SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Como posso iniciar o shell do Hive com configurações específicas num cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Especifica um par de chave-valor de configuração quando começar a Hive shell. Para obter mais informações, consulte [leitura adicional](#additional-reading-end).

   ```apache
   hive -hiveconf a=b 
   ```

2. Para listar todas as configurações em vigor no shell do Hive, utilize o seguinte comando:

   ```apache
   hive> set;
   ```

   Por exemplo, utilize o seguinte comando para iniciar o shell do Hive com o registo de depuração ativado na consola do:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console 
   ```

### <a name="additional-reading"></a>Leitura adicional

- [Propriedades de configuração do Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Como analisar os dados do Apache Tez DAG num caminho crítico para o cluster?


### <a name="resolution-steps"></a>Passos de resolução
 
1. Para analisar um Apache Tez Acíclico (DAG) num gráfico de crítico para o cluster, ligue ao cluster do HDInsight com SSH. Para obter mais informações, consulte [leitura adicional](#additional-reading-end).

2. No prompt de comando, execute o seguinte comando:
   
   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Para listar outros analisadores que podem ser utilizados para analisar Tez DAG, utilize o seguinte comando:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Tem de fornecer um programa de exemplo como o primeiro argumento.

   Nomes de programa válidos incluem:
    - **ContainerReuseAnalyzer**: Imprimir os detalhes de reutilização de contentor num DAG
    - **CriticalPath**: Localizar o caminho crítico de um DAG
    - **LocalityAnalyzer**: Detalhes de localidade de impressão num DAG
    - **ShuffleTimeAnalyzer**: Analisar os detalhes de tempo de shuffle num DAG
    - **SkewAnalyzer**: Analisar os detalhes distorção num DAG
    - **SlowNodeAnalyzer**: Detalhes do nó de impressão num DAG
    - **SlowTaskIdentifier**: Imprimir os detalhes da tarefa lenta num DAG
    - **SlowestVertexAnalyzer**: Imprima detalhes mais lentos do vértice num DAG
    - **SpillAnalyzer**: Imprima vaze detalhes num DAG
    - **TaskConcurrencyAnalyzer**: Imprimir os detalhes de simultaneidade de tarefas num DAG
    - **VertexLevelCriticalPathAnalyzer**: Localizar o caminho crítico no nível de vértice num DAG


### <a name="additional-reading"></a>Leitura adicional

- [Ligar a um cluster do HDInsight com SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Como transferir dados de Tez DAG a partir de um cluster?


#### <a name="resolution-steps"></a>Passos de resolução

Existem duas formas para recolher os dados de Tez DAG:

- Da linha de comandos:
 
    Ligar ao cluster do HDInsight com SSH. No prompt de comando, execute o seguinte comando:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
  ```

- Utilize a vista Ambari Tez:
   
  1. Vá para o Ambari. 
  2. Aceda à vista de Tez (sob o ícone de mosaicos no canto superior direito). 
  3. Selecione o DAG em que pretende visualizar.
  4. Selecione **transferir dados**.

### <a name="additional-reading-end"></a>Leitura adicional

[Ligar a um cluster do HDInsight com SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


### <a name="see-also"></a>Consultar Também
[Resolver problemas com o Azure HDInsight](hdinsight-troubleshoot-guide.md)




