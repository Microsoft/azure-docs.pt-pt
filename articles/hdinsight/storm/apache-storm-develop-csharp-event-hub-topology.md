---
title: Processar eventos dos hubs de eventos com o Storm – Azure HDInsight
description: Saiba como processar dados de hubs de eventos do Azure com C# uma topologia Storm criada no Visual Studio, usando as ferramentas do HDInsight para Visual Studio.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 62d65a4f004494ac4ce4ecd3df0f091460028d8f
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800067"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Processar eventos dos hubs de eventos do Azure com o Apache StormC#no HDInsight ()

Saiba como trabalhar com hubs de eventos do Azure de [Apache Storm](https://storm.apache.org/) no HDInsight. Este documento usa uma C# topologia Storm para ler e gravar dados de hubs de eventos

> [!NOTE]  
> Para obter uma versão Java deste projeto, consulte [processar eventos dos hubs de eventos do Azure com Apache Storm no HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

## <a name="scpnet"></a>SCP.NET

As etapas neste documento usam o SCP.NET, um pacote NuGet que facilita a criação C# de topologias e componentes para uso com o Storm no HDInsight.

> [!IMPORTANT]  
> Embora as etapas neste documento dependam de um ambiente de desenvolvimento do Windows com o Visual Studio, o projeto compilado pode ser enviado para um Storm no cluster HDInsight que usa o Linux. Somente os clusters baseados em Linux criados após 28 de outubro de 2016 oferecem suporte a topologias SCP.NET.

HDInsight 3,4 e maior usam mono para executar C# topologias. O exemplo usado neste documento funciona com o HDInsight 3,6. Se você planeja criar suas próprias soluções .NET para HDInsight, verifique o documento de [compatibilidade mono](https://www.mono-project.com/docs/about-mono/compatibility/) para obter possíveis incompatibilidades.

### <a name="cluster-versioning"></a>Controle de versão de cluster

O pacote NuGet Microsoft. SCP. net. SDK que você usa para seu projeto deve corresponder à versão principal do Storm instalada no HDInsight. As versões 3,5 e 3,6 do HDInsight usam o Storm 1. x, portanto, você deve usar o SCP.NET versão 1.0. x. x com esses clusters.

> [!IMPORTANT]  
> O exemplo neste documento espera um cluster HDInsight 3,5 ou 3,6.
>
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. 

C#as topologias também devem ter como destino o .NET 4,5.

## <a name="how-to-work-with-event-hubs"></a>Como trabalhar com hubs de eventos

A Microsoft fornece um conjunto de componentes Java que podem ser usados para se comunicar com os hubs de eventos de uma topologia Storm. Você pode encontrar o arquivo Java Archive (JAR) que contém uma versão compatível do HDInsight 3,6 desses componentes em [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]  
> Embora os componentes sejam escritos em Java, você pode usá-los facilmente de C# uma topologia.

Os seguintes componentes são usados neste exemplo:

* __EventHubSpout__: lê dados dos hubs de eventos.
* __EventHubBolt__: grava dados em hubs de eventos.
* __EventHubSpoutConfig__: usado para configurar o EventHubSpout.
* __EventHubBoltConfig__: usado para configurar o EventHubBolt.

### <a name="example-spout-usage"></a>Exemplo de uso de Spout

SCP.NET fornece métodos para adicionar um EventHubSpout à sua topologia. Esses métodos facilitam a adição de um Spout do que usar os métodos genéricos para adicionar um componente Java. O exemplo a seguir demonstra como criar um Spout usando os métodos __SetEventHubSpout__ e **EventHubSpoutConfig** fornecidos por SCP.net:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

O exemplo anterior cria um novo componente Spout chamado __EventHubSpout__e o configura para se comunicar com um hub de eventos. A dica de paralelismo para o componente é definida como o número de partições no Hub de eventos. Essa configuração permite que o Storm crie uma instância do componente para cada partição.

### <a name="example-bolt-usage"></a>Exemplo de uso de raio

Use o método **JavaComponmentConstructor** para criar uma instância do parafuso. O exemplo a seguir demonstra como criar e configurar uma nova instância do **EventHubBolt**:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> Este exemplo usa uma expressão Clojure passada como uma cadeia de caracteres, em vez de usar **JavaComponentConstructor** para criar um **EventHubBoltConfig**, como o exemplo de Spout. O método funciona. Use o método que mais lhe sentirá.

## <a name="download-the-completed-project"></a>Baixar o projeto concluído

Você pode baixar uma versão completa do projeto criado neste artigo do [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). No entanto, você ainda precisa fornecer definições de configuração seguindo as etapas neste artigo.

### <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Storm no HDInsight. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** para o **tipo de cluster**.

    > [!WARNING]  
    > O exemplo usado neste documento requer o Storm no HDInsight versão 3,5 ou 3,6. Isso não funciona com versões anteriores do HDInsight, devido à interrupção das alterações no nome da classe. Para obter uma versão deste exemplo que funciona com clusters mais antigos, consulte [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* Um [Hub de eventos do Azure](../../event-hubs/event-hubs-create.md).

* O [SDK do .net do Azure](https://azure.microsoft.com/downloads/).

* As [Ferramentas do HDInsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1,8 ou posterior em seu ambiente de desenvolvimento. Os downloads do JDK estão disponíveis no [Oracle](https://aka.ms/azure-jdks).

  * A variável de ambiente **JAVA_HOME** deve apontar para o diretório que contém Java.
  * O diretório **% JAVA_HOME%/bin** deve estar no caminho.

## <a name="download-the-event-hubs-components"></a>Baixar os componentes dos hubs de eventos

Baixe o componente Spout dos hubs de eventos e componentes de raio de [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Crie um diretório chamado `eventhubspout`e salve o arquivo no diretório.

## <a name="configure-event-hubs"></a>Configurar hubs de eventos

Hubs de eventos é a fonte de dados para este exemplo. Use as informações na seção "criar um hub de eventos" de introdução [aos hubs de eventos](../../event-hubs/event-hubs-create.md).

1. Depois que o Hub de eventos tiver sido criado, exiba as configurações do **EventHub** no portal do Azure e selecione **políticas de acesso compartilhado**. Selecione **+ Adicionar** para adicionar as seguintes políticas:

   | Nome | Permissões |
   | --- | --- |
   | escritor |Enviar |
   | leitora |Escutar |

    ![Captura de tela da janela de políticas de acesso de compartilhamento](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. Selecione as políticas de **leitor** e **gravador** . Copie e salve o valor de chave primária para ambas as políticas, pois esses valores serão usados posteriormente.

## <a name="configure-the-eventhubwriter"></a>Configurar o EventHubWriter

1. Se você ainda não instalou a versão mais recente das ferramentas do HDInsight para Visual Studio, consulte Introdução ao [uso das ferramentas do hdinsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Baixe a solução do [eventhub-Storm-híbrido](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. No projeto **EventHubWriter** , abra o arquivo **app. config** . Use as informações do hub de eventos que você configurou anteriormente para preencher o valor das seguintes chaves:

   | Chave | Valor |
   | --- | --- |
   | EventHubPolicyName |gravador (se você usou um nome diferente para a política com permissão de *envio* , use-o em vez disso). |
   | EventHubPolicyKey |A chave para a política do gravador. |
   | EventHubNamespace |O namespace que contém o Hub de eventos. |
   | EventHubName |O nome do hub de eventos. |
   | EventHubPartitionCount |O número de partições em seu hub de eventos. |

4. Salve e feche o arquivo **app. config** .

## <a name="configure-the-eventhubreader"></a>Configurar o EventHubReader

1. Abra o projeto **EventHubReader** .

2. Abra o arquivo **app. config** para o **EventHubReader**. Use as informações do hub de eventos que você configurou anteriormente para preencher o valor das seguintes chaves:

   | Chave | Valor |
   | --- | --- |
   | EventHubPolicyName |leitor (se você usou um nome diferente para a política com a permissão *escutar* , use-o em vez disso). |
   | EventHubPolicyKey |A chave para a política de leitor. |
   | EventHubNamespace |O namespace que contém o Hub de eventos. |
   | EventHubName |O nome do hub de eventos. |
   | EventHubPartitionCount |O número de partições em seu hub de eventos. |

3. Salve e feche o arquivo **app. config** .

## <a name="deploy-the-topologies"></a>Implantar as topologias

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **EventHubReader** e selecione **Enviar para o Storm no HDInsight**.

    ![Captura de tela de Gerenciador de Soluções, com enviar ao Storm no HDInsight realçado](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. Na caixa de diálogo **Enviar topologia** , selecione o **cluster Storm**. Expanda **configurações adicionais**, selecione **caminhos de arquivo Java**, selecione **...** e selecione o diretório que contém o arquivo JAR que você baixou anteriormente. Por fim, clique em **Enviar**.

    ![Captura de tela da caixa de diálogo Enviar topologia](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. Quando a topologia tiver sido enviada, o **Visualizador de topologias Storm** será exibido. Para exibir informações sobre a topologia, selecione a topologia **EventHubReader** no painel esquerdo.

    ![Captura de tela do Visualizador de topologias Storm](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **EventHubWriter** e selecione **Enviar para o Storm no HDInsight**.

5. Na caixa de diálogo **Enviar topologia** , selecione o **cluster Storm**. Expanda **configurações adicionais**, selecione **caminhos de arquivo Java**, selecione **...** e selecione o diretório que contém o arquivo JAR que você baixou anteriormente. Por fim, clique em **Enviar**.

6. Quando a topologia tiver sido enviada, atualize a lista de topologia no **Visualizador de topologias do Storm** para verificar se ambas as topologias estão em execução no cluster.

7. No **Visualizador de topologias Storm**, selecione a topologia **EventHubReader** .

8. Para abrir o resumo do componente para o parafuso, clique duas vezes no componente **LogBolt** no diagrama.

9. Na seção **executores** , selecione um dos links na coluna **porta** . Isso exibe informações registradas pelo componente. As informações registradas em log são semelhantes ao seguinte texto:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Parar as topologias

Para interromper as topologias, selecione cada topologia no **Visualizador de topologia do Storm**e clique em **eliminar**.

![Captura de tela do Storm Topology Viewer, com o botão Kill realçado](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passos seguintes

Neste documento, você aprendeu a usar o Spout dos hubs de eventos Java e o rolo de uma C# topologia para trabalhar com os dados nos hubs de eventos do Azure. Para saber mais sobre a C# criação de topologias, consulte o seguinte:

* [Desenvolver C# topologias para Apache Storm no HDInsight usando o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Guia de programação do SCP](apache-storm-scp-programming-guide.md)
* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)
