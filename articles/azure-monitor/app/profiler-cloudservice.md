---
title: Perfil live serviços Cloud do Azure com o Application Insights | Documentos da Microsoft
description: Ative o Application Insights Profiler para serviços Cloud do Azure.
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
ms.openlocfilehash: 2e13f1f09fcdfb68a99e705511e3659f1632132e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895486"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Perfil em direto serviços Cloud do Azure com o Application Insights

Também pode implementar o Application Insights Profiler estes serviços:
* [Serviço de Aplicações do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplicações do Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler é instalado com a extensão de diagnóstico do Azure. Terá de configurar os diagnósticos do Azure para instalar o Profiler e enviar perfis para o recurso do Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Ative Profiler para serviços Cloud do Azure
1. Certifique-se de que está a utilizar [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou posterior. É suficiente confirmar que o *ServiceConfiguration.\*.cscfg* arquivos têm uma `osFamily` valor "5" ou posterior.

1. Adicione [serviços Cloud do Application Insights SDK para o Azure](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

   >**Há um bug no Criador de perfil que é fornecido na versão mais recente do WAD para serviços Cloud.** Para usar o criador de perfil com um serviço em nuvem, ela oferece suporte apenas SDK de ia para a versão 2.7.2. Se estiver a utilizar uma versão mais recente do SDK do IA, terá de voltar atrás e 2.7.2 para usar o criador de perfil. Se utilizar o Visual Studio para mudar a versão do SDK de informações da aplicação, poderá receber um erro de redirecionamento de ligação em tempo de execução. Isto acontece porque o "newVersion" no ficheiro Web. config do applicationinsights deve ser definido para "2.7.2.0" depois de fazer downgrade o SDK de IA, mas não é atualizado automaticamente.

1. Acompanhar pedidos com o Application Insights:

    * Para funções da web ASP.NET, Application Insights pode controlar os pedidos automaticamente.

    * Para funções de trabalho, [adicionar código para monitorizar os pedidos](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Configure a extensão de diagnóstico do Azure para ativar o Profiler, fazendo o seguinte:

    a. Localize a [diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* de ficheiros para a sua função de aplicação, como mostrado aqui:  

      ![Localização do ficheiro de configuração de diagnósticos](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Se não encontrar o ficheiro, consulte [configurar diagnósticos para serviços Cloud do Azure e máquinas virtuais](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Adicione as seguintes `SinksConfig` secção como um elemento subordinado do `WadCfg`:  

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
    > Se o *diagnostics.wadcfgx* ficheiro também contém outro sink do tipo Application Insights, todos os três das seguintes chaves de instrumentação tem de corresponder ao:  
    > * A chave que é utilizada pela sua aplicação. 
    > * A chave utilizada pelo sink do Application Insights. 
    > * A chave utilizada pelo coletor ApplicationInsightsProfiler. 
    >
    > Pode encontrar o valor de chave de instrumentação real é utilizado pelos `ApplicationInsights` serem digeridos a *ServiceConfiguration.\*.cscfg* ficheiros. 
    > Após o lançamento do SDK do Azure do Visual Studio 15,5, apenas as chaves de instrumentação que são utilizadas pela aplicação e o sink de ApplicationInsightsProfiler têm de corresponder entre si.

1. Implementar o seu serviço com a nova configuração de diagnósticos e Application Insights Profiler está configurado para ser executado no seu serviço.
 
## <a name="next-steps"></a>Passos Seguintes

* Gerar tráfego para a aplicação (por exemplo, iniciar uma [teste de disponibilidade](monitor-web-app-availability.md)). Em seguida, aguarde 10 a 15 minutos para que os rastreios começar a ser enviados para a instância do Application Insights.
* Ver [rastreios do Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal do Azure.
* Para resolver problemas do Profiler, consulte [Profiler de resolução de problemas](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
