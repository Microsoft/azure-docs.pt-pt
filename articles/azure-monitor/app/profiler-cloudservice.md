---
title: Perfil ao vivo Azure Cloud Services com Insights de Aplicações / Microsoft Docs
description: Ativar o Profiler de Insights de Aplicações para serviços em nuvem Azure.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 3fbeb1120e97a884135cd4622a49ef97fd43e58e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77671669"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Perfil ao vivo Serviços em Nuvem Azure com Insights de Aplicações

Também pode implementar o Profiler Application Insights nestes serviços:
* [Serviço de Aplicações do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplicações de tecido de serviço Azure](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

O Profiler Application Insights está instalado com a extensão Azure Diagnostics. Basta configurar o Azure Diagnostics para instalar o Profiler e enviar perfis para o seu recurso Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Ativar o Profiler para serviços em nuvem Azure
1. Verifique se está a utilizar [o Quadro .NET 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou mais recente. Se estiver a utilizar a família OS 4, terá de instalar o .NET Framework 4.6.1 ou mais recente com uma [tarefa de arranque.](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-install-dotnet) Os Family 5 inclui uma versão compatível do Quadro .NET por padrão. 

1. Adicionar [Insights de Aplicação SDK aos serviços de cloud Azure](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **O bug no perfil que envia no WAD para serviços cloud foi corrigido.** A versão mais recente do WAD (1.12.2.0) para Serviços cloud funciona com todas as versões recentes da App Insights SDK. Os anfitriões do Cloud Service atualizarão o WAD automaticamente, mas não é imediato. Para forçar uma atualização, pode recolocar o seu serviço ou reiniciar o nó.

1. Rastrear pedidos com Insights de Aplicação:

    * Para ASP.NET funções web, o Application Insights pode rastrear os pedidos automaticamente.

    * Para funções de trabalhador, [adicione código para rastrear pedidos](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Configure a extensão Azure Diagnostics para permitir o Profiler:

    a. Localize o ficheiro [Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *Diagnostics.wadcfgx* para a sua função de candidatura, como mostrado aqui:  

      ![Localização do ficheiro config de diagnóstico](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Se não conseguir encontrar o ficheiro, consulte [configurar diagnósticos para serviços de nuvem azure e máquinas virtuais.](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

    b. Adicione a seguinte `SinksConfig` secção como elemento infantil `WadCfg` de:  

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
    > Se o ficheiro *diagnostic.wadcfgx* também contiver outro lavatório do tipo ApplicationInsights, todas as três teclas de instrumentação devem coincidir:  
    > * A chave que é usada pela sua aplicação. 
    > * A chave que é usada pela pia ApplicationInsights. 
    > * A chave que é usada pelo sinker ApplicationInsightsProfiler. 
    >
    > Pode encontrar o valor real da chave de instrumentação que é usado pela `ApplicationInsights` pia na Configuração de * \* Serviço. ficheiros cscfg.* 
    > Após o lançamento do Visual Studio 15.5 Azure SDK, apenas as teclas de instrumentação utilizadas pela aplicação e a pia ApplicationInsightsProfiler precisam de combinar entre si.

1. Implemente o seu serviço com a nova configuração de Diagnóstico e o Application Insights Profiler está configurado para executar no seu serviço.
 
## <a name="next-steps"></a>Próximos passos

* Gere tráfego para a sua aplicação (por exemplo, lance um [teste de disponibilidade).](monitor-web-app-availability.md) Em seguida, aguarde 10 a 15 minutos para que os vestígios comecem a ser enviados para a instância De Insights de Aplicação.
* Consulte [os vestígios do Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal Azure.
* Para resolver problemas de perfis, consulte [a resolução de problemas do Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
