---
title: Otimizar o custo das implantações em várias regiões em Azure Cosmos DB
description: Este artigo explica como gerir os custos de implantações multi-regiões em Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 8d98c9a7e58f08d9ad63183805cd6cd0d2ab3b3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570176"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Otimizar o custo de várias regiões no Azure Cosmos DB

Pode adicionar e remover regiões à sua conta Azure Cosmos a qualquer momento. A produção que configura para várias bases de dados e contentores da Azure Cosmos é reservada em cada região associada à sua conta. Se a produção provisida por hora, isto é, a soma de RU/s configurada em todas as bases de dados e contentores para a sua conta Azure Cosmos `T` é e o número de regiões Azure associadas à sua conta de base de dados `N` é, então o total proviscioso para a sua conta Cosmos, por uma hora é igual a:

1. `T x N RU/s` se a sua conta Azure Cosmos estiver configurada com uma única região de escrita. 

1. `T x (N+1) RU/s` se a sua conta Azure Cosmos estiver configurada com todas as regiões capazes de processar escritas. 

O débito aprovisionado numa única região de escrita custa 0,008 $/hora por 100 RU/s e o débito aprovisionado em várias regiões de escrita custa 0,016 $/hora por 100 RU/s. Para saber mais, consulte [a página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)da Azure Cosmos DB .

## <a name="costs-for-multiple-write-regions"></a>Custos para várias regiões de escrita

Num sistema de escrita multi-região, as RUs disponíveis para operações de escrita aumentam `N` os tempos em que `N` o número de regiões de escrita é o número de regiões de escrita. Ao contrário das regiões que escrevem, todas as regiões são atualmente conflituosas e devem apoiar a resolução de conflitos. A quantidade de carga de trabalho para os escritores aumentou. Do ponto de vista do planeamento de custos, para executar `M` ru/s de escritas em todo o mundo, você precisará de providenciar M a `RUs` nível de um contentor ou base de dados. Em seguida, pode adicionar quantas regiões quiser e usá-las para escrever para executar `M` ru vale de escritas mundiais. 

### <a name="example"></a>Exemplo

Considere que tem um contentor nos EUA ocidental abasteçado com produção 10K RU/s e armazena 1 TB de dados este mês. Vamos supor que você adiciona três regiões - Eua Leste, Norte da Europa e Ásia Oriental, cada uma com o mesmo armazenamento e produção e você quer a capacidade de escrever para os recipientes em todas as quatro regiões a partir da sua app distribuída globalmente. A sua fatura mensal total (assumindo 31 dias) num mês é a seguinte:

|**Item**|**Utilização (mensal)**|**Rate** (Taxa)|**Custo Mensal**|
|----|----|----|----|
|Conta de produção de contentores nos EUA Ocidentais (várias regiões de escrita) |10K RU/s * 24 * 31 |$0,016 por 100 RU/s por hora |$1.190,40 |
|Conta de produção para 3 regiões adicionais - Leste dos EUA, Norte da Europa e Ásia Oriental (várias regiões de escrita) |(3 + 1) * 10K RU/s * 24 * 31 |$0,016 por 100 RU/s por hora |$4.761,60 |
|Conta de armazenamento para contentor no Oeste dos EUA |1 TB (ou 1,024 GB) |$0,25/GB |$256 |
|Conta de armazenamento para 3 regiões adicionais - Leste dos EUA, Norte da Europa e Ásia Oriental |3 * 1 TB (ou 3,072 GB) |$0,25/GB |$768 |
|**Total**|||**$6.976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Melhorar a utilização da produção por região

Se tiver uma utilização ineficiente, por exemplo, uma ou mais regiões subutilídas ou sobreutilizá-la, pode tomar as seguintes medidas para melhorar a utilização da produção:  

1. Certifique-se de que otimiza a produção (RUs) na região de escrita primeiro e, em seguida, faça o máximo uso das RUs nas regiões de leitura utilizando o feed de mudança da região de leitura, etc. 

2. Várias regiões de escrita lêem e escrevem podem ser dimensionadas em todas as regiões associadas à conta de Azure Cosmos. 

3. Monitorize a atividade nas suas regiões e poderá adicionar e remover regiões a pedido para escalar a sua leitura e escrever.

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode proceder para saber mais sobre a otimização de custos na Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB da Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de produção](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](optimize-cost-queries.md)

