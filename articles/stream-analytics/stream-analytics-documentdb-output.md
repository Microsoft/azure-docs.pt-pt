---
title: Saída Azure Stream Analytics para Azure Cosmos DB
description: Este artigo descreve como usar o Azure Stream Analytics para guardar a saída para Azure Cosmos DB para a saída JSON, para arquivar dados e consultas de baixa latência em dados JSON não estruturados.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: 891cd651278906c6ff4b24d91342c612c67604de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91596560"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Saída Azure Stream Analytics para Azure Cosmos DB  
O Azure Stream Analytics pode visar [a Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) para a saída JSON, permitindo o arquivamento de dados e consultas de baixa latência em dados JSON não estruturados. Este documento abrange algumas boas práticas para a implementação desta configuração.

Se não está familiarizado com a Azure Cosmos DB, consulte a documentação do [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) para começar. 

> [!Note]
> Neste momento, o Stream Analytics suporta a ligação ao Azure Cosmos DB apenas através da *API SQL*.
> Outras APIs DB de Azure Cosmos ainda não estão apoiadas. Se apontar o Stream Analytics para as contas DB do Azure Cosmos criadas com outras APIs, os dados podem não estar devidamente armazenados. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Básicos da Azure Cosmos DB como alvo de saída
A saída DB do Azure Cosmos no Stream Analytics permite escrever os resultados de processamento de fluxo como saída JSON para os seus contentores DB Azure Cosmos. 

O Stream Analytics não cria contentores na sua base de dados. Em vez disso, requer que os cries na frente. Em seguida, pode controlar os custos de faturação dos contentores DB da Azure Cosmos. Também pode sintonizar o desempenho, consistência e capacidade dos seus recipientes diretamente utilizando as [APIs DB Azure Cosmos](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Deve adicionar 0.0.0.0 à lista de IPs permitidos da sua firewall Azure Cosmos DB.

As secções seguintes detalham algumas das opções do contentor para Azure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Afinação da consistência, disponibilidade e latência
Para corresponder aos seus requisitos de aplicação, a Azure Cosmos DB permite-lhe afinar a base de dados e os contentores e fazer trocas entre consistência, disponibilidade, latência e produção. 

Dependendo dos níveis de consistência de leitura que o seu cenário necessita contra a leitura e a latência, pode escolher um nível de consistência na sua conta de base de dados. Pode melhorar a produção aumentando as Unidades de Pedido (RUs) no recipiente. 

Também por padrão, a Azure Cosmos DB permite uma indexação sincronizada em cada operação CRUD ao seu contentor. Esta é outra opção útil para controlar o desempenho de escrita/leitura em Azure Cosmos DB. 

Para mais informações, reveja a [sua base de dados e artigo de níveis de consistência de consulta.](../cosmos-db/consistency-levels.md)

## <a name="upserts-from-stream-analytics"></a>Upserts da Stream Analytics
A integração do Stream Analytics com o Azure Cosmos DB permite-lhe inserir ou atualizar registos no seu contentor com base numa determinada coluna **de ID do Documento.** Isto também é chamado de *upsert.*

Stream Analytics usa uma abordagem otimista de upsert. As atualizações só acontecem quando uma inserção falha com um conflito de identificação de documento. 

Com o nível de compatibilidade 1.0, o Stream Analytics executa esta atualização como uma operação PATCH, pelo que permite atualizações parciais no documento. Stream Analytics adiciona novas propriedades ou substitui uma propriedade existente incrementalmente. No entanto, alterações nos valores das propriedades da matriz no documento JSON resultam na sobreposição de toda a matriz. Isto é, a matriz não está fundida. 

Com 1.2, o comportamento de upsert é modificado para inserir ou substituir o documento. A secção posterior sobre o nível de compatibilidade 1.2 descreve ainda este comportamento.

Se o documento JSON de entrada tiver um campo de ID existente, esse campo é automaticamente utilizado como coluna **de ID documental** em Azure Cosmos DB. Quaisquer escritos subsequentes são tratados como tal, levando a uma destas situações:

- IDs únicos levam a inserir.
- Duplicar iDs e **documento definido** para **iD** levar a upsert.
- Duplicar iDs e **ID de documento** não configurar o erro, após o primeiro documento.

Se pretender guardar *todos os* documentos, incluindo os que têm um ID duplicado, mude o nome do campo de identificação na sua consulta (utilizando a palavra-chave **AS).** Deixe que a Azure Cosmos DB crie o campo de ID ou substitua o ID pelo valor de outra coluna (utilizando a palavra-chave **AS** ou utilizando a definição **de ID** do documento).

## <a name="data-partitioning-in-azure-cosmos-db"></a>Partição de dados em Azure Cosmos DB
A Azure Cosmos DB escala automaticamente as divisórias com base na sua carga de trabalho. Por isso, recomendamos recipientes [ilimitados](../cosmos-db/partition-data.md) como abordagem para dividir os seus dados. Quando o Stream Analytics escreve para recipientes ilimitados, utiliza tantos escritores paralelos como o anterior esquema de consulta ou de divisão de entrada.

> [!NOTE]
> O Azure Stream Analytics suporta apenas recipientes ilimitados com chaves de partição no nível superior. Por exemplo, `/region` é apoiado. As teclas de partição aninhadas (por exemplo, `/region/name` ) não são suportadas. 

Dependendo da sua escolha da chave de partição, poderá receber este _aviso:_

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

É importante escolher uma propriedade chave de partição que tenha uma série de valores distintos, e que lhe permita distribuir a sua carga de trabalho uniformemente através destes valores. Como artefacto natural da partição, os pedidos que envolvem a mesma chave de partição são limitados pela produção máxima de uma única partição. 

O tamanho de armazenamento para documentos que pertencem ao mesmo valor da chave de partição está limitado a 20 GB (o limite de tamanho da [partição física](../cosmos-db/partition-data.md) é de 50 GB). Uma [chave de partição ideal](../cosmos-db/partitioning-overview.md#choose-partitionkey) é aquela que aparece frequentemente como um filtro nas suas consultas e tem cardinaldade suficiente para garantir que a sua solução seja escalável.

As chaves de partição usadas para consultas stream analytics e Cosmos DB não precisam de ser idênticas. As topologias totalmente paralelas recomendam a utilização da *chave de partição de entrada,* `PartitionId` como a chave de partição da consulta Stream Analytics, mas essa pode não ser a escolha recomendada para a chave de partição de um recipiente Cosmos DB.

Uma chave de partição é também o limite para transações em procedimentos armazenados e gatilhos para Azure Cosmos DB. Deve escolher a chave de partição para que os documentos que ocorrem em conjunto em transações partilhem o mesmo valor chave de partição. O artigo [Partition in Azure Cosmos DB](../cosmos-db/partitioning-overview.md) dá mais detalhes sobre a escolha de uma chave de partição.

Para recipientes DB Azure Cosmos fixos, o Stream Analytics não permite que a escala seja aumentada ou fora depois de cheias. Têm um limite superior de 10 GB e 10.000 RU/s de produção. Para migrar os dados de um recipiente fixo para um recipiente ilimitado (por exemplo, um com pelo menos 1.000 RU/s e uma chave de partição), utilize a ferramenta de [migração de dados](../cosmos-db/import-data.md) ou a [biblioteca de feed de alteração](../cosmos-db/change-feed.md).

A capacidade de escrever para vários recipientes fixos está a ser depreciada. Não recomendamos para escalonar o seu trabalho de Stream Analytics.

## <a name="improved-throughput-with-compatibility-level-12"></a>Produção melhorada com compatibilidade nível 1.2
Com o nível de compatibilidade 1.2, o Stream Analytics apoia a integração nativa para escrever em massa em Azure Cosmos DB. Esta integração permite escrever eficazmente para Azure Cosmos DB, ao mesmo tempo que maximiza a produção e lida com os pedidos de estrangulamento de forma eficiente. 

O mecanismo de escrita melhorado está disponível sob um novo nível de compatibilidade devido a uma diferença no comportamento de upsert. Com níveis antes de 1.2, o comportamento de upsert é inserir ou fundir o documento. Com 1.2, o comportamento de upsert é modificado para inserir ou substituir o documento.

Com níveis antes de 1.2, o Stream Analytics utiliza um procedimento personalizado armazenado para aumentar os documentos por chave de partição em Azure Cosmos DB. Lá, um lote é escrito como uma transação. Mesmo quando um único registo atinge um erro transitório (estrangulamento), todo o lote tem de ser novamente experimentado. Isto torna cenários com mesmo um estrangulamento razoável relativamente lento.

O exemplo a seguir mostra dois trabalhos de Stream Analytics idênticos lendo a partir da mesma entrada Azure Event Hubs. Ambos os trabalhos stream Analytics são [totalmente divididos](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) com uma consulta passthrough e escrever para recipientes DB Azure Cosmos idênticos. As métricas à esquerda são do trabalho configurado com o nível de compatibilidade 1.0. As métricas à direita estão configuradas com 1.2. A chave de partição de um contentor Azure Cosmos DB é um GUID único que vem do evento de entrada.

![Comparação das métricas stream analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

A taxa de entrada em Event Hubs é duas vezes maior do que os contentores DB Azure Cosmos (20.000 RUs) estão configurados para acolher, pelo que é esperado estrangulamento em Azure Cosmos DB. No entanto, o trabalho com 1.2 está constantemente a escrever a uma produção mais elevada (eventos de saída por minuto) e com uma utilização média de SU mais baixa. No seu ambiente, esta diferença dependerá de poucos mais fatores. Estes fatores incluem a escolha do formato do evento, o tamanho do evento/mensagem, as teclas de partição e a consulta.

![Comparação de métricas Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Com 1.2, o Stream Analytics é mais inteligente na utilização de 100% da produção disponível em Azure Cosmos DB com muito poucas resubmissãos de estrangulamento ou limitação de taxas. Isto proporciona uma melhor experiência para outras cargas de trabalho, como consultas que correm no recipiente ao mesmo tempo. Se quiser ver como o Stream Analytics escala com a Azure Cosmos DB como pia para 1.000 a 10.000 mensagens por segundo, experimente [este projeto de amostra Azure.](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)

A produção de DB Azure Cosmos é idêntica com 1.0 e 1.1. *Recomendamos vivamente* que utilize o nível de compatibilidade 1.2 em Stream Analytics com Azure Cosmos DB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Definições de DB Azure Cosmos para a saída JSON

A utilização do Azure Cosmos DB como uma saída no Stream Analytics gera o seguinte pedido de informação.

![Campos de informação para um fluxo de saída DB Azure Cosmos](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | Descrição|
|-------------   | -------------|
|Alias de saída    | Um pseudónimo para se referir a esta saída na sua consulta Stream Analytics.|
|Subscrição    | A assinatura do Azure.|
|ID da Conta      | O nome ou ponto final URI da conta DB da Azure Cosmos.|
|Chave da conta     | A chave de acesso partilhada para a conta DB da Azure Cosmos.|
|Base de Dados        | O nome da base de dados Azure Cosmos DB.|
|Nome do contentor | O nome do recipiente, tal `MyContainer` como. Um contentor chamado `MyContainer` deve existir.  |
|ID do documento     | Opcional. O nome da coluna em eventos de saída utilizados como a chave única em que as operações de inserção ou atualização devem ser baseadas. Se o deixar vazio, todos os eventos serão inseridos, sem opção de atualização.|

Depois de configurar a saída DB do Azure Cosmos, pode usá-la na consulta como alvo de uma [declaração INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Quando se está a usar uma saída DB do Azure Cosmos dessa forma, [uma chave de partição precisa de ser definida explicitamente.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks) 

O registo de saída deve conter uma coluna sensível a casos com o nome da chave de partição em Azure Cosmos DB. Para alcançar uma maior paralelização, a declaração pode exigir uma [cláusula PARTITION BY](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) que utilize a mesma coluna.

Aqui está uma consulta de amostra:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Processamento de erros e tentativas

Se uma falha transitória, indisponibilidade de serviço ou estrangulamento acontecer enquanto o Stream Analytics está a enviar eventos para Azure Cosmos DB, a Stream Analytics tenta indefinidamente para terminar a operação com sucesso. Mas não tenta retréis para os seguintes fracassos:

- Não autorizado (código de erro HTTP 401)
- NotFound (código de erro HTTP 404)
- Proibido (código de erro HTTP 403)
- BadRequest (código de erro HTTP 400)
