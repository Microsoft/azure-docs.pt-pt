---
title: Perfil de aplicativos de Service Fabric dinâmicos do Azure com Application Insights | Microsoft Docs
description: Habilitar o Profiler para um aplicativo Service Fabric
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a7a64be3c73ea82c6bf3d905772f3278f9bda5df
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818475"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Perfil de aplicativos de Service Fabric dinâmicos do Azure com Application Insights

Você também pode implantar Application Insights Profiler nesses serviços:
* [App Service do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Configurar a definição de implantação de ambiente

Application Insights Profiler está incluído no Diagnóstico do Azure. Você pode instalar a extensão de Diagnóstico do Azure usando um modelo de Azure Resource Manager para o cluster de Service Fabric. Obtenha um [modelo que instala diagnóstico do Azure em um Cluster Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Para configurar seu ambiente, execute as seguintes ações:

1. O criador de perfil dá suporte a .NET Framework e .Net Core. Se você estiver usando .NET Framework, verifique se está usando [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou posterior. É suficiente confirmar que o sistema operacional implantado é `Windows Server 2012 R2` ou posterior. O criador de perfil dá suporte ao .NET Core 2,1 e a aplicativos mais recentes.

1. Procure a extensão [diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) no arquivo de modelo de implantação.

1. Adicione a seguinte seção `SinksConfig` como um elemento filho de `WadCfg`. Substitua o valor da propriedade `ApplicationInsightsProfiler` pela sua própria chave de instrumentação Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Para obter informações sobre como adicionar a extensão de diagnóstico ao seu modelo de implantação, consulte [usar monitoramento e diagnóstico com uma VM do Windows e modelos de Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. Implante o Cluster Service Fabric usando o modelo de Azure Resource Manager.  
  Se suas configurações estiverem corretas, Application Insights Profiler será instalado e habilitado quando a extensão de Diagnóstico do Azure for instalada. 

1. Adicione Application Insights ao seu aplicativo Service Fabric.  
  Para que o profiler colete perfis para suas solicitações, seu aplicativo deve estar controlando operações com Application Insights. Para APIs sem monitoração de estado, você pode consultar as instruções para [controlar as solicitações de criação de perfil](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Para obter mais informações sobre como controlar operações personalizadas em outros tipos de aplicativos, consulte [rastrear operações personalizadas com o SDK do .net Application insights](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Reimplante seu aplicativo.


## <a name="next-steps"></a>Passos seguintes

* Gere o tráfego para seu aplicativo (por exemplo, inicie um [teste de disponibilidade](monitor-web-app-availability.md)). Em seguida, aguarde de 10 a 15 minutos para que os rastreamentos comecem a ser enviados para a instância de Application Insights.
* Consulte [rastreamentos do profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal do Azure.
* Para obter ajuda com solução de problemas de criador de perfil, consulte [solução de problemas do profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
