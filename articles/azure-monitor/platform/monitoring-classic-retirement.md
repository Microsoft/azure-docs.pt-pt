---
title: Alertas unificados & monitoramento no Azure Monitor substitui alertas clássicos & monitoramento
description: Visão geral da desativação dos serviços e funcionalidades de monitoramento clássicos, mostrados anteriormente em portal do Azure em alertas (clássico). O monitoramento clássico de alertas & inclui alertas de métrica clássicos para recursos do Azure, alertas de métrica clássicos para Application Insights, alertas clássicos do WebTest para Application Insights, alertas com base em métricas personalizadas clássicas para Application Insights e clássico alertas para Application Insights SmartDetection v1
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: ea50a4f10aa1d794f875cb1eb9601774bec146cd
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676900"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Alertas unificados & monitoramento no Azure Monitor substitui alertas clássicos & monitoramento

Azure Monitor agora se tornou um serviço de monitoramento de pilha completo unificado, que agora dá suporte a ' uma métrica ' e a ' um alerta ' entre os recursos; para obter mais informações, consulte nossa [postagem no blog sobre novos Azure monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). As novas plataformas de monitoramento e alerta do Azure foram criadas para serem mais rápidas, inteligentes e extensíveis – mantendo o ritmo com os crescentes extensãos de computação em nuvem e em linha com a filosofia de nuvem inteligente da Microsoft. 

Com a nova plataforma de monitoramento e alerta do Azure em vigor, iremos desativá-la na seção "clássico" de monitoramento e alerta da plataforma de alertas na exibição de alerta *clássico* dos alertas do Azure, **será preterida em agosto de 2019 em nuvens públicas do Azure** . A [nuvem do Azure governamental](../../azure-government/documentation-government-welcome.md) e o [Azure China 21vianet](https://docs.azure.cn/) não serão afetados.

> [!NOTE]
> Devido ao atraso na distribuição da ferramenta de migração, a data de desativação da migração de alertas clássicos foi [prorrogada para 31 de agosto de 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) da data de lançamento original de 30 de junho de 2019.

 ![Alerta clássico no portal do Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Incentivamos você a começar e recriar seus alertas na nova plataforma. Para clientes que têm um grande número de alertas, estamos [implantando em fases](alerts-understand-migration.md#rollout-phases), uma [ferramenta de migração voluntária](alerts-using-migration-tool.md) para mover alertas clássicos existentes para o novo sistema de alertas sem interrupções ou custos adicionais.

> [!IMPORTANT]
> As regras de alerta clássico criadas no log de atividades não serão preteridas nem migradas. Todas as regras de alerta clássicas criadas no log de atividades podem ser acessadas e usadas no estado em que se encontram do novo Azure Monitor-Alerts. Para obter mais informações, consulte [criar, exibir e gerenciar alertas do log de atividades usando Azure monitor](../../azure-monitor/platform/alerts-activity-log.md). Da mesma forma, alertas na integridade do serviço podem ser acessados e usados no estado em que se encontram da nova seção de integridade do serviço. Para obter detalhes, consulte [alertas sobre notificações de integridade do serviço](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Métricas e alertas unificados no Application Insights

A plataforma métrica mais recente do Azure Monitor agora será o monitoramento de energia de Application Insights. Essa movimentação significa que Application Insights se conectará a grupos de ações, permitindo muito mais opções do que apenas as chamadas de email e webhook anteriores. Os alertas agora podem disparar chamadas de voz, Azure Functions, aplicativos lógicos, SMS e ferramentas de ITSM, como o ServiceNow e Runbooks de automação. Com monitoramento e alertas quase em tempo real, a nova plataforma permite que Application Insights usuários aproveitem a mesma tecnologia que o monitoramento de energia em outros recursos do Azure e a base do monitoramento de produtos da Microsoft.

O novo monitoramento e alerta unificados para o Application Insights abrangerá:

- **Application insights métricas de plataforma** – que fornece métricas pré-criados populares do produto Application insights. Para obter mais informações, consulte este artigo sobre como usar [métricas de plataforma para Application insights em novos Azure monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Application insights a disponibilidade e o teste na Web** -que fornece a capacidade de avaliar a resposta e a disponibilidade do seu aplicativo Web ou servidor. Para obter mais informações, consulte este artigo sobre como usar [alertas e testes de disponibilidade para Application insights em novos Azure monitor](../../azure-monitor/app/monitor-web-app-availability.md).
- **Application insights métricas personalizadas** – que permite definir e emitir suas próprias métricas para monitoramento e alertas. Para obter mais informações, consulte este artigo sobre como usar a [métrica personalizada para Application insights sobre novos Azure monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Application insights anomalias de falha (parte da detecção inteligente)** – que o notificará automaticamente quase em tempo real se seu aplicativo Web sofrer um aumento anormal na taxa de solicitações HTTP ou chamadas de dependência com falha. Para obter mais informações, consulte este artigo sobre como usar [a detecção inteligente-anomalias de falha](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Métricas e alertas unificados para outros recursos do Azure

Desde março de 2018, a próxima geração de alertas e monitoramento multidimensional para recursos do Azure está em disponibilidade. Agora, a plataforma de métricas e os alertas mais recentes são mais rápidos com recursos quase em tempo real. O mais importante é que os alertas de plataforma de métrica mais recentes fornecem mais granularidade, pois a plataforma mais recente inclui a opção de dimensões, que permite dividir e filtrar a combinação, condição ou operação de valor específico. Como todos os alertas no novo Azure Monitor, os alertas de métrica mais recentes são mais extensíveis com o uso de ActionGroups – permitindo que as notificações se expandam além de email ou webhook para SMS, voz, Azure Function, runbook de automação e muito mais. Para obter mais informações, consulte [criar, exibir e gerenciar alertas de métrica usando Azure monitor](../../azure-monitor/platform/alerts-metric.md).
As métricas mais recentes para os recursos do Azure estão disponíveis como:

- **Azure monitor métricas da plataforma Standard** – que fornece métricas preenchidas previamente e populares de vários serviços e produtos do Azure. Para obter mais informações, consulte este artigo sobre [métricas com suporte em Azure monitor](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) e [suporte a alertas de métrica em Azure monitor](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure monitor métricas personalizadas** – que fornece métricas de fontes orientadas pelo usuário, incluindo o agente de diagnóstico do Azure. Para obter mais informações, consulte este artigo sobre [métricas personalizadas em Azure monitor](../../azure-monitor/platform/metrics-custom-overview.md). Usando métricas personalizadas, você também pode publicar métricas coletadas pelo [Windows diagnóstico do Azure Agent](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) e pelo [agente InfluxData Telegraf](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Desativação da plataforma de monitoramento e alerta clássica

Como mencionado anteriormente, a plataforma de monitoramento e alertas clássicas utilizável atualmente da [seção alertas (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) do portal do Azure será desativada nos próximos meses, dado que eles foram substituídos pelo sistema mais recente.
Monitoramento e alertas clássicos mais antigos serão desativados em 31 de agosto de 2019; incluindo o fechamento de APIs relacionadas, portal do Azure interface e serviços nela. Especificamente, esses recursos serão preteridos:

- Métricas e alertas mais antigos (clássicos) para recursos do Azure como disponíveis atualmente por meio da [seção alertas (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) de portal do Azure; acessível como recurso [Microsoft. insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- A plataforma mais antiga (clássica) e métricas personalizadas para Application Insights, bem como alertas nelas como disponíveis atualmente por meio da [seção alertas (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) de portal do Azure e acessíveis como [o recurso Microsoft. insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Alerta de anomalias de falha (clássico) mais antigo disponível atualmente como [detecção inteligente dentro de Application insights](../../azure-monitor/app/proactive-diagnostics.md) no portal do Azure; com alertas configurados mostrados na [seção alertas (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) de portal do Azure

Todos os sistemas de monitoramento e alerta clássicos, incluindo [API](https://msdn.microsoft.com/library/azure/dn931945.aspx)correspondente, [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md), [CLI](../../azure-monitor/platform/alerts-classic-portal.md), [portal do Azure página](../../azure-monitor/platform/alerts-classic-portal.md)e [modelo de recurso](../../azure-monitor/platform/alerts-enable-template.md) , permanecerão utilizáveis até o fim de agosto de 2019. 

No final de agosto de 2019, em Azure Monitor:

- O serviço de monitoramento e alertas clássicos será desativado e não estará mais disponível para a criação de novas regras de alerta.
- Qualquer regra de alerta que continue existindo em alertas (clássicos) Além de 2019 de agosto continuará a executar e acionar notificações, mas não estará disponível para modificação.
- A partir de setembro de 2019, as regras de alerta no monitoramento clássico & alertas que podem ser migrados, serão automaticamente movidas pela Microsoft para o equivalente na nova plataforma do Azure monitor em fases que abrangem algumas semanas. O processo será contínuo sem nenhum tempo de inatividade e os clientes não terão nenhuma perda na cobertura do monitoramento.
- As regras de alerta migradas para a nova plataforma de alertas fornecerão a cobertura de monitoramento como antes, mas serão acionadas notificações com novas cargas. Qualquer endereço de email, ponto de extremidade de webhook ou link de aplicativo lógico associado à regra de alerta clássica será postergado quando for migrado, mas poderá não se comportar corretamente, pois a carga do alerta será diferente na nova plataforma.
- Algumas [regras de alerta clássicas que não podem ser migradas automaticamente](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) e exigem a ação manual dos usuários continuarão a ser executadas até 2020 de junho.

> [!IMPORTANT]
> O monitor de Microsoft Azure foi distribuído na [ferramenta de fases para migrar voluntariamente](alerts-using-migration-tool.md) suas regras de alerta clássicas para a nova plataforma em breve. E execute-o por força para todas as regras de alerta clássicas que ainda existem e podem ser migradas, a partir de setembro de 2019. Os clientes precisarão garantir que a automação que consome a carga da regra de alerta clássica seja adaptada para lidar com a nova carga de [métricas unificadas e alertas em Application insights](#unified-metrics-and-alerts-in-application-insights) ou [métricas unificadas e alertas para outros recursos do Azure](#unified-metrics-and-alerts-for-other-azure-resources), após a migração das regras de alerta clássicas. Para obter mais informações, consulte [preparar para a migração da regra de alerta clássica](alerts-prepare-migration.md)

Este artigo será atualizado continuamente com links & detalhes sobre a nova funcionalidade de alerta de & de monitoramento do Azure, bem como a disponibilidade de ferramentas para ajudar os usuários na adoção da nova plataforma de Azure Monitor.

## <a name="pricing-for-migrated-alert-rules"></a>Preços das regras de alerta migradas

Estamos implantando uma ferramenta de migração para ajudá-lo a migrar seus [alertas Azure monitor clássicos](../../azure-monitor/platform/alerts-classic.overview.md) para a nova experiência de alertas. As regras de alertas migradas e os grupos de ações migrados correspondentes (e-mail, webhook ou LogicApp) continuam a ser gratuitos. A funcionalidade de que dispunha com os alertas clássicos, incluindo a possibilidade de editar o limiar, o tipo de agregação e a granularidade da agregação, continuará a estar disponível gratuitamente com a regra de alerta migrada. No entanto, se editar a regra de alerta migrada para utilizar alguma das funcionalidades, notificações ou tipos de ações da nova plataforma de alertas, aplicar-se-á um custo correspondente. Para obter mais informações sobre os preços das regras e notificações de alerta, consulte [preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

Seguem-se exemplos de casos nos quais incorrerá em custos pela regra de alerta:

- Qualquer regra de alerta (não migrada) nova criada para além das unidades gratuitas, na nova plataforma do Azure Monitor
- Quaisquer dados ingeridos e retidos para além das unidades gratuitas incluídas no Azure Monitor
- Quaisquer multitestes Web executados pelo Application Insights
- Quaisquer métricas personalizadas armazenadas para além das unidades gratuitas incluídas no Azure Monitor
- Quaisquer regras de alerta migradas que são editadas para usar recursos de alerta de métrica mais recentes, como frequência, vários recursos/dimensões, [limites dinâmicos](alerts-dynamic-thresholds.md), alteração de recurso/sinal e assim por diante.
- Quaisquer grupos de ação migrados que são editados para usar notificações mais recentes ou tipos de ação como SMS, chamada de voz e/ou integração de ITSM.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [novo Azure monitor unificado](../../azure-monitor/overview.md).
* Saiba mais sobre os novos [alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
