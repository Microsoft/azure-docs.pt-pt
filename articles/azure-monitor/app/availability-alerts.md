---
title: Configurar alertas de disponibilidade com o Aplicativo Azure insights | Microsoft Docs
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
ms.openlocfilehash: 1d7527d6f52235c6b95ad2e336ea9f9ba85d6344
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114401"
---
# <a name="availability-alerts"></a>Alertas de disponibilidade

O [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) envia regularmente pedidos Web para a sua aplicação a partir de pontos em todo o mundo. Ele poderá alertá-lo se seu aplicativo não estiver respondendo ou se responder muito devagar.

## <a name="enable-alerts"></a>Ativar alertas

Os alertas agora são automaticamente habilitados por padrão, mas para configurar completamente o alerta, primeiro você precisa criar inicialmente seu teste de disponibilidade.

![Criar experiência](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Com os [novos alertas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)unificados, as preferências de severidade e notificação da regra de alerta com [grupos de ação](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **devem ser** configuradas na experiência de alertas. Sem as etapas a seguir, você receberá apenas notificações no Portal.

1. Depois de salvar o teste de disponibilidade, na guia detalhes, clique nas reticências do teste que você acabou de fazer. Clique em "Editar alerta".

   ![Editar após salvar](./media/availability-alerts/edit-alert.png)

2. Defina o nível de severidade desejado, a descrição da regra e o mais importante – o grupo de ações que tem as preferências de notificação que você gostaria de usar para esta regra de alerta.

   ![Editar após salvar](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Alertar sobre falhas de relatório em X fora dos locais de Y

A regra de alerta X fora dos locais Y é habilitada por padrão na [nova experiência de alertas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)unificados, quando você cria um novo teste de disponibilidade. Você pode recusar selecionando a opção "clássico" ou optando por desabilitar a regra de alerta.

> [!NOTE]
> Configure os grupos de ação para receber notificações quando o alerta disparar seguindo as etapas acima. Sem essa etapa, você só receberá notificações no portal quando a regra for disparada.
>

### <a name="alert-on-availability-metrics"></a>Alerta sobre métricas de disponibilidade

Usando os [novos alertas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)unificados, você pode alertar sobre disponibilidade de agregação segmentada e métricas de duração de teste também:

1. Selecione um recurso de Application Insights na experiência de métricas e selecione uma métrica de disponibilidade:

    ![Seleção de métricas de disponibilidade](./media/availability-alerts/select-metric.png)

2. Configurar a opção alertas no menu o levará para a nova experiência, na qual você pode selecionar testes específicos ou locais para configurar a regra de alerta. Você também pode configurar os grupos de ação para esta regra de alerta aqui.

### <a name="alert-on-custom-analytics-queries"></a>Alerta sobre consultas de análise personalizadas

Usando os [novos alertas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)unificados, você pode alertar sobre [consultas de log personalizadas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Com consultas personalizadas, você pode alertar sobre qualquer condição arbitrária que ajuda a obter o sinal mais confiável de problemas de disponibilidade. Isso também é aplicável se você estiver enviando resultados de disponibilidade personalizados usando o SDK do API trackavailability.

> [!Tip]
> As métricas sobre os dados de disponibilidade incluem quaisquer resultados de disponibilidade personalizados que você possa estar enviando chamando nosso SDK do API trackavailability. Você pode usar o alerta no suporte a métricas para alertar sobre os resultados de disponibilidade personalizados.
>

## <a name="automate-alerts"></a>Automatizar alertas

Para automatizar esse processo com modelos de Azure Resource Manager, consulte a documentação [criar um alerta de métrica com o modelo do Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-a-availability-test-along-with-availability-test-alert) .

## <a name="troubleshooting"></a>Resolução de problemas

[Artigo de solução de problemas](troubleshoot-availability.md)dedicado.

## <a name="next-steps"></a>Passos Seguintes

* [Testes na Web de várias etapas](availability-multistep.md)
* [Testes da Web de ping de URL](monitor-web-app-availability.md)
