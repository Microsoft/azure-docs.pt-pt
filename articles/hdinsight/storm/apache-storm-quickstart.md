---
title: 'Início rápido: Criar e monitorizar a topologia do Apache Storm no HDInsight do Azure'
description: Guia de introdução, saiba como criar e monitorizar uma topologia do Apache Storm no HDInsight do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 49da6ad20b29193f0430a66658f1bf80625704b3
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274119"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Início rápido: Criar e monitorizar uma topologia do Apache Storm no HDInsight do Azure

O Apache Storm é um sistema de cálculo dimensionável, tolerante a falhas, distribuído e em tempo real para o processamento de fluxos de dados. Com o Storm no Azure HDInsight, pode criar um cluster do Storm baseado na nuvem que executa a análise de macrodados em tempo real.

Neste início rápido, vai utilizar um exemplo do Apache [storm-starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projeto para criar e monitorizar uma topologia do Apache Storm num cluster do Apache Storm existente.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Storm no HDInsight. Ver [Apache Hadoop criar clusters no portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** para **tipo de Cluster**.

* Um cliente SSH. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>Criar a topologia

1. Ligar ao cluster do Storm. Editar o comando abaixo, substituindo `CLUSTERNAME` com o nome do Storm de cluster e, em seguida, introduza o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. O **WordCount** exemplo está incluído no seu cluster do HDInsight em `/usr/hdp/current/storm-client/contrib/storm-starter/`. A topologia gera frases aleatórias e conta quantas vezes palavras ocorrem. Utilize o seguinte comando para iniciar o **wordcount** topologia no cluster:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>Monitorizar a topologia

O Storm fornece uma interface web para trabalhar com topologias em execução e está incluído no seu cluster do HDInsight.

Siga estes passos para monitorizar a topologia através da IU do Storm:

1. Para apresentar a IU do Storm, abra um browser para `https://CLUSTERNAME.azurehdinsight.net/stormui`. Substitua `CLUSTERNAME` pelo nome do cluster.

2. Sob **resumo da topologia**, selecione a **wordcount** entrada no **nome** coluna. São apresentadas mais informações sobre a topologia.

    ![Dashboard do Storm com informações de topologia do WordCount do storm-starter.](./media/apache-storm-quickstart/topology-summary.png)

    A nova página fornece as seguintes informações:

    |Propriedade | Descrição |
    |---|---|
    |Estatísticas de topologia|Informações básicas sobre o desempenho de topologia, organizadas em intervalos de tempo. Selecionar um intervalo de tempo específico altera o intervalo de tempo das informações apresentadas noutras secções da página.|
    |Spouts|Informações básicas sobre spouts, incluindo o último erro devolvido por cada spout.|
    |Bolts|Informações básicas sobre bolts.|
    |Configuração da topologia|Obter informações detalhadas sobre a configuração da topologia.|
    |Ativar|Retoma o processamento de uma topologia desativada.|
    |desativar|Coloca em pausa uma topologia em execução.|
    |Reequilibrar|Ajusta o paralelismo da topologia. Deve rebalancear as topologias em execução depois de ter alterado o número de nós no cluster. O rebalanceamento ajusta o paralelismo para compensar o número maior/menor de nós no cluster. Para obter mais informações, consulte [compreender o paralelismo de uma topologia do Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |kill|Termina uma topologia do Storm após o tempo limite especificado.|

3. Nesta página, selecione uma entrada da secção **Spouts** ou **Bolts**. São apresentadas informações sobre o componente selecionado.

    ![Dashboard do Storm com informações sobre os componentes selecionados.](./media/apache-storm-quickstart/component-summary.png)

    A nova página apresenta as seguintes informações:

    |Propriedade | Descrição |
    |---|---|
    |Estatísticas de spout/Bolt|Informações básicas sobre o desempenho dos componentes, organizadas em intervalos de tempo. Selecionar um intervalo de tempo específico altera o intervalo de tempo das informações apresentadas noutras secções da página.|
    |Estatísticas de entrada (apenas bolt)|Informações sobre componentes que produzem dados consumidos pelo bolt.|
    |Estatísticas de saída|Informações sobre dados emitidos por este bolt.|
    |Executores|Informações sobre as instâncias deste componente.|
    |Erros|Erros produzidos por este componente.|

4. Ao visualizar os detalhes de um spout ou bolt, selecione uma entrada na coluna **Porta** na secção **Executores** para ver os detalhes de uma instância específica do componente.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    Neste exemplo, a palavra **sete** ocorreu 1493957 vezes. Esta contagem refere-se ao número de vezes que a palavra foi encontrada desde que esta topologia foi iniciada.

## <a name="stop-the-topology"></a>Parar a topologia

Regresse à página **Resumo da topologia** para aceder à topologia da contagem de palavras e, em seguida, selecione o botão **Eliminar** na secção **Ações de topologia**. Quando lhe for solicitado, introduza 10 para os segundos a aguardar antes de parar a topologia. Após o período de tempo limite, a topologia deixará de ser apresentada quando visitar a secção **IU do Storm** do dashboard.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o início rápido, pode querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para eliminar um cluster, veja [eliminar um cluster do HDInsight com o seu browser, o PowerShell ou a CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, utilizou um exemplo do Apache [storm-starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projeto para criar e monitorizar uma topologia do Apache Storm num cluster do Apache Storm existente. Avance para o artigo seguinte para aprender as noções básicas de gerenciamento e monitoramento de topologias do Apache Storm.

> [!div class="nextstepaction"]
>[Implementar e gerir topologias do Apache Storm no HDInsight do Azure ](./apache-storm-deploy-monitor-topology-linux.md)