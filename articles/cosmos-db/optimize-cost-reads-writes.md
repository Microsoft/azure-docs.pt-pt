---
title: Otimizar o custo das leituras e dos escritos no Azure Cosmos DB
description: Este artigo explica como reduzir os custos do Azure Cosmos DB ao realizar operações de leitura e escrita nos dados.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: bbdd1bedb7b9a9f00a0b65ccc4c108ba6fd2638c
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78398946"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Otimizar leituras e escreve custo saca-se em Azure Cosmos DB

Este artigo descreve como o custo necessário para ler e escrever dados da Azure Cosmos DB é calculado. As operações de leitura incluem obter operações em itens e as operações de escrita incluem inserir, substituir, excluir e melhorar os itens.  

## <a name="cost-of-reads-and-writes"></a>Custo de leituras e gravações

A Azure Cosmos DB garante um desempenho previsível em termos de entrada e latência utilizando um modelo de entrada provisionado. O serviço de entrada previsto está representado em termos de [Unidades](request-units.md) de Pedido por segundo, ou RU/s. Uma Unidade de Pedido (RU) é uma abstração lógica sobre recursos computacionais como CPU, memória, IO, etc. que são necessários para realizar um pedido. A entrada prevista (RUs) é reservada e dedicada ao seu recipiente ou base de dados para fornecer uma entrada e latência previsíveis. A entrada aprovisionada permite que o Azure Cosmos DB proporcione um desempenho previsível e consistente, baixa latência garantida e elevada disponibilidade em qualquer escala. As unidades de pedido representam a moeda normalizada que simplifica o raciocínio sobre quantos recursos uma aplicação necessita. 

Não é preciso pensar em diferenciar as unidades de pedido entre leituras e escritos. O modelo de moeda unificada das unidades de pedido cria eficiências para utilizar intercambiavelmente a mesma capacidade de entrada tanto para leituras como para escritos. A tabela seguinte mostra o custo das leituras e dos escritos em termos de RU/s para itens com 1 KB e 100 KB de tamanho.

|**Tamanho do artigo**  |**Custo de uma leitura** |**Custo de uma escrita**|
|---------|---------|---------|
|1 KB |1 RU |5 RUs |
|100 KB |10 RUs |50 RUs |

Ler um item de 1 KB em tamanho custa um RU. Escrever um item que é 1-KB custa cinco RUs. Os custos de leitura e escrita são aplicáveis quando utilizar o [nível de consistência](consistency-levels.md)da sessão predefinida .  As considerações em torno das RUs incluem: tamanho do item, contagem de propriedades, consistência de dados, propriedades indexadas, indexação e padrões de consulta.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Otimizar o custo das escritas e leituras

Quando executa operações de escrita, deve fornecer capacidade suficiente para suportar o número de escritos necessários por segundo. Pode aumentar a entrada prevista utilizando SDK, portal, CLI antes de realizar as escritas e, em seguida, reduzir a entrada após a conclusão das escritas. A sua entrada para o período de escrita é a entrada mínima necessária para os dados dado, além da entrada necessária para inserir carga de trabalho, assumindo que nenhuma outra carga de trabalho está em execução. 

Se estiver a executar outras cargas de trabalho simultaneamente, por exemplo, consulta/leitura/atualização/eliminação, deve adicionar as unidades de pedido adicionais necessárias para essas operações também. Se as operações de escrita forem limitadas à taxa, pode personalizar a política de retry/backoff utilizando Os SDKs DB Azure Cosmos. Por exemplo, pode aumentar a carga até que uma pequena taxa de pedidos seja limitada à taxa. Se ocorrer um limite de taxa, o pedido do cliente deve recuar nos pedidos de limitação de taxas para o intervalo de repetição especificado. Antes de tentar escrever, deve ter um intervalo mínimo de tempo entre tentativas. O suporte à política de retry está incluído em SQL .NET, Java, Node.js e Python SDKs e todas as versões suportadas dos SDKs .NET Core. 

Também pode inserir dados em massa no Azure Cosmos DB ou copiar dados de qualquer loja de dados de origem suportada para o Azure Cosmos DB utilizando a [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). A Azure Data Factory integra-se de forma nativa com a API a granel do Azure Cosmos DB para proporcionar o melhor desempenho, quando escreve dados.

## <a name="next-steps"></a>Passos seguintes

Em seguida, poderá proceder a mais informações sobre a otimização de custos em Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimização do custo de entrada](optimize-cost-throughput.md)
* Saiba mais sobre [otimização do custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo das contas multi-regiões da Azure Cosmos](optimize-cost-regions.md)
