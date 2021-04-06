---
title: Capacidade de um caso de Gestão da API da Azure | Microsoft Docs
description: Este artigo explica qual é a métrica da capacidade e como tomar decisões informadas sobre a escala de um caso de Gestão API Azure.
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
ms.openlocfilehash: c7c002b57f2220ac0a9fba43a8081b2a4ed800e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93081064"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Capacidade de uma instância da Gestão de API do Azure

**A capacidade** é a métrica mais importante do [Azure Monitor](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) para tomar decisões informadas sobre a escala de um caso de Gestão de API para acomodar mais carga. A sua construção é complexa e impõe determinado comportamento.

Este artigo explica qual é a **capacidade** e como se comporta. Mostra como aceder às métricas **de capacidade** no portal Azure e sugere quando considerar o dimensionamento ou a atualização da sua instância de Gestão de API.

> [!IMPORTANT]
> Este artigo discute como pode monitorizar e escalar o seu exemplo de Gestão API Azure com base na sua métrica de capacidade. No entanto, é igualmente importante compreender o que acontece quando uma instância individual de Gestão da API *atingiu* efetivamente a sua capacidade. A Azure API Management não aplicará qualquer estrangulamento ao nível do serviço para evitar uma sobrecarga física das instâncias. Quando um caso atinge a sua capacidade física, comportar-se-á semelhante a qualquer servidor web sobrecarregado que não seja capaz de processar pedidos de entrada: a latência aumentará, as ligações serão retiradas, ocorrerão erros de tempo limite, etc. Isto significa que os clientes da API devem estar preparados para lidar com esta possibilidade semelhante a qualquer outro serviço externo (por exemplo, aplicando políticas de retrips).

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, você deve ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Um caso APIM. Para obter mais informações, consulte [Criar uma instância de Gestão API Azure.](get-started-create-service-instance.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>O que é capacidade

![Diagrama que explica a métrica da capacidade.](./media/api-management-capacity/capacity-ingredients.png)

**A capacidade** é um indicador de carga num caso de Gestão de API. Reflete o uso de recursos (CPU, memória) e os comprimentos das filas de rede. O uso da CPU e da memória revela o consumo de recursos por:

+ Serviços de plano de dados da API Management, como o processamento de pedidos, que podem incluir o encaminhamento de pedidos ou a execução de uma apólice.
+ Serviços de gestão de aviões de gestão da API Management, tais como ações de gestão aplicadas através do Portal Azure ou ARM, ou carga proveniente do portal de [desenvolvimento.](api-management-howto-developer-portal.md)
+ Processos selecionados do sistema operativo, incluindo processos que envolvem custos de apertos de mão TLS em novas ligações.

A **capacidade** total é uma média dos seus próprios valores de cada unidade de um caso de Gestão de API.

Embora a **métrica da capacidade** seja projetada para resolver problemas com a sua instância de Gestão de API, existem casos em que os problemas não se refletem em alterações na **métrica** da capacidade .

## <a name="capacity-metric-behavior"></a>Comportamento métrico da capacidade

Devido à sua construção, a **capacidade da** vida real pode ser impactada por muitas variáveis, por exemplo:

+ padrões de conexão (nova ligação num pedido vs reutilização da ligação existente)
+ tamanho de um pedido e resposta
+ políticas configuradas em cada API ou número de clientes que enviam pedidos.

Quanto mais complexas forem as operações sobre os pedidos, maior será o consumo **de capacidade.** Por exemplo, políticas de transformação complexas consomem muito mais CPU do que um simples pedido de encaminhamento. As respostas lentas do serviço backend também irão aumentá-lo.

> [!IMPORTANT]
> **A capacidade** não é uma medida direta do número de pedidos que estão a ser processados.

![Picos métricos de capacidade](./media/api-management-capacity/capacity-spikes.png)

**A capacidade** também pode aumentar intermitentemente ou ser superior a zero, mesmo que não haja pedidos a ser processados. Acontece devido a ações específicas do sistema ou da plataforma e não deve ser tomado em consideração na hora de decidir se deve ou não escalar um caso.

A **baixa capacidade métrica** não significa necessariamente que o seu caso de Gestão de API não esteja a ter problemas.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Utilize o Portal Azure para examinar a capacidade
  
![Métrica de capacidade](./media/api-management-capacity/capacity-metric.png)  

1. Navegue para o seu caso APIM no [portal Azure](https://portal.azure.com/).
2. Selecione **Métricas**.
3. A partir da secção roxa, selecione a métrica de **capacidade** das métricas disponíveis e deixe a agregação **padrão de Avg.**

    > [!TIP]
    > Deve sempre olhar para uma desagregação métrica **de capacidade** por local para evitar interpretações erradas.

4. A partir da secção verde, selecione **Localização** para dividir a métrica por dimensão.
5. Escolha um calendário desejado na barra superior da secção.

    Pode definir um alerta métrico para que saiba quando algo inesperado está acontecendo. Por exemplo, receba notificações quando a sua instância APIM estiver a exceder a capacidade máxima esperada durante mais de 20 minutos.

    >[!TIP]
    > Pode configurar alertas para o informar quando o seu serviço está a esgotar-se na capacidade ou utilizar a funcionalidade de autoscalagem do Azure Monitor para adicionar automaticamente uma unidade de Gestão API Azure. A operação de escala pode demorar cerca de 30 minutos, por isso deve planear as suas regras em conformidade.  
    > Só é permitido escalonar a localização principal.

## <a name="use-capacity-for-scaling-decisions"></a>Capacidade de utilização para decisões de escalonamento

**A capacidade** é a métrica para tomar decisões sobre a escala de um caso de Gestão de API para acomodar mais carga. Considere:

+ Olhando para uma tendência de longo prazo e média.
+ Ignorando picos repentinos que provavelmente não estão relacionados com qualquer aumento de carga (ver secção "Comportamento métrico da capacidade" para explicação).
+ Atualização ou dimensionamento do seu caso, quando o valor da **capacidade** exceder 60% ou 70% por um período de tempo mais longo (por exemplo, 30 minutos). Valores diferentes podem funcionar melhor para o seu serviço ou cenário.

>[!TIP]  
> Se conseguir estimar previamente o seu tráfego, teste a sua instância APIM em cargas de trabalho que espera. Pode aumentar gradualmente a carga de pedido no seu inquilino e monitorizar o valor da métrica da capacidade correspondente à sua carga máxima. Siga os passos da secção anterior para utilizar o portal Azure para entender a quantidade de capacidade utilizada em qualquer momento.

## <a name="next-steps"></a>Passos seguintes

- [Como escalar ou atualizar uma instância de serviço da API AZure](upgrade-and-scale.md)
- [Otimize e poupe nos gastos na nuvem](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)