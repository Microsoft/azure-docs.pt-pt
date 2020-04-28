---
title: Eventos de processo saem de Centros de Eventos com Tempestade - Azure HDInsight
description: Saiba como processar dados do Azure Event Hubs com uma topologia C# Storm criada no Estúdio Visual, utilizando as ferramentas HDInsight para O Estúdio Visual.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 85b7093df99127b690c51e8f2f28d18e3f5f3c95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981628"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Processe eventos a partir de Hubs de Eventos Azure com Tempestade Apache em HDInsight (C#)

Saiba como trabalhar com os Hubs de Eventos Azure da [Tempestade Apache](https://storm.apache.org/) no HDInsight. Este documento usa uma topologia de tempestade C# para ler e escrever dados de Centros de Eventos

> [!NOTE]  
> Para uma versão Java deste projeto, consulte [eventos processuais a partir de Hubs de Eventos Azure com Tempestade Apache no HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

## <a name="scpnet"></a>SCP.NET

Os passos neste documento usam SCP.NET, um pacote NuGet que facilita a criação de topoologias e componentes C# para utilização com storm on HDInsight.

HDInsight 3.4 e maior utilização Mono para executar topoologias C#. O exemplo utilizado neste documento funciona com o HDInsight 3.6. Se planeia criar as suas próprias soluções .NET para o HDInsight, verifique o documento de [compatibilidade Mono](https://www.mono-project.com/docs/about-mono/compatibility/) para obter potenciais incompatibilidades.

### <a name="cluster-versioning"></a>Versão cluster

O pacote Microsoft.SCP.Net.SDK NuGet que utiliza para o seu projeto deve corresponder à versão principal da Tempestade instalada no HDInsight. As versões HDInsight 3.5 e 3.6 usam storm 1.x, por isso deve usar SCP.NET versão 1.0.x.x com estes clusters.

As topoologias C# também devem visar .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Como trabalhar com centros de eventos

A Microsoft fornece um conjunto de componentes Java que podem ser usados para comunicar com Os Hubs de Eventos a partir de uma topologia storm. Pode encontrar o ficheiro Java archive (JAR) que contém uma versão compatível com [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar)o HDInsight 3.6 destes componentes em .

> [!IMPORTANT]  
> Enquanto os componentes estão escritos em Java, você pode facilmente usá-los a partir de uma topologia C#.

Neste exemplo, são utilizados os seguintes componentes:

* __EventHubSpout__: Lê dados dos Centros de Eventos.
* __EventHubBolt__: Escreve dados para Centros de Eventos.
* __EventHubSpoutConfig__: Usado para configurar eventHubSpout.
* __EventHubBoltConfig__: Usado para configurar EventHubBolt.

### <a name="example-spout-usage"></a>Uso de bico de exemplo

SCP.NET fornece métodos para adicionar um EventHubSpout à sua topologia. Estes métodos facilitam a adição de um bico do que a utilização dos métodos genéricos para adicionar um componente Java. O exemplo seguinte demonstra como criar um bico utilizando os métodos __SetEventHubSpout__ e **EventHubSpoutConfig** fornecidos por SCP.NET:

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

O exemplo anterior cria um novo componente de bico chamado __EventHubSpout__, e confunde-o para comunicar com um hub de eventos. A dica de paralelismo para o componente está definida para o número de divisórias no centro do evento. Esta definição permite que a Tempestade crie uma instância do componente para cada partição.

### <a name="example-bolt-usage"></a>Exemplo de utilização de parafusos

Utilize o método **JavaComponmentConstructor** para criar uma instância do parafuso. O exemplo que se segue demonstra como criar e configurar uma nova instância do **EventHubBolt:**

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
> Este exemplo usa uma expressão Clojure passada como uma corda, em vez de usar **javaComponentConstructor** para criar um **EventHubBoltConfig**, como o exemplo do bico fez. Qualquer um dos métodos funciona. Usa o método que te faz sentir melhor.

## <a name="download-the-completed-project"></a>Descarregue o projeto concluído

Você pode baixar uma versão completa do projeto criado neste artigo a partir do [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). No entanto, ainda precisa de fornecer configurações de configuração seguindo os passos deste artigo.

### <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado de tempestade Apache no HDInsight. Consulte os [clusters De Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** for **Cluster type**.

* Um centro de [eventos Azure.](../../event-hubs/event-hubs-create.md)

* O [Azure.NET SDK](https://azure.microsoft.com/downloads/).

* As [ferramentas HDInsight para Estúdio Visual](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 ou mais tarde no seu ambiente de desenvolvimento. Os downloads de JDK estão disponíveis na [Oracle](https://aka.ms/azure-jdks).

  * A variável **ambiente JAVA_HOME** deve apontar para o diretório que contém Java.
  * O diretório **%JAVA_HOME%/bin** deve estar no caminho.

## <a name="download-the-event-hubs-components"></a>Descarregue os componentes do Event Hubs

Descarregue o componente de spout e parafuso spout do Event Hubs a partir de [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Crie um `eventhubspout`diretório chamado e guarde o ficheiro no diretório.

## <a name="configure-event-hubs"></a>Configure Centros de Eventos

O Event Hubs é a fonte de dados deste exemplo. Utilize a informação na secção "Criar um hub de eventos" do [Get started with Event Hubs](../../event-hubs/event-hubs-create.md).

1. Depois de criado o hub do evento, veja as definições do **EventHub** no portal Azure e selecione políticas de **acesso partilhado.** Selecione **+ Adicionar** para criar as seguintes políticas:

   | Nome | Permissões |
   | --- | --- |
   | escritor |Enviar |
   | leitor |Escutar |

    ![Screenshot da janela de políticas de acesso ao share](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. Selecione as políticas do **leitor** e **do escritor.** Copiar e guardar o valor-chave principal para ambas as políticas, uma vez que estes valores são usados posteriormente.

## <a name="configure-the-eventhubwriter"></a>Configure o EventHubWriter

1. Se ainda não instalou a versão mais recente das ferramentas HDInsight para Visual Studio, consulte [o Get a utilizar ferramentas HDInsight para O Estúdio Visual](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Baixe a solução a partir de [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Open **EventHubExample.sln**. No projeto **EventHubWriter,** abra o ficheiro **App.config.** Utilize as informações do centro de eventos que configurau anteriormente para preencher o valor para as seguintes teclas:

   | Chave | Valor |
   | --- | --- |
   | Nome de Política de EventHub |escritor (Se usou um nome diferente para a política com permissão *enviar,* use-o em vez disso.) |
   | EventHubPolicyKey |A chave para a política do escritor. |
   | Espaço EventHubName |O espaço de nome que contém o seu centro de eventos. |
   | Nome EventHub |O seu nome de centro de eventos. |
   | Contócção de Partição EventHub |O número de divisórias no seu centro de eventos. |

4. Guarde e feche o ficheiro **App.config.**

## <a name="configure-the-eventhubreader"></a>Configure o EventHubReader

1. Abra o projeto **EventHubReader.**

2. Abra o ficheiro **App.config** para o **EventHubReader**. Utilize as informações do centro de eventos que configurau anteriormente para preencher o valor para as seguintes teclas:

   | Chave | Valor |
   | --- | --- |
   | Nome de Política de EventHub |leitor (Se usou um nome diferente para a política com permissão de *escuta,* use-o em vez disso.) |
   | EventHubPolicyKey |A chave para a política dos leitores. |
   | Espaço EventHubName |O espaço de nome que contém o seu centro de eventos. |
   | Nome EventHub |O seu nome de centro de eventos. |
   | Contócção de Partição EventHub |O número de divisórias no seu centro de eventos. |

3. Guarde e feche o ficheiro **App.config.**

## <a name="deploy-the-topologies"></a>Implementar as topoologias

1. Do **Solution Explorer,** clique à direita no projeto **EventHubReader** e selecione **Submeter a Tempestade no HDInsight**.

    ![Screenshot do Solution Explorer, com Submeter à Tempestade no HDInsight em destaque](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. Na caixa de diálogo **de Topologia Submeter,** selecione o seu **Aglomerado de Tempestades**. Expandir **Configurações Adicionais,** selecione **Java File Paths,** selecione **...**, e selecione o diretório que contém o ficheiro JAR que descarregou anteriormente. Finalmente, clique em **Submeter**.

    ![Screenshot da caixa de diálogo de topologia de submissão](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. Quando a topologia foi submetida, o Espectador de **Topoologia** da Tempestade aparece. Para ver informações sobre a topologia, selecione a topologia **EventHubReader** no painel esquerdo.

    ![Screenshot do Espectador de Topoologias da Tempestade](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. A partir do **Solution Explorer,** clique à direita no projeto **EventHubWriter** e selecione **Submeter a Tempestade no HDInsight**.

5. Na caixa de diálogo **de Topologia Submeter,** selecione o seu **Aglomerado de Tempestades**. Expandir **Configurações Adicionais,** selecione **Java File Paths,** selecione **...**, e selecione o diretório que contém o ficheiro JAR que descarregou anteriormente. Finalmente, clique em **Submeter**.

6. Quando a topologia tiver sido submetida, refresque a lista de topologia no **Storm Topologies Viewer** para verificar se ambas as topoologias estão em execução no cluster.

7. No **Storm Topologies Viewer,** selecione a topologia **EventHubReader.**

8. Para abrir o resumo do componente para o parafuso, clique duas vezes no componente **LogBolt** no diagrama.

9. Na secção **Executores,** selecione um dos links da coluna **da Porta.** Isto exibe informações registadas pelo componente. As informações registadas são semelhantes ao seguinte texto:

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>Parar as topologias

Para parar as topoologias, selecione cada topologia no **Storm Topology Viewer,** em seguida, clique em **Kill**.

![Screenshot do Storm Topology Viewer, com botão Kill em destaque](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>Elimine o seu cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar o bico e o parafuso do Java Event Hubs a partir de uma topologia C# para trabalhar com dados em Azure Event Hubs. Para saber mais sobre a criação de topoologias C#, consulte o seguinte:

* [Desenvolva topoologias C# para Apache Storm no HDInsight usando o Estúdio Visual](apache-storm-develop-csharp-visual-studio-topology.md)
* [Guia de programação sCP](apache-storm-scp-programming-guide.md)
* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)
