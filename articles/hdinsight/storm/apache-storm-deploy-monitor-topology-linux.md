---
title: Implementar e gerir as topologias da Tempestade Apache em Azure HDInsight
description: Aprenda a implantar, monitorizar e gerir as topologias da Tempestade Apache utilizando o Painel de Tempestade em HDInsight baseado em Linux. Use ferramentas Hadoop para Estúdio Visual.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: fb57992c8e26560061faf68443c4993801f9713d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871576"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Implementar e gerir as topologias da Tempestade Apache em Azure HDInsight

Neste documento, aprenda os fundamentos da gestão e monitorização das topologias [da Tempestade Apache](https://storm.apache.org/) em execução em clusters Storm on HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado de tempestade Apache em HDInsight. Consulte [os clusters Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** for **Cluster type**.

* (Opcional) Familiaridade com Secure Shell (SSH) e Secure Copy (SCP). Para obter mais informações, veja [Ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Opcional) Visual Studio, Azure SDK 2.5.1 ou mais recente, e as Ferramentas do Lago de Dados para O Estúdio Visual. Para mais informações, consulte [Apache Hadoop & Ferramentas do Lago de Dados do Estúdio Visual.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="submit-a-topology-using-visual-studio"></a>Submeta uma topologia usando o Visual Studio

Pode utilizar as Ferramentas do Lago de Dados para o Estúdio Visual para submeter c# ou topologias híbridas ao seu cluster Storm. Os seguintes passos utilizam uma aplicação de amostra. Para obter informações sobre a criação de topologia utilizando as Ferramentas do Lago de [Dados, consulte as topologias da Tempestade Apache com Visual Studio e C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. Se ainda não instalou a versão mais recente das ferramentas data lake para o Estúdio Visual, consulte [Use Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > As Ferramentas Azure Data Lake e Stream Analytics foram anteriormente chamadas de HdInsight Tools for Visual Studio.
    >
    > Azure Data Lake e Stream Analytics Tools for Visual Studio estão incluídos na carga de trabalho de desenvolvimento do **Azure** para o Visual Studio 2019.

1. Inicie o Visual Studio.

1. Na janela **Iniciar,** **selecione Criar um novo projeto.**

1. Na **janela criar uma nova** janela do projeto, selecione a caixa de pesquisa e introduza `Storm` . Em seguida, escolha **Storm Sample** na lista de resultados e selecione **Seguinte**.

1. Na **janela Configure** a sua nova janela do projeto, insira um **nome de Projeto,** e vá ou crie uma **Localização** para salvar o novo projeto em. Em seguida, selecione **Criar**.

    :::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png" alt-text="Configure a sua nova janela de projeto, Visual Studio" border="true":::

1. A partir do **Server Explorer,** clique com o botão direito **Azure** e selecione **Connect to Microsoft Azure Subscription...** e complete o processo de iniciar súpido.

1. A partir do **Solution Explorer,** clique com o botão direito do projeto e escolha **Submeter-se a Tempestade em HDInsight**.

    > [!NOTE]  
    > Se solicitado, insira as credenciais de login para a sua subscrição Azure. Se tiver mais de uma subscrição, inscreva-se na que contém o seu cluster Storm on HDInsight.

1. Na caixa de diálogo **'Enviar Topologia',** sob a lista de recuos do **Storm Cluster,** escolha a tempestade no cluster HDInsight e, em seguida, selecione **Enviar por isso**. Pode monitorizar se a submissão é bem sucedida visualizando o painel **de saída.**

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Submeta uma topologia usando SSH e o comando da tempestade

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sua sessão de ssh, use o seguinte comando para iniciar a topologia do exemplo **wordCount:**

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Deste modo, este comando inicia a topologia do WordCount de exemplo no cluster. Esta topologia gera frases aleatoriamente, e depois conta a ocorrência de cada palavra nas frases.

    > [!NOTE]  
    > Ao submeter a topologia ao cluster, deve primeiro copiar o ficheiro .jar que contém o cluster antes de utilizar o `storm` comando. Para copiar o ficheiro para o cluster, pode utilizar o `scp` comando. Por exemplo, introduza `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > O exemplo *wordCount,* e outros exemplos de arranque de tempestade, já estão incluídos no seu cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/` .

## <a name="submit-a-topology-programmatically"></a>Submeta uma topologia programática

Pode implantar programáticamente uma topologia utilizando o serviço Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) fornece um exemplo de aplicação Java que demonstra como implementar e iniciar uma topologia através do serviço Nimbus.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitorize e gere uma topologia em Visual Studio

Quando submete uma topologia usando o Visual Studio, aparece a janela **Storm Toplogies View.** Selecione a topologia da lista para ver informações sobre a topologia de execução.

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png" alt-text="Topologia monitor, Storm Toplogies View window, Visual Studio" border="true":::

> [!NOTE]  
> Também pode ver **As Topologias** de Tempestade do Explorador de **Servidores.** Expanda **a Azure**  >  **HDInsight,** clique à direita numa tempestade no cluster HDInsight e, em seguida, selecione **Ver Topologias de Tempestade**.

Selecione a forma para os bicos ou parafusos para visualizar informações sobre estes componentes. Uma ponta de ferramenta com informação de componente aparece para o item selecionado.

### <a name="deactivate-and-reactivate-a-topology"></a>Desativar e reativar uma topologia

Desativar uma topologia faz uma pausa até que a topologia seja morta ou reativada. Para fazer estas operações, utilize os botões **Desativar** e **Reativar** na área **de Ações** no topo da janela **Storm Toplogies View.**

### <a name="rebalance-a-topology"></a>Reequilibrar uma topologia

Reequilibar uma topologia permite ao sistema rever o paralelismo da topologia. Por exemplo, se redimensionar o cluster para adicionar mais notas, o reequilíbrio permite que uma topologia veja os novos nós.

Para reequilibrar uma topologia, utilize o botão **reequilíbrio** na área de **Ações** da janela **Storm Toplogies View.**

> [!WARNING]  
> Reequilibar uma topologia desativa a topologia, redistribui os trabalhadores uniformemente através do cluster, e depois devolve a topologia ao estado em que estava antes do reequilíbrio ocorrer. Se a topologia estava ativa, volta a estar ativa. Se a topologia foi desativada, continua desativada.

### <a name="kill-a-running-topology"></a>Matar uma topologia de corrida

As topologias da tempestade continuam a funcionar até serem paradas ou o aglomerado ser apagado. Para parar uma topologia, utilize o botão **Kill** na área **de Ações.**

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitorize e gere uma topologia usando o SSH e o comando da tempestade

O `storm` utilitário permite-lhe trabalhar com topologias de execução a partir da linha de comando. Utilize `storm -h` para uma lista completa de comandos.

### <a name="list-topologies"></a>Lista de toplogies

Utilize o seguinte comando para listar todas as topologias em execução:

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

Desativar uma topologia faz uma pausa até que a topologia seja morta ou reativada. Utilize os seguintes comandos para desativar ou reativar:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Matar uma topologia de corrida

As topologias da tempestade, uma vez iniciadas, continuam a correr até pararem. Para parar uma topologia, utilize o seguinte comando:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Reequilibrar uma topologia

Reequilibar uma topologia permite ao sistema rever o paralelismo da topologia. Por exemplo, se redimensionar o cluster para adicionar mais notas, o reequilíbrio permite que uma topologia veja os novos nós.

> [!WARNING]  
> Reequilibar uma topologia desativa a topologia, redistribui os trabalhadores uniformemente através do cluster, e depois devolve a topologia ao estado em que estava antes do reequilíbrio ocorrer. Se a topologia estava ativa, volta a estar ativa. Se foi desativado, continua desativado.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitorize e gere uma topologia usando a UI tempestade

O Storm UI fornece uma interface web para trabalhar com topologias de execução, e está incluído no seu cluster HDInsight. Para ver o Storm UI, use um navegador web para `https://CLUSTERNAME.azurehdinsight.net/stormui` abrir, onde *clustername* é o nome do seu cluster.

> [!NOTE]  
> Se lhe for pedido que forneça um nome de utilizador e uma palavra-passe, insira o nome de utilizador do administrador de cluster e a palavra-passe que utilizou ao criar o cluster.

### <a name="storm-ui-main-page"></a>Página principal da Tempestade UI

A página principal do UI da tempestade fornece as seguintes informações:

| Section | Description |
| --- | --- |
| Resumo do cluster| Informação básica sobre o aglomerado de tempestades. |
| Resumo de Nimbus | Uma lista de informações básicas do Nimbus. |
| Resumo da topologia | Uma lista de topologias em execução. Para ver mais informações sobre uma topologia específica, selecione o seu link na coluna **Nome.** |
| Resumo do supervisor | Informação sobre o supervisor da tempestade. Para ver os recursos do trabalhador associados a um supervisor específico, selecione a sua ligação na coluna **Host** ou **Id.** |
| Configuração nimbus | Configuração nimbus para o cluster. |

A página principal da Storm UI é semelhante a esta página web:

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png" alt-text="Página principal, Storm UI, Apache Storm toplogies, Azure" border="true":::

#### <a name="topology-summary"></a>Resumo da topologia

A seleção de um link da secção **de resumo topologia** apresenta as seguintes informações sobre a topologia:

| Section | Description |
| --- | --- |
| Resumo da topologia | Informação básica sobre a topologia. |
| Ações de topologia| Ações de gestão que pode fazer pela topologia. As ações disponíveis são descritas mais tarde nesta secção. |
| Estatísticas de topologia | Estatísticas sobre a topologia. Para definir o prazo para uma entrada nesta secção, selecione o seu link na coluna **Janela.** |
| Bicos *(prazo)* | Os bicos usados pela topologia. Para ver mais informações sobre um bico específico, selecione o seu link na coluna **Id.** |
| Parafusos *(prazo)* | Os parafusos usados pela topologia. Para ver mais informações sobre um parafuso específico, selecione o seu link na coluna **Id.** |
| Recursos dos trabalhadores | Uma lista de recursos operários. Para ver mais informações sobre um recurso de trabalhador específico, selecione a sua ligação na coluna **Host.** |
| Visualização topologia | Um **botão de visualização** do espetáculo que exibe uma visualização da topologia. |
| Configuração topologia | A configuração da topologia selecionada. |

A página de resumo da topologia da tempestade é semelhante a esta página web:

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png" alt-text="Página de resumo de topologia, Tempestade UI, Tempestade Apache, Azure" border="true":::

Na secção **de ações de Topologia,** pode selecionar os seguintes botões para fazer uma ação:

| Botão | Description |
| --- | --- |
| Ativar | Retoma o processamento de uma topologia desativada. |
| Desativar | Pausa numa topologia de corrida. |
| Reequilíbrio | Ajusta o paralelismo da topologia. Devias reequilibrar as topologias depois de teres mudado o número de nós no agrupamento. Esta operação permite que a topologia ajuste o paralelismo para compensar o número adicional ou reduzido de nós no cluster.<br/><br/>Para obter mais informações, consulte <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">compreender o paralelismo de uma topologia da Tempestade Apache.</a>
| Matar | Termina uma topologia da tempestade após o tempo limite especificado. |
| Depurar | Começa uma sessão de depuragem para a topologia de corrida. |
| Pare Debug | Termina a sessão de depurar para a topologia de corrida. |
| Alterar nível de registo | Modifica o nível de registo de depuragem. |

##### <a name="spout-and-bolt-summary"></a>Resumo do bico e do parafuso

A seleção de um bico nas secções **Descascados** ou **Parafusos** apresenta as seguintes informações sobre o item selecionado:

| Section | Description |
| --- | --- |
| Resumo do componente | Informações básicas sobre o bico ou parafuso. |
| Ações componentes | **Botões Debug** e **Stop Debug.** |
| Estatísticas de bico ou estatísticas de parafusos | Estatísticas sobre o bico ou parafuso. Para definir o prazo para uma entrada nesta secção, selecione o seu link na coluna **Janela.** |
| (Apenas para parafusos)<br/>Estatísticas de entrada *(prazo)* | Informação sobre os fluxos de entrada consumidos pelo parafuso. |
| Estatísticas de saída *(prazo)* | Informação sobre os fluxos emitidos pelo bico ou parafuso. |
| Perfis e depuragem | Controlos para perfis e depurações dos componentes nesta página. Pode definir o valor **Status / Timeout (Minutos)** e pode selecionar botões para **JStack,** **Restart Worker** e **Heap**. |
| Executores *(prazo)* | Informação sobre os casos do bico ou parafuso. Para visualizar um registo de informações de diagnóstico produzidas para este caso, selecione a entrada do **Porto** para um executor específico. Também pode ver os recursos dos trabalhadores associados a um executor específico selecionando o seu link na coluna **Host.** |
| Erros | Qualquer informação de erro para o bico ou parafuso. |

A página de resumo do parafuso de tempestade é semelhante a esta página web:

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png" alt-text="Página sumária do parafuso, Storm UI, Apache Storm, Azure" border="true":::

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitorize e gere a topologia utilizando a API REST

O UI storm é construído em cima da API REST, para que você possa fazer tarefas de gestão e monitorização semelhantes usando a API REST. Pode utilizar a API REST para criar ferramentas personalizadas para gerir e monitorizar as topologias da Tempestade.

Para mais informações, consulte [Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). As seguintes informações são específicas para a utilização da API REST com Apache Storm em HDInsight.

> [!IMPORTANT]  
> A API Storm REST não está disponível publicamente através da internet. Deve ser acedido através de um túnel SSH para o nó de cabeça do cluster HDInsight. Para obter informações sobre a criação e utilização de um túnel [SSH, consulte o túnel SSH para aceder ao Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Base URI

O URI base para o REST API nos clusters HDInsight baseados em Linux está disponível no endereço `https://HEADNODEFQDN:8744/api/v1/` URL, onde substitui *o HEADNODEFQDN* pelo nó de cabeça. O nome de domínio do nó da cabeça é gerado durante a criação do cluster e não é estático.

Pode encontrar o nome de domínio totalmente qualificado (FQDN) para o nó da cabeça do cluster de várias maneiras:

| Método de descoberta FQDN | Description |
| --- | --- |
| Sessão SSH | Utilize o comando `headnode -f` de uma sessão de SSH para o cluster. |
| Teia Ambari | Na página web do cluster Ambari `https://CLUSTERNAME.azurehdinsight.net` (), selecione **Serviços** a partir do topo da página e, em seguida, selecione **Storm**. A partir do separador **Resumo,** selecione **Storm UI Server**. O FQDN do nó que acolhe a API storm ui e rest é apresentado no topo da página. |
| Ambari REST API | Use o comando `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para recuperar informações sobre o nó que a API storm ui e rest Estão a funcionar. Substitua as duas instâncias do *CLUSTERNAME* pelo nome do cluster. Quando for solicitado, introduza a palavra-passe para a conta do utilizador (administrador). Na resposta, a entrada "host_name" da saída JSON contém o FQDN do nó. |

### <a name="authentication"></a>Autenticação

Os pedidos à API REST devem utilizar *a autenticação básica,* pelo que tem de utilizar o nome e a palavra-passe do administrador para o cluster HDInsight.

> [!NOTE]  
> Como a autenticação básica é enviada utilizando texto claro, deve *sempre* utilizar HTTPS para garantir comunicações com o cluster.

### <a name="return-values"></a>Valores de retorno

As informações que são devolvidas da API REST só podem ser utilizáveis dentro do cluster. Por exemplo, o nome de domínio totalmente qualificado (FQDN) devolvido para servidores [Apache ZooKeeper](https://zookeeper.apache.org/) não está acessível a partir da internet.

## <a name="next-steps"></a>Passos seguintes

Saiba como [desenvolver topologias baseadas em Java usando Apache Maven.](apache-storm-develop-java-topology.md)

Para obter uma lista de mais topologias de exemplo, consulte [as topologias da Tempestade Apache em Azure HDInsight](apache-storm-example-topology.md).
