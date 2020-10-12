---
title: Produção de escala em Azure Cosmos DB
description: Este artigo descreve como a Azure Cosmos DB escala a produção em diferentes regiões onde a conta Azure Cosmos é abastada.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 94b5b3d2ab1f576f87ead23b389252ec96a20f11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397356"
---
# <a name="how-does-azure-cosmos-db-globally-scale-the-provisioned-throughput"></a>Como é que a Azure Cosmos DB escala globalmente a produção a provisionada?

Em Azure Cosmos DB, a produção a provisionada é representada como unidades de pedido/segundo (RU/s ou rus forma plural). As RUs medem o custo das operações de leitura e escrita contra o seu contentor Cosmos, como mostra a seguinte imagem:

:::image type="content" source="./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png" alt-text="Unidades de Pedido" border="false":::

Você pode providenciar RUs em um recipiente Cosmos ou uma base de dados Cosmos. As RUs afetuadas num contentor estão exclusivamente disponíveis para as operações efetuadas nesse contentor. As RUs previstas numa base de dados são partilhadas entre todos os contentores nessa base de dados (exceto em contentores com RUs exclusivamente atribuídos).

Para dimensionamento elástico provisor, pode aumentar ou diminuir o R/S a qualquer momento. Para obter mais informações, consulte [como obter a produção](set-throughput.md) e dimensionar elasticamente os contentores e bases de dados da Cosmos. Para uma produção global de escala, pode adicionar ou remover regiões da sua conta Cosmos a qualquer momento. Para obter mais informações, consulte [Regiões de adicionar/remover da sua conta de base de dados.](how-to-manage-database-account.md#addremove-regions-from-your-database-account) Associar várias regiões com uma conta Cosmos é importante em muitos cenários - para alcançar baixa latência e [alta disponibilidade](high-availability.md) em todo o mundo.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Como a produção a provisionada é distribuída por regiões

Se você *fornecer RUs 'R'* num recipiente Cosmos (ou base de dados), a Cosmos DB garante que as RUs *'R'* estão disponíveis em *cada* região associada à sua conta Cosmos. Cada vez que adiciona uma nova região à sua conta, a Cosmos DB fornece automaticamente *R'RUs* na região recém-adicionada. As operações realizadas contra o seu contentor Cosmos são garantidas para obter *RUs 'R'* em cada região. Não se pode atribuir RUs seletivamente a uma região específica. As RUs a provisionadas num contentor cosmos (ou base de dados) são a provisionadas em todas as regiões associadas à sua conta Cosmos.

Assumindo que um recipiente Cosmos está configurado com *RUs 'R'* e existem regiões *'N'* associadas à conta cosmos, então:

- Se a conta Cosmos estiver configurada com uma única região de escrita, o total de RUs disponíveis globalmente no recipiente = *R* x *N*.

- Se a conta Cosmos estiver configurada com múltiplas regiões de escrita, o total de RUs disponíveis globalmente no recipiente = *R* x *(N*+1). As *RUs* adicionais são automaticamente a provisionadas para processar a atualização de conflitos e tráfego anti-entropia em todas as regiões.

A sua escolha de modelo de [consistência](consistency-levels.md) também afeta a produção. Pode obter aproximadamente 2x de produção de leitura para os níveis de consistência mais descontraídos (por exemplo, *sessão,* *prefixo consistente* e *consistência eventual)* em comparação com níveis de consistência mais fortes (por exemplo, *estagnação limitada* ou *forte* consistência).

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode aprender a configurar a produção num contentor ou base de dados:

* [Obter e definir o rendimento para contentores e bases de dados](set-throughput.md) 

