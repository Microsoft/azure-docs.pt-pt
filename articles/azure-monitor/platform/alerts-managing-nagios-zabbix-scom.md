---
title: Gerenciar alertas de System Center Operations Manager, Zabbix e Nagios no Azure Monitor
description: Gerenciar alertas de System Center Operations Manager, Zabbix e Nagios no Azure Monitor
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 6730402348069df7c2597aa0d73a4211669c2d66
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702839"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Gerenciar alertas de System Center Operations Manager, Zabbix e Nagios no Azure Monitor

Agora você pode exibir seus alertas em Nagios, Zabbix e System Center Operations Manager em [Azure monitor](https://aka.ms/azure-alerts-overview). Esses alertas são provenientes de integrações com servidores Nagios/Zabbix ou System Center Operations Manager no Log Analytics. 

## <a name="prerequisites"></a>Pré-requisitos
Todos os registros no repositório de Log Analytics com um tipo de alerta serão importados para o Azure Monitor, portanto, você deve executar a configuração necessária para coletar esses registros.
1. Para alertas de **Nagios** e **Zabbix** , [Configure esses servidores](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) para [enviar alertas](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) para log Analytics.
1. Para **System Center Operations Manager** alertas, [Conecte seu grupo de gerenciamento de Operations Manager ao seu espaço de trabalho do log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Depois disso, implante a solução [Gerenciamento de alertas](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) do Marketplace de soluções do Azure. Depois de concluído, todos os alertas criados no System Center Operations Manager são importados para o Log Analytics.

## <a name="view-your-alert-instances"></a>Exibir suas instâncias de alerta
Depois de configurar a importação no Log Analytics, você pode começar a exibir instâncias de alerta desses serviços de monitoramento no [Azure monitor](https://aka.ms/azure-alerts-overview). Quando estiverem presentes no Azure Monitor, você poderá [gerenciar suas instâncias de alerta](https://aka.ms/managing-alert-instances), [gerenciar grupos inteligentes criados nesses alertas](https://aka.ms/managing-smart-groups) e [alterar o estado de seus alertas e grupos inteligentes](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Essa solução só permite que você exiba as instâncias de alerta System Center Operations Manager/Zabbix/Nagios acionadas no Azure Monitor. A configuração da regra de alerta só pode ser exibida/editada nas respectivas ferramentas de monitoramento. 
>  1. Todas as instâncias de alerta disparadas estarão disponíveis no Azure Monitor e no Log Analytics do Azure. Atualmente, não há nenhuma maneira de escolher entre os dois ou ingerir apenas alertas específicos acionados.
>  1. Todos os alertas de System Center Operations Manager, Zabbix e Nagios têm o tipo de sinal "Unknown", pois o tipo de telemetria subjacente não está disponível.
>  1. Os alertas do Nagios não têm monitoração de estado – por exemplo, a [condição do monitor](https://aka.ms/azure-alerts-overview) de um alerta não vai de "disparado" para "resolvido". Em vez disso, os "disparados" e "resolvidos" são exibidos como instâncias de alerta separadas. 

