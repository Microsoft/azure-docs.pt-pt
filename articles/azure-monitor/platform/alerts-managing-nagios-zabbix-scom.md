---
title: Gerir alertas do Nagios, Zabbix e System Center Operations Manager no Azure Monitor
description: Gerir alertas do Nagios, Zabbix e System Center Operations Manager no Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 367a7020e56db7105da7624c7911ae5b59f1365a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105894"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Gerir alertas do Nagios, Zabbix e System Center Operations Manager no Azure Monitor

Agora pode ver os seus alertas de Nagios, Zabbix e System Center Operations Manager no [Azure Monitor](./alerts-overview.md). Estes alertas provêm de integrações com servidores Nagios/Zabbix ou Gestor de Operações do Centro de Sistema em Log Analytics. 

## <a name="prerequisites"></a>Pré-requisitos
Quaisquer registos no repositório Log Analytics com um tipo de Alerta serão importados para o Azure Monitor, pelo que deve realizar a configuração necessária para recolher estes registos.
1. Para **alertas de Nagios** e **Zabbix,** [configuure esses servidores](../learn/quick-collect-linux-computer.md) para [enviar alertas](./data-sources-custom-logs.md?toc=/azure/azure-monitor/toc.json) para o Log Analytics.
1. Para alertas **do Gestor de Operações do Centro de Sistema,** [ligue o seu grupo de gestão de Gestor de Operações ao seu espaço de trabalho Log Analytics.](./om-agents.md) Em sequência disso, implemente a solução [de Gestão](./alert-management-solution.md) de Alerta do mercado de soluções Azure. Uma vez feito, quaisquer alertas criados no Gestor de Operações do Centro de Sistema são importados para o Log Analytics.

## <a name="view-your-alert-instances"></a>Veja as suas instâncias de alerta
Uma vez configurado a importação para o Log Analytics, pode começar a visualizar casos de alerta destes serviços de monitorização no [Azure Monitor](./alerts-overview.md). Assim que estiverem presentes no Azure Monitor, pode [gerir os seus casos de alerta,](./alerts-managing-alert-instances.md?toc=%252fazure%252fazure-monitor%252ftoc.json) [gerir grupos inteligentes criados nestes alertas](./alerts-managing-smart-groups.md?toc=%252fazure%252fazure-monitor%252ftoc.json) e [alterar o estado dos seus alertas e grupos inteligentes.](./alerts-managing-alert-states.md?toc=%252fazure%252fazure-monitor%252ftoc.json)

> [!NOTE]
>  1. Esta solução só permite visualizar o Gestor de Operações do Centro de Sistema/Zabbix/Nagios, que disparou casos de alerta no Azure Monitor. A configuração da regra de alerta só pode ser visualizada/editada nas respetivas ferramentas de monitorização. 
>  1. Todas as instâncias de alerta disparadas estarão disponíveis tanto no Azure Monitor como no Azure Log Analytics. Atualmente, não há como escolher entre os dois ou ingerir apenas alertas de disparo específicos.
>  1. Todos os alertas do Gestor de Operações do System Center, Zabbix e Nagios têm o tipo de sinal "Desconhecido", uma vez que o tipo de telemetria subjacente não está disponível.
>  1. Os alertas de Nagios não são imponentes – por exemplo, a condição do [monitor](./alerts-overview.md) de um alerta não passará de "Disparado" para "Resolvido". Em vez disso, tanto os "Disparados" como os "Resolvidos" são apresentados como instâncias de alerta separadas.