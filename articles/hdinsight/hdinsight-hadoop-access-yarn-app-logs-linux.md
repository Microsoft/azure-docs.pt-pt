---
title: Acessar Apache Hadoop logs de aplicativo do YARN-Azure HDInsight
description: Saiba como acessar os logs de aplicativo do YARN em um cluster HDInsight baseado em Linux (Apache Hadoop) usando a linha de comando e um navegador da Web.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76764389"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Acessar Apache Hadoop logs de aplicativo do YARN no HDInsight baseado em Linux

Saiba como acessar os logs para os aplicativos [Apache HADOOP yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (ainda outro negociador de recursos) em um cluster de [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight.

## <a name="what-is-apache-yarn"></a>O que é o Apache YARN?

O YARN dá suporte a vários modelos de programação ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) sendo um deles) desacoplando o gerenciamento de recursos do agendamento/monitoramento de aplicativos. O YARN usa um *ResourceManager* global (RM), *NodeManagers* (NMS) por um nó de trabalho e por aplicativo *Gerenciador* (AMS). O aplicativo AM negocia recursos (CPU, memória, disco, rede) para executar seu aplicativo com o RM. O RM funciona com NMs para conceder esses recursos, que são concedidos como *contêineres*. A AM é responsável por acompanhar o progresso dos contêineres atribuídos a ele pelo RM. Um aplicativo pode exigir muitos contêineres, dependendo da natureza do aplicativo.

Cada aplicativo pode consistir em várias *tentativas de aplicativo*. Se um aplicativo falhar, ele poderá ser tentado novamente como uma nova tentativa. Cada tentativa é executada em um contêiner. De certa forma, um contêiner fornece o contexto para a unidade de trabalho básica executada por um aplicativo YARN. Todo o trabalho feito dentro do contexto de um contêiner é executado no nó de trabalho único no qual o contêiner foi alocado. Consulte [Hadoop: Writing yarn Applications](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)ou [Apache Hadoop yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) para referência adicional.

Para dimensionar o cluster para dar suporte a uma taxa de transferência de processamento maior, você pode usar o [dimensionamento automático](hdinsight-autoscale-clusters.md) ou [dimensionar seus clusters manualmente usando alguns idiomas diferentes](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="yarn-timeline-server"></a>Servidor de linha do tempo do fio yARN

O [Apache HADOOP yarn Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fornece informações genéricas sobre aplicativos concluídos

YARN Timeline Server inclui os seguintes tipos de dados:

* A ID do aplicativo, um identificador exclusivo de um aplicativo
* O usuário que iniciou o aplicativo
* Informações sobre tentativas feitas para concluir o aplicativo
* Os contêineres usados por qualquer tentativa de aplicativo específica

## <a name="yarn-applications-and-logs"></a>Aplicações e registos de ARN

O YARN dá suporte a vários modelos de programação ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) sendo um deles) desacoplando o gerenciamento de recursos do agendamento/monitoramento de aplicativos. O YARN usa um *ResourceManager* global (RM), *NodeManagers* (NMS) por um nó de trabalho e por aplicativo *Gerenciador* (AMS). O aplicativo AM negocia recursos (CPU, memória, disco, rede) para executar seu aplicativo com o RM. O RM funciona com NMs para conceder esses recursos, que são concedidos como *contêineres*. A AM é responsável por acompanhar o progresso dos contêineres atribuídos a ele pelo RM. Um aplicativo pode exigir muitos contêineres, dependendo da natureza do aplicativo.

Cada aplicativo pode consistir em várias *tentativas de aplicativo*. Se um aplicativo falhar, ele poderá ser tentado novamente como uma nova tentativa. Cada tentativa é executada em um contêiner. De certa forma, um contêiner fornece o contexto para a unidade de trabalho básica executada por um aplicativo YARN. Todo o trabalho feito dentro do contexto de um contêiner é executado no nó de trabalho único no qual o contêiner foi alocado. Consulte [Apache Hadoop conceitos de yarn](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) para referência adicional.

Os logs de aplicativo (e os logs de contêiner associados) são críticos na depuração de aplicativos do Hadoop problemáticos. O YARN fornece uma boa estrutura para coletar, agregar e armazenar logs de aplicativos com o recurso de [agregação de log](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) . O recurso de agregação de log torna o acesso a logs de aplicativo mais determinístico. Ele agrega logs em todos os contêineres em um nó de trabalho e os armazena como um arquivo de log agregado por nó de trabalho. O log é armazenado no sistema de arquivos padrão após a conclusão de um aplicativo. Seu aplicativo pode usar centenas ou milhares de contêineres, mas os logs para todos os contêineres executados em um único nó de trabalho sempre são agregados a um único arquivo. Portanto, há apenas um log por nó de trabalho usado pelo seu aplicativo. A agregação de log é habilitada por padrão nos clusters HDInsight versão 3,0 e posteriores. Os logs agregados estão localizados no armazenamento padrão para o cluster. O caminho a seguir é o caminho do HDFS para os logs:

```
/app-logs/<user>/logs/<applicationId>
```

No caminho, `user` é o nome do usuário que iniciou o aplicativo. O `applicationId` é o identificador exclusivo atribuído a um aplicativo pelo YARN RM.

Os logs agregados não são diretamente legíveis, pois são gravados em um formato [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf), [binário](https://issues.apache.org/jira/browse/HADOOP-3315) indexado pelo contêiner. Use os logs ResourceManager do YARN ou as ferramentas da CLI para exibir esses logs como texto sem formatação para aplicativos ou contêineres de interesse.

## <a name="yarn-logs-in-an-esp-cluster"></a>Fios logs em um cluster ESP

Duas configurações devem ser adicionadas ao `mapred-site` personalizado em Ambari.

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

1. A partir do Ambari UI, navegue até **MapReduce2** > **Configs** > **Advanced** > **Custom mapred-site**.

1. Adicione *um* dos seguintes conjuntos de propriedades:

    **Conjunto 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Conjunto 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Guarde alterações e reinicie todos os serviços afetados.

## <a name="yarn-cli-tools"></a>Ferramentas da CLI do YARN

1. Use o [comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando a seguir substituindo CLUSTERname pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Enumerar todos os ids de aplicação das aplicações yarn atualmente em execução com o seguinte comando:

    ```bash
    yarn top
    ```

    Tenha em anotao o id da aplicação a partir da coluna `APPLICATIONID` cujos registos devem ser descarregados.

    ```output
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

1. Você pode exibir esses logs como texto sem formatação executando um dos seguintes comandos:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Especifique o &lt;applicationId >, &lt;usuário-quem-iniciou-o-aplicativo >, &lt;ContainerId > e &lt;informações de > de endereço do nó de trabalho ao executar esses comandos.

### <a name="other-sample-commands"></a>Outros comandos de amostra

1. Baixe os registos de recipientes Yarn para todos os mestres de aplicação com o comando abaixo. Isto criará o ficheiro de registo denominado `amlogs.txt` em formato de texto.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Descarregue os registos de contentores Yarn apenas para o mais recente mestre de aplicações com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Descarregue os registos de contentores YARN para os dois primeiros mestres de aplicações com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Descarregue todos os registos de contentores Yarn com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Descarregue o registo do recipiente de fios para um determinado recipiente com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>Interface do usuário do ResourceManager YARN

A interface do usuário do ResourceManager YARN é executada no cabeçalho do cluster. Ele é acessado por meio da interface do usuário do amAmbari Web. Use as etapas a seguir para exibir os logs do YARN:

1. No navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`. Substitua CLUSTERname pelo nome do seu cluster HDInsight.

2. Na lista de serviços à esquerda, selecione **yarn**.

    ![Serviço yarn do Apache Ambari selecionado](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Na lista suspensa **links rápidos** , selecione um dos nós de cabeçalho do cluster e, em seguida, selecione **log de ResourceManager**.

    ![Links rápidos do Apache Ambari yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Você verá uma lista de links para logs do YARN.
