---
title: Gerir alertas do SCOM, Nagios no Azure Monitor e do Zabbix
description: Gerir alertas do SCOM, Nagios no Azure Monitor e do Zabbix
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 48fb9d8eaf2003834a420b48d649c830c608fd6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712990"
---
# <a name="manage-alerts-from-scom-zabbix-and-nagios-in-azure-monitor"></a>Gerir alertas do SCOM, Nagios no Azure Monitor e do Zabbix

Agora, pode ver os alertas do Nagios e do System Center Operations Manager no Zabbix [do Azure Monitor](https://aka.ms/azure-alerts-overview). Estes alertas provenientes de integrações com servidores da Nagios/Zabbix ou System Center Operations Manager para o Log Analytics. 

## <a name="prerequisites"></a>Pré-requisitos
Quaisquer registos no repositório do Log Analytics com o tipo de alerta serão importados para o Azure Monitor, portanto, é necessário executar a configuração que é necessário para recolher estes registos.
1. Para **Nagios** e **Zabbix** alertas, [configurar esses servidores](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) para [enviar alertas](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) ao Log Analytics.
1. Para **System Center Operations Manager** alertas, [ligar o seu grupo de gestão do Operations Manager à sua área de trabalho do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Ao seguir esta, implementar o [gestão de alertas](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) solução no mercado de soluções do Azure. Quando tiver terminado, todos os alertas criados no System Center Operations Manager são importados para o Log Analytics.

## <a name="view-your-alert-instances"></a>Ver as suas instâncias de alerta
Assim que tiver configurado a importação para o Log Analytics, pode começar a ver alerta instâncias de um estes serviços de monitorização [do Azure Monitor](https://aka.ms/azure-alerts-overview). Assim que estiverem presentes no Azure Monitor, pode [gerir instâncias de alerta](https://aka.ms/managing-alert-instances), [gerir smart grupos criados nestes alertas](https://aka.ms/managing-smart-groups) e [alterar o estado dos alertas e grupos inteligentes](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Esta solução apenas permite-lhe ver as instâncias de alerta do SCOM/Zabbix/Nagios disparado no Azure Monitor. Configuração de regra de alerta só pode ser visualizado/editado em ferramentas de monitorização correspondentes. 
>  1. Todas as instâncias de alertas acionadas vão estar disponíveis no Azure Monitor e o Azure Log Analytics. Atualmente, não é possível escolher entre os dois ou ingestão só específicas acionados alertas.
>  1. Todos os alertas do SCOM, Nagios e do Zabbix tem o tipo de sinal "Desconhecido", uma vez que o tipo de telemetria subjacente não está disponível.
>  1. Alertas do Nagios não têm monitorização de estado – por exemplo, o [monitorizar a condição](https://aka.ms/azure-alerts-overview) de um alerta não vai de "Fired" como "Resolvido". Em vez disso, o "Fired" e "Resolvido" são apresentadas como instâncias separadas de alerta. 

