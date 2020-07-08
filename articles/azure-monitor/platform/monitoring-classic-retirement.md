---
title: Atualização da monitorização clássica de & de alerta no Azure Monitor
description: Descrição da retirada de serviços e funcionalidades clássicos de monitorização, anteriormente mostrados no portal Azure em Alertas (clássico).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: dec4d6824644cabf8b1872da207b8554fee0b3d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77659480"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Alerta unificado & monitorização no Azure Monitor substitui monitorização clássica de & de alerta

O Azure Monitor tornou-se agora um serviço de monitorização de pilhas completas unificado, que agora suporta "One Metric" e "One Alerts" entre recursos; para mais informações, consulte o nosso [blog post no novo Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). As novas plataformas de monitorização e alerta do Azure foram construídas para serem mais rápidas, inteligentes e extensíveis – acompanhando a expansão crescente da computação em nuvem e em linha com a filosofia microsoft Intelligent Cloud. 

Com a nova plataforma de monitorização e alerta do Azure em vigor, vamos retirar a plataforma de monitorização e alerta "clássica" - hospedada na secção de *alertas clássicos* de alertas Azure, **será depreciada até agosto de 2019 nas nuvens públicas do Azure.** [A nuvem do Governo de Azure](../../azure-government/documentation-government-welcome.md) e [Azure China 21Vianet](https://docs.azure.cn/) não serão afetadas.

> [!NOTE]
> Devido ao atraso no lançamento da ferramenta de migração, a data de aposentação dos alertas clássicos de migração foi alargada até 31 de agosto de [2019 a](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) partir da data inicialmente anunciada de 30 de junho de 2019.

 ![Alerta clássico no portal Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Encorajamo-lo a começar e a recriar os seus alertas na nova plataforma. Para os clientes que têm um grande número de alertas, estamos [a lançar fases](alerts-understand-migration.md#rollout-phases), uma [ferramenta de migração voluntária](alerts-using-migration-tool.md) para mover os alertas clássicos existentes para o novo sistema de alertas sem interrupções ou custos adicionais.

> [!IMPORTANT]
> As regras clássicas de alerta criadas no Registo de Atividades não serão depreciadas ou migradas. Todas as regras clássicas de alerta criadas no Registo de Atividades podem ser acedidas e usadas como é do novo Azure Monitor - Alertas. Para obter mais informações, consulte [Criar, visualizar e gerir alertas de registo de atividade usando o Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md). Da mesma forma, os alertas sobre a saúde do serviço podem ser acedidos e usados como é da nova secção de Saúde do Serviço. Para mais informações, consulte [alertas sobre as notificações de saúde do serviço.](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Métricas e Alertas Unificados em Insights de Aplicações

A plataforma métrica mais recente do Azure Monitor passará a ser monitorizada por energia proveniente da Application Insights. Este movimento significa que os Application Insights ligar-se-ão a Grupos de Ação, permitindo muito mais opções do que apenas as chamadas de e-mail e webhook anteriores. Os alertas podem agora desencadear chamadas de voz, funções Azure, Aplicações Lógicas, SMS e FERRAMENTAS ITSM como ServiceNow e Cartões de Automação. Com a monitorização e alerta em tempo real, a nova plataforma permite que os utilizadores do Application Insights aproveitem a mesma monitorização de energia tecnológica através de outros recursos da Azure e subjacentes à monitorização dos produtos da Microsoft.

O novo acompanhamento e alerta unificado para insights de aplicação abrangerá:

- **Métricas da Plataforma Application Insights** – que fornece métricas pré-incorporadas populares do produto Application Insights. Para obter mais informações, consulte este artigo sobre a utilização [de Métricas da Plataforma para Insights de Aplicações no novo Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Aplicação Insights Disponibilidade e teste Web** - que lhe proporciona a capacidade de avaliar a capacidade de avaliar a capacidade de resposta e disponibilidade da sua aplicação web ou servidor. Para obter mais informações, consulte este artigo sobre a utilização [de Testes de Disponibilidade e Alertas para Informações sobre aplicações no novo Monitor Azure.](../../azure-monitor/app/monitor-web-app-availability.md)
- **Application Insights Métricas personalizadas** – que permite definir e emitir as suas próprias métricas para monitorização e alertas. Para obter mais informações, consulte este artigo sobre a utilização [de Métrica Personalizada para Insights de Aplicações no novo Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomalias de falha de deteção de aplicações (parte da Deteção Inteligente)** – que o notifica automaticamente em tempo real se a sua aplicação web experimentar um aumento anormal na taxa de pedidos http falhados ou chamadas de dependência. Para obter mais informações, consulte este artigo sobre a [utilização de Smart Detection - Anomalias de falha](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Métricas e Alertas Unificados para outros recursos da Azure

Desde março de 2018, a próxima geração de alertas e monitorização multidimensional para os recursos do Azure tem estado disponível. Agora, a plataforma métrica mais recente e alerta é mais rápida com capacidades quase em tempo real. Mais importante ainda, os alertas mais recentes da plataforma métrica proporcionam mais granularidade, uma vez que a plataforma mais recente inclui a opção de dimensões, que permitem cortar e filtrar para combinação, condição ou funcionamento específicos de valor. Tal como todos os alertas do novo Azure Monitor, os alertas métricos mais recentes são mais extensíveis com a utilização de ActionGroups – permitindo que as notificações se expandam para além do email ou webhook para SMS, Voz, Função Azure, Manual de Automação e muito mais. Para obter mais informações, consulte [Criar, ver e gerir alertas métricos utilizando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).
As métricas mais recentes para os recursos Azure estão disponíveis como:

- **Métricas da plataforma Azure Monitor Standard** – que fornece métricas pré-povoadas populares de vários serviços e produtos Azure. Para obter mais informações, consulte este artigo sobre [métricas suportadas em alertas](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) [métricos Azure](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts)Monitor e Support no Azure Monitor .
- **Métricas personalizadas do Azure Monitor** – que fornecem métricas de fontes orientadas pelo utilizador, incluindo o agente Azure Diagnostics. Para mais informações, consulte este artigo sobre [métricas personalizadas no Azure Monitor.](../../azure-monitor/platform/metrics-custom-overview.md) Utilizando métricas personalizadas, também pode publicar métricas recolhidas pelo [agente de diagnóstico do Windows Azure e](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) pelo agente [InfluxData Telegraf.](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md)

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Plataforma de monitorização e alerta clássico de aposentadoria

Como anteriormente afirmado, a plataforma clássica de monitorização e alerta atualmente utilizável a partir da [secção alertas (clássicos)](../../azure-monitor/platform/alerts-classic.overview.md) do portal Azure será retirada nos próximos meses, dado que foram substituídas pelo sistema mais recente.
A monitorização e alerta clássicos mais antigos será retirado no dia 31 de agosto de 2019; incluindo o encerramento de APIs relacionados, interface do portal Azure e Serviços nele. Especificamente, estas características serão depreciadas:

- Métricas e alertas mais antigos (clássicos) para recursos Azure, como atualmente disponível através da [secção Alerts (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) do portal Azure; acessível como [recurso microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Plataforma mais antiga (clássica) e métricas personalizadas para Insights de Aplicações, bem como alertando-os como atualmente disponível através da [secção alerts (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) do portal Azure e acessível como [recurso microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Alerta de anomalias de falha mais antigas (clássicas) atualmente disponíveis como [Smart Detection inside Application Insights](../../azure-monitor/app/proactive-diagnostics.md) no portal Azure; com alertas configurados mostrados na [secção alertas (clássicos)](../../azure-monitor/platform/alerts-classic.overview.md) do portal Azure

Todos os sistemas clássicos de monitorização e alerta, incluindo [a API](https://msdn.microsoft.com/library/azure/dn931945.aspx)correspondente, [PowerShell,](../../azure-monitor/platform/alerts-classic-portal.md) [CLI,](../../azure-monitor/platform/alerts-classic-portal.md) [página do portal Azure](../../azure-monitor/platform/alerts-classic-portal.md)e [Modelo de Recursos](../../azure-monitor/platform/alerts-enable-template.md) permanecerão utilizáveis até final de agosto de 2019. 

No final de agosto de 2019, no Azure Monitor:

- O serviço clássico de monitorização e alertas será reformado e deixará de estar disponível para a criação de novas regras de alerta.
- Quaisquer regras de alerta que continuem a existir em Alertas (clássicos) para além de agosto de 2019 continuarão a ser executadas e notificações de incêndio, mas não estarão disponíveis para modificação.
- A partir de setembro de 2019, as regras de alerta em & de monitorização clássica que podem ser migradas, serão automaticamente transferidas pela Microsoft para o seu equivalente na nova plataforma de monitores Azure, em fases que se estendem por algumas semanas. O processo será perfeito sem qualquer tempo de inatividade e os clientes não terão qualquer perda na cobertura de monitorização.
- As regras de alerta migradas para a nova plataforma de alertas fornecerão cobertura de monitorização como antes, mas irão disparar a notificação com novas cargas. Qualquer endereço de e-mail, ponto final webhook ou link de aplicação lógica associado à regra de alerta clássico será transportado para a frente quando migrado, mas pode não se comportar corretamente como carga útil de alerta será diferente na nova plataforma.
- Algumas [regras clássicas](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) de alerta que não podem ser migradas automaticamente e que exigem ação manual dos utilizadores continuarão a decorrer até junho de 2020.

> [!IMPORTANT]
> O Microsoft Azure Monitor lançou em fases [a ferramenta para migrar voluntariamente](alerts-using-migration-tool.md) as suas regras clássicas de alerta para a nova plataforma em breve. E executá-lo à força para todas as regras clássicas de alerta que ainda existem e podem ser migradas, a partir de setembro de 2019. Os clientes terão de garantir que a automatização que consome a carga clássica da regra de alerta é adaptada para lidar com a nova carga útil das [Métricas Unificadas e Alertas em Insights](#unified-metrics-and-alerts-in-application-insights) de Aplicação ou [Métricas e Alertas Unificados para outros recursos Azure,](#unified-metrics-and-alerts-for-other-azure-resources)pós-migração das regras clássicas de alerta. Para mais informações, consulte [preparar-se para a migração clássica da regra de alerta](alerts-prepare-migration.md)

Este artigo será continuamente atualizado com links & detalhes sobre a nova funcionalidade de monitorização do Azure & alertando, bem como a disponibilização de ferramentas para ajudar os utilizadores na adoção da nova plataforma Azure Monitor.

## <a name="pricing-for-migrated-alert-rules"></a>Preços das regras de alerta migratório

Estamos a lançar uma ferramenta de migração para o ajudar a migrar os [seus alertas clássicos](../../azure-monitor/platform/alerts-classic.overview.md) do Azure Monitor para a nova experiência de alertas. As regras de alerta migradas e os correspondentes grupos de ação migrados (e-mail, webhook ou LogicApp) permanecerão gratuitos. A funcionalidade que tinha com alertas clássicos, incluindo a capacidade de editar o limiar, tipo de agregação, e a granularidade agregação continuarão disponíveis gratuitamente com a sua regra de alerta migrado. No entanto, se editar a regra de alerta migrado para utilizar qualquer uma das novas funcionalidades da plataforma de alerta, notificações ou tipos de ação, será aplicada uma taxa correspondente. Para obter mais informações sobre os preços das regras de alerta e notificações, consulte [a Azure Monitor Pricing](https://azure.microsoft.com/pricing/details/monitor/).

Seguem-se exemplos de casos em que incorrerá numa cobrança pela sua regra de alerta:

- Qualquer nova regra de alerta (não migrada) criada para além das unidades livres, na nova plataforma Azure Monitor
- Quaisquer dados ingeridos e retidos para além das unidades gratuitas incluídas pelo Azure Monitor
- Quaisquer testes web de vários testes executados pela Application Insights
- Quaisquer métricas personalizadas armazenadas para além de unidades gratuitas incluídas no Azure Monitor
- Quaisquer regras de alerta migradas que sejam editadas para usar funcionalidades de alerta métrica mais recentes como frequência, múltiplos recursos/dimensões, [Limiares Dinâmicos,](alerts-dynamic-thresholds.md)alteração de recursos/sinal, e assim por diante.
- Quaisquer grupos de ação migrados que sejam editados para utilizar notificações mais recentes, ou tipos de ação como a integração DE SMS, Voice Call e/ou ITSM.

## <a name="next-steps"></a>Próximos passos

* Conheça o [novo Azure Monitor unificado.](../../azure-monitor/overview.md)
* Saiba mais sobre os novos [Alertas Azure.](../../azure-monitor/platform/alerts-overview.md)
