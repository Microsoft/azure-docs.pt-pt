---
title: Capacidade de uma instância de Gestão API Azure [ Microsoft Docs
description: Este artigo explica qual é a métrica da capacidade e como tomar decisões informadas sobre a escala de uma instância de Gestão de API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.custom: fasttrack-edit
ms.openlocfilehash: b6d949b50be348e72cedfc3710383308b04de106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336014"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Capacidade de uma instância da Gestão de API do Azure

**A capacidade** é a métrica mais importante do [Monitor Azure](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) para tomar decisões informadas sobre a possibilidade de escalar uma instância de Gestão API para acomodar mais carga. A sua construção é complexa e impõe determinado comportamento.

Este artigo explica qual é a **capacidade** e como se comporta. Mostra como aceder às métricas de **capacidade** no portal Azure e sugere quando considerar a escala ou a atualização da sua instância de Gestão API.

> [!IMPORTANT]
> Este artigo discute como pode monitorizar e escalar a sua instância de Gestão API Azure com base na sua métrica de capacidade. No entanto, é igualmente importante compreender o que acontece quando uma instância individual de Gestão de API *atingiu* efetivamente a sua capacidade. A Azure API Management não aplicará qualquer estrangulamento ao nível do serviço para evitar uma sobrecarga física das ocorrências. Quando uma instância atinge a sua capacidade física, comportar-se-á semelhante a qualquer servidor web sobrecarregado que não seja capaz de processar pedidos de entrada: a latência aumentará, as ligações serão abandonadas, ocorrerão erros de timeout, etc. Isto significa que os clientes da API devem estar preparados para lidar com esta possibilidade semelhante à de qualquer outro serviço externo (por exemplo, aplicando políticas de retenção).

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, deve ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Um caso DaPIM. Para mais informações, consulte Criar uma instância de [Gestão API Azure.](get-started-create-service-instance.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>O que é capacidade

![Métrica de capacidade](./media/api-management-capacity/capacity-ingredients.png)

**A capacidade** é um indicador de carga numa instância de Gestão API. Reflete o uso de recursos (CPU, memória) e comprimentos de fila de rede. CpU e uso de memória revelam consumo de recursos por:

+ Serviços de aviões de dados da API Management, tais como o processamento de pedidos, que podem incluir o reencaminhamento de pedidos ou a execução de uma política.
+ Serviços de planos de gestão de gestão da API, tais como ações de gestão aplicadas através do Portal Azure ou ARM, ou carga proveniente do portal de [desenvolvimento.](api-management-howto-developer-portal.md)
+ Processos de sistema operativo selecionados, incluindo processos que envolvem custos de apertos de mão TLS em novas ligações.

A **capacidade** total é uma média dos seus próprios valores de cada unidade de uma instância de Gestão API.

Embora a métrica de **capacidade** seja projetada para problemas de superfície com a sua instância de Gestão API, há casos em que os problemas não se refletem em alterações na métrica de **capacidade**.

## <a name="capacity-metric-behavior"></a>Comportamento métrico da capacidade

Devido à sua construção, a **capacidade** de vida real pode ser impactada por muitas variáveis, por exemplo:

+ padrões de conexão (nova ligação a pedido vs reutilização da ligação existente)
+ tamanho de um pedido e resposta
+ políticas configuradas em cada API ou número de clientes que enviam pedidos.

Quanto mais complexas forem as operações nos pedidos, maior será o consumo **de capacidade.** Por exemplo, políticas de transformação complexas consomem muito mais CPU do que um simples pedido de reencaminhamento. As respostas lentas do serviço de backend também o aumentarão.

> [!IMPORTANT]
> **A capacidade** não é uma medida direta do número de pedidos que estão a ser processados.

![Picos métricos de capacidade](./media/api-management-capacity/capacity-spikes.png)

**A capacidade** também pode aumentar intermitentemente ou ser maior do que zero, mesmo que não haja pedidos a serem processados. Acontece por causa de ações específicas do sistema ou da plataforma e não deve ser tomado em consideração na decisão de escalar uma instância.

A métrica de baixa **capacidade** não significa necessariamente que a sua instância de Gestão API não esteja a ter problemas.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Utilize o Portal Azure para examinar a capacidade
  
![Métrica de capacidade](./media/api-management-capacity/capacity-metric.png)  

1. Navegue para a sua instância APIM no [portal Azure.](https://portal.azure.com/)
2. Selecione **Métricas**.
3. A partir da secção roxa, **selecione A** métrica de Capacidade a partir das métricas disponíveis e deixe a agregação padrão **de Avg.**

    > [!TIP]
    > Deve sempre olhar para uma avaria métrica de **capacidade** por local para evitar interpretações erradas.

4. A partir da secção verde, selecione **Localização** para dividir a métrica por dimensão.
5. Escolha um prazo desejado a partir da barra superior da secção.

    Pode definir um alerta métrico para saber quando algo inesperado está acontecendo. Por exemplo, obtenha notificações quando a sua instância APIM tiver excedido a sua capacidade máxima esperada por mais de 20 minutos.

    >[!TIP]
    > Pode configurar alertas para o informar quando o seu serviço está a esgotar-se na capacidade ou utilizar a funcionalidade de autoscalcificação Azure Monitor para adicionar automaticamente uma unidade de Gestão De API Azure. A operação de escala pode demorar cerca de 30 minutos, por isso deve planear as suas regras em conformidade.  
    > Só é permitido escalar a localização principal.

## <a name="use-capacity-for-scaling-decisions"></a>Capacidade de utilização para decisões de escala

**A capacidade** é a métrica para tomar decisões se escalar uma instância de Gestão API para acomodar mais carga. Considere:

+ Olhando para uma tendência a longo prazo e média.
+ Ignorando picos repentinos que provavelmente não estão relacionados com qualquer aumento de carga (ver secção "Comportamento métrico de capacidade" para explicação).
+ Atualizar ou escalonar a sua instância, quando o valor da **capacidade**ultrapassa 60% ou 70% por um período de tempo mais longo (por exemplo, 30 minutos). Valores diferentes podem funcionar melhor para o seu serviço ou cenário.

>[!TIP]  
> Se conseguir estimar previamente o seu tráfego, teste a sua instância APIM sobre as cargas de trabalho que espera. Pode aumentar gradualmente a carga de pedido do seu inquilino e monitorizar qual o valor da métrica de capacidade corresponde à sua carga máxima. Siga os passos da secção anterior para utilizar o portal Azure para perceber a quantidade de capacidade utilizada em qualquer momento.

## <a name="next-steps"></a>Passos seguintes

[Como escalar ou atualizar uma instância de serviço de Gestão API Azure](upgrade-and-scale.md)