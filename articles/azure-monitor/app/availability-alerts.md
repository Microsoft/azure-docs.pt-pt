---
title: Configurar alertas de disponibilidade com a Azure Application Insights | Microsoft Docs
description: Saiba como configurar testes web em Application Insights. Receber alertas se um site ficar indisponível ou responder lentamente.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: cbafa7997d203cf06a3e91965355258f0088d77e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589885"
---
# <a name="availability-alerts"></a>Alertas de disponibilidade

O [Azure Application Insights](./app-insights-overview.md) envia regularmente pedidos Web para a sua aplicação a partir de pontos em todo o mundo. Pode alertá-lo se a sua aplicação não estiver a responder, ou se responder muito lentamente.

## <a name="enable-alerts"></a>Ativar alertas

Os alertas são agora automaticamente ativados por padrão, mas para configurar totalmente o alerta tem primeiro de criar o seu teste de disponibilidade.

![Criar experiência](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Com os [novos alertas unificados,](../alerts/alerts-overview.md)a gravidade da regra de alerta e as preferências de notificação com [grupos de ação](../alerts/action-groups.md) devem **ser** configuradas na experiência dos alertas. Sem os seguintes passos, só receberá notificações no portal.

1. Depois de guardar o teste de disponibilidade, no separador detalhes clique na elipse pelo teste que acabou de escamá-la. Clique em "editar alerta".

   ![A screenshot mostra o alerta editar selecionado do menu.](./media/availability-alerts/edit-alert.png)

2. Desa estacie o nível de gravidade pretendido, a descrição das regras e, mais importante, o grupo de ação que tem as preferências de notificação que gostaria de utilizar para esta regra de alerta.

   ![A screenshot mostra a página de gestão de regras onde pode editar a regra.](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> Os alertas de disponibilidade criados através desta experiência são baseados no Estado. Isto significa que quando os critérios de alerta são cumpridos, um único alerta é gerado quando o site é detetado como indisponível. Se o site ainda estiver em baixo da próxima vez que os critérios de alerta forem avaliados, isso não gerará um novo alerta. Portanto, se o seu site estivesse em baixo durante uma hora e tivesse configurado um alerta de e-mail, só receberia um e-mail quando o site foi avariou, e um e-mail subsequente quando o site estava de volta. Não receberia alertas contínuos lembrando que o site ainda não estava disponível.

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Alerta em X fora de locais Y reportando falhas

A regra de alerta de x fora de y localizações é ativada por padrão na [nova experiência de alertas unificados](../alerts/alerts-overview.md), quando você cria um novo teste de disponibilidade. Pode optar por não selecionar a opção "clássico" ou optar por desativar a regra de alerta.

> [!NOTE]
> Configure os grupos de ação para receber notificações quando o alerta é acionado seguindo os passos acima. Sem este passo, só receberá notificações no portal quando a regra disparar.
>

### <a name="alert-on-availability-metrics"></a>Alerta sobre métricas de disponibilidade

Utilizando os [novos alertas unificados,](../alerts/alerts-overview.md)pode alertar também para a disponibilidade agregada segmentada e para as métricas de duração do teste:

1. Selecione um recurso De Insights de Aplicação na experiência Métricas e selecione uma métrica de Disponibilidade:

    ![Seleção de métricas de disponibilidade](./media/availability-alerts/select-metric.png)

2. A opção de alerta de configuração do menu irá levá-lo à nova experiência onde pode selecionar testes ou locais específicos para configurar a regra de alerta. Pode também configurar os grupos de ação para esta regra de alerta aqui.

### <a name="alert-on-custom-analytics-queries"></a>Alerta em consultas de análise personalizadas

Utilizando os [novos alertas unificados,](../alerts/alerts-overview.md)pode alertar em [consultas de registo personalizadas](../alerts/alerts-unified-log.md). Com consultas personalizadas, pode alertar sobre qualquer condição arbitrária que o ajude a obter o sinal mais fiável de problemas de disponibilidade. Isto também é aplicável, se estiver a enviar resultados de disponibilidade personalizados usando o TrackVailability SDK.

> [!Tip]
> As métricas dos dados de disponibilidade incluem quaisquer resultados de disponibilidade personalizados que possa estar a enviar, ligando para o nosso TrackVailability SDK. Pode utilizar o suporte de alerta sobre métricas para alertar sobre os resultados de disponibilidade personalizados.
>

## <a name="automate-alerts"></a>Automatize alertas

Para automatizar este processo com os modelos do Gestor de Recursos Azure, consulte o [Alerta métrico criar um alerta métrico com](../alerts/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert) documentação do modelo do Gestor de Recursos.

## <a name="troubleshooting"></a>Resolução de problemas

Artigo dedicado [à resolução de problemas.](troubleshoot-availability.md)

## <a name="next-steps"></a>Passos seguintes

* [Testes Web com vários passos](availability-multistep.md)
* [Testes web de url ping](monitor-web-app-availability.md)

