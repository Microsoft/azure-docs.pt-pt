---
title: Capacidade de uma instância de gerenciamento de API do Azure | Microsoft Docs
description: Este artigo explica o que é a métrica de capacidade e como tomar decisões informadas quanto à escala de uma instância de gerenciamento de API do Azure.
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
ms.openlocfilehash: a585ab059319b15be1f2a86bf10b7dc58da72494
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299453"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Capacidade de uma instância de gerenciamento de API do Azure

A **capacidade** é a mais importante [Azure monitor métrica](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) para tomar decisões informadas quanto à possibilidade de dimensionar uma instância de gerenciamento de API para acomodar mais carga. Sua construção é complexa e impõe determinado comportamento.

Este artigo explica o que é a **capacidade** e como ela se comporta. Ele mostra como acessar as métricas de **capacidade** no portal do Azure e sugere quando considerar o dimensionamento ou a atualização da instância de gerenciamento de API.

> [!IMPORTANT]
> Este artigo discute como você pode monitorar e dimensionar sua instância de gerenciamento de API do Azure com base na sua métrica de capacidade. No entanto, é igualmente importante entender o que acontece quando uma instância de gerenciamento de API individual realmente *atingiu* sua capacidade. O gerenciamento de API do Azure não aplicará nenhuma limitação no nível de serviço para evitar uma sobrecarga física das instâncias. Quando uma instância atinge sua capacidade física, ela se comportará de forma semelhante a qualquer servidor Web sobrecarregado que não consiga processar as solicitações de entrada: a latência aumentará, as conexões serão eliminadas, ocorrerão erros de tempo limite, etc. Isso significa que os clientes de API devem estar preparados para lidar com essa possibilidade semelhante a qualquer outro serviço externo (por exemplo, aplicando políticas de repetição).

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas deste artigo, você deve ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância de APIM. Para obter mais informações, consulte [criar uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>O que é a capacidade

![Métrica de capacidade](./media/api-management-capacity/capacity-ingredients.png)

A **capacidade** é um indicador de carga em uma instância de gerenciamento de API. Ele reflete o uso de recursos (CPU, memória) e comprimentos de fila de rede. Uso de CPU e memória revela consumo de recursos por:

+ Serviços de plano de dados de gerenciamento de API, como processamento de solicitações, que podem incluir solicitações de encaminhamento ou execução de uma política.
+ Serviços de plano de gerenciamento de API, como ações de gerenciamento aplicadas por meio do portal do Azure ou ARM, ou carga proveniente do [portal do desenvolvedor](api-management-howto-developer-portal.md).
+ Os processos do sistema operacional selecionados, incluindo processos que envolvem o custo de Handshakes de SSL em novas conexões.

A **capacidade** total é uma média de seus próprios valores de cada unidade de uma instância de gerenciamento de API.

Embora a **métrica de capacidade** seja projetada para a superfície de problemas com sua instância de gerenciamento de API, há casos em que os problemas não serão refletidos nas alterações na **métrica de capacidade**.

## <a name="capacity-metric-behavior"></a>Comportamento de métrica de capacidade

Devido à sua construção, a **capacidade** real pode ser afetada por muitas variáveis, por exemplo:

+ padrões de conexão (nova conexão em uma solicitação versus reutilizar a conexão existente)
+ tamanho de uma solicitação e resposta
+ políticas configuradas em cada API ou número de clientes que enviam solicitações.

Quanto mais complexas forem as operações nas solicitações, maior será o consumo de **capacidade** . Por exemplo, políticas de transformação complexas consomem muito mais CPU do que um encaminhamento de solicitação simples. As respostas de serviço de back-end lentas aumentarão também.

> [!IMPORTANT]
> A **capacidade** não é uma medida direta do número de solicitações que estão sendo processadas.

![Picos de métrica de capacidade](./media/api-management-capacity/capacity-spikes.png)

A **capacidade** também pode ser propicomente intermitente ou maior que zero, mesmo se não houver nenhuma solicitação sendo processada. Isso acontece devido a ações específicas do sistema ou da plataforma e não deve ser levado em consideração ao decidir se é necessário dimensionar uma instância.

A **métrica** de baixa capacidade não significa necessariamente que sua instância de gerenciamento de API não está enfrentando problemas.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Usar o portal do Azure para examinar a capacidade
  
![Métrica de capacidade](./media/api-management-capacity/capacity-metric.png)  

1. Navegue até sua instância do APIM no [portal do Azure](https://portal.azure.com/).
2. Selecione **Métricas**.
3. Na seção roxa, selecione métrica de **capacidade** de métricas disponíveis e deixe a agregação padrão de **média** .

    > [!TIP]
    > Você deve sempre examinar uma análise de métrica de **capacidade** por local para evitar interpretações erradas.

4. Na seção verde, selecione **local** para dividir a métrica por dimensão.
5. Escolha um período de tempo desejado na barra superior da seção.

    Você pode definir um alerta de métrica para informá-lo quando algo inesperado estiver acontecendo. Por exemplo, obtenha notificações quando sua instância do APIM estiver excedendo sua capacidade de pico esperada por mais de 20 minutos.

    >[!TIP]
    > Você pode configurar alertas para que você saiba quando o serviço está ficando com pouca capacidade ou use Azure Monitor funcionalidade de dimensionamento automático para adicionar automaticamente uma unidade de gerenciamento de API do Azure. A operação de dimensionamento pode levar cerca de 30 minutos, portanto, você deve planejar suas regras de acordo.  
    > Só é permitido dimensionar o local mestre.

## <a name="use-capacity-for-scaling-decisions"></a>Use a capacidade para as decisões de dimensionamento

A **capacidade** é a métrica para tomar decisões quanto à possibilidade de dimensionar uma instância de gerenciamento de API para acomodar mais carga. Aconselhável

+ Observando uma tendência e uma média de longo prazo.
+ Ignorando picos repentinos que provavelmente não estão relacionados a qualquer aumento na carga (consulte a seção "comportamento de métrica de capacidade" para obter uma explicação).
+ Atualizar ou dimensionar sua instância, quando o valor da **capacidade**excede 60% ou 70% por um período de tempo maior (por exemplo, 30 minutos). Valores diferentes podem funcionar melhor para seu serviço ou cenário.

>[!TIP]  
> Se você for capaz de estimar o tráfego com antecedência, teste sua instância do APIM em cargas de trabalho que você espera. Você pode aumentar a carga da solicitação em seu locatário gradualmente e monitorar qual valor da métrica de capacidade corresponde à sua carga de pico. Siga as etapas da seção anterior para usar portal do Azure para entender a quantidade de capacidade usada em um determinado momento.

## <a name="next-steps"></a>Passos seguintes

[Como dimensionar ou atualizar uma instância do serviço de gerenciamento de API do Azure](upgrade-and-scale.md)