---
title: Acesso Registos de aplicações YARN Apache Hadoop - Azure HDInsight
description: Saiba como aceder aos registos de aplicações YARN num cluster HDInsight (Apache Hadoop) baseado em Linux, utilizando tanto a linha de comando como um navegador web.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764389"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Aceder aos registos de aplicações do YARN apache hadoop no HDInsight baseado em Linux

Saiba como aceder aos registos das aplicações [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Mais Um Negociador de Recursos) num cluster [Apache Hadoop](https://hadoop.apache.org/) em Azure HDInsight.

## <a name="what-is-apache-yarn"></a>O que é Apache YARN?

O YARN suporta vários modelos de programação[(Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) sendo um deles) dissociando a gestão de recursos a partir do agendamento/monitorização de aplicações. O YARN utiliza um *ResourceManager* global (RM), por-trabalhador-nódeManagers (NMs) e *ApplicationMasters* (MA). *NodeManagers* A AM por aplicação negoceia recursos (CPU, memória, disco, rede) para executar a sua aplicação com o RM. O RM trabalha com as MD para conceder estes recursos, que são concedidos como *contentores.* A AM é responsável por acompanhar o progresso dos contentores que lhe foram atribuídos pela RM. Uma aplicação pode exigir muitos contentores dependendo da natureza da aplicação.

Cada aplicação pode consistir em *múltiplas tentativas*de aplicação . Se uma aplicação falhar, pode ser novamente tentada como uma nova tentativa. Cada tentativa corre num contentor. De certa forma, um recipiente fornece o contexto para a unidade básica de trabalho realizada por uma aplicação YARN. Todo o trabalho que é feito no contexto de um contentor é realizado no nó de trabalhador único em que o contentor foi atribuído. Ver [Hadoop: Escrita aplicações de ARN](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html), ou [YARN De Hadoop Apache](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) para posterior referência.

Para escalar o seu cluster para suportar uma maior produção de processamento, pode utilizar a [escala automática](hdinsight-autoscale-clusters.md) ou escalar os [seus clusters manualmente utilizando algumas línguas diferentes.](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters)

## <a name="yarn-timeline-server"></a>Servidor de linha do tempo do fio yARN

O [Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fornece informações genéricas sobre aplicações concluídas

O Servidor de Linha do Tempo Do Fio inclui o seguinte tipo de dados:

* O ID da aplicação, um identificador único de uma aplicação
* O utilizador que iniciou a aplicação
* Informações sobre tentativas feitas para completar o pedido
* Os recipientes utilizados por qualquer tentativa de aplicação

## <a name="yarn-applications-and-logs"></a>Aplicações e registos de ARN

O YARN suporta vários modelos de programação[(Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) sendo um deles) dissociando a gestão de recursos a partir do agendamento/monitorização de aplicações. O YARN utiliza um *ResourceManager* global (RM), por-trabalhador-nódeManagers (NMs) e *ApplicationMasters* (MA). *NodeManagers* A AM por aplicação negoceia recursos (CPU, memória, disco, rede) para executar a sua aplicação com o RM. O RM trabalha com as MD para conceder estes recursos, que são concedidos como *contentores.* A AM é responsável por acompanhar o progresso dos contentores que lhe foram atribuídos pela RM. Uma aplicação pode exigir muitos contentores dependendo da natureza da aplicação.

Cada aplicação pode consistir em *múltiplas tentativas*de aplicação . Se uma aplicação falhar, pode ser novamente tentada como uma nova tentativa. Cada tentativa corre num contentor. De certa forma, um recipiente fornece o contexto para a unidade básica de trabalho realizada por uma aplicação YARN. Todo o trabalho que é feito no contexto de um contentor é realizado no nó de trabalhador único em que o contentor foi atribuído. Consulte os [Conceitos YARN de Hadoop Apache Hadoop](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) para mais referências.

Os registos de aplicação (e os registos de contentores associados) são fundamentais para depurar aplicações problemáticas de Hadoop. O YARN fornece uma boa estrutura para recolher, agregar e armazenar registos de aplicações com a funcionalidade de [Agregação](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) de Registos. A função de Agregação de Registos torna o acesso aos registos de aplicações mais determinístico. Agrega troncos em todos os contentores num nó de trabalhador e armazena-os como um ficheiro de registo agregado por nó de trabalhador. O registo é armazenado no sistema de ficheiros predefinido após o acabamento de uma aplicação. A sua aplicação pode utilizar centenas ou milhares de contentores, mas os registos para todos os recipientes executados num único nó de trabalhador esgotam-se sempre num único ficheiro. Portanto, só há um log por nó de trabalhador usado pela sua aplicação. A agregação de registoé ativada por padrão na versão 3.0 dos clusters HDInsight. Os registos agregados estão localizados no armazenamento predefinido do cluster. O seguinte caminho é o caminho HDFS para os registos:

```
/app-logs/<user>/logs/<applicationId>
```

No caminho, `user` está o nome do utilizador que iniciou a aplicação. O `applicationId` identificador único atribuído a uma aplicação pelo Arn RM yARN.

Os registos agregados não são diretamente legíveis, uma vez que estão escritos num [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf), [formato binário](https://issues.apache.org/jira/browse/HADOOP-3315) indexado por recipiente. Utilize os registos do YARN ResourceManager ou ferramentas CLI para ver estes registos como texto simples para aplicações ou contentores de interesse.

## <a name="yarn-logs-in-an-esp-cluster"></a>Fios logs em um cluster ESP

Duas configurações devem ser `mapred-site` adicionadas ao costume em Ambari.

1. De um navegador web, navegue até, `https://CLUSTERNAME.azurehdinsight.net`onde `CLUSTERNAME` está o nome do seu cluster.

1. A partir do Ambari UI, navegue até **MapReduce2** > **Configs** > **Advanced** > Custom**mapred-site**.

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

## <a name="yarn-cli-tools"></a>Ferramentas DE CLI de FIOS

1. Utilize [o comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Enumerar todos os ids de aplicação das aplicações yarn atualmente em execução com o seguinte comando:

    ```bash
    yarn top
    ```

    Note o id `APPLICATIONID` da aplicação a partir da coluna cujos registos devem ser descarregados.

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

1. Pode ver estes registos como texto simples executando um dos seguintes comandos:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Especifique &lt;a &lt;aplicaçãoId>,> de &lt;aplicação iniciado &lt;pelo utilizador,> de contentores e> informações de endereço de nó de trabalhador esgote durante a execução destes comandos.

### <a name="other-sample-commands"></a>Outros comandos de amostra

1. Baixe os registos de recipientes Yarn para todos os mestres de aplicação com o comando abaixo. Isto criará o `amlogs.txt` ficheiro de registo nomeado em formato de texto.

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

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

O YARN ResourceManager UI funciona no headnode do cluster. É acedido através da UI web ambari. Utilize os seguintes passos para visualizar os registos de ARN:

1. No seu navegador web, navegue para `https://CLUSTERNAME.azurehdinsight.net`. Substitua CLUSTERNAME pelo nome do cluster do HDInsight.

2. A partir da lista de serviços à esquerda, selecione **YARN**.

    ![Serviço Apache Ambari Yarn selecionado](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. A partir do dropdown **quick Links,** selecione um dos nós da cabeça do cluster e, em seguida, selecione **Registo de Gestor de Recursos**.

    ![Ligações rápidas apache Ambari Yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    É-lhe apresentada uma lista de links para registos de ARN.
