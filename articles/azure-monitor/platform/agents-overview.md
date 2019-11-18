---
title: Visão geral dos agentes de monitoramento do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral detalhada dos agentes do Azure disponíveis que dão suporte ao monitoramento de máquinas virtuais hospedadas no Azure ou ambiente híbrido.
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: a01258799efa81c8d3ddba398facaa90c24c2513
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150039"
---
# <a name="overview-of-the-azure-monitor-agents"></a>Visão geral dos agentes de Azure Monitor 
Os recursos de computação, como máquinas virtuais, geram dados para monitorar o desempenho e a disponibilidade, assim como [outros recursos de nuvem](../insights/monitor-azure-resource.md). No entanto, os recursos de computação também têm um sistema operacional convidado e as cargas de trabalho que precisam ser monitoradas. Coletar esses dados de monitoramento de dentro do recurso requer um agente. Este artigo descreve os agentes usados pelo Azure Monitor e ajuda a determinar qual é necessário para atender aos requisitos para seu ambiente específico.

## <a name="summary-of-agents"></a>Resumo dos agentes

> [!NOTE]
> Atualmente, Azure Monitor tem vários agentes devido a uma consolidação recente de Azure Monitor e Log Analytics. Ambos os agentes compartilham alguns recursos, enquanto outros recursos são exclusivos de um agente específico. Dependendo de seus requisitos, talvez seja necessário um dos agentes ou ambos. 

Azure Monitor tem três agentes que fornecem uma funcionalidade específica. Dependendo dos seus requisitos, você pode instalar um único agente ou vários em suas máquinas virtuais e outros recursos de computação.

* [Extensão de Diagnóstico do Azure](#azure-diagnostic-extension)
* [Agente de Log Analytics](#log-analytics-agent)
* [Agente de dependência](#dependency-agent)

A tabela a seguir fornece uma comparação rápida dos diferentes agentes. Consulte o restante deste artigo para obter detalhes sobre cada um.

| | Extensão de diagnóstico do Azure | Agente de Log Analytics | Agente de Dependência |
|:---|:---|:---|:---|
| Ambientes com suporte | Azure | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local |
| Sistemas operativos | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| Dependências de agente  | Nenhum | Nenhum | Requer agente de Log Analytics |
| Dados recolhidos | Registos de Eventos<br>Eventos ETW<br>Syslog<br>Desempenho<br>Registos do IIS<br>Logs de saída de rastreamento de aplicativo .NET<br>Informações de falha de sistema | Registos de Eventos<br>Syslog<br>Desempenho<br>Registos do IIS<br>Registos personalizados<br>Dados de soluções | Detalhes e dependências do processo<br>Métricas de conexão de rede |
| Dados enviados para | Storage do Azure<br>Métricas de Azure Monitor<br>Hub de Eventos | Registos do Azure Monitor | Registos do Azure Monitor |



## <a name="azure-diagnostic-extension"></a>Extensão de diagnóstico do Azure
A [extensão diagnóstico do Azure](../../azure-monitor/platform/diagnostics-extension-overview.md) coleta dados de monitoramento do sistema operacional convidado e das cargas de trabalho dos recursos de computação do Azure. Ele coleta principalmente dados no armazenamento do Azure. Você pode configurar Azure Monitor para copiar os dados do armazenamento para um espaço de trabalho do Log Analytics. Você também pode coletar dados de desempenho de convidado em métricas de Azure Monitor.

A extensão de diagnóstico do Azure é geralmente conhecida como a extensão WAD (diagnóstico do Windows Azure) ou LAD (diagnóstico do Azure do Linux).


### <a name="scenarios-supported"></a>Cenários com suporte

Os cenários com suporte pela extensão de Diagnóstico do Azure incluem o seguinte:

* Colete logs e dados de desempenho dos recursos de computação do Azure.
* Arquive logs e dados de desempenho do sistema operacional convidado para o armazenamento do Azure.
* Exibir dados de monitoramento no armazenamento usando uma ferramenta como o [Gerenciador de armazenamento do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
* Coletar dados de desempenho em um banco de dado de métricas para aproveitar os recursos com suporte pelas [métricas de Azure monitor](data-platform-metrics.md) , como [alertas de métrica](../../azure-monitor/platform/alerts-metric-overview.md) quase em tempo real e [dimensionamento automático](autoscale-overview.md). 
* Colete dados de monitoramento do [armazenamento para um espaço de trabalho log Analytics](azure-storage-iis-table.md) para aproveitar os recursos com suporte dos [logs de Azure monitor](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , como consultas de [log](../log-query/log-query-overview.md).
* Envie dados de monitoramento para ferramentas de terceiros usando os [hubs de eventos do Azure](diagnostics-extension-stream-event-hubs.md).
* Investigue os problemas de inicialização da VM com o [diagnóstico de inicialização](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Copie dados de aplicativos em execução em sua VM [para Application insights](diagnostics-extension-to-application-insights.md) para integrar com outro monitoramento de aplicativo.

## <a name="log-analytics-agent"></a>Agente de Log Analytics
O [agente de log Analytics](log-analytics-agent.md) coleta dados de monitoramento do sistema operacional convidado e cargas de trabalho de máquinas virtuais no Azure, outros provedores de nuvem e locais. Ele coleta dados em um espaço de trabalho Log Analytics.

O agente de Log Analytics é o mesmo agente usado pelo System Center Operations Manager e os computadores de agente multihome para se comunicarem com o grupo de gerenciamento e Azure Monitor simultaneamente. Esse agente também é exigido por determinadas soluções no Azure Monitor.

O agente de Log Analytics para Windows é geralmente chamado de MMA (agente de gerenciamento da Microsoft). O agente de Log Analytics para Linux é geralmente conhecido como agente do OMS.


### <a name="scenarios-supported"></a>Cenários com suporte

Os cenários com suporte pelo agente de Log Analytics incluem o seguinte:

* Colete logs e dados de desempenho de máquinas virtuais no Azure, outros provedores de nuvem e locais. 
* Colete dados de monitoramento para um espaço de trabalho Log Analytics para aproveitar os recursos com suporte dos [logs de Azure monitor](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , como [consultas de log](../log-query/log-query-overview.md).
* Use Azure Monitor soluções de monitoramento, como [Azure monitor para VMs](../insights/vminsights-overview.md), [Azure monitor para contêineres](../insights/container-insights-overview.md), etc.  
* Gerencie a segurança de suas máquinas virtuais usando o [Azure Sentinel](../../sentinel/overview.md) , que requer o agente de log Analytics.
* Gerencie a segurança de suas máquinas virtuais usando a [central de segurança do Azure](../../security-center/security-center-intro.md) , que requer o agente de log Analytics.
* Use os recursos da [automação do Azure](../../automation/automation-intro.md) para fornecer gerenciamento abrangente de suas VMs do Azure por meio de seu ciclo de vida.  Exemplos desses recursos que exigem o agente de Log Analytics incluem:
  * [Gerenciamento de atualização de automação do Azure](../../automation/automation-update-management.md) de atualizações do sistema operacional.
  * [Configuração de estado da automação do Azure](../../automation/automation-dsc-overview.md) para manter o estado de configuração consistente.
  * Acompanhe as alterações de configuração com a [automação do Azure controle de alterações e inventário](../../automation/change-tracking.md).

## <a name="dependency-agent"></a>Agente de Dependência
O agente de dependência coleta dados descobertos sobre processos em execução na máquina virtual e dependências de processo externo. Esse agente é necessário para [mapa do serviço](../insights/service-map.md) e o recurso de mapa [Azure monitor para VMs](../insights/vminsights-overview.md). O agente de dependência requer o agente de Log Analytics e grava dados em um espaço de trabalho Log Analytics no Azure Monitor.


## <a name="using-multiple-agents"></a>Usando vários agentes
Você pode ter requisitos específicos para usar a extensão de diagnóstico do Azure ou o agente de Log Analytics para uma determinada máquina virtual. Por exemplo, talvez você queira usar alertas de métrica que exijam a extensão de diagnóstico do Azure. Mas talvez você também queira usar o recurso de mapa de Azure Monitor para VMs que exige o agente de dependência e o agente de Log Analytics. Nesse caso, você pode usar vários agentes, e esse é um cenário comum para clientes que exigem funcionalidade de cada um.

### <a name="considerations"></a>Considerações

- O agente de dependência requer que o agente de Log Analytics seja instalado na mesma máquina virtual.
- Em VMs do Linux, o agente de Log Analytics deve ser instalado antes da extensão de diagnóstico do Azure.


## <a name="next-steps"></a>Passos seguintes

- Consulte [visão geral do agente de log Analytics](../../azure-monitor/platform/log-analytics-agent.md) para examinar os requisitos e os métodos com suporte para implantar o agente em computadores hospedados no Azure, em seu datacenter ou em outro ambiente de nuvem.

