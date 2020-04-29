---
title: Profile live Azure Service Fabric apps com Insights de Aplicação
description: Ativar o Profiler para uma aplicação de Tecido de Serviço
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 664d6eb377185613a1a5670daf6747b482c79d9d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671618"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Aplicações de tecido de serviço Azure ao vivo de perfil com Insights de Aplicação

Também pode implementar o Perfil de Insights de Aplicação nestes serviços:
* [Serviço de Aplicações do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Configurar a definição de implantação do ambiente

O Perfil de Insights de Aplicação está incluído na Azure Diagnostics. Pode instalar a extensão de Diagnóstico Sinuoso Azure utilizando um modelo de Gestor de Recursos Azure para o seu cluster de tecido de serviço. Obtenha um [modelo que instala diagnósticos Azure num cluster](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)de tecido de serviço .

Para configurar o seu ambiente, tome as seguintes ações:

1. O profiler suporta .NET Framework e .Net Core. Se estiver a utilizar a .NET Framework, certifique-se de que está a utilizar [a .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou posterior. É suficiente para confirmar que o `Windows Server 2012 R2` Sistema operativo implantado é ou mais tarde. O Profiler suporta .NET Core 2.1 e aplicações mais recentes.

1. Procure a extensão de [Diagnóstico Sinuoso Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) no ficheiro do modelo de implementação.

1. Adicione a `SinksConfig` seguinte secção como `WadCfg`elemento infantil de . Substitua `ApplicationInsightsProfiler` o valor patrimonial pela sua própria chave de instrumentação Application Insights:  

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

      Para obter informações sobre a adição da extensão de Diagnóstico ao seu modelo de implementação, consulte [monitorização e diagnóstico de utilização com modelos de VM windows e Gestor de Recursos Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. Implemente o seu cluster de Tecido de Serviço utilizando o seu modelo de Gestor de Recursos Azure.  
  Se as definições estiverem corretas, o Perfil de Insights da Aplicação será instalado e ativado quando a extensão de Diagnóstico Azure estiver instalada. 

1. Adicione informações de aplicação à sua aplicação Service Fabric.  
  Para que o Profiler recolha perfis para os seus pedidos, a sua aplicação deve estar a acompanhar as operações com insights de aplicação. Para APIs apátridas, pode consultar instruções para [rastrear Pedidos de perfis](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Para obter mais informações sobre o rastreio de operações personalizadas noutros tipos de aplicações, consulte [as operações personalizadas de rastreio com o Application Insights .NET SDK](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Recoloque a sua aplicação.


## <a name="next-steps"></a>Passos seguintes

* Gere tráfego na sua aplicação (por exemplo, lance um [teste de disponibilidade).](monitor-web-app-availability.md) Em seguida, aguarde 10 a 15 minutos para que os vestígios comecem a ser enviados para a instância Deinsights de Aplicação.
* Consulte [os vestígios](profiler-overview.md?toc=/azure/azure-monitor/toc.json) do Profiler no portal Azure.
* Para obter ajuda com problemas de resolução de problemas, consulte a resolução de [problemas do Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
