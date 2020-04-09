---
title: Trabalhando com o suporte de feed de mudança em Azure Cosmos DB
description: Use o suporte de alimentação de alterações da Azure Cosmos DB para rastrear alterações nos documentos, processamento baseado em eventos como gatilhos, e manter caches e sistemas analíticos atualizados
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: e36e95aeb25c83ccd94f11e25bfe9f1b8f7bfdad
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984866"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Feed de alterações no Azure Cosmos DB

O suporte do feed de alterações no Azure Cosmos DB funciona através da escuta de alterações num contentor do Azure Cosmos. Em seguida, disponibiliza a lista ordenada dos documentos que foram alterados, pela ordem pela qual foram modificados. As alterações são preservadas, podem ser processadas de forma assíncrona e incremental e o resultado pode ser distribuído por um ou mais consumidores para processamento paralelo.

Saiba mais sobre [os padrões](change-feed-design-patterns.md)de design de feed de mudança.

## <a name="supported-apis-and-client-sdks"></a>APIs suportados e SDKs de clientes

Esta funcionalidade é atualmente suportada pelas seguintes APIs DB Da Azure Cosmos e sDKs de clientes.

| **Controladores do cliente** | **SQL API** | **API da Azure Cosmos DB para Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API do Gremlin**|**API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Sim | Sim | Sim | Sim | Não |
|Java|Sim|Sim|Sim|Sim|Não|
|Python|Sim|Sim|Sim|Sim|Não|
|Nó/JS|Sim|Sim|Sim|Sim|Não|

## <a name="change-feed-and-different-operations"></a>Alterar alimentos e diferentes operações

Hoje, você vê todas as inserções e atualizações no feed de mudança. Não é possível filtrar o feed de mudança para um tipo específico de operação. Uma alternativa possível é adicionar um "marcador suave" no item para atualizações e filtro com base nisso ao processar itens no feed de mudança.

Atualmente, a alteração do feed não regista apaga-se. À semelhança do exemplo anterior, pode adicionar um marcador suave nos itens que estão a ser eliminados. Por exemplo, pode adicionar um atributo no item chamado "eliminado" e defini-lo como "verdadeiro" e definir um TTL no item, para que possa ser automaticamente eliminado. Pode ler o feed de mudança para itens históricos (a alteração mais recente correspondente ao item, não inclui as alterações intermédias), por exemplo, itens que foram adicionados há cinco anos. Pode ler o feed de alteração desde a origem do seu recipiente, mas se um item for eliminado, este será removido do feed de alteração.

### <a name="sort-order-of-items-in-change-feed"></a>Ordenar ordem de itens no feed de mudança

Os itens de alimentação mudam na ordem do seu tempo de modificação. Esta ordem de classificação é garantida por chave de partição lógica.

### <a name="consistency-level"></a>Nível de consistência

Ao consumir o feed de mudança num nível de consistência eventual, pode haver duplicados eventos entre as operações subsequentes de leitura de feed de mudança (o último evento de uma operação de leitura aparece como o primeiro do próximo).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Alterar feed nas contas multi-regiões do Azure Cosmos

Numa conta multi-região do Azure Cosmos, se uma região de escrita falhar, a mudança de alimentos funcionará através da operação de failover manual e será contígua.

### <a name="change-feed-and-time-to-live-ttl"></a>Alterar feed e tempo para viver (TTL)

Se uma propriedade TTL (Time to Live) estiver definida num item para -1, a mudança de feed persistirá para sempre. Se os dados não forem eliminados, permanecerá no feed de alteração.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Alterar alimentação e _etag, _lsn ou _ts

O formato _etag é interno e não deve assumir a dependência dele, porque pode mudar a qualquer momento. _ts é uma modificação ou um carimbo temporal de criação. Pode usar _ts para comparação cronológica. _lsn é um id de lote que é adicionado apenas para a mudança de alimentos; representa o ID de transação. Muitos itens podem ter o mesmo _lsn. O ETag no FeedResponse é diferente do _etag que vê no item. _etag é um identificador interno e é utilizado para o controlo de moedas que conta sobre a versão do item, enquanto o ETag é utilizado para sequenciar o feed.

## <a name="working-with-change-feed"></a>Trabalhar com a mudança de alimentos

Pode trabalhar com o feed de mudança utilizando as seguintes opções:

* [Utilização de feed de mudança com funções azure](change-feed-functions.md)
* [Utilização de feed de mudança com processador de feed de mudança](change-feed-processor.md) 

O feed de alteração está disponível para cada chave de partição lógica dentro do recipiente, e pode ser distribuído por um ou mais consumidores para processamento paralelo, como mostra a imagem abaixo.

![Processamento distribuído de feed de mudança de DB Azure Cosmos](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Características do feed de mudança

* O feed de alteração é ativado por padrão para todas as contas da Azure Cosmos.

* Pode utilizar a sua [entrada aprovisionada](request-units.md) para ler a partir do feed de mudanças, como qualquer outra operação da Azure Cosmos DB, em qualquer uma das regiões associadas à sua base de dados Azure Cosmos.

* O feed de alteração inclui inserções e operações de atualização feitas em itens dentro do recipiente. Pode capturar exclusões colocando uma bandeira de "soft-delete" dentro dos seus itens (por exemplo, documentos) em vez de exclusões. Em alternativa, pode definir um período de validade finito para os seus itens com a [capacidade TTL](time-to-live.md). Por exemplo, 24 horas e usar o valor dessa propriedade para capturar eliminações. Com esta solução, tem de processar as alterações num intervalo de tempo mais curto do que o período de validade do TTL.

* Cada alteração para um item aparece exatamente uma vez no feed de mudança, e os clientes devem gerir a lógica de checkpointing. Se pretender evitar a complexidade da gestão dos pontos de verificação, o processador de alimentação de mudanças fornece um checkpoint automático e uma semântica "pelo menos uma vez". Consulte [a utilização do feed de mudança com o processador de alimentação de mudanças](change-feed-processor.md).

* Apenas a alteração mais recente para um determinado item está incluída no registo de alterações. Podem não estar disponíveis alterações intermédias.

* O feed de alteração é classificado pela ordem de modificação dentro de cada valor-chave de divisória lógica. Não há ordem garantida entre os valores-chave da partilha.

* As alterações podem ser sincronizadas a partir de qualquer ponto no tempo, ou seja, não existe um período fixo de retenção de dados para o qual existem alterações disponíveis.

* As alterações estão disponíveis em paralelo para todas as chaves de partição lógica de um recipiente Azure Cosmos. Esta capacidade permite que as mudanças de grandes contentores sejam processadas paralelamente por vários consumidores.

* As aplicações podem solicitar múltiplas alterações no mesmo recipiente simultaneamente. ChangeFeedOptions.StartTime pode ser usado para fornecer um ponto de partida inicial. Por exemplo, para encontrar o símbolo de continuação correspondente a um determinado relógio. O ContinuationToken, se especificado, tem precedência sobre os valores StartTime e StartFromBeginning. A precisão de ChangeFeedOptions.StartTime é ~5 segs.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Alterar feed em APIs para Cassandra e MongoDB

A funcionalidade de alimentação de mudanças é surgida como fluxo de mudança em MongoDB API e Consulta com predicado em Cassandra API. Para saber mais sobre os detalhes de implementação da MongoDB API, consulte os [streams Change no Azure Cosmos DB API para MongoDB](mongodb-change-streams.md).

A Nativa Apache Cassandra fornece a captura de dados de mudança (CDC), um mecanismo para sinalizar tabelas específicas para o arquivo, bem como rejeitar escritos para essas tabelas uma vez que um tamanho configurável no disco para o log CDC é alcançado. A funcionalidade de feed de mudança em Azure Cosmos DB API para Cassandra aumenta a capacidade de consultar as mudanças com predicado via CQL. Para saber mais sobre os detalhes da implementação, consulte [change feed no Azure Cosmos DB API para Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Passos seguintes

Pode agora proceder a mais informações sobre a mudança de alimentos nos seguintes artigos:

* [Opções para ler feed de mudança](read-change-feed.md)
* [Utilização de feed de mudança com funções azure](change-feed-functions.md)
* [Utilização do processador de feed de mudança](change-feed-processor.md)
