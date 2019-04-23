---
title: Solicitar unidades e o débito no Azure Cosmos DB
description: Saiba mais sobre como especificar e estimar os requisitos da unidade de pedido no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 72ec8332a3363d5336fb84b3390d5e44ced0b2e5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798968"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades de pedido no Azure Cosmos DB

Com o Azure Cosmos DB, paga a taxa de transferência, aprovisionamento e o armazenamento que consumir numa base horária. Taxa de transferência tem de ser aprovisionada para garantir que os recursos de sistema suficientes estão disponíveis para sua base de dados do Cosmos do Azure em todos os momentos. Tem recursos suficientes para atingir ou exceder os [SLAs do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

O Azure Cosmos DB suporta várias APIs, como SQL, MongoDB, Cassandra, Gremlin e tabela. Cada API tem seu próprio conjunto de operações de base de dados. Estas variam de operações de ponto de simple lê e escreve consultas complexas. Cada operação de base de dados consome recursos do sistema com base na complexidade da operação. 

O custo de todas as operações de base de dados é normalizado pelo Azure Cosmos DB e é expressa por *unidades de pedido* (ou RUs, para abreviar). Pode pensar RUs por segundo, como a moeda de débito. RU por segundo é a moeda de uma taxa. Ele abstrai os recursos do sistema, tais como CPU, memória e IOPS, que são necessárias para realizar as operações de base de dados suportadas pelo Azure Cosmos DB. 

O custo para ler um 1 KB é item 1 unidade de pedido (ou 1 RU). Todas as outras operações de base de dados da mesma forma são atribuídas um custo com o RUs. Não importa qual API que utilizar para interagir com o contentor do Cosmos do Azure, os custos são sempre medidos pela RUs. Se a operação de base de dados é uma gravação, custos de leitura ou de consulta, são sempre medidos em RUs.

A imagem seguinte mostra a idéia de alto nível de RUs:

![Operações de base de dados consumam unidades de pedido](./media/request-units/request-units.png)

Para gerir e planear a capacidade, o Azure Cosmos DB garante que o número de RUs para uma operação de base de dados especificada ao longo de um determinado conjunto de dados é determinístico. Pode examinar o cabeçalho de resposta para controlar o número de RUs consumidos por qualquer operação de base de dados. Quando compreender o [fatores que afetam os custos de RU](request-units.md#request-unit-considerations) e requisitos de débito da sua aplicação, pode executar a aplicação com eficiência de custos.

Aprovisionar o número de RUs para a sua aplicação numa base por segundo em incrementos de 100 RU por segundo. Para dimensionar o débito aprovisionado para a sua aplicação, pode aumentar ou diminuir o número de Urs em qualquer altura. Pode dimensionar em incrementos ou diminui de 100 RUs. Pode fazer as alterações por meio de programação ou através do portal do Azure. É cobrado à hora.

Pode aprovisionar o débito em duas granularidades distintos: 

* **Contentores**: Para obter mais informações, consulte [débito de aprovisionar num contentor do Azure Cosmos](how-to-provision-container-throughput.md).
* **Bases de dados**: Para obter mais informações, consulte [débito de aprovisionar uma base de dados do Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considerações de unidade de pedido

Embora a estimar o número de RUs por segundo para aprovisionar, considere os seguintes fatores:

* **Tamanho do item**: À medida que aumenta o tamanho de um item, também aumenta o número de RUs consumidos para ler ou escrever o item.

* **Indexação do item**: Por predefinição, cada item é indexado automaticamente. Menos RUs são consumidos se optar por não indexar alguns dos seus itens num contentor.

* **Contagem de propriedade do item**: Partindo do princípio de que a indexação de padrão é em todas as propriedades, o número de RUs consumidas para escrever que um item aumenta à medida que os aumentos de contagem de propriedade do item.

* **Propriedades indexadas**: Uma política de índice em cada contentor determina quais propriedades são indexadas por predefinição. Para reduzir o consumo de RU para operações de escrita, limite o número de propriedades indexadas.

* **Consistência dos dados**: Os níveis de consistência forte e estagnação limitada consumam RUs aproximadamente duas vezes mais durante a realização de operações de leitura quando comparada com a que de outro Relaxada níveis de consistência.

* **Padrões de consulta**: A complexidade de uma consulta afeta RUs quantos são consumidos para uma operação. Fatores que afetam o custo das operações de consulta: 
    
    - O número de resultados da consulta
    - O número de predicados
    - A natureza dos predicados
    - O número de funções definidas pelo utilizador
    - O tamanho dos dados de origem
    - O tamanho do conjunto de resultados
    - Projeções

  O Azure Cosmos DB garante que a mesma consulta nos mesmos dados custa sempre o mesmo número de RUs em execuções repetidas.

* **Utilização do script**: Tal como acontece com consultas, procedimentos armazenados e acionadores consumam RUs com base na complexidade das operações que são executadas. Ao desenvolver seu aplicativo, Inspecione o [cabeçalho do pedido de cobrança](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) para compreender melhor quanta capacidade de RU consome de cada operação.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre como [débito de aprovisionar em bancos de dados e contentores do Azure Cosmos](set-throughput.md).
* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba mais sobre como [globalmente dimensionar o débito aprovisionado](scaling-throughput.md).
* Saiba como [débito de aprovisionar num contentor do Azure Cosmos](how-to-provision-container-throughput.md).
* Saiba como [débito de aprovisionar uma base de dados do Azure Cosmos](how-to-provision-database-throughput.md).
* Saiba como [encontrar as cobranças de unidades de pedido para uma operação](find-request-unit-charge.md).
* Saiba como [otimizar o débito aprovisionado de custo no Azure Cosmos DB](optimize-cost-throughput.md).
* Saiba como [otimizar leituras e gravações de custos no Azure Cosmos DB](optimize-cost-reads-writes.md).
* Saiba como [otimizar o custo de consulta no Azure Cosmos DB](optimize-cost-queries.md).
