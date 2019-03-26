---
title: Dimensionar o débito no Azure Cosmos DB
description: Este artigo descreve como o Azure Cosmos DB dimensiona débito de forma elástica
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: b645fe210e7eeb073380dcadefead3e1b4d7ccc0
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407441"
---
# <a name="globally-scale-provisioned-throughput"></a>Dimensionamento global de débito aprovisionado 

No Azure Cosmos DB, o débito aprovisionado é representado como pedido unidades por segundo (RU/s ou a forma plural RUs). RUs avaliar o custo de leitura e escrita em relação a seu contentor do Cosmos como mostrado na imagem seguinte:

![Unidades de Pedido](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Pode aprovisionar RUs num contentor do Cosmos ou uma base de dados do Cosmos. RUs aprovisionadas num contentor são exclusivamente disponíveis para as operações executadas nesse contentor. RUs aprovisionadas numa base de dados são partilhados entre todos os contentores dentro dessa base de dados (exceto para os contentores que tenham exclusivamente atribuídas RUs).

Para dimensionar elasticamente o débito aprovisionado, pode aumentar ou diminuir o RU/s aprovisionada em qualquer altura. Para obter mais informações, consulte [débito aprovisionar procedimentos](set-throughput.md) e e dimensionar bases de dados e contentores de Cosmos. Para globalmente dimensionar o débito, pode adicionar ou remover regiões a partir da sua conta do Cosmos em qualquer altura. Para obter mais informações, consulte [Adicionar/remover regiões da sua conta de base de dados](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Associação de várias regiões com uma conta do Cosmos é importante em muitos cenários - para alcançar a baixa latência e [elevada disponibilidade](high-availability.md) em todo o mundo.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>como o débito aprovisionado é distribuído em várias regiões

Se aprovisionar *'R'* RUs num contêiner Cosmos (ou a base de dados) do Cosmos DB garante que *'R'* RUs estão disponíveis no *cada* região associada à sua conta do Cosmos. Sempre que adicionar uma nova região à sua conta do Cosmos DB automaticamente Aprovisiona *'R'* RUs na região recentemente adicionado. As operações executadas em relação a seu contentor do Cosmos são garantidas para obter *'R'* RUs em cada região. Não é possível atribuir seletivamente RUs numa região específica. O RUs aprovisionadas num contentor do Cosmos (ou da base de dados) é aprovisionado em todas as regiões à conta do Cosmos.

Supondo que um contentor do Cosmos está configurado com *'R'* RUs e existem *"n"* regiões associadas a conta do Cosmos, em seguida:

- Se a conta do Cosmos estiver configurada com uma região de escrita única, o total de RUs disponível globalmente no contentor = *R* x *N*.

- Se a conta do Cosmos estiver configurada com várias regiões de escrita, o total de RUs disponível globalmente no contentor = *R* x (*N*+ 1). Adicional *R* RUs são automaticamente aprovisionadas para conflitos de atualização de processo e o tráfego de entropia anti em todas as regiões.

Sua escolha de [modelo de consistência](consistency-levels.md) também afeta a taxa de transferência. Pode obter aproximadamente 2 x débito de leitura para os níveis de consistência mais flexíveis (por exemplo, *sessão*, *prefixo consistente* e *eventual* consistência) em comparação com níveis de consistência mais fortes (por exemplo, *estagnação limitada* ou *forte* consistência).

## <a name="next-steps"></a>Passos Seguintes

Em seguida pode saber como configurar o débito num contentor ou da base de dados:

* [Obter e definir a taxa de transferência para contentores e bases de dados](set-throughput.md) 

