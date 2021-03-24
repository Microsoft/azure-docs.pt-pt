---
title: Eventos de processo de Centros de Eventos com Tempestade - Azure HDInsight
description: Aprenda a processar dados do Azure Event Hubs com uma topologia de tempestade C# criada em Visual Studio, utilizando as ferramentas HDInsight para Visual Studio.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ee483872535c58bde521cf0a751058fdc2fecd40
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871457"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Eventos de processo de Azure Event Hubs com Apache Storm on HDInsight (C#)

Aprenda a trabalhar com a Azure Event Hubs da [Apache Storm](https://storm.apache.org/) em HDInsight. Este documento usa uma topologia de tempestade C# para ler e escrever dados de Event Hubs

> [!NOTE]  
> Para uma versão Java deste projeto, consulte [eventos processuais de Azure Event Hubs com Apache Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

## <a name="scpnet"></a>SCP.NET

Os passos deste documento utilizam SCP.NET, um pacote NuGet que facilita a criação de topologias e componentes C# para utilização com Storm on HDInsight.

HDInsight 3.4 e maior utilização Mono para executar topologias C#. O exemplo utilizado neste documento funciona com HDInsight 3.6. Se planeia criar as suas próprias soluções .NET para HDInsight, verifique o documento [de compatibilidade Mono](https://www.mono-project.com/docs/about-mono/compatibility/) para obter potenciais incompatibilidades.

### <a name="cluster-versioning"></a>Versão de cluster

O pacote Microsoft.SCP.Net.SDK NuGet que utiliza para o seu projeto deve corresponder à versão principal da Storm instalada no HDInsight. As versões HDInsight 3.5 e 3.6 utilizam a Storm 1.x, pelo que deve utilizar SCP.NET versão 1.0.x.x com estes clusters.

As topologias C# também devem ter como alvo .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Como trabalhar com centros de eventos

A Microsoft fornece um conjunto de componentes Java que podem ser usados para comunicar com os Centros de Eventos a partir de uma topologia storm. Pode encontrar o ficheiro java (JAR) que contém uma versão compatível com HDInsight 3.6 destes componentes em [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) .

> [!IMPORTANT]  
> Enquanto os componentes estiverem escritos em Java, pode usá-los facilmente a partir de uma topologia C#.

Neste exemplo são utilizados os seguintes componentes:

* __EventHubSpout__: Lê dados dos Centros de Eventos.
* __EventHubBolt__: Escreve dados para os Centros de Eventos.
* __EventHubSpoutConfig__: Usado para configurar eventHubSpout.
* __EventHubBoltConfig__: Usado para configurar o EventHubBolt.

### <a name="example-spout-usage"></a>Uso de bico de exemplo

SCP.NET fornece métodos para adicionar um EventHubSpout à sua topologia. Estes métodos facilitam a adição de um bico do que a utilização dos métodos genéricos para a adição de um componente Java. O exemplo a seguir demonstra como criar um bico utilizando os métodos __SetEventHubSpout__ e **EventHubSpoutConfig** fornecidos por SCP.NET:

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

O exemplo anterior cria um novo componente de bico chamado __EventHubSpout__, e configura-o para comunicar com um centro de eventos. A indicação do paralelismo para o componente é definida para o número de divisórias no centro de eventos. Esta definição permite que a Tempestade crie uma instância do componente para cada divisória.

### <a name="example-bolt-usage"></a>Uso de parafusos de exemplo

Utilize o método **JavaComponmentConstructor** para criar uma instância do parafuso. O exemplo a seguir demonstra como criar e configurar uma nova instância do **EventHubBolt:**

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
> Este exemplo usa uma expressão Clojure passada como uma corda, em vez de usar **JavaComponentConstructor** para criar um **EventHubBoltConfig**, como o exemplo do bico fez. Qualquer um dos métodos funciona. Usa o método que te parece melhor.

## <a name="download-the-completed-project"></a>Descarregue o projeto concluído

Você pode baixar uma versão completa do projeto criado neste artigo a partir do [GitHub.](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) No entanto, ainda precisa de fornecer definições de configuração seguindo os passos deste artigo.

### <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado de tempestade Apache em HDInsight. Consulte [os clusters Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** for **Cluster type**.

* Um [centro de eventos Azure.](../../event-hubs/event-hubs-create.md)

* O [Azure .NET SDK](https://azure.microsoft.com/downloads/).

* As [ferramentas HDInsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 ou mais tarde no seu ambiente de desenvolvimento. Os downloads JDK estão disponíveis na [Oracle.](/azure/developer/java/fundamentals/java-jdk-long-term-support)

  * A variável **ambiente JAVA_HOME** deve apontar para o diretório que contém Java.
  * O **diretório %JAVA_HOME%/bin** deve estar no caminho.

## <a name="download-the-event-hubs-components"></a>Descarregue os componentes do Event Hubs

Descarregue o componente de bico e parafuso do Event Hubs a partir de [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) .

Crie um diretório chamado `eventhubspout` , e guarde o ficheiro para o diretório.

## <a name="configure-event-hubs"></a>Configure centros de eventos

O Event Hubs é a fonte de dados para este exemplo. Utilize as informações na secção "Criar um centro de [eventos" de Começar com Os Centros de Eventos.](../../event-hubs/event-hubs-create.md)

1. Depois de criado o centro de eventos, veja as definições do **EventHub** no portal Azure e selecione **políticas de acesso partilhado**. **Selecione + Adicionar** para criar as seguintes políticas:

   | Name | Permissões |
   | --- | --- |
   | escritor |Enviar |
   | leitor |Escutar |

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png" alt-text="Screenshot da janela de políticas de acesso share" border="true":::

2. Selecione as políticas do **leitor** e **do escritor.** Copie e guarde o valor principal para ambas as políticas, uma vez que estes valores são usados mais tarde.

## <a name="configure-the-eventhubwriter"></a>Configure o EventHubWriter

1. Se ainda não instalou a versão mais recente das ferramentas HDInsight para Visual Studio, consulte [Começar a usar ferramentas HDInsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Descarregue a solução a partir de [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. **Evento AbertoHubExample.sln**. No projeto **EventHubWriter,** abra o ficheiro **App.config.** Utilize as informações do centro de eventos que configuraste anteriormente para preencher o valor das seguintes teclas:

   | Chave | Valor |
   | --- | --- |
   | Nome eventHubPolicy |escritor (Se usou um nome diferente para a apólice com permissão *de envio,* use-o em vez disso.) |
   | EventHubPolicyKey |A chave para a política do escritor. |
   | Espaço EventHubName |O espaço de nome que contém o seu centro de eventos. |
   | Nome eventHub |O nome do seu centro de eventos. |
   | EventHubPartitionCount |O número de divisórias no seu centro de eventos. |

4. Guarde e feche o ficheiro **App.config.**

## <a name="configure-the-eventhubreader"></a>Configure o EventHubReader

1. Abra o projeto **EventHubReader.**

2. Abra o ficheiro **App.config** para o **EventHubReader**. Utilize as informações do centro de eventos que configuraste anteriormente para preencher o valor das seguintes teclas:

   | Chave | Valor |
   | --- | --- |
   | Nome eventHubPolicy |leitor (Se usou um nome diferente para a política com permissão de *escuta,* use-o em vez disso.) |
   | EventHubPolicyKey |A chave para a política dos leitores. |
   | Espaço EventHubName |O espaço de nome que contém o seu centro de eventos. |
   | Nome eventHub |O nome do seu centro de eventos. |
   | EventHubPartitionCount |O número de divisórias no seu centro de eventos. |

3. Guarde e feche o ficheiro **App.config.**

## <a name="deploy-the-topologies"></a>Implementar as topologias

1. A partir do **Solution Explorer,** clique à direita no projeto **EventHubReader** e selecione **Submeter à Tempestade em HDInsight**.

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png" alt-text="Screenshot do Solution Explorer, com Submissão à Tempestade em HDInsight em destaque" border="true":::

2. Na caixa de diálogo **De Topologia de Submissão,** selecione o seu **Cluster de Tempestades**. Expandir **Configurações Adicionais**, selecione **Java File Paths**, selecione... e selecione o diretório que contém o ficheiro JAR que descarregou anteriormente.  Finalmente, clique **em Enviar por isso.**

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png" alt-text="Screenshot da caixa de diálogo de Topologia de Submissão" border="true":::

3. Quando a topologia é submetida, o Espectador de **Topo de Estado** aparece. Para ver informações sobre a topologia, selecione a topologia **EventHubReader** no painel esquerdo.

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png" alt-text="Screenshot do espectador de Topologias da Tempestade" border="true":::

4. A partir do **Solution Explorer,** clique à direita no projeto **EventHubWriter** e selecione **Submeter à Tempestade em HDInsight**.

5. Na caixa de diálogo **De Topologia de Submissão,** selecione o seu **Cluster de Tempestades**. Expandir **Configurações Adicionais**, selecione **Java File Paths**, selecione... e selecione o diretório que contém o ficheiro JAR que descarregou anteriormente.  Finalmente, clique **em Enviar por isso.**

6. Quando a topologia tiver sido submetida, refresque a lista de topologias no Espectador de **Topologias para** verificar se ambas as topologias estão a funcionar no cluster.

7. No **Storm Toplogies Viewer**, selecione a topologia **EventHubReader.**

8. Para abrir o resumo do componente para o perno, clique duas vezes no componente **LogBolt** no diagrama.

9. Na secção **Executores,** selecione um dos links na coluna **Do Porto.** Isto mostra informações registadas pelo componente. As informações registadas são semelhantes ao seguinte texto:

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>Parar as topologias

Para parar as topologias, selecione cada topologia no **Storm Topology Viewer** e, em seguida, clique em **Kill**.

:::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png" alt-text="Screenshot do Storm Topology Viewer, com botão Kill em destaque" border="true":::

## <a name="delete-your-cluster"></a>Elimine o seu cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passos seguintes

Neste documento, você aprendeu a usar o bico e parafuso do Java Event Hubs de uma topologia C# para trabalhar com dados em Azure Event Hubs. Para saber mais sobre a criação de topologias C#, consulte o seguinte:

* [Desenvolver topologias C# para Apache Storm em HDInsight usando Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Guia de programação SCP](apache-storm-scp-programming-guide.md)
* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)