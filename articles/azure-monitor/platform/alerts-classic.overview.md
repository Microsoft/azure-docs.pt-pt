---
title: Visão geral dos alertas clássicos no Azure Monitor
description: Os alertas clássicos estão a ser depreciados. Os alertas permitem-lhe monitorizar métricas, eventos ou registos de recursos do Azure e ser notificado quando uma condição especificada for cumprida.
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: c7dfbd60b7a668b849c3150bb16e40285c345634
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87844118"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>O que são alertas clássicos no Microsoft Azure?

> [!NOTE]
> Este artigo descreve como criar alertas métricos clássicos mais antigos. O Azure Monitor suporta agora [novos alertas métricos em tempo real e uma nova experiência de alertas.](./alerts-overview.md) Os alertas clássicos são [retirados](./monitoring-classic-retirement.md), embora ainda em uso limitado para recursos que ainda não suportam os novos alertas.
>

Os alertas permitem configurar condições sobre os dados e ser notificado quando as condições correspondem aos dados de monitorização mais recentes.

## <a name="old-and-new-alerting-capabilities"></a>Capacidades de alerta antigas e novas

No passado, o Azure Monitor, Application Insights, Log Analytics e Service Health tinham capacidades de alerta separadas. Horas extras, O Azure melhorou e combinou tanto a interface do utilizador como os diferentes métodos de alerta. A consolidação ainda está em curso.

Só é possível ver alertas clássicos no ecrã do utilizador dos alertas clássicos no Portal Azure. Obtém este ecrã do botão **de alerta clássico do View** no ecrã de alertas. 

 ![Escolhas de alerta no portal Azure](media/alerts-classic.overview/monitor-alert-screen2.png)

A experiência do utilizador dos novos alertas tem os seguintes benefícios sobre a experiência de alertas clássicos:
- **Melhor sistema de notificação** - Todos os alertas mais recentes utilizam grupos de ação, que são nomeados grupos de notificações e ações que podem ser reutilizados em vários alertas. Os alertas métricos clássicos e os alertas mais antigos do Log Analytics não utilizam grupos de ação.
- **Uma experiência de autoria unificada** - Toda a criação de alerta para métricas, registos e registos de atividades através do Azure Monitor, Log Analytics e Application Insights está num só local.
- **Ver alertas de Log Analytics disparados no portal Azure** - Agora também pode ver alertas de Log Analytics disparados na sua subscrição. Anteriormente, estes estavam num portal separado.
- **Separação de alertas e regras de alerta** - Regras de alerta (definição de estado que desencadeia um alerta) e Alertas de Incêndio (uma instância do disparo da regra de alerta) são diferenciados, pelo que as vistas operacionais e de configuração são separadas.
- **Melhor fluxo de trabalho** - A experiência de autoria de novos alertas guia o utilizador ao longo do processo de configuração de uma regra de alerta, o que torna mais simples descobrir as coisas certas para ser alertado.
- O estado de **consolidação** e **definição** de alerta de alerta de alerta de alerta inteligente - os alertas mais recentes incluem a funcionalidade de agrupamento de automóveis que mostra alertas semelhantes em conjunto para reduzir a sobrecarga na interface do utilizador. 

Os alertas métricos mais recentes têm os seguintes benefícios sobre os alertas métricos clássicos:
- **Latência melhorada**: Os alertas métricos mais recentes podem funcionar com a mesma frequência que um minuto. Os alertas métricos mais antigos funcionam sempre a uma frequência de 5 minutos. Os alertas mais recentes têm aumentado o atraso da ocorrência de emissão para notificação ou ação (3 a 5 minutos). Os alertas mais antigos são de 5 a 15 minutos dependendo do tipo.  Os alertas de registo normalmente têm um atraso de 10 a 15 minutos devido ao tempo que leva para ingerir os registos, mas os métodos de processamento mais recentes estão a reduzir esse tempo. 
- **Suporte para métricas multidimensionais**: Pode alertar em métricas dimensionais permitindo-lhe monitorizar um segmento interessante da métrica.
- **Mais controlo sobre as condições métricas**: Pode definir regras de alerta mais ricas. Os alertas mais recentes de apoio à monitorização dos valores máximos, mínimos, médios e totais das métricas.
- **Monitorização combinada de várias métricas**: Pode monitorizar várias métricas (atualmente, até duas métricas) com uma única regra. Um alerta é desencadeado se ambas as métricas violarem os respetivos limiares para o período de tempo especificado.
- **Melhor sistema de notificação**: Todos os alertas mais recentes utilizam grupos de [ação,](./action-groups.md)que são nomeados grupos de notificações e ações que podem ser reutilizados em vários alertas.  Os alertas métricos clássicos e os alertas mais antigos do Log Analytics não utilizam grupos de ação. 
- **Métricas de Logs** (pré-visualização pública): Os dados de registo que vão para o Log Analytics podem agora ser extraídos e convertidos em métricas do Monitor Azure e depois alertados como outras métricas. Consulte [alertas (clássicos)]() para a terminologia específica aos alertas clássicos. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Alertas clássicos sobre os dados do Azure Monitor
Existem dois tipos de alertas clássicos disponíveis - alertas métricos e alertas de registo de atividade.

* **Alertas métricos clássicos** - Este alerta dispara quando o valor de uma métrica especificada cruza um limiar que atribui. O alerta gera uma notificação quando esse limiar é cruzado e a condição de alerta é cumprida. Nessa altura, o alerta é considerado "Ativado". Gera outra notificação quando é "resolvida" - isto é, quando o limiar é ultrapassado novamente e a condição já não é cumprida.

* **Alertas clássicos de registo de atividade -** Um alerta de registo de streaming que desencadeia uma entrada de evento de Registo de Atividade que corresponde aos critérios do filtro. Estes alertas têm apenas um estado, "Ativado". O motor de alerta aplica simplesmente os critérios do filtro a qualquer evento novo. Não procura entradas mais antigas. Estes alertas podem notificá-lo quando ocorre um novo incidente de Saúde de Serviço ou quando um utilizador ou aplicação executa uma operação na sua subscrição, por exemplo, "Eliminar máquina virtual".

Para os dados de registo de recursos disponíveis através do Azure Monitor, encaminhe os dados para o Log Analytics e utilize um alerta de consulta de registo. Log Analytics agora usa o [novo método de alerta](./alerts-overview.md) 

O diagrama seguinte resume as fontes de dados no Azure Monitor e, conceptualmente, como pode alertar esses dados.

![Alertas explicados](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomia de alertas (clássico)
A Azure usa os seguintes termos para descrever alertas clássicos e suas funções:
* **Alerta** - uma definição de critérios (uma ou mais regras ou condições) que se torna ativado quando cumprido.
* **Ativo** - o estado quando os critérios definidos por um alerta clássico são cumpridos.
* **Resolvido** - o estado em que os critérios definidos por um alerta clássico já não são cumpridos depois de ter sido previamente cumprido.
* **Notificação** - a ação tomada com base num alerta clássico que se torna ativo.
* **Ação** - uma chamada específica enviada a um recetor de uma notificação (por exemplo, enviar um endereço ou enviar para um URL webhook). As notificações podem normalmente desencadear múltiplas ações.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Como recebo uma notificação de um alerta clássico do Azure Monitor?
Historicamente, os alertas do Azure de diferentes serviços usaram os seus próprios métodos de notificação incorporados. 

O Azure Monitor criou um agrupamento de notificações reutilizável chamado *grupos de ação*. Os grupos de ação especificam um conjunto de recetores para uma notificação. Sempre que um alerta é ativado que faz referência ao Grupo de Ação, todos os recetores recebem essa notificação. Os grupos de ação permitem-lhe reutilizar um agrupamento de recetores (por exemplo, a sua lista de engenheiros de chamada) através de muitos objetos de alerta. Grupos de ação suportam a notificação publicando num URL webhook, além de endereços de e-mail, números de SMS e uma série de outras ações.  Para mais informações, consulte [os grupos de ação.](./action-groups.md) 

Os alertas clássicos de registo de atividade mais antigos utilizam grupos de ação.

No entanto, os alertas métricos mais antigos não utilizam grupos de ação. Em vez disso, pode configurar as seguintes ações: 
- Envie notificações por e-mail ao administrador de serviço, aos coadministradores ou a endereços de e-mail adicionais que especifique.
- Ligue para um webhook, que lhe permite lançar ações adicionais de automação.

Webhooks permite automação e remediação, por exemplo, utilizando:
- Runbook da Automatização do Azure
- Função do Azure
- Azure Logic App
- um serviço de terceiros

## <a name="next-steps"></a>Passos seguintes
Obtenha informações sobre as regras de alerta e configurando-as utilizando:

* Saiba mais sobre [Métricas](data-platform.md)
* Configurar [alertas métricos clássicos via portal Azure](alerts-classic-portal.md)
* Configurar [alertas métricos clássicos PowerShell](alerts-classic-portal.md)
* Configure a [interface clássica de linha de comando de alertas métricos (CLI)](alerts-classic-portal.md)
* Configurar [métrica clássica alertas Azure Monitor REST API](/rest/api/monitor/alertrules)
* Saiba mais sobre [O Registo de Atividades](platform-logs-overview.md)
* Configure [alertas de registo de atividade através do portal Azure](activity-log-alerts.md)
* Configure [alertas de registo de atividades através do Gestor de Recursos](alerts-activity-log.md)
* Reveja o [esquema de alerta de atividade webhook](activity-log-alerts-webhook.md)
* Saiba mais sobre [grupos de ação](action-groups.md)
* Configurar [novos alertas](alerts-metric.md)

