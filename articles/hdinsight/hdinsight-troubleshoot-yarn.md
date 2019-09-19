---
title: Resolver problemas de YARN no HDInsight do Azure
description: Obtenha respostas a perguntas comuns sobre como trabalhar com o Apache Hadoop YARN e o Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 107230a4d8c2e0427c2fcf2a8862559c707831a1
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076574"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Solucionar problemas Apache Hadoop YARN usando o Azure HDInsight

Saiba mais sobre os principais problemas e resolução ao trabalhar com payloads de Apache Hadoop YARN no Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Como posso criar uma nova fila YARN num cluster?

### <a name="resolution-steps"></a>Passos de resolução

Utilize os seguintes passos Ambari para criar uma nova fila do YARN e, em seguida, equilibrar a alocação de capacidade entre todas as filas.

Neste exemplo, dois filas existentes (**predefinição** e **thriftsvr**) ambos forem alteradas, da capacidade de 50%, a capacidade de 25%, o que lhe dá a nova capacidade de 50% da fila (spark).

| Filas | Capacidade | Capacidade máxima |
| --- | --- | --- |
| predefinição | 25% | 50% |
| thrftsvr | 25% | 50% |
| Spark | 50% | 50% |

1. Selecione o **vistas Ambari** ícone e, em seguida, selecione o padrão de grade. Em seguida, selecione **Gestor de filas do YARN**.

    ![Selecione o ícone de vistas do Ambari](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Selecione o **predefinição** fila.

    ![Selecione a fila padrão](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. Para o **predefinição** colocar em fila, altere a **capacidade** de 50% a 25%. Para o **thriftsvr** colocar em fila, altere a **capacidade** para 25%.

    ![Alterar a capacidade para 25% para as filas de padrão e thriftsvr](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Para criar uma nova fila, selecione **adicionar fila**.

    ![Selecione adicionar fila](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Nome da nova fila.

    ![Nome da fila de Spark](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Deixe o **capacidade** valores em 50% e, em seguida, selecione a **ações** botão.

    ![Selecione o botão de ações](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Selecione **guarde e Atualize as filas**.

    ![Selecione salvar e Atualize as filas](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

Estas alterações são visíveis imediatamente na IU de agendador do YARN.

### <a name="additional-reading"></a>Leitura adicional

- [Apache Hadoop YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Como transferir registos do YARN a partir de um cluster?

### <a name="resolution-steps"></a>Passos de resolução 

1. Ligar ao cluster do HDInsight com um cliente Secure Shell (SSH). Para obter mais informações, consulte [leitura adicional](#additional-reading-2).

1. Para listar todos os IDs de aplicação das aplicações do YARN que estão atualmente em execução, execute o seguinte comando:

    ```apache
    yarn top
    ```

    As identificações são listadas na **APPLICATIONID** coluna. Pode transferir registos a partir da **APPLICATIONID** coluna.

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

1. Para transferir os registos de contentor do YARN para todos os principais de aplicação, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Este comando cria um ficheiro de registo com o nome amlogs.txt.

1. Para transferir os registos de contentor do YARN para apenas o mestre de aplicação mais recente, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Este comando cria um ficheiro de registo com o nome latestamlogs.txt.

1. Para transferir os registos de contentor do YARN para modelos de estrutura mestres primeiro dois aplicativos, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Este comando cria um ficheiro de registo com o nome first2amlogs.txt.

1. Para transferir todos os registos de contentor do YARN, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Este comando cria um ficheiro de registo com o nome logs.txt.

1. Para transferir o registo de contentor do YARN para um contentor específico, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Este comando cria um ficheiro de registo com o nome containerlogs.txt.

### <a name="additional-reading-2"></a>Leitura adicional

- [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Conceitos do Apache Hadoop YARN e aplicações](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

- Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

- Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
