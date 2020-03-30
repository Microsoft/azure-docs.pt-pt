---
title: 'Quickstart: Criar/gerir a topologia da tempestade Apache - Azure HDInsight'
description: No arranque rápido, aprenda a criar e monitorizar uma topologia de tempestade Apache em Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 24173b553f30f652caf20b1ec7500fd9c4d2f7a0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "73241218"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Quickstart: Crie e monitorize uma topologia de tempestade Apache em Azure HDInsight

O Apache Storm é um sistema de cálculo dimensionável, tolerante a falhas, distribuído e em tempo real para o processamento de fluxos de dados. Com o Storm no Azure HDInsight, pode criar um cluster do Storm baseado na nuvem que executa a análise de macrodados em tempo real.

Neste arranque rápido, você usa um exemplo do projeto apache [de arranque de tempestade](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) para criar e monitorizar uma topologia de tempestade Apache para um aglomerado de tempestade Apache existente.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado de tempestade Apache no HDInsight. Consulte os [clusters De Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** for **Cluster type**.

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>Criar a topologia

1. Ligue-se ao seu aglomerado de tempestades. Edite o comando `CLUSTERNAME` abaixo substituindo pelo nome do seu aglomerado de tempestades e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. O exemplo **WordCount** está incluído no seu `/usr/hdp/current/storm-client/contrib/storm-starter/`cluster HDInsight em . A topologia gera frases aleatórias e conta quantas vezes as palavras ocorrem. Utilize o seguinte comando para iniciar a topologia do **wordcount** no cluster:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>Monitorizar a topologia

A tempestade fornece uma interface web para trabalhar com topoologias em execução, e está incluída no seu cluster HDInsight.

Siga estes passos para monitorizar a topologia através da IU do Storm:

1. Para apresentar a IU do Storm, abra um browser para `https://CLUSTERNAME.azurehdinsight.net/stormui`. Substitua `CLUSTERNAME` pelo nome do cluster.

2. Em resumo de **Topologia,** selecione a entrada do **wordcount** na coluna **Nome.** São apresentadas mais informações sobre a topologia.

    ![Dashboard do Storm com informações de topologia do WordCount do storm-starter.](./media/apache-storm-quickstart/hdi-topology-summary.png)

    A nova página fornece as seguintes informações:

    |Propriedade | Descrição |
    |---|---|
    |Estatísticas de topologia|Informação básica sobre o desempenho da topologia, organizada em janelas do tempo. Selecionar um intervalo de tempo específico altera o intervalo de tempo das informações apresentadas noutras secções da página.|
    |Bicos|Informações básicas sobre bicos, incluindo o último erro devolvido por cada bico.|
    |Parafusos|Informação básica sobre parafusos.|
    |Configuração topologia|Informações detalhadas sobre a configuração de topologia.|
    |Ativar|Retoma o processamento de uma topologia desativada.|
    |Desativar|Pausa uma topologia de corrida.|
    |Reequilíbrio|Ajusta o paralelismo da topologia. Deve rebalancear as topologias em execução depois de ter alterado o número de nós no cluster. O rebalanceamento ajusta o paralelismo para compensar o número maior/menor de nós no cluster. Para mais informações, consulte [Compreender o paralelismo de uma topologia da tempestade Apache.](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)|
    |Matar|Termina uma topologia de tempestade após o tempo limite especificado.|

3. Nesta página, selecione uma entrada da secção **Spouts** ou **Bolts**. São apresentadas informações sobre o componente selecionado.

    ![Dashboard do Storm com informações sobre os componentes selecionados.](./media/apache-storm-quickstart/hdi-component-summary.png)

    A nova página apresenta as seguintes informações:

    |Propriedade | Descrição |
    |---|---|
    |Estatísticas spout/Bolt|Informação básica sobre o desempenho do componente, organizada em janelas de tempo. Selecionar um intervalo de tempo específico altera o intervalo de tempo das informações apresentadas noutras secções da página.|
    |Estatísticas de entrada (apenas parafuso)|Informação sobre componentes que produzem dados consumidos pelo parafuso.|
    |Estatísticas de saída|Informação sobre os dados emitidos por este parafuso.|
    |Executores|Informações sobre casos deste componente.|
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

Depois de completar o arranque rápido, poderá querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, usou um exemplo do projeto apache [de arranque](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) de tempestade para criar e monitorizar uma topologia da tempestade Apache para um aglomerado de tempestade Apache existente. Avançar para o próximo artigo para aprender o básico de gerir e monitorizar as topoologias da Tempestade Apache.

> [!div class="nextstepaction"]
>[Implementar e gerir topoologias da tempestade Apache no Azure HDInsight](./apache-storm-deploy-monitor-topology-linux.md)