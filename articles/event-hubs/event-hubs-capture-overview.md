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
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: c166f4cace6a8cc25b36a84f4614033801e69a51
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265015"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Capturar eventos através dos Hubs de eventos do Azure no armazenamento de Blobs do Azure ou de armazenamento do Azure Data Lake
O Azure Event Hubs permite-lhe capturar automaticamente os dados de streaming em Event Hubs num [armazenamento Azure Blob](https://azure.microsoft.com/services/storage/blobs/) ou na conta de Armazenamento do [Lago Azure Data Gen 1 ou Gen 2](https://azure.microsoft.com/services/data-lake-store/) à sua escolha, com a flexibilidade adicional de especificar um intervalo de tempo ou tamanho. A configuração da Captura é rápida, não existem custos administrativos para executá-lo, e escala automaticamente com [unidades](event-hubs-scalability.md#throughput-units)de produção de Hubs de Eventos . Captura de Hubs de eventos é a forma mais fácil de carregar dados de transmissão em fluxo para o Azure e permite-lhe concentrar-se no processamento de dados em vez de captura de dados.

Captura de Hubs de eventos permite-lhe processar pipelines em tempo real e em lote no mesmo fluxo. Isso significa que pode criar soluções que aumentam com as suas necessidades ao longo do tempo. Se estiver a criar sistemas baseados em batch hoje em dia com um olho em direção futuro processamento em tempo real, ou que pretende adicionar um caminho típico eficiente para uma solução em tempo real, a captura de Hubs de eventos torna o trabalho com dados mais fácil de transmissão em fluxo.


## <a name="how-event-hubs-capture-works"></a>Como funciona a captura de Hubs de eventos

Os Hubs de eventos é uma memória intermédia durável de tempo de retenção para entrada de telemetria, semelhante a um registo distribuído. A chave para escalar em Event Hubs é o [modelo de consumidor dividido.](event-hubs-scalability.md#partitions) Cada partição é um segmento independente de dados e é consumida de forma independente. Ao longo do tempo estes dados idade dos, com base no período de retenção configurável. Como resultado, um hub de eventos fornecido nunca recebe "demasiado completo."

A Captura de Hubs de Eventos permite especificar a sua própria conta de armazenamento e contentor Azure Blob, ou conta de Armazenamento de Lagos De Dados Azure, que são usadas para armazenar os dados capturados. Estas contas podem ser na mesma região que o seu hub de eventos ou noutra região, adicionando a flexibilidade da funcionalidade de captura de Hubs de eventos.

Os dados capturados são escritos em formato [Apache Avro:][Apache Avro] um formato compacto, rápido e binário que fornece estruturas de dados ricas com esquemas inline. Este formato é bastante usado no ecossistema Hadoop, o Stream Analytics e o Azure Data Factory. Obter mais informações sobre como trabalhar com o Avro estão disponíveis neste artigo.

### <a name="capture-windowing"></a>Capturar janelas

Captura de Hubs de eventos permite-lhe configurar uma janela para controlar a capturar. Essa janela é um tamanho mínimo e a configuração de tempo com um "primeiro wins policy", que significa que o primeiro acionador encontrado faz com que uma operação de captura. Se tiver um quinze minutos, 100 MB capturar a janela e a enviar de 1 MB por segundo, os acionadores de janela de tamanho antes da janela de tempo. Cada partição captura de forma independente e escreve um blob de blocos concluído no momento da captura, com o nome para a hora em que o intervalo de captura foi encontrado. A Convenção de nomenclatura do armazenamento é o seguinte:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Tenha em atenção que os valores de data estão preenchidos com zeros; pode ser um nome de ficheiro de exemplo:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Caso a sua bolha de armazenamento Azure esteja temporariamente indisponível, a Captura de Hubs de Eventos conservará os seus dados para o período de retenção de dados configurado no seu centro de eventos e encha os dados assim que a sua conta de armazenamento estiver novamente disponível.

### <a name="scaling-to-throughput-units"></a>Dimensionar para as unidades de débito

O tráfego de Hubs de Eventos é controlado por [unidades](event-hubs-scalability.md#throughput-units)de produção . Uma única unidade de débito permite 1 MB por segundo ou 1000 eventos por segundo de entrada e de duas vezes essa quantidade de saída. Os Standard Event Hubs podem ser configurados com unidades de produção de 1-20, e pode comprar mais com um pedido de [suporte][support request]de aumento de quota . A utilização além das suas unidades de débito adquiridas é limitada. Captura de Hubs de eventos copia os dados diretamente a partir do armazenamento interno dos Hubs de eventos, ignorando as quotas de saída de unidade de débito e a guardar a saída para outros leitores de processamento, como o Stream Analytics ou do Spark.

Depois de configurada, a captura de Hubs de eventos é executada automaticamente quando enviar o seu primeiro evento e continua a ser executado. Para tornar mais fácil para o processamento a jusante saber se o processo está a funcionar, os Hubs de eventos escreve ficheiros vazios quando não houver dados. Este processo fornece uma cadência previsível e um marcador que pode alimentar os processadores do batch.

## <a name="setting-up-event-hubs-capture"></a>Configurar a captura de Hubs de eventos

Pode configurar a Captura no tempo de criação do hub de eventos utilizando o [portal Azure,](https://portal.azure.com)ou utilizando modelos do Gestor de Recursos Azure. Para obter mais informações, veja os artigos seguintes:

- [Ativar a captura de hubs de eventos usando o portal Azure](event-hubs-capture-enable-through-portal.md)
- [Crie um espaço de nome de Hubs de Eventos com um hub de eventos e ative a Captura usando um modelo de Gestor de Recursos Azure](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Explorar os arquivos capturados e trabalhar com o Avro

Captura de Hubs de eventos cria arquivos no formato Avro, conforme especificado na janela de tempo configurado. Pode ver estes ficheiros em qualquer ferramenta como o [Azure Storage Explorer][Azure Storage Explorer]. Pode transferir os ficheiros localmente para trabalhar nos mesmos.

Os ficheiros produzidos pela captura de Hubs de eventos com o esquema de Avro seguinte:

![Esquema avro][3]

Uma maneira fácil de explorar ficheiros Avro é usando o jarro [Avro Tools][Avro Tools] de Apache. Também pode utilizar a [Apache Drill][Apache Drill] para uma experiência leve orientada por SQL ou [Apache Spark][Apache Spark] para realizar um processamento complexo distribuído nos dados ingeridos. 

### <a name="use-apache-drill"></a>Use broca Apache

[Apache Drill][Apache Drill] é um "motor de consulta SQL de código aberto para exploração de Big Data" que pode consultar dados estruturados e semi-estruturados onde quer que esteja. O motor pode funcionar como um nó autónomo ou como um enorme cluster para um grande desempenho.

Está disponível um suporte nativo ao armazenamento do Azure Blob, o que facilita a consulta de dados num ficheiro Avro, conforme descrito na documentação:

[Broca Apache: Plugin de armazenamento de blob azure][Apache Drill: Azure Blob Storage Plugin]

Para consulta fácil de ficheiros capturados, pode criar e executar um VM com Broca Apache habilitado através de um recipiente para aceder ao armazenamento azure blob:

https://github.com/yorek/apache-drill-azure-blob

Uma amostra completa de ponta a ponta está disponível no repositório streaming em escala:

[Streaming em escala: Captura de hubs de eventos]

### <a name="use-apache-spark"></a>Use faísca Apache

[Apache Spark][Apache Spark] é um "motor de análise unificado para processamento de dados em larga escala". Suporta diferentes idiomas, incluindo o SQL, e pode facilmente aceder ao armazenamento Azure Blob. Existem algumas opções para executar Apache Spark em Azure, e cada uma fornece fácil acesso ao armazenamento Azure Blob:

- [HDInsight: Ficheiros de endereços no armazenamento do Azure][HDInsight: Address files in Azure storage]
- [Azure Databricks: Armazenamento Azure Blob][Azure Databricks: Azure Blob Storage]
- [Serviço Kubernetes do Azure](https://docs.microsoft.com/azure/aks/spark-job) 

### <a name="use-avro-tools"></a>Use ferramentas Avro

[Avro Tools][Avro Tools] está disponível como um pacote de frascos. Depois de descarregar o ficheiro do frasco, pode ver o esquema de um ficheiro Avro específico executando o seguinte comando:

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

Para efetuar o processamento de mais avançado, transfira e instale o Avro para sua escolha de plataforma. No momento desta escrita, existem implementações C++disponíveis para C, C\#, Java, NodeJS, Perl, PHP, Python e Ruby.

Apache Avro tem guias completos de arranque para [Java][Java] e [Python.][Python] Você também pode ler o [artigo Getting started with Event Hubs Capture.](event-hubs-capture-python.md)

## <a name="how-event-hubs-capture-is-charged"></a>Como a captura de Hubs de eventos é cobrada

Captura de Hubs de eventos é medido da mesma forma para unidades de débito: como uma tarifa por hora. O custo é diretamente proporcional ao número de unidades de débito adquiridas para o espaço de nomes. Como unidades de débito for aumentadas e diminuídas, medidores de captura de Hubs de eventos aumentarem e diminuir para fornecer desempenho correspondente. Os medidores ocorrerem em conjunto. Para mais detalhes sobre preços, consulte os preços dos Centros de [Eventos.](https://azure.microsoft.com/pricing/details/event-hubs/) 

Note que a Captura não consome quota de saída, uma vez que é faturada separadamente. 

## <a name="integration-with-event-grid"></a>Integração com o Event Grid 

Pode criar uma subscrição do Azure Event Grid com um espaço de nomes de Hubs de eventos como origem. O seguinte tutorial mostra-lhe como criar uma subscrição da Rede de Eventos com um hub de eventos como fonte e uma aplicação De Funções Azure como um lavatório: [Processar e migrar dados de Hubs de Eventos capturados para um Armazém de Dados SQL usando a Grelha de Eventos e Funções Azure.](store-captured-data-data-warehouse.md)

## <a name="next-steps"></a>Passos seguintes
Captura de Hubs de eventos é a maneira mais fácil obter dados no Azure. Utilizar o Azure Data Lake, o Azure Data Factory e o Azure HDInsight, pode efetuar o processamento de batch e outras análises com ferramentas familiares e plataformas à sua escolha, em qualquer escala precisa.

Saiba como ativar esta funcionalidade utilizando o portal Azure e o modelo do Gestor de Recursos Azure:

- [Utilizar o portal do Azure para ativar a Captura dos Hubs de Eventos](event-hubs-capture-enable-through-portal.md)
- [Use um modelo de Gestor de Recursos Azure para permitir a captura de hubs de eventos](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www.apache.org/dist/avro/stable/java/avro-tools-1.9.2.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streaming em escala: Captura de hubs de eventos]: https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
