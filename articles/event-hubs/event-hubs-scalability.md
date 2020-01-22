---
title: Escalabilidade-hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como dimensionar os hubs de eventos do Azure usando partições e unidades de produtividade.
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
ms.openlocfilehash: 2b36faef8c39a8e9b02a056576ae7f5a77b1f6bf
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76309528"
---
# <a name="scaling-with-event-hubs"></a>Dimensionamento com hubs de eventos

Há dois fatores que influenciam o dimensionamento com os hubs de eventos.
*   Unidades de débito
*   Partições

## <a name="throughput-units"></a>Unidades de débito

A capacidade de débito do Event Hubs é controlada por *unidades de débito*. As unidades de débito são unidades de capacidade previamente compradas. Uma única taxa de transferência permite que você:

* Entrada: Até 1 MB por segundo ou 1000 eventos por segundo (o que acontecer primeiro).
* Saída: Até 2 MB por segundo ou 4096 eventos por segundo.

Além da capacidade das unidades de débito adquiridas, a entrada é limitada e é devolvida uma [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). A saída não produz exceções de limitação, mas continua a ser limitada à capacidade das unidades de débito adquiridas. Se receber exceções da taxa de publicação ou estiver à espera de ver uma saída superior, não se esqueça de verificar quantas unidades de débito adquiriu para o espaço de nomes. Pode gerir unidades de débito no **escala** painel dos espaços de nomes no [portal do Azure](https://portal.azure.com). Também pode gerir unidades de débito programaticamente usando a [APIs de Hubs de eventos](event-hubs-api-overview.md).

Unidades de débito são previamente adquiridas e são faturadas por hora. Assim que forem adquiridas, as unidades de débito são faturadas por um mínimo de uma hora. Débito até 20 unidades podem ser adquiridas para um espaço de nomes de Hubs de eventos e são partilhadas entre todos os hubs de eventos nesse espaço de nomes.

O **ampliação automática** funcionalidade dos Hubs de eventos de dimensionamento automaticamente do aumento do número de unidades de débito, para utilização de satisfazer as necessidades. Aumentar as unidades de débito impede cenários, no qual de limitação:

- As taxas de entrada de dados excederem as unidades de débito do conjunto.
- Taxas de pedidos de saída de dados excederem as unidades de débito do conjunto.

O serviço de Hubs de eventos aumenta o débito, quando a carga aumenta acima do limiar mínimo, sem quaisquer pedidos a falhar com erros de ServerBusy. 

Para obter mais informações sobre o recurso de inflar automaticamente, consulte [dimensionar automaticamente unidades de produtividade](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partições
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Chave de partição

Pode utilizar uma [chave de partição](event-hubs-programming-guide.md#partition-key) para mapear dados de eventos recebidos em partições específicas para a finalidade de organização de dados. A chave de partição é um valor fornecido pelo remetente transmitido para um hub de eventos. É processada através de uma função hash estática que cria a atribuição de partições. Se não especificar uma chave de partição ao publicar um evento, é utilizada uma atribuição round robin.

O publicador de eventos apenas tem conhecimento da respetiva chave de partição, não da partição onde os eventos são publicados. Este desacoplamento da chave e da partição faz com que o remetente não tenha necessidade de saber muito sobre o processamento a jusante. Uma identidade por dispositivo ou utilizador exclusivo faz com que uma chave de partição seja segura, mas outros atributos como a geografia também podem ser utilizados para agrupar os eventos relacionados numa única partição.


## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

- [Dimensionar automaticamente unidades de débito](event-hubs-auto-inflate.md)
- [Descrição geral de serviço de Hubs de eventos](event-hubs-what-is-event-hubs.md)
