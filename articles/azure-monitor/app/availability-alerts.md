---
title: Configurar alertas de disponibilidade com o Azure Application Insights | Documentos da Microsoft
description: Configurar testes Web no Application Insights. Receber alertas se um site ficar indisponível ou responder lentamente.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: cc022f91d4b4fec42929769df8c979320548a1f9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304915"
---
# <a name="availability-alerts"></a>Alertas de disponibilidade

O [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) envia regularmente pedidos Web para a sua aplicação a partir de pontos em todo o mundo. Ele pode alertá-lo se seu aplicativo não está a responder ou se responde muito lenta.

## <a name="enable-alerts"></a>Ativar alertas

Alertas agora são automaticamente ativados por predefinição, mas para configurar o alerta em primeiro lugar tem de criar inicialmente o teste de disponibilidade.

![Criar experiência](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Com o [novos alertas unificadas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), as preferências de gravidade e a notificação de regra de alerta com [grupos de ação](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **tem de ser** configurado na experiência de alertas. Sem os seguintes passos, apenas receberá notificações no portal.

1. Depois de guardar o teste de disponibilidade, nos detalhes de separador clique nas reticências pelo teste que acabou de criar. Clique em "Editar alerta".

   ![Editar depois de guardar](./media/availability-alerts/edit-alert.png)

2. Defina o nível de gravidade pretendidos, a descrição da regra e mais importante, o grupo de ação com as preferências de notificação que pretende utilizar para esta regra de alerta.

   ![Editar depois de guardar](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Alerta sobre X fora de geração de relatórios de falhas de locais de Y

O X fora de localizações de Y regra de alerta está ativada por predefinição no [experiência de novos alertas unificadas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), ao criar um novo teste de disponibilidade. Pode optar por, selecionando a opção "clássica" ou optar por desabilitar a regra de alerta.

> [!NOTE]
> Configure os grupos de ação para receber notificações quando o alerta for acionado, seguindo os passos acima. Sem essa etapa, receberá apenas notificações no portal quando a regra for acionada.
>

### <a name="alert-on-availability-metrics"></a>Alerta relativamente a métricas de disponibilidade

Utilizar o [novos alertas unificadas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), pode alertar sobre a disponibilidade de agregação segmentada e testar também as métricas de duração:

1. Selecione um recurso do Application Insights a experiência de métricas e selecione uma métrica de disponibilidade:

    ![Seleção de métricas de disponibilidade](./media/availability-alerts/select-metric.png)

2. Configure alertas de opção no menu leva-o para a nova experiência onde pode selecionar localizações para configurar a regra de alerta sobre ou de testes específicos. Também pode configurar os grupos de ação para esta regra de alerta aqui.

### <a name="alert-on-custom-analytics-queries"></a>Alerta sobre consultas de análise personalizada

Utilizar o [novos alertas unificadas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), pode alertar relativamente [consultas de registo personalizado](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Com consultas personalizadas, pode alertar relativamente a quaisquer condições de arbitrária que o ajuda a obter o sinal de mais confiável de problemas de disponibilidade. Isso também é particularmente aplicável, se estiverem a enviar os resultados de disponibilidade personalizado com o SDK de TrackAvailability. 

> [!Tip]
> As métricas em dados de disponibilidade incluem quaisquer resultados de disponibilidade personalizado que pode enviar ao chamar o nosso SDK TrackAvailability. Pode utilizar alertas em métricas de suporte para o alerta, nos resultados de disponibilidade personalizado.
>

## <a name="troubleshooting"></a>Resolução de problemas

Dedicado [artigo de resolução de problemas](troubleshoot-availability.md).

## <a name="next-steps"></a>Passos Seguintes

* [Testes web de vários passos](availability-multistep.md)
* [Testes de web de ping do URL](monitor-web-app-availability.md)

