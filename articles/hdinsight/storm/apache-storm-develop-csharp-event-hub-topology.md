---
title: Processar eventos dos Hubs de eventos com o Storm - Azure HDInsight
description: Saiba como processar os dados dos Hubs de eventos do Azure com uma topologia de Storm em c#, criada no Visual Studio, utilizando as ferramentas do HDInsight para Visual Studio.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: b02945197b20c7fe704d0f8cfa9201a5b9cbc292
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125209"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Processar eventos dos Hubs de eventos do Azure com o Apache Storm no HDInsight (C#)

Saiba como trabalhar com os Hubs de eventos do Azure partir [Apache Storm](https://storm.apache.org/) no HDInsight. Este documento usa uma topologia de Storm em c# para ler e escrever dados a partir dos Hubs de eventos

> [!NOTE]  
> Para obter uma versão de Java deste projeto, consulte [processar eventos dos Hubs de eventos do Azure com o Apache Storm no HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

## <a name="scpnet"></a>SCP.NET

Os passos neste documento utilizam SCP.NET, um pacote NuGet que torna mais fácil criar topologias c# e componentes para utilização com o Storm no HDInsight.

> [!IMPORTANT]  
> Embora os passos neste documento dependem de um ambiente de desenvolvimento do Windows com o Visual Studio, o projeto compilado pode ser submetido para um Storm num cluster do HDInsight que utiliza o Linux. Apenas os clusters baseados em Linux criados após a 28 de Outubro de 2016, suportam SCP.NET topologias.

HDInsight 3.4 e maior utilização Mono para executar topologias c#. No exemplo utilizado neste documento funciona com o HDInsight 3.6. Se pretender criar suas próprias soluções .NET para o HDInsight, consulte a [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) documento quanto a potenciais incompatibilidades.

### <a name="cluster-versioning"></a>Controlo de versões de cluster

O pacote NuGet da Microsoft que utiliza para o seu projeto tem de corresponder a versão principal do Storm instalada no HDInsight. As versões do HDInsight 3.5 e 3.6 utilizam o Storm 1.x, deve usar SCP.NET versão 1.0.x.x com esses clusters.

> [!IMPORTANT]  
> O exemplo neste documento espera um 3.5 HDInsight ou 3.6 cluster.
>
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

Topologias c# também devem visar o .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Como trabalhar com os Hubs de eventos

A Microsoft fornece um conjunto de componentes de Java que pode ser utilizado para comunicar com os Hubs de eventos de uma topologia Storm. Pode encontrar o ficheiro de arquivo (JAR) de Java que contém uma versão compatível do HDInsight 3.6 desses componentes em [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]  
> Enquanto os componentes são escritos em Java, pode usá-los facilmente de uma topologia em C#.

Os seguintes componentes são utilizados neste exemplo:

* __EventHubSpout__: Lê os dados dos Hubs de eventos.
* __EventHubBolt__: Escreve dados para os Hubs de eventos.
* __EventHubSpoutConfig__: Utilizado para configurar EventHubSpout.
* __EventHubBoltConfig__: Utilizado para configurar EventHubBolt.

### <a name="example-spout-usage"></a>Exemplo de utilização de spout

SCP.NET fornece métodos para adicionar um EventHubSpout a sua topologia. Esses métodos mais fácil adicionar um spout superior à utilização de métodos genéricos para adicionar um componente de Java. O exemplo seguinte demonstra como criar um spout utilizando o __SetEventHubSpout__ e **EventHubSpoutConfig** métodos fornecidos pela SCP.NET:

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

O exemplo anterior cria um novo componente de spout com o nome __EventHubSpout__e configura-o para comunicar com um hub de eventos. A sugestão de paralelismo para o componente está definida para o número de partições de evento hub. Esta definição permite o Storm criar uma instância do componente para cada partição.

### <a name="example-bolt-usage"></a>Exemplo de utilização de bolt

Utilize o **JavaComponmentConstructor** método para criar uma instância do bolt. O exemplo seguinte demonstra como criar e configurar uma nova instância do **EventHubBolt**:

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
> Este exemplo utiliza uma expressão de Clojure transmitida como uma cadeia de caracteres, em vez de usar **JavaComponentConstructor** para criar um **EventHubBoltConfig**, como o exemplo de spout fez. Qualquer um dos métodos funciona. Utilize o método que funciona melhor para si.

## <a name="download-the-completed-project"></a>Transfira o projeto concluído

Pode baixar uma versão completa do projeto que criou neste tutorial de [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). No entanto, ainda precisa fornecer definições de configuração ao seguir os passos neste tutorial.

### <a name="prerequisites"></a>Pré-requisitos

* Uma [Apache Storm no HDInsight clusters versão 3.5 ou 3.6](apache-storm-tutorial-get-started-linux.md).

    > [!WARNING]  
    > No exemplo utilizado neste documento requer o Storm no HDInsight versão 3.5 ou 3.6. Isso não funciona com versões anteriores do HDInsight, devido a alterações de nome de classe recentes. Para obter uma versão deste exemplo que funciona com clusters mais antigos, consulte [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* Uma [hub de eventos do Azure](../../event-hubs/event-hubs-create.md).

* O [SDK .NET do Azure](https://azure.microsoft.com/downloads/).

* O [ferramentas do HDInsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 ou posterior no seu ambiente de desenvolvimento. Downloads do JDK estão disponíveis a partir [Oracle](https://aka.ms/azure-jdks).

  * O **JAVA_HOME** variável de ambiente tem de apontar para o diretório que contém o Java.
  * O **%JAVA_HOME%/bin** diretório tem de estar no caminho.

## <a name="download-the-event-hubs-components"></a>Transferir os componentes de Hubs de eventos

Transferir o spout de Hubs de eventos e bolt componente a partir [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Criar um diretório chamado `eventhubspout`e guarde o ficheiro no diretório.

## <a name="configure-event-hubs"></a>Configurar os Hubs de eventos

Os Hubs de eventos é a origem de dados para este exemplo. Utilize as informações na secção "Criar um hub de eventos" [introdução aos Hubs de eventos](../../event-hubs/event-hubs-create.md).

1. Depois de criar o hub de eventos, ver o **EventHub** definições no portal do Azure e selecione **políticas de acesso partilhado**. Selecione **+ adicionar** para adicionar as seguintes políticas:

   | Name | Permissões |
   | --- | --- |
   | escritor |Enviar |
   | leitor |Vigiar |

    ![Janela de políticas de acesso de captura de ecrã de partilha](./media/apache-storm-develop-csharp-event-hub-topology/sas.png)

2. Selecione o **leitor** e **escritor** políticas. Copie e guarde o valor da chave primário para ambas as políticas, como estes valores são utilizados mais tarde.

## <a name="configure-the-eventhubwriter"></a>Configurar o EventHubWriter

1. Se ainda não tiver instalado a versão mais recente das ferramentas do HDInsight para Visual Studio, consulte [começar a utilizar as ferramentas do HDInsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Transferir a solução a partir [eventhub-storm-híbrida](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Na **EventHubWriter** projeto, abra o **App. config** ficheiro. Utilize as informações do hub de eventos que configurou anteriormente para preencher o valor para as seguintes chaves:

   | Chave | Value |
   | --- | --- |
   | EventHubPolicyName |gravador (se tiver utilizado um nome diferente para a política com *enviar* permissão, utilizá-lo em vez disso.) |
   | EventHubPolicyKey |A chave para a política de escritor. |
   | EventHubNamespace |O espaço de nomes que contém o seu hub de eventos. |
   | EventHubName |O nome do hub de eventos. |
   | EventHubPartitionCount |O número de partições num hub de eventos. |

4. Guarde e feche o **App. config** ficheiro.

## <a name="configure-the-eventhubreader"></a>Configurar o EventHubReader

1. Abra o **EventHubReader** projeto.

2. Abra o **App. config** de ficheiros para o **EventHubReader**. Utilize as informações do hub de eventos que configurou anteriormente para preencher o valor para as seguintes chaves:

   | Chave | Value |
   | --- | --- |
   | EventHubPolicyName |leitor (se tiver utilizado um nome diferente para a política com *escutar* permissão, utilizá-lo em vez disso.) |
   | EventHubPolicyKey |A chave para a política de leitor. |
   | EventHubNamespace |O espaço de nomes que contém o seu hub de eventos. |
   | EventHubName |O nome do hub de eventos. |
   | EventHubPartitionCount |O número de partições num hub de eventos. |

3. Guarde e feche o **App. config** ficheiro.

## <a name="deploy-the-topologies"></a>Implementar as topologias

1. Partir **Explorador de soluções**, com o botão direito a **EventHubReader** do projeto e selecione **Submit para Storm no HDInsight**.

    ![Captura de ecrã do Explorador de soluções, com o envio para o Storm no HDInsight realçado](./media/apache-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Sobre o **submeter topologia** caixa de diálogo, selecione seu **Cluster do Storm**. Expanda **configurações adicionais**, selecione **caminhos de ficheiro de Java**, selecione **...** e selecione o diretório que contém o ficheiro JAR que transferiu anteriormente. Por fim, clique em **submeter**.

    ![Caixa de diálogo de captura de ecrã de submeter a topologia](./media/apache-storm-develop-csharp-event-hub-topology/submit.png)

3. Quando for submetida a topologia, o **Visualizador de topologias do Storm** aparece. Para ver informações sobre a topologia, selecione o **EventHubReader** topologia no painel esquerdo.

    ![Captura de ecrã do Visualizador de topologias do Storm](./media/apache-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Partir **Explorador de soluções**, com o botão direito a **EventHubWriter** do projeto e selecione **Submit para Storm no HDInsight**.

5. Sobre o **submeter topologia** caixa de diálogo, selecione seu **Cluster do Storm**. Expanda **configurações adicionais**, selecione **caminhos de ficheiro de Java**, selecione **...** e selecione o diretório que contém o ficheiro JAR que transferiu anteriormente. Por fim, clique em **submeter**.

6. Quando a topologia foi submetida, atualize a lista de topologia no **Visualizador de topologias do Storm** para verificar se ambas as topologias estão em execução no cluster.

7. Na **Visualizador de topologias do Storm**, selecione a **EventHubReader** topologia.

8. Para abrir o componente de resumo para o bolt, faça duplo clique o **LogBolt** componente no diagrama.

9. Na **executores** secção, selecione um dos links na **porta** coluna. Esta ação apresenta informações registadas pelo componente. As informações com sessão iniciada são semelhantes ao seguinte texto:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Parar as topologias

Para parar as topologias, selecione cada topologia no **Visualizador de topologia do Storm**, em seguida, clique em **Kill**.

![Captura de ecrã do Storm topologia Visualizador, com o botão de eliminação realçado](./media/apache-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Elimina o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste documento, aprendeu como utilizar o spout de Java dos Hubs de eventos e bolt de uma topologia c# para trabalhar com dados nos Hubs de eventos do Azure. Para saber mais sobre como criar topologias c#, consulte o seguinte:

* [Desenvolver topologias c# para Apache Storm no HDInsight com o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Guia de programação do SCP](apache-storm-scp-programming-guide.md)
* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)
