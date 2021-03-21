---
title: Descrição geral da extensão do Diagnóstico do Azure
description: Utilize diagnósticos Azure para depuração, medição de desempenho, monitorização, análise de tráfego em serviços na nuvem, máquinas virtuais e tecido de serviço
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 90addb74811819dec563ba072e9a6aea2cf72ca4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036542"
---
# <a name="azure-diagnostics-extension-overview"></a>Descrição geral da extensão do Diagnóstico do Azure
A extensão Azure Diagnostics é um [agente no Azure Monitor](../agents/agents-overview.md) que recolhe dados de monitorização do sistema operativo convidado de recursos compute da Azure, incluindo máquinas virtuais. Este artigo fornece uma visão geral da extensão do Azure Diagnostics, incluindo funcionalidade específica que suporta e opções de instalação e configuração. 

> [!NOTE]
> A extensão Azure Diagnostics é um dos agentes disponíveis para recolher dados de monitorização do sistema operativo de recursos computatórios dos hóspedes. Consulte [a visão geral dos agentes do Monitor Azure ](../agents/agents-overview.md) para obter uma descrição dos diferentes agentes e orientações sobre a seleção dos agentes adequados para os seus requisitos.

## <a name="primary-scenarios"></a>Cenários principais
Os cenários primários abordados pela extensão do diagnóstico são:

- Colete as métricas dos hóspedes em Azure Monitor Metrics.
- Envie registos e métricas de hóspedes para o armazenamento Azure para arquivar.
- Envie registos e métricas de hóspedes para os centros de eventos Azure para enviar fora de Azure.


## <a name="comparison-to-log-analytics-agent"></a>Comparação com o agente Log Analytics
O agente Log Analytics no Azure Monitor também pode ser utilizado para recolher dados de monitorização do sistema operativo de hóspedes de máquinas virtuais. Pode optar por utilizar qualquer um ou ambos dependendo dos seus requisitos. Consulte [a visão geral dos agentes do Monitor Azure](../agents/agents-overview.md) para uma comparação detalhada dos agentes do Monitor Azure. 

As principais diferenças a ter em conta são:

- A extensão de diagnóstico Azure só pode ser usada com máquinas virtuais Azure. O agente Log Analytics pode ser utilizado com máquinas virtuais em Azure, outras nuvens e no local.
- A extensão Azure Diagnostics envia dados para Azure Storage, [Azure Monitor Metrics (apenas](../essentials/data-platform-metrics.md) Windows) e Centros de Eventos. O agente Log Analytics recolhe dados para os [registos do Monitor Azure](../logs/data-platform-logs.md).
- O agente Log Analytics é necessário para [soluções,](../monitor-reference.md#insights-and-core-solutions) [insights VM,](../vm/vminsights-overview.md)e outros serviços como [o Azure Security Center](../../security-center/index.yml).

## <a name="costs"></a>Custos
Não há custo para a Extensão de Diagnóstico Azure, mas pode incorrer em encargos para os dados ingeridos. Verifique [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para o destino onde está a recolher dados.

## <a name="data-collected"></a>Dados recolhidos
As tabelas que se seguem listam os dados que podem ser recolhidos pela extensão de diagnóstico do Windows e do Linux.

### <a name="windows-diagnostics-extension-wad"></a>Extensão de diagnóstico do Windows (WAD)

| Origem de dados | Description |
| --- | --- |
| Registos do Evento Windows   | Eventos do registo de eventos do Windows. |
| Contadores de desempenho | Valores numéricos que medem o desempenho de diferentes aspetos do sistema operativo e cargas de trabalho. |
| Registos do IIS             | Informações de utilização para sites IIS em execução no sistema operativo do hóspede. |
| Registos de aplicações     | Trace mensagens escritas pela sua aplicação. |
| Registos .NET EventSource |Eventos de escrita de código usando a classe .NET [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) |
| [Registos ETW baseados em manifesto](/windows/desktop/etw/about-event-tracing) |Rastreio de eventos para eventos Windows gerados por qualquer processo. |
| Despejos de colisão (troncos)   | Informação sobre o estado do processo se uma aplicação falhar. |
| Registos baseados em ficheiros    | Registos criados pela sua aplicação ou serviço. |
| Registos de diagnóstico de agentes | Informação sobre o próprio Azure Diagnostics. |


### <a name="linux-diagnostics-extension-lad"></a>Extensão de diagnóstico linux (LAD)

| Origem de dados | Description |
| --- | --- |
| Syslog | Eventos enviados para o sistema de registo de eventos Linux.   |
| Contadores de desempenho  | Valores numéricos que medem o desempenho de diferentes aspetos do sistema operativo e cargas de trabalho. |
| Ficheiros de registo | Entradas enviadas para um registo baseado em ficheiros.  |

## <a name="data-destinations"></a>Destinos de dados
A extensão Azure Diagnostic para Windows e Linux recolhe sempre dados numa conta de Armazenamento Azure. Consulte [instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)](diagnostics-extension-windows-install.md) e [utilizar a Extensão de Diagnóstico do Linux para monitorizar métricas e registos](../../virtual-machines/extensions/diagnostics-linux.md) para obter uma lista de tabelas e bolhas específicas onde estes dados são recolhidos.

Configure um ou mais *sumidouros de dados* para enviar dados para outros destinos adicionais. As secções seguintes listam os lavatórios disponíveis para a extensão de diagnósticos Windows e Linux.

### <a name="windows-diagnostics-extension-wad"></a>Extensão de diagnóstico do Windows (WAD)

| Destino | Description |
|:---|:---|
| Métricas do Azure Monitor | Recolher dados de desempenho para Azure Monitor Metrics. Consulte [as métricas do Guest OS para a base de dados métrica do Azure Monitor](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Hubs de Eventos | Utilize os Hubs de Eventos Azure para enviar dados para fora do Azure. Ver [dados de streaming Azure Diagnostics para Centros de Eventos](diagnostics-extension-stream-event-hubs.md) |
| Bolhas de armazenamento Azure | Escreva para os dados para bolhas no Azure Storage, além de tabelas. |
| Application Insights | Recolher dados de aplicações em execução no seu VM para Application Insights para integrar-se com outra monitorização de aplicações. Consulte [Enviar dados de diagnóstico para Insights de Aplicação](diagnostics-extension-to-application-insights.md). |

Também pode recolher dados do WAD do armazenamento num espaço de trabalho do Log Analytics para analisá-los com Registos monitores Azure, embora o agente Log Analytics seja normalmente utilizado para esta funcionalidade. Pode enviar dados diretamente para um espaço de trabalho do Log Analytics e suporta soluções e insights que fornecem funcionalidades adicionais.  Consulte [os registos de diagnóstico do Azure da Azure Storage](../agents/diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Extensão de diagnóstico linux (LAD)
Lad escreve dados para tabelas no Azure Storage. Suporta os lavatórios na mesa seguinte.

| Destino | Description |
|:---|:---|
| Hubs de Eventos | Utilize os Hubs de Eventos Azure para enviar dados para fora do Azure. |
| Bolhas de armazenamento Azure | Escreva para os dados para bolhas no Azure Storage, além de tabelas. |
| Métricas do Azure Monitor | Instale o agente Telegraf para além do LAD. Consulte [recolher métricas personalizadas para um Linux VM com o agente InfluxData Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Instalação e configuração
A extensão de Diagnóstico é implementada como uma [extensão de máquina virtual](../../virtual-machines/extensions/overview.md) em Azure, por isso suporta as mesmas opções de instalação usando modelos de Gestor de Recursos, PowerShell e CLI. Consulte [extensões e funcionalidades de máquinas virtuais para](../../virtual-machines/extensions/features-windows.md) [extensões e funcionalidades de máquinas Virtual e Windows e Virtual para linux](../../virtual-machines/extensions/features-linux.md) para detalhes gerais sobre a instalação e manutenção de extensões de máquinas virtuais.

Também pode instalar e configurar a extensão de diagnóstico Windows e Linux no portal Azure em **definições de Diagnóstico** na secção de **Monitorização** do menu da máquina virtual.

Consulte os seguintes artigos para obter mais informações sobre a instalação e configuração da extensão de diagnóstico para Windows e Linux.

- [Instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)](diagnostics-extension-windows-install.md)
- [Using Linux Diagnostic Extension to monitor metrics and logs](../../virtual-machines/extensions/diagnostics-linux.md) (Utilizar a Extensão de Diagnóstico do Linux para monitorizar métricas e registos)

## <a name="other-documentation"></a>Outra documentação

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure Cloud Service (clássico) Web e Funções de Trabalhador
- [Introdução à Monitorização do Serviço de Nuvem](../../cloud-services/cloud-services-how-to-monitor.md)
- [Habilitar diagnósticos Azure em Serviços cloud Azure](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Insights de Aplicação para serviços em nuvem Azure](../app/cloudservices.md)<br>[Trace o fluxo de uma aplicação de Serviços cloud com diagnósticos Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Monitorizar e diagnosticar serviços numa configuração de desenvolvimento do computador local](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Passos seguintes


* Aprenda a [utilizar contadores de desempenho em diagnósticos Azure](../../cloud-services/diagnostics-performance-counters.md).
* Se tiver problemas com diagnósticos a começar ou a encontrar os seus dados em mesas de armazenamento Azure, consulte [o TroubleShooting Azure Diagnostics](diagnostics-extension-troubleshooting.md)