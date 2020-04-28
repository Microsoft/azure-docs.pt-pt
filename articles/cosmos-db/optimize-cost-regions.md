---
title: Otimizar o custo para implantações multi-regiões em Azure Cosmos DB
description: Este artigo explica como gerir os custos de implantações multi-regiões em Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: e0a24b52c12bce6a8e016a926dfa64a1e36a7cc6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72753308"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Otimizar o custo de várias regiões no Azure Cosmos DB

Pode adicionar e remover regiões à sua conta Azure Cosmos a qualquer momento. A entrada que configura para várias bases de dados e contentores do Azure Cosmos é reservada em cada região associada à sua conta. Se a produção prevista por hora, esta é a soma de RU/s configurada em todas as `T` bases de dados e contentores para a `N`sua conta Azure Cosmos e o número de regiões Azure associadas à sua conta de base de dados é, então o total de produção previsto para a sua conta Cosmos, por uma determinada hora é igual a:

1. `T x N RU/s`se a sua conta Azure Cosmos estiver configurada com uma única região de escrita. 

1. `T x (N+1) RU/s`se a sua conta Azure Cosmos estiver configurada com todas as regiões capazes de processar escreve. 

O débito aprovisionado numa única região de escrita custa 0,008 $/hora por 100 RU/s e o débito aprovisionado em várias regiões de escrita custa 0,016 $/hora por 100 RU/s. Para saber mais, consulte a página de [preços](https://azure.microsoft.com/pricing/details/cosmos-db/)do Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Custos para várias regiões de escrita

Num sistema multi-master, as RUs disponíveis líquidas para operações de escrita aumentam os tempos `N` em `N` que o número de regiões de escrita. Ao contrário dos escritos de uma região, todas as regiões são agora repreensíveis e devem apoiar a resolução de conflitos. A quantidade de carga de trabalho para os escritores aumentou. Do ponto de vista da `M` planeamento de custos, para realizar escritas `RUs` ru/s em todo o mundo, você precisará fornecer M a nível de recipiente ou base de dados. Em seguida, pode adicionar o maior número de regiões `M` que gostaria e usá-las para escrever para executar escritas em todo o mundo. 

### <a name="example"></a>Exemplo

Considere que tem um contentor no Oeste dos EUA aprovisionado com a entrada 10K RU/s e armazena 1 TB de dados este mês. Vamos supor que você adiciona três regiões - Leste dos EUA, Norte da Europa e Ásia Oriental, cada uma com o mesmo armazenamento e produção e você quer a capacidade de escrever para os recipientes em todas as quatro regiões a partir da sua app distribuída globalmente. A sua conta mensal total (assumindo 31 dias) num mês é a seguinte:

|**Item**|**Utilização (mensal)**|**Tarifa**|**Custo Mensal**|
|----|----|----|----|
|Fatura de entrada para contentor nos EUA Ocidentais (várias regiões de escrita) |10K RU/s * 24 * 31 |$0.016 por 100 RU/s por hora |$1.190,40 |
|Fatura de entrada para 3 regiões adicionais - Leste dos EUA, Norte da Europa e Ásia Oriental (múltiplas regiões de escrita) |(3 + 1) * 10K RU/s * 24 * 31 |$0.016 por 100 RU/s por hora |$4.761,60 |
|Conta de armazenamento de contentores no Oeste dos EUA |1 TB (ou 1,024 GB) |$0.25/GB |$256 |
|Conta de armazenamento para 3 regiões adicionais - Leste dos EUA, Norte da Europa e Ásia Oriental |3 * 1 TB (ou 3,072 GB) |$0.25/GB |$768 |
|**Total**|||**$6.976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Melhorar a utilização de resultados por região

Se tiver uma utilização ineficiente, por exemplo, uma ou mais regiões subutilizadas ou sobreutilizadas, pode tomar as seguintes medidas para melhorar a utilização da utilização da utilização da utilização da utilização da utilização da parte de entrada:  

1. Certifique-se de otimizar primeiro a entrada prevista (RUs) na região de escrita e, em seguida, fazer o máximo uso das RUs nas regiões de leitura, utilizando o feed de mudança da região de leitura, etc. 

2. Várias regiões de escrita lê em todas as regiões associadas à conta Azure Cosmos. 

3. Monitorize a atividade nas suas regiões e poderá adicionar e remover regiões a pedido de escalar a sua leitura e escrever a sua versão.

## <a name="next-steps"></a>Passos seguintes

Em seguida, poderá proceder a mais informações sobre a otimização de custos em Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimização do custo de entrada](optimize-cost-throughput.md)
* Saiba mais sobre [otimização do custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](optimize-cost-queries.md)

