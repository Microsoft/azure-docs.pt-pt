---
title: Acessar Apache Hadoop logs de aplicativo do YARN no HDInsight baseado em Linux – Azure
description: Saiba como acessar os logs de aplicativo do YARN em um cluster HDInsight baseado em Linux (Apache Hadoop) usando a linha de comando e um navegador da Web.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: 2b230f91b9d6b169b89b125bdd0394c2c7ecd96f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879849"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Acessar Apache Hadoop logs de aplicativo do YARN no HDInsight baseado em Linux

Saiba como acessar os logs para os aplicativos [Apache HADOOP yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (ainda outro negociador de recursos) em um cluster de [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight.

## <a name="YARNTimelineServer"></a>YARN Timeline Server

O [Apache HADOOP yarn Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fornece informações genéricas sobre aplicativos concluídos

YARN Timeline Server inclui os seguintes tipos de dados:

* A ID do aplicativo, um identificador exclusivo de um aplicativo
* O usuário que iniciou o aplicativo
* Informações sobre tentativas feitas para concluir o aplicativo
* Os contêineres usados por qualquer tentativa de aplicativo específica

## <a name="YARNAppsAndLogs"></a>Aplicativos e logs do YARN

O YARN dá suporte a vários modelos de programação ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) sendo um deles) desacoplando o gerenciamento de recursos do agendamento/monitoramento de aplicativos. O YARN usa um *ResourceManager* global (RM), *NodeManagers* (NMS) por um nó de trabalho e por aplicativo *Gerenciador* (AMS). O aplicativo AM negocia recursos (CPU, memória, disco, rede) para executar seu aplicativo com o RM. O RM funciona com NMs para conceder esses recursos, que são concedidos como *contêineres*. A AM é responsável por acompanhar o progresso dos contêineres atribuídos a ele pelo RM. Um aplicativo pode exigir muitos contêineres, dependendo da natureza do aplicativo.

Cada aplicativo pode consistir em várias *tentativas de aplicativo*. Se um aplicativo falhar, ele poderá ser tentado novamente como uma nova tentativa. Cada tentativa é executada em um contêiner. De certa forma, um contêiner fornece o contexto para a unidade de trabalho básica executada por um aplicativo YARN. Todo o trabalho feito dentro do contexto de um contêiner é executado no nó de trabalho único no qual o contêiner foi alocado. Consulte [Apache Hadoop conceitos de yarn](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) para referência adicional.

Os logs de aplicativo (e os logs de contêiner associados) são críticos na depuração de aplicativos do Hadoop problemáticos. O YARN fornece uma boa estrutura para coletar, agregar e armazenar logs de aplicativos com o recurso de [agregação de log](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) . O recurso de agregação de log torna o acesso a logs de aplicativo mais determinístico. Ele agrega logs em todos os contêineres em um nó de trabalho e os armazena como um arquivo de log agregado por nó de trabalho. O log é armazenado no sistema de arquivos padrão após a conclusão de um aplicativo. Seu aplicativo pode usar centenas ou milhares de contêineres, mas os logs para todos os contêineres executados em um único nó de trabalho sempre são agregados a um único arquivo. Portanto, há apenas um log por nó de trabalho usado pelo seu aplicativo. A agregação de log é habilitada por padrão nos clusters HDInsight versão 3,0 e posteriores. Os logs agregados estão localizados no armazenamento padrão para o cluster. O caminho a seguir é o caminho do HDFS para os logs:

    /app-logs/<user>/logs/<applicationId>

No caminho, `user` é o nome do usuário que iniciou o aplicativo. O `applicationId` é o identificador exclusivo atribuído a um aplicativo pelo yarn RM.

Os logs agregados não são diretamente legíveis, pois são gravados em um formato de [TFile][T-file], [binário][binary-format] indexado pelo contêiner. Use os logs ResourceManager do YARN ou as ferramentas da CLI para exibir esses logs como texto sem formatação para aplicativos ou contêineres de interesse.

## <a name="yarn-cli-tools"></a>Ferramentas da CLI do YARN

Para usar as ferramentas da CLI do YARN, primeiro você deve se conectar ao cluster HDInsight usando SSH. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

Você pode exibir esses logs como texto sem formatação executando um dos seguintes comandos:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Especifique o &lt;ApplicationId >, &lt;usuário-quem-iniciou-The-Application >, &lt;idcontêiner > e &lt;> informações de endereço de nó de trabalho ao executar esses comandos.

## <a name="yarn-resourcemanager-ui"></a>Interface do usuário do ResourceManager YARN

A interface do usuário do ResourceManager YARN é executada no cabeçalho do cluster. Ele é acessado por meio da interface do usuário do amAmbari Web. Use as etapas a seguir para exibir os logs do YARN:

1. No navegador da Web, navegue até https://CLUSTERNAME.azurehdinsight.net. Substitua CLUSTERname pelo nome do seu cluster HDInsight.
2. Na lista de serviços à esquerda, selecione **yarn**.

    ![Serviço yarn selecionado](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Na lista suspensa **links rápidos** , selecione um dos nós de cabeçalho do cluster e, em seguida, selecione **log de ResourceManager**.

    ![Links rápidos do yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Você verá uma lista de links para logs do YARN.

[YARN-timeline-server]:https://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
