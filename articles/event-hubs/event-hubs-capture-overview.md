---
title: Capturar eventos de streaming - Azure Event Hubs [ Azure Event Hubs ] Microsoft Docs
description: Este artigo fornece uma visão geral da funcionalidade Capture que permite capturar eventos que transmitem através do Azure Event Hubs.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265015"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Capture eventos através de Hubs de Eventos Azure em Armazenamento De Blob Azure ou Armazenamento de Lago de Dados Azure
O Azure Event Hubs permite-lhe capturar automaticamente os dados de streaming em Event Hubs num [armazenamento Azure Blob](https://azure.microsoft.com/services/storage/blobs/) ou na conta de Armazenamento do [Lago Azure Data Gen 1 ou Gen 2](https://azure.microsoft.com/services/data-lake-store/) à sua escolha, com a flexibilidade adicional de especificar um intervalo de tempo ou tamanho. A configuração da Captura é rápida, não existem custos administrativos para executá-lo, e escala automaticamente com [unidades](event-hubs-scalability.md#throughput-units)de produção de Hubs de Eventos . O Event Hubs Capture é a forma mais fácil de carregar dados de streaming para o Azure, e permite-lhe focar-se no processamento de dados e não na captura de dados.

A Captura de Hubs de Eventos permite-lhe processar oleodutos em tempo real e baseados em lotes no mesmo fluxo. Isto significa que pode construir soluções que cresçam com as suas necessidades ao longo do tempo. Quer esteja a construir sistemas baseados em lotes hoje em dia com vista para o processamento futuro em tempo real, ou se quer adicionar um caminho frio eficiente a uma solução em tempo real existente, o Event Hubs Capture facilita o trabalho com dados de streaming.


## <a name="how-event-hubs-capture-works"></a>Como funciona a captura de centros de eventos

O Event Hubs é um tampão durável de retenção de tempo para a entrada de telemetria, semelhante a um registo distribuído. A chave para escalar em Event Hubs é o [modelo de consumidor dividido.](event-hubs-scalability.md#partitions) Cada partição é um segmento independente de dados e é consumido de forma independente. Com o tempo, estes dados envelhecem, com base no período de retenção configurável. Como resultado, um determinado centro de eventos nunca fica "muito cheio".

A Captura de Hubs de Eventos permite especificar a sua própria conta de armazenamento e contentor Azure Blob, ou conta de Armazenamento de Lagos De Dados Azure, que são usadas para armazenar os dados capturados. Estas contas podem estar na mesma região que o seu centro de eventos ou noutra região, aumentando a flexibilidade da funcionalidade de Captura de Hubs de Eventos.

Os dados capturados são escritos em formato [Apache Avro:][Apache Avro] um formato compacto, rápido e binário que fornece estruturas de dados ricas com esquemas inline. Este formato é amplamente utilizado no ecossistema Hadoop, Stream Analytics e Azure Data Factory. Mais informações sobre o trabalho com a Avro estão disponíveis mais tarde neste artigo.

### <a name="capture-windowing"></a>Capturar janelas

A Captura de Hubs de Eventos permite-lhe configurar uma janela para controlar a captura. Esta janela é uma configuração de tamanho mínimo e tempo com uma "primeira política de vitórias", o que significa que o primeiro gatilho encontrado causa uma operação de captura. Se tiver uma janela de captura de 15 minutos e 100 MB e enviar 1 MB por segundo, a janela de tamanho dispara antes da janela do tempo. Cada partição captura independentemente e escreve uma bolha de bloco completano momento da captura, nomeada para o momento em que o intervalo de captura foi encontrado. A convenção de nomeação de armazenamento é a seguinte:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Note que os valores da data são acolchoados com zeros; um nome de ficheiro de exemplo pode ser:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Caso a sua bolha de armazenamento Azure esteja temporariamente indisponível, a Captura de Hubs de Eventos conservará os seus dados para o período de retenção de dados configurado no seu centro de eventos e encha os dados assim que a sua conta de armazenamento estiver novamente disponível.

### <a name="scaling-to-throughput-units"></a>Escalapara unidades de entrada

O tráfego de Hubs de Eventos é controlado por [unidades](event-hubs-scalability.md#throughput-units)de produção . Uma única unidade de entrada permite 1 MB por segundo ou 1000 eventos por segundo de entrada e o dobro dessa quantidade de saída. Os Standard Event Hubs podem ser configurados com unidades de produção de 1-20, e pode comprar mais com um pedido de [suporte][support request]de aumento de quota . O uso para além das unidades de entrada adquiridas é acelerado. Os Hubs de Eventos Capturam dados diretamente do armazenamento interno do Event Hubs, contornando as quotas da unidade de produção e salvando as suas saídas para outros leitores de processamento, como stream analytics ou Spark.

Uma vez configurado, a Captura de Centros de Eventos é automática quando envia o seu primeiro evento e continua a correr. Para facilitar o seu processamento a jusante saber que o processo está a funcionar, o Event Hubs escreve ficheiros vazios quando não há dados. Este processo fornece uma cadência e marcador previsíveis que podem alimentar os seus processadores de lote.

## <a name="setting-up-event-hubs-capture"></a>Configuração da captura de hubs de eventos

Pode configurar a Captura no tempo de criação do hub de eventos utilizando o [portal Azure,](https://portal.azure.com)ou utilizando modelos do Gestor de Recursos Azure. Para obter mais informações, veja os artigos seguintes:

- [Ativar a funcionalidade de Captura de Hubs de Eventos no portal do Azure](event-hubs-capture-enable-through-portal.md)
- [Criar um espaço de nomes de Hubs de Eventos com o hub de um evento e ativar a Captura através de um modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Explorar os ficheiros capturados e trabalhar com a Avro

O Event Hubs Capture cria ficheiros no formato Avro, conforme especificado na janela de tempo configurada. Pode ver estes ficheiros em qualquer ferramenta como o [Azure Storage Explorer][Azure Storage Explorer]. Pode descarregar os ficheiros localmente para trabalhar neles.

Os ficheiros produzidos pela Event Hubs Capture têm o seguinte esquema Avro:

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
- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/spark-job) 

### <a name="use-avro-tools"></a>Use ferramentas Avro

[Avro Tools][Avro Tools] está disponível como um pacote de frascos. Depois de descarregar o ficheiro do frasco, pode ver o esquema de um ficheiro Avro específico executando o seguinte comando:

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

Também pode utilizar ferramentas Avro para converter o ficheiro em formato JSON e realizar outro processamento.

Para realizar um processamento mais avançado, descarregue e instale a Avro para a sua escolha da plataforma. No momento desta escrita, existem implementações disponíveis para C, C++, C,\#Java, NodeJS, Perl, PHP, Python e Ruby.

Apache Avro tem guias completos de arranque para [Java][Java] e [Python.][Python] Você também pode ler o [artigo Getting started with Event Hubs Capture.](event-hubs-capture-python.md)

## <a name="how-event-hubs-capture-is-charged"></a>Como é que a captura de hubs de eventos é carregada

A captura de Hubs de Eventos é medida da mesma forma às unidades de produção: como uma carga horária. A carga é diretamente proporcional ao número de unidades de entrada adquiridas para o espaço de nome. À medida que as unidades de produção são aumentadas e diminuídas, os centros de eventos Capturam os contadores e diminuem para proporcionar um desempenho correspondente. Os contadores ocorrem em conjunto. Para mais detalhes sobre preços, consulte os preços dos Centros de [Eventos.](https://azure.microsoft.com/pricing/details/event-hubs/) 

Note que a Captura não consome quota de saída, uma vez que é faturada separadamente. 

## <a name="integration-with-event-grid"></a>Integração com Grelha de Eventos 

Pode criar uma subscrição da Azure Event Grid com um espaço de nome do Event Hubs como fonte. O seguinte tutorial mostra-lhe como criar uma subscrição da Rede de Eventos com um hub de eventos como fonte e uma aplicação De Funções Azure como um lavatório: [Processar e migrar dados de Hubs de Eventos capturados para um Armazém de Dados SQL usando a Grelha de Eventos e Funções Azure.](store-captured-data-data-warehouse.md)

## <a name="next-steps"></a>Passos seguintes
O Event Hubs Capture é a forma mais fácil de obter dados no Azure. Utilizando o Azure Data Lake, a Azure Data Factory e o Azure HDInsight, pode realizar o processamento de lotes e outras análises utilizando ferramentas e plataformas familiares à sua escolha, a qualquer escala que necessite.

Saiba como ativar esta funcionalidade utilizando o portal Azure e o modelo do Gestor de Recursos Azure:

- [Utilizar o portal do Azure para ativar a Recolha dos Hubs de Eventos](event-hubs-capture-enable-through-portal.md)
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
[Streaming em escala: Captura de hubs de eventos]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
