---
title: Implantar e gerenciar topologias de Apache Storm no Azure HDInsight
description: Saiba como implantar, monitorar e gerenciar topologias de Apache Storm usando o painel do Storm no HDInsight baseado em Linux. Use as ferramentas do Hadoop para Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 1c219c85836eb4730fa90918385555c433a12449
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915106"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Implantar e gerenciar topologias de Apache Storm no Azure HDInsight 

Neste documento, Aprenda as noções básicas de gerenciamento e monitoramento de topologias [Apache Storm](https://storm.apache.org/) em execução no Storm em clusters HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Storm no HDInsight. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** para o **tipo de cluster**.


* Adicional Familiaridade com SSH e SCP: Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Adicional Visual Studio: SDK do Azure 2.5.1 ou mais recente e as ferramentas de Data Lake para Visual Studio. Para obter mais informações, consulte Introdução ao [uso de ferramentas de data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    Uma das seguintes versões do Visual Studio:

  * Visual Studio 2012 com atualização 4

  * Visual Studio 2013 com a atualização 4 ou [Visual Studio 2013 comunidade](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (qualquer edição)

  * Visual Studio 2017 (qualquer edição). As ferramentas de Data Lake para Visual Studio 2017 são instaladas como parte da carga de trabalho do Azure.

## <a name="submit-a-topology-visual-studio"></a>Enviar uma topologia: Visual Studio

As ferramentas do HDInsight podem ser usadas para C# enviar ou topologias híbridas para o cluster Storm. As etapas a seguir usam um aplicativo de exemplo. Para obter informações sobre como criar usando as ferramentas do HDInsight, consulte [ C# desenvolver topologias usando as ferramentas do Hdinsight para Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Se você ainda não instalou a versão mais recente do Data Lake Tools para Visual Studio, consulte Introdução ao [uso de ferramentas do data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > As ferramentas de Data Lake para Visual Studio eram anteriormente chamadas de ferramentas do HDInsight para Visual Studio.
    >
    > As ferramentas do Data Lake para Visual Studio estão incluídas na __carga de trabalho do Azure__ para o visual Studio 2017.

2. Abra o Visual Studio, selecione **arquivo** > **novo** > **projeto**.

3. Na caixa de diálogo **novo projeto** , expanda**modelos** **instalados** > e, em seguida, selecione **HDInsight**. Na lista de modelos, selecione **exemplo do Storm**. Na parte inferior da caixa de diálogo, digite um nome para o aplicativo.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

4. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Enviar para o Storm no HDInsight**.

   > [!NOTE]  
   > Se solicitado, insira as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, faça logon no que contém o Storm no cluster HDInsight.

5. Selecione seu Storm no cluster HDInsight na lista suspensa **cluster Storm** e, em seguida, selecione **Enviar**. Você pode monitorar se o envio é bem-sucedido usando a janela **saída** .

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Enviar uma topologia: SSH e o comando Storm

1. Use o SSH para se conectar ao cluster HDInsight. Substitua **username** pelo nome do seu logon SSH. Substitua **ClusterName** pelo nome do cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para saber mais sobre como usar o SSH para se conectar ao cluster HDInsight, confira [usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize o seguinte comando para iniciar uma topologia de exemplo:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Deste modo, este comando inicia a topologia do WordCount de exemplo no cluster. Essa topologia gera sentenças aleatoriamente e, em seguida, conta a ocorrência de cada palavra nas sentenças.

   > [!NOTE]  
   > Ao submeter a topologia para o cluster, primeiro, tem de copiar o ficheiro jar que contém o cluster antes de utilizar o comando `storm`. Para copiar o arquivo para o cluster, você pode usar o `scp` comando. Por exemplo, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > O exemplo de WordCount e outros exemplos de storm starter já estão incluídos no seu cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Enviar uma topologia: programaticamente

Você pode implantar uma topologia programaticamente usando o serviço Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)fornece um exemplo de aplicativo Java que demonstra como implantar e iniciar uma topologia por meio do serviço Nimbus.

## <a name="monitor-and-manage-visual-studio"></a>Monitorar e gerenciar: Visual Studio

Quando uma topologia é enviada usando o Visual Studio, a exibição **topologias Storm** é exibida. Selecione a topologia na lista para exibir informações sobre a topologia em execução.

![monitor do Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> Você também pode exibir **topologias Storm** de **Gerenciador de servidores** expandindo o **Azure** > **HDInsight**e clicando com o botão direito do mouse em um Storm no cluster HDInsight e selecionando **Exibir topologias Storm**.

Selecione a forma dos limites ou dos parafusos para exibir informações sobre esses componentes. Uma nova janela é aberta para cada item selecionado.

### <a name="deactivate-and-reactivate"></a>Desativar e reativar

A desativação de uma topologia a pausa até que seja eliminada ou reativada. Para executar essas operações, use os botões __desativar__ e __reativar__ na parte superior do __Resumo da topologia__.

### <a name="rebalance"></a>Reequilibrar

O rebalanceamento de uma topologia permite que o sistema revise o paralelismo da topologia. Por exemplo, se você redimensionou o cluster para adicionar mais anotações, o rebalanceamento permite que uma topologia Veja os novos nós.

Para reequilibrar uma topologia, use o botão __rebalance__ na parte superior do __Resumo da topologia__.

> [!WARNING]  
> O rebalanceamento de uma topologia primeiro desativa a topologia e, em seguida, redistribui os operadores uniformemente no cluster e, finalmente, retorna a topologia para o estado em que estava antes do rebalanceamento. Portanto, se a topologia estava ativa, ela se torna ativa novamente. Se ela foi desativada, ela permanecerá desativada.

### <a name="kill-a-topology"></a>Eliminar uma topologia

As topologias do Storm continuam em execução até que elas sejam interrompidas ou o cluster seja excluído. Para interromper uma topologia, use o botão __eliminar__ na parte superior do __Resumo da topologia__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Monitorar e gerenciar: SSH e o comando Storm

O `storm` utilitário permite que você trabalhe com topologias em execução na linha de comando. Use `storm -h` para obter uma lista completa de comandos.

### <a name="list-topologies"></a>Listar topologias

Use o seguinte comando para listar todas as topologias em execução:

    storm list

Este comando devolve informações semelhantes ao texto seguinte:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Desativar e reativar

A desativação de uma topologia a pausa até que seja eliminada ou reativada. Use o seguinte comando para desativar e reativar:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Eliminar uma topologia em execução

Topologias Storm, uma vez iniciadas, continuam em execução até serem interrompidas. Para interromper uma topologia, use o seguinte comando:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Reequilibrar

O rebalanceamento de uma topologia permite que o sistema revise o paralelismo da topologia. Por exemplo, se você redimensionou o cluster para adicionar mais anotações, o rebalanceamento permite que uma topologia Veja os novos nós.

> [!WARNING]  
> O rebalanceamento de uma topologia primeiro desativa a topologia e, em seguida, redistribui os operadores uniformemente no cluster e, finalmente, retorna a topologia para o estado em que estava antes do rebalanceamento. Portanto, se a topologia estava ativa, ela se torna ativa novamente. Se ela foi desativada, ela permanecerá desativada.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Monitorar e gerenciar: Interface do usuário do Storm

A IU do Storm fornece uma interface Web para trabalhar com topologias em execução e está incluída no cluster do HDInsight. Para exibir a interface do usuário do Storm, use um navegador **https://CLUSTERNAME.azurehdinsight.net/stormui** da Web para abrir, em que **ClusterName** é o nome do cluster.

> [!NOTE]  
> Se lhe for pedido que forneça um nome de utilizador e uma palavra-passe, introduza o administrador do cluster (admin) e a palavra-passe que utilizou ao criar o cluster.

### <a name="main-page"></a>Página Principal

A página principal da interface do usuário do Storm fornece as seguintes informações:

* **Resumo do cluster**: Informações básicas sobre o cluster Storm.
* **Resumo da topologia**: Uma lista de topologias em execução. Use os links nesta seção para exibir mais informações sobre topologias específicas.
* **Resumo do supervisor**: Informações sobre o supervisor do Storm.
* **Configuração do Nimbus**: Configuração do Nimbus para o cluster.

### <a name="topology-summary"></a>Resumo da topologia

A seleção de um link na seção **Resumo da topologia** exibe as seguintes informações sobre a topologia:

* **Resumo da topologia**: Informações básicas sobre a topologia.
* **Ações de topologia**: Ações de gerenciamento que você pode executar para a topologia.

  * **Ativar**: Retoma o processamento de uma topologia desativada.
  * **Desativar**: Pausa uma topologia em execução.
  * **Rebalancear**: Ajusta o paralelismo da topologia. Deve rebalancear as topologias em execução depois de ter alterado o número de nós no cluster. Essa operação permite que a topologia ajuste o paralelismo para compensar o número maior ou menor de nós no cluster.

    Para obter mais informações, consulte <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">noções básicas sobre o paralelismo de uma topologia de Apache Storm</a>.
  * **Eliminar**: Encerra uma topologia Storm após o tempo limite especificado.
* **Estatísticas de topologia**: Estatísticas sobre a topologia. Para definir o período de tempo para as entradas restantes na página, use os links na coluna **janela** .
* **Esgotamentos**: As saídas usadas pela topologia. Use os links nesta seção para exibir mais informações sobre estornos específicos.
* Os **parafusos**: Os parafusos usados pela topologia. Use os links nesta seção para exibir mais informações sobre os parafusos específicos.
* **Configuração de topologia**: A configuração da topologia selecionada.

### <a name="spout-and-bolt-summary"></a>Resumo do Spout e do rolo

A seleção de um Spout nas seções de estornos **ou de** **parafusos** exibe as seguintes informações sobre o item selecionado:

* **Resumo do componente**: Informações básicas sobre o Spout ou o parafuso.
* **Estatísticas de Spout/raio**: Estatísticas sobre o Spout ou o parafuso. Para definir o período de tempo para as entradas restantes na página, use os links na coluna **janela** .
* **Estatísticas de entrada** (somente raio): Informações sobre os fluxos de entrada consumidos pelo parafuso.
* **Estatísticas de saída**: Informações sobre os fluxos emitidos pelo Spout ou parafuso.
* **Executores**: Informações sobre as instâncias do Spout ou do parafuso. Selecione a entrada **porta** para um executor específico para exibir um log das informações de diagnóstico produzidas para essa instância.
* **Erros**: Qualquer informação de erro para o Spout ou o parafuso.

## <a name="monitor-and-manage-rest-api"></a>Monitorar e gerenciar: API REST

A interface do usuário do Storm é criada sobre a API REST, para que você possa executar funcionalidades de gerenciamento e monitoramento semelhantes usando a API REST. Você pode usar a API REST para criar ferramentas personalizadas para gerenciar e monitorar topologias Storm.

Para obter mais informações, consulte [API REST da interface do usuário Apache Storm](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). As informações a seguir são específicas para usar a API REST com Apache Storm no HDInsight.

> [!IMPORTANT]  
> A API REST do Storm não está disponível publicamente pela Internet e deve ser acessada usando um túnel SSH para o nó principal do cluster HDInsight. Para obter informações sobre como criar e usar um túnel SSH, consulte [usar o túnel SSH para acessar a IU da Web do Apache Ambari, ResourceManager, JobHistory, NameNode, Apache Oozie e outras interfaces do usuário da Web](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI de base

O URI de base para a API REST em clusters HDInsight baseados em Linux está disponível no nó principal em **https:\//HEADNODEFQDN: 8744/API/v1/** . O nome de domínio do nó principal é gerado durante a criação do cluster e não é estático.

Você pode encontrar o FQDN (nome de domínio totalmente qualificado) para o nó principal do cluster de várias maneiras diferentes:

* **De uma sessão SSH**: Use o comando `headnode -f` de uma sessão SSH para o cluster.
* **Da Web do Ambari**: Selecione **Serviços** na parte superior da página e, em seguida, selecione **Storm**. Na guia **Resumo** , selecione **servidor de interface do usuário do Storm**. O FQDN do nó que hospeda a interface do usuário do Storm e a API REST é exibido na parte superior da página.
* **Da API REST do amAmbari**: Use o comando `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para recuperar informações sobre o nó em que a interface do usuário do Storm e a API REST estão em execução. Substitua **ClusterName** pelo nome do cluster. Quando solicitado, insira a senha para a conta de logon (administrador). Na resposta, a entrada "HOST_NAME" contém o FQDN do nó.

### <a name="authentication"></a>Authentication

As solicitações para a API REST devem usar a **autenticação básica**, portanto, você usa o nome de administrador do cluster HDInsight e a senha.

> [!NOTE]  
> Como a autenticação básica é enviada usando texto não criptografado, você **sempre** deve usar HTTPS para proteger as comunicações com o cluster.

### <a name="return-values"></a>Valores de retorno

As informações retornadas da API REST podem ser usadas somente de dentro do cluster. Por exemplo, o FQDN (nome de domínio totalmente qualificado) retornado para servidores de [Apache ZooKeeper](https://zookeeper.apache.org/) não é acessível pela Internet.

## <a name="next-steps"></a>Próximos Passos

Saiba como [desenvolver topologias baseadas em Java usando o Apache Maven](apache-storm-develop-java-topology.md).

Para obter uma lista de mais topologias de exemplo, consulte [topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md).
