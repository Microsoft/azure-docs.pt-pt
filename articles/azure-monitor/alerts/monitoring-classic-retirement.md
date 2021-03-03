---
title: Atualização da monitorização clássica de & de alerta no Azure Monitor
description: Descrição da retirada de serviços e funcionalidades clássicos de monitorização, anteriormente mostrados no portal Azure em Alertas (clássico).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: a862e54b5e1eddce5fcabf050eba1cfa1f103e30
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734762"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Alerta unificado & monitorização no Azure Monitor substitui monitorização clássica de & de alerta

O Azure Monitor é uma pilha de monitorização unificada que suporta os recursos de Azure 'One Metric' e 'One Alerts'. Veja mais informações neste [post de blog.](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/) As novas plataformas de monitorização e alerta do Azure foram construídas para serem mais rápidas, inteligentes e extensíveis – acompanhando a expansão crescente da computação em nuvem e em linha com a filosofia microsoft Intelligent Cloud.

Com a nova plataforma de monitorização e alerta do Azure em vigor, os alertas clássicos no Azure Monitor são retirados para utilizadores de nuvem pública, embora ainda em uso limitado até **31 de maio de 2021**. Os alertas clássicos para a nuvem do Governo Azure e para o Azure China 21Vianet vão reformar-se a **29 de fevereiro de 2024**.

 ![Alerta clássico no portal Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Encorajamo-lo a começar e a recriar os seus alertas na nova plataforma.

> [!IMPORTANT]
> As regras clássicas de alerta criadas no Registo de Atividades não serão depreciadas ou migradas. Todas as regras clássicas de alerta criadas no Registo de Atividades podem ser acedidas e usadas como é do novo Azure Monitor - Alertas. Para obter mais informações, consulte [Criar, visualizar e gerir alertas de registo de atividade usando o Azure Monitor](./alerts-activity-log.md). Da mesma forma, os alertas sobre a saúde do serviço podem ser acedidos e usados como é da nova secção de Saúde do Serviço. Para mais informações, consulte [alertas sobre as notificações de saúde do serviço.](../../service-health/alerts-activity-log-service-notifications-portal.md)

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Métricas e Alertas Unificados para recursos Azure

Em março de 2018, lançámos a próxima geração de alertas para os recursos da Azure. A plataforma métrica mais recente e alerta é mais rápida e proporciona mais granularidade usando dimensões. As dimensões permitem cortar e filtrar para combinação de valor específico, condição ou funcionamento. Os alertas métricos mais recentes utilizam grupos de ação permitindo mais notificações e ações de automação. Consulte mais informações sobre [a gestão de alertas métricos utilizando o Azure Monitor](./alerts-metric.md).

As métricas mais recentes para os recursos Azure estão disponíveis como:

- **Métricas da plataforma Azure Monitor Standard** – que fornece métricas pré-povoadas populares de vários serviços e produtos Azure. Para obter mais informações, consulte este artigo sobre [métricas suportadas em alertas](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) [métricos Azure](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts)Monitor e Support no Azure Monitor .
- **Métricas personalizadas do Azure Monitor** – que fornecem métricas de fontes orientadas pelo utilizador, incluindo o agente Azure Diagnostics. Para mais informações, consulte este artigo sobre [métricas personalizadas no Azure Monitor.](../essentials/metrics-custom-overview.md) Utilizando métricas personalizadas, também pode publicar métricas recolhidas pelo [agente de diagnóstico do Windows Azure e](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) pelo agente [InfluxData Telegraf.](../essentials/collect-custom-metrics-linux-telegraf.md)

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Plataforma de monitorização e alerta clássico de aposentadoria

Como anteriormente afirmado, a monitorização e o alerta clássicos mais antigos são retirados para os utilizadores de nuvem pública. Inclui o encerramento de APIs relacionados, interface do portal Azure e serviços nele, embora ainda em uso limitado até **31 de maio de 2021**. Os alertas clássicos para a nuvem do Governo Azure e para o Azure China 21Vianet vão reformar-se a **29 de fevereiro de 2024**.

Especificamente, o âmbito de aposentadoria é para métricas clássicas atualmente disponíveis na [secção Alertas (clássico)](./alerts-classic.overview.md) do portal Azure e acessível como [recursos microsoft.insights/alertrules.](/rest/api/monitor/alertrules)

Isto significa:

- O serviço clássico de monitorização e alertas será reformado e deixará de estar disponível para a criação de novas regras de alerta.
- Quaisquer regras de alerta que continuem a existir em Alertas (clássicos) continuarão a executar e a disparar notificações.
- A maioria das regras clássicas de alerta serão migradas. O processo será perfeito sem qualquer tempo de inatividade e os clientes não terão qualquer perda na cobertura de monitorização.
- As notificações disparadas incluirão uma nova estrutura de cargas. O alvo terá de ser adaptado para trabalhar com a nova estrutura.
- Algumas [regras clássicas](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) de alerta que não podem ser migradas automaticamente e exigirão que a ação manual dos utilizadores continue a funcionar.

> [!IMPORTANT]
> O Azure Monitor lançou uma [ferramenta para migrar voluntariamente](alerts-using-migration-tool.md) as suas regras clássicas de alerta para a nova plataforma. As restantes regras serão migradas automaticamente assim que o serviço for retirado. Os clientes terão de garantir que a automatização que consome a carga clássica da regra de alerta é adaptada para lidar com a nova carga útil das [Métricas Unificadas e Alertas para outros recursos Azure,](#unified-metrics-and-alerts-for-azure-resources)após a migração das regras clássicas de alerta. Para mais informações, consulte [preparar-se para a migração clássica da regra de alerta.](alerts-prepare-migration.md)

## <a name="pricing-for-migrated-alert-rules"></a>Preços das regras de alerta migratório

Estamos a lançar uma ferramenta de migração para ajudá-lo a migrar os [seus alertas clássicos](./alerts-classic.overview.md) do Azure Monitor para a nova experiência de alertas. As regras de alerta migradas e os correspondentes grupos de ação migrados (e-mail, webhook ou LogicApp) permanecerão gratuitos. A funcionalidade que tinha com alertas clássicos, incluindo a capacidade de editar o limiar, tipo de agregação, e a granularidade agregação continuarão disponíveis gratuitamente com a sua regra de alerta migrado. No entanto, se editar a regra de alerta migrado para utilizar qualquer uma das novas funcionalidades da plataforma de alerta, notificações ou tipos de ação, será aplicada uma taxa correspondente. Para obter mais informações sobre os preços das regras de alerta e notificações, consulte [a Azure Monitor Pricing](https://azure.microsoft.com/pricing/details/monitor/).

Seguem-se exemplos de casos em que incorrerá numa cobrança pela sua regra de alerta:

- Qualquer nova regra de alerta (não migrada) criada para além das unidades livres, na nova plataforma Azure Monitor
- Quaisquer dados ingeridos e retidos para além das unidades gratuitas incluídas pelo Azure Monitor
- Quaisquer testes web de vários testes executados pela Application Insights
- Quaisquer métricas personalizadas armazenadas para além de unidades gratuitas incluídas no Azure Monitor
- Quaisquer regras de alerta migradas que sejam editadas para usar funcionalidades de alerta métrica mais recentes como frequência, múltiplos recursos/dimensões, [Limiares Dinâmicos,](../alerts/alerts-dynamic-thresholds.md)alteração de recursos/sinal, e assim por diante.
- Quaisquer grupos de ação migrados que sejam editados para utilizar notificações mais recentes, ou tipos de ação como a integração DE SMS, Voice Call e/ou ITSM.

## <a name="next-steps"></a>Passos seguintes

* Conheça o [novo Azure Monitor unificado.](../overview.md)
* Saiba mais sobre os novos [Alertas Azure.](./alerts-overview.md)