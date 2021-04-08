---
title: Capture streaming eventos - Azure Event Hubs | Microsoft Docs
description: Este artigo fornece uma visão geral da funcionalidade Captura que lhe permite capturar eventos streaming através de Azure Event Hubs.
ms.topic: article
ms.date: 02/16/2021
ms.openlocfilehash: 9f0ec1223c06b908a9aa9f3ac5c5b19ead2fe962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595965"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Capture eventos através de Azure Event Hubs em Azure Blob Storage ou Azure Data Lake Storage
O Azure Event Hubs permite-lhe capturar automaticamente os dados de streaming em Event Hubs num [armazenamento Azure Blob](https://azure.microsoft.com/services/storage/blobs/) ou na conta [Azure Data Lake Storage Gen 1 ou Gen 2](https://azure.microsoft.com/services/data-lake-store/) da sua escolha, com a flexibilidade adicional de especificar um intervalo de tempo ou tamanho. A configuração do Capture é rápida, não há custos administrativos para executá-lo, e escala automaticamente com [unidades de produção](event-hubs-scalability.md#throughput-units)de Event Hubs . O Event Hubs Capture é a forma mais fácil de carregar dados de streaming no Azure e permite-lhe focar no processamento de dados e não na captura de dados.

> [!NOTE]
> Configurar a captura de centros de eventos para usar o Azure Data Lake Storage **Gen 2** é o mesmo que configurar para usar um armazenamento Azure Blob. Para mais detalhes, consulte [a Captura de Hubs de Eventos Configurar](event-hubs-capture-enable-through-portal.md). 

O Event Hubs Capture permite-lhe processar gasodutos em tempo real e baseados em lotes no mesmo fluxo. Isto significa que pode construir soluções que cresçam com as suas necessidades ao longo do tempo. Quer esteja a construir sistemas baseados em lotes hoje com um olho para o processamento futuro em tempo real, ou se pretende adicionar um caminho frio eficiente a uma solução existente em tempo real, o Event Hubs Capture facilita o trabalho com dados de streaming.

> [!IMPORTANT]
> A conta de armazenamento de destino (Azure Storage ou Azure Data Lake Storage) deve estar na mesma subscrição que o centro de eventos. 

## <a name="how-event-hubs-capture-works"></a>Como funciona a captura de centros de eventos

Event Hubs é um tampão durável de retenção de tempo para entrada de telemetria, semelhante a um log distribuído. A chave para a escala em Event Hubs é o [modelo de consumidor dividido.](event-hubs-scalability.md#partitions) Cada divisória é um segmento independente de dados e é consumida de forma independente. Com o tempo, estes dados envelhecem, com base no período de retenção configurável. Como resultado, um dado centro de eventos nunca fica "muito cheio".

O Event Hubs Capture permite-lhe especificar a sua própria conta de armazenamento e contentor Azure Blob, ou conta de armazenamento do Lago de Dados Azure, que são utilizados para armazenar os dados capturados. Estas contas podem estar na mesma região que o seu centro de eventos ou noutra região, aumentando a flexibilidade da funcionalidade de Captura de Centros de Eventos.

Os dados capturados estão escritos no formato [Apache Avro:][Apache Avro] um formato compacto, rápido e binário que fornece estruturas de dados ricas com esquema inline. Este formato é amplamente utilizado no ecossistema Hadoop, Stream Analytics e Azure Data Factory. Mais informações sobre trabalhar com a Avro estão disponíveis mais tarde neste artigo.

### <a name="capture-windowing"></a>Captação de janelas

O Event Hubs Capture permite-lhe configurar uma janela para controlar a captura. Esta janela é uma configuração de tamanho e tempo mínimo com uma "política de primeira vitória", o que significa que o primeiro gatilho encontrado causa uma operação de captura. Se tiver uma janela de captura de 15 minutos e 100 MB e enviar 1 MB por segundo, a janela de tamanho dispara antes da janela do tempo. Cada divisória captura independentemente e escreve uma bolha de bloco completa no momento da captura, nomeada para o momento em que o intervalo de captura foi encontrado. A convenção de nomeação de armazenamento é a seguinte:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Os valores da data são acolchoados com zeros; um nome de arquivo exemplo pode ser:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Caso a sua bolha de armazenamento Azure esteja temporariamente indisponível, o Event Hubs Capture conservará os seus dados para o período de retenção de dados configurado no seu centro de eventos e preencherá os dados assim que a sua conta de armazenamento estiver novamente disponível.

### <a name="scaling-to-throughput-units"></a>Dimensionamento para unidades de produção

O tráfego do Event Hubs é controlado por [unidades de produção.](event-hubs-scalability.md#throughput-units) Uma única unidade de produção permite 1 MB por segundo ou 1000 eventos por segundo de entrada e o dobro dessa quantidade de saída. Os Standard Event Hubs podem ser configurados com 1-20 unidades de produção, e pode comprar mais com um pedido de [apoio][support request]ao aumento de quota. A utilização para além das suas unidades de produção compradas é acelerada. O Event Hubs Capture copia dados diretamente do armazenamento interno do Event Hubs, contornando as quotas de saída da unidade de produção e guardando a sua saída para outros leitores de processamento, como Stream Analytics ou Spark.

Uma vez configurado, o Event Hubs Capture funciona automaticamente quando envia o seu primeiro evento e continua a correr. Para facilitar o processamento a jusante saber que o processo está a funcionar, o Event Hubs escreve ficheiros vazios quando não há dados. Este processo fornece uma cadência e marcador previsíveis que podem alimentar os seus processadores de lote.

## <a name="setting-up-event-hubs-capture"></a>Criação de Captura de Centros de Eventos

Pode configurar o Tempo de Criação do Centro de Eventos utilizando o [portal Azure,](https://portal.azure.com)ou utilizando modelos de Gestor de Recursos Azure. Para obter mais informações, veja os seguintes artigos:

- [Ativar a funcionalidade de Captura de Hubs de Eventos no portal do Azure](event-hubs-capture-enable-through-portal.md)
- [Criar um espaço de nomes de Hubs de Eventos com o hub de um evento e ativar a Captura através de um modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

> [!NOTE]
> Se ativar a funcionalidade Captura para um centro de eventos existente, a funcionalidade captura eventos que chegam ao centro de eventos **depois** de a funcionalidade ser ativada. Não captura eventos que existiam no centro de eventos antes da funcionalidade ser ligada. 

## <a name="exploring-the-captured-files-and-working-with-avro"></a>Explorar os ficheiros capturados e trabalhar com a Avro

O Event Hubs Capture cria ficheiros em formato Avro, conforme especificado na janela de tempo configurada. Pode ver estes ficheiros em qualquer ferramenta como [o Azure Storage Explorer][Azure Storage Explorer]. Pode descarregar os ficheiros localmente para os trabalhar.

Os ficheiros produzidos pela Event Hubs Capture têm o seguinte esquema Avro:

![Esquema de Avro][3]

Uma maneira fácil de explorar ficheiros Avro é usando o frasco [Avro Tools][Avro Tools] de Apache. Também pode utilizar [o Apache Drill][Apache Drill] para uma experiência leve de SQL ou Apache [Spark][Apache Spark] para realizar um processamento distribuído complexo nos dados ingeridos. 

### <a name="use-apache-drill"></a>Use a broca Apache

[Apache Drill][Apache Drill] é um "motor de consulta SQL de código aberto para a exploração de Big Data" que pode consultar dados estruturados e semi-estruturados onde quer que esteja. O motor pode funcionar como um nó autónomo ou como um enorme cluster para um grande desempenho.

Um suporte nativo ao armazenamento Azure Blob está disponível, o que facilita a consulta de dados num ficheiro Avro, conforme descrito na documentação:

[Perfurador Apache: Plugin de armazenamento de blob Azure][Apache Drill: Azure Blob Storage Plugin]

Para consultar facilmente ficheiros capturados, pode criar e executar um VM com Apache Drill habilitado através de um contentor para aceder ao armazenamento do Azure Blob. Consulte a seguinte amostra: [Streaming em escala com captura de centros de eventos](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-capture).

### <a name="use-apache-spark"></a>Use a faísca Apache

[Apache Spark][Apache Spark] é um "motor de análise unificado para o processamento de dados em larga escala". Suporta diferentes idiomas, incluindo SQL, e pode facilmente aceder ao armazenamento do Azure Blob. Existem algumas opções para executar Apache Spark em Azure, e cada uma fornece fácil acesso ao armazenamento Azure Blob:

- [HDInsight: Ficheiros de endereços no armazenamento do Azure][HDInsight: Address files in Azure storage]
- [Azure Databricks: Armazenamento Azure Blob][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](../aks/spark-job.md) 

### <a name="use-avro-tools"></a>Use ferramentas Avro

[As Ferramentas Avro][Avro Tools] estão disponíveis como um pacote de frascos. Depois de descarregar o ficheiro do frasco, pode ver o esquema de um ficheiro Avro específico executando o seguinte comando:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

Este comando retorna

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Também pode utilizar as Ferramentas Avro para converter o ficheiro no formato JSON e realizar outros processamentos.

Para realizar um processamento mais avançado, descarregue e instale o Avro para a sua escolha de plataforma. No momento desta escrita, existem implementações disponíveis para C, C++, \# C, Java, NodeJS, Perl, PHP, Python e Ruby.

Apache Avro tem guias completos de Arranque para [Java][Java] e [Python.][Python] Também pode ler o artigo ['Começar com o Event Hubs Capture'.](event-hubs-capture-python.md)

## <a name="how-event-hubs-capture-is-charged"></a>Como a captura de centros de eventos é carregada

Event Hubs Capture é medido de forma semelhante às unidades de produção: como uma carga de hora a hora. A carga é diretamente proporcional ao número de unidades de produção adquiridas para o espaço de nome. À medida que as unidades de produção são aumentadas e diminuídas, os contadores de captura de centros de eventos aumentam e diminuem para proporcionar um desempenho correspondente. Os contadores ocorrem em conjunto. Para obter detalhes sobre os preços, consulte [os preços do Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/). 

A captura não consome quota de saída, uma vez que é faturada separadamente. 

## <a name="integration-with-event-grid"></a>Integração com a Grelha de Eventos 

Pode criar uma subscrição da Azure Event Grid com um espaço de nomes de Event Hubs como fonte. O seguinte tutorial mostra-lhe como criar uma subscrição de Event Grid com um centro de eventos como fonte e uma aplicação Azure Functions como pia: [Processar e migrar dados de Centros de Eventos capturados para um Azure Synapse Analytics usando a Grade de Eventos e Funções Azure.](store-captured-data-data-warehouse.md)

## <a name="next-steps"></a>Passos seguintes
Event Hubs Capture é a forma mais fácil de obter dados no Azure. Utilizando o Azure Data Lake, Azure Data Factory e Azure HDInsight, pode realizar o processamento de lotes e outras análises utilizando ferramentas e plataformas familiares à sua escolha, em qualquer escala que necessite.

Saiba como ativar esta funcionalidade utilizando o portal Azure e o modelo Azure Resource Manager:

- [Utilizar o portal do Azure para ativar a Recolha dos Hubs de Eventos](event-hubs-capture-enable-through-portal.md)
- [Use um modelo de Gestor de Recursos Azure para ativar a captura de centros de eventos](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://github.com/microsoft/AzureStorageExplorer/releases
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://downloads.apache.org/avro/stable/java/
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streaming at Scale: Event Hubs Capture]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
