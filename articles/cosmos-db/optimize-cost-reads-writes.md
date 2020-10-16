---
title: Otimizar o custo das leituras e dos escritos no Azure Cosmos DB
description: Este artigo explica como reduzir os custos da Azure Cosmos DB ao realizar operações de leitura e escrita nos dados.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.openlocfilehash: 38084bf30df2a597e7a7bc46ba4c52cf371c3c7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318254"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Otimizar leituras e escreve custos em Azure Cosmos DB

Este artigo descreve como o custo necessário para ler e escrever dados da Azure Cosmos DB é calculado. As operações de leitura incluem [leituras de pontos e consultas.](sql-query-getting-started.md) As operações de escrita incluem inserção, substituição, eliminação e redução de itens.  

## <a name="cost-of-reads-and-writes"></a>Custo das leituras e dos escritos

A Azure Cosmos DB garante um desempenho previsível em termos de produção e latência utilizando um modelo de produção provisionado. O valor previsto é representado em termos de [Unidades](request-units.md) de Pedido por segundo, ou RU/s. Uma Unidade de Pedido (RU) é uma abstração lógica sobre recursos computativos tais como CPU, memória, IO, etc. que são obrigados a realizar um pedido. A produção prevista (RUs) é reservada e dedicada ao seu contentor ou base de dados para fornecer produção e latência previsíveis. A produção a provisionada permite que a Azure Cosmos DB proporcione um desempenho previsível e consistente, baixa latência garantida e elevada disponibilidade em qualquer escala. As unidades de pedido representam a moeda normalizada que simplifica o raciocínio sobre quantos recursos uma aplicação necessita.

Não tens de pensar em diferenciar unidades de pedido entre leituras e escritos. O modelo de moeda unificada das unidades de pedido cria eficiências para utilizar intercambiavelmente a mesma capacidade de produção tanto para leituras como para escritas. A tabela a seguir mostra o custo das leituras e escritas de pontos em termos de RU/s para itens de tamanho de 1 KB e 100 KB.

|**Tamanho do item**  |**Custo de um ponto lido** |**Custo de uma escrita**|
|---------|---------|---------|
|1 KB |1 RU |5 RUs |
|100 KB |10 RUs |50 RUs |

Fazer um ponto ler para um item que é 1 KB em tamanho custa um RU. Escrever um artigo que é 1-KB custa cinco RUs. Os custos de leitura e escrita são aplicáveis quando se utiliza o [nível](consistency-levels.md)de consistência da sessão predefinido .  As considerações em torno das RUs incluem: tamanho do item, contagem de propriedades, consistência de dados, propriedades indexadas, indexação e padrões de consulta.

[As leituras de](sql-query-getting-started.md) pontos custam significativamente menos RU's do que consultas. As leituras de pontos, ao contrário das consultas, não precisam de usar o motor de consulta para aceder aos dados podem salvar ru's. A carga de consulta RU depende da complexidade da consulta e do número de itens que o motor de consulta precisava de carregar.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Otimizar o custo das escritas e leituras

Quando efetuar operações de escrita, deverá fornecer capacidade suficiente para suportar o número de escritos necessários por segundo. Pode aumentar a produção a provisionada utilizando SDK, portal, CLI antes de efetuar as gravações e, em seguida, reduzir a produção após a conclusão das escritas. A sua produção para o período de escrita é a produção mínima necessária para os dados dados, mais a produção necessária para inserir a carga de trabalho assumindo que não estão a funcionar outras cargas de trabalho.

Se estiver a executar outras cargas de trabalho simultaneamente, por exemplo, consulta/leitura/atualização/eliminação, deve adicionar também as unidades de pedido adicionais necessárias para essas operações. Se as operações de escrita forem limitadas à taxa, pode personalizar a política de retídula/backoff utilizando SDKs Azure Cosmos DB. Por exemplo, pode aumentar a carga até que uma pequena taxa de pedidos seja limitada. Se ocorrer um limite de taxa, o pedido do cliente deve recuar em pedidos de limitação de taxa para o intervalo de repetição especificado. Antes de voltar a tentar escrever, deve ter um intervalo mínimo de tempo entre as retrações. O suporte à política de recandidúsão está incluído nos SDKs .NET, Java, Node.js e Python e em todas as versões suportadas dos SDKs .NET Core.

Também pode inserir dados em massa na Azure Cosmos DB ou copiar dados de qualquer loja de dados de origem suportada para Azure Cosmos DB utilizando [a Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). A Azure Data Factory integra-se de forma nativa com a API APC AZURE Cosmos DB Bulk para proporcionar o melhor desempenho, quando escreve dados.

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode proceder para saber mais sobre a otimização de custos na Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB da Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de produção](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo das contas da Azure Cosmos em várias regiões](optimize-cost-regions.md)
