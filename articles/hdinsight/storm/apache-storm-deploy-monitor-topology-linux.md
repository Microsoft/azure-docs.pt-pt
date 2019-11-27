---
title: Implantar e gerenciar topologias de Apache Storm no Azure HDInsight
description: Saiba como implantar, monitorar e gerenciar topologias de Apache Storm usando o painel do Storm no HDInsight baseado em Linux. Use as ferramentas do Hadoop para Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 82c5db4f75f131ebdc2434955108e7d50237d9ba
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228944"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Implantar e gerenciar topologias de Apache Storm no Azure HDInsight 

Neste documento, Aprenda as noções básicas de gerenciamento e monitoramento de topologias [Apache Storm](https://storm.apache.org/) em execução no Storm em clusters HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Storm no HDInsight. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** para o **tipo de cluster**.

* Adicional Familiaridade com o Secure Shell (SSH) e a cópia segura (SCP). Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Adicional Visual Studio, SDK do Azure 2.5.1 ou mais recente e as ferramentas de Data Lake para Visual Studio. Para obter mais informações, consulte [Apache Hadoop & ferramentas de data Lake do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Enviar uma topologia usando o Visual Studio

Você pode usar as ferramentas de Data Lake para o Visual Studio C# para enviar ou topologias híbridas para o cluster Storm. As etapas a seguir usam um aplicativo de exemplo. Para obter informações sobre a criação de topologia usando as ferramentas de Data Lake, consulte [topologias C#de Apache Storm com o Visual Studio e ](apache-storm-develop-csharp-visual-studio-topology.md)o.

1. Se você ainda não instalou a versão mais recente do Data Lake Tools para Visual Studio, consulte [usar as ferramentas do data Lake para o Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > As ferramentas de Data Lake para Visual Studio eram anteriormente chamadas de ferramentas do HDInsight para Visual Studio.
    >
    > As ferramentas do Data Lake para Visual Studio estão incluídas na **carga de trabalho do Azure** para o visual Studio 2019.

2. Abra o Visual Studio.

3. Na janela **Iniciar** , selecione **criar um novo projeto**.

4. Na janela **criar um novo projeto** , selecione a caixa Pesquisar e insira *Storm*. Em seguida, escolha **exemplo do Storm** na lista de resultados e selecione **Avançar**.

5. Na janela **configurar seu novo projeto** , insira um **nome de projeto**e vá para ou crie um **local** para salvar o novo projeto. Em seguida, selecione **Criar**.

    ![Configurar sua nova janela de projeto, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

6. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e escolha **Enviar para o Storm no HDInsight**.

    > [!NOTE]  
    > Se solicitado, insira as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, entre com a que contém o Storm no cluster HDInsight.

7. Na caixa de diálogo **Enviar topologia** , na lista suspensa **cluster Storm** , escolha seu Storm no cluster HDInsight e, em seguida, selecione **Enviar**. Você pode monitorar se o envio foi bem-sucedido exibindo o painel de **saída** .

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Enviar uma topologia usando SSH e o comando Storm

Para enviar uma topologia para o Storm usando SSH:

1. Use o SSH para se conectar ao cluster HDInsight. Substitua `USERNAME` pelo nome do seu nome de usuário SSH (como *sshuser*). Substitua `CLUSTERNAME` pelo nome do cluster HDInsight.

    ```shell
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para obter mais informações sobre como usar o SSH para se conectar ao cluster HDInsight, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use o seguinte comando para iniciar a topologia de exemplo *WordCount* :

    ```ssh
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Deste modo, este comando inicia a topologia do WordCount de exemplo no cluster. Essa topologia gera sentenças aleatoriamente e, em seguida, conta a ocorrência de cada palavra nas sentenças.

    > [!NOTE]  
    > Ao enviar a topologia para o cluster, você deve primeiro copiar o arquivo. jar que contém o cluster antes de usar o comando `storm`. Para copiar o arquivo para o cluster, você pode usar o comando `scp`. Por exemplo, introduza `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > O exemplo de *WordCount* e outros exemplos do Storm Starter já estão incluídos no cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Enviar uma topologia programaticamente

Você pode implantar uma topologia programaticamente usando o serviço Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) fornece um exemplo de aplicativo Java que demonstra como implantar e iniciar uma topologia por meio do serviço Nimbus.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitorar e gerenciar uma topologia no Visual Studio

Quando você envia uma topologia usando o Visual Studio, a janela **exibição de topologias Storm** é exibida. Selecione a topologia na lista para exibir informações sobre a topologia em execução.

![Monitorar topologia, janela de exibição de topologias Storm, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> Você também pode exibir **topologias Storm** de **Gerenciador de servidores**. Expanda **Azure** > **HDInsight**, clique com o botão direito do mouse em um cluster Storm no HDInsight e selecione **Exibir topologias Storm**.

Selecione a forma dos limites ou dos parafusos para exibir informações sobre esses componentes. Uma dica de ferramenta com informações de componente é exibida para o item selecionado.

### <a name="deactivate-and-reactivate-a-topology"></a>Desativar e reativar uma topologia

A desativação de uma topologia a pausa até que a topologia seja eliminada ou reativada. Para executar essas operações, use os botões **desativar** e **reativar** na área **ações** na parte superior da janela **exibição de topologias Storm** .

### <a name="rebalance-a-topology"></a>Rebalancear uma topologia

O rebalanceamento de uma topologia permite que o sistema revise o paralelismo da topologia. Por exemplo, se você redimensionou o cluster para adicionar mais anotações, o rebalanceamento permite que uma topologia Veja os novos nós.

Para reequilibrar uma topologia, use o botão **rebalance** na área **ações** da janela **exibição de topologias Storm** .

> [!WARNING]  
> O rebalanceamento de uma topologia desativa a topologia, redistribui os operadores uniformemente no cluster e, em seguida, retorna a topologia para o estado em que estava antes do rebalanceamento. Se a topologia estava ativa, ela se torna ativa novamente. Se a topologia for desativada, ela permanecerá desativada.

### <a name="kill-a-running-topology"></a>Eliminar uma topologia em execução

As topologias do Storm continuam em execução até que elas sejam interrompidas ou o cluster seja excluído. Para interromper uma topologia, use o botão **eliminar** na área **ações** .

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitorar e gerenciar uma topologia usando SSH e o comando Storm

O utilitário `storm` permite que você trabalhe com topologias em execução na linha de comando. Use `storm -h` para obter uma lista completa de comandos.

### <a name="list-topologies"></a>Listar topologias

Use o seguinte comando para listar todas as topologias em execução:

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

A desativação de uma topologia a pausa até que a topologia seja eliminada ou reativada. Use os seguintes comandos para desativar ou reativar:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Eliminar uma topologia em execução

Topologias Storm, uma vez iniciadas, continuam em execução até serem interrompidas. Para interromper uma topologia, use o seguinte comando:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Rebalancear uma topologia

O rebalanceamento de uma topologia permite que o sistema revise o paralelismo da topologia. Por exemplo, se você redimensionou o cluster para adicionar mais anotações, o rebalanceamento permite que uma topologia Veja os novos nós.

> [!WARNING]  
> O rebalanceamento de uma topologia desativa a topologia, redistribui os operadores uniformemente no cluster e, em seguida, retorna a topologia para o estado em que estava antes do rebalanceamento. Se a topologia estava ativa, ela se torna ativa novamente. Se ela foi desativada, ela permanecerá desativada.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitorar e gerenciar uma topologia usando a interface do usuário do Storm

A interface do usuário do Storm fornece uma interface da Web para trabalhar com topologias em execução e está incluída em seu cluster HDInsight. Para exibir a interface do usuário do Storm, use um navegador da Web para abrir `https://CLUSTERNAME.azurehdinsight.net/stormui`, em que *ClusterName* é o nome do cluster.

> [!NOTE]  
> Se for solicitado que você forneça um nome de usuário e uma senha, insira o nome e a senha do administrador de cluster que você usou ao criar o cluster.

### <a name="storm-ui-main-page"></a>Página principal da interface do usuário do Storm

A página principal da interface do usuário do Storm fornece as seguintes informações:

| Section | Descrição |
| --- | --- |
| **Resumo do cluster** | Informações básicas sobre o cluster Storm. |
| **Resumo de Nimbus** | Uma lista de informações de Nimbus básicas. |
| **Resumo da topologia** | Uma lista de topologias em execução. Para exibir mais informações sobre uma topologia específica, selecione seu link na coluna **nome** . |
| **Resumo do supervisor** | Informações sobre o supervisor do Storm. Para ver os recursos de trabalho associados a um supervisor específico, selecione seu link na coluna **host** ou **ID** . |
| **Configuração do Nimbus** | Configuração do Nimbus para o cluster. |

A página principal da interface do usuário do Storm é semelhante a esta página da Web:

![Página principal, interface do usuário do Storm, topologias de Apache Storm, Insight do Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Resumo da topologia

A seleção de um link na seção **Resumo da topologia** exibe as seguintes informações sobre a topologia:

| Section | Descrição |
| --- | --- |
| **Resumo da topologia** | Informações básicas sobre a topologia. |
| **Ações de topologia** | Ações de gerenciamento que você pode fazer para a topologia. As ações disponíveis são descritas posteriormente nesta seção. |
| **Estatísticas de topologia** | Estatísticas sobre a topologia. Para definir o intervalo de tempo para uma entrada nesta seção, selecione seu link na coluna **janela** . |
| **Esgotamentos** *(intervalo de tempo)* | As saídas usadas pela topologia. Para exibir mais informações sobre um Spout específico, selecione seu link na coluna **ID** . |
| **Parafusos** *(intervalo de tempo)* | Os parafusos usados pela topologia. Para exibir mais informações sobre um raio específico, selecione seu link na coluna **ID** . |
| **Recursos de trabalho** | Uma lista de recursos de trabalho. Para exibir mais informações sobre um recurso de trabalho específico, selecione seu link na coluna **host** . |
| **Visualização da topologia** | Um botão **Mostrar visualização** que exibe uma visualização da topologia. |
| **Configuração de topologia** | A configuração da topologia selecionada. |

A página de resumo da topologia do Storm é semelhante a esta página da Web:

![Página de resumo da topologia, interface do usuário do Storm, Apache Storm, Insight do Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

Na seção **ações de topologia** , você pode selecionar os botões a seguir para executar uma ação:

| Button | Descrição |
| --- | --- |
| **Ativar** | Retoma o processamento de uma topologia desativada. |
| **Ativar** | Pausa uma topologia em execução. |
| **Reequilibrar** | Ajusta o paralelismo da topologia. Você deve redistribuir topologias em execução depois de alterar o número de nós no cluster. Essa operação permite que a topologia ajuste o paralelismo para compensar o número adicional ou reduzido de nós no cluster.<br/><br/>Para obter mais informações, consulte <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">noções básicas sobre o paralelismo de uma topologia de Apache Storm</a>.
| **Finaliza** | Encerra uma topologia Storm após o tempo limite especificado. |
| **Depurar** | Inicia uma sessão de depuração para a topologia em execução. |
| **Parar depuração** | Encerra a sessão de depuração para a topologia em execução. |
| **Alterar nível de log** | Modifica o nível de log de depuração. |

##### <a name="spout-and-bolt-summary"></a>Resumo do Spout e do rolo

A seleção de um Spout nas seções de estornos **ou de** **parafusos** exibe as seguintes informações sobre o item selecionado:

| Section | Descrição |
| --- | --- |
| **Resumo do componente** | Informações básicas sobre o Spout ou o parafuso. |
| **Ações de componente** | Botões **depurar** e **parar depuração** . |
| **Estatísticas de Spout** ou **Estatísticas de raio** | Estatísticas sobre o Spout ou o parafuso. Para definir o intervalo de tempo para uma entrada nesta seção, selecione seu link na coluna **janela** . |
| (Somente raio)<br/>**Estatísticas de entrada** *(período)* | Informações sobre os fluxos de entrada consumidos pelo parafuso. |
| **Estatísticas de saída** *(período)* | Informações sobre os fluxos emitidos pelo Spout ou parafuso. |
| **Criação de perfil e depuração** | Controles para criação de perfil e depuração dos componentes nesta página. Você pode definir o valor de **status/tempo limite (minutos)** e pode selecionar botões para **JStack**, **reiniciar o trabalho**e **heap**. |
| **Executores** *(período)* | Informações sobre as instâncias do Spout ou do parafuso. Para exibir um log das informações de diagnóstico produzidas para essa instância, selecione a entrada **porta** para um executor específico. Você também pode ver os recursos de trabalho associados a um executor específico selecionando seu link na coluna **host** . |
| **Los** | Qualquer informação de erro para o Spout ou o parafuso. |

A página de Resumo de parafusos do Storm é semelhante a esta página da Web:

![Página de resumo do raio, interface do usuário do Storm, Apache Storm, Insight do Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitorar e gerenciar a topologia usando a API REST

A interface do usuário do Storm é criada sobre a API REST, para que você possa fazer tarefas de gerenciamento e monitoramento semelhantes usando a API REST. Você pode usar a API REST para criar ferramentas personalizadas para gerenciar e monitorar topologias Storm.

Para obter mais informações, consulte [API REST da interface do usuário Apache Storm](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). As informações a seguir são específicas para usar a API REST com Apache Storm no HDInsight.

> [!IMPORTANT]  
> A API REST do Storm não está disponível publicamente pela Internet. Ele deve ser acessado usando um túnel SSH para o nó principal do cluster HDInsight. Para obter informações sobre como criar e usar um túnel SSH, consulte [usar o túnel SSH para acessar o Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI de base

O URI de base para a API REST em clusters HDInsight baseados em Linux está disponível no endereço de URL `https://HEADNODEFQDN:8744/api/v1/`, onde você substitui o *HEADNODEFQDN* pelo nó de cabeçalho. O nome de domínio do nó principal é gerado durante a criação do cluster e não é estático.

Você pode encontrar o FQDN (nome de domínio totalmente qualificado) para o nó principal do cluster de várias maneiras:

| Método de descoberta de FQDN | Descrição |
| --- | --- |
| Sessão SSH | Use o `headnode -f` de comando de uma sessão SSH para o cluster. |
| Ambari Web | Na página da Web do Ambari cluster (`https://CLUSTERNAME.azurehdinsight.net`), selecione **Serviços** na parte superior da página e, em seguida, selecione **Storm**. Na guia **Resumo** , selecione **servidor de interface do usuário do Storm**. O FQDN do nó que hospeda a interface do usuário do Storm e a API REST é exibido na parte superior da página. |
| API REST do Ambari | Use o comando `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para recuperar informações sobre o nó em que a interface do usuário do Storm e a API REST estão em execução. Substitua as duas instâncias de *ClusterName* pelo nome do cluster. Quando solicitado, insira a senha para a conta de usuário (administrador). Na resposta, a entrada "host_name" da saída JSON contém o FQDN do nó. |

### <a name="authentication"></a>Autenticação

As solicitações para a API REST devem usar a *autenticação básica*, portanto, você precisa usar o nome do administrador e a senha para o cluster HDInsight.

> [!NOTE]  
> Como a autenticação básica é enviada usando texto não criptografado, você *sempre* deve usar HTTPS para proteger as comunicações com o cluster.

### <a name="return-values"></a>Valores de retorno

As informações retornadas da API REST podem ser usadas somente de dentro do cluster. Por exemplo, o FQDN (nome de domínio totalmente qualificado) retornado para servidores [Apache ZooKeeper](https://zookeeper.apache.org/) não está acessível pela Internet.

## <a name="next-steps"></a>Passos seguintes

Saiba como [desenvolver topologias baseadas em Java usando o Apache Maven](apache-storm-develop-java-topology.md).

Para obter uma lista de mais topologias de exemplo, consulte o [exemplo Apache Storm topologias no Azure HDInsight](apache-storm-example-topology.md).
