---
title: Aceder a registos de aplicações Apache Hadoop YARN - Azure HDInsight
description: Saiba como aceder aos registos de aplicações yarn num cluster HDInsight (Apache Hadoop) baseado em Linux utilizando a linha de comando e um navegador web.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 9558e6c5ddd58b1d5fd70da03187caef50d1275d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865575"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Aceder a registos de aplicações apache Hadoop YARN em HDInsight baseado em Linux

Saiba como aceder aos registos de aplicações [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Mais Um Negociador de Recursos) num cluster Apache Hadoop em Azure HDInsight.

## <a name="what-is-apache-yarn"></a>O que é Apache YARN?

O YARN suporta vários modelos de programação (Apache Hadoop MapReduce é um deles) ao dissociar a gestão de recursos a partir do agendamento/monitorização de aplicações. O YARN utiliza um global *`ResourceManager`* (RM), *node node node* de trabalhador (NMs) e por aplicação *ApplicationMasters* (AMs). A AM por aplicação negoceia recursos (CPU, memória, disco, rede) para executar a sua aplicação com o RM. O RM trabalha com os NMs para conceder estes recursos, que são concedidos como *contentores.* A AM é responsável por acompanhar o progresso dos contentores que lhe foram atribuídos pela RM. Uma aplicação pode exigir muitos recipientes dependendo da natureza da aplicação.

Cada aplicação pode consistir em *múltiplas tentativas* de aplicação . Se uma aplicação falhar, pode ser novamente julgada como uma nova tentativa. Cada tentativa corre num contentor. De certa forma, um recipiente fornece o contexto para a unidade básica de trabalho realizada por uma aplicação YARN. Todo o trabalho que é feito no contexto de um contentor é feito no nó de um único trabalhador no qual o contentor foi dado. Consulte [Hadoop: Writing YARN Applications](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html), ou [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) para mais referências.

Para escalar o seu cluster para suportar uma maior produção de processamento, pode utilizar [autoescala](hdinsight-autoscale-clusters.md) ou [escalar os seus clusters manualmente utilizando algumas línguas diferentes.](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters)

## <a name="yarn-timeline-server"></a>Servidor de linha do tempo YARN

O [Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fornece informações genéricas sobre aplicações concluídas

O YARN Timeline Server inclui o seguinte tipo de dados:

* O ID da aplicação, um identificador único de uma aplicação
* O utilizador que iniciou a aplicação
* Informação sobre tentativas feitas para completar o pedido
* Os contentores utilizados por qualquer tentativa de aplicação

## <a name="yarn-applications-and-logs"></a>Aplicações e registos de YARN

Os registos de aplicações (e os registos de contentores associados) são fundamentais para depurar aplicações problemáticas de Hadoop. O YARN fornece uma boa estrutura para recolher, agregar e armazenar registos de aplicações com agregação de registos.

A funcionalidade de agregação de registos torna o acesso aos registos de aplicações mais determinístico. Agrega troncos em todos os contentores de um nó de trabalhador e armazena-os como um ficheiro de registo agregado por nó de trabalhador. O registo é armazenado no sistema de ficheiros predefinidos após o fim de uma aplicação. A sua aplicação pode utilizar centenas ou milhares de contentores, mas os registos para todos os contentores que funcionam num único nó de trabalhador são sempre agregados a um único ficheiro. Portanto, há apenas 1 registo por nó de trabalhador usado pela sua aplicação. A agregação de registos é ativada por padrão na versão 3.0 ou acima dos clusters HDInsight. Os troncos agregados estão localizados em armazenamento predefinido para o cluster. O seguinte caminho é o caminho HDFS para os registos:

```
/app-logs/<user>/logs/<applicationId>
```

No caminho, `user` está o nome do utilizador que iniciou a aplicação. Este `applicationId` é o identificador único atribuído a uma aplicação pelo RM YARN.

Os registos agregados não são diretamente legíveis, uma vez que estão escritos num TFile, formato binário indexado por contentor. Utilize os registos YARN `ResourceManager` ou ferramentas CLI para ver estes registos como texto simples para aplicações ou recipientes de interesse.

## <a name="yarn-logs-in-an-esp-cluster"></a>Troncos de fios em um cluster ESP

Duas configurações devem ser adicionadas ao costume `mapred-site` em Ambari.

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net` para, onde `CLUSTERNAME` está o nome do seu cluster.

1. A partir do Ambari UI, navegue até **MapReduce2**  >  **Configs**  >  **Advanced**  >  **Custom mapred-site**.

1. Adicione *um dos* seguintes conjuntos de propriedades:

    **Conjunto 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Conjunto 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Guarde as alterações e reinicie todos os serviços afetados.

## <a name="yarn-cli-tools"></a>Ferramentas YARN CLI

1. Utilize [o comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Listar todos os IDs de aplicação das aplicações de Yarn atualmente em execução com o seguinte comando:

    ```bash
    yarn top
    ```

    Note o ID da aplicação a partir da `APPLICATIONID` coluna cujos registos devem ser descarregados.

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

1. Pode ver estes registos como texto simples, executando um dos seguintes comandos:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Especifique a &lt; aplicaçãoId>, &lt;> de aplicação do utilizador- que iniciou a aplicação, &lt;> de contentores e endereço de nó de &lt; trabalhador> informações ao executar estes comandos.

### <a name="other-sample-commands"></a>Outros comandos de amostra

1. Descarregue os registos de contentores de Fios para todos os mestres de aplicações com o comando abaixo. Este passo criará o ficheiro de registo nomeado `amlogs.txt` em formato de texto.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Descarregue os registos de contentores yarn apenas para o mais recente mestre de aplicações com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Descarregue os registos de contentores YARN para os dois primeiros mestres de aplicações com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Descarregue todos os registos de contentores de Fios com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Descarregue o registo do contentor de fios para um determinado recipiente com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>YARN `ResourceManager` UI

A UI YARN `ResourceManager` funciona no headnode do cluster. É acedido através da UI web Ambari. Utilize os seguintes passos para visualizar os registos YARN:

1. No seu navegador web, navegue para `https://CLUSTERNAME.azurehdinsight.net` . Substitua CLUSTERNAME pelo nome do cluster do HDInsight.

2. Da lista de serviços à esquerda, selecione **YARN**.

    :::image type="content" source="./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png" alt-text="Serviço Apache Ambari Yarn selecionado":::

3. A partir do **dropdown de Links Rápidos,** selecione um dos nós da cabeça do cluster e, em seguida, selecione **`ResourceManager Log`** .

    :::image type="content" source="./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png" alt-text="Ligações rápidas apache Ambari Yarn":::

    É-lhe apresentada uma lista de links para registos DE FIOS.

## <a name="next-steps"></a>Passos seguintes

* [Arquitetura do Apache Hadoop no HDInsight](hdinsight-hadoop-architecture.md)
* [Resolver Problemas do YARN do Apache Hadoop com o Azure HDInsight](hdinsight-troubleshoot-yarn.md)
