---
title: Escalabilidade - Hubs de Eventos Azure [ Microsoft Docs
description: Este artigo fornece informações sobre como escalar os Hubs de Eventos Azure utilizando divisórias e unidades de produção.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79280953"
---
# <a name="scaling-with-event-hubs"></a>Escalacom Hubs de Eventos

Há dois fatores que influenciam a escalação com os Centros de Eventos.
*   Unidades de débito
*   Partições

## <a name="throughput-units"></a>Unidades de débito

A capacidade de débito do Event Hubs é controlada por *unidades de débito*. As unidades de débito são unidades de capacidade previamente compradas. Uma única entrada permite::

* Ingress: Até 1 MB por segundo ou 1000 eventos por segundo (o que vier em primeiro lugar).
* Egress: Até 2 MB por segundo ou 4096 eventos por segundo.

Além da capacidade das unidades de débito adquiridas, a entrada é limitada e é devolvida uma [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). A saída não produz exceções de limitação, mas continua a ser limitada à capacidade das unidades de débito adquiridas. Se receber exceções da taxa de publicação ou estiver à espera de ver uma saída superior, não se esqueça de verificar quantas unidades de débito adquiriu para o espaço de nomes. Pode gerir as unidades de débito no painel **Dimensionamento** dos espaços de nomes no [portal do Azure](https://portal.azure.com). Também pode gerir as unidades de produção programáticamente utilizando as APIs do [Event Hubs](event-hubs-api-overview.md).

As unidades de entrada são pré-compradas e são faturadas por hora. Assim que forem adquiridas, as unidades de débito são faturadas por um mínimo de uma hora. Até 20 unidades de entrada podem ser compradas para um espaço de nome do Event Hubs e são partilhadas em todos os centros de eventos desse espaço de nome.

A função **de auto-insuflar** os Centros de Eventos aumenta automaticamente aumentando o número de unidades de produção, para satisfazer as necessidades de utilização. O aumento das unidades de entrada impede cenários de estrangulamento, nos quais:

- As taxas de ingresso de dados excedem as unidades de entrada definidas.
- As taxas de pedido de saída de dados excedem as unidades de entrada definidas.

O serviço Event Hubs aumenta a produção quando a carga aumenta para além do limiar mínimo, sem que quaisquer pedidos falhem com erros do ServerBusy. 

Para obter mais informações sobre a função de inflação automática, consulte [automaticamente as unidades](event-hubs-auto-inflate.md)de produção .

## <a name="partitions"></a>Partições
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Chave de partição

Pode utilizar uma [chave de partição](event-hubs-programming-guide.md#partition-key) para mapear dados de eventos recebidos em partições específicas para a finalidade de organização de dados. A chave de partição é um valor fornecido pelo remetente transmitido para um hub de eventos. É processada através de uma função hash estática que cria a atribuição de partições. Se não especificar uma chave de partição ao publicar um evento, é utilizada uma atribuição round robin.

O publicador de eventos apenas tem conhecimento da respetiva chave de partição, não da partição onde os eventos são publicados. Este desacoplamento da chave e da partição faz com que o remetente não tenha necessidade de saber muito sobre o processamento a jusante. Uma identidade por dispositivo ou utilizador exclusivo faz com que uma chave de partição seja segura, mas outros atributos como a geografia também podem ser utilizados para agrupar os eventos relacionados numa única partição.


## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

- [Dimensionar automaticamente unidades de débito](event-hubs-auto-inflate.md)
- [Visão geral do serviço De Evento Hubs](event-hubs-what-is-event-hubs.md)
