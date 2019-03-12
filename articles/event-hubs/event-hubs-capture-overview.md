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
ms.openlocfilehash: f1fefb863770c4b71249e924162c543cc217c9d2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541473"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Capturar eventos através dos Hubs de eventos do Azure no armazenamento de Blobs do Azure ou de armazenamento do Azure Data Lake
Os Hubs de eventos do Azure permite-lhe capturar automaticamente os dados de transmissão em fluxo nos Hubs de eventos num [armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) ou [o armazenamento do Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) à sua escolha, com a flexibilidade adicional do especificar um hora ou intervalo de tamanho. Configurar a captura é rápido, existem sem os custos administrativos para executá-lo e é escalado automaticamente com os Hubs de eventos [unidades de débito](event-hubs-features.md#capacity). Captura de Hubs de eventos é a forma mais fácil de carregar dados de transmissão em fluxo para o Azure e permite-lhe concentrar-se no processamento de dados em vez de captura de dados.

Captura de Hubs de eventos permite-lhe processar pipelines em tempo real e em lote no mesmo fluxo. Isso significa que pode criar soluções que aumentam com as suas necessidades ao longo do tempo. Se estiver a criar sistemas baseados em batch hoje em dia com um olho em direção futuro processamento em tempo real, ou que pretende adicionar um caminho típico eficiente para uma solução em tempo real, a captura de Hubs de eventos torna o trabalho com dados mais fácil de transmissão em fluxo.

## <a name="how-event-hubs-capture-works"></a>Como funciona a captura de Hubs de eventos

Os Hubs de eventos é uma memória intermédia durável de tempo de retenção para entrada de telemetria, semelhante a um registo distribuído. A chave do dimensionamento nos Hubs de eventos é o [modelo de consumidor particionado](event-hubs-features.md#partitions). Cada partição é um segmento independente de dados e é consumida de forma independente. Ao longo do tempo estes dados idade dos, com base no período de retenção configurável. Como resultado, um hub de eventos fornecido nunca recebe "demasiado completo."

Captura de Hubs de eventos permite-lhe especificar sua própria conta de armazenamento de Blobs do Azure e um contentor ou uma conta do Azure Data Lake Store, que são utilizadas para armazenar os dados capturados. Estas contas podem ser na mesma região que o seu hub de eventos ou noutra região, adicionando a flexibilidade da funcionalidade de captura de Hubs de eventos.

Dados capturados são escritos em [Apache Avro] [ Apache Avro] formato: um formato compacto, rápido e binário que fornece estruturas de dados avançados com o esquema inline. Este formato é bastante usado no ecossistema Hadoop, o Stream Analytics e o Azure Data Factory. Obter mais informações sobre como trabalhar com o Avro estão disponíveis neste artigo.

### <a name="capture-windowing"></a>Capturar janelas

Captura de Hubs de eventos permite-lhe configurar uma janela para controlar a capturar. Essa janela é um tamanho mínimo e a configuração de tempo com um "primeiro wins policy", que significa que o primeiro acionador encontrado faz com que uma operação de captura. Se tiver um quinze minutos, 100 MB capturar a janela e a enviar de 1 MB por segundo, os acionadores de janela de tamanho antes da janela de tempo. Cada partição captura de forma independente e escreve um blob de blocos concluído no momento da captura, com o nome para a hora em que o intervalo de captura foi encontrado. A Convenção de nomenclatura do armazenamento é o seguinte:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Tenha em atenção que os valores de data estão preenchidos com zeros; pode ser um nome de ficheiro de exemplo:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

### <a name="scaling-to-throughput-units"></a>Dimensionar para as unidades de débito

Tráfego de Hubs de eventos é controlado pelas [unidades de débito](event-hubs-features.md#capacity). Uma única unidade de débito permite 1 MB por segundo ou 1000 eventos por segundo de entrada e de duas vezes essa quantidade de saída. Hubs de eventos Standard pode ser configurados com as unidades de débito de 1 a 20, e pode comprar mais com uma quota de aumentar [pedido de suporte][support request]. A utilização além das suas unidades de débito adquiridas é limitada. Captura de Hubs de eventos copia os dados diretamente a partir do armazenamento interno dos Hubs de eventos, ignorando as quotas de saída de unidade de débito e a guardar a saída para outros leitores de processamento, como o Stream Analytics ou do Spark.

Depois de configurada, a captura de Hubs de eventos é executada automaticamente quando enviar o seu primeiro evento e continua a ser executado. Para tornar mais fácil para o processamento a jusante saber se o processo está a funcionar, os Hubs de eventos escreve ficheiros vazios quando não houver dados. Este processo fornece uma cadência previsível e um marcador que pode alimentar os processadores do batch.

## <a name="setting-up-event-hubs-capture"></a>Configurar a captura de Hubs de eventos

Pode configurar a captura na através do tempo de criação do hub de eventos a [portal do Azure](https://portal.azure.com), ou utilizando modelos Azure Resource Manager. Para obter mais informações, veja os artigos seguintes:

- [Ativar a captura de Hubs de eventos com o portal do Azure](event-hubs-capture-enable-through-portal.md)
- [Criar um espaço de nomes de Hubs de eventos com um hub de eventos e ativar a captura através de um modelo Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Explorar os arquivos capturados e trabalhar com o Avro

Captura de Hubs de eventos cria arquivos no formato Avro, conforme especificado na janela de tempo configurado. Podem ver os ficheiros em qualquer ferramenta, como [Explorador de armazenamento do Azure][Azure Storage Explorer]. Pode transferir os ficheiros localmente para trabalhar nos mesmos.

Os ficheiros produzidos pela captura de Hubs de eventos com o esquema de Avro seguinte:

![Esquema Avro][3]

Uma forma fácil de explorar os ficheiros Avro é utilizando o [ferramentas do Avro] [ Avro Tools] jar do Apache. Também pode utilizar [Apache Drill] [ Apache Drill] para uma experiência condicionada por SQL de simples ou [Apache Spark] [ Apache Spark] efetuar complexos distribuído processamento dos dados ingeridos. 

### <a name="use-apache-drill"></a>Utilizar Apache Drill

[Apache Drill] [ Apache Drill] é um "open-source motor de consulta SQL para exploração de grandes volumes de dados" que pode consultar dados estruturados e semiestruturados, sempre que é. O mecanismo pode ser executado como um nó autónomo ou como um cluster grande ótimo desempenho.

Um suporte nativo para o armazenamento de Blobs do Azure está disponível, que torna mais fácil para consultar dados num arquivo de Avro, conforme descrito na documentação do:

[Apache Drill: Plug-in de armazenamento de Blobs do Azure][Apache Drill: Azure Blob Storage Plugin]

Para consultar facilmente capturados ficheiros, pode criar e executar uma VM com o Apache Drill ativada através de um contentor para aceder ao armazenamento de Blobs do Azure:

https://github.com/yorek/apache-drill-azure-blob

Um exemplo completo ponta a ponta está disponível na transmissão em fluxo no repositório de dimensionamento:

[Transmissão em fluxo em escala: Captura de Hubs de eventos]

### <a name="use-apache-spark"></a>Use Apache Spark

[Apache Spark] [ Apache Spark] é um "unificada motor de análise para processamento de dados em grande escala." Ele oferece suporte a idiomas diferentes, incluindo SQL e pode acessar facilmente o armazenamento de Blobs do Azure. Existem duas opções para executar o Apache Spark no Azure e fornecem acesso fácil para o armazenamento de Blobs do Azure:

- [HDInsight: Endereçar ficheiros no armazenamento do Azure][HDInsight: Address files in Azure storage]
- [O Azure Databricks: Armazenamento de Blobs do Azure][Azure Databricks: Azure Blob Storage]

### <a name="use-avro-tools"></a>Utilizar as ferramentas do Avro

[Ferramentas do Avro] [ Avro Tools] estão disponíveis como um pacote de jar. Depois de transferir o ficheiro jar, pode ver o esquema de um arquivo específico do Avro executando o seguinte comando:

```shell
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
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

Apache Avro tem guias de introdução completos para [Java] [ Java] e [Python][Python]. Pode ainda ler os [introdução à captura de Hubs de eventos](event-hubs-capture-python.md) artigo.

## <a name="how-event-hubs-capture-is-charged"></a>Como a captura de Hubs de eventos é cobrada

Captura de Hubs de eventos é medido da mesma forma para unidades de débito: como uma tarifa por hora. O custo é diretamente proporcional ao número de unidades de débito adquiridas para o espaço de nomes. Como unidades de débito for aumentadas e diminuídas, medidores de captura de Hubs de eventos aumentarem e diminuir para fornecer desempenho correspondente. Os medidores ocorrerem em conjunto. Para detalhes de preços, consulte [preços de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="integration-with-event-grid"></a>Integração com o Event Grid 

Pode criar uma subscrição do Azure Event Grid com um espaço de nomes de Hubs de eventos como origem. O tutorial seguinte mostra como criar uma subscrição do Event Grid com um hub de eventos como uma origem e de uma aplicação de funções do Azure como um sink: [Processar e migrar dados de Hubs de eventos capturados para um SQL Data Warehouse com o Event Grid e as funções do Azure](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Passos Seguintes

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
[Avro Tools]: https://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage#address-files-in-azure-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Transmissão em fluxo em escala: Captura de Hubs de eventos]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
