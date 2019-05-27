---
title: Otimizar o custo para implementações em várias regiões do Azure Cosmos DB
description: Este artigo explica como gerenciar custos de implementações em várias regiões do Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 478714f48782adb138f1ed803d53c81ec48f2efd
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967284"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Otimizar o custo de várias regiões do Azure Cosmos DB

Pode adicionar e remover regiões à sua conta do Cosmos do Azure em qualquer altura. O débito que configurar para vários bancos de dados do Cosmos do Azure e contentores está reservado em cada região associada à sua conta. Se o débito aprovisionado por hora, que é a soma de RU/s configurado em todos os bancos de dados e contentores para a sua conta do Cosmos do Azure é `T` e o número de regiões do Azure associado à conta de base de dados é `N`, em seguida, o total débito aprovisionado para a sua conta do Cosmos, para uma determinada hora é igual a:

1. `T x N RU/s` Se a sua conta do Cosmos do Azure está configurada com uma região de escrita única. 

1. `T x (N+1) RU/s` Se a sua conta do Cosmos do Azure está configurada com todas as regiões capazes de processar escritas. 

Débito aprovisionado com a região de escrita única custa US $0.008/ hora por 100 RU/s e débito aprovisionado com várias regiões graváveis custa US $0.016 / por hora por 100 RU/s. Para obter mais informações, veja o Azure Cosmos DB [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="costs-for-multiple-write-regions"></a>Custos de várias regiões de escrita

Num sistema de vários mestre, o net RUs disponível para escrever os aumentos de operações `N` vezes onde `N` é o número de regiões de escrita. Ao contrário de escritas de região única, a cada região agora é gravável e deve dar suporte a resolução de conflitos. A quantidade de carga de trabalho para escritores de aumentou. Do custo planear o ponto de vista, para efetuar `M` valor de RU/s de escritas em todo o mundo, será necessário aprovisionar M `RUs` num nível de contêiner ou base de dados. Em seguida, pode adicionar tantas regiões à medida que pretende e usá-los para escritas para realizar `M` valor de RU de escritas em todo o mundo. 

### <a name="example"></a>Exemplo

Considere tem um contentor em E.U.A. oeste aprovisionado com um débito de 10 mil RU/s e armazena 1 TB de dados deste mês. Suponhamos que adiciona três regiões - E.U.A. leste, Europa do Norte e Ásia Oriental, cada um com o mesmo armazenamento e débito e quiser ter a capacidade para escrever os contentores em todas as quatro regiões da sua aplicação distribuída globalmente. A fatura de mensal total (assumindo 31 dias) num mês é o seguinte:

|**Item**|**Utilização (mensal)**|**Taxa de**|**Custo mensal**|
|----|----|----|----|
|Conta de débito para o contentor em E.U.A. oeste (várias regiões de escrita) |10K RU/s * 24 * 31 |US $0.016 por 100 RU/s por hora |$1,190.40 |
|Conta de débito para 3 regiões adicionais – E.U. a leste, Europa do Norte e Ásia Oriental (várias regiões de escrita) |(3 + 1) * 10K RU/s * 24 * 31 |US $0.016 por 100 RU/s por hora |$4,761.60 |
|Conta de armazenamento para o contentor em E.U.A. Oeste |100 GB |US $ 0,25/GB |$25 |
|Conta de armazenamento para três regiões adicionais – E.U.A Leste, Europa do Norte e Ásia Oriental |3 * 1 TB |US $ 0,25/GB |$75 |
|**Total**|||**$6,052** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Melhorar a utilização de débito num por região base

Se tiver de utilização ineficaz, por exemplo, um ou mais subutilizadas ou superutilizadas regiões, pode eliminar os seguintes passos para melhorar a utilização de débito:  

1. Certifique-se de otimizar o débito aprovisionado (RUs) na região de escrita pela primeira vez e, em seguida, faça o máximo proveito do RUs em regiões de leitura com o feed de etc a região de leitura de alterações. 

2. Lê de várias regiões de escrita e escritas podem ser dimensionadas de todas as regiões associadas à conta do Cosmos do Azure. 

3. Monitorizar a atividade em suas regiões e poderia adicionar e remover regiões a pedido para dimensionar a sua leitura e escrita de débito.

## <a name="next-steps"></a>Passos Seguintes

Em seguida, pode avançar para obter mais informações sobre a otimização de custos no Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [entender a sua fatura do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de débito](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo de consultas](optimize-cost-queries.md)

