---
title: Atualização do alerta clássico & monitorização no Monitor Azure
description: Descrição da reforma dos serviços e funcionalidades clássicas de monitorização, anteriormente mostrada no portal Azure sob Alertas (clássico).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: dec4d6824644cabf8b1872da207b8554fee0b3d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77659480"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Alerta unificado & monitorização no Monitor Azure substitui monitorização clássica de alerta &

O Azure Monitor tornou-se agora um serviço de monitorização de pilhas completas unificado, que agora suporta "One Metric" e "One Alerts" através dos recursos; para mais informações, consulte a nossa [publicação de blog no novo Monitor Azure.](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/) As novas plataformas de monitorização e alerta do Azure foram construídas para serem mais rápidas, inteligentes e extensíveis – acompanhando o ritmo crescente da computação em nuvem e em linha com a filosofia microsoft Intelligent Cloud. 

Com a nova plataforma de monitorização e alerta do Azure em vigor, vamos retirar a plataforma de monitorização e alerta "clássica" - alojada dentro da vista da secção de *alertas clássicos* dos alertas Azure, **será depreceada até agosto de 2019 nas nuvens públicas de Azure.** [A nuvem do Governo Azure](../../azure-government/documentation-government-welcome.md) e a [Azure China 21Vianet](https://docs.azure.cn/) não serão afetadas.

> [!NOTE]
> Devido ao atraso no lançamento da ferramenta de migração, a data de reforma para alertas clássicos migração foi alargada para 31 de agosto de [2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) a partir da data inicialmente anunciada de 30 de junho de 2019.

 ![Alerta clássico no portal Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Encorajamo-lo a começar e a recriar os seus alertas na nova plataforma. Para os clientes que têm um grande número de alertas, estamos a [lançar por fases](alerts-understand-migration.md#rollout-phases), uma [ferramenta de migração voluntária](alerts-using-migration-tool.md) para mover os alertas clássicos existentes para o novo sistema de alertas sem perturbações ou custos adicionais.

> [!IMPORTANT]
> As regras clássicas de alerta criadas no Registo de Atividade não serão depreciadas ou migradas. Todas as regras clássicas de alerta criadas no Registo de Atividade podem ser acedidas e usadas como é do novo Monitor Azure - Alertas. Para mais informações, consulte [Criar, visualizar e gerir alertas](../../azure-monitor/platform/alerts-activity-log.md)de registo de atividade utilizando o Monitor Azure . Da mesma forma, os alertas sobre a Saúde de Serviço podem ser acedidos e utilizados como é da nova secção de Saúde de Serviço. Para mais detalhes, consulte [alertas sobre notificações](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)de saúde de serviço .

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Métricas unificadas e alertas em insights de aplicação

A nova plataforma métrica do Azure Monitor passará a ser monitorizada por energia proveniente de Application Insights. Este movimento significa que os Insights de Aplicação ligar-se-ão aos Grupos de Ação, permitindo muito mais opções do que apenas as chamadas anteriores de e-mail e webhook. Os alertas podem agora desencadear chamadas de voz, funções Azure, aplicações lógicas, SMS e ferramentas ITSM como ServiceNow e Automation Runbooks. Com monitorização e alerta quase em tempo real, a nova plataforma permite que os utilizadores de Application Insights aproveitem a mesma monitorização de energia tecnológica em outros recursos do Azure e monitorização subjacente aos produtos da Microsoft.

O novo Acompanhamento unificado e alerta para insights de aplicação abrangerá:

- **Aplicações Insights Platform métricas** – que fornece métricas pré-construídas populares a partir do produto Application Insights. Para mais informações, consulte este artigo sobre a utilização de Métricas de Plataforma para Insights de [Aplicação no novo Monitor Do Azure](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Informações de aplicação Disponibilidade e teste Web** - que lhe fornece a capacidade de avaliar a capacidade de resposta e disponibilidade da sua aplicação web ou servidor. Para mais informações, consulte este artigo sobre a utilização de [Testes de Disponibilidade e Alertas para Informações de Aplicação no novo Monitor Do Azure](../../azure-monitor/app/monitor-web-app-availability.md).
- **Insights De Aplicação Métricas personalizadas** – que permite definir e emitir as suas próprias métricas para monitorização e alertas. Para mais informações, consulte este artigo sobre a utilização de [Métricas Personalizadas para Insights de Aplicação no novo Monitor Azure](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Aplicação Insights Falha Anomalias (parte da Deteção Inteligente)** – que o notifica automaticamente em tempo real se a sua aplicação da Web experimentar um aumento anormal na taxa de pedidos de HTTP falhados ou chamadas de dependência. Para mais informações, consulte este artigo sobre a [utilização de Smart Detection - Anomalias](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics)de Falha .

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Métricas unificadas e alertas para outros recursos do Azure

Desde março de 2018, a próxima geração de alerta e monitorização multidimensional para os recursos do Azure tem estado disponível. Agora, a nova plataforma métrica e alerta é mais rápida com capacidades quase em tempo real. Mais importante ainda, os alertas de plataforma métrica mais recentes proporcionam mais granularidade, uma vez que a plataforma mais recente inclui a opção de dimensões, que lhe permitem cortar e filtrar para combinação de valor específico, condição ou operação. Tal como todos os alertas no novo Monitor Azure, os novos alertas métricos são mais extensíveis com a utilização do ActionGroups – permitindo que as notificações se expandam para além do e-mail ou webhook para SMS, Voice, Azure Function, Automation Runbook e muito mais. Para mais informações, consulte [Criar, visualizar e gerir alertas métricos utilizando o Monitor Azure.](../../azure-monitor/platform/alerts-metric.md)
Estão disponíveis métricas mais recentes para os recursos Azure:

- **As métricas** da plataforma Azure Monitor Standard – que fornece métricas pré-povoadas populares de vários serviços e produtos Azure. Para mais informações, consulte este artigo sobre [métricas suportadas sobre alertas métricos](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) de Monitor E Suporte Azure [no Monitor Azure](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **As métricas personalizadas do Azure Monitor** – que fornecem métricas de fontes orientadas pelo utilizador, incluindo o agente de Diagnóstico Sinuoso Azure. Para mais informações, consulte este artigo sobre [métricas personalizadas no Monitor Azure](../../azure-monitor/platform/metrics-custom-overview.md). Utilizando métricas personalizadas, também pode publicar métricas recolhidas pelo [agente de Diagnóstico siografado do Windows Azure](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) e [pelo agente Da Telegraf InfluxData](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Aposentadoria da plataforma clássica de monitorização e alerta

Como referido anteriormente, a plataforma clássica de monitorização e alerta atualmente utilizável a partir da [secção alertas (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) do portal Azure será aposentada nos próximos meses, dado que foram substituídos pelo sistema mais recente.
A monitorização e alerta clássicos mais antigos serão retirados a 31 de agosto de 2019; incluindo o encerramento de APIs relacionados, interface de portal Azure e Serviços nele. Especificamente, estas funcionalidades serão depreciadas:

- Métricas e alertas mais antigos (clássicos) para os recursos Do Azure, atualmente disponíveis através da [secção Alerts (clássica)](../../azure-monitor/platform/alerts-classic.overview.md) do portal Azure; acessível como [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) recurso
- Plataforma mais antiga (clássica) e métricas personalizadas para Application Insights, bem como alertá-los como atualmente disponíveis através da [secção alerts (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) do portal Azure e acessíveis como recurso [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Anomalias de falha mais antigas (clássicas) alertam atualmente como [Smart Detection inside Application Insights](../../azure-monitor/app/proactive-diagnostics.md) no portal Azure; com alertas configurados na [secção Alertas (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) do portal Azure

Todos os sistemas clássicos de monitorização e alerta, incluindo a correspondente [API,](https://msdn.microsoft.com/library/azure/dn931945.aspx) [PowerShell,](../../azure-monitor/platform/alerts-classic-portal.md) [CLI,](../../azure-monitor/platform/alerts-classic-portal.md) [página do portal Azure,](../../azure-monitor/platform/alerts-classic-portal.md)e Modelo de [Recursos](../../azure-monitor/platform/alerts-enable-template.md) permanecerão utilizáveis até final de agosto de 2019. 

No final de agosto de 2019, no Monitor Azure:

- O serviço clássico de monitorização e alertas será retirado e deixará de estar disponível para a criação de novas regras de alerta.
- Quaisquer regras de alerta que continuem a existir em Alertas (clássicos) para além de agosto de 2019 continuarão a executar e a disparar notificações, mas não estarão disponíveis para modificação.
- A partir de setembro de 2019, as regras de alerta na monitorização clássica & alerta que podem ser migradas, serão automaticamente transferidas pela Microsoft para o seu equivalente na nova plataforma de monitores Azure em fases que se estendem por algumas semanas. O processo será perfeito sem qualquer tempo de inatividade e os clientes não terão perdas na cobertura de monitorização.
- As regras de alerta migradas para a nova plataforma de alertas fornecerão cobertura de monitorização como antes, mas irão disparar a notificação com novas cargas. Qualquer endereço de e-mail, ponto final do webhook ou ligação lógica de aplicação associada à regra de alerta clássico será transportado para a frente quando migrado, mas pode não se comportar corretamente, uma vez que a carga útil de alerta será diferente na nova plataforma.
- Algumas regras clássicas de [alerta que não podem ser automaticamente migradas](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) e exigem uma ação manual dos utilizadores continuarão a decorrer até junho de 2020.

> [!IMPORTANT]
> O Microsoft Azure Monitor lançou uma ferramenta fases [para migrar voluntariamente](alerts-using-migration-tool.md) as suas regras clássicas de alerta para a nova plataforma em breve. E executá-lo à força para todas as regras clássicas de alerta que ainda existem e podem ser migradas, a partir de setembro de 2019. Os clientes terão de garantir que a automatização que consome a carga útil clássica da regra de alerta seja adaptada para lidar com a nova carga útil das [Métricas Unificadas e Alertas em Insights](#unified-metrics-and-alerts-in-application-insights) de Aplicação ou [Métricas Unificadas e Alertas para outros recursos Azure,](#unified-metrics-and-alerts-for-other-azure-resources)pós-migração das regras clássicas de alerta. Para mais informações, [consulte preparar-se para](alerts-prepare-migration.md) a migração de regras de alerta clássico

Este artigo será continuamente atualizado com links & detalhes sobre a nova funcionalidade de monitorização do Azure &, bem como a disponibilidade de ferramentas para ajudar os utilizadores a adotar a nova plataforma Do Monitor Do Azure.

## <a name="pricing-for-migrated-alert-rules"></a>Preços para regras de alerta migrado

Estamos a lançar uma ferramenta de migração para o ajudar a migrar os seus [alertas clássicos](../../azure-monitor/platform/alerts-classic.overview.md) do Azure Monitor para a experiência dos novos alertas. As regras de alerta migradas e os correspondentes grupos de ação migrado (e-mail, webhook ou LogicApp) permanecerão gratuitos. A funcionalidade que teve com alertas clássicos, incluindo a capacidade de editar o limiar, tipo de agregação, e a granularidade de agregação continuarão disponíveis gratuitamente com a sua regra de alerta migrado. No entanto, se editar a regra de alerta migrado para utilizar qualquer uma das novas funcionalidades, notificações ou tipos de ação da nova plataforma de alerta, aplicar-se-á uma taxa correspondente. Para obter mais informações sobre os preços das regras de alerta e notificações, consulte o [Preço do Monitor do Azure](https://azure.microsoft.com/pricing/details/monitor/).

Seguem-se exemplos de casos em que incorrerá numa acusação pela sua regra de alerta:

- Qualquer nova regra de alerta (não migrada) criada para além das unidades livres, na nova plataforma Do Monitor Azure
- Quaisquer dados ingeridos e retidos para além de unidades gratuitas incluídas pelo Azure Monitor
- Quaisquer testes web de vários testes executados por Application Insights
- Quaisquer métricas personalizadas armazenadas para além de unidades gratuitas incluídas no Monitor Azure
- Quaisquer regras de alerta migradas que sejam editadas para usar novas funcionalidades de alerta métrico, como frequência, múltiplos recursos/dimensões, [Limiares Dinâmicos,](alerts-dynamic-thresholds.md)alteração de recursos/sinal, e assim por diante.
- Quaisquer grupos de ação migrados que sejam editados para usar notificações mais recentes, ou tipos de ação como SMS, Voice Call e/ou integração ITSM.

## <a name="next-steps"></a>Passos seguintes

* Conheça o [novo Monitor Azure unificado.](../../azure-monitor/overview.md)
* Saiba mais sobre os novos [Alertas Azure.](../../azure-monitor/platform/alerts-overview.md)
