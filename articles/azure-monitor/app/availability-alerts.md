---
title: Configurar alertas de disponibilidade com insights de aplicação do Azure [ Microsoft Docs
description: Configurar testes Web no Application Insights. Receber alertas se um site ficar indisponível ou responder lentamente.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 5af6aec2267384c37f664522d075bf26c632e7e9
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382873"
---
# <a name="availability-alerts"></a>Alertas de disponibilidade

O [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) envia regularmente pedidos Web para a sua aplicação a partir de pontos em todo o mundo. Pode alertá-lo se a sua aplicação não estiver a responder, ou se responder muito lentamente.

## <a name="enable-alerts"></a>Ativar alertas

Os alertas estão agora automaticamente ativados por padrão, mas para configurar completamente o alerta, primeiro tem de criar o seu teste de disponibilidade.

![Criar experiência](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Com os [novos alertas unificados,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)as preferências de regra de alerta e notificação com [grupos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) de ação **devem ser** configuradas na experiência de alertas. Sem os seguintes passos, só receberá notificações no portal.

1. Depois de guardar o teste de disponibilidade, no separador de detalhes clique na elipse pelo teste que acabou de fazer. Clique em "editar alerta".

   ![Editar após guardar](./media/availability-alerts/edit-alert.png)

2. Detete o nível de gravidade desejado, a descrição da regra e, mais importante, o grupo de ação que tem as preferências de notificação que gostaria de utilizar para esta regra de alerta.

   ![Editar após guardar](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> Os alertas de disponibilidade criados através desta experiência são baseados no Estado. Isto significa que quando os critérios de alerta são cumpridos, um único alerta é gerado quando o site é detetado como indisponível. Se o site ainda estiver em baixo da próxima vez que os critérios de alerta forem avaliados, isso não gerará um novo alerta. Por isso, se o seu site estivesse avariado durante uma hora e tivesse configurado um alerta de e-mail, só receberia um e-mail quando o site fosse encerrado, e um e-mail subsequente quando o site voltasse a funcionar. Não receberia alertas contínuos lembrando-lhe que o site ainda não estava disponível.

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Alerta em X fora dos locais de Y reportando falhas

A regra de alerta de localização X out of Y é ativada por padrão na nova experiência de [alertas unificados,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)quando cria um novo teste de disponibilidade. Pode optar por não optar por selecionar a opção "clássica" ou optar por desativar a regra de alerta.

> [!NOTE]
> Configure os grupos de ação para receber notificações quando o alerta disparar seguindo os passos acima. Sem este passo, só receberá notificações no portal quando a regra disparar.
>

### <a name="alert-on-availability-metrics"></a>Alerta sobre métricas de disponibilidade

Utilizando os [novos alertas unificados,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)pode alertar também para as métricas de disponibilidade agregada segmentadas e métricas de duração do teste:

1. Selecione um recurso Deinsights de Aplicação na experiência Metrics e selecione uma métrica de disponibilidade:

    ![Seleção de métricas de disponibilidade](./media/availability-alerts/select-metric.png)

2. Configurar a opção de alertas a partir do menu irá levá-lo à nova experiência onde pode selecionar testes ou locais específicos para configurar a regra de alerta. Também pode configurar os grupos de ação para esta regra de alerta aqui.

### <a name="alert-on-custom-analytics-queries"></a>Alerta sobre consultas de análise personalizadas

Utilizando os [novos alertas unificados,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)pode alertar para [consultas de registo personalizadas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Com consultas personalizadas, pode alertar sobre qualquer condição arbitrária que o ajude a obter o sinal mais fiável de problemas de disponibilidade. Isto também é aplicável, se estiver a enviar resultados de disponibilidade personalizados utilizando o TrackAvailability SDK.

> [!Tip]
> As métricas dos dados de disponibilidade incluem quaisquer resultados de disponibilidade personalizados que possa estar a submeter, ligando para o nosso TrackAvailability SDK. Pode utilizar o alerta sobre o suporte de métricas para alertar sobre os resultados de disponibilidade personalizada.
>

## <a name="automate-alerts"></a>Alertas de automatização

Para automatizar este processo com os modelos do Gestor de Recursos Azure, consulte o Create a Metric alert com documentação do modelo do Gestor de [Recursos.](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert)

## <a name="troubleshooting"></a>Resolução de problemas

Artigo dedicado à resolução de [problemas.](troubleshoot-availability.md)

## <a name="next-steps"></a>Passos seguintes

* [Testes Web com vários passos](availability-multistep.md)
* [Url ping web testes](monitor-web-app-availability.md)
