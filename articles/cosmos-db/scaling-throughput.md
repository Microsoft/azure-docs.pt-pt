---
title: Salcação de entrada em Azure Cosmos DB
description: Este artigo descreve como o Azure Cosmos DB escala a entrada em diferentes regiões onde a conta Azure Cosmos é aprovisionada.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873510"
---
# <a name="globally-scale-provisioned-throughput"></a>Dimensionamento global de débito aprovisionado 

Em Azure Cosmos DB, a provisão é representada como unidades de pedido/segundo (RU/s ou a forma plural RUs). As RUs medem o custo das operações de leitura e escrita contra o seu contentor Cosmos, como mostra a seguinte imagem:

![Unidades de Pedido](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Você pode fornecer RUs em um recipiente Cosmos ou uma base de dados Cosmos. As RUs aprovisionadas num contentor estão exclusivamente disponíveis para as operações realizadas nesse contentor. As RUs disponibilizadas numa base de dados são partilhadas entre todos os contentores dessa base de dados (exceto quaisquer contentores com RUs exclusivamente atribuídos).

Para uma escala elástica prevista, pode aumentar ou diminuir o RU/s aqualquer momento. Para mais informações, consulte [como fornecer a entrada](set-throughput.md) e para escalar elástico contentores e bases de dados cosmos. Para uma produção global de escala, pode adicionar ou remover regiões da sua conta Cosmos a qualquer momento. Para mais informações, consulte [Adicionar/remover regiões da sua conta](how-to-manage-database-account.md#addremove-regions-from-your-database-account)de base de dados . Associar várias regiões a uma conta Cosmos é importante em muitos cenários - para alcançar baixa latência e [alta disponibilidade](high-availability.md) em todo o mundo.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Como a entrada é distribuída por regiões

Se fornecer *RUs 'R'* num contentor Cosmos (ou base de dados), a Cosmos DB garante que as RUs *'R'* estão disponíveis em *cada* região associada à sua conta Cosmos. Cada vez que adiciona uma nova região à sua conta, cosmos DB provisões automáticas *de* RUs na região recém-adicionada. As operações realizadas contra o seu contentor Cosmos são garantidas para obter *RUs 'R'* em cada região. Não se pode atribuir rUs seletivamente a uma região específica. As RUs aprovisionadas num contentor Cosmos (ou base de dados) estão aprovisionadas em todas as regiões associadas à sua conta Cosmos.

Assumindo que um contentor Cosmos está configurado com RUs *'R'* e existem regiões *'N'* associadas à conta Cosmos, então:

- Se a conta Cosmos estiver configurada com uma única região de escrita, o total de RUs disponíveis globalmente no recipiente = *R* x *N*.

- Se a conta Cosmos estiver configurada com várias regiões de escrita, o total de RUs disponíveis globalmente no recipiente = *R* x (*N*+1). As *RUs* adicionais são automaticamente previstas para processar conflitos de atualização e tráfego anti-entropia em todas as regiões.

A sua escolha de modelo de [consistência](consistency-levels.md) também afeta a entrada. Pode obter aproximadamente 2x de leitura de entrada para os níveis de consistência mais descontraídos (por exemplo, *sessão,* *prefixo consistente* e *eventual* consistência) em comparação com níveis de consistência mais fortes (por exemplo, *estagnação limitada* ou *forte* consistência).

## <a name="next-steps"></a>Passos seguintes

Em seguida, poderá aprender a configurar a entrada num recipiente ou base de dados:

* [Obtenha e coloque a entrada para recipientes e bases de dados](set-throughput.md) 

