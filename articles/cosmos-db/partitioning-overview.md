---
title: Particionamento no Azure Cosmos DB
description: Visão geral do particionamento no Azure Cosmos DB.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: e80e548ceae2149fe7061da42c71ee8b61f00a72
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717561"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Particionamento no Azure Cosmos DB

Azure Cosmos DB usa o particionamento para dimensionar contêineres individuais em um banco de dados para atender às necessidades de desempenho do seu aplicativo. No particionamento, os itens em um contêiner são divididos em subconjuntos distintos chamados *partições lógicas*. As partições lógicas são formadas com base no valor de uma *chave de partição* associada a cada item em um contêiner. Todos os itens em uma partição lógica têm o mesmo valor de chave de partição.

Por exemplo, um contêiner contém itens. Cada item tem um valor exclusivo para a `UserID` propriedade. Se `UserID` serve como a chave de partição para os itens no contêiner e há 1.000 valores exclusivos `UserID` , 1.000 partições lógicas são criadas para o contêiner.

Além de uma chave de partição que determina a partição lógica do item, cada item em um contêiner tem uma *ID de item* (exclusiva em uma partição lógica). A combinação da chave de partição e da ID do item cria o *índice*do item, que identifica exclusivamente o item.

[Escolher uma chave de partição](partitioning-overview.md#choose-partitionkey) é uma decisão importante que afetará o desempenho do aplicativo.

## <a name="managing-logical-partitions"></a>Gerenciando partições lógicas

Azure Cosmos DB gerencia de forma transparente e automática o posicionamento de partições lógicas em partições físicas para atender com eficiência às necessidades de escalabilidade e desempenho do contêiner. À medida que os requisitos de taxa de transferência e armazenamento de um aplicativo aumentam, Azure Cosmos DB move partições lógicas para espalhar automaticamente a carga em um número maior de servidores. 

O Azure Cosmos DB usa o particionamento baseado em hash para distribuir partições lógicas entre partições físicas. Azure Cosmos DB hash do valor de chave de partição de um item. O resultado com hash determina a partição física. Em seguida, Azure Cosmos DB aloca o espaço de chave dos hashes de chave de partição uniformemente entre as partições físicas.

As consultas que acessam dados em uma única partição lógica são mais econômicas do que as consultas que acessam várias partições. As transações (em procedimentos armazenados ou gatilhos) são permitidas somente em itens em uma única partição lógica.

Para saber mais sobre como Azure Cosmos DB gerencia partições, consulte [partições lógicas](partition-data.md). (Não é necessário entender os detalhes internos para criar ou executar seus aplicativos, mas adicionados aqui para um leitor curioso.)

## <a id="choose-partitionkey"></a>Escolhendo uma chave de partição

Veja a seguir uma boa orientação para escolher uma chave de partição:

* Uma única partição lógica tem um limite superior de 10 GB de armazenamento.  

* Os contêineres Cosmos do Azure têm uma taxa de transferência mínima de 400 unidades de solicitação por segundo (RU/s). Quando a taxa de transferência é provisionada em um banco de dados, o RUs mínimo por contêiner é de 100 unidades de solicitação por segundo (RU/s). As solicitações para a mesma chave de partição não podem exceder a taxa de transferência alocada para uma partição. Se as solicitações excederem a taxa de transferência alocada, as solicitações serão limitadas por taxa. Portanto, é importante escolher uma chave de partição que não resulte em "pontos de acesso" em seu aplicativo.

* Escolha uma chave de partição que tenha uma ampla gama de valores e padrões de acesso espalhados uniformemente entre partições lógicas. Isso ajuda a distribuir os dados e a atividade em seu contêiner pelo conjunto de partições lógicas, para que os recursos para armazenamento de dados e taxa de transferência possam ser distribuídos entre as partições lógicas.

* Escolha uma chave de partição que espalha a carga de trabalho uniformemente em todas as partições e uniformemente ao longo do tempo. Sua escolha de chave de partição deve equilibrar a necessidade de consultas e transações de partição eficientes em relação à meta de distribuição de itens em várias partições para obter escalabilidade.

* Os candidatos às chaves de partição podem incluir propriedades que aparecem com frequência como um filtro em suas consultas. As consultas podem ser roteadas com eficiência, incluindo a chave de partição no predicado de filtro.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [particionamento e dimensionamento horizontal em Azure Cosmos DB](partition-data.md).
* Saiba mais sobre a [taxa de transferência provisionada no Azure Cosmos DB](request-units.md).
* Saiba mais sobre a [distribuição global no Azure Cosmos DB](distribute-data-globally.md).
