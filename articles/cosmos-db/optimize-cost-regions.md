---
title: Otimize o custo para implantações em várias regiões no Azure Cosmos DB
description: Este artigo explica como gerenciar os custos de implantações de várias regiões no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rimman
ms.openlocfilehash: 233eab1fc49d7ce4cbb1e5b98b67eda9a64aa195
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667601"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Otimizar o custo de várias regiões no Azure Cosmos DB

Você pode adicionar e remover regiões para sua conta do Azure Cosmos a qualquer momento. A taxa de transferência que você configura para vários contêineres e bancos de dados Cosmos do Azure é reservada em cada região associada à sua conta. Se a produtividade provisionada por hora, que é a soma de ru/s configurada em todos os bancos de dados e contêineres para sua conta `T` do Azure cosmos é e o número de regiões do Azure associadas `N`à sua conta de banco de dados é, o total a taxa de transferência provisionada para sua conta do cosmos, por uma determinada hora, é igual a:

1. `T x N RU/s`Se sua conta do Azure Cosmos estiver configurada com uma única região de gravação. 

1. `T x (N+1) RU/s`Se sua conta do Azure Cosmos estiver configurada com todas as regiões capazes de processar gravações. 

A taxa de transferência provisionada com uma única região de gravação custa US $0.008/hora por 100 RU/s e taxa de transferência provisionada com várias regiões graváveis custam US $0.016/por hora por 100 RU/s. Para saber mais, consulte Azure Cosmos DB [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="costs-for-multiple-write-regions"></a>Custos para várias regiões de gravação

Em um sistema de vários mestres, a rede RUs disponível para operações de gravação `N` aumenta os `N` tempos em que é o número de regiões de gravação. Ao contrário das gravações de região única, todas as regiões agora são graváveis e devem dar suporte à resolução de conflitos. A quantidade de carga de trabalho para os gravadores aumentou. Do ponto de vista do planejamento de custos, para `M` executar ru/s de gravações em todo o mundo, você precisará `RUs` provisionar M em um nível de contêiner ou banco de dados. Em seguida, você pode adicionar quantas regiões desejar e usá-las para gravações para executar `M` ru de gravações mundiais. 

### <a name="example"></a>Exemplo

Considere que você tem um contêiner no oeste dos EUA provisionado com a taxa de transferência de 10K RU/s e armazena 1 TB de dados neste mês. Vamos supor que você adicione três regiões – leste dos EUA, Europa Setentrional e Ásia Oriental, cada uma com o mesmo armazenamento e taxa de transferência e deseja a capacidade de gravar nos contêineres em todas as quatro regiões de seu aplicativo distribuído globalmente. A sua conta mensal total (supondo 31 dias) em um mês é a seguinte:

|**Item**|**Uso (mensal)**|**Frequência**|**Custo mensal**|
|----|----|----|----|
|Fatura de taxa de transferência para o contêiner no oeste dos EUA (várias regiões de gravação) |10K RU/s * 24 * 31 |$0.16 por 100 RU/s por hora |$1190.40 |
|Fatura de taxa de transferência para três regiões adicionais – leste dos EUA, Europa Setentrional e Ásia Oriental (várias regiões de gravação) |(3 + 1) * 10K RU/s * 24 * 31 |$0.16 por 100 RU/s por hora |$4761.60 |
|Conta de armazenamento para o contentor em E.U.A. Oeste |1 TB (ou 1.024 GB) |US $0,25/GB |$256 |
|Conta de armazenamento para três regiões adicionais – E.U.A Leste, Europa do Norte e Ásia Oriental |3 * 1 TB (ou 3.072 GB) |US $0,25/GB |$768 |
|**Total**|||**$6976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Melhorar a utilização da produtividade de acordo com cada região

Se você tiver uma utilização ineficiente, por exemplo, uma ou mais regiões subutilizadas ou superutilizadas, você poderá executar as seguintes etapas para melhorar a utilização da taxa de transferência:  

1. Certifique-se de otimizar a produtividade provisionada (RUs) primeiro na região de gravação e, em seguida, faça o uso máximo do RUs em regiões de leitura usando o feed de alterações da região de leitura, etc. 

2. Várias leituras e gravações de regiões de gravação podem ser dimensionadas em todas as regiões associadas à conta do Azure Cosmos. 

3. Monitore a atividade em suas regiões e você pode adicionar e remover regiões sob demanda para dimensionar sua taxa de transferência de leitura e gravação.

## <a name="next-steps"></a>Passos Seguintes

Em seguida, você pode prosseguir para saber mais sobre a otimização de custos no Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre como [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre como [entender sua fatura de Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre como [otimizar o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre como [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre como [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre como [otimizar o custo de consultas](optimize-cost-queries.md)

