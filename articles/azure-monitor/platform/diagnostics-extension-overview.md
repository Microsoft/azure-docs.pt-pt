---
title: Descrição geral da extensão do diagnóstico do Azure
description: Utilizar o diagnóstico do Azure para depuração, avaliar o desempenho, monitoramento, análise de tráfego nos serviços cloud, máquinas virtuais e o service fabric
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 8a287f118c126967d2cf8cad77a434cfecc098eb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078544"
---
# <a name="what-is-azure-diagnostics-extension"></a>O que é a extensão de diagnóstico do Azure
A extensão de diagnóstico do Azure é um agente no Azure que permite a recolha de dados de diagnóstico num aplicativo implantado. Pode utilizar a extensão de diagnóstico de várias origens diferentes. Atualmente suportados são Web do serviço de Cloud do Azure (clássico) e funções de trabalho, máquinas virtuais, a Máquina Virtual Dimensionar conjuntos e o Service Fabric. Outros serviços do Azure têm métodos de diagnóstico diferentes. Ver [descrição geral da monitorização no Azure](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Agente Linux
R [Linux versão da extensão](../../virtual-machines/extensions/diagnostics-linux.md) está disponível para máquinas virtuais em execução no Linux. As estatísticas recolhidas e comportamento variam em relação a versão do Windows.

## <a name="data-you-can-collect"></a>Pode recolher de dados
A extensão de diagnóstico do Azure pode recolher os seguintes tipos de dados:

| Origem de Dados | Descrição |
| --- | --- |
| Métricas de contador de desempenho |Sistema operativo e contadores de desempenho personalizado |
| Registos de aplicações |Mensagens de rastreio escritas pelo seu aplicativo |
| Registos de Eventos do Windows |Informações enviadas para o sistema de Registro em log de eventos do Windows |
| Registos de EventSource de .NET |Código de escrita de eventos usando o .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) classe |
| Registos do IIS |Informações sobre os web sites do IIS |
| [Manifesto com base em registos ETW](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Eventos de rastreio para o Windows de eventos gerados por algum processo. (1) |
| Falha de sistema (logs) |Informações sobre o estado do processo no caso de falha de uma aplicação |
| Registos de erros personalizados |Registos criados pela sua aplicação ou serviço |
| Registos de infraestrutura de diagnóstico do Azure |Informações sobre o diagnóstico do Azure em si |

(1) para obter uma lista de fornecedores ETW, execute `c:\Windows\System32\logman.exe query providers` numa janela de consola no computador que pretende recolher informações de.

## <a name="data-storage"></a>Armazenamento de dados
A extensão armazena os respetivos dados num [conta de armazenamento do Azure](diagnostics-extension-to-storage.md) que especificar.

Também pode enviá-lo para [Application Insights](../../azure-monitor/app/cloudservices.md). 

Outra opção é transmiti-lo para [Hub de eventos](../../event-hubs/event-hubs-about.md), que depois permite-lhe enviar para o serviços de monitorização não pertencente ao Azure.

Tem também a opção de enviar seus dados para a base de dados de séries de tempo de métricas do Azure Monitor. Neste momento, este sink só é aplicável a contadores de desempenho. Permite-lhe enviar contadores de desempenho na como métricas personalizadas. Esta funcionalidade está em pré-visualização. Suporta o sink do Azure Monitor:
* Obter todos os contadores de desempenho enviados para o Azure Monitor através do [métricas do Azure Monitor APIs.](https://docs.microsoft.com/rest/api/monitor/)
* Alertas em todos os contadores de desempenho enviados para o Azure Monitor através da [alertas de métricas](../../azure-monitor/platform/alerts-overview.md) no Azure Monitor
* Ao tratar o operador de caráter universal no contadores de desempenho como a dimensão de "Instância" em sua métrica.  Por exemplo, se recolheu o "disco lógico (\*) / DiskWrites/seg" contador deve ser capaz de filtro e dividir a dimensão de "Instância" plotagem ou alertar relativamente a escritas de disco/seg para cada disco lógico na VM (por exemplo, c)

Para saber mais sobre como configurar este sink, consulte o [documentação de esquema de diagnóstico do Azure.](diagnostics-extension-schema-1dot3.md)

## <a name="costs"></a>Custos
Cada uma das opções acima pode incorrer em custos. Certifique-se de que pesquisá-los para evitar faturas inesperadas.  O Application Insights, hub de eventos, e o armazenamento do Azure tem custos separados associados à ingestão e a hora de armazenados. Em particular, armazenamento do Azure irá conter todos os dados indefinidamente pelo que deve remover os dados mais antigos após um determinado período de tempo para manter os custos baixos.    

## <a name="versioning-and-configuration-schema"></a>Esquema de controlo de versões e a configuração
Ver [histórico de versões do diagnóstico do Azure e o esquema](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Passos Seguintes
Escolha qual serviço está a tentar recolher diagnósticos em e utilize os seguintes artigos para começar a utilizar. Utilize as ligações de gerais de diagnóstico do Azure para referência para tarefas específicas.

## <a name="cloud-services-using-azure-diagnostics"></a>Serviços cloud com o diagnóstico do Azure
* Se utilizar o Visual Studio, consulte [Use o Visual Studio para rastrear um aplicativo de serviços Cloud](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) para começar a utilizar. Caso contrário, consulte
* [Como monitorizar serviços em nuvem com o diagnóstico do Azure](../../cloud-services/cloud-services-how-to-monitor.md)
* [Configurar o diagnóstico do Azure numa aplicação de serviços Cloud](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Para tópicos mais avançados, consulte

* [Utilizar o diagnóstico do Azure com o Application Insights para serviços em nuvem](../../azure-monitor/app/cloudservices.md)
* [Rastrear o fluxo de um aplicativo de serviços em nuvem com o diagnóstico do Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Utilize o PowerShell para configurar diagnósticos nos serviços Cloud](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtual Machines
* Se utilizar o Visual Studio, consulte [Use o Visual Studio para máquinas virtuais do Azure de rastreio](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) para começar a utilizar. Caso contrário, consulte
* [Configurar o diagnóstico do Azure numa máquina Virtual do Azure](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

Para tópicos mais avançados, consulte

* [Utilize o PowerShell para configurar diagnósticos em máquinas de virtuais do Azure](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Criar uma Máquina Virtual do Windows com monitorização e diagnóstico com o modelo do Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Comece a utilizar [monitorizar uma aplicação do Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Muitos outros artigos de diagnóstico de Service Fabric estão disponíveis na árvore de navegação à esquerda depois de obter a este artigo.

## <a name="general-articles"></a>Artigos gerais
* Aprenda a [utilizar contadores de desempenho no diagnóstico do Azure](../../cloud-services/diagnostics-performance-counters.md).
* Se tiver problemas com a partir de diagnóstico ou localizar os seus dados nas tabelas de armazenamento do Azure, consulte [resolução de problemas do diagnóstico do Azure](diagnostics-extension-troubleshooting.md)

