---
title: Saída de Azure Stream Analytics para Azure Cosmos DB
description: Este artigo descreve como usar o Azure Stream Analytics para economizar saída para o Azure Cosmos DB para a saída jSON, para arquivamento de dados e consultas de baixa latência em dados JSON não estruturados.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79254446"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Saída de Azure Stream Analytics para Azure Cosmos DB  
O Azure Stream Analytics pode visar [o Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) para a saída jSON, permitindo o arquivamento de dados e consultas de baixa latência em dados JSON não estruturados. Este documento abrange algumas boas práticas para implementar esta configuração.

Se não está familiarizado com o Azure Cosmos DB, consulte a [documentação do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) para começar. 

> [!Note]
> Neste momento, o Stream Analytics suporta a ligação ao Azure Cosmos DB apenas através da *API SQL*.
> Outras APIs DB da Azure Cosmos ainda não são apoiadas. Se apontar o Stream Analytics para as contas DO BD da Azure Cosmos criadas com outras APIs, os dados podem não estar devidamente armazenados. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Básicos de Azure Cosmos DB como alvo de saída
A saída do Azure Cosmos DB no Stream Analytics permite escrever os resultados do processamento de fluxo à medida que a JSON produz nos seus recipientes Azure Cosmos DB. 

O Stream Analytics não cria recipientes na sua base de dados. Em vez disso, requer que os crie na frente. Em seguida, pode controlar os custos de faturação dos contentores Azure Cosmos DB. Também pode afinar o desempenho, consistência e capacidade dos seus recipientes diretamente utilizando as [APIs DB Do Cosmos Azure](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Deve adicionar 0.0.0.0 à lista de IPs permitidos da sua firewall Azure Cosmos DB.

As secções seguintes detalham algumas das opções de contentores para o Azure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Consistência de afinação, disponibilidade e latência
Para corresponder aos seus requisitos de aplicação, o Azure Cosmos DB permite-lhe afinar a base de dados e os contentores e fazer trocas entre consistência, disponibilidade, latência e saída. 

Dependendo dos níveis de consistência de leitura que o seu cenário necessita contra a leitura e a latência, pode escolher um nível de consistência na sua conta de base de dados. Pode melhorar a entrada através da escala das Unidades de Pedido (RUs) no recipiente. 

Também por padrão, o Azure Cosmos DB permite indexação sincronizada em cada operação CRUD ao seu recipiente. Esta é outra opção útil para controlar o desempenho de escrita/leitura em Azure Cosmos DB. 

Para mais informações, reveja o artigo Alterar a sua base de dados e consultar os níveis de [consistência.](../cosmos-db/consistency-levels.md)

## <a name="upserts-from-stream-analytics"></a>Upserts de Stream Analytics
A integração do Stream Analytics com o Azure Cosmos DB permite-lhe inserir ou atualizar registos no seu recipiente com base numa dada coluna **de ID de documento.** Isto também é chamado *de upsert*.

Stream Analytics usa uma abordagem otimista de upsert. As atualizações só acontecem quando uma inserção falha com um conflito de identificação de documentos. 

Com o nível de compatibilidade 1.0, o Stream Analytics executa esta atualização como uma operação PATCH, pelo que permite atualizações parciais para o documento. Stream Analytics adiciona novas propriedades ou substitui uma propriedade existente incrementalmente. No entanto, alterações nos valores das propriedades da matriz no seu documento JSON resultam na sobreposição de toda a matriz. Ou seja, a matriz não está fundida. 

Com 1.2, o comportamento de upsert é modificado para inserir ou substituir o documento. A secção posterior sobre compatibilidade nível 1.2 descreve ainda este comportamento.

Se o documento JSON de entrada tiver um campo de identificação existente, esse campo é automaticamente utilizado como coluna **id** documental em Azure Cosmos DB. Quaisquer escritos subsequentes são tratados como tal, levando a uma destas situações:

- IDs exclusivos levam à inserção.
- IDs duplicados e **id de documento** definido para **ID** levam a upsert.
- IDs duplicados e **ID de documento** não definidolevam a erros, após o primeiro documento.

Se quiser guardar *todos os* documentos, incluindo os que têm identificação duplicada, mude o nome do campo de identificação na sua consulta (utilizando a palavra-chave **AS).** Deixe o Azure Cosmos DB criar o campo de ID ou substituir o ID pelo valor de outra coluna (utilizando a palavra-chave **AS** ou utilizando a definição de ID de **documento).**

## <a name="data-partitioning-in-azure-cosmos-db"></a>Partição de dados em Azure Cosmos DB
O Azure Cosmos DB escala automaticamente as divisórias com base na sua carga de trabalho. Por isso, recomendamos recipientes [ilimitados](../cosmos-db/partition-data.md) como abordagem para a partilha dos seus dados. Quando o Stream Analytics escreve para recipientes ilimitados, utiliza tantos escritores paralelos como o passo de consulta anterior ou o esquema de partição de entrada.

> [!NOTE]
> O Azure Stream Analytics suporta apenas recipientes ilimitados com chaves de partição no nível superior. Por exemplo, `/region` é apoiado. As teclas de partição aninhadas (por exemplo, `/region/name`) não são suportadas. 

Dependendo da sua escolha da chave de partição, poderá receber este _aviso:_

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

É importante escolher uma propriedade chave de partição que tenha uma série de valores distintos, e que lhe permite distribuir a sua carga de trabalho uniformemente por estes valores. Como artefacto natural da partilha, os pedidos que envolvem a mesma chave de partição são limitados pela potência máxima de uma única divisória. 

O tamanho do armazenamento de documentos que pertencem à mesma chave de partição está limitado a 10 GB. Uma chave de partição ideal é aquela que aparece frequentemente como um filtro nas suas consultas e tem cardinalidade suficiente para garantir que a sua solução é escalável.

Uma chave de partição é também o limite para transações em procedimentos armazenados e gatilhos para Azure Cosmos DB. Deve escolher a chave de partição para que os documentos que ocorram em conjunto nas transações partilhem o mesmo valor-chave de partilha. O artigo [Partição em Azure Cosmos DB](../cosmos-db/partitioning-overview.md) dá mais detalhes sobre a escolha de uma chave de partição.

Para recipientes db azure cosmos fixos, o Stream Analytics não permite aumentar ou sair depois de estarem cheios. Têm um limite superior de 10 GB e 10.000 RU/s de entrada. Para migrar os dados de um recipiente fixo para um recipiente ilimitado (por exemplo, um com pelo menos 1.000 RU/s e uma chave de partição), utilize a ferramenta de [migração](../cosmos-db/import-data.md) de dados ou a biblioteca de alimentos para [mudanças.](../cosmos-db/change-feed.md)

A capacidade de escrever a vários recipientes fixos está a ser depreciada. Não o recomendamos para escalonar o seu trabalho de Stream Analytics.

## <a name="improved-throughput-with-compatibility-level-12"></a>Melhor a sua contribuição com o nível de compatibilidade 1.2
Com compatibilidade nível 1.2, stream Analytics apoia a integração nativa para escrever a granel em Azure Cosmos DB. Esta integração permite escrever eficazmente ao Azure Cosmos DB, maximizando simultaneamente a entrada e manuseando eficientemente os pedidos de estrangulamento. 

O mecanismo de escrita melhorado está disponível sob um novo nível de compatibilidade devido a uma diferença no comportamento de upsert. Com níveis antes de 1.2, o comportamento de upsert é inserir ou fundir o documento. Com 1.2, o comportamento de upsert é modificado para inserir ou substituir o documento.

Com níveis antes de 1.2, o Stream Analytics utiliza um procedimento personalizado armazenado para amassar documentos por chave de partição em Azure Cosmos DB. Ali, um lote é escrito como uma transação. Mesmo quando um único disco atinge um erro transitório (estrangulamento), todo o lote tem de ser novamente experimentado. Isto faz com que os cenários com uma aceleração razoável seja relativamente lento.

O exemplo seguinte mostra dois trabalhos idênticos da Stream Analytics a ler a partir da mesma entrada do Azure Event Hubs. Ambos os trabalhos da Stream Analytics são [totalmente divididos](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) com uma consulta de passagem e escrevem para recipientes db da Azure Cosmos idênticos. As métricas à esquerda são do trabalho configurado com o nível de compatibilidade 1.0. As métricas à direita estão configuradas com 1.2. A chave de partição do contentor Azure Cosmos DB é um GUID único que vem do evento de entrada.

![Comparação das métricas stream analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

A taxa de eventos de entrada em Event Hubs é duas vezes maior do que os contentores Azure Cosmos DB (20.000 RUs) estão configurados para acolher, pelo que é esperado estrangulamento em Azure Cosmos DB. No entanto, o trabalho com 1.2 é consistentemente escrito a uma produção mais alta (eventos de saída por minuto) e com uma utilização média mais baixa da SU%. No seu ambiente, esta diferença dependerá de mais alguns fatores. Estes fatores incluem a escolha do formato do evento, o tamanho do evento/mensagem de entrada, as teclas de partição e a consulta.

![Comparação de métricas de DB da Azure Cosmos](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Com 1.2, o Stream Analytics é mais inteligente na utilização de 100 por cento da entrada disponível em Azure Cosmos DB com muito poucas resubmissões de estrangulamento ou limitação de taxas. Isto proporciona uma melhor experiência para outras cargas de trabalho, como consultas que correm no recipiente ao mesmo tempo. Se quiser ver como o Stream Analytics se escorre com o Azure Cosmos DB como um lavatório para 1.000 a 10.000 mensagens por segundo, experimente [este projeto de amostra Azure.](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)

A produção da saída Azure Cosmos DB é idêntica a 1.0 e 1.1. *Recomendamos vivamente* que utilize o nível de compatibilidade 1.2 em Stream Analytics com o Azure Cosmos DB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Definições de DB do Azure Cosmos para a saída json

A utilização do Azure Cosmos DB como uma saída no Stream Analytics gera o seguinte pedido de informação.

![Campos de informação para um fluxo de saída Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | Descrição|
|-------------   | -------------|
|Alias de saída    | Um pseudónimo para se referir a esta saída na sua consulta stream analytics.|
|Subscrição    | A assinatura Azure.|
|ID da Conta      | O nome ou ponto final URI da conta Azure Cosmos DB.|
|Chave de conta     | A chave de acesso partilhado para a conta Azure Cosmos DB.|
|Base de Dados        | O nome da base de dados Azure Cosmos DB.|
|Nome do contentor | O nome do `MyContainer`recipiente, como . Um contentor `MyContainer` chamado deve existir.  |
|Id documento     | Opcional. O nome da coluna em eventos de saída utilizados como chave única na qual devem ser baseadas as operações de inserção ou atualização. Se o deixar vazio, todos os eventos serão inseridos, sem opção de atualização.|

Depois de configurar a saída Do Azure Cosmos DB, pode usá-la na consulta como alvo de uma [declaração EM](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Quando se está a usar uma saída De DB Azure Cosmos dessa forma, uma chave de [partição tem de ser definida explicitamente.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks) 

O registo de saída deve conter uma coluna sensível a casos com o nome da chave de partição em Azure Cosmos DB. Para conseguir uma maior paralelização, a declaração pode requerer uma [cláusula DE PARTIÇÃO POR](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) QUE utiliza a mesma coluna.

Aqui está uma consulta de amostra:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Processamento de erros e tentativas

Se uma falha transitória, a indisponibilidade do serviço ou o estrangulamento ocorrerem enquanto o Stream Analytics está a enviar eventos para o Azure Cosmos DB, o Stream Analytics tenta indefinidamente terminar a operação com sucesso. Mas não tenta voltar a tentar os seguintes fracassos:

- Não autorizado (código de erro HTTP 401)
- NotFound (código de erro HTTP 404)
- Proibido (código de erro HTTP 403)
- BadRequest (código de erro HTTP 400)
