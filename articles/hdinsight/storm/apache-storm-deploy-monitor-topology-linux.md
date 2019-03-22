---
title: Implementar e gerir topologias do Apache Storm no HDInsight do Azure
description: Saiba como implementar, monitorizar e gerir topologias do Apache Storm com o Dashboard do Storm no HDInsight baseado em Linux. Utilize as ferramentas Hadoop do Visual Studio.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 5f6708a9c22939395f992c2ac58a7e510b35f763
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317277"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Implementar e gerir topologias do Apache Storm no HDInsight do Azure 

Neste documento, aprender as noções básicas de gerenciamento e monitoramento [Apache Storm](https://storm.apache.org/) topologias em execução no Storm no HDInsight clusters.

> [!IMPORTANT]  
> Os passos neste artigo requerem um Storm baseado em Linux num cluster do HDInsight. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows). 
>
> Para obter informações sobre como implementar e monitorizar topologias no HDInsight baseado em Windows, consulte [implementar e gerir topologias do Apache Storm no HDInsight baseado em Windows](apache-storm-deploy-monitor-topology.md).


## <a name="prerequisites"></a>Pré-requisitos

* **Um Storm baseado em Linux no HDInsight cluster**: veja [introdução ao Apache Storm no HDInsight](apache-storm-tutorial-get-started-linux.md) para obter os passos sobre como criar um cluster

* (Opcional) **Familiaridade com o SSH e SCP**: Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Opcional) **Visual Studio**: SDK do Azure 2.5.1 ou mais recente e ferramentas do Data Lake para Visual Studio. Para obter mais informações, consulte [começar a utilizar o Data Lake Tools para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    Uma das seguintes versões do Visual Studio:

  * O Visual Studio 2012 com atualização 4

  * Visual Studio 2013 com a atualização 4 ou [Comunidade do Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (qualquer edição)

  * Visual Studio 2017 (qualquer edição). Ferramentas do Data Lake para Visual Studio 2017 são instaladas como parte da carga de trabalho do Azure.

## <a name="submit-a-topology-visual-studio"></a>Submeta uma topologia: Visual Studio

As ferramentas do HDInsight pode ser utilizadas para submeter as topologias de c# ou híbrida no seu cluster do Storm. Os passos seguintes utilizam um aplicativo de exemplo. Para obter informações sobre como criar sobre como utilizar as ferramentas do HDInsight, consulte [desenvolver topologias c# com as ferramentas do HDInsight para Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Se ainda não tiver instalado a versão mais recente das ferramentas do Data Lake para Visual Studio, consulte [começar a utilizar o Data Lake Tools para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > O Data Lake Tools para Visual Studio eram anteriormente denominado as ferramentas do HDInsight para Visual Studio.
    >
    > Ferramentas do Data Lake para Visual Studio estão incluídas no __carga de trabalho do Azure__ para Visual Studio 2017.

2. Abra o Visual Studio, selecione **arquivo** > **New** > **projeto**.

3. Na **novo projeto** caixa de diálogo caixa, expanda **instalado** > **modelos**e, em seguida, selecione **HDInsight**. Na lista de modelos, selecione **exemplo de Storm**. Na parte inferior da caixa de diálogo, escreva um nome para a aplicação.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. Na **Explorador de soluções**, clique com o botão direito no projeto e selecione **submeter para Storm no HDInsight**.

   > [!NOTE]  
   > Se lhe for pedido, introduza as credenciais de início de sessão para a sua subscrição do Azure. Se tiver mais de uma subscrição, inicie sessão no que contém o seu cluster Storm no HDInsight.

5. Selecione o Storm no cluster do HDInsight a partir da **Cluster do Storm** na lista pendente e, em seguida, selecione **submeter**. Pode monitorizar se a submissão for bem sucedida, utilizando o **saída** janela.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Submeta uma topologia: SSH e o comando de Storm

1. Utilize o SSH para ligar ao cluster do HDInsight. Substitua **nome de utilizador** o nome do seu início de sessão SSH. Substitua **CLUSTERNAME** com o nome do cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obter mais informações sobre como utilizar o SSH para ligar ao cluster do HDInsight, consulte [utilizar o SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize o seguinte comando para iniciar uma topologia de exemplo:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Deste modo, este comando inicia a topologia do WordCount de exemplo no cluster. Esta topologia gera aleatoriamente frases e, em seguida, a conta a ocorrência de cada palavra nas frases.

   > [!NOTE]  
   > Ao submeter a topologia para o cluster, primeiro, tem de copiar o ficheiro jar que contém o cluster antes de utilizar o comando `storm`. Para copiar o ficheiro para o cluster, pode utilizar o `scp` comando. Por exemplo, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > O exemplo de WordCount e outros exemplos de storm starter já estão incluídos no seu cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Submeter uma topologia: por meio de programação

Por meio de programação, pode implementar uma topologia de utilizar o serviço de Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) Fornece um exemplo de aplicação de Java que demonstra como implementar e iniciar uma topologia através do serviço de Nimbus.

## <a name="monitor-and-manage-visual-studio"></a>Monitorizar e gerir: Visual Studio

Quando uma topologia é submetida com o Visual Studio, o **topologias do Storm** exibição aparece. Selecione a topologia da lista para ver informações sobre a topologia em execução.

![monitor do Visual studio](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]  
> Também pode ver **topologias do Storm** partir **Explorador de servidores** ao expandir **Azure** > **HDInsight**e, em seguida, clicar com o botão direito um Storm num cluster do HDInsight e selecionando **Zobrazit Topologie Stormu**.

Selecione a forma de spouts ou bolts para ver informações sobre estes componentes. É aberta uma janela nova para cada item selecionado.

### <a name="deactivate-and-reactivate"></a>Desativar e reativar as

Desativar uma topologia de pausa-lo até que ele seja eliminado ou reativado. Para efetuar estas operações, utilize o __desativar__ e __reativar__ botões na parte superior do __resumo da topologia__.

### <a name="rebalance"></a>Reequilibrar

Reequilibrar uma topologia permite que o sistema rever o paralelismo da topologia. Por exemplo, se tiver redimensionado o cluster para adicionar mais notas, reequilibrar permite uma topologia ver os novos nós.

Para reequilibrar uma topologia, utilize o __reequilibrar__ botão na parte superior a __resumo da topologia__.

> [!WARNING]  
> Reequilibrar uma topologia de primeiro desativa a topologia, em seguida, redistribui trabalhadores uniformemente no cluster, em seguida, finalmente retorna a topologia para o estado em que estava antes de reequilíbrio ocorreu. Se a topologia do Active Directory, ele se torna então active novamente. Se ele foi desativado, continua a ser desativado.

### <a name="kill-a-topology"></a>Eliminar uma topologia

Topologias do Storm continuam em execução até que estão paradas ou o cluster é eliminado. Para parar uma topologia, utilize o __Kill__ botão na parte superior a __resumo da topologia__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Monitorizar e gerir: SSH e o comando de Storm

O `storm` utilitário permite-lhe trabalhar com topologias em execução na linha de comando. Utilize `storm -h` para obter uma lista completa de comandos.

### <a name="list-topologies"></a>Topologias de lista

Utilize o seguinte comando para listar todas as nas topologias em execução:

    storm list

Este comando devolve informações semelhantes ao texto seguinte:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Desativar e reativar as

Desativar uma topologia de pausa-lo até que ele seja eliminado ou reativado. Utilize o comando seguinte para desativar e reativar as:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Eliminar uma topologia em execução

Topologias, uma vez iniciadas o Storm, continuar em execução até serem parados. Para parar uma topologia, utilize o seguinte comando:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Reequilibrar

Reequilibrar uma topologia permite que o sistema rever o paralelismo da topologia. Por exemplo, se tiver redimensionado o cluster para adicionar mais notas, reequilibrar permite uma topologia ver os novos nós.

> [!WARNING]  
> Reequilibrar uma topologia de primeiro desativa a topologia, em seguida, redistribui trabalhadores uniformemente no cluster, em seguida, finalmente retorna a topologia para o estado em que estava antes de reequilíbrio ocorreu. Se a topologia do Active Directory, ele se torna então active novamente. Se ele foi desativado, continua a ser desativado.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Monitorizar e gerir: IU do Storm

A IU do Storm fornece uma interface Web para trabalhar com topologias em execução e está incluída no cluster do HDInsight. Para ver a IU do Storm, utilize um browser para abrir **https://CLUSTERNAME.azurehdinsight.net/stormui**, em que **CLUSTERNAME** é o nome do seu cluster.

> [!NOTE]  
> Se lhe for pedido que forneça um nome de utilizador e uma palavra-passe, introduza o administrador do cluster (admin) e a palavra-passe que utilizou ao criar o cluster.

### <a name="main-page"></a>Página principal

A página principal da IU do Storm fornece as seguintes informações:

* **Resumo do cluster**: Informações básicas sobre o cluster do Storm.
* **Resumida da topologia**: Uma lista de execução de topologias. Use os links nesta secção para ver mais informações sobre topologias específicas.
* **Resumo do supervisor**: Informações sobre o supervisor de Storm.
* **Configuração de nimbus**: Configuração de nimbus para o cluster.

### <a name="topology-summary"></a>Resumida da topologia

Selecionar uma ligação a partir da **resumo da topologia** secção apresenta as seguintes informações sobre a topologia:

* **Resumida da topologia**: Informações básicas sobre a topologia.
* **Ações de topologia**: Ações de gestão que pode efetuar para a topologia.

  * **Ativar**: Retoma o processamento de uma topologia desativada.
  * **Desativar**: Coloca em pausa uma topologia em execução.
  * **Reequilibrar**: Ajusta o paralelismo da topologia. Deve rebalancear as topologias em execução depois de ter alterado o número de nós no cluster. Esta operação permite que a topologia ajuste o paralelismo para compensar o número de maior ou menor de nós do cluster.

    Para obter mais informações, consulte <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">compreender o paralelismo de uma topologia do Apache Storm</a>.
  * **Eliminar**: Termina uma topologia do Storm após o tempo limite especificado.
* **Estatísticas de topologia**: Estatísticas sobre a topologia. Para definir o período de tempo para as entradas restantes na página, utilize as ligações na **janela** coluna.
* **Spouts**: Os spouts utilizados pela topologia. Use os links nesta secção para ver mais informações sobre spouts específicos.
* **Bolts**: Os bolts utilizados pela topologia. Use os links nesta secção para ver mais informações sobre bolts específicos.
* **Configuração da topologia**: A configuração da topologia selecionada.

### <a name="spout-and-bolt-summary"></a>Spout e o resumo de Bolt

Selecionar um spout do **Spouts** ou **Bolts** secções apresenta as seguintes informações sobre o item selecionado:

* **Resumo dos componentes**: Informações básicas sobre o spout ou bolt.
* **Estatísticas de spout/Bolt**: Estatísticas sobre o spout ou bolt. Para definir o período de tempo para as entradas restantes na página, utilize as ligações na **janela** coluna.
* **Estatísticas de entrada** (apenas bolt): Informações sobre os fluxos de entrada consumidos pelo bolt.
* **Estatísticas de saída**: Informações sobre os fluxos emitidos pelo spout ou bolt.
* **Executores**: Informações sobre as instâncias do spout ou bolt. Selecione o **porta** produzidos de entrada para um executor específico ver um log de informações de diagnóstico para esta instância.
* **Erros**: Qualquer informação de erro para o spout ou bolt.

## <a name="monitor-and-manage-rest-api"></a>Monitorizar e gerir: API REST

A IU do Storm baseia-se com base na API do REST, para que possa executar semelhantes de gestão e monitorização funcionalidade com a API de REST. Pode utilizar a API REST para criar ferramentas personalizadas para gerir e monitorizar topologias do Storm.

Para obter mais informações, consulte [API de REST de interface do Usuário do Apache Storm](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). As seguintes informações são específicas para utilizar a API de REST com o Apache Storm no HDInsight.

> [!IMPORTANT]  
> A API de REST do Storm não está disponível publicamente na Internet e tem de ser acessada através de um túnel SSH ao nó principal do cluster do HDInsight. Para obter informações sobre como criar e utilizar um túnel SSH, veja [utilizar túnel SSH para aceder à IU web do Apache Ambari, ResourceManager, JobHistory, NameNode, Apache Oozie e outras interfaces do usuário de web](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI de base

O URI de base para a API de REST em clusters do HDInsight baseado em Linux está disponível no nó principal em **https:\//HEADNODEFQDN:8744/api/v1/**. O nome de domínio do nó principal é gerado durante a criação do cluster e não é estático.

Pode encontrar o nome de domínio completamente qualificado (FQDN) para o nó principal do cluster de várias maneiras diferentes:

* **Numa sessão SSH**: Utilize o comando `headnode -f` numa sessão SSH para o cluster.
* **Da Web do Ambari**: Selecione **serviços** na parte superior da página, em seguida, selecione **Storm**. Do **resumo** separador, selecione **servidor de IU do Storm**. O FQDN do nó que aloja a API de REST de IU do Storm e é apresentado na parte superior da página.
* **A partir da API REST Ambari**: Utilize o comando `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para obter informações sobre a API de REST e a IU do Storm em execução no nó. Substitua **CLUSTERNAME** com o nome do cluster. Quando lhe for pedido, introduza a palavra-passe da conta de início de sessão (admin). Em resposta, a entrada de "host_name" contém o FQDN do nó.

### <a name="authentication"></a>Authentication

Tem de utilizar pedidos para a API REST **autenticação básica**, por isso, utilize o nome de administrador de cluster do HDInsight e a palavra-passe.

> [!NOTE]  
> Uma vez que a autenticação básica é enviada com o texto não criptografado, deve **sempre** utilizar HTTPS para proteger as comunicações com o cluster.

### <a name="return-values"></a>Valores de retorno

Informações que são devolvidas a partir da API de REST podem apenas ser utilizáveis no dentro do cluster. Por exemplo, o nome domínio completamente qualificado (FQDN) para devolvidos [Apache ZooKeeper](https://zookeeper.apache.org/) servidores não está acessível a partir da Internet.

## <a name="next-steps"></a>Próximos Passos

Saiba como [topologias baseadas em Java desenvolver com o Apache Maven](apache-storm-develop-java-topology.md).

Para obter uma lista das topologias de exemplo mais, consulte [topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md).
