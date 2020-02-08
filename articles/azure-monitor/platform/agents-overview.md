---
title: Visão geral dos agentes de monitorização do Azure Microsoft Docs
description: Este artigo fornece uma visão detalhada dos agentes Azure disponíveis que suportam a monitorização de máquinas virtuais alojadas em ambiente Azure ou híbrido.
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: ae799e9a852b8700399ef695c54b3348174b560c
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069409"
---
# <a name="overview-of-the-azure-monitor-agents"></a>Visão geral dos agentes do Monitor Azure 
Os recursos computacionais, como as máquinas virtuais, geram dados para monitorizar o seu desempenho e disponibilidade, tal como [outros recursos na nuvem.](../insights/monitor-azure-resource.md) Os recursos computacionais também têm um sistema operativo de hóspedes e cargas de trabalho que precisam de ser monitorizadas. Recolher estes dados de monitorização a partir de dentro do recurso requer um agente. Este artigo descreve os agentes utilizados pelo Azure Monitor e ajuda-o a determinar quais os requisitos necessários para satisfazer os requisitos para o seu ambiente específico.

## <a name="summary-of-agents"></a>Resumo dos agentes

> [!NOTE]
> O Azure Monitor conta atualmente com vários agentes devido à recente consolidação do Azure Monitor e do Log Analytics. Ambos os agentes partilham algumas capacidades, enquanto outras funcionalidades são únicas para um agente em particular. Dependendo dos seus requisitos, pode precisar de um dos agentes ou ambos. 

O Azure Monitor tem três agentes que cada um fornece uma funcionalidade específica. Dependendo dos seus requisitos, poderá instalar um único agente ou múltiplo nas suas máquinas virtuais e outros recursos computacionais.

* [Extensão de Diagnóstico Azure](#azure-diagnostic-extension)
* [Agente de Log Analytics](#log-analytics-agent)
* [Agente de dependência](#dependency-agent)

A tabela seguinte proporciona uma comparação rápida dos diferentes agentes. Consulte o resto deste artigo para mais detalhes sobre cada um.

| | Extensão de diagnóstico azure | Agente do Log Analytics | Agente de Dependência |
|:---|:---|:---|:---|
| Ambientes apoiados | Azure | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local |
| Sistemas operativos | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| Dependências de agentes  | Nenhuma | Nenhuma | Requer o agente Log Analytics |
| Dados recolhidos | Registos de Eventos<br>Eventos ETW<br>Syslog<br>Desempenho<br>Registos do IIS<br>.NET app rastreio logs de saída<br>Informações de falha de sistema | Registos de Eventos<br>Syslog<br>Desempenho<br>Registos do IIS<br>Registos personalizados<br>Dados de soluções | Detalhes e dependências do processo<br>Métricas de ligação de rede |
| Dados enviados para | Storage do Azure<br>Métricas de Azure Monitor<br>Hub de Eventos | Registos do Azure Monitor | Registos do Azure Monitor |



## <a name="azure-diagnostic-extension"></a>Extensão de diagnóstico do Azure
A [extensão Azure Diagnostics](../../azure-monitor/platform/diagnostics-extension-overview.md) recolhe dados de monitorização do sistema operativo dos hóspedes e cargas de trabalho dos recursos da computação Azure. Recolhe principalmente dados no Armazenamento Azure. Pode configurar o Monitor Azure para copiar os dados do armazenamento para um espaço de trabalho de Log Analytics. Também pode recolher dados de desempenho dos hóspedes em Métricas do Monitor Azure.

A extensão de diagnóstico azure é frequentemente referida como a extensão de Diagnóstico Do Windows Azure (WAD) ou do Diagnóstico Linux Azure (LAD).


### <a name="scenarios-supported"></a>Cenários apoiados

Os cenários suportados pela extensão do Azure Diagnostics incluem:

* Recolha registos e dados de desempenho dos recursos da computação Azure.
* Arquivo de registos e dados de desempenho do sistema operativo do hóspede para o armazenamento Azure.
* Ver dados de monitorização no armazenamento utilizando uma ferramenta como o [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
* Recolher dados de desempenho numa base de dados de métricas para tirar partido de funcionalidades suportadas por Métricas do [Monitor Azure,](data-platform-metrics.md) tais como [alertas métricos](../../azure-monitor/platform/alerts-metric-overview.md) em tempo real e [escala automática.](autoscale-overview.md) 
* Recolher dados de monitorização do armazenamento para um espaço de [trabalho do Log Analytics](azure-storage-iis-table.md) para tirar partido das funcionalidades suportadas por [Registos do Monitor Azure,](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) tais como consultas de [registo.](../log-query/log-query-overview.md)
* Envie dados de monitorização para ferramentas de terceiros utilizando hubs de [eventos azure](diagnostics-extension-stream-event-hubs.md).
* Investigue problemas de arranque da VM com diagnósticos de [arranque.](../../virtual-machines/troubleshooting/boot-diagnostics.md)
* Copie os dados das aplicações que estão a ser recorridas no seu VM [para A aplicação Insights](diagnostics-extension-to-application-insights.md) para se integrar com outras monitorizações de aplicações.

## <a name="log-analytics-agent"></a>Agente do Log Analytics
O [agente Log Analytics](log-analytics-agent.md) recolhe dados de monitorização do sistema operativo de hóspedes e cargas de trabalho de máquinas virtuais em Azure, outros fornecedores de nuvem e no local. Recolhe dados num espaço de trabalho do Log Analytics.

O agente Log Analytics é o mesmo agente utilizado pelo System Center Operations Manager, e computadores de agente multidomésticos para comunicar com o seu grupo de gestão e o Monitor Azure simultaneamente. Este agente também é exigido por certas soluções no Monitor Azure.

O agente Log Analytics para Windows é frequentemente referido como Microsoft Management Agent (MMA). O agente Log Analytics para o Linux é frequentemente referido como agente OMS.


### <a name="scenarios-supported"></a>Cenários apoiados

Os cenários suportados pelo agente Log Analytics incluem o seguinte:

* Recolha registos e dados de desempenho de máquinas virtuais em Azure, outros fornecedores de nuvem e no local. 
* Recolher dados de monitorização para um espaço de trabalho de Log Analytics para tirar partido das funcionalidades suportadas por [Registos do Monitor Do Azure,](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) tais como consultas de [log](../log-query/log-query-overview.md).
* Utilize soluções de monitorização do Monitor Azure, tais como [o Monitor Azure para VMs,](../insights/vminsights-overview.md) [Monitor Azure para contentores,](../insights/container-insights-overview.md)etc.  
* Gerencie a segurança das suas máquinas virtuais utilizando [o Azure Sentinel,](../../sentinel/overview.md) que requer o agente Log Analytics.
* Gerencie a segurança das suas máquinas virtuais utilizando o [Azure Security Center,](../../security-center/security-center-intro.md) que requer o agente Log Analytics.
* Utilize funcionalidades da [Azure Automation](../../automation/automation-intro.md) para oferecer uma gestão abrangente dos seus VMs Azure através do seu ciclo de vida.  Exemplos destas funcionalidades que requerem o agente Log Analytics incluem:
  * Gestão de atualizações do sistema operativo [Azure Automation Update.](../../automation/automation-update-management.md)
  * [Configuração do Estado da Automação Azure](../../automation/automation-dsc-overview.md) para manter um estado de configuração consistente.
  * Alterar a configuração da faixa com rastreio e inventário de [alterações de automatização azure](../../automation/change-tracking.md).

## <a name="dependency-agent"></a>Agente de Dependência
O agente da Dependência recolhe dados descobertos sobre processos em execução na máquina virtual e dependências de processos externos. Este agente é necessário para o Mapa de [Serviço](../insights/service-map.md) e a funcionalidade de mapa [Azure Monitor para VMs](../insights/vminsights-overview.md). O agente Dependency requer o agente Log Analytics e escreve dados para um espaço de trabalho de Log Analytics no Monitor Azure.


## <a name="using-multiple-agents"></a>Usando vários agentes
Pode ter requisitos específicos para utilizar a extensão de diagnóstico Azure ou o agente Log Analytics para uma determinada máquina virtual. Por exemplo, pode querer utilizar alertas métricos que requerem extensão de diagnóstico azure. Mas também pode querer utilizar a funcionalidade Map do Monitor Azure para VMs, que requer o agente dependency e o agente Log Analytics. Neste caso, pode utilizar vários agentes, e este é um cenário comum para clientes que necessitam de funcionalidade seletiva de cada um.

### <a name="considerations"></a>Considerações

- O agente Dependency exige que o agente Log Analytics seja instalado na mesma máquina virtual.
- Nos VMs Linux, o agente Log Analytics deve ser instalado antes da extensão de diagnóstico do Azure.


## <a name="next-steps"></a>Passos seguintes

- Consulte [a visão geral do agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) para rever os requisitos e métodos suportados para implementar o agente em máquinas hospedadas em Azure, no seu datacenter ou outro ambiente em nuvem.

