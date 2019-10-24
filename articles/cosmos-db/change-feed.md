---
title: Trabalhando com o suporte do feed de alterações no Azure Cosmos DB
description: Use Azure Cosmos DB suporte do feed de alterações para controlar alterações em documentos e executar processamento baseado em eventos, como gatilhos e manter os sistemas de cache e análise atualizados.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 8e6bd3dadd636127f212db0ea0c0755a6b52a087
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757016"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Alterar feed em Azure Cosmos DB-visão geral

O suporte ao feed de alterações no Azure Cosmos DB funciona ouvindo um contêiner Cosmos do Azure para qualquer alteração. Em seguida, disponibiliza a lista ordenada dos documentos que foram alterados, pela ordem pela qual foram modificados. As alterações são preservadas, podem ser processadas de forma assíncrona e incremental, e o resultado pode ser distribuído por um ou mais consumidores para processamento paralelo. 

Azure Cosmos DB é adequado para IoT, jogos, varejo e aplicativos de log operacional. Um padrão de design comum nesses aplicativos é usar alterações nos dados para disparar ações adicionais. Exemplos de ações adicionais incluem:

* Disparar uma notificação ou uma chamada para uma API, quando um item é inserido ou atualizado.
* Processamento de fluxo em tempo real para IoT ou processamento de análise em tempo real em dados operacionais.
* Movimentação de dados adicional sincronizando com um cache ou um mecanismo de pesquisa, ou um data warehouse ou arquivando dados no armazenamento frio.

O feed de alterações no Azure Cosmos DB permite que você crie soluções eficientes e escalonáveis para cada um desses padrões, conforme mostrado na imagem a seguir:

![Usando Azure Cosmos DB feed de alterações para análise em tempo real e cenários de computação orientados a eventos](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>APIs e SDKs de cliente com suporte

Atualmente, há suporte para esse recurso nas seguintes APIs de Azure Cosmos DB e SDKs de cliente.

| **Drivers de cliente** | **CLI do Azure** | **API DO SQL** | **API do Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API Gremlin**|**API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | N/D | Sim | Não | Não | Sim | Não |
|Java|N/D|Sim|Não|Não|Sim|Não|
|Python|N/D|Sim|Não|Não|Sim|Não|
|Node/JS|N/D|Sim|Não|Não|Sim|Não|

## <a name="change-feed-and-different-operations"></a>O feed de alterações e operações diferentes

Hoje, você verá todas as operações no feed de alterações. A funcionalidade em que você pode controlar o feed de alterações, para operações específicas como somente atualizações e não inserções ainda não está disponível. Você pode adicionar um "marcador flexível" no item para atualizações e filtrar com base no que se processam itens no feed de alterações. Atualmente, o feed de alterações não registra exclusões. Semelhante ao exemplo anterior, você pode adicionar um marcador flexível nos itens que estão sendo excluídos, por exemplo, você pode adicionar um atributo no item chamado "Deleted" e defini-lo como "true" e definir um TTL no item, para que ele possa ser excluído automaticamente. Você pode ler o feed de alterações para itens históricos (a alteração mais recente correspondente ao item, não inclui as alterações intermediárias), por exemplo, itens que foram adicionados cinco anos atrás. Se o item não for excluído, você poderá ler o feed de alterações até a origem do seu contêiner.

### <a name="sort-order-of-items-in-change-feed"></a>Ordem de classificação de itens no feed de alterações

Os itens do feed de alterações vêm na ordem de seu tempo de modificação. Essa ordem de classificação é garantida por chave de partição lógica.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Alterar feed em contas do Azure Cosmos de várias regiões

Em uma conta do Azure Cosmos de várias regiões, se uma região de gravação falhar, o feed de alterações funcionará na operação de failover manual e será contíguo.

### <a name="change-feed-and-time-to-live-ttl"></a>Alterar feed e TTL (vida útil)

Se uma propriedade TTL (vida útil) for definida em um item como-1, o feed de alterações persistirá de forma contínua. Se os dados não forem excluídos, eles permanecerão no feed de alterações.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Change feed e _etag, _lsn ou _ts

O formato _etag é interno e você não deve se depender dele, pois ele pode mudar a qualquer momento. _ts é uma modificação ou um carimbo de data/hora de criação. Você pode usar _ts para comparação cronológica. _lsn é uma ID de lote que é adicionada somente para o feed de alterações; representa a ID da transação. Muitos itens podem ter o mesmo _lsn. ETag em FeedResponse é diferente do _etag que você vê no item. _etag é um identificador interno e é usado para controle de simultaneidade diz sobre a versão do item, enquanto ETag é usado para sequenciar o feed.

## <a name="change-feed-use-cases-and-scenarios"></a>Cenários e casos de uso do feed de alterações

O feed de alterações permite o processamento eficiente de conjuntos de grandes volumes com um alto volume de gravações. O feed de alterações também oferece uma alternativa para consultar um conjunto de um inteiro para identificar o que mudou.

### <a name="use-cases"></a>Casos de utilização

Por exemplo, com o feed de alterações, você pode executar as seguintes tarefas com eficiência:

* Atualizar um cache, atualizar um índice de pesquisa ou atualizar um data warehouse com os dados armazenados no Azure Cosmos DB.

* Implemente um arquivamento e uma camada de dados no nível do aplicativo, por exemplo, armazene "dados ativos" em Azure Cosmos DB e expire "dados frios" para outros sistemas de armazenamento, por exemplo, o [armazenamento de BLOBs do Azure](../storage/common/storage-introduction.md).

* Execute migrações sem tempo de inatividade para outra conta do Azure Cosmos ou outro contêiner Cosmos do Azure com uma chave de partição lógica diferente.

* Implemente a [arquitetura lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) usando Azure Cosmos DB, onde Azure Cosmos DB dá suporte a camadas de serviço em tempo real, em lote e de consulta, permitindo, assim, a arquitetura lambda com baixo TCO.

* Receba e armazene dados de eventos de dispositivos, sensores, infraestrutura e aplicativos e processe esses eventos em tempo real, por exemplo, usando o [Spark](../hdinsight/spark/apache-spark-overview.md).  A imagem a seguir mostra como você pode implementar a arquitetura lambda usando Azure Cosmos DB por meio do feed de alterações:

![Pipeline lambda baseado em Azure Cosmos DB para ingestão e consulta](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Cenários

A seguir estão alguns dos cenários que você pode implementar facilmente com o feed de alterações:

* Em seus aplicativos móveis ou Web sem [servidor](https://azure.microsoft.com/solutions/serverless/) , você pode acompanhar eventos como todas as alterações no perfil, nas preferências ou no local de seu cliente e disparar determinadas ações, por exemplo, enviar notificações por push para seus dispositivos usando o [Azure Funções](change-feed-functions.md)do.

* Se você estiver usando Azure Cosmos DB para criar um jogo, você pode, por exemplo, usar o feed de alterações para implementar placares em tempo real com base em pontuações de jogos concluídos.


## <a name="working-with-change-feed"></a>Trabalhando com o feed de alterações

Você pode trabalhar com o feed de alterações usando as seguintes opções:

* [Usando o feed de alterações com Azure Functions](change-feed-functions.md)
* [Usando o feed de alterações com o processador do feed de alterações](change-feed-processor.md) 

O feed de alterações está disponível para cada chave de partição lógica dentro do contêiner e pode ser distribuído em um ou mais consumidores para processamento paralelo, conforme mostrado na imagem abaixo.

![Processamento distribuído de Azure Cosmos DB feed de alterações](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Recursos do feed de alterações

* O feed de alterações é habilitado por padrão para todas as contas do Azure Cosmos.

* Você pode usar sua [taxa de transferência provisionada](request-units.md) para ler o feed de alterações, assim como qualquer outra operação de Azure Cosmos DB, em qualquer uma das regiões associadas ao banco de dados Cosmos do Azure.

* O feed de alterações inclui inserções e operações de atualização feitas em itens dentro do contêiner. Você pode capturar exclusões definindo um sinalizador de "exclusão reversível" dentro de seus itens (por exemplo, documentos) em vez de exclusões. Como alternativa, você pode definir um período de expiração finito para seus itens com a [capacidade TTL](time-to-live.md). Por exemplo, 24 horas e usar o valor dessa propriedade para capturar exclusões. Com essa solução, você precisa processar as alterações em um intervalo de tempo menor do que o período de Expiração TTL. 

* Cada alteração em um item aparece exatamente uma vez no feed de alterações, e os clientes devem gerenciar a lógica de ponto de verificação. Se você quiser evitar a complexidade do gerenciamento de pontos de verificação, o processador do feed de alterações fornecerá o ponto de verificação automático e a semântica "pelo menos uma vez". Consulte [usar o feed de alterações com o processador do feed de alterações](change-feed-processor.md).

* Somente a alteração mais recente de um determinado item é incluída no log de alterações. As alterações intermediárias podem não estar disponíveis.

* O feed de alterações é classificado pela ordem de modificação dentro de cada valor de chave de partição lógica. Não há nenhuma ordem garantida nos valores de chave de partição.

* As alterações podem ser sincronizadas a partir de qualquer ponto no tempo, ou seja, não há nenhum período de retenção de dados fixo para o qual as alterações estão disponíveis.

* As alterações estão disponíveis em paralelo para todas as chaves de partição lógica de um contêiner Cosmos do Azure. Esse recurso permite que as alterações de contêineres grandes sejam processadas em paralelo por vários consumidores.

* Os aplicativos podem solicitar vários feeds de alteração no mesmo contêiner simultaneamente. ChangeFeedOptions. StartTime pode ser usado para fornecer um ponto de partida inicial. Por exemplo, para localizar o token de continuação correspondente a uma determinada hora do relógio. O ContinuationToken, se especificado, vence os valores de StartTime e StartFromBeginning. A precisão de ChangeFeedOptions. StartTime é de aproximadamente 5 segundos. 

## <a name="next-steps"></a>Passos seguintes

Agora você pode continuar para saber mais sobre o feed de alterações nos seguintes artigos:

* [Opções para ler o feed de alterações](read-change-feed.md)
* [Usando o feed de alterações com Azure Functions](change-feed-functions.md)
* [Usando o processador do feed de alterações](change-feed-processor.md)
