---
title: Visão geral dos agentes de monitorização do Azure Microsoft Docs
description: Este artigo fornece uma visão detalhada dos agentes Azure disponíveis que suportam a monitorização de máquinas virtuais alojadas em ambiente Azure ou híbrido.
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: e3385234433a292ce146c9aed25ecfeb1095d79a
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616121"
---
# <a name="overview-of-azure-monitor-agents"></a>Visão geral dos agentes do Monitor Azure

Máquinas virtuais e outros recursos computacionais exigem que um agente recolha dados de monitorização para medir o desempenho e disponibilidade do seu sistema operativo convidado e cargas de trabalho. Este artigo descreve os agentes utilizados pelo Azure Monitor e ajuda-o a determinar quais os requisitos necessários para satisfazer os requisitos para o seu ambiente específico.

> [!NOTE]
> O Azure Monitor conta atualmente com vários agentes devido à recente consolidação do Azure Monitor e do Log Analytics. Embora possa haver sobreposição nas suas características, cada um tem capacidades únicas. Dependendo dos seus requisitos, poderá necessitar de um ou mais dos agentes nas suas máquinas virtuais. 

Pode ter um conjunto específico de requisitos que não podem ser completamente satisfeitos com um único agente para uma determinada máquina virtual. Por exemplo, pode querer utilizar alertas métricos que requerem extensão de diagnóstico do Azure, mas também pretende alavancar a funcionalidade do Monitor Azure para VMs que requer o agente Log Analytics e o agente Dependency. Em casos como este, pode utilizar vários agentes, e este é um cenário comum para clientes que necessitam de funcionalidade seletiva de cada um.

## <a name="summary-of-agents"></a>Resumo dos agentes

As tabelas seguintes proporcionam uma comparação rápida dos agentes do Monitor Azure para Windows e Linux. Mais detalhes sobre cada um deles são fornecidos na secção abaixo. 

### <a name="windows-agents"></a>Agentes do Windows

| | Diagnóstico<br>extensão (WAD) | Log Analytics<br>agente | Dependência<br>agente |
|:---|:---|:---|:---|
| Ambientes apoiados | Azure | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local | 
| Requisitos do agente  | Nenhum | Nenhum | Requer o agente Log Analytics |
| Dados recolhidos | Registos de Eventos<br>Eventos ETW<br>Desempenho<br>Registos baseados em ficheiros<br>Registos do IIS<br>registos de aplicativos .NET<br>Informações de falha de sistema<br>Registos de diagnóstico de agente | Registos de Eventos<br>Desempenho<IIS logs><br>Registos baseados em ficheiros<br>Insights e soluções<br>Outros serviços | Detalhes e dependências do processo<br>Métricas de ligação de rede |
| Dados enviados para | Storage do Azure<br>Métricas do Monitor Azure<br>Hub de Eventos | Registos do Azure Monitor | Registos do Azure Monitor |


### <a name="linux-agents"></a>Agentes do Linux

| | Diagnóstico<br>extensão (LAD) | Telegrafo<br>agente | Log Analytics<br>agente | Dependência<br>agente |
|:---|:---|:---|:---|:---|
| Ambientes apoiados | Azure | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local |
| Requisitos do agente  | Nenhum | Nenhum | Nenhum | Requer o agente Log Analytics |
| Dados recolhidos | Syslog<br>Desempenho | Desempenho | Syslog<br>Desempenho| Detalhes e dependências do processo<br>Métricas de ligação de rede |
| Dados enviados para | Storage do Azure<br>Hub de Eventos | Métricas do Monitor Azure | Registos do Azure Monitor | Registos do Azure Monitor |

## <a name="log-analytics-agent"></a>Agente do Log Analytics

O [agente Log Analytics](log-analytics-agent.md) recolhe dados de monitorização do sistema operativo de hóspedes e cargas de trabalho de máquinas virtuais em Azure, outros fornecedores de nuvem e no local. Recolhe dados num espaço de trabalho do Log Analytics. O agente Log Analytics é o mesmo agente utilizado pelo System Center Operations Manager, e pode comunicar com o seu grupo de gestão e o Monitor Azure simultaneamente. Este agente também é exigido por certos insights e soluções no Monitor Azure.


> [!NOTE]
> O agente Log Analytics para Windows é frequentemente referido como Microsoft Monitoring Agent (MMA). O agente Log Analytics para o Linux é frequentemente referido como agente OMS.



Utilize o agente Log Analytics se precisar:

* Recolha registos e dados de desempenho de máquinas virtuais ou físicas fora do Azure. 
* Envie dados para um espaço de trabalho de Log Analytics para tirar partido das funcionalidades suportadas por [Registos do Monitor Do Azure,](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) tais como consultas de [log](../log-query/log-query-overview.md).
* Utilize o [Monitor Azure para VMs](../insights/vminsights-overview.md) que lhe permite monitorizar as suas máquinas virtuais à escala e monitoriza os seus processos e dependências de outros recursos e processos externos..  
* Gerencie a segurança das suas máquinas virtuais utilizando o [Azure Security Center](../../security-center/security-center-intro.md) ou [o Azure Sentinel](../../sentinel/overview.md).
* Utilize a gestão da atualização da [automação azure,](../../automation/automation-update-management.md)configuração do Estado da [Automação Azure,](../../automation/automation-dsc-overview.md)ou Rastreio e Inventário de [Alterações de Automação Azure](../../automation/change-tracking.md) para fornecer uma gestão abrangente dos seus VMs Azure
* Utilize [diferentes soluções](../monitor-reference.md#insights-and-core-solutions) para monitorizar um determinado serviço ou aplicação.

As limitações do agente Log Analytics incluem:

- Não é possível enviar dados para as Métricas do Monitor Azure, armazenamento azure ou hubs de eventos Azure.

## <a name="azure-diagnostics-extension"></a>Extensão de diagnóstico do Azure

A [extensão Azure Diagnostics](diagnostics-extension-overview.md) recolhe dados de monitorização do sistema operativo dos hóspedes e cargas de trabalho de máquinas virtuais Azure e outros recursos de computação. Recolhe principalmente dados no Armazenamento Azure, mas também permite definir afundados de dados para também enviar dados para outros destinos, como o Azure Monitor Metrics e o Azure Event Hubs.

Utilize a extensão de diagnóstico Azure se precisar:

- Envie dados para o Armazenamento Azure para arquivar ou analisá-los com ferramentas como [o Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Envie dados para [as Métricas do Monitor Do Azure](data-platform-metrics.md) para analisá-los com o explorador de métricas e para tirar partido de [funcionalidades](metrics-getting-started.md) como [alertas métricos](../../azure-monitor/platform/alerts-metric-overview.md) em tempo real e [escala automática](autoscale-overview.md) (apenas windows).
- Envie dados para ferramentas de terceiros utilizando hubs de [eventos Azure](diagnostics-extension-stream-event-hubs.md).
- Colete [Boot Diagnostics](../../virtual-machines/troubleshooting/boot-diagnostics.md) para investigar problemas de arranque da VM.

As limitações da extensão de diagnóstico sinuosa incluem:

- Só pode ser usado com recursos Azure.
- Capacidade limitada de enviar dados para registos do Monitor Azure.

## <a name="telegraf-agent"></a>Agente de telegrafo

O [agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) é utilizado para recolher dados de desempenho dos computadores Linux para as Métricas do Monitor Azure.

Use o agente Telegraf se precisar:

* Envie dados para [as Métricas do Monitor Azure](data-platform-metrics.md) para analisá-los com o explorador de métricas e para tirar partido de [funcionalidades](metrics-getting-started.md) como [alertas métricos](../../azure-monitor/platform/alerts-metric-overview.md) em tempo real e [escala automática](autoscale-overview.md) (apenas Linux). 



## <a name="dependency-agent"></a>Agente de Dependência

O agente da Dependência recolhe dados descobertos sobre processos em execução na máquina virtual e dependências de processos externos. 

Utilize o agente de dependência se precisar:

* Utilize o monitor de mapas Do Mapa [para VMs](../insights/vminsights-overview.md) ou a solução [service Map.](../insights/service-map.md)

Considere o seguinte ao utilizar o agente de dependência:

- O agente Dependency exige que o agente Log Analytics seja instalado na mesma máquina virtual.
- Nos VMs Linux, o agente Log Analytics deve ser instalado antes da extensão de diagnóstico do Azure.

## <a name="extensions-compared-to-agents"></a>Extensões em comparação com agentes

A extensão Log Analytics para [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md) instala o agente Log Analytics em máquinas virtuais Azure. A extensão de dependência do Monitor Azure para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) instala o agente Dependency em máquinas virtuais Azure. Estes são os mesmos agentes acima descritos, mas permitem geri-los através de [extensões de máquinas virtuais](../../virtual-machines/extensions/overview.md). Deve utilizar extensões para instalar e gerir os agentes sempre que possível.


## <a name="next-steps"></a>Passos seguintes

Obtenha mais detalhes sobre cada um dos agentes no seguinte:

- [Visão geral do agente Log Analytics](log-analytics-agent.md)
- [Visão geral da extensão do Azure Diagnostics](diagnostics-extension-overview.md)
- [Colete métricas personalizadas para um VM Linux com o agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md)
