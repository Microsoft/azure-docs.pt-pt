---
title: Gerir alertas do Nagios, Zabbix e System Center Operations Manager no Azure Monitor
description: Gerir alertas do Nagios, Zabbix e System Center Operations Manager no Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 5ef03f522fb91302bfdec985f1e3c5cf8138ae97
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77667453"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Gerir alertas do Nagios, Zabbix e System Center Operations Manager no Azure Monitor

Agora pode ver os seus alertas de Nagios, Zabbix e System Center Operations Manager no [Azure Monitor](https://aka.ms/azure-alerts-overview). Estes alertas provêm de integrações com servidores Nagios/Zabbix ou Gestor de Operações do Centro de Sistema em Log Analytics. 

## <a name="prerequisites"></a>Pré-requisitos
Quaisquer registos no repositório Log Analytics com um tipo de Alerta serão importados para o Azure Monitor, pelo que deve realizar a configuração necessária para recolher estes registos.
1. Para **alertas de Nagios** e **Zabbix,** [configuure esses servidores](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) para [enviar alertas](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) para o Log Analytics.
1. Para alertas **do Gestor de Operações do Centro de Sistema,** [ligue o seu grupo de gestão de Gestor de Operações ao seu espaço de trabalho Log Analytics.](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents) Em sequência disso, implemente a solução [de Gestão](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) de Alerta do mercado de soluções Azure. Uma vez feito, quaisquer alertas criados no Gestor de Operações do Centro de Sistema são importados para o Log Analytics.

## <a name="view-your-alert-instances"></a>Veja as suas instâncias de alerta
Uma vez configurado a importação para o Log Analytics, pode começar a visualizar casos de alerta destes serviços de monitorização no [Azure Monitor](https://aka.ms/azure-alerts-overview). Assim que estiverem presentes no Azure Monitor, pode [gerir os seus casos de alerta,](https://aka.ms/managing-alert-instances) [gerir grupos inteligentes criados nestes alertas](https://aka.ms/managing-smart-groups) e [alterar o estado dos seus alertas e grupos inteligentes.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  1. Esta solução só permite visualizar o Gestor de Operações do Centro de Sistema/Zabbix/Nagios, que disparou casos de alerta no Azure Monitor. A configuração da regra de alerta só pode ser visualizada/editada nas respetivas ferramentas de monitorização. 
>  1. Todas as instâncias de alerta disparadas estarão disponíveis tanto no Azure Monitor como no Azure Log Analytics. Atualmente, não há como escolher entre os dois ou ingerir apenas alertas de disparo específicos.
>  1. Todos os alertas do Gestor de Operações do System Center, Zabbix e Nagios têm o tipo de sinal "Desconhecido", uma vez que o tipo de telemetria subjacente não está disponível.
>  1. Os alertas de Nagios não são imponentes – por exemplo, a condição do [monitor](https://aka.ms/azure-alerts-overview) de um alerta não passará de "Disparado" para "Resolvido". Em vez disso, tanto os "Disparados" como os "Resolvidos" são apresentados como instâncias de alerta separadas. 

