---
title: Criação de partições no Azure Cosmos DB
description: Descrição geral da criação de partições no Azure Cosmos DB.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: e88be8e7b94566ff94dd94a8679f8ade9d54c0b6
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762328"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Criação de partições no Azure Cosmos DB

O Azure Cosmos DB utiliza a criação de partições para dimensionar contentores individuais num banco de dados para satisfazer as necessidades de desempenho da sua aplicação. Na criação de partições, os itens num contentor são divididos em subconjuntos distintos chamados *partições lógicas*. Partições lógicas são formadas com base no valor de um *chave de partição* que está associado a cada item num contentor. Todos os itens numa partição lógica tem o mesmo valor de chave de partição.

Por exemplo, um contêiner contém itens. Cada item tem um valor exclusivo para o `UserID` propriedade. Se `UserID` serve como a partição da chave para os itens no contentor e existem 1.000 exclusivo `UserID` valores, 1000 partições lógicas são criadas para o contentor.

Além de uma chave de partição que determina a partição de lógica do item, cada item num contêiner tem um *ID de item* (exclusivos dentro de uma partição lógica). Combinando a chave de partição e o ID de item cria o item *índice*, que identifica exclusivamente o item.

[Escolher uma chave de partição](partitioning-overview.md#choose-partitionkey) é uma decisão importante que irão afetar o desempenho do aplicativo.

## <a name="managing-logical-partitions"></a>Gerenciamento de partições lógicas

O Azure Cosmos DB forma transparente e automática gerencia o posicionamento de partições lógicas em partições físicas com eficiência a satisfazer as necessidades de escalabilidade e desempenho do contentor. À medida que aumentam os requisitos de armazenamento e débito de uma aplicação, o Azure Cosmos DB move partições lógicas para distribuir automaticamente a carga por um maior número de servidores. 

O Azure Cosmos DB utiliza baseada em hash de criação de partições para distribuir partições lógicas entre partições físicas. O Azure Cosmos DB codifica o valor de chave de partição de um item. O resultado codificado determina a partição física. Em seguida, do Azure Cosmos DB aloca espaço de chave de partição principais hashes uniformemente entre as partições físicas.

As consultas que acedem a dados dentro de uma única partição lógica são mais económicas do que as consultas que acessam várias partições. Transações (em procedimentos armazenados ou disparadores) são permitidas apenas em relação a itens numa única partição lógica.

Para saber mais sobre como o Azure Cosmos DB gere partições, veja [partições lógicas](partition-data.md). (Ele não é necessário compreender os detalhes internos para criar ou executar as suas aplicações, mas adicionada aqui para um leitor curioso.)

## <a id="choose-partitionkey"></a>Escolher uma chave de partição

Segue-se uma boa documentação de orientação para escolher uma chave de partição:

* Uma única partição lógica tem um limite máximo de 10 GB de armazenamento.  

* Os contentores de Cosmos do Azure têm um débito mínimo de 400 unidades de pedido por segundo (RU/s). Pedidos para a mesma chave de partição não podem exceder a taxa de transferência que é atribuída a uma partição. Se solicitações excederem a taxa de transferência alocada, as solicitações são taxa limitado. Por isso, é importante escolher uma chave de partição não resulta na "pontos de acesso" na sua aplicação.

* Escolha uma chave de partição que tenha uma vasta gama de valores e padrões de acesso que são distribuídos uniformemente por partições lógicas. Isto ajuda a distribuir os dados e a atividade no seu contentor entre o conjunto de partições lógicas, para que os recursos de armazenamento e débito de dados podem ser distribuídos por partições lógicas.

* Escolha uma chave de partição que propaga uniformemente a carga de trabalho, todas as partições e de forma uniforme ao longo do tempo. À sua escolha da chave de partição deve equilibrar a necessidade de consultas de partição eficiente e transações face ao objectivo de distribuição de itens em várias partições para alcançar a escalabilidade.

* Candidatos para as chaves de partição podem incluir propriedades que são apresentados frequentemente como um filtro em suas consultas. Consultas podem ser encaminhadas com eficiência, incluindo a chave de partição no predicado de filtro.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [criação de partições e dimensionamento horizontal no Azure Cosmos DB](partition-data.md).
* Saiba mais sobre [débito aprovisionado no Azure Cosmos DB](request-units.md).
* Saiba mais sobre [distribuição global no Azure Cosmos DB](distribute-data-globally.md).
