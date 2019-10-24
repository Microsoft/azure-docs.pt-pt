---
title: Otimizando o custo de leituras e gravações no Azure Cosmos DB
description: Este artigo explica como reduzir Azure Cosmos DB custos ao executar operações de leitura e gravação nos dados.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 934853b80c6e6377923df4c2b5cce7b7d7d57d7c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754936"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Otimizar o custo de leituras e gravações no Azure Cosmos DB

Este artigo descreve como o custo necessário para ler e gravar dados de Azure Cosmos DB é calculado. As operações de leitura incluem operações Get em itens e operações de gravação incluem inserir, substituir, excluir e Upsert de itens.  

## <a name="cost-of-reads-and-writes"></a>Custo de leituras e gravações

Azure Cosmos DB garante desempenho previsível em termos de taxa de transferência e latência usando um modelo de taxa de transferência provisionado. A taxa de transferência provisionada é representada em termos de [unidades de solicitação](request-units.md) por segundo ou ru/s. Uma RU (unidade de solicitação) é uma abstração lógica sobre recursos de computação, como CPU, memória, e/s, etc., que são necessários para executar uma solicitação. A taxa de transferência provisionada (RUs) é reservada e dedicada ao seu contêiner ou banco de dados para fornecer taxa de transferência e latência previsíveis. A taxa de transferência provisionada permite que Azure Cosmos DB forneçam desempenho previsível e consistente, baixa latência garantida e alta disponibilidade em qualquer escala. As unidades de solicitação representam a moeda normalizada que simplifica o raciocínio sobre quantos recursos um aplicativo precisa. 

Você não precisa pensar em diferenciar unidades de solicitação entre leituras e gravações. O modelo de moeda unificada das unidades de solicitação cria eficiências para usar de forma intercambiável a mesma capacidade de taxa de transferência para leituras e gravações. A tabela a seguir mostra o custo de leituras e gravações em termos de RU/s para itens que são de 1 KB e 100 KB de tamanho.

|**Tamanho do item**  |**Custo de uma leitura** |**Custo de uma gravação**|
|---------|---------|---------|
|1 KB |1 RU |5 RUs |
|100 KB |10 RUs |RUs 50 |

Ler um item com 1 KB de tamanho custa um RU. A gravação de um item que é de 1 KB custa cinco RUs. Os custos de leitura e gravação são aplicáveis ao usar o [nível de consistência](consistency-levels.md)de sessão padrão.  As considerações sobre RUs incluem: tamanho do item, contagem de propriedades, consistência de dados, propriedades indexadas, indexação e padrões de consulta.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>Custo normalizado para leituras e gravações de 1 milhão

O provisionamento de 1.000 RU/s se traduz em 3,6 milhões RU/hora e custará $0.08 pela hora (nos EUA e na Europa). Para um item de 1 KB, você pode executar 3,6 milhões leituras ou 720.000 gravações, (esse valor é calculado como: `3.6 million RU / 5`) por hora com essa taxa de transferência provisionada. Normalizado para um milhão de leituras e gravações, o custo seria de $0.22 para 1 milhão leituras (esse valor é calculado como: $% 0,08/3,6 milhão) e $0.111 para gravações de 1 milhão (esse valor é calculado como: $% 0,08/0,72 milhão).

## <a name="number-of-regions-and-the-request-units-cost"></a>O número de regiões e o custo das unidades de solicitação

O custo de gravações é constante, independentemente do número de regiões associadas à conta do Azure Cosmos. Em outras palavras, uma gravação de 1 KB custará cinco RUs, independentemente do número de regiões associadas à conta. Há uma quantidade não trivial de recursos gastos na replicação, na aceitação e no processamento do tráfego de replicação em todas as regiões. Para obter detalhes sobre a otimização de custo de várias regiões, consulte [o artigo Otimizando o custo de contas de Cosmos de várias regiões](optimize-cost-regions.md) .

## <a name="optimize-the-cost-of-writes-and-reads"></a>Otimizar o custo de gravações e leituras

Ao executar operações de gravação, você deve provisionar capacidade suficiente para dar suporte ao número de gravações necessárias por segundo. Você pode aumentar a taxa de transferência provisionada usando o SDK, o portal, a CLI antes de executar as gravações e, em seguida, reduzir a taxa de transferência depois que as gravações forem concluídas. Sua taxa de transferência para o período de gravação é a taxa de transferência mínima necessária para os dados fornecidos, além da taxa de transferência necessária para inserir carga de trabalho, supondo que nenhuma outra carga de trabalho esteja em execução. 

Se você estiver executando outras cargas de trabalho simultaneamente, por exemplo, consulta/leitura/atualização/exclusão, deverá adicionar também as unidades de solicitação adicionais necessárias para essas operações. Se as operações de gravação forem limitadas por taxa, você poderá personalizar a política de repetição/retirada usando Azure Cosmos DB SDKs. Por exemplo, você pode aumentar a carga até que uma pequena taxa de solicitações receba uma taxa limitada. Se o limite de taxa ocorrer, o aplicativo cliente deverá fazer logoff em solicitações de limitação de taxa para o intervalo de repetição especificado. Antes de repetir as gravações, você deve ter uma quantidade mínima de intervalo de tempo entre as repetições. O suporte à política de repetição está incluído nos SDKs do SQL .NET, Java, Node. js e Python e todas as versões com suporte dos SDKs do .NET Core. 

Você também pode inserir dados em massa em Azure Cosmos DB ou copiar dados de qualquer armazenamento de dados de origem com suporte para Azure Cosmos DB usando [Azure data Factory](../data-factory/connector-azure-cosmos-db.md). Azure Data Factory integra-se nativamente com a API de Azure Cosmos DB em massa para fornecer o melhor desempenho ao gravar dados.

## <a name="next-steps"></a>Passos seguintes

Em seguida, você pode prosseguir para saber mais sobre a otimização de custos no Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre como [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre como [entender sua fatura de Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre como [otimizar o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre como [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre como [otimizar o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre como [otimizar o custo de contas do Azure Cosmos de várias regiões](optimize-cost-regions.md)
