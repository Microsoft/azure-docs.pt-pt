---
title: Trabalhar com o suporte de feed de mudança em Azure Cosmos DB
description: Utilize o suporte de feed de alteração Azure Cosmos DB para rastrear alterações em documentos, processamento baseado em eventos como gatilhos e manter caches e sistemas analíticos atualizados
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18, "seo-nov-2020"
ms.openlocfilehash: c6856a0cb70123f1a3570b611c81660a592fdc1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027811"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Feed de alterações no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis-except-table](includes/appliesto-all-apis-except-table.md)]

Alterar o feed em Azure Cosmos DB é um registo persistente de alterações num recipiente na ordem em que ocorrem. O suporte do feed de alterações no Azure Cosmos DB funciona através da escuta de alterações num contentor do Azure Cosmos. Em seguida, disponibiliza a lista ordenada dos documentos que foram alterados, pela ordem pela qual foram modificados. As alterações persistiu podem ser processadas de forma assíncronea e incremental, e a produção pode ser distribuída por um ou mais consumidores para processamento paralelo.

Saiba mais sobre [mudar padrões de design de feed.](change-feed-design-patterns.md)

## <a name="supported-apis-and-client-sdks"></a>APIs suportados e SDKs cliente

Esta funcionalidade é atualmente suportada pelas seguintes APIs DB da Azure Cosmos e SDKs cliente.

| **Motoristas de clientes** | **SQL API** | **A API de Azure Cosmos DB para Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API do Gremlin**|**API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Yes | Yes | Yes | Yes | No |
|Java|Yes|Yes|Yes|Yes|No|
|Python|Yes|Yes|Yes|Yes|No|
|Nó/JS|Yes|Yes|Yes|Yes|No|

## <a name="change-feed-and-different-operations"></a>Alterar feed e diferentes operações

Hoje, vê todas as inserções e atualizações no feed de alteração. Não é possível filtrar o feed de alteração para um tipo específico de funcionamento. Uma alternativa possível é adicionar um "marcador suave" no item para atualizações e filtro com base nisso ao processar itens no feed de alteração.

Atualmente, a alteração do feed não regista exclusões. Semelhante ao exemplo anterior, pode adicionar um marcador suave nos itens que estão a ser eliminados. Por exemplo, pode adicionar um atributo no item chamado "eliminado" e defini-lo como "verdadeiro" e definir um TTL no item, para que possa ser automaticamente eliminado. Pode ler o feed de alteração de itens históricos (a alteração mais recente correspondente ao item, não inclui as alterações intermédias), por exemplo, itens que foram adicionados há cinco anos. Pode ler o feed de alteração até à origem do seu recipiente, mas se um item for eliminado, será removido do feed de alteração.

### <a name="sort-order-of-items-in-change-feed"></a>Ordenar a ordem dos itens no feed de alteração

Os itens de alimentação de alteração vêm na ordem do seu tempo de modificação. Esta ordem de tipo é garantida por chave de partição lógica.

### <a name="consistency-level"></a>Nível de consistência

Enquanto consome o feed de mudança num nível de consistência eventual, pode haver eventos duplicados entre as operações subsequentes de leitura do feed de alteração (o último evento de uma operação de leitura aparece como o primeiro da seguinte).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Mude o feed nas contas da Azure Cosmos em várias regiões

Numa conta multi-região do Azure Cosmos, se uma região de escrita falhar, a alteração do feed funcionará em toda a operação de failover manual e será contígua.

### <a name="change-feed-and-time-to-live-ttl"></a>Alterar feed e Tempo para Viver (TTL)

Se uma propriedade TTL (Tempo para Viver) estiver definida num item para -1, o feed de alteração persistirá para sempre. Se os dados não forem eliminados, permanecerá no feed de alteração.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Mude o feed e _etag, _lsn ou _ts

O formato _etag é interno e não deve assumir dependência, porque pode mudar a qualquer momento. _ts é uma modificação ou uma estamp de tempo de criação. Pode usar _ts para comparação cronológica. _lsn é um ID de lote que é adicionado apenas para alterar feed; representa o ID de transação. Muitos itens podem ter a mesma _lsn. O ETag no FeedResponse é diferente do _etag que vê no item. _etag é um identificador interno e é utilizado para o controlo da conuncy. A propriedade _etag fala sobre a versão do item, enquanto a propriedade ETag é usada para sequenciar o feed.

## <a name="working-with-change-feed"></a>Trabalhar com o feed de mudança

Pode trabalhar com o feed de alteração utilizando as seguintes opções:

* [Utilização de feed de alteração com Funções Azure](change-feed-functions.md)
* [Utilização de feed de alteração com processador de feed de alteração](change-feed-processor.md) 

O feed de alteração está disponível para cada chave de partição lógica dentro do recipiente, e pode ser distribuído por um ou mais consumidores para processamento paralelo, como mostra a imagem abaixo.

:::image type="content" source="./media/change-feed/changefeedvisual.png" alt-text="Processamento distribuído do feed de alteração DB da Azure Cosmos" border="false":::

## <a name="features-of-change-feed"></a>Características do feed de mudança

* O feed de alteração é ativado por padrão para todas as contas Azure Cosmos.

* Pode utilizar a sua [produção a provisionada](request-units.md) para ler a partir do feed de alteração, tal como qualquer outra operação DB da Azure Cosmos, em qualquer uma das regiões associadas à sua base de dados Azure Cosmos.

* O feed de alteração inclui inserções e operações de atualização feitas em itens dentro do contentor. Pode capturar eliminações definindo uma bandeira de "soft-delete" dentro dos seus itens (por exemplo, documentos) no lugar das eliminações. Em alternativa, pode definir um período de validade finito para os seus itens com a [capacidade TTL](time-to-live.md). Por exemplo, 24 horas e use o valor dessa propriedade para capturar exclusões. Com esta solução, tem de processar as alterações num intervalo de tempo mais curto do que o período de validade do TTL.

* Cada alteração a um item aparece exatamente uma vez no feed de alteração, e os clientes devem gerir a lógica de checkpointing. Se quiser evitar a complexidade da gestão dos pontos de verificação, o processador de alimentação de alteração fornece uma verificação automática e semântica "pelo menos uma vez". Consulte [a utilização do feed de alteração com o processador change feed](change-feed-processor.md).

* Apenas a alteração mais recente para um determinado item está incluída no registo de alteração. As alterações intermédias podem não estar disponíveis.

* O feed de alteração é classificado pela ordem de modificação dentro de cada valor lógico da chave de partição. Não há ordem garantida em todos os valores-chave da partição.

* As alterações podem ser sincronizadas a partir de qualquer ponto no tempo, ou seja, não existe um período de retenção de dados fixo para o qual as alterações estão disponíveis.

* As alterações estão disponíveis em paralelo para todas as chaves de partição lógicas de um recipiente Azure Cosmos. Esta capacidade permite que as mudanças de grandes contentores sejam processadas paralelamente por vários consumidores.

* As aplicações podem solicitar várias alterações no mesmo recipiente simultaneamente. ChangeFeedOptions.StartTime pode ser usado para fornecer um ponto de partida inicial. Por exemplo, para encontrar o token de continuação correspondente a um determinado relógio. O ContinuationToken, se especificado, tem precedência sobre os valores StartTime e StartFromBeginning. A precisão de ChangeFeedOptions.StartTime é de ~5 segundos.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Alterar feed em APIs para Cassandra e MongoDB

A funcionalidade de feed de alteração é emergida como fluxo de mudança na API mongodb e consulta com predicado em Cassandra API. Para saber mais sobre os detalhes de implementação da MongoDB API, consulte os [streams Change no AZure Cosmos DB API para a MongoDB](mongodb-change-streams.md).

A Nativa Apache Cassandra fornece a captura de dados de mudança (CDC), um mecanismo para sinalizar tabelas específicas para arquivo, bem como rejeitar escritas para essas tabelas uma vez que um tamanho-on-disco configurável para o log CDC é alcançado. A funcionalidade de feed de mudança em Azure Cosmos DB API para Cassandra aumenta a capacidade de consultar as mudanças com predicado via CQL. Para saber mais sobre os detalhes da implementação, consulte [change feed na API API do Azure Cosmos para Cassandra.](cassandra-change-feed.md)

## <a name="next-steps"></a>Passos seguintes

Pode agora proceder para saber mais sobre a alteração do feed nos seguintes artigos:

* [Opções para ler alterar feed](read-change-feed.md)
* [Utilização de feed de alteração com Funções Azure](change-feed-functions.md)
* [Utilização do processador change feed](change-feed-processor.md)
