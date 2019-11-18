---
title: Capturar eventos de transmissão em fluxo - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece uma visão geral da funcionalidade de captura permite capturar eventos de transmissão em fluxo através dos Hubs de eventos do Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 17906a7d0953d8b320301d74cda81d14c9ad340f
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123442"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Capturar eventos através dos Hubs de eventos do Azure no armazenamento de Blobs do Azure ou de armazenamento do Azure Data Lake
Os Hubs de eventos do Azure permite-lhe capturar automaticamente os dados de transmissão em fluxo nos Hubs de eventos num [armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) ou [o armazenamento do Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) à sua escolha, com a flexibilidade adicional do especificar um hora ou intervalo de tamanho. Configurar a captura é rápido, existem sem os custos administrativos para executá-lo e é escalado automaticamente com os Hubs de eventos [unidades de débito](event-hubs-scalability.md#throughput-units). Captura de Hubs de eventos é a forma mais fácil de carregar dados de transmissão em fluxo para o Azure e permite-lhe concentrar-se no processamento de dados em vez de captura de dados.

Captura de Hubs de eventos permite-lhe processar pipelines em tempo real e em lote no mesmo fluxo. Isso significa que pode criar soluções que aumentam com as suas necessidades ao longo do tempo. Se estiver a criar sistemas baseados em batch hoje em dia com um olho em direção futuro processamento em tempo real, ou que pretende adicionar um caminho típico eficiente para uma solução em tempo real, a captura de Hubs de eventos torna o trabalho com dados mais fácil de transmissão em fluxo.

## <a name="how-event-hubs-capture-works"></a>Como funciona a captura de Hubs de eventos

Os Hubs de eventos é uma memória intermédia durável de tempo de retenção para entrada de telemetria, semelhante a um registo distribuído. A chave do dimensionamento nos Hubs de eventos é o [modelo de consumidor particionado](event-hubs-scalability.md#partitions). Cada partição é um segmento independente de dados e é consumida de forma independente. Ao longo do tempo estes dados idade dos, com base no período de retenção configurável. Como resultado, um hub de eventos fornecido nunca recebe "demasiado completo."

Captura de Hubs de eventos permite-lhe especificar sua própria conta de armazenamento de Blobs do Azure e um contentor ou uma conta do Azure Data Lake Store, que são utilizadas para armazenar os dados capturados. Estas contas podem ser na mesma região que o seu hub de eventos ou noutra região, adicionando a flexibilidade da funcionalidade de captura de Hubs de eventos.

Os dados capturados são gravados no formato [Apache Avro][Apache Avro] : um formato compacto, rápido e binário que fornece estruturas de dados avançadas com esquema embutido. Este formato é bastante usado no ecossistema Hadoop, o Stream Analytics e o Azure Data Factory. Obter mais informações sobre como trabalhar com o Avro estão disponíveis neste artigo.

### <a name="capture-windowing"></a>Capturar janelas

Captura de Hubs de eventos permite-lhe configurar uma janela para controlar a capturar. Essa janela é um tamanho mínimo e a configuração de tempo com um "primeiro wins policy", que significa que o primeiro acionador encontrado faz com que uma operação de captura. Se tiver um quinze minutos, 100 MB capturar a janela e a enviar de 1 MB por segundo, os acionadores de janela de tamanho antes da janela de tempo. Cada partição captura de forma independente e escreve um blob de blocos concluído no momento da captura, com o nome para a hora em que o intervalo de captura foi encontrado. A Convenção de nomenclatura do armazenamento é o seguinte:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Tenha em atenção que os valores de data estão preenchidos com zeros; pode ser um nome de ficheiro de exemplo:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Caso o seu blob de armazenamento do Azure esteja temporariamente indisponível, a captura de hubs de eventos manterá seus dados para o período de retenção de dados configurado em seu hub de eventos e preencherá os dados assim que sua conta de armazenamento estiver disponível novamente.

### <a name="scaling-to-throughput-units"></a>Dimensionar para as unidades de débito

Tráfego de Hubs de eventos é controlado pelas [unidades de débito](event-hubs-scalability.md#throughput-units). Uma única unidade de débito permite 1 MB por segundo ou 1000 eventos por segundo de entrada e de duas vezes essa quantidade de saída. Os hubs de eventos padrão podem ser configurados com 1-20 unidades de produtividade e você pode comprar mais com uma [solicitação de suporte][support request]de aumento de cota. A utilização além das suas unidades de débito adquiridas é limitada. Captura de Hubs de eventos copia os dados diretamente a partir do armazenamento interno dos Hubs de eventos, ignorando as quotas de saída de unidade de débito e a guardar a saída para outros leitores de processamento, como o Stream Analytics ou do Spark.

Depois de configurada, a captura de Hubs de eventos é executada automaticamente quando enviar o seu primeiro evento e continua a ser executado. Para tornar mais fácil para o processamento a jusante saber se o processo está a funcionar, os Hubs de eventos escreve ficheiros vazios quando não houver dados. Este processo fornece uma cadência previsível e um marcador que pode alimentar os processadores do batch.

## <a name="setting-up-event-hubs-capture"></a>Configurar a captura de Hubs de eventos

Pode configurar a captura na através do tempo de criação do hub de eventos a [portal do Azure](https://portal.azure.com), ou utilizando modelos Azure Resource Manager. Para obter mais informações, veja os artigos seguintes:

- [Ativar a captura de Hubs de eventos com o portal do Azure](event-hubs-capture-enable-through-portal.md)
- [Criar um espaço de nomes de Hubs de eventos com um hub de eventos e ativar a captura através de um modelo Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Explorar os arquivos capturados e trabalhar com o Avro

Captura de Hubs de eventos cria arquivos no formato Avro, conforme especificado na janela de tempo configurado. Você pode exibir esses arquivos em qualquer ferramenta, como [Gerenciador de armazenamento do Azure][Azure Storage Explorer]. Pode transferir os ficheiros localmente para trabalhar nos mesmos.

Os ficheiros produzidos pela captura de Hubs de eventos com o esquema de Avro seguinte:

![Esquema Avro][3]

Uma maneira fácil de explorar arquivos Avro é usando o jar das [Ferramentas do Avro][Avro Tools] do Apache. Você também pode usar o [Apache Drill][Apache Drill] para uma experiência leve orientada por SQL ou [Apache Spark][Apache Spark] para executar processamento distribuído complexo nos dados ingeridos. 

### <a name="use-apache-drill"></a>Usar o Apache Drill

O [Apache Drill][Apache Drill] é um "mecanismo de consulta SQL de código aberto para exploração de Big data" que pode consultar dados estruturados e semiestruturados onde quer que esteja. O mecanismo pode ser executado como um nó autônomo ou como um cluster enorme para um ótimo desempenho.

Um suporte nativo ao armazenamento de BLOBs do Azure está disponível, o que torna mais fácil consultar dados em um arquivo Avro, conforme descrito na documentação:

[Apache Drill: plug-in do armazenamento de BLOBs do Azure][Apache Drill: Azure Blob Storage Plugin]

Para consultar facilmente os arquivos capturados, você pode criar e executar uma VM com o Apache Drill habilitado por meio de um contêiner para acessar o armazenamento de BLOBs do Azure:

https://github.com/yorek/apache-drill-azure-blob

Um exemplo completo de ponta a ponta está disponível no repositório de streaming em escala:

[Streaming em escala: captura de hubs de eventos]

### <a name="use-apache-spark"></a>Usar Apache Spark

[Apache Spark][Apache Spark] é um "mecanismo de análise unificado para processamento de dados em grande escala". Ele dá suporte a diferentes idiomas, incluindo SQL, e pode acessar facilmente o armazenamento de BLOBs do Azure. Há duas opções para executar Apache Spark no Azure, e ambas fornecem acesso fácil ao armazenamento de BLOBs do Azure:

- [HDInsight: Arquivos de endereço no armazenamento do Azure][HDInsight: Address files in Azure storage]
- [Azure Databricks: armazenamento de BLOBs do Azure][Azure Databricks: Azure Blob Storage]

### <a name="use-avro-tools"></a>Usar ferramentas do Avro

As [Ferramentas do Avro][Avro Tools] estão disponíveis como um pacote jar. Depois de baixar o arquivo JAR, você poderá ver o esquema de um arquivo Avro específico executando o seguinte comando:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

Este comando devolve

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

Também pode utilizar ferramentas de Avro para converter o ficheiro em formato JSON e executar outro processamento.

Para efetuar o processamento de mais avançado, transfira e instale o Avro para sua escolha de plataforma. No momento da redação deste artigo, há implementações disponíveis para C, C++, C\#, Java, NodeJS, Perl, PHP, Python e Ruby.

O Apache Avro tem guias de Introdução completas para [Java][Java] e [Python][Python]. Pode ainda ler os [introdução à captura de Hubs de eventos](event-hubs-capture-python.md) artigo.

## <a name="how-event-hubs-capture-is-charged"></a>Como a captura de Hubs de eventos é cobrada

Captura de Hubs de eventos é medido da mesma forma para unidades de débito: como uma tarifa por hora. O custo é diretamente proporcional ao número de unidades de débito adquiridas para o espaço de nomes. Como unidades de débito for aumentadas e diminuídas, medidores de captura de Hubs de eventos aumentarem e diminuir para fornecer desempenho correspondente. Os medidores ocorrerem em conjunto. Para detalhes de preços, consulte [preços de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/). 

Observe que a captura não consome a cota de saída, pois é cobrada separadamente. 

## <a name="integration-with-event-grid"></a>Integração com o Event Grid 

Pode criar uma subscrição do Azure Event Grid com um espaço de nomes de Hubs de eventos como origem. O tutorial seguinte mostra-lhe como criar uma subscrição do Event Grid com um hub de eventos como uma origem e de uma aplicação de funções do Azure como um sink: [processo e migrar dados de Hubs de eventos capturados para um SQL Data Warehouse com o Event Grid e as funções do Azure](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Passos seguintes

Captura de Hubs de eventos é a maneira mais fácil obter dados no Azure. Utilizar o Azure Data Lake, o Azure Data Factory e o Azure HDInsight, pode efetuar o processamento de batch e outras análises com ferramentas familiares e plataformas à sua escolha, em qualquer escala precisa.

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Introdução ao envio e receção de eventos](event-hubs-dotnet-framework-getstarted-send.md)
* [Descrição geral dos Hubs de Eventos][Event Hubs overview]

[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www.apache.org/dist/avro/stable/java/avro-tools-1.9.1.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streaming em escala: captura de hubs de eventos]: https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
