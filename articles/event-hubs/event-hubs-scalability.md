---
title: Escalabilidade, os Hubs de eventos do Azure | Documentos da Microsoft
description: Fornece informações sobre como dimensionar os Hubs de eventos do Azure.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 3eb20013a6b3afaddce10f2e4652add0edf22a9a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276785"
---
# <a name="scaling-with-event-hubs"></a>Dimensionar com os Hubs de eventos

Existem dois fatores que influenciam o dimensionamento com os Hubs de eventos.
*   Unidades de débito
*   Partições

## <a name="throughput-units"></a>Unidades de débito

A capacidade de débito do Event Hubs é controlada por *unidades de débito*. As unidades de débito são unidades de capacidade previamente compradas. Uma taxa de transferência única permite-lhe:

* Entrada: Até 1 MB por segundo ou 1000 eventos por segundo (o que acontecer primeiro).
* Saída: Até 2 MB por segundo ou 4096 eventos por segundo.

Além da capacidade das unidades de débito adquiridas, a entrada é limitada e é devolvida uma [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). A saída não produz exceções de limitação, mas continua a ser limitada à capacidade das unidades de débito adquiridas. Se receber exceções da taxa de publicação ou estiver à espera de ver uma saída superior, não se esqueça de verificar quantas unidades de débito adquiriu para o espaço de nomes. Pode gerir unidades de débito no **escala** painel dos espaços de nomes no [portal do Azure](https://portal.azure.com). Também pode gerir unidades de débito programaticamente usando a [APIs de Hubs de eventos](event-hubs-api-overview.md).

Unidades de débito são previamente adquiridas e são faturadas por hora. Assim que forem adquiridas, as unidades de débito são faturadas por um mínimo de uma hora. Débito até 20 unidades podem ser adquiridas para um espaço de nomes de Hubs de eventos e são partilhadas entre todos os hubs de eventos nesse espaço de nomes.

O **ampliação automática** funcionalidade dos Hubs de eventos de dimensionamento automaticamente do aumento do número de unidades de débito, para utilização de satisfazer as necessidades. Aumentar as unidades de débito impede cenários, no qual de limitação:

- As taxas de entrada de dados excederem as unidades de débito do conjunto.
- Taxas de pedidos de saída de dados excederem as unidades de débito do conjunto.

O serviço de Hubs de eventos aumenta o débito, quando a carga aumenta acima do limiar mínimo, sem quaisquer pedidos a falhar com erros de ServerBusy. 

Para obter mais informações sobre a funcionalidade de ampliação automática, consulte [Dimensionar automaticamente unidades de débito](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partições

As partições permitem-lhe dimensionamento para seu processamento a jusante. Por causa do modelo de consumidor particionado dos Hubs de eventos oferece com partições, pode aumentar horizontalmente ao processar seus eventos em simultâneo. Um Hub de eventos pode ter até 32 partições.

Recomendamos que equilibre as unidades de débito de 1:1 e as partições para alcançar a escala ideal. Uma única partição tem uma entrada e de saída até uma unidade de débito garantido. Embora possa ser capaz de alcançar um débito mais elevado numa partição, o desempenho não é garantido. É por isso é altamente recomendável que o número de partições num hub de eventos de ser maior que ou igual ao número de unidades de débito.

Tendo em conta o débito total que tencione necessidade, sabe o número de unidades de débito que necessita e o número mínimo de partições, mas o número de partições que deve ter? Escolha o número de partições com base em quer atingir o paralelismo a jusante, bem como as suas necessidades de débito futuras. Não existe nenhum custo associado para o número de partições que tem dentro de um Hub de eventos.

Para obter informações detalhadas sobre os preços dos Hubs de Eventos, veja [Preços de Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Passos Seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

- [Dimensionar automaticamente unidades de débito](event-hubs-auto-inflate.md)
- [Descrição geral de serviço de Hubs de eventos](event-hubs-what-is-event-hubs.md)
