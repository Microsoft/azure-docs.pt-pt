---
title: Implementar e gerir topoologias da tempestade Apache no Azure HDInsight
description: Aprenda a implementar, monitorizar e gerir as topoologias da Tempestade Apache utilizando o Storm Dashboard no HDInsight baseado em Linux. Utilize ferramentas Hadoop para Estúdio Visual.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: e890289230b3215bd102d8c5a78dca4f1b7b90f8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271905"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Implementar e gerir topoologias da tempestade Apache no Azure HDInsight

Neste documento, aprenda os fundamentos da gestão e monitorização das topoologias da [Tempestade Apache](https://storm.apache.org/) em execução em aglomerados HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado de tempestade Apache no HDInsight. Consulte os [clusters De Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** for **Cluster type**.

* (Opcional) Familiaridade com concha segura (SSH) e Cópia Segura (SCP). Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Opcional) Visual Studio, Azure SDK 2.5.1 ou mais recente, e as Ferramentas data lake para estúdio visual. Para mais informações, consulte [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Submeta uma topologia usando o Estúdio Visual

Pode utilizar as Ferramentas data lake C# para o Estúdio Visual para submeter ou topoologias híbridas ao seu cluster Storm. Os seguintes passos utilizam uma aplicação de amostragem. Para obter informações sobre a criação de topologia utilizando as Ferramentas data lake, consulte [as topoologias C#apache Storm com Visual Studio e ](apache-storm-develop-csharp-visual-studio-topology.md).

1. Se ainda não instalou a versão mais recente das ferramentas data lake para estúdio visual, consulte [Use Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > As Ferramentas Azure Data Lake e Stream Analytics foram anteriormente chamadas de Ferramentas HDInsight para Estúdio Visual.
    >
    > As ferramentas Azure Data Lake e Stream Analytics para O Estúdio Visual estão incluídas na carga de trabalho de **desenvolvimento do Azure** para o Visual Studio 2019.

1. Inicie o Visual Studio.

1. Na janela **Iniciar,** selecione **Criar um novo projeto**.

1. Na **Janela Criar uma nova** janela de projeto, selecione a caixa de pesquisa e introduza `Storm`. Em seguida, escolha **a Amostra** de Tempestade na lista de resultados e selecione **Next**.

1. Na **Configuração** da sua nova janela de projeto, insira um **nome de Projeto,** e vá ou crie um **Local** para salvar o novo projeto. Em seguida, selecione **Criar**.

    ![Configure a sua nova janela de projeto, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. A partir do **Server Explorer,** clique no **Azure** e selecione **Connect to Microsoft Azure Subscription...** e complete o processo de início de sessão.

1. Do **Solution Explorer,** clique no projeto e escolha **Submeter-se à tempestade no HDInsight**.

    > [!NOTE]  
    > Se solicitado, introduza as credenciais de login para a sua subscrição Azure. Se tiver mais de uma subscrição, inscreva-se na que contém o seu cluster Storm on HDInsight.

1. Na caixa de diálogo De submissão de **Topologia,** sob a lista de drop-down do **Storm Cluster,** escolha a sua tempestade no cluster HDInsight e, em seguida, selecione **Submeter**. Pode monitorizar se a submissão é bem sucedida visualizando o painel **de saída.**

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Submeta uma topologia usando o Comando SSH e a Tempestade

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sua sessão ssh, use o seguinte comando para iniciar a topologia do exemplo **WordCount:**

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Deste modo, este comando inicia a topologia do WordCount de exemplo no cluster. Esta topologia gera aleatoriamente frases, e depois conta a ocorrência de cada palavra nas frases.

    > [!NOTE]  
    > Ao submeter a topologia ao cluster, deve primeiro copiar o ficheiro .jar contendo o cluster antes de utilizar o comando `storm`. Para copiar o ficheiro para o cluster, pode utilizar o comando `scp`. Por exemplo, introduza `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > O exemplo *do WordCount,* e outros exemplos de arranque de tempestade, já estão incluídos no seu cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Submeter uma topologia programática

Pode implementar programáticamente uma topologia utilizando o serviço Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) fornece um exemplo de aplicação Java que demonstra como implementar e iniciar uma topologia através do serviço Nimbus.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitorize e gerencie uma topologia no Estúdio Visual

Quando submete uma topologia usando o Estúdio Visual, aparece a janela **Storm Topologies View.** Selecione a topologia da lista para visualizar informações sobre a topologia em execução.

![Monitor topologia, Storm Topologies Ver janela, Estúdio Visual](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> Também pode ver **as Topologs** storm do **Server Explorer.** Expanda **o Azure** > **HDInsight,** clique à direita numa tempestade no cluster HDInsight e, em seguida, selecione **Ver Topoologias**de Tempestade .

Selecione a forma para os bicos ou parafusos para ver informações sobre estes componentes. Aparece uma dica de ferramenta com informações do componente para o item selecionado.

### <a name="deactivate-and-reactivate-a-topology"></a>Desativar e reativar uma topologia

Desativar uma topologia interrompe-a até que a topologia seja morta ou reativada. Para então, utilize os botões **Deactivate** e **Reativar** na área **de Ações** no topo da janela Storm **Topologies Ver.**

### <a name="rebalance-a-topology"></a>Reequilibrar uma topologia

Reequilibrar uma topologia permite ao sistema rever o paralelismo da topologia. Por exemplo, se redimensionou o cluster para adicionar mais notas, o reequilíbrio permite que uma topologia veja os novos nós.

Para reequilibrar uma topologia, utilize o botão **Rebalance** na área **de Ações** da janela **Storm Topologies Ver.**

> [!WARNING]  
> Reequilibrar uma topologia desativa a topologia, redistribui os trabalhadores uniformemente em todo o cluster, e depois devolve a topologia ao estado em que estava antes de ocorrer o reequilíbrio. Se a topologia estava ativa, volta a ser ativa. Se a topologia foi desativada, continua desativada.

### <a name="kill-a-running-topology"></a>Mate uma topologia em execução

As topoologias da tempestade continuam a funcionar até que sejam paradas ou o aglomerado seja apagado. Para parar uma topologia, utilize o botão **Kill** na área **de Ações.**

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitorize e gerencie uma topologia usando o Comando SSH e a Tempestade

O utilitário `storm` permite-lhe trabalhar com topoologias de execução a partir da linha de comando. Use `storm -h` para uma lista completa de comandos.

### <a name="list-topologies"></a>Topologies de lista

Utilize o seguinte comando para listar todas as topoologias em execução:

```shell
storm list
```

Este comando devolve informações semelhantes ao texto seguinte:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Desativar e reativar uma topologia

Desativar uma topologia interrompe-a até que a topologia seja morta ou reativada. Utilize os seguintes comandos para desativar ou reativar:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Mate uma topologia em execução

As topoologias da tempestade, uma vez iniciadas, continuam a correr até pararem. Para parar uma topologia, utilize o seguinte comando:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Reequilibrar uma topologia

Reequilibrar uma topologia permite ao sistema rever o paralelismo da topologia. Por exemplo, se redimensionou o cluster para adicionar mais notas, o reequilíbrio permite que uma topologia veja os novos nós.

> [!WARNING]  
> Reequilibrar uma topologia desativa a topologia, redistribui os trabalhadores uniformemente em todo o cluster, e depois devolve a topologia ao estado em que estava antes de ocorrer o reequilíbrio. Se a topologia estava ativa, volta a ser ativa. Se foi desativada, continua desativada.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitorize e gerencie uma topologia usando a Tempestade UI

A Storm UI fornece uma interface web para trabalhar com topoologias em execução, e está incluído no seu cluster HDInsight. Para ver a Tempestade UI, utilize um navegador web para abrir `https://CLUSTERNAME.azurehdinsight.net/stormui`, onde *clusterNAME* é o nome do seu cluster.

> [!NOTE]  
> Se lhe for solicitado que forneça um nome de utilizador e uma palavra-passe, introduza o nome de utilizador do administrador do cluster e a palavra-passe que utilizou ao criar o cluster.

### <a name="storm-ui-main-page"></a>Página principal da Tempestade UI

A página principal da Tempestade UI fornece as seguintes informações:

| Section | Descrição |
| --- | --- |
| Resumo do cluster| Informação básica sobre o aglomerado de tempestades. |
| Resumo do Nimbus | Uma lista de informações básicas do Nimbus. |
| Resumo de Topologia | Uma lista de topoologias. Para ver mais informações sobre uma topologia específica, selecione o seu link na coluna **Nome.** |
| Resumo do supervisor | Informações sobre o supervisor da tempestade. Para ver os recursos dos trabalhadores associados a um supervisor específico, selecione o seu link na coluna **Anfitrião** ou **Id.** |
| Configuração Nimbus | Configuração Nimbus para o cluster. |

A página principal da Storm UI é semelhante a esta página web:

![Página principal, Tempestade UI, Apache Storm topoologias, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Resumo de Topologia

A seleção de um link da secção resumo de **Topologia** apresenta as seguintes informações sobre a topologia:

| Section | Descrição |
| --- | --- |
| Resumo de Topologia | Informação básica sobre a topologia. |
| Ações de topologia| Ações de gestão que pode fazer para a topologia. As ações disponíveis são descritas mais tarde nesta secção. |
| Estatísticas de topologia | Estatísticas sobre a topologia. Para definir o prazo para uma entrada nesta secção, selecione o seu link na coluna **Janela.** |
| Bicos *(prazo)* | Os bicos usados pela topologia. Para ver mais informações sobre um bico específico, selecione o seu link na coluna **Id.** |
| Parafusos *(prazo)* | Os parafusos usados pela topologia. Para ver mais informações sobre um parafuso específico, selecione o seu link na coluna **Id.** |
| Recursos dos trabalhadores | Uma lista de recursos dos trabalhadores. Para ver mais informações sobre um recurso específico do trabalhador, selecione o seu link na coluna **Anfitrião.** |
| Visualização de topologia | Um botão **de visualização** de espetáculos que exibe uma visualização da topologia. |
| Configuração topologia | A configuração da topologia selecionada. |

A página resumo da topologia da tempestade parece semelhante a esta página web:

![Página de resumo de topologia, Tempestade UI, Tempestade Apache, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

Na secção de ações de **Topologia,** pode selecionar os seguintes botões para fazer uma ação:

| Botão | Descrição |
| --- | --- |
| Ativar | Retoma o processamento de uma topologia desativada. |
| Desativar | Pausa uma topologia de corrida. |
| Reequilíbrio | Ajusta o paralelismo da topologia. Deve reequilibrar as topoologias depois de ter mudado o número de nós no cluster. Esta operação permite que a topologia ajuste o paralelismo para compensar o número adicional ou reduzido de nós no cluster.<br/><br/>Para mais informações, consulte <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Compreender o paralelismo de uma topologia da tempestade Apache.</a>
| Matar | Termina uma topologia de tempestade após o tempo limite especificado. |
| Depurar | Começa uma sessão de depuração para a topologia em execução. |
| Parar o Debug | Termina a sessão de depuração para a topologia de corrida. |
| Alterar o Nível de Registo | Modifica o nível de registo de depuração. |

##### <a name="spout-and-bolt-summary"></a>Resumo do bico e do parafuso

A seleção de um bico das secções **Spouts** ou **Bolts** apresenta as seguintes informações sobre o item selecionado:

| Section | Descrição |
| --- | --- |
| Resumo dos componentes | Informação básica sobre o bico ou parafuso. |
| Ações de componentes | **Depurar** e parar os botões **Debug.** |
| Estatísticas de bico ou estatísticas de parafusos | Estatísticas sobre o bico ou parafuso. Para definir o prazo para uma entrada nesta secção, selecione o seu link na coluna **Janela.** |
| (apenas parafusos)<br/>Estatísticas de entrada *(prazo)* | Informação sobre os fluxos de entrada consumidos pelo parafuso. |
| Estatísticas de saída *(período de tempo)* | Informações sobre os fluxos emitidos pelo bico ou parafuso. |
| Perfis e depuração | Controlos para perfis e depuração dos componentes nesta página. Pode definir o valor **de Estado / Tempo (Minutos)** e selecionar botões para **JStack,** **Restart Worker**e **Heap**. |
| Executores *(prazo)* | Informações sobre os casos do bico ou parafuso. Para visualizar um registo de informações de diagnóstico produzidas para este exemplo, selecione a entrada **da Porta** para um executor específico. Também pode ver os recursos do trabalhador associados a um executor específico, selecionando o seu link na coluna **Anfitrião.** |
| Erros | Qualquer informação de erro para o bico ou parafuso. |

A página de resumo do bolt storm parece semelhante a esta página web:

![Página de resumo bolt, Tempestade UI, Tempestade Apache, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitorize e gerencie a topologia utilizando a API REST

A Storm UI é construída em cima da API REST, para que você possa fazer tarefas de gestão e monitorização semelhantes usando a API REST. Você pode usar a API REST para criar ferramentas personalizadas para gerir e monitorizar topoologias storm.

Para mais informações, consulte [Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). As seguintes informações são específicas para usar a API REST com a Tempestade Apache no HDInsight.

> [!IMPORTANT]  
> A Storm REST API não está disponível publicamente através da internet. Deve ser acedido utilizando um túnel SSH para o nó de cabeça de cluster HDInsight. Para obter informações sobre a criação e utilização de um túnel SSH, consulte [O túnel SSH use para aceder ao Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Base URI

O URI base para o REST API em clusters HDInsight baseados em Linux está disponível no endereço URL `https://HEADNODEFQDN:8744/api/v1/`, onde substitui *headnodeFQDN* pelo nó de cabeça. O nome de domínio do nó da cabeça é gerado durante a criação do cluster e não é estático.

Pode encontrar o nome de domínio totalmente qualificado (FQDN) para o nó de cabeça de cluster de várias maneiras:

| Método de descoberta FQDN | Descrição |
| --- | --- |
| SEssão SSH | Utilize o comando `headnode -f` de uma sessão sSH para o cluster. |
| Teia de Ambari | Na página web do cluster Ambari (`https://CLUSTERNAME.azurehdinsight.net`), selecione **Serviços** a partir do topo da página e, em seguida, selecione **Storm**. A partir do separador **Resumo,** selecione **Storm UI Server**. O FQDN do nó que acolhe a Tempestade UI e a API REST é exibido no topo da página. |
| Ambari REST API | Use o comando `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para obter informações sobre o nó que a Tempestade UI e a Rest API estão a decorrer. Substitua as duas instâncias de *CLUSTERNAME* com o nome do cluster. Quando for solicitado, introduza a palavra-passe para a conta do utilizador (administrador). Na resposta, a entrada "host_name" da saída JSON contém o FQDN do nó. |

### <a name="authentication"></a>Autenticação

Os pedidos para a API REST devem utilizar a *autenticação básica,* pelo que tem de utilizar o nome do administrador e a palavra-passe para o cluster HDInsight.

> [!NOTE]  
> Como a autenticação básica é enviada utilizando texto claro, deve *sempre* utilizar https para proteger as comunicações com o cluster.

### <a name="return-values"></a>Valores de retorno

A informação que é devolvida da API REST só pode ser utilizável a partir do interior do cluster. Por exemplo, o nome de domínio totalmente qualificado (FQDN) devolvido para servidores [Apache ZooKeeper](https://zookeeper.apache.org/) não é acessível a partir da internet.

## <a name="next-steps"></a>Passos seguintes

Aprenda a [desenvolver topoologias baseadas em Java usando Apache Maven](apache-storm-develop-java-topology.md).

Para obter uma lista de topoologias mais exemplos, consulte [as topoologias da tempestade Apache em Azure HDInsight](apache-storm-example-topology.md).
