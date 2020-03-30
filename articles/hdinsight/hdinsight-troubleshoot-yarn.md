---
title: Sessão de problemas yARN em Azure HDInsight
description: Obtenha respostas a perguntas comuns sobre trabalhar com o YARN Apache Hadoop e o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: f0c7b966b9fa7580809d2df0f4d05a7146ca0fd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272204"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Resolver Problemas do YARN do Apache Hadoop com o Azure HDInsight

Conheça os principais problemas e as suas resoluções ao trabalhar com as cargas do YARN Apache Hadoop em Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Como posso criar uma nova fila de Arn num aglomerado?

### <a name="resolution-steps"></a>Passos de resolução

Use os seguintes passos em Ambari para criar uma nova fila de Arn e, em seguida, equilibrar a atribuição de capacidade entre todas as filas.

Neste exemplo, duas filas existentes (**padrão** e **thriftsvr**) são alteradas de 50% de capacidade para 25% de capacidade, o que dá à nova fila (faísca) 50% de capacidade.

| Filas | Capacidade | Capacidade máxima |
| --- | --- | --- |
| predefinição | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. Selecione o ícone **Ambari Views** e, em seguida, selecione o padrão da grelha. Em seguida, selecione **YARN Queue Manager**.

    ![Apache Ambari dashboard YARN Queue Manager](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Selecione a fila **predefinida.**

    ![Apache Ambari YARN seleciona fila padrão](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. Para a fila **padrão,** altere a **capacidade** de 50% para 25%. Para a fila **thriftsvr,** mude a **capacidade** para 25%.

    ![Alterar a capacidade para 25% para as filas de incumprimento e de délversvr](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Para criar uma nova fila, selecione **Adicionar fila**.

    ![Painel de instrumentos Apache Ambari YARN Adicionar Fila](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Diga o nome da nova fila.

    ![Apache Ambari YARN nome do dashboard Queue](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Deixe os valores de **capacidade** em 50%, e, em seguida, selecione o botão **Ações.**

    ![Apache Ambari YARN seleciona ação](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Selecione **Guardar e refrescar as filas**.

    ![Selecione Guardar e refrescar filas](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

Estas alterações são visíveis imediatamente no YARN Scheduler UI.

### <a name="additional-reading"></a>Leitura adicional

- [Programador de capacidade sarn de Hadoop](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Como faço o download de registos de Arn de um cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Ligue-se ao cluster HDInsight utilizando um cliente Secure Shell (SSH). Para mais informações, consulte [Leitura adicional](#additional-reading-2).

1. Para listar todos os IDs de aplicação das aplicações YARN que estão atualmente em execução, executar o seguinte comando:

    ```apache
    yarn top
    ```

    Os IDs estão listados na coluna **APPLICATIONID.** Pode descarregar registos a partir da coluna **APPLICATIONID.**

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

1. Para descarregar registos de contentores YARN apenas para o mais recente mestre de aplicações, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Este comando cria um ficheiro de registo chamado latestamlogs.txt.

1. Para descarregar os registos de contentores YARN para os dois primeiros mestres de aplicações, utilize o seguinte comando:

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

- [Ligue-se ao HDInsight (Apache Hadoop) utilizando o SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Conceitos e aplicações yARN apache Hadoop](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

- Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
