---
title: Service Bus, preços e faturação | Documentos da Microsoft
description: Descrição geral da estrutura de preços do Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: 859add6c9310cf5f18ed7090c8e93d4896b59a0b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433387"
---
# <a name="service-bus-pricing-and-billing"></a>Service Bus, preços e faturação

O Azure Service Bus está disponível em Standard e [Premium](service-bus-premium-messaging.md) escalões. Pode escolher um escalão de serviço para cada espaço de nomes de serviço do Service Bus que criou e esta seleção do escalão de mensagens em fila aplica-se em todas as entidades criadas dentro desse namespace.

> [!NOTE]
> Para obter informações detalhadas sobre os preços atuais do Service Bus, consulte a [página de preços do Azure Service Bus](https://azure.microsoft.com/pricing/details/service-bus/)e o [FAQ do Service Bus](service-bus-faq.md#pricing).
>
>

O Service Bus utiliza os seguintes medidores de 2 para filas e tópicos/subscrições:

1. **Operações de mensagens**: Definido como chamadas de API em relação a pontos finais de serviço de fila ou tópico/subscrição. Este medidor substitui as mensagens enviadas ou recebidas como a unidade primária de utilização cobrar para filas e tópicos/subscrições.
2. **Ligações mediadas**: Definido como o número máximo de ligações persistentes aberto relativamente a filas, tópicos ou subscrições durante um período de amostragem de uma hora determinada. Este medidor aplica-se apenas no escalão Standard, na qual pode abrir ligações adicionais (anteriormente, eram limitadas a 100 por fila/tópico/subscrição ligações) de uma taxa nominal por conexão.

O **padrão** apresenta o escalão de preços escalonados para operações executadas com filas e tópicos/subscrições, resultando em descontos com base no volume de até 80% com os mais altos níveis de utilização. Também existe uma cobrança de base de escalão Standard de US $10 por mês, o que lhe permitem realizar operações de até 12,5 milhões por mês sem custos adicionais.

O **Premium** escalão fornece isolamento de recursos na camada de CPU e memória, para que cada carga de trabalho do cliente seja executado de forma isolada. Este contentor de recursos é designado por *unidade de mensagens*. A cada espaço de nomes premium é atribuído, pelo menos, uma unidade de mensagens. Pode comprar 1, 2 ou 4 unidades de mensagens para cada espaço de nomes Premium do Service Bus. Uma única carga de trabalho ou entidade pode abranger várias unidades de mensagens e o número de unidades de mensagens pode ser alterado à vontade, embora a faturação seja realizada com base em tarifas diárias ou de 24 horas. O resultado é um desempenho previsível e repetível da sua solução com base no Service Bus. Este desempenho não só é mais previsível e repetível, como também mais rápido.

> [!NOTE]
> Tópicos e subscrições só estão disponíveis no Standard ou Premium escalões; de preço o escalão básico oferece suporte a apenas filas.

O custo de base do escalão Standard é cobrado apenas uma vez por mês por subscrição do Azure. Isso significa que, depois de criar um espaço de nomes do Service Bus de escalão Standard individual, pode criar tantas adicionais espaços de nomes Standard que pretender na mesma subscrição do Azure, sem incorrer em custos base adicionais.

O [preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) tabela resume as diferenças funcionais entre os escalões Standard e Premium.

## <a name="messaging-operations"></a>Operações de mensagens

Filas e tópicos/subscrições são cobradas por "operação", não por mensagem. Uma operação refere-se para qualquer chamada à API feita relativamente de um ponto de extremidade de serviço de fila ou tópico/subscrição. Isto inclui operações de gestão, envio/receção e estado de sessão.

| Tipo de Operação | Descrição |
| --- | --- |
| Gestão |Crie, ler, atualizar, eliminar (CRUD) relativamente a filas ou tópicos/subscrições. |
| Mensagens |Enviar e receber mensagens com filas ou tópicos/subscrições. |
| Estado da sessão |Obter ou definir o estado da sessão numa fila ou tópico/subscrição. |

Para obter detalhes de custo, veja os preços indicados na [preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) página.

## <a name="brokered-connections"></a>Ligações mediadas

*Ligações mediadas* acomodar os padrões de utilização que envolvem um grande número de "persistentemente ligados" remetentes/destinatários relativamente a filas, tópicos ou subscrições. Conectados de forma permanente remetentes/destinatários são aqueles que se ligam através de AMQP ou HTTP com um diferente de zero recebem o tempo limite (por exemplo, HTTP consulta longa). Os remetentes HTTP e os recetores com um tempo limite imediato não geram ligações mediadas.

Para quotas de ligação e outros limites de serviço, consulte a [quotas do Service Bus](service-bus-quotas.md) artigo. Para obter mais informações sobre as ligações mediadas, consulte a [FAQ](#faq) seção mais adiante neste artigo.

O escalão Standard remove o limite de ligações mediadas por espaço de nomes e contagens de utilização de agregação de ligações mediadas por subscrição do Azure. Para obter mais informações, consulte a [ligações mediadas](https://azure.microsoft.com/pricing/details/service-bus/) tabela.

> [!NOTE]
> 1000 ligações mediadas estão incluídas no escalão mensagens padrão (através do custo base) e podem ser partilhadas entre todas as filas, tópicos e subscrições na subscrição do Azure associada.
>
>

<br />

> [!NOTE]
> A faturação baseia-se no número máximo de ligações simultâneas e é rateada por hora com base em 730 horas por mês.
>
>

### <a name="premium-tier"></a>Escalão Premium

As ligações mediadas não são cobradas no escalão Premium.

## <a name="faq"></a>FAQ

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>O que são ligações mediadas e como é que sou cobrado por eles?

Uma ligação mediada é definida de uma das seguintes formas:

1. Uma ligação AMQP de um cliente para uma fila do Service Bus ou tópico/subscrição.
2. Uma chamada HTTP para receber uma mensagem de um tópico ou fila do Service Bus com um valor de tempo limite superior a zero.

Custos do Service Bus para o número máximo de ligações mediadas simultâneas que excedam a quantidade incluída (1000 no escalão Standard). Os picos são medidos numa base horária, calculados dividindo pelas 730 horas por mês e somados ao longo do período de faturação mensal. A quantidade incluída (1000 ligações mediadas por mês) é aplicada no final do período de faturação relativamente à soma dos picos calculados pro rata à hora.

Por exemplo:

1. Cada 10 000 dispositivos se liga através de uma única ligação AMQP e recebe comandos de um tópico do Service Bus. Os dispositivos enviam eventos de telemetria para um Hub de eventos. Se todos os dispositivos se ligarem durante 12 horas por dia, aplicam as seguintes cobranças de ligação (acrescidos de quaisquer outros custos do Service Bus tópico): 10.000 ligações * 12 horas * 30 dias 730 = 4,931 de ligações mediadas. Depois do montante mensal de 1000 ligações mediadas, ser--ão cobradas 4000 ligações mediadas, à taxa de US $0.03 por ligação mediada, para um total de US $120.
2. 10.000 dispositivos recebem mensagens de uma fila do Service Bus por HTTP, especificando um tempo limite de diferente de zero. Se todos os dispositivos se ligarem durante 12 horas por dia, verá os seguintes custos de ligação (acrescidos de quaisquer outros custos do Service Bus): 10.000 ligações de receção HTTP * 12 horas por dia * 30 dias / 730 horas = 4,931 de ligações mediadas.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Aplicam-se custos de ligações mediadas a filas e tópicos/subscrições?

Sim. Não há custos de ligação para o envio de eventos por HTTP, independentemente do número de sistemas ou dispositivos de envio. A receção de eventos por HTTP com um tempo limite superior a zero, por vezes denominada "consulta longa", gera custos de ligações mediadas. As ligações AMQP geram custos de ligações mediadas independentemente de as ligações estarem a ser utilizadas para envio ou receção. As primeiras 1000 ligações mediadas em todos os espaços de nomes padrão numa subscrição do Azure estão incluídas sem custos extra (além do custo base). Uma vez que este número permitido é suficiente para abranger muitos cenários de mensagens serviço a serviço, custos de ligações mediadas geralmente só se tornam relevantes se planeia utilizar consulta longa de AMQP ou HTTP com um grande número de clientes; Por exemplo, para obter uma transmissão de eventos mais eficiente ou para permitir a comunicação bidirecional com muitos dispositivos ou instâncias da aplicação.

## <a name="next-steps"></a>Passos Seguintes

* Para obter detalhes completos sobre os preços do Service Bus, consulte a [página de preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
* Consulte a [FAQ do Service Bus](service-bus-faq.md#pricing) para alguns FAQ comuns sobre o Service bus, preços e faturação.

[Azure portal]: https://portal.azure.com
