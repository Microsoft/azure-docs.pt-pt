---
title: Visão geral dos alertas clássicos no Microsoft Azure e Azure Monitor
description: Os alertas clássicos estão sendo preteridos. Os alertas permitem que você monitore as métricas, eventos ou logs de recursos do Azure e seja notificado quando uma condição especificada for atendida.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 05/19/2018
ms.openlocfilehash: 177c110ce9679f3d564918dfac50b3e3f66f84ce
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893591"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>O que são alertas clássicos no Microsoft Azure?

> [!NOTE]
> Este artigo descreve como criar alertas de métrica clássicos mais antigos. O Azure Monitor agora dá suporte [a alertas de métrica quase em tempo real mais recentes e uma nova experiência de alertas](../../azure-monitor/platform/alerts-overview.md). Os alertas clássicos são [desativados](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement), embora ainda estejam em uso limitado para recursos que ainda não dão suporte aos novos alertas. 
>

Os alertas permitem que você configure condições sobre os dados e sejam notificados quando as condições corresponderem aos dados de monitoramento mais recentes.

## <a name="old-and-new-alerting-capabilities"></a>Novos e antigos recursos de alerta

No passado Azure Monitor, Application Insights, Log Analytics e integridade do serviço tinham recursos de alerta separados. Horas extras, o Azure melhorou e combinou a interface do usuário e os diferentes métodos de alerta. A consolidação ainda está em andamento.

Você pode exibir alertas clássicos somente na tela de usuário alertas clássicos no portal do Azure. Você obtém essa tela no botão **exibir alertas clássicos** na tela alertas. 

 ![Opções de alerta no portal do Azure](media/alerts-classic.overview/monitor-alert-screen2.png)

A nova experiência do usuário de alertas tem os seguintes benefícios em relação à experiência de alertas clássicos:
-   **Melhor sistema de notificação** -todos os alertas mais recentes usam grupos de ação, que são nomeados grupos de notificações e ações que podem ser reutilizados em vários alertas. Os alertas de métrica clássicos e os alertas de Log Analytics mais antigos não usam grupos de ação.
-   **Uma experiência de criação unificada** – toda a criação de alertas para métricas, logs e log de atividades entre Azure Monitor, Log Analytics e Application insights está em um único local.
-   **Exibir alertas de log Analytics acionados no portal do Azure** -agora você também pode ver os alertas de log Analytics acionados em sua assinatura. Anteriormente, eles estavam em um portal separado.
-   **Separação de alertas acionados e regras de alerta** – regras de alerta (a definição de condição que dispara um alerta) e os alertas acionados (uma instância do acionamento da regra de alerta) são diferenciados, portanto, as exibições operacional e de configuração são separadas.
-   **Melhor fluxo de trabalho** – a nova experiência de criação de alertas orienta o usuário ao longo do processo de configuração de uma regra de alerta, o que torna mais simples descobrir as coisas certas para receber alertas.
-   **Alertas inteligentes consolidação** e **configuração do estado de alerta** -alertas mais recentes incluem a funcionalidade de agrupamento automático mostrando alertas semelhantes para reduzir a sobrecarga na interface do usuário. 

Os alertas de métrica mais recentes têm os seguintes benefícios em relação aos alertas de métrica clássicos:
-   **Latência aprimorada**: alertas de métrica mais recentes podem ser executados com a frequência de cada minuto. Alertas de métrica mais antigos sempre são executados em uma frequência de 5 minutos. Alertas mais recentes aumentam o atraso menor da ocorrência de problema para notificação ou ação (de 3 a 5 minutos). Os alertas mais antigos são de 5 a 15 minutos, dependendo do tipo.  Os alertas de log normalmente têm um atraso de 10 a 15 minutos devido ao tempo necessário para ingerir os logs, mas os métodos de processamento mais recentes estão reduzindo esse tempo. 
-   **Suporte para métricas multidimensionais**: você pode alertar sobre métricas dimensionais que permitem monitorar um segmento interessante da métrica.
-   **Mais controle sobre as condições métricas**: você pode definir regras de alerta mais avançadas. Os alertas mais recentes dão suporte ao monitoramento dos valores máximo, mínimo, médio e total de métricas.
-   **Monitoramento combinado de várias métricas**: você pode monitorar várias métricas (atualmente, até duas métricas) com uma única regra. Um alerta será disparado se ambas as métricas violarem seus respectivos limites para o período de tempo especificado.
-   **Melhor sistema de notificação**: todos os alertas mais recentes usam [grupos de ação](../../azure-monitor/platform/action-groups.md), que são nomeados grupos de notificações e ações que podem ser reutilizados em vários alertas.  Os alertas de métrica clássicos e os alertas de Log Analytics mais antigos não usam grupos de ação. 
-   **Métricas de logs** (visualização pública): os dados de log que entram no log Analytics agora podem ser extraídos e convertidos em métricas de Azure monitor e, em seguida, alertados assim como outras métricas. Consulte [alertas (clássicos)](alerts-classic.overview.md) para a terminologia específica para alertas clássicos. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Alertas clássicos em dados de Azure Monitor
Há dois tipos de alertas clássicos disponíveis-alertas de métrica e alertas de log de atividades.

* **Alertas de métrica clássicos** – esse alerta é disparado quando o valor de uma métrica especificada ultrapassa um limite que você atribui. O alerta gera uma notificação quando esse limite é ultrapassado e a condição de alerta é atendida. Nesse ponto, o alerta é considerado "ativado". Ele gera outra notificação quando é "resolvido" – ou seja, quando o limite é atingido novamente e a condição não é mais atendida.

* **Alertas do log de atividades clássicas** – um alerta do log de streaming que dispara em uma entrada de evento do log de atividades que corresponde aos seus critérios de filtro. Esses alertas têm apenas um estado, "ativado". O mecanismo de alerta simplesmente aplica os critérios de filtro a qualquer novo evento. Ele não pesquisa para localizar entradas mais antigas. Esses alertas podem notificá-lo quando um novo incidente de integridade do serviço ocorrer ou quando um usuário ou aplicativo executar uma operação em sua assinatura, por exemplo, "excluir máquina virtual".

Para dados de log de recursos disponíveis por meio de Azure Monitor, encaminhe os dados para Log Analytics e use um alerta de consulta de log. Log Analytics agora usa o [novo método de alerta](../../azure-monitor/platform/alerts-overview.md) 

O diagrama a seguir resume as fontes de dados em Azure Monitor e, conceitualmente, como você pode alertar sobre esses dados.

![Alertas explicados](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomia de alertas (clássico)
O Azure usa os seguintes termos para descrever os alertas clássicos e suas funções:
* **Alerta** -uma definição de critérios (uma ou mais regras ou condições) que se torna ativada quando atendida.
* **Ativo** -o estado quando os critérios definidos por um alerta clássico são atendidos.
* **Resolvido** -o estado quando os critérios definidos por um alerta clássico não são mais atendidos depois de terem sido atendidos anteriormente.
* **Notificação** -a ação realizada com base em um alerta clássico se torna ativa.
* **Ação** -uma chamada específica enviada a um destinatário de uma notificação (por exemplo, enviando um endereço por email ou postando para uma URL de webhook). As notificações geralmente podem disparar várias ações.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Como fazer receber uma notificação de um alerta do Azure Monitor clássico?
Historicamente, os alertas do Azure de diferentes serviços usaram seus próprios métodos de notificação internos. 

Azure Monitor criou um agrupamento de notificação reutilizável chamado *grupos de ação*. Grupos de ação especificam um conjunto de destinatários para uma notificação. Sempre que um alerta é ativado e faz referência ao grupo de ações, todos os destinatários recebem essa notificação. Os grupos de ação permitem reutilizar um agrupamento de receptores (por exemplo, sua lista de engenheiros em chamada) em vários objetos de alerta. Os grupos de ação dão suporte à notificação postando em uma URL de webhook, além de endereços de email, números de SMS e várias outras ações.  Para obter mais informações, consulte [grupos de ações](../../azure-monitor/platform/action-groups.md). 

Os alertas mais antigos do log de atividades clássicas usam grupos de ação.

No entanto, os alertas de métrica mais antigos não usam grupos de ação. Em vez disso, você pode configurar as seguintes ações: 
- Envie notificações por email para o administrador de serviços, para coadministradores ou para endereços de email adicionais que você especificar.
- Chame um webhook, que permite que você inicie ações de automação adicionais.

Os WebHooks permitem a automação e a correção, por exemplo, usando:
- Runbook da Automatização do Azure
- Azure Function
- Aplicação Lógica do Azure
- um serviço de terceiros

## <a name="next-steps"></a>Passos seguintes
Obter informações sobre regras de alerta e configurá-las usando:

* Saiba mais sobre [métricas](data-platform.md)
* Configurar [alertas de métrica clássicos via portal do Azure](alerts-classic-portal.md)
* Configurar o [PowerShell de alertas de métrica clássico](alerts-classic-portal.md)
* Configurar [a CLI (interface de linha de comando) de alertas de métrica clássico](alerts-classic-portal.md)
* Configurar [alertas de métrica clássicos Azure monitor API REST](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Saiba mais sobre o [log de atividades](activity-logs-overview.md)
* Configurar [alertas do log de atividades via portal do Azure](activity-log-alerts.md)
* Configurar [alertas do log de atividades por meio do Resource Manager](alerts-activity-log.md)
* Examinar o [esquema de webhook de alerta do log de atividades](activity-log-alerts-webhook.md)
* Saiba mais sobre [grupos de ação](action-groups.md)
* Configurar [alertas mais recentes](alerts-metric.md)
