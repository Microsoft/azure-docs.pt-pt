---
title: Gerir alertas do System Center Operations Manager, Zabbix e Nagios no Monitor Azure
description: Gerir alertas do System Center Operations Manager, Zabbix e Nagios no Monitor Azure
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 5ef03f522fb91302bfdec985f1e3c5cf8138ae97
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667453"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Gerir alertas do System Center Operations Manager, Zabbix e Nagios no Monitor Azure

Pode agora ver os seus alertas de Nagios, Zabbix e System Center Operations Manager no [Azure Monitor](https://aka.ms/azure-alerts-overview). Estes alertas provêm de integrações com servidores Nagios/Zabbix ou System Center Operations Manager em Log Analytics. 

## <a name="prerequisites"></a>Pré-requisitos
Quaisquer registos no repositório Log Analytics com um tipo de Alerta serão importados para o Monitor Azure, pelo que deve realizar a configuração necessária para recolher estes registos.
1. Para os **alertas Nagios** e **Zabbix,** [configure os servidores](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) para [enviar alertas](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) para o Log Analytics.
1. Para alertas de **Gestor de Operações do System Center,** ligue o seu grupo de [gestão de Gestão](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents)de Operações ao seu espaço de trabalho Log Analytics . A seguir, implemente a solução [de Gestão](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) de Alerta solução a partir do mercado de soluções Azure. Uma vez realizados, quaisquer alertas criados no System Center Operations Manager são importados para o Log Analytics.

## <a name="view-your-alert-instances"></a>Veja as suas instâncias de alerta
Depois de configurar a importação para o Log Analytics, pode começar a visualizar casos de alerta a partir destes serviços de monitorização no [Monitor Azure](https://aka.ms/azure-alerts-overview). Uma vez presentes no Monitor Azure, pode [gerir os seus casos](https://aka.ms/managing-alert-instances)de alerta, gerir [grupos inteligentes criados nestes alertas](https://aka.ms/managing-smart-groups) e [alterar o estado dos seus alertas e grupos inteligentes](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Esta solução permite-lhe apenas visualizar o System Center Operations Manager/Zabbix/Nagios disparou casos de alerta no Monitor Azure. A configuração da regra de alerta só pode ser visualizada/editada nas respetivas ferramentas de monitorização. 
>  1. Todos os casos de alerta disparados estarão disponíveis tanto no Azure Monitor como no Azure Log Analytics. Atualmente, não há como escolher entre os dois ou ingerir apenas alertas específicos disparados.
>  1. Todos os alertas do System Center Operations Manager, Zabbix e Nagios têm o tipo de sinal "Desconhecido", uma vez que o tipo de telemetria subjacente não está disponível.
>  1. Os alertas de Nagios não são audacionais – por exemplo, o estado de [monitorização](https://aka.ms/azure-alerts-overview) de um alerta não passará de "Despedido" para "Resolvido". Em vez disso, tanto os "Despedidos" como os "Resolvidos" são apresentados como instâncias de alerta separadas. 

