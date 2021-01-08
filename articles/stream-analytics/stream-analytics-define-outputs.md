---
title: Saídas do Azure Stream Analytics
description: Este artigo descreve as opções de saída de dados disponíveis para o Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 12/9/2020
ms.openlocfilehash: 3ce4f673657561e196520466b569d0cf83d75a8a
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019351"
---
# <a name="outputs-from-azure-stream-analytics"></a>Saídas do Azure Stream Analytics

Um trabalho do Azure Stream Analytics consiste numa entrada, consulta e saída. Existem vários tipos de saída para os quais pode enviar dados transformados. Este artigo lista as saídas suportadas do Stream Analytics. Quando conceber a sua consulta Stream Analytics, consulte o nome da saída utilizando a [cláusula INTO](/stream-analytics-query/into-azure-stream-analytics). Pode utilizar uma única saída por trabalho, ou várias saídas por trabalho de streaming (se precisar) adicionando várias cláusulas INTO à consulta.

Para criar, editar e testar as saídas de trabalho stream Analytics, pode utilizar o [portal Azure](stream-analytics-quick-create-portal.md#configure-job-output) [, Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API,](/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations) [REST API](/rest/api/streamanalytics/)e Visual [Studio](stream-analytics-quick-create-vs.md).

Alguns tipos de saídas [suportam a partição,](#partitioning)e [os tamanhos](#output-batch-size) dos lotes de saída variam para otimizar a produção. A tabela a seguir mostra funcionalidades suportadas por cada tipo de saída:

| Tipo de saída | Criação de partições | Segurança | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|Sim|Utilizador do Azure Ative Directory </br> , Identidade Gerida|
|[Base de Dados SQL do Azure](sql-database-output.md)|Sim, opcional.|Auth utilizador SQL, </br> Identidade Gerida (pré-visualização)|
|[Azure Synapse Analytics](azure-synapse-analytics-output.md)|Sim|Auth utilizador SQL, </br> Identidade Gerida (pré-visualização)|
|[Armazenamento de bolhas e Azure Data Lake Gen 2](blob-storage-azure-data-lake-gen2-output.md)|Sim|Chave de acesso, </br> Identidade Gerida (pré-visualização)|
|[Azure Event Hubs](event-hubs-output.md)|Sim, precisa definir a coluna da chave de partição na configuração de saída.|Chave de acesso, </br> Identidade Gerida (pré-visualização)|
|[Power BI](power-bi-output.md)|Não|Utilizador do Azure Ative Directory, </br> Identidade Gerida|
|[Armazenamento de tabelas do Azure](table-storage-output.md)|Sim|Chave da conta|
|[Filas de autocarros da Azure Service](service-bus-queues-output.md)|Sim|Chave de acesso|
|[Tópicos de ônibus de serviço Azure](service-bus-topics-output.md)|Sim|Chave de acesso|
|[BD do Cosmos para o Azure](azure-cosmos-db-output.md)|Sim|Chave de acesso|
|[Funções do Azure](azure-functions-output.md)|Sim|Chave de acesso|

## <a name="partitioning"></a>Criação de partições

Stream Analytics suporta divisórias para todas as saídas, exceto para Power BI. Para obter mais informações sobre as chaves de partição e o número de autores de saída, consulte o artigo para o tipo de saída específico em que está interessado. Todos os artigos de saída estão ligados na secção anterior.  

Além disso, para uma afinação mais avançada das divisórias, o número de escritores de saída pode ser controlado usando uma `INTO <partition count>` cláusula (ver [INTO)](/stream-analytics-query/into-azure-stream-analytics#into-shard-count)na sua consulta, o que pode ser útil para alcançar uma topologia de trabalho desejada. Se o adaptador de saída não estiver dividido, a falta de dados numa partição de entrada causa um atraso até ao tempo de chegada tardio. Nesses casos, a produção é fundida a um único escritor, o que pode causar estrangulamentos no seu oleoduto. Para saber mais sobre a política de chegada tardia, consulte considerações de [ordem de eventos Azure Stream Analytics](./stream-analytics-time-handling.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída

Todas as saídas suportam o lote, mas apenas algum tamanho do lote de suporte explicitamente. O Azure Stream Analytics utiliza lotes de tamanho variável para processar eventos e escrever para saídas. Normalmente, o motor Stream Analytics não escreve uma mensagem de cada vez e utiliza lotes para eficiência. Quando a taxa dos eventos de entrada e saída é elevada, o Stream Analytics utiliza lotes maiores. Quando a taxa de saída é baixa, utiliza lotes menores para manter a latência baixa.

## <a name="parquet-output-batching-window-properties"></a>Propriedades da janela de loteamento de saída parquet

Ao utilizar a implementação do modelo do Azure Resource Manager ou a API REST, as duas propriedades da janela de lote são:

1. *timeWindow*

   O tempo máximo de espera por lote. O valor deve ser uma cadeia de Timespan. Por exemplo, "00:02:00" por dois minutos. Após este período, o lote é escrito para a saída mesmo que o requisito de linhas mínimas não seja cumprido. O valor predefinido é de 1 minuto e o máximo permitido é de 2 horas. Se a sua saída de bolha tiver frequência de padrão de trajetória, o tempo de espera não pode ser superior ao intervalo de tempo da partição.

2. *tamanhoWindow*

   O número de filas mínimas por lote. Para o Parquet, cada lote cria um novo ficheiro. O valor predefinido atual é de 2.000 linhas e o máximo permitido é de 10.000 linhas.

Estas propriedades da janela de loteamento só são suportadas pela versão API **2017-04-01-pré-visualização**. Abaixo está um exemplo da carga útil JSON para uma chamada de API REST:

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
>
> [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
