---
title: Perfil ao vivo Aplicativos de Tecido de Serviço Azure com Insights de Aplicação
description: Ativar o Profiler para uma aplicação de Tecido de Serviço
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 67e7765a1f46c2be5790c11687e06ea624702b9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589569"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Aplicações de tecido de serviço Azure ao vivo com Insights de Aplicação

Também pode implementar o Profiler Application Insights nestes serviços:
* [Serviço de Aplicações do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Configurar a definição de implantação do ambiente

O Profiler De Insights de Aplicação está incluído no Azure Diagnostics. Pode instalar a extensão Azure Diagnostics utilizando um modelo de Gestor de Recursos Azure para o seu cluster de Tecido de Serviço. Obtenha um [modelo que instale Azure Diagnostics em um cluster de tecido de serviço.](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Para configurar o seu ambiente, tome as seguintes ações:

1. Profiler suporta .NET Framework e .Net Core. Se estiver a utilizar o Quadro .NET, certifique-se de que utiliza [o Quadro .NET 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou mais tarde. É suficiente para confirmar que o sistema operativo é `Windows Server 2012 R2` ou mais tarde. O profiler suporta .NET Core 2.1 e aplicações mais recentes.

1. Procure a extensão [Azure Diagnostics](../agents/diagnostics-extension-overview.md) no ficheiro do modelo de implementação.

1. Adicione a `SinksConfig` seguinte secção como elemento infantil de `WadCfg` . Substitua o `ApplicationInsightsProfiler` valor da propriedade pela sua própria chave de instrumentação Application Insights:  

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

      Para obter informações sobre a adição da extensão de Diagnóstico ao seu modelo de implementação, consulte [monitorizar e diagnosticar a utilização com os modelos windows VM e Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json).

1. Implemente o seu cluster de Tecido de Serviço utilizando o seu modelo de Gestor de Recursos Azure.  
  Se as definições estiverem corretas, o Profiler Application Insights será instalado e ativado quando a extensão Azure Diagnostics estiver instalada. 

1. Adicione Insights de Aplicação à sua aplicação De Tecido de Serviço.  
  Para o Profiler recolher perfis para os seus pedidos, a sua aplicação deve estar a acompanhar as operações com o Application Insights. Para APIs apátridas apátridas, pode consultar instruções para [rastrear pedidos de perfis](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Para obter mais informações sobre o rastreio de operações personalizadas noutros tipos de apps, consulte [as operações personalizadas com o Application Insights .NET SDK](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Reimplantar a sua aplicação.


## <a name="next-steps"></a>Passos seguintes

* Gere tráfego para a sua aplicação (por exemplo, lance um [teste de disponibilidade).](monitor-web-app-availability.md) Em seguida, aguarde 10 a 15 minutos para que os vestígios comecem a ser enviados para a instância De Insights de Aplicação.
* Consulte [os vestígios do Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal Azure.
* Para obter ajuda para resolver problemas de perfis, consulte [a resolução de problemas do Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
