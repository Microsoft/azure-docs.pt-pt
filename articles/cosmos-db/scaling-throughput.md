---
title: Dimensionando a taxa de transferência em Azure Cosmos DB
description: Este artigo descreve como Azure Cosmos DB escala de transferência de forma elástica
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 29a92f04a1d36004fa082bfafe2310f9e0e3e5c6
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467607"
---
# <a name="globally-scale-provisioned-throughput"></a>Dimensionamento global de débito aprovisionado 

Em Azure Cosmos DB, a taxa de transferência provisionada é representada como unidades de solicitação/segundo (RU/s ou o formato plural de RUs). RUs mede o custo de operações de leitura e gravação em seu contêiner Cosmos, conforme mostrado na imagem a seguir:

![Unidades de Pedido](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Você pode provisionar RUs em um contêiner Cosmos ou um banco de dados Cosmos. O RUs provisionado em um contêiner está exclusivamente disponível para as operações executadas nesse contêiner. O RUs provisionado em um banco de dados é compartilhado entre todos os contêineres dentro desse banco de dados (exceto para todos os contêineres com RUs atribuído exclusivamente).

Para o dimensionamento elástico da taxa de transferência provisionada, você pode aumentar ou diminuir as RU/s provisionadas a qualquer momento. Para obter mais informações, consulte Como provisionar a [taxa de transferência](set-throughput.md) e para dimensionar de forma elástica contêineres e bancos de dados do cosmos. Para uma taxa de transferência de dimensionamento global, você pode adicionar ou remover regiões da sua conta do cosmos a qualquer momento. Para obter mais informações, consulte [Adicionar/remover regiões de sua conta de banco de dados](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Associar várias regiões a uma conta do cosmos é importante em muitos cenários – para atingir baixa latência e [alta disponibilidade](high-availability.md) em todo o mundo.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Como a taxa de transferência provisionada é distribuída entre regiões

Se você provisionar RUs *' r '* em um contêiner Cosmos (ou banco de dados), o cosmos DB garantirá que o RUS *' r '* esteja disponível em *cada* região associada à sua conta do cosmos. Cada vez que você adiciona uma nova região à sua conta, Cosmos DB automaticamente provisiona RUs *' R '* na região recém-adicionada. As operações executadas em seu contêiner Cosmos têm a garantia de obter RUs *' R '* em cada região. Você não pode atribuir RUs seletivamente a uma região específica. O RUs provisionado em um contêiner Cosmos (ou banco de dados) é provisionado em todas as regiões associadas à sua conta do cosmos.

Supondo que um contêiner Cosmos esteja configurado com RUs *' R '* e que existam *' n'* regiões associadas à conta Cosmos, então:

- Se a conta Cosmos estiver configurada com uma única região de gravação, o RUs total disponível globalmente no contêiner = *R* x *N*.

- Se a conta Cosmos estiver configurada com várias regiões de gravação, o RUs total disponível globalmente no contêiner = *R* x (*N*+ 1). O RUs de *R* adicional é provisionado automaticamente para processar conflitos de atualização e tráfego de antientropia em todas as regiões.

Sua escolha de [modelo de consistência](consistency-levels.md) também afeta a taxa de transferência. Você pode obter aproximadamente 2x de taxa de transferência de leitura para os níveis de consistência mais relaxados (por exemplo, *sessão*, *prefixo consistente* e consistência *eventual* ) em comparação com os níveis de consistência mais fortes (por exemplo, desatualização *limitada* ou  *consistência forte* ).

## <a name="next-steps"></a>Passos Seguintes

Em seguida, você pode aprender a configurar a taxa de transferência em um contêiner ou banco de dados:

* [Obter e definir a taxa de transferência para contêineres e bancos de dados](set-throughput.md) 

