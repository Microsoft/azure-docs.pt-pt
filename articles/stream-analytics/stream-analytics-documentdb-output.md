---
title: Saída do Azure Stream Analytics ao Azure Cosmos DB
description: Este artigo descreve como utilizar o Azure Stream Analytics para guardar a saída para o Azure Cosmos DB para a saída JSON, para o arquivo de dados e consultas de baixa latência nos dados não estruturados de JSON.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986993"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Saída do Azure Stream Analytics ao Azure Cosmos DB  
Azure Stream Analytics pode direcionar [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) para saída JSON, permitindo o arquivamento de dados e consultas de baixa latência em dados JSON não estruturados. Este documento abrange algumas das melhores práticas para implementar esta configuração.

Se você não estiver familiarizado com Azure Cosmos DB, consulte a [documentação do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) para começar. 

> [!Note]
> Neste momento, Stream Analytics dá suporte à conexão para Azure Cosmos DB somente por meio da *API do SQL*.
> Outras APIs do Azure Cosmos DB ainda não são suportados. Se você apontar Stream Analytics para Azure Cosmos DB contas criadas com outras APIs, talvez os dados não sejam armazenados corretamente. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Noções básicas de Azure Cosmos DB como um destino de saída
A saída de Azure Cosmos DB no Stream Analytics permite gravar os resultados do processamento de fluxo como saída JSON em seus contêineres de Azure Cosmos DB. 

Stream Analytics não cria contêineres em seu banco de dados. Em vez disso, ele exige que você os crie antecipadamente. Em seguida, você pode controlar os custos de cobrança de contêineres de Azure Cosmos DB. Você também pode ajustar o desempenho, a consistência e a capacidade de seus contêineres diretamente usando as [APIs de Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Você deve adicionar 0.0.0.0 à lista de IPs permitidos de seu firewall Azure Cosmos DB.

As seções a seguir detalham algumas das opções de contêiner para Azure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Ajustando a consistência, a disponibilidade e a latência
Para corresponder aos requisitos do aplicativo, Azure Cosmos DB permite ajustar o banco de dados e os contêineres e fazer compensações entre consistência, disponibilidade, latência e taxa de transferência. 

Dependendo do que níveis de consistência de leitura suas necessidades de cenário em relação a ler e escrever a latência, que pode escolher um nível de consistência na sua conta de base de dados. Você pode melhorar a taxa de transferência expandindo as unidades de solicitação (RUs) no contêiner. 

Também por padrão, Azure Cosmos DB habilita a indexação síncrona em cada operação CRUD para seu contêiner. Essa é outra opção útil para controlar o desempenho de gravação/leitura no Azure Cosmos DB. 

Para obter mais informações, examine o artigo [alterar o banco de dados e os níveis de consistência de consulta](../cosmos-db/consistency-levels.md) .

## <a name="upserts-from-stream-analytics"></a>Upserts do Stream Analytics
Stream Analytics integração com o Azure Cosmos DB permite inserir ou atualizar registros em seu contêiner com base em uma determinada coluna de **ID de documento** . Isso também é chamado de *Upsert*.

Stream Analytics usa uma abordagem Upsert otimista. As atualizações acontecem somente quando uma inserção falha com um conflito de ID de documento. 

Com o nível de compatibilidade 1,0, o Stream Analytics executa essa atualização como uma operação de PATCH, de modo que permite atualizações parciais no documento. Stream Analytics adiciona novas propriedades ou substitui uma propriedade existente de forma incremental. No entanto, as alterações nos valores das propriedades de matriz em seu documento JSON resultam na substituição de toda a matriz. Ou seja, a matriz não é mesclada. 

Com o 1,2, o comportamento Upsert é modificado para inserir ou substituir o documento. A seção mais adiante sobre o nível de compatibilidade 1,2 descreve ainda mais esse comportamento.

Se o documento JSON de entrada tiver um campo ID existente, esse campo será usado automaticamente como a coluna **ID do documento** em Azure Cosmos DB. Todas as gravações subsequentes são tratadas como tal, levando a uma destas situações:

- As IDs exclusivas levam à inserção.
- IDs duplicadas e **ID do documento** definidas como **ID** Lead to Upsert.
- IDs duplicadas e **ID do documento** não definidas cliente potencial para erro, após o primeiro documento.

Se você quiser salvar *todos os* documentos, incluindo aqueles que têm uma ID duplicada, renomeie o campo ID em sua consulta (usando **a palavra-chave as)** . Deixe Azure Cosmos DB criar o campo ID ou substituir a ID pelo valor de outra coluna (usando **a palavra-chave as ou** usando a configuração ID do **documento** ).

## <a name="data-partitioning-in-azure-cosmos-db"></a>Particionamento de dados no Azure Cosmos DB
Azure Cosmos DB dimensiona automaticamente as partições com base em sua carga de trabalho. Portanto, recomendamos contêineres [ilimitados](../cosmos-db/partition-data.md) como a abordagem para particionar seus dados. Quando Stream Analytics grava em contêineres ilimitados, ele usa como muitos gravadores paralelos como a etapa de consulta anterior ou o esquema de particionamento de entrada.

> [!NOTE]
> Azure Stream Analytics dá suporte apenas a contêineres ilimitados com chaves de partição no nível superior. Por exemplo, `/region` é suportada. Não há suporte para chaves de partição aninhadas (por exemplo, `/region/name`). 

Dependendo de sua escolha de chave de partição, você poderá receber este _aviso_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

É importante escolher uma propriedade de chave de partição que tenha um número de valores distintos e que permita distribuir sua carga de trabalho uniformemente entre esses valores. Como um artefato natural de particionamento, as solicitações que envolvem a mesma chave de partição são limitadas pela taxa de transferência máxima de uma única partição. 

O tamanho do armazenamento de documentos que pertencem à mesma chave de partição é limitado a 10 GB. Uma chave de partição ideal é aquela que aparece com frequência como um filtro em suas consultas e tem cardinalidade suficiente para garantir que sua solução seja escalonável.

Uma chave de partição também é o limite para transações em procedimentos armazenados e gatilhos para Azure Cosmos DB. Você deve escolher a chave de partição para que os documentos que ocorrem em transações compartilhem o mesmo valor de chave de partição. O artigo [particionamento no Azure Cosmos DB](../cosmos-db/partitioning-overview.md) fornece mais detalhes sobre como escolher uma chave de partição.

Para contêineres de Azure Cosmos DB fixos, Stream Analytics não permite escalar verticalmente ou horizontalmente depois que eles estiverem cheios. Eles têm um limite superior de 10 GB e 10.000 RU/s de taxa de transferência. Para migrar os dados de um contêiner fixo para um contêiner ilimitado (por exemplo, um com pelo menos 1.000 RU/s e uma chave de partição), use a [ferramenta de migração de dados](../cosmos-db/import-data.md) ou a biblioteca de [feeds de alterações](../cosmos-db/change-feed.md).

A capacidade de gravar em vários contêineres fixos está sendo preterida. Não recomendamos isso para escalar horizontalmente seu trabalho de Stream Analytics.

## <a name="improved-throughput-with-compatibility-level-12"></a>Taxa de transferência aprimorada com nível de compatibilidade 1,2
Com o nível de compatibilidade 1,2, o Stream Analytics dá suporte à integração nativa para gravação em massa em Azure Cosmos DB. Essa integração permite gravar com eficiência em Azure Cosmos DB enquanto maximiza a taxa de transferência e manipula com eficiência as solicitações de limitação. 

O mecanismo de escrita aprimorado está disponível em um novo nível de compatibilidade devido a uma diferença no comportamento de Upsert. Com os níveis anteriores a 1,2, o comportamento do Upsert é inserir ou mesclar o documento. Com o 1,2, o comportamento Upsert é modificado para inserir ou substituir o documento.

Com níveis anteriores a 1,2, Stream Analytics usa um procedimento armazenado personalizado para Upsert em massa documentos por chave de partição no Azure Cosmos DB. Lá, um lote é gravado como uma transação. Mesmo quando um único registro atinge um erro transitório (limitação), todo o lote precisa ser repetido. Isso torna os cenários com limitação uniforme relativamente lenta.

O exemplo a seguir mostra dois trabalhos de Stream Analytics idênticos lendo da mesma entrada dos hubs de eventos do Azure. Ambos os trabalhos de Stream Analytics são [totalmente particionados](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) com uma consulta de passagem e gravam em contêineres de Azure Cosmos DB idênticos. As métricas à esquerda são do trabalho configurado com o nível de compatibilidade 1,0. As métricas à direita são configuradas com 1,2. Uma chave de partição do contêiner de Azure Cosmos DB é um GUID exclusivo proveniente do evento de entrada.

![Comparação de métricas de Stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

A taxa de eventos de entrada nos hubs de eventos é duas vezes maior do que Azure Cosmos DB contêineres (20.000 RUs) são configurados para serem necessários, portanto, a limitação é esperada em Azure Cosmos DB. No entanto, o trabalho com 1.2 é consistentemente escrito a uma produção mais alta (eventos de saída por minuto) e com uma utilização média mais baixa da SU%. Em seu ambiente, essa diferença dependerá de mais alguns fatores. Esses fatores incluem opções de formato de evento, tamanho de evento/mensagem de entrada, chaves de partição e consulta.

![Comparação de métricas de Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Com o 1,2, Stream Analytics é mais inteligente na utilização de 100% da taxa de transferência disponível em Azure Cosmos DB com poucos reenvios de limitação ou limitação de taxa. Isso fornece uma experiência melhor para outras cargas de trabalho, como consultas em execução no contêiner ao mesmo tempo. Se você quiser ver como Stream Analytics é dimensionada com Azure Cosmos DB como um coletor de 1.000 a 10.000 mensagens por segundo, experimente [este projeto de exemplo do Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb).

A taxa de transferência de Azure Cosmos DB saída é idêntica com 1,0 e 1,1. É *altamente recomendável* que você use o nível de compatibilidade 1,2 em Stream Analytics com Azure Cosmos DB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Configurações de Azure Cosmos DB para saída JSON

O uso de Azure Cosmos DB como uma saída no Stream Analytics gera o prompt a seguir para obter informações.

![Campos de informações para um fluxo de saída Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | Descrição|
|-------------   | -------------|
|Alias de saída    | Um alias para se referir a essa saída em sua consulta de Stream Analytics.|
|Subscrição    | A assinatura do Azure.|
|ID da Conta      | O nome ou URI do ponto de extremidade da conta de Azure Cosmos DB.|
|Chave de conta     | A chave de acesso compartilhado para a conta de Azure Cosmos DB.|
|Base de Dados        | O nome do banco de dados Azure Cosmos DB.|
|Nome do contentor | O nome do contêiner, como `MyContainer`. Um contêiner chamado `MyContainer` deve existir.  |
|ID do documento     | Opcional. O nome da coluna em eventos de saída utilizado como a chave exclusiva no qual inserção ou atualização de operações devem basear-se. Se você deixá-lo vazio, todos os eventos serão inseridos, sem a opção de atualização.|

Depois de configurar a saída de Azure Cosmos DB, você pode usá-la na consulta como o destino de uma [instrução into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Quando você estiver usando uma saída de Azure Cosmos DB dessa forma, [uma chave de partição precisará ser definida explicitamente](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). 

O registro de saída deve conter uma coluna que diferencia maiúsculas de minúsculas, denominada após a chave de partição em Azure Cosmos DB. Para obter maior paralelização, a instrução pode exigir uma [cláusula PARTITION by](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) que usa a mesma coluna.

Veja um exemplo de consulta:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Processamento de erros e tentativas

Se ocorrer uma falha transitória, indisponibilidade de serviço ou limitação enquanto Stream Analytics estiver enviando eventos para Azure Cosmos DB, Stream Analytics novas tentativas indefinidamente para concluir a operação com êxito. Mas não tenta novas tentativas para as seguintes falhas:

- Não autorizado (código de erro HTTP 401)
- Não encontrado (código de erro HTTP 404)
- Proibido (código de erro HTTP 403)
- BadRequest (código de erro HTTP 400)
