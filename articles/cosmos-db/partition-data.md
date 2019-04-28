---
title: Criação de partições e dimensionamento horizontal no Azure Cosmos DB
description: Saiba mais sobre a criação de partições como funciona no Azure Cosmos DB, como configurar a criação de partições e chaves de partição e como escolher a chave de partição correta para a sua aplicação.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: cf454d6504f0433d7ac7ca883982ae317b14f814
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60928864"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Criação de partições e dimensionamento horizontal no Azure Cosmos DB

Este artigo explica as partições físicas e lógicas no Azure Cosmos DB. Ele também aborda melhores práticas para dimensionamento e a criação de partições. 

## <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica consiste num conjunto de itens que tenham a mesma chave de partição. Por exemplo, num contentor onde todos os itens contêm uma `City` propriedade, pode usar `City` como a chave de partição para o contentor. Grupos de itens que tenham valores específicos para `City`, tal como `London`, `Paris`, e `NYC`, partições lógicas distintas de formulário. Não precisa se preocupar sobre como eliminar uma partição, quando os dados subjacentes são eliminados.

No Azure Cosmos DB, um contentor é a unidade fundamental de escalabilidade. Dados que são adicionados para o contentor e a taxa de transferência que for aprovisionado no contentor são automaticamente particionada (horizontalmente) num conjunto de partições lógicas. Dados e débito são divididos em partições com base na chave de partição que especificou para o contentor do Cosmos do Azure. Para obter mais informações, consulte [criar um contentor do Azure Cosmos](how-to-create-container.md).

Uma partição lógica também define o âmbito das transações da base de dados. Pode atualizar itens dentro de uma partição lógica com um [transação com o isolamento do instantâneo](database-transactions-optimistic-concurrency.md). Quando novos itens são adicionados a um contentor, as novas partições lógicas transparente são criadas pelo sistema.

## <a name="physical-partitions"></a>Partições físicas

Um contentor do Cosmos do Azure é dimensionado ao distribuir os dados e o débito num grande número de partições lógicas. Internamente, uma ou mais partições lógicas estão mapeadas para uma partição física que consiste num conjunto de réplicas, também conhecido como um [ *conjunto de réplicas*](global-dist-under-the-hood.md). Cada réplica definir anfitriões uma instância do motor de base de dados do Azure Cosmos DB. Um conjunto de réplicas torna os dados armazenados na partição física durável, de elevada disponibilidade e consistente. Uma partição física oferece suporte a quantidade máxima de unidades de armazenamento e a pedido (RUs). Cada réplica que constitui a partição física herda a quota de armazenamento da partição. Todas as réplicas de uma partição física coletivamente suportam a taxa de transferência que é atribuída para a partição física. 

A imagem seguinte mostra como lógica partições são mapeadas para as partições físicas que são distribuídas globalmente:

![Uma imagem que demonstra a criação de partições do Azure Cosmos DB](./media/partition-data/logical-partitions.png)

Débito aprovisionado para um contentor é dividido igualmente entre partições físicas. Uma estrutura de chave de partição que não a distribuir uniformemente os pedidos de débito pode criar partições "quentes". Partições muito ativas podem resultar na limitação de velocidade e no uso ineficiente do débito aprovisionado e custos superiores.

Ao contrário das partições lógicas, as partições físicas são uma implementação interna do sistema. Não é possível controlar o tamanho, a colocação ou a contagem de partições físicas e não é possível controlar o mapeamento entre partições lógicas e as partições físicas. No entanto, pode controlar o número de partições lógicas e a distribuição de dados, a carga de trabalho e débito [escolhendo a chave de partição lógica certo](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [escolher uma chave de partição](partitioning-overview.md#choose-partitionkey).
* Saiba mais sobre [débito aprovisionado no Azure Cosmos DB](request-units.md).
* Saiba mais sobre [distribuição global no Azure Cosmos DB](distribute-data-globally.md).
* Saiba como [débito de aprovisionar num contentor do Azure Cosmos](how-to-provision-container-throughput.md).
* Saiba como [débito de aprovisionar uma base de dados do Azure Cosmos](how-to-provision-database-throughput.md).
