---
title: Perfil de serviços de nuvem do Azure em tempo real com Application Insights | Microsoft Docs
description: Habilite o Application Insights Profiler para os serviços de nuvem do Azure.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 93392e379cbb03508fefc1877d5d50e04436b79c
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737221"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Perfil de serviços de nuvem do Azure dinâmicos com Application Insights

Você também pode implantar Application Insights Profiler nesses serviços:
* [Serviço de Aplicações do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplicativos Service Fabric do Azure](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

O Application Insights Profiler é instalado com a extensão Diagnóstico do Azure. Você só precisa configurar Diagnóstico do Azure para instalar o Profiler e enviar perfis para o recurso de Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Habilitar o Profiler para serviços de nuvem do Azure
1. Verifique se você está usando [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou mais recente. Se estiver usando a família de sistemas operacionais 4, você precisará instalar .NET Framework 4.6.1 ou mais recente com uma [tarefa de inicialização](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-dotnet-install-dotnet). A família 5 do so inclui uma versão compatível do .NET Framework por padrão. 

1. Adicionar [Application insights SDK aos serviços de nuvem do Azure](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **O bug no criador de perfil que acompanha o WAD para serviços de nuvem foi corrigido.** A versão mais recente do WAD (1.12.2.0) para serviços de nuvem funciona com todas as versões recentes do SDK do App insights. Os hosts do serviço de nuvem atualizarão automaticamente o WAD, mas não será imediato. Para forçar uma atualização, você pode reimplantar o serviço ou reinicializar o nó.

1. Rastrear solicitações com Application Insights:

    * Para funções Web ASP.NET, Application Insights pode rastrear as solicitações automaticamente.

    * Para funções de trabalho, [adicione o código para rastrear solicitações](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Configure a extensão de Diagnóstico do Azure para habilitar o Profiler:

    a. Localize o arquivo [diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *Diagnostics. wadcfgx* para sua função de aplicativo, conforme mostrado aqui:  

      ![Local do arquivo de configuração de diagnóstico](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Se você não encontrar o arquivo, consulte [Configurar o diagnóstico para os serviços de nuvem do Azure e máquinas virtuais](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Adicione a seguinte `SinksConfig` seção como um elemento filho de `WadCfg`:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > Se o arquivo *Diagnostics. wadcfgx* também contiver outro coletor do tipo ApplicationInsights, todas as três seguintes chaves de instrumentação deverão corresponder:  
    > * A chave usada pelo seu aplicativo. 
    > * A chave usada pelo coletor ApplicationInsights. 
    > * A chave usada pelo coletor ApplicationInsightsProfiler. 
    >
    > Pode encontrar o valor de chave de instrumentação real é utilizado pelos `ApplicationInsights` serem digeridos a *ServiceConfiguration.\*.cscfg* ficheiros. 
    > Após a versão do SDK do Azure do Visual Studio 15,5, somente as chaves de instrumentação usadas pelo aplicativo e o coletor ApplicationInsightsProfiler precisam corresponder umas às outras.

1. Implante seu serviço com a nova configuração de diagnóstico e Application Insights Profiler está configurado para ser executado em seu serviço.
 
## <a name="next-steps"></a>Passos Seguintes

* Gere o tráfego para seu aplicativo (por exemplo, inicie um [teste de disponibilidade](monitor-web-app-availability.md)). Em seguida, aguarde de 10 a 15 minutos para que os rastreamentos comecem a ser enviados para a instância de Application Insights.
* Consulte [rastreamentos](profiler-overview.md?toc=/azure/azure-monitor/toc.json) do profiler no portal do Azure.
* Para solucionar problemas do criador de perfil, consulte [solução de problemas](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)do profiler.
