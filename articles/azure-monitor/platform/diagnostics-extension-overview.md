---
title: Visão geral da extensão do Azure Diagnostics
description: Utilize diagnósticos Azure para depuração, medição do desempenho, monitorização, análise de tráfego em serviços na nuvem, máquinas virtuais e tecido de serviço
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 5dcdfba6e8dd00c8ba09e5e98293a30d19e51c99
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83635958"
---
# <a name="azure-diagnostics-extension-overview"></a>Visão geral da extensão do Azure Diagnostics
A extensão Azure Diagnostics é um [agente no Azure Monitor](agents-overview.md) que recolhe dados de monitorização do sistema operativo convidado de recursos computacionais do Azure, incluindo máquinas virtuais. Este artigo fornece uma visão geral da extensão do Azure Diagnostics, incluindo funcionalidade específica que suporta e opções de instalação e configuração. 

> [!NOTE]
> A extensão azure Diagnostics é um dos agentes disponíveis para recolher dados de monitorização do sistema operativo convidado de recursos computacionais. Consulte [a visão geral dos agentes do Monitor Azure](agents-overview.md) para obter uma descrição dos diferentes agentes e orientação sobre a seleção dos agentes apropriados para os seus requisitos.

## <a name="primary-scenarios"></a>Cenários principais
Os cenários primários abordados pela extensão de diagnósticos são:

- Colete as métricas dos hóspedes nas Métricas do Monitor Azure.
- Envie registos e métricas de hóspedes para o armazenamento azure para arquivamento.
- Envie registos e métricas de hóspedes para os centros de eventos azure para enviar para fora de Azure.


## <a name="comparison-to-log-analytics-agent"></a>Comparação com o agente Log Analytics
O agente Log Analytics no Monitor Azure também pode ser utilizado para recolher dados de monitorização do sistema operativo convidado de máquinas virtuais. Pode optar por utilizar ambos ou ambos, dependendo dos seus requisitos. Consulte [a visão geral dos agentes do Monitor Azure](agents-overview.md) para uma comparação detalhada dos agentes do Monitor Azure. 

As principais diferenças a ter em conta são:

- A extensão de diagnóstico azure só pode ser utilizada com máquinas virtuais Azure. O agente Log Analytics pode ser usado com máquinas virtuais em Azure, outras nuvens e no local.
- A extensão azure diagnostics envia dados para o Armazenamento Azure, [Métricas do Monitor Azure](data-platform-metrics.md) (apenas para windows) e Centros de Eventos. O agente Log Analytics recolhe dados para registos do [Monitor Azure](data-platform-logs.md).
- O agente Log Analytics é necessário para [soluções](../monitor-reference.md#insights-and-core-solutions), [Monitor Azure para VMs,](../insights/vminsights-overview.md)e outros serviços como [o Azure Security Center](/azure/security-center/).

## <a name="costs"></a>Custos
Não há qualquer custo para a Extensão de Diagnóstico do Azure, mas pode incorrer em encargos para os dados ingeridos. Consulte os preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para o destino onde está a recolher dados.

## <a name="data-collected"></a>Dados recolhidos
As tabelas seguintes listam os dados que podem ser recolhidos pela extensão de diagnóstico windows e Linux.

### <a name="windows-diagnostics-extension-wad"></a>Extensão de diagnóstico do Windows (WAD)

| Origem de Dados | Descrição |
| --- | --- |
| Registos do Evento Windows   | Eventos do registo de eventos do Windows. |
| Contadores de desempenho | Valores numéricos que medem o desempenho de diferentes aspetos do sistema operativo e cargas de trabalho. |
| Registos do IIS             | Informações de utilização para web sites IIS em execução no sistema operativo de hóspedes. |
| Registos de aplicação     | Rastreie mensagens escritas pela sua aplicação. |
| .NET Registos EventSource |Eventos de escrita de código usando a classe .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) |
| [Registos ETW baseados em manifesto](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Evento Rastreio para eventos Windows gerados por qualquer processo. |
| Despejos de colisão (troncos)   | Informações sobre o estado do processo se uma aplicação falhar. |
| Registos baseados em ficheiros    | Registos criados pela sua aplicação ou serviço. |
| Registos de diagnóstico do agente | Informação sobre o próprio Azure Diagnostics. |


### <a name="linux-diagnostics-extension-lad"></a>Extensão de diagnóstico linux (LAD)

| Origem de Dados | Descrição |
| --- | --- |
| Syslog | Eventos enviados para o sistema de registo de eventos Linux.   |
| Contadores de desempenho  | Valores numéricos que medem o desempenho de diferentes aspetos do sistema operativo e cargas de trabalho. |
| Ficheiros de registo | Entradas enviadas para um registo baseado em ficheiros.  |

## <a name="data-destinations"></a>Destinos de dados
A extensão De Diagnóstico Azure tanto para Windows como para Linux recolhe sempre dados numa conta de Armazenamento Azure. Consulte a instalação e configuração da extensão de [diagnóstico do Windows Azure (WAD)](diagnostics-extension-windows-install.md) e utilize a [extensão de diagnóstico do Linux para monitorizar métricas e registos](../../virtual-machines/extensions/diagnostics-linux.md) para obter uma lista de tabelas e bolhas específicas onde estes dados são recolhidos.

Configure um ou mais *afundados* de dados para enviar dados para outros destinos adicionais. As seguintes secções listam os lavatórios disponíveis para a extensão de diagnóstico Windows e Linux.

### <a name="windows-diagnostics-extension-wad"></a>Extensão de diagnóstico do Windows (WAD)

| Destino | Descrição |
|:---|:---|
| Métricas do Monitor Azure | Recolher dados de desempenho para as Métricas do Monitor Azure. Consulte [Enviar métricas de SO do Hóspede para a base](collect-custom-metrics-guestos-resource-manager-vm.md)de dados métrica do Monitor Azure .  |
| Hubs de Eventos | Utilize hubs de eventos Azure para enviar dados para fora do Azure. Consulte os dados de [Streaming Azure Diagnostics para Centros de Eventos](diagnostics-extension-stream-event-hubs.md) |
| Bolhas de armazenamento azure | Escreva aos dados para blobs no Armazenamento Azure, além de tabelas. |
| Application Insights | Recolha dados de aplicações em execução no seu VM a Insights de Aplicação para integrar com outras monitorizações de aplicações. Consulte [Enviar dados de diagnóstico para Informações de Aplicação](diagnostics-extension-to-application-insights.md). |

Também pode recolher dados wad do armazenamento para um espaço de trabalho de Log Analytics para analisá-los com Registos de Monitor Estoque, embora o agente Log Analytics seja normalmente utilizado para esta funcionalidade. Pode enviar dados diretamente para um espaço de trabalho log Analytics e suporta soluções e insights que fornecem funcionalidadeadicional.  Consulte [os registos de diagnóstico Azure do Armazenamento Azure](diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Extensão de diagnóstico linux (LAD)
Lad escreve dados para tabelas no Armazenamento Azure. Suporta os lavatórios na tabela seguinte.

| Destino | Descrição |
|:---|:---|
| Hubs de Eventos | Utilize hubs de eventos Azure para enviar dados para fora do Azure. |
| Bolhas de armazenamento azure | Escreva aos dados para blobs no Armazenamento Azure, além de tabelas. |
| Métricas do Monitor Azure | Instale o agente Telegraf para além do LAD. Consulte [Recolher métricas personalizadas para um VM Linux com o agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Instalação e configuração
A extensão de Diagnóstico é implementada como uma [extensão virtual](../../virtual-machines/extensions/overview.md) da máquina em Azure, por isso suporta as mesmas opções de instalação utilizando modelos de Gestor de Recursos, PowerShell e CLI. Consulte [extensões e funcionalidades da máquina Virtual para](../../virtual-machines/extensions/features-windows.md) extensões de máquinas Windows e Virtual e [funcionalidades para o Linux](../../virtual-machines/extensions/features-linux.md) para obter detalhes gerais sobre a instalação e manutenção de extensões de máquinas virtuais.

Também pode instalar e configurar a extensão de diagnóstico windows e linux no portal Azure sob **as definições** de Diagnóstico na secção **de Monitorização** do menu da máquina virtual.

Consulte os seguintes artigos para obter mais informações sobre a instalação e configuração da extensão de diagnóstico para Windows e Linux.

- [Instalar e configurar extensão de diagnóstico do Windows Azure (WAD)](diagnostics-extension-windows-install.md)
- [Using Linux Diagnostic Extension to monitor metrics and logs](../../virtual-machines/extensions/diagnostics-linux.md) (Utilizar a Extensão de Diagnóstico do Linux para monitorizar métricas e registos)

## <a name="other-documentation"></a>Outra documentação

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure Cloud Service (clássico) Web e Funções de Trabalhador
- [Introdução à Monitorização do Serviço de Nuvem](../../cloud-services/cloud-services-how-to-monitor.md)
- [Habilitar diagnósticos azure em serviços de nuvem azure](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Informações de aplicação para serviços de nuvem Azure](../app/cloudservices.md)<br>[Trace o fluxo de uma aplicação de Serviços cloud com diagnósticos azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Monitorizar e diagnosticar serviços numa configuração de desenvolvimento do computador local](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Passos seguintes


* Aprenda a [utilizar contadores de desempenho em diagnósticos Azure](../../cloud-services/diagnostics-performance-counters.md).
* Se tiver problemas com diagnósticos a começar ou a encontrar os seus dados nas mesas de armazenamento do Azure, consulte [TroubleShooting Azure Diagnostics](diagnostics-extension-troubleshooting.md)

