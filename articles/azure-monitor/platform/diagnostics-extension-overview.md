---
title: Visão geral da extensão de Diagnóstico do Azure
description: Use o diagnóstico do Azure para depuração, medição de desempenho, monitoramento, análise de tráfego em serviços de nuvem, máquinas virtuais e Service Fabric
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 02/13/2019
ms.openlocfilehash: d1721411b57fc3542af48fc5f48eca7e4a2d06c8
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552145"
---
# <a name="what-is-azure-diagnostics-extension"></a>O que é Diagnóstico do Azure extensão
A extensão de Diagnóstico do Azure é um agente no Azure que habilita a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico de várias fontes diferentes. Atualmente, há suporte para funções Web e de trabalho do serviço de nuvem do Azure (clássico), máquinas virtuais, conjuntos de dimensionamento de máquinas virtuais e Service Fabric. Outros serviços do Azure têm métodos de diagnóstico diferentes. Consulte [visão geral do monitoramento no Azure](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Agente Linux
Uma [versão do Linux da extensão](../../virtual-machines/extensions/diagnostics-linux.md) está disponível para máquinas virtuais que executam o Linux. As estatísticas coletadas e o comportamento variam da versão do Windows.

## <a name="data-you-can-collect"></a>Dados que você pode coletar
A extensão Diagnóstico do Azure pode coletar os seguintes tipos de dados:

| Fonte de dados | Descrição |
| --- | --- |
| Métricas do contador de desempenho |Sistema operacional e contadores de desempenho personalizados |
| Logs de aplicativo |Rastrear mensagens gravadas pelo seu aplicativo |
| Logs de eventos do Windows |Informações enviadas ao sistema de log de eventos do Windows |
| Logs de EventSource do .NET |Eventos de gravação de código usando a classe [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) do .net |
| Registos do IIS |Informações sobre sites do IIS |
| [Logs ETW baseados em manifesto](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Rastreamento de eventos para eventos do Windows gerados por qualquer processo. uma |
| Despejos de memória (logs) |Informações sobre o estado do processo se um aplicativo falhar |
| Registos de erros personalizados |Logs criados por seu aplicativo ou serviço |
| Logs de infraestrutura de diagnóstico do Azure |Informações sobre Diagnóstico do Azure si mesmo |

(1) para obter uma lista de provedores de ETW, execute `c:\Windows\System32\logman.exe query providers` em uma janela de console no computador do qual você gostaria de coletar informações.

## <a name="data-storage"></a>Armazenamento de dados
A extensão armazena seus dados em uma [conta de armazenamento do Azure](diagnostics-extension-to-storage.md) que você especificar.

Você também pode enviá-lo para [Application insights](../../azure-monitor/app/cloudservices.md). 

Outra opção é transmitir para o [Hub de eventos](../../event-hubs/event-hubs-about.md), que permite enviá-lo aos serviços de monitoramento não Azure.

Você também tem a opção de enviar seus dados para Azure Monitor banco de dado de série temporal de métricas. Neste momento, esse coletor só é aplicável a contadores de desempenho. Ele permite que você envie contadores de desempenho no como métricas personalizadas. Este recurso está em versão prévia. O coletor de Azure Monitor dá suporte a:
* Recuperar todos os contadores de desempenho enviados para Azure Monitor por meio das [APIs de métricas de Azure monitor.](https://docs.microsoft.com/rest/api/monitor/)
* Alertas em todos os contadores de desempenho enviados para Azure Monitor por meio dos [alertas de métrica](../../azure-monitor/platform/alerts-overview.md) no Azure monitor
* Tratando o operador curinga em contadores de desempenho como a dimensão "instância" em sua métrica.  Por exemplo, se você coletou o contador "LogicalDisk (\*)/DiskWrites/sec", poderá filtrar e dividir na dimensão "instância" para plotar ou alertar as gravações de disco/s para cada disco lógico na VM (por exemplo, C:)

Para saber mais sobre como configurar esse coletor, consulte a documentação do [esquema de diagnóstico do Azure.](diagnostics-extension-schema-1dot3.md)

## <a name="costs"></a>Custos
Cada uma das opções acima pode incorrer em custos. Não se esqueça de Pesquisar para evitar faturas inesperadas.  Application Insights, o Hub de eventos e o armazenamento do Azure têm custos separados associados à ingestão e à hora armazenada. Em particular, o armazenamento do Azure manterá todos os dados para sempre, para que você possa desejar limpar os dados mais antigos após um determinado período de tempo para manter os custos inativos.    

## <a name="versioning-and-configuration-schema"></a>Esquema de controle de versão e configuração
Consulte [diagnóstico do Azure histórico e esquema de versão](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Passos seguintes
Escolha em qual serviço você está tentando coletar diagnósticos e use os artigos a seguir para começar. Use os links gerais de diagnóstico do Azure para referência a tarefas específicas.

## <a name="cloud-services-using-azure-diagnostics"></a>Serviços de nuvem usando Diagnóstico do Azure
* Se estiver usando o Visual Studio, consulte [usar o Visual Studio para rastrear um aplicativo de serviços de nuvem](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) para começar. Caso contrário, consulte
* [Como monitorar serviços de nuvem usando o Diagnóstico do Azure](../../cloud-services/cloud-services-how-to-monitor.md)
* [Configurar Diagnóstico do Azure em um aplicativo de serviços de nuvem](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Para obter tópicos mais avançados, consulte

* [Usando Diagnóstico do Azure com Application Insights para serviços de nuvem](../../azure-monitor/app/cloudservices.md)
* [Rastrear o fluxo de um aplicativo de serviços de nuvem com Diagnóstico do Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Usar o PowerShell para configurar o diagnóstico nos serviços de nuvem](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Máquinas Virtuais
* Se estiver usando o Visual Studio, consulte [usar o Visual Studio para rastrear as máquinas virtuais do Azure](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) para começar. Caso contrário, consulte
* [Configurar Diagnóstico do Azure em uma máquina virtual do Azure](/azure/virtual-machines/extensions/diagnostics-windows)

Para obter tópicos mais avançados, consulte

* [Usar o PowerShell para configurar o diagnóstico em máquinas virtuais do Azure](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Criar uma máquina virtual do Windows com monitoramento e diagnóstico usando Azure Resource Manager modelo](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Comece a [monitorar um aplicativo Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Muitos outros artigos de diagnóstico Service Fabric estão disponíveis na árvore de navegação à esquerda quando você chega a este artigo.

## <a name="general-articles"></a>Artigos gerais
* Aprenda a [usar contadores de desempenho no diagnóstico do Azure](../../cloud-services/diagnostics-performance-counters.md).
* Se você tiver problemas com o diagnóstico ao iniciar ou localizar seus dados nas tabelas do armazenamento do Azure, consulte [solução de problemas diagnóstico do Azure](diagnostics-extension-troubleshooting.md)

