---
title: Visão geral dos agentes de monitorização do Azure Microsoft Docs
description: Este artigo fornece uma visão detalhada dos agentes Azure disponíveis que suportam a monitorização de máquinas virtuais hospedadas em ambiente Azure ou híbrido.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686669"
---
# <a name="overview-of-azure-monitor-agents"></a>Visão geral dos agentes do Monitor Azure

Máquinas virtuais e outros recursos de computação requerem que um agente recolha dados de monitorização para medir o desempenho e disponibilidade do seu sistema operativo convidado e cargas de trabalho. Este artigo descreve os agentes utilizados pelo Azure Monitor e ajuda-o a determinar quais os requisitos necessários para o seu ambiente específico.

> [!NOTE]
> A Azure Monitor tem atualmente vários agentes devido à recente consolidação do Azure Monitor e do Log Analytics. Embora possa haver sobreposição nas suas características, cada uma tem capacidades únicas. Dependendo dos seus requisitos, poderá precisar de um ou mais agentes nas suas máquinas virtuais. 

Você pode ter um conjunto específico de requisitos que não podem ser completamente cumpridos com um único agente para uma determinada máquina virtual. Por exemplo, pode querer utilizar alertas métricos que requerem extensão de diagnóstico Azure, mas também quer aproveitar a funcionalidade do Azure Monitor para VMs que requer o agente Log Analytics e o agente Dependency. Em casos como este, você pode usar vários agentes, e este é um cenário comum para clientes que requerem funcionalidade de cada um.

## <a name="summary-of-agents"></a>Resumo dos agentes

As tabelas seguintes proporcionam uma comparação rápida dos agentes do Monitor Azure para Windows e Linux. São fornecidos mais pormenores sobre cada um deles na secção abaixo. 

### <a name="windows-agents"></a>Agentes do Windows

| | Diagnóstico<br>extensão (WAD) | Log Analytics<br>agente | Dependência<br>agente |
|:---|:---|:---|:---|
| Ambientes apoiados | Azure | Azure<br>Outra nuvem<br>No local | Azure<br>Outra nuvem<br>No local | 
| Requisitos do agente  | Nenhum | Nenhum | Requer agente log analytics |
| Dados recolhidos | Registos de Eventos<br>Eventos da ETW<br>Desempenho<br>Registos baseados em ficheiros<br>Registos do IIS<br>Registos de aplicações .NET<br>Informações de falha de sistema<br>Registos de diagnóstico de agentes | Registos de Eventos<br>Desempenho<IIS logs><br>Registos baseados em ficheiros<br>Insights e soluções<br>Outros serviços | Detalhes e dependências do processo<br>Métricas de ligação de rede |
| Dados enviados para | Storage do Azure<br>Métricas do Monitor Azure<br>Hub de Eventos | Registos do Azure Monitor | Registos do Azure Monitor |


### <a name="linux-agents"></a>Agentes do Linux

| | Diagnóstico<br>extensão (LAD) | Telegrafa<br>agente | Log Analytics<br>agente | Dependência<br>agente |
|:---|:---|:---|:---|:---|
| Ambientes apoiados | Azure | Azure<br>Outra nuvem<br>No local | Azure<br>Outra nuvem<br>No local | Azure<br>Outra nuvem<br>No local |
| Requisitos do agente  | Nenhum | Nenhum | Nenhum | Requer agente log analytics |
| Dados recolhidos | Syslog<br>Desempenho | Desempenho | Syslog<br>Desempenho| Detalhes e dependências do processo<br>Métricas de ligação de rede |
| Dados enviados para | Storage do Azure<br>Hub de Eventos | Métricas do Monitor Azure | Registos do Azure Monitor | Registos do Azure Monitor |

## <a name="log-analytics-agent"></a>Agente do Log Analytics

O [agente Do Log Analytics](log-analytics-agent.md) recolhe dados de monitorização do sistema operativo dos hóspedes e cargas de trabalho de máquinas virtuais em Azure, outros fornecedores de nuvem e no local. Recolhe dados num espaço de trabalho do Log Analytics. O agente Log Analytics é o mesmo agente utilizado pelo Gestor de Operações do System Center, e pode comunicar com o seu grupo de gestão e o Azure Monitor simultaneamente. Este agente também é exigido por certos insights e soluções no Azure Monitor.


> [!NOTE]
> O agente Log Analytics para windows é frequentemente referido como Agente de Monitorização da Microsoft (MMA). O agente Log Analytics para Linux é frequentemente referido como agente OMS.



Utilize o agente Log Analytics se for necessário:

* Recolher registos e dados de desempenho de máquinas virtuais ou físicas fora de Azure. 
* Envie dados para um espaço de trabalho log analytics para tirar partido das funcionalidades suportadas por [Registos do Monitor Azure,](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) tais como [consultas de registo .](../log-query/log-query-overview.md)
* Utilize [o Azure Monitor para VMs](../insights/vminsights-overview.md) que lhe permite monitorizar as suas máquinas virtuais em escala e monitorizar os seus processos e dependências de outros recursos e processos externos..  
* Gerencie a segurança das suas máquinas virtuais utilizando o [Azure Security Center](../../security-center/security-center-intro.md) ou [o Azure Sentinel](../../sentinel/overview.md).
* Utilize [a gestão de Azure Automation Update](../../automation/automation-update-management.md), [Azure Automation State Configuration](../../automation/automation-dsc-overview.md), ou [Azure Automation Change Tracking and Inventory](../../automation/change-tracking.md) para fornecer uma gestão abrangente dos seus VMs Azure
* Utilize [diferentes soluções](../monitor-reference.md#insights-and-core-solutions) para monitorizar um determinado serviço ou aplicação.

As limitações do agente Log Analytics incluem:

- Não é possível enviar dados para Azure Monitor Metrics, Azure Storage ou Azure Event Hubs.

## <a name="azure-diagnostics-extension"></a>Extensão de diagnóstico do Azure

A [extensão Azure Diagnostics](diagnostics-extension-overview.md) recolhe dados de monitorização do sistema operativo convidado e cargas de trabalho de máquinas virtuais Azure e outros recursos computatórios. Recolhe principalmente dados no Azure Storage, mas também permite definir sumidouros de dados para também enviar dados para outros destinos, como Azure Monitor Metrics e Azure Event Hubs.

Utilize a extensão de diagnóstico Azure se for necessário:

- Envie dados para o Azure Storage para arquivar ou analisá-los com ferramentas como [o Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Envie dados para [a Azure Monitor Metrics](data-platform-metrics.md) para analisá-lo com o explorador de [métricas](metrics-getting-started.md) e para tirar partido de funcionalidades como [alertas métricos próximos](../../azure-monitor/platform/alerts-metric-overview.md) em tempo real e [autoescalação](autoscale-overview.md) (apenas Windows).
- Enviar dados para ferramentas de terceiros utilizando [hubs de eventos Azure](diagnostics-extension-stream-event-hubs.md).
- Colete [o Boot Diagnostics](../../virtual-machines/troubleshooting/boot-diagnostics.md) para investigar problemas de arranque VM.

As limitações da extensão de diagnósticos Azure incluem:

- Só pode ser usado com recursos Azure.
- Capacidade limitada de enviar dados para registos do Monitor Azure.

## <a name="telegraf-agent"></a>Agente telegraf

O [agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) é utilizado para recolher dados de desempenho dos computadores Linux para as Métricas do Monitor Azure.

Utilize o agente Telegraf se for necessário:

* Envie dados para [a Azure Monitor Metrics](data-platform-metrics.md) para analisá-lo com o explorador de [métricas](metrics-getting-started.md) e para tirar partido de funcionalidades como [alertas métricos próximos](../../azure-monitor/platform/alerts-metric-overview.md) em tempo real e [autoescalação](autoscale-overview.md) (apenas Linux). 



## <a name="dependency-agent"></a>Agente de Dependência

O agente de Dependência recolhe dados descobertos sobre processos em execução na máquina virtual e dependências de processos externos. 

Utilize o agente de dependência se for necessário:

* Utilize o Monitor Azure do Mapa [para VMs](../insights/vminsights-overview.md) ou a solução [de Mapa de Serviço.](../insights/service-map.md)

Considere o seguinte ao utilizar o agente Desadependição:

- O agente Desadependido requer que o agente Log Analytics seja instalado na mesma máquina virtual.
- Nos VMs Linux, o agente Log Analytics deve ser instalado antes da extensão de diagnóstico Azure.

## <a name="extensions-compared-to-agents"></a>Extensões comparadas com agentes

A extensão Log Analytics para [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md) instala o agente Log Analytics em máquinas virtuais Azure. A extensão de dependência do Monitor Azure para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) instala o agente Dependency em máquinas virtuais Azure. Estes são os mesmos agentes descritos acima, mas permitem-lhe geri-los através [de extensões de máquinas virtuais.](../../virtual-machines/extensions/overview.md) Deve utilizar extensões para instalar e gerir os agentes sempre que possível.


## <a name="next-steps"></a>Passos seguintes

Obtenha mais detalhes sobre cada um dos agentes no seguinte:

- [Visão geral do agente Log Analytics](log-analytics-agent.md)
- [Visão geral da extensão do Azure Diagnostics](diagnostics-extension-overview.md)
- [Colete métricas personalizadas para um Linux VM com o agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md)
