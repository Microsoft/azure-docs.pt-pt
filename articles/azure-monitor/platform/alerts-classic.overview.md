---
title: Descrição geral dos alertas Clássicos no Microsoft Azure e o Azure Monitor
description: Vão ser preteridas alertas clássicos. Alertas permitem-lhe monitorizar métricas de recurso do Azure, eventos ou os registos e ser notificado quando for cumprida uma condição que especificar.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/29/2018
ms.author: robb
ms.openlocfilehash: 3b6ddd99d7b198cdc5274b2944a723e68864bd4f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993027"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>O que são alertas Clássicos no Microsoft Azure?

> [!NOTE]
> Este artigo descreve como criar alertas de métricas clássicas mais antigas. Agora suporta o Monitor do Azure [mais recente alertas de métricas de quase em tempo real e uma nova experiência de alertas](../../azure-monitor/platform/alerts-overview.md). Alertas clássicos são [agendados para serem extinguidos](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).  
>

Alertas permitem-lhe configurar as condições sobre os dados e ser notificado quando as condições corresponderem aos dados monitorização mais recentes.

## <a name="old-and-new-alerting-capabilities"></a>Velha novas capacidades e alertas

No passado do Azure Monitor, o Application Insights, o Log Analytics e o estado de funcionamento do serviço tinham separado alertas capacidades. Horas extraordinárias, o Azure melhorada e combinados a interface do usuário e os métodos diferentes de alertas. A consolidação ainda está em processo. Alertas

Pode ver alertas clássicos apenas no ecrã de utilizador de alertas Clássicos no Portal do Azure. Obtém este ecrã do **ver alertas clássicos** botão no ecrã de alertas. 

 ![Opções de alerta no portal do Azure](media/alerts-classic.overview/monitor-alert-screen2.png)

A nova experiência de utilizador de alertas tem as seguintes vantagens sobre a experiência de alertas clássicos:
-   **Melhor o sistema de notificação** -todos os alertas mais recentes utilizam grupos de ação, o que são denominados grupos de notificações e ações que podem ser reutilizadas em vários alertas. Não utilize grupos de ação clássicas alertas de métricas e alertas mais antigas do Log Analytics.
-   **A experiência de criação de unificação** - todos os alertas de criação para métricas, registos e a atividade de registo em Azure Monitor, o Log Analytics, e o Application Insights está num único lugar.
-   **Vista acionados alertas do Log Analytics no portal do Azure** -pode agora também Consulte acionados alertas do Log Analytics na sua subscrição. Anteriormente eles eram num portal separado.
-   **Separação de alertas acionados e as regras de alerta** - as regras de alerta (a definição da condição que aciona um alerta) e são diferenciadas acionados alertas (uma instância do disparo de regra de alerta), para que as vistas de configuração e operacionais são separadas.
-   **Fluxo de trabalho melhor** - novos alertas guias de experiência de criação do utilizador ao longo do processo de configuração de uma regra de alerta, o que torna mais simples de descobrir o certo para receber alertas em.
-   **Consolidação de alertas do smart** e **definir o estado do alerta** -alertas mais recentes incluem a funcionalidade de agrupamento automático que mostra alertas semelhantes em conjunto para reduzir a sobrecarga na interface do usuário. 

Os alertas de métricas mais recentes têm as seguintes vantagens sobre os alertas de métrica clássicos:
-   **Melhorada a latência**: Alertas de métricas mais recente podem ser executado com uma frequência de cada minuto. Alertas de métricas mais antigas são sempre executam com uma frequência de 5 minutos. Alertas mais recentes têm aumentando o atraso menor de ocorrência do problema, a notificação ou ação (3 a 5 minutos). Alertas mais antigas são 5 a 15 minutos, dependendo do tipo.  Alertas de registo têm normalmente o atraso de 10 a 15 minutos devido ao tempo necessário para ingerir os registos, mas os métodos de processamento mais novos são reduzir esse tempo. 
-   **Suporte para métricas multidimensionais**: Pode alertar relativamente a métricas dimensionais, permitindo-lhe monitorizar um segmento interessante da métrica.
-   **Mais controlo sobre condições de métricas**: Pode definir regras de alerta mais sofisticadas. Os alertas mais recente suportam os valores máximos, mínimo, médios e total de métricas de monitorização.
-   **Combinados a monitorização de várias métricas**: Pode monitorizar várias métricas (atualmente, até duas métricas) com uma única regra. Um alerta é acionado se ambas as métricas de violações seus respectivos limites para o período de tempo especificado.
-   **Melhor o sistema de notificação**: Utilizam todos os alertas mais recentes [grupos de ação](../../azure-monitor/platform/action-groups.md), que é denominado grupos de notificações e ações que podem ser reutilizadas em vários alertas.  Não utilize grupos de ação clássicas alertas de métricas e alertas mais antigas do Log Analytics. 
-   **Métricas de Logs** (pré-visualização pública): Dados de registo vai para o Log Analytics podem agora ser extraídos e convertidas em métricas do Azure Monitor e, em seguida, alertado em tal como outras métricas. Ver [alertas (clássico)](alerts-classic.overview.md) para a terminologia específica para alertas clássicos. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Alertas clássicos nos dados do Azure Monitor
Existem dois tipos de alertas clássicos está disponíveis – alertas de métricas e alertas de registo de atividade.

* **Alertas de métricas clássicas** -este alerta é acionada quando o valor de uma métrica especificado ultrapassar um limiar que atribuir. O alerta gera uma notificação quando esse limiar for ultrapassado e a condição de alerta foi cumprida. Nesse ponto, o alerta é considerado "Ativado". Gera outra notificação quando ele é "resolvido" - ou seja, quando o limiar é cruzado novamente e a condição já não for cumprida.

* **Alertas de registo de atividade clássica** -um alerta de registo de transmissão em fluxo que aciona numa entrada de eventos de registo de atividades que corresponde aos critérios de filtro. Estes alertas têm apenas um Estado, "Ativado". O mecanismo de alerta simplesmente aplica-se os critérios de filtro para qualquer novo evento. Ele não pesquisar para encontrar as entradas mais antigas. Estes alertas podem notificá-lo quando ocorre um novo incidente do Estado de funcionamento do serviço ou quando um utilizador ou aplicação realiza uma operação na sua subscrição, por exemplo, "excluir máquina virtual."

Para os dados de registo de diagnóstico disponíveis através do Azure Monitor, encaminhar os dados para o Log Analytics (anteriormente conhecido como OMS) e utilize um alerta de consulta do Log Analytics. Utiliza agora de análise de registo a [novo método de alerta](../../azure-monitor/platform/alerts-overview.md) 

O diagrama seguinte resume as origens de dados no Azure Monitor e, conceitualmente, como pode alertar fora dos dados.

![Alertas explicadas](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomia de alertas (clássico)
O Azure utiliza os seguintes termos para descrever alertas clássicos e suas funções:
* **Alerta** -uma definição de critérios (um ou mais regras ou condições), o que torna-se ativado quando cumpridos.
* **Active Directory** -o estado quando os critérios definidos por um alerta clássico são cumpridos.
* **Resolvido** -o estado quando os critérios definidos por um alerta clássico já não for cumprida após ter anteriormente foram cumpridos.
* **Notificação** – a ação tomada com base num alerta clássico se tornando Active Directory.
* **Ação** -uma chamada específica enviada para um recetor de uma notificação (por exemplo, um endereço de envio por email ou lançamento para um URL do webhook). Notificações, normalmente, podem acionar várias ações.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Como faço para receber uma notificação de um alerta clássico do Azure Monitor?
Historicamente, os alertas do Azure a partir de diferentes serviços utilizados seus próprios métodos de notificação internos. 

O Azure Monitor criado uma notificação reutilizável de agrupamento chamado *grupos de ação*. Grupos de ação de especificar um conjunto de recetores para uma notificação. Sempre que um alerta é ativado que referencia o grupo de ação, todos os recetores recebem essa notificação. Grupos de ação permitem que reutilize um agrupamento de recetores (por exemplo, sua lista de engenheiro na chamada) em vários objetos de alerta. Grupos de ação suportam a notificação, publicando um URL do webhook, além de endereços de e-mail, os números SMS e diversas outras ações.  Para obter mais informações, consulte [grupos de ação](../../azure-monitor/platform/action-groups.md). 

Alertas de registo de atividades mais antigas clássicos utilizam grupos de ação.

No entanto, os alertas de métricas mais antigos não utilize grupos de ação. Em vez disso, pode configurar as seguintes ações: 
- Envie notificações por e-mail para o administrador de serviços, para coadministradores ou para endereços de e-mail adicionais que especificar.
- Chame um webhook, o que permite-lhe iniciar ações adicionais de automatização.

Webhooks permite a automação e remediação, por exemplo, usando:
- Runbook da Automatização do Azure
- Função do Azure
- Aplicação lógica do Azure
- Um serviço de terceiros

## <a name="next-steps"></a>Passos Seguintes
Obter informações sobre regras de alerta e configurá-los usando:

* Saiba mais sobre [métricas](../../azure-monitor/platform/data-collection.md)
* Configurar [alertas de métrica clássico através do portal do Azure](alerts-classic-portal.md)
* Configurar [PowerShell de alertas de métrica clássico](alerts-classic-portal.md)
* Configurar [interface de linha de comandos de alertas de métrica clássico (CLI)](alerts-classic-portal.md)
* Configurar [clássico métrica alertas Monitor API REST do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Saiba mais sobre [registo de atividades](../../azure-monitor/platform/activity-logs-overview.md)
* Configurar [alertas de registo de atividade através do portal do Azure](../../azure-monitor/platform/activity-log-alerts.md)
* Configurar [alertas de registo de atividade através do Gestor de recursos](alerts-activity-log.md)
* Reveja o [esquema de webhook de alerta de registo de atividades](../../azure-monitor/platform/activity-log-alerts-webhook.md)
* Saiba mais sobre [grupos de ação](../../azure-monitor/platform/action-groups.md)
* Configurar [alertas mais recentes](../../azure-monitor/platform/alerts-metric.md)
