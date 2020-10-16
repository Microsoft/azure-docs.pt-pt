---
title: Escalaability - Azure Event Hubs / Microsoft Docs
description: Este artigo fornece informações sobre como escalar os Hubs de Eventos Azure utilizando divisórias e unidades de produção.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4dacb24ace2332f590db54959cbf1f06694b982b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86521960"
---
# <a name="scaling-with-event-hubs"></a>Escalando com centros de eventos

Existem dois fatores que influenciam a escala com os Centros de Eventos.
*   Unidades de débito
*   Partições

## <a name="throughput-units"></a>Unidades de débito

A capacidade de débito do Event Hubs é controlada por *unidades de débito*. As unidades de débito são unidades de capacidade previamente compradas. Uma única produção permite-lhe:

* Ingress: Até 1 MB por segundo ou 1000 eventos por segundo (o que vier primeiro).
* Egress: Até 2 MB por segundo ou 4096 eventos por segundo.

Além da capacidade das unidades de débito adquiridas, a entrada é limitada e é devolvida uma [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). A saída não produz exceções de limitação, mas continua a ser limitada à capacidade das unidades de débito adquiridas. Se receber exceções da taxa de publicação ou estiver à espera de ver uma saída superior, não se esqueça de verificar quantas unidades de débito adquiriu para o espaço de nomes. Pode gerir as unidades de débito no painel **Dimensionamento** dos espaços de nomes no [portal do Azure](https://portal.azure.com). Também pode gerir unidades de produção programáticas utilizando as [APIs dos Centros de Eventos.](./event-hubs-samples.md)

As unidades de produção são pré-compradas e são cobradas por hora. Assim que forem adquiridas, as unidades de débito são faturadas por um mínimo de uma hora. Até 20 unidades de produção podem ser compradas para um espaço de nomes de Event Hubs e são partilhadas em todos os centros de eventos nesse espaço de nome.

A função **de insuflação automática** dos Centros de Eventos aumenta automaticamente aumentando o número de unidades de produção, para satisfazer as necessidades de utilização. O aumento das unidades de produção impede cenários de estrangulamento, nos quais:

- As taxas de entrada de dados excedem as unidades de produção definidas.
- As taxas de pedido de saída de dados excedem as unidades de produção definidas.

O serviço Event Hubs aumenta a produção quando a carga aumenta para além do limiar mínimo, sem que quaisquer pedidos falhem com erros do ServerBusy. 

Para obter mais informações sobre a função de insuflado automático, consulte [unidades de produção de escala automática .](event-hubs-auto-inflate.md)

## <a name="partitions"></a>Partições
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Chave de partição

Pode utilizar uma [chave de partição](event-hubs-programming-guide.md#partition-key) para mapear dados de eventos recebidos em partições específicas para a finalidade de organização de dados. A chave de partição é um valor fornecido pelo remetente transmitido para um hub de eventos. É processada através de uma função hash estática que cria a atribuição de partições. Se não especificar uma chave de partição ao publicar um evento, é utilizada uma atribuição round robin.

O publicador de eventos apenas tem conhecimento da respetiva chave de partição, não da partição onde os eventos são publicados. Este desacoplamento da chave e da partição faz com que o remetente não tenha necessidade de saber muito sobre o processamento a jusante. Uma identidade por dispositivo ou utilizador exclusivo faz com que uma chave de partição seja segura, mas outros atributos como a geografia também podem ser utilizados para agrupar os eventos relacionados numa única partição.


## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

- [Dimensionar automaticamente unidades de débito](event-hubs-auto-inflate.md)
- [Visão geral do serviço do Event Hubs](./event-hubs-about.md)
