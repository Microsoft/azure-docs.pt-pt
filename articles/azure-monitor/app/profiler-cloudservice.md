---
title: Profile live Azure Cloud Services com Insights de Aplicação / Microsoft Docs
description: Ativar o Perfil de Insights de Aplicação para serviços de nuvem Azure.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 3fbeb1120e97a884135cd4622a49ef97fd43e58e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671669"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profile live Azure Cloud Services com Insights de Aplicação

Também pode implementar o Perfil de Insights de Aplicação nestes serviços:
* [Serviço de Aplicações do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplicações azure service fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

O Perfil insights de aplicação está instalado com a extensão de Diagnóstico Azure. Basta configurar o Azure Diagnostics para instalar o Profiler e enviar perfis para o seu recurso Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Ativar o Profiler para serviços de nuvem azure
1. Verifique se está a utilizar [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou mais recente. Se estiver a utilizar a família OS 4, terá de instalar .NET Framework 4.6.1 ou mais recente com uma tarefa de [arranque](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-install-dotnet). A Família OS 5 inclui uma versão compatível do .NET Framework por padrão. 

1. Adicione insights de [aplicação SDK aos serviços de nuvem azure](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **O bug no perfil que os navios do WAD para serviços de nuvem foi corrigido.** A versão mais recente do WAD (1.12.2.0) para serviços de cloud funciona com todas as versões recentes do App Insights SDK. Os anfitriões do Cloud Service atualizarão o WAD automaticamente, mas não é imediato. Para forçar uma atualização, pode recolocar o seu serviço ou reiniciar o nó.

1. Rastrear pedidos com Insights de Aplicação:

    * Para ASP.NET funções web, os Insights de Aplicação podem rastrear os pedidos automaticamente.

    * Para funções de trabalhador, [adicione código para rastrear pedidos](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Configure a extensão de diagnóstico sinuoso para ativar o Profiler:

    a. Localize o ficheiro [De diagnóstico sinuoso de Diagnóstico sinuoso.wadcfgx](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* para a sua função de candidatura, como mostra aqui:  

      ![Localização do ficheiro config de diagnóstico](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Se não encontrar o ficheiro, consulte [Configurar diagnósticos para Serviços de Nuvem Azure e Máquinas Virtuais](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Adicione a `SinksConfig` seguinte secção como `WadCfg`elemento infantil de:  

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
    > Se o ficheiro *diagnostic.wadcfgx* também contiver outro sumidouro do tipo ApplicationInsights, todas as três teclas de instrumentação devem coincidir:  
    > * A chave que é usada pela sua aplicação. 
    > * A chave que é usada pela pia ApplicationInsights. 
    > * A chave que é usada pela pia ApplicationInsightsProfiler. 
    >
    > Pode encontrar o valor real da chave de `ApplicationInsights` instrumentação que é usado pela pia na Configuração de *Serviço.\* ficheiros cscfg.* 
    > Após o lançamento do Estúdio Visual 15.5 Azure SDK, apenas as teclas de instrumentação que são utilizadas pela aplicação e a pia ApplicationInsightsProfiler precisam de combinar entre si.

1. Implemente o seu serviço com a nova configuração de Diagnósticos e o Perfil de Insights de Aplicação está configurado para ser executado no seu serviço.
 
## <a name="next-steps"></a>Passos seguintes

* Gere tráfego na sua aplicação (por exemplo, lance um [teste de disponibilidade).](monitor-web-app-availability.md) Em seguida, aguarde 10 a 15 minutos para que os vestígios comecem a ser enviados para a instância Deinsights de Aplicação.
* Consulte [os vestígios](profiler-overview.md?toc=/azure/azure-monitor/toc.json) do Profiler no portal Azure.
* Para resolver problemas com o Profiler, consulte a resolução de problemas do [Profiler.](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)
