---
title: 'Início rápido: Criar e monitorar a topologia de Apache Storm no Azure HDInsight'
description: No início rápido, saiba como criar e monitorar uma topologia de Apache Storm no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 9e48cb53b55cdc4200498a54dba31ae93ca8b31a
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018594"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Início rápido: Criar e monitorar uma topologia de Apache Storm no Azure HDInsight

O Apache Storm é um sistema de cálculo dimensionável, tolerante a falhas, distribuído e em tempo real para o processamento de fluxos de dados. Com o Storm no Azure HDInsight, pode criar um cluster do Storm baseado na nuvem que executa a análise de macrodados em tempo real.

Neste guia de início rápido, você usa um exemplo do projeto do Apache [Storm-Starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) para criar e monitorar uma topologia de Apache Storm para um cluster apache Storm existente.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Storm no HDInsight. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** para o **tipo de cluster**.

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>Criar a topologia

1. Conecte-se ao cluster Storm. Edite o comando a seguir `CLUSTERNAME` substituindo pelo nome do cluster Storm e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. O exemplo de **WordCount** está incluído em seu cluster HDInsight `/usr/hdp/current/storm-client/contrib/storm-starter/`em. A topologia gera frases aleatórias e conta quantas vezes as palavras ocorrem. Use o seguinte comando para iniciar a topologia **WordCount** no cluster:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>Monitorizar a topologia

O Storm fornece uma interface da Web para trabalhar com topologias em execução e está incluído em seu cluster HDInsight.

Siga estes passos para monitorizar a topologia através da IU do Storm:

1. Para apresentar a IU do Storm, abra um browser para `https://CLUSTERNAME.azurehdinsight.net/stormui`. Substitua `CLUSTERNAME` pelo nome do cluster.

2. Em **Resumo da topologia**, selecione a entrada **WordCount** na coluna **nome** . São apresentadas mais informações sobre a topologia.

    ![Dashboard do Storm com informações de topologia do WordCount do storm-starter.](./media/apache-storm-quickstart/hdi-topology-summary.png)

    A nova página fornece as seguintes informações:

    |Propriedade | Descrição |
    |---|---|
    |Estatísticas de topologia|Informações básicas sobre o desempenho da topologia, organizadas em janelas de tempo. Selecionar um intervalo de tempo específico altera o intervalo de tempo das informações apresentadas noutras secções da página.|
    |Spouts|Informações básicas sobre esgotamentos, incluindo o último erro retornado por cada Spout.|
    |Bolts|Informações básicas sobre os parafusos.|
    |Configuração de topologia|Informações detalhadas sobre a configuração de topologia.|
    |Ativar|Retoma o processamento de uma topologia desativada.|
    |Desativar|Pausa uma topologia em execução.|
    |Reequilibrar|Ajusta o paralelismo da topologia. Deve rebalancear as topologias em execução depois de ter alterado o número de nós no cluster. O rebalanceamento ajusta o paralelismo para compensar o número maior/menor de nós no cluster. Para obter mais informações, consulte [noções básicas sobre o paralelismo de uma topologia de Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |Cancelar|Encerra uma topologia Storm após o tempo limite especificado.|

3. Nesta página, selecione uma entrada da secção **Spouts** ou **Bolts**. São apresentadas informações sobre o componente selecionado.

    ![Dashboard do Storm com informações sobre os componentes selecionados.](./media/apache-storm-quickstart/hdi-component-summary.png)

    A nova página exibe as seguintes informações:

    |Propriedade | Descrição |
    |---|---|
    |Estatísticas de Spout/raio|Informações básicas sobre o desempenho do componente, organizadas em janelas de tempo. Selecionar um intervalo de tempo específico altera o intervalo de tempo das informações apresentadas noutras secções da página.|
    |Estatísticas de entrada (somente raio)|Informações sobre componentes que produzem dados consumidos pelo parafuso.|
    |Estatísticas de saída|Informações sobre dados emitidos por este parafuso.|
    |Executores|Informações sobre instâncias deste componente.|
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

Depois de concluir o início rápido, talvez você queira excluir o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para excluir um cluster, consulte [excluir um cluster HDInsight usando seu navegador, o PowerShell ou o CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você usou um exemplo do projeto do Apache [Storm-Starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) para criar e monitorar uma topologia de Apache Storm para um cluster apache Storm existente. Avance para o próximo artigo para aprender as noções básicas de gerenciamento e monitoramento de topologias de Apache Storm.

> [!div class="nextstepaction"]
>[Implantar e gerenciar topologias de Apache Storm no Azure HDInsight](./apache-storm-deploy-monitor-topology-linux.md)