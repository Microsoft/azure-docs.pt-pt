---
title: Otimizar o custo das implantações em várias regiões em Azure Cosmos DB
description: Este artigo explica como gerir os custos de implantações multi-regiões em Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: 010ca40f4f3aacd6353aecd150e944672cc09066
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097518"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Otimizar o custo de várias regiões no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Pode adicionar e remover regiões à sua conta Azure Cosmos a qualquer momento. A produção que configura para várias bases de dados e contentores da Azure Cosmos é reservada em cada região associada à sua conta. Se a produção provisida por hora, isto é, a soma de RU/s configurada em todas as bases de dados e contentores para a sua conta Azure Cosmos `T` é e o número de regiões Azure associadas à sua conta de base de dados é , `N` então o total proviscioso para a sua conta Cosmos, por uma hora é igual a `T x N RU/s` .

O débito aprovisionado numa única região de escrita custa 0,008 $/hora por 100 RU/s e o débito aprovisionado em várias regiões de escrita custa 0,016 $/hora por 100 RU/s. Para saber mais, consulte [a página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)da Azure Cosmos DB .

## <a name="costs-for-multiple-write-regions"></a>Custos para várias regiões de escrita

Num sistema de escrita multi-região, as RUs disponíveis para operações de escrita aumentam `N` os tempos em que `N` o número de regiões de escrita é o número de regiões de escrita. Ao contrário das regiões que escrevem, todas as regiões são atualmente conflituosas e apoiam a resolução de conflitos. Do ponto de vista do planeamento de custos, para executar `M` ru/s de escritas em todo o mundo, você precisará de providenciar M a `RUs` nível de um contentor ou base de dados. Em seguida, pode adicionar quantas regiões quiser e usá-las para escrever para executar `M` ru vale de escritas mundiais.

### <a name="example"></a>Exemplo

Considere que tem um contentor nos EUA ocidental configurado para escritas de uma região única, a provisionado com produção 10K RU/s e armazena 1 TB de dados este mês. Vamos supor que você adiciona uma região, Leste dos EUA, com o mesmo armazenamento e produção e você quer a capacidade de escrever para os recipientes em ambas as regiões a partir de sua app. A sua fatura mensal total (assumindo 31 dias) num mês é a seguinte:

|**Item**|**Utilização (mensal)**|**Rate** (Taxa)|**Custo Mensal**|
|----|----|----|----|
|Conta de produção de contentores nos EUA Ocidentais (regiões de escrita única) |10K RU/s * 24 horas * 31 dias |$0.008 por 100 RU/s por hora |$584.06 |
|Conta de produção de contentores em 2 regiões - Eua Ocidental & Leste dos EUA (várias regiões de escrita) |2 * 10K RU/s * 24 horas * 31 dias|$0,016 por 100 RU/s por hora |$2.336.26 |
|Conta de armazenamento para contentor no Oeste dos EUA |1 TB (ou 1,024 GB) |$0,25/GB |$256 |
|Conta de armazenamento para 2 regiões - West US & East US |2 * 1 TB (ou 3,072 GB) |$0,25/GB |$768 |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Melhorar a utilização da produção por região

Se tiver uma utilização ineficiente, por exemplo, uma ou mais regiões de leitura subutil utilizadas, pode tomar medidas para utilizar ao máximo as RUs nas regiões de leitura, utilizando o feed de mudança da região de leitura ou transferi-lo para outro secundário se for sobreutilizá-lo. Você precisará garantir que otimiza a produção (RUs) na região de escrita em primeiro lugar. As gravações custam mais do que as leituras, a menos que consultas muito grandes, para que manter a utilização até possa ser um desafio. No geral, monitorize a produção consumida nas suas regiões e adicione ou remova regiões a pedido para escalar a sua produção de leitura e escrita, fazendo com que compreenda o impacto da latência para quaisquer aplicações que sejam implementadas na mesma região.

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode proceder para saber mais sobre a otimização de custos na Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB da Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de produção](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](./optimize-cost-reads-writes.md)