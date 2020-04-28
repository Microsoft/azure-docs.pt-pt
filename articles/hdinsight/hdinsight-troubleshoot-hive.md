---
title: Problemas de saque colmeia usando Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre trabalhar com Apache Hive e Azure HDInsight.
keywords: Azure HDInsight, Hive, FAQ, guia de resolução de problemas, questões comuns
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 02247adb9852a72b386feb2ef0924b0f1b3d6277
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895225"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Troubleshoot Apache Hive by using Azure HDInsight (Utilizar o Azure HDInsight para resolver problemas do Apache Hive)

Conheça as principais questões e as suas resoluções ao trabalhar com as cargas da Apache Hive em Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Como exporto uma metaloja da Colmeia e a importei noutro cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Ligue-se ao cluster HDInsight utilizando um cliente Secure Shell (SSH). Para mais informações, consulte [Leitura adicional](#additional-reading-end).

2. Execute o seguinte comando no cluster HDInsight a partir do qual pretende exportar a metaloja:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Este comando gera um ficheiro chamado allatables.sql.

3. Copie o ficheiro alltables.sql para o novo cluster HDInsight e, em seguida, execute o seguinte comando:

    ```apache
    hive -f alltables.sql
    ```

O código nos passos de resolução pressupõe que os caminhos de dados no novo cluster são os mesmos que os caminhos de dados do antigo cluster. Se os caminhos dos dados forem diferentes, pode editar manualmente o ficheiro gerado `alltables.sql` para refletir quaisquer alterações.

### <a name="additional-reading"></a>Leitura adicional

- [Ligue-se a um cluster HDInsight utilizando o SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Como posso localizar os troncos da Hive num aglomerado?

### <a name="resolution-steps"></a>Passos de resolução

1. Ligue-se ao cluster HDInsight utilizando o SSH. Para mais informações, consulte **Leitura adicional**.

2. Para visualizar os registos dos clientes da Hive, utilize o seguinte comando:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Para visualizar os registos da metastore da Hive, utilize o seguinte comando:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Para visualizar os registos do servidor da Hive, utilize o seguinte comando:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Leitura adicional

- [Ligue-se a um cluster HDInsight utilizando o SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Como lanço a concha da Colmeia com configurações específicas num cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Especifique um par de valor de chave de configuração quando iniciar a concha da Colmeia. Para mais informações, consulte [Leitura adicional](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Para enumerar todas as configurações eficazes na concha da Colmeia, utilize o seguinte comando:

   ```apache
   hive> set;
   ```

   Por exemplo, utilize o seguinte comando para iniciar a concha da Hive com o registo de depuração ativado na consola:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Leitura adicional

- [Propriedades de configuração da colmeia](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-apache-tez-dag-data-on-a-cluster-critical-path"></a><a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Como analiso os dados do Apache Tez DAG num caminho crítico de cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Para analisar um Apache Tez dirigido a um gráfico acíclico (DAG) num gráfico crítico de cluster, ligue-se ao cluster HDInsight utilizando o SSH. Para mais informações, consulte [Leitura adicional](#additional-reading-end).

2. Num pedido de comando, execute o seguinte comando:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Para listar outros analisadores que podem ser usados para analisar tez DAG, use o seguinte comando:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Deve fornecer um programa de exemplo como o primeiro argumento.

   Os nomes de programas válidos incluem:
    - **ContainerReuseAnalyzer**: Imprimir os detalhes de reutilização do recipiente num DAG
    - **CriticalPath**: Encontre o caminho crítico de um DAG
    - **LocalityAnalyzer**: Imprimir detalhes da localidade num DAG
    - **ShuffleTimeAnalyzer**: Analise os detalhes do tempo de baralhar num DAG
    - **SkewAnalyzer**: Analise os detalhes distorcidos num DAG
    - **SlowNodeAnalyzer**: Imprimir detalhes do nó num DAG
    - **SlowTaskIdentifier**: Imprimir detalhes de tarefa lenta num DAG
    - **VertexAnalyzer mais lento**: Imprimir os detalhes do vértice mais lento num DAG
    - **SpillAnalyzer**: Imprimir detalhes do derrame num DAG
    - **TaskConcurrencyAnalyzer**: Imprima os detalhes da conmoeda de tarefa num DAG
    - **VertexLevelCriticalPathAnalyzer**: Encontre o caminho crítico ao nível do vértice num DAG

### <a name="additional-reading"></a>Leitura adicional

- [Ligue-se a um cluster HDInsight utilizando o SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Como posso descarregar dados do Tez DAG de um cluster?

#### <a name="resolution-steps"></a>Passos de resolução

Existem duas formas de recolher os dados do Tez DAG:

- Da linha de comandos:

    Ligue-se ao cluster HDInsight utilizando o SSH. No pedido de comando, executar o seguinte comando:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Use a vista Ambari Tez:

  1. Vai a Ambari.
  2. Vá à vista tez (sob o ícone dos azulejos no canto superior direito).
  3. Selecione o DAG que pretende ver.
  4. Selecione **dados de descarregamento**.

### <a name="additional-reading"></a><a name="additional-reading-end"></a>Leitura adicional

[Ligue-se a um cluster HDInsight utilizando o SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

- Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
