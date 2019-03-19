---
title: Exemplos do storm-starter no Apache Storm no HDInsight - Azure
description: Saiba como fazer a análise de macrodados e processar dados em tempo real com o Apache Storm e os exemplos do storm-starter no HDInsight.
keywords: storm-starter, exemplo do apache storm
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 5c0fe0921446dde12771bacf096f806701b90444
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118004"
---
# <a name="get-started-with-apache-storm-on-hdinsight-using-the-storm-starter-examples"></a>Introdução ao Apache Storm no HDInsight com os exemplos do storm-starter

Aprenda a usar [Apache Storm](https://storm.apache.org/) no HDInsight com os exemplos do storm-starter.

O Apache Storm é um sistema de cálculo dimensionável, tolerante a falhas, distribuído e em tempo real para o processamento de fluxos de dados. Com o Storm no Azure HDInsight, pode criar um cluster do Storm baseado na nuvem que executa a análise de macrodados em tempo real.

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Estar familiarizado com o SSH e o SCP**. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-storm-cluster"></a>Criar um cluster do Apache Storm

Utilize os seguintes passos para criar um Storm num cluster HDInsight:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Navegue para **+ criar um recurso** > **análise** > **HDInsight**.

    ![Criar um cluster HDInsight](./media/apache-storm-tutorial-get-started-linux/create-hdinsight.png)

2. Na secção **Informações Básicas**, introduza as seguintes informações:

    * **Nome do cluster**: O nome do cluster do HDInsight.
    * **Subscrição**: Selecione a subscrição a utilizar.
    * **Nome de início de sessão do cluster** e **palavra-passe de início de sessão do Cluster**: O início de sessão quando aceder ao cluster através de HTTPS. Utilize estas credenciais para aceder aos serviços, como a IU Web do Ambari ou a API REST.
    * **Secure Shell (SSH) nome de utilizador**: O início de sessão utilizado ao aceder ao cluster através de SSH. Por predefinição, a palavra-passe é igual à palavra-passe de início de sessão do cluster.
    * **Grupo de recursos**: O grupo de recursos em que o cluster é criado.
    * **Localização**: A região do Azure em que o cluster é criado.

   ![Selecionar subscrição](./media/apache-storm-tutorial-get-started-linux/hdinsight-basic-configuration.png)

3. Selecione **Tipo de cluster**e, em seguida, defina os seguintes valores na secção **Configuração de cluster**:

    * **Tipo de cluster**: Storm

    * **Sistema operativo**: Linux

    * **Versão**: Storm 1.1.0 (HDI 3.6)

   Por fim, utilize o botão **Selecionar** para guardar as definições.

    ![Selecionar tipo de cluster](./media/apache-storm-tutorial-get-started-linux/set-hdinsight-cluster-type.png)

4. Depois de selecionar o tipo de cluster, utilize o botão __Selecionar__ para definir o tipo de cluster. Em seguida, utilize o botão __Seguinte__ para concluir a configuração básica.

5. Na secção **Armazenamento**, selecione ou crie uma Conta de armazenamento. Para seguir os passos neste documento, deixe os outros campos nesta secção com os valores predefinidos. Utilize o botão __Seguinte__ para guardar a configuração do armazenamento. Para obter mais informações sobre como utilizar a geração 2 de armazenamento do Data Lake, veja [início rápido: Configurar clusters no HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    ![Configurar as definições de conta de armazenamento do HDInsight](./media/apache-storm-tutorial-get-started-linux/set-hdinsight-storage-account.png)

6. Na secção **Resumo**, reveja a configuração do cluster. Utilize as ligações __Editar__ para alterar quaisquer definições que estejam incorretas. Por fim, clique no botão __Criar__ para criar o cluster.

    ![Resumo da configuração do cluster](./media/apache-storm-tutorial-get-started-linux/hdinsight-configuration-summary.png)

    > [!NOTE]
    > A criação do cluster pode demorar até 20 minutos.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Executar um exemplo do storm-starter no HDInsight

1. Ligar ao cluster do HDInsight com o SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!TIP]  
    > O cliente SSH pode indicar que não é possível estabelecer a autenticidade do anfitrião. Se assim for, introduza `yes` para continuar.

    > [!NOTE]  
    > Se utilizou uma palavra-passe para proteger a sua conta de utilizador do SSH, é solicitado que a introduza. Se utilizou uma chave pública, poderá ter de utilizar o parâmetro `-i` para especificar a chave privada correspondente. Por exemplo, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize o seguinte comando para iniciar uma topologia de exemplo:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount

    Deste modo, este comando inicia a topologia do WordCount de exemplo no cluster. Esta topologia gera frases aleatórias e conta quantas vezes as palavras ocorrem. O nome amigável da topologia é `wordcount`.

    > [!NOTE]  
    > Ao submeter as topologias para o cluster, primeiro, tem de copiar o ficheiro jar que contém o cluster antes de utilizar o comando `storm`. Utilize o comando `scp` para copiar o ficheiro. Por exemplo, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > O exemplo do WordCount e outros exemplos do storm-starter já estão incluídos no seu cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

Se estiver interessado em ver a origem para os exemplos do storm-starter, pode encontrar o código em [https://github.com/apache/storm/tree/1.1.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.1.x-branch/examples/storm-starter). Esta ligação é para o Storm 1.1.x, que é fornecido com o HDInsight 3.6. Para outras versões do Storm, utilize o botão __Ramo__ na parte superior da página para selecionar uma versão diferente do Storm.

## <a name="monitor-the-topology"></a>Monitorizar a topologia

A IU do Storm fornece uma interface Web para trabalhar com topologias em execução e está incluída no cluster do HDInsight.

Siga estes passos para monitorizar a topologia através da IU do Storm:

1. Para apresentar a IU do Storm, abra um browser para `https://CLUSTERNAME.azurehdinsight.net/stormui`. Substitua **CLUSTERNAME** pelo nome do cluster.

    > [!NOTE]  
    > Se lhe for pedido que forneça um nome de utilizador e uma palavra-passe, introduza o administrador do cluster (admin) e a palavra-passe que utilizou ao criar o cluster.

2. Em **Resumo da topologia**, selecione a entrada **wordcount** na coluna **Nome**. São apresentadas mais informações sobre a topologia.

    ![Dashboard do Storm com informações de topologia do WordCount do storm-starter.](./media/apache-storm-tutorial-get-started-linux/topology-summary.png)

    Esta página fornece as seguintes informações:

   * **Estatísticas de topologia** – informações básicas sobre o desempenho da topologia, organizadas em intervalos de tempo.

       > [!NOTE]  
       > Selecionar um intervalo de tempo específico altera o intervalo de tempo das informações apresentadas noutras secções da página.

   * **Spouts** – informações básicas sobre spouts, incluindo o último erro devolvido por cada spout.

   * **Bolts** – informações básicas sobre bolts.

   * **Configuração da topologia** – informações detalhadas sobre a configuração da topologia.

     Esta página também fornece ações que podem ser efetuadas na topologia:

   * **Ativar** – retoma o processamento de uma topologia desativada.

   * **Desativar** – coloca em pausa uma topologia em execução.

   * **Rebalancear** – ajusta o paralelismo da topologia. Deve rebalancear as topologias em execução depois de ter alterado o número de nós no cluster. O rebalanceamento ajusta o paralelismo para compensar o número maior/menor de nós no cluster. Para obter mais informações, consulte [compreender o paralelismo de uma topologia do Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

   * **Eliminar** – termina uma topologia do Storm após o tempo limite especificado.

3. Nesta página, selecione uma entrada da secção **Spouts** ou **Bolts**. São apresentadas informações sobre o componente selecionado.

    ![Dashboard do Storm com informações sobre os componentes selecionados.](./media/apache-storm-tutorial-get-started-linux/component-summary.png)

    Esta página apresenta as seguintes informações:

    * **Estatísticas de Spout/Bolt** – Informações básicas sobre o desempenho dos componentes, organizadas em intervalos de tempo.

        > [!NOTE]  
        > Selecionar um intervalo de tempo específico altera o intervalo de tempo das informações apresentadas noutras secções da página.

    * **Estatísticas de entrada** (apenas bolt) – Informações sobre componentes que produzem os dados consumidos pelo bolt.

    * **Estatísticas de saída** – Informações sobre dados emitidos por este bolt.

    * **Executores** – Informações sobre as instâncias deste componente.

    * **Erros** – Erros produzidos por este componente.

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

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Caso se depare com um problema com a criação de um cluster do HDInsight, veja [aceder aos requisitos de controlo](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a id="next"></a>Passos seguintes

Neste tutorial do Apache Storm, aprendeu as noções básicas de trabalhar com o Storm no HDInsight. Em seguida, saiba como [topologias baseadas em Java desenvolver com o Apache Maven](apache-storm-develop-java-topology.md).

Se já estiver familiarizado com o desenvolvimento de topologias baseadas em Java, veja o documento [Deploy and manage Apache Storm topologies on HDInsight (Implementar e gerir topologias do Apache Storm no HDInsight)](apache-storm-deploy-monitor-topology-linux.md).

Se for um programador do .NET, pode criar topologias C# ou C#/Java híbridas com o Visual Studio. Para obter mais informações, consulte [desenvolver C# topologias do Apache Storm no HDInsight com ferramentas de Apache Hadoop para Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Para obter topologias de exemplo que podem ser utilizadas com Storm no HDInsight, veja os exemplos seguintes:

* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: https://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[preview-portal]: https://portal.azure.com/
