---
title: Visão geral dos alertas clássicos no Monitor Azure
description: Os alertas clássicos estão a ser depreciados. Os alertas permitem-lhe monitorizar as métricas, eventos ou registos de recursos do Azure e ser notificado quando uma condição que especifica for satisfeita.
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: e9c269db870f582c176783a4654b5de251e24412
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114490"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>O que são alertas clássicos no Microsoft Azure?

> [!NOTE]
> Este artigo descreve como criar alertas métricos clássicos mais antigos. O Azure Monitor suporta agora [novos alertas métricos em tempo real e uma nova experiência de alertas.](../../azure-monitor/platform/alerts-overview.md) Os alertas clássicos são [retirados](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement), embora ainda em uso limitado para recursos que ainda não suportam os novos alertas. 
>

Os alertas permitem configurar as condições sobre os dados e ser notificado quando as condições correspondem aos dados de monitorização mais recentes.

## <a name="old-and-new-alerting-capabilities"></a>Antigas e novas capacidades de alerta

No passado, o Azure Monitor, application Insights, Log Analytics e Service Health tinham capacidades de alerta separadas. Horas extraordinárias, o Azure melhorou e combinou tanto a interface do utilizador como os diferentes métodos de alerta. A consolidação ainda está em curso.

Só é possível visualizar alertas clássicos no ecrã clássico dos alertas no Portal Azure. Obtém este ecrã a partir do botão de **alertaclássico Ver** no ecrã de alertas. 

 ![Escolhas de alerta no portal Azure](media/alerts-classic.overview/monitor-alert-screen2.png)

Os novos alertas a experiência do utilizador tem os seguintes benefícios sobre a experiência de alertas clássicos:
- **Melhor sistema de notificação** - Todos os alertas mais recentes utilizam grupos de ação, que são nomeados grupos de notificações e ações que podem ser reutilizadas em vários alertas. Os alertas métricos clássicos e os alertas de Log Analytics mais antigos não utilizam grupos de ação.
- **Uma experiência de autoria unificada** - Toda a criação de alerta para métricas, registos e registo de atividade através do Azure Monitor, Log Analytics e Application Insights está num só local.
- **Ver alertas de Log Analytics disparados no portal Azure** - Agora também pode ver alertas de Log Analytics disparados na sua subscrição. Anteriormente, estes estavam num portal separado.
- **Separação de alertas e regras de alerta disparados** - Regras de alerta (a definição de condição que desencadeia um alerta) e Alertas de Incêndio (instância da regra de alerta de disparo) são diferenciados, pelo que as vistas operacionais e de configuração são separadas.
- **Melhor fluxo de trabalho** - Os novos alertas que autorizam a experiência orienta o utilizador ao longo do processo de configuração de uma regra de alerta, o que torna mais simples descobrir as coisas certas para ser alertado.
- Estado de **consolidação** e alerta de alerta **smart Alerts** - Os alertas mais recentes incluem funcionalidade de agrupamento automático que mostra alertas semelhantes em conjunto para reduzir a sobrecarga na interface do utilizador. 

Os novos alertas métricos têm os seguintes benefícios sobre os alertas métricos clássicos:
- **Latência melhorada**: Os alertas métricos mais recentes podem ser executados com a mesma frequência a cada minuto. Os alertas métricos mais antigos funcionam sempre a uma frequência de 5 minutos. Os alertas mais recentes têm aumentado o atraso da ocorrência de emissão para notificação ou ação (3 a 5 minutos). Os alertas mais antigos são de 5 a 15 minutos, dependendo do tipo.  Os alertas de log normalmente têm um atraso de 10 a 15 minutos devido ao tempo que leva para ingerir os registos, mas os métodos de processamento mais recentes estão reduzindo esse tempo. 
- **Suporte para métricas multidimensionais:** Pode alertar sobre métricas dimensionais que lhe permitem monitorizar um segmento interessante da métrica.
- **Mais controlo sobre as condições métricas:** Pode definir regras de alerta mais ricas. Os novos alertas de suporte monitorizando os valores máximos, mínimos, médios e totais das métricas.
- **Monitorização combinada de múltiplas métricas:** Pode monitorizar várias métricas (atualmente, até duas métricas) com uma única regra. É desencadeado um alerta se ambas as métricas violarem os respetivos limiares durante o período de tempo especificado.
- **Melhor sistema de notificação**: Todos os alertas mais recentes utilizam grupos de [ação,](../../azure-monitor/platform/action-groups.md)que são nomeados grupos de notificações e ações que podem ser reutilizadas em múltiplos alertas.  Os alertas métricos clássicos e os alertas de Log Analytics mais antigos não utilizam grupos de ação. 
- **Métricas de Registos** (pré-visualização pública): Os dados de log indo para log Analytics podem agora ser extraídos e convertidos em métricas do Monitor Azure e, em seguida, alertados em outras métricas. Consulte [alertas (clássicos)](alerts-classic.overview.md) para a terminologia específica dos alertas clássicos. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Alertas clássicos sobre dados do Monitor do Azure
Existem dois tipos de alertas clássicos disponíveis - alertas métricos e alertas de registo de atividade.

* **Alertas métricos clássicos** - Este alerta dispara quando o valor de uma métrica especificada atravessa um limiar que atribui. O alerta gera uma notificação quando esse limiar é ultrapassado e a condição de alerta é satisfeita. Nessa altura, o alerta é considerado "Ativado". Gera outra notificação quando é "Resolvida" - isto é, quando o limiar é ultrapassado novamente e a condição já não é cumprida.

* **Alertas clássicos** de registo de atividade - Um alerta de log de streaming que dispara numa entrada de evento de Registo de Atividadeque corresponde aos critérios do filtro. Estes alertas têm apenas um estado, "Ativado". O motor de alerta aplica simplesmente os critérios de filtro a qualquer novo evento. Não procura encontrar entradas mais antigas. Estes alertas podem notificá-lo quando ocorre um novo incidente de Saúde de Serviço ou quando um utilizador ou aplicação executa uma operação na sua subscrição, por exemplo, "Eliminar a máquina virtual".

Para obter dados de registo de recursos disponíveis através do Monitor Azure, encaminhe os dados para o Log Analytics e utilize um alerta de consulta de registo. Log Analytics agora usa o [novo método de alerta](../../azure-monitor/platform/alerts-overview.md) 

O diagrama seguinte resume fontes de dados no Monitor Azure e, conceptualmente, como pode alertar para fora desses dados.

![Alertas explicados](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomia de alertas (clássico)
O Azure usa os seguintes termos para descrever alertas clássicos e as suas funções:
* **Alerta** - uma definição de critérios (uma ou mais regras ou condições) que é ativada quando satisfeita.
* **Ativo** - o estado em que os critérios definidos por um alerta clássico são cumpridos.
* **Resolvido** - o estado em que os critérios definidos por um alerta clássico já não são cumpridos depois de terem sido cumpridos anteriormente.
* **Notificação** - a ação tomada com base num alerta clássico que se tornou ativa.
* **Ação** - uma chamada específica enviada a um recetor de uma notificação (por exemplo, enviando um endereço ou postando para um URL webhook). As notificações podem normalmente desencadear múltiplas ações.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Como recebo uma notificação de um alerta clássico do Monitor Azure?
Historicamente, os alertas azure de diferentes serviços usaram os seus próprios métodos de notificação incorporados. 

O Azure Monitor criou um agrupamento de notificações reutilizável chamado grupos de *ação.* Os grupos de ação especificam um conjunto de recetores para uma notificação. Sempre que é ativado um alerta que faz referência ao Grupo de Ação, todos os recetores recebem essa notificação. Os grupos de ação permitem-lhe reutilizar um agrupamento de recetores (por exemplo, a sua lista de engenheiros de chamadas) em muitos objetos de alerta. Os grupos de ação suportam a notificação publicando um URL webhook para além de endereços de e-mail, números de SMS e uma série de outras ações.  Para mais informações, consulte [grupos de ação](../../azure-monitor/platform/action-groups.md). 

Os alertas de Log de Atividade clássicos mais antigos usam grupos de ação.

No entanto, os alertas métricos mais antigos não utilizam grupos de ação. Em vez disso, pode configurar as seguintes ações: 
- Envie notificações de e-mail ao administrador de serviço, a coadministradores ou a endereços de e-mail adicionais que especifique.
- Ligue para um webhook, que lhe permite lançar ações adicionais de automação.

Webhooks permite automação e reparação, por exemplo, usando:
- Runbook da Automatização do Azure
- Função do Azure
- App Lógica Azure
- um serviço de terceiros

## <a name="next-steps"></a>Passos seguintes
Obtenha informações sobre as regras de alerta e configurá-las utilizando:

* Saiba mais sobre [Métricas](data-platform.md)
* Configure [alertas métricos clássicos através](alerts-classic-portal.md) do portal Azure
* Configurar [alertas métricos clássicos PowerShell](alerts-classic-portal.md)
* Configurar a [interface clássica de linha de comando de alertas métricos (CLI)](alerts-classic-portal.md)
* Configurar [alertas métricos clássicos Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Saiba mais sobre [o Registo de Atividades](platform-logs-overview.md)
* Configurar alertas de registo de atividade através do [portal Azure](activity-log-alerts.md)
* Configurar [alertas](alerts-activity-log.md) de registo de atividade através do Gestor de Recursos
* Reveja o esquema de alerta de registo de [atividade webhook](activity-log-alerts-webhook.md)
* Saiba mais sobre [grupos de ação](action-groups.md)
* Configurar [novos alertas](alerts-metric.md)
