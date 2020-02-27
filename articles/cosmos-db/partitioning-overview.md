---
title: Partição em Azure Cosmos DB
description: Aprenda sobre a partilha em Azure Cosmos DB, as melhores práticas ao escolher uma chave de partição, e como gerir divisórias lógicas
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621868"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partição em Azure Cosmos DB

A Azure Cosmos DB utiliza a partilha para escalar recipientes individuais numa base de dados para atender às necessidades de desempenho da sua aplicação. Na partilha, os itens de um recipiente são divididos em subconjuntos distintos *chamados divisórias lógicas*. As divisórias lógicas são formadas com base no valor de uma chave de *partição* que está associada a cada item num recipiente. Todos os itens numa divisória lógica têm o mesmo valor-chave de partição.

Por exemplo, um recipiente contém itens. Cada item tem um valor único para a propriedade `UserID`. Se `UserID` servir como chave de partição para os itens no recipiente e houver 1.000 valores únicos de `UserID`, 1.000 divisórias lógicas são criadas para o recipiente.

Além de uma chave de partição que determina a partição lógica do item, cada item num recipiente tem um ID de *item* (único dentro de uma divisória lógica). Combinar a chave de partição e o ID do item cria o *índice*do item, que identifica exclusivamente o item.

[Escolher uma chave de partição](partitioning-overview.md#choose-partitionkey) é uma decisão importante que irá afetar o desempenho da sua aplicação.

## <a name="managing-logical-partitions"></a>Gestão de divisórias lógicas

A Azure Cosmos DB gere de forma transparente e automática a colocação de divisórias lógicas em divisórias físicas para satisfazer eficientemente as necessidades de escalabilidade e desempenho do recipiente. À medida que os requisitos de entrada e armazenamento de uma aplicação aumentam, o Azure Cosmos DB move divisórias lógicas para espalhar automaticamente a carga através de um maior número de servidores. 

O Azure Cosmos DB usa divisórias baseadas em hash para espalhar divisórias lógicas através de divisórias físicas. Azure Cosmos DB hashes o valor chave da partição de um item. O resultado hashed determina a partição física. Em seguida, o Azure Cosmos DB atribui o espaço chave da chave de partição hashes uniformemente através das divisórias físicas.

As consultas que acedem a dados dentro de uma única divisória lógica são mais rentáveis do que consultas que acedem a várias divisórias. As transações (em procedimentos ou gatilhos armazenados) só são permitidas contra itens numa única partição lógica.

Para saber mais sobre como o Azure Cosmos DB gere as divisórias, consulte [divisórias lógicas.](partition-data.md) (Não é necessário compreender os detalhes internos para construir ou executar as suas aplicações, mas adicionado aqui para um leitor curioso.)

## <a id="choose-partitionkey"></a>Escolher uma chave de partição

Segue-se uma boa orientação para a escolha de uma chave de partição:

* Uma única partição lógica tem um limite superior de 20 GB de armazenamento.  

* Os contentores Azure Cosmos têm uma entrada mínima de 400 unidades de pedido por segundo (RU/s). Quando a entrada é aprovisionada numa base de dados, as RUs mínimas por recipiente são 100 unidades de pedido por segundo (RU/s). Os pedidos para a mesma chave de partição não podem exceder a entrada atribuída a uma partição. Se os pedidos excederem a entrada atribuída, os pedidos são limitados à taxa. Então, é importante escolher uma chave de partição que não resulte em "pontos quentes" dentro da sua aplicação.

* Escolha uma chave de partição que tenha uma ampla gama de valores e padrões de acesso que estão uniformemente espalhados por divisórias lógicas. Isto ajuda a espalhar os dados e a atividade no seu recipiente através do conjunto de divisórias lógicas, de modo a que os recursos para armazenamento e entrada de dados possam ser distribuídos pelas divisórias lógicas.

* Escolha uma chave de partição que espalhe a carga de trabalho uniformemente em todas as divisórias e uniformemente ao longo do tempo. A sua escolha de chave de partição deve equilibrar a necessidade de consultas e transações de partição eficientes contra o objetivo de distribuir itens através de várias divisórias para alcançar a escalabilidade.

* Os candidatos a chaves de partição podem incluir propriedades que aparecem frequentemente como filtro nas suas consultas. As consultas podem ser eficientemente encaminhadas, incluindo a chave de partição no predicado do filtro.

## <a name="next-steps"></a>Passos seguintes

* Aprenda sobre [divisórias e escalas horizontais em Azure Cosmos DB](partition-data.md).
* Saiba mais sobre [a provisão disponibilizada em Azure Cosmos DB](request-units.md).
* Conheça a [distribuição global em Azure Cosmos DB.](distribute-data-globally.md)
