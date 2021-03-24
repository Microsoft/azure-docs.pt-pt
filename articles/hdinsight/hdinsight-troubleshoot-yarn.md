---
title: Resolução de problemas YARN em Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre trabalhar com Apache Hadoop YARN e Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 0cd2571276992812327e286ba9b935fcbf6fbbaf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871814"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Resolver Problemas do YARN do Apache Hadoop com o Azure HDInsight

Conheça as principais questões e suas resoluções ao trabalhar com apache Hadoop YARN em Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Como posso criar uma nova fila de YARN num aglomerado?

### <a name="resolution-steps"></a>Passos de resolução

Use os seguintes passos em Ambari para criar uma nova fila de YARN e, em seguida, equilibrar a alocação de capacidade entre todas as filas.

Neste exemplo, duas filas existentes (**padrão** e **thriftsvr**) são alteradas de 50% de capacidade para 25%, o que dá à nova fila (faísca) capacidade de 50%.

| Fila | Capacidade | Capacidade máxima |
| --- | --- | --- |
| predefinição | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. Selecione o ícone **Ambari Views** e, em seguida, selecione o padrão da grelha. Em seguida, selecione **YARN Queue Manager**.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png" alt-text="Gestor de fila de painel Apache Ambari" border="false":::
2. Selecione a fila **predefinitiva.**

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png" alt-text="Apache Ambari YARN seleciona fila padrão" border="false":::
3. Para a fila **padrão,** altere a **capacidade** de 50% para 25%. Para a fila **thriftsvr,** mude a **capacidade** para 25%.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png" alt-text="Altere a capacidade para 25% para as filas de incumprimento e thriftsvr" border="false":::
4. Para criar uma nova fila, **selecione Add Queue**.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png" alt-text="Painel de instrumentos Apache Ambari YARN Adicionar Fila" border="false":::

5. Diga o nome da nova fila.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png" alt-text="Apache Ambari YARN nome do painel de instrumentos Queue" border="false":::  

6. Deixe os valores **de capacidade** a 50%, e, em seguida, selecione o botão **Ações.**

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png" alt-text="Apache Ambari YARN seleciona ação" border="false":::  
7. **Selecione Guardar e refrescar as filas**.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png" alt-text="Selecione Guardar e refrescar as filas" border="false":::  

Estas alterações são visíveis imediatamente na UI do Programador YARN.

### <a name="additional-reading"></a>Leitura adicional

- [Capacidade de Yarn de Apache Hadoop](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Como faço para descarregar registos de YARN de um cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Ligue-se ao cluster HDInsight utilizando um cliente Secure Shell (SSH). Para mais informações, consulte [leitura adicional.](#additional-reading-2)

1. Para listar todos os IDs de aplicação das aplicações YARN que estão atualmente em execução, executar o seguinte comando:

    ```apache
    yarn top
    ```

    Os IDs estão listados na coluna **APPLICATIONID.** Pode baixar registos a partir da coluna **APPLICATIONID.**

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Para baixar os registos de contentores YARN para todos os mestres de aplicações, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Este comando cria um ficheiro de registo chamado amlogs.txt.

1. Para baixar os registos de contentores YARN apenas para o mais recente comandante de aplicações, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Este comando cria um ficheiro de registo chamado latestamlogs.txt.

1. Para baixar os registos de contentores YARN para os dois primeiros mestres de aplicações, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Este comando cria um ficheiro de registo chamado first2amlogs.txt.

1. Para descarregar todos os registos de contentores YARN, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Este comando cria um ficheiro de registo chamado logs.txt.

1. Para descarregar o registo do contentor YARN para um recipiente específico, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Este comando cria um ficheiro de registo chamado containerlogs.txt.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>Leitura adicional

- [Ligue-se ao HDInsight (Apache Hadoop) utilizando o SSH](./hdinsight-hadoop-linux-use-ssh-unix.md)
- [Conceitos e aplicações apache Hadoop YARN](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]