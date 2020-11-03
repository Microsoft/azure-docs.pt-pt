---
title: Resolução de problemas Colmeia usando Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre trabalhar com Apache Hive e Azure HDInsight.
keywords: Azure HDInsight, Hive, FAQ, guia de resolução de problemas, questões comuns
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: f1a26e3323e4d1db2e9b2bda9afaa2756307749b
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288959"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Troubleshoot Apache Hive by using Azure HDInsight (Utilizar o Azure HDInsight para resolver problemas do Apache Hive)

Conheça as principais questões e as suas resoluções ao trabalhar com as cargas da Apache Hive em Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Como exporto uma metásta de Colmeia e a importo noutro cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Ligue-se ao cluster HDInsight utilizando um cliente Secure Shell (SSH). Para mais informações, consulte [leitura adicional.](#additional-reading-end)

2. Executar o seguinte comando no cluster HDInsight a partir do qual pretende exportar a meta-loja:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Este comando gera um ficheiro chamado allatables.sql.

3. Copie o ficheiro alltables.sql para o novo cluster HDInsight e, em seguida, executar o seguinte comando:

    ```apache
    hive -f alltables.sql
    ```

O código nas etapas de resolução pressupõe que os caminhos de dados no novo cluster são os mesmos que os caminhos de dados no antigo cluster. Se os caminhos de dados forem diferentes, pode editar manualmente o ficheiro gerado `alltables.sql` para refletir quaisquer alterações.

### <a name="additional-reading"></a>Leitura adicional

- [Conecte-se a um cluster HDInsight utilizando O SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Como localizo os troncos da Colmeia num aglomerado?

### <a name="resolution-steps"></a>Passos de resolução

1. Ligue-se ao cluster HDInsight utilizando SSH. Para mais informações, consulte **leitura adicional.**

2. Para visualizar os registos do cliente da Hive, utilize o seguinte comando:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Para ver os registos de metástasias da Hive, utilize o seguinte comando:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Para visualizar os registos do servidor Hive, utilize o seguinte comando:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Leitura adicional

- [Conecte-se a um cluster HDInsight utilizando O SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Como lanço a concha da Colmeia com configurações específicas num cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Especifique um par de valores-chave de configuração quando iniciar a casca de Colmeia. Para mais informações, consulte [leitura adicional.](#additional-reading-end)

   ```apache
   hive -hiveconf a=b
   ```

2. Para listar todas as configurações eficazes na casca da Colmeia, utilize o seguinte comando:

   ```apache
   hive> set;
   ```

   Por exemplo, utilize o seguinte comando para iniciar a casca de Colmeia com registo de depurado ativado na consola:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Leitura adicional

- [Propriedades de configuração de colmeia](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-apache-tez-dag-data-on-a-cluster-critical-path"></a><a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Como analiso os dados do Apache Tez DAG num caminho crítico de cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Para analisar um gráfico acicílico dirigido a Apache Tez (DAG) num gráfico crítico de cluster, conecte-se ao cluster HDInsight utilizando o SSH. Para mais informações, consulte [leitura adicional.](#additional-reading-end)

2. Com um pedido de comando, executar o seguinte comando:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Para listar outros analisadores que podem ser usados para analisar o Tez DAG, utilize o seguinte comando:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Deve fornecer um programa de exemplo como primeiro argumento.

   Os nomes válidos do programa incluem:
    - **ContainerReuseAnalyzer** : Imprimir detalhes de reutilização de contentores num DAG
    - **CriticalPath** : Encontre o caminho crítico de um DAG
    - **LocalidadeAnalyzer** : Imprima detalhes da localidade num DAG
    - **ShuffleTimeAnalyzer** : Analise os detalhes do tempo de baralhar num DAG
    - **SkewAnalyzer** : Analise os detalhes distorcidos num DAG
    - **SlowNodeAnalyzer** : Imprima detalhes do nó num DAG
    - **SlowTaskIdentifier** : Imprima detalhes de tarefas lentas num DAG
    - **SlowestVertexAnalyzer** : Imprima detalhes vértuosos mais lentos num DAG
    - **SpillAnalyzer** : Imprima detalhes de derrame num DAG
    - **TaskConcurrencyAnalyzer** : Imprima os detalhes da conusciência da tarefa num DAG
    - **VertexLevelCriticalPathAnalyzer** : Encontre o caminho crítico ao nível do vértice num DAG

### <a name="additional-reading"></a>Leitura adicional

- [Conecte-se a um cluster HDInsight utilizando O SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Como faço para descarregar os dados do Tez DAG de um cluster?

#### <a name="resolution-steps"></a>Passos de resolução

Existem duas formas de recolher os dados do Tez DAG:

- Da linha de comandos:

    Ligue-se ao cluster HDInsight utilizando SSH. Na pronta do comando, executar o seguinte comando:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Utilize a vista Ambari Tez:

  1. Vai a Ambari.
  2. Vá à vista Tez (sob o ícone de azulejos no canto superior direito).
  3. Selecione o DAG que pretende visualizar.
  4. Selecione **Baixar os dados**.

### <a name="additional-reading"></a><a name="additional-reading-end"></a>Leitura adicional

[Conecte-se a um cluster HDInsight utilizando O SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]