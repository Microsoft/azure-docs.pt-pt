---
title: Guias de resolução de problemas Azure HDInsight
description: Resolução de problemas Apache Hadoop cargas de trabalho usando Azure HDInsight. A documentação passo a passo mostra-lhe como usar o HDInsight para resolver problemas comuns com Apache Hive, Apache Spark, Apache YARN, Apache HBase, HDFS e Apache Storm.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 12448d3e5e77d05f3b8fbd474179c4fcf7c57acc
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535081"
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Resolução de problemas utilizando Azure HDInsight

| Carga de trabalho apache | Principais perguntas? |
|---|---|
|![hdinsight apache HBase ícone ícone](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hbase.png)<br>[Resolução de problemas do Apache HBase]()|<br>[Regiões não-assinadas](hbase/hbase-troubleshoot-unassigned-regions.md#scenario-unassigned-regions)<br><br>[Tempos limite com o comando “hbase hbck” no Azure HDInsight](hbase/hbase-troubleshoot-timeouts-hbase-hbck.md)<br><br>[Problemas de conectividade Apache Phoenix em Azure HDInsight](hbase/hbase-troubleshoot-phoenix-connectivity.md)<br><br>[O que faz com que um servidor principal não arranque?](hbase/hbase-troubleshoot-start-fails.md)<br><br>[BindException - Endereço já em uso](hbase/hbase-troubleshoot-bindexception-address-use.md)|
|![hdinsight apache hdfs ícone ícone](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hdfs.png)<br>[Resolução de problemas Apache Hadoop HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Como acesso um HDFS local de dentro de um aglomerado?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[HDFS local preso em modo de segurança no cluster Azure HDInsight](hadoop/hdinsight-hdfs-troubleshoot-safe-mode.md)|
|![hdinsight apache ícone de colmeia](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hive.png)<br>[Resolução de problemas Apache Hive](hdinsight-troubleshoot-hive.md)|<br>[Como exporto uma metásta de Colmeia e a importo noutro cluster?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Como localizo os troncos da Colmeia Apache num aglomerado?](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Como lanço a concha da Colmeia Apache com configurações específicas num aglomerado?](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Como analiso os dados do Apache Tez DAG num caminho crítico de cluster?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Como faço o download de dados do Apache Tez DAG de um cluster?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![hdinsight apache ícone ícone faísca](./media/hdinsight-troubleshoot-guide/hdinsight-apache-spark.png)<br>[Resolução de problemas Apache Spark](./spark/apache-troubleshoot-spark.md)|<br>[Como configuro uma aplicação Apache Spark através do Apache Ambari em clusters?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters)<br><br>[Como configuro uma aplicação Apache Spark através de um bloco de notas Jupyter em clusters?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Como configuro uma aplicação Apache Spark através do Apache Livy em clusters?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters)<br><br>[Como configuro uma aplicação Apache Spark através de spark-submit em clusters?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters)<br><br>[Como posso configurar uma aplicação Apache Spark utilizando o IntelliJ?](spark/apache-spark-intellij-tool-plugin.md)<br><br>[Como posso configurar uma aplicação Apache Spark usando Eclipse?](spark/apache-spark-eclipse-tool-plugin.md)<br><br>[Como posso configurar uma aplicação Apache Spark utilizando o VSCode?](hdinsight-for-vscode.md)<br><br>[Exceção outOfMemoryError para a Faísca Apache](spark/apache-spark-troubleshoot-outofmemory.md#scenario-outofmemoryerror-exception-for-apache-spark)|
|![hdinsight apache ícone ícone tempestade](./media/hdinsight-troubleshoot-guide/hdinsight-apache-storm.png)<br>[Resolução de problemas Tempestade Apache](./storm/apache-troubleshoot-storm.md)|<br>[Como acesso à UI da Tempestade Apache num aglomerado?](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Como transfira informação do centro de verificação do centro de testes da Apache Storm de uma topologia para outra?](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Como localizo binários de tempestade num aglomerado?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Como determino a topologia de implantação de um aglomerado de tempestades?](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Como localizo os binários do centro de eventos da Apache Storm para o desenvolvimento?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)|
|![hdinsight apache yarn ícone ícone](./media/hdinsight-troubleshoot-guide/hdinsight-apache-yarn.png)<br>[Resolução de problemas Apache Hadoop YARN](hdinsight-troubleshoot-YARN.md)|<br>[Como posso criar uma nova fila de YARN Apache Hadoop num aglomerado?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Como faço para descarregar os registos de YARN apache Hadoop de um cluster?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Recursos de resolução de problemas hdInsight

| Para obter informações sobre | Veja estes artigos |
| --- | --- |
| HDInsight no Linux e otimização | - [Informação sobre a utilização do HDInsight no Linux](hdinsight-hadoop-linux-information.md)<br>- [Memória de Apache Hadoop e resolução de problemas de desempenho](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Desempenho da consulta da Colmeia Apache](https://web.archive.org/web/20190217214250/https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Troncos e despejos | - [Aceder a registos de aplicações apache Hadoop YARN no Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Permitir despejos de pilhas para serviços Apache Hadoop em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)|
| Erros | - [Compreender e resolver erros do WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Configurações da Colmeia Apache para corrigir erro outofMemory](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Ferramentas | - [Otimizar consultas de Colmeia Apache](hdinsight-hadoop-optimize-hive-query.md)<br>- [Ferramenta HDInsight IntelliJ](./spark/apache-spark-intellij-tool-plugin.md)<br>- [Ferramenta hdInsight Eclipse](./spark/apache-spark-eclipse-tool-plugin.md)<br>- [Ferramenta HDInsight VSCode](hdinsight-for-vscode.md)<br>- [Ferramenta HDInsight Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md) |

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
