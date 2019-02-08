---
title: Perfil de aplicativos em direto do Azure Service Fabric com o Application Insights | Documentos da Microsoft
description: Ative Profiler para uma aplicação do Service Fabric
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
ms.openlocfilehash: 73e453385a6916b6999691afbacd1f102ce7270d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865748"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Perfil de aplicativos em direto do Azure Service Fabric com o Application Insights

Também pode implementar o Application Insights Profiler estes serviços:
* [Serviço de Aplicações do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Configurar a definição de implementação do ambiente

Application Insights Profiler está incluído no diagnóstico do Azure. Pode instalar a extensão de diagnóstico do Azure, utilizando um modelo Azure Resource Manager para o seu cluster do Service Fabric. Obter um [modelo de que instala o diagnóstico do Azure num Cluster do Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Para configurar o ambiente, siga as seguintes ações:

1. Para se certificar de que está a utilizar [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou posterior, é suficiente confirmar que o sistema operativo implementado é `Windows Server 2012 R2` ou posterior.

1. Procure o [diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) extensão no ficheiro de modelo de implementação.

1. Adicione as seguintes `SinksConfig` secção como um elemento subordinado do `WadCfg`. Substitua o `ApplicationInsightsProfiler` valor da propriedade com a sua própria chave de instrumentação do Application Insights:  

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

      Para obter informações sobre como adicionar a extensão de diagnóstico ao seu modelo de implementação, consulte [monitorização de utilização e diagnóstico com modelos de VM do Windows e do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. Implemente cluster do Service Fabric, utilizando o modelo do Azure Resource Manager.  
  Se as definições estão corretas, Application Insights Profiler irá instalar e ativar quando a extensão de diagnóstico do Azure está instalada. 

1. Adicione o Application Insights à sua aplicação do Service Fabric.  
  Para o Profiler recolher perfis para seus pedidos, seu aplicativo deve ser enviando dados de solicitação para o Application Insights. Para obter mais informações, vá para o [Application Insights SDK para projetos do Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric) página.

1. Reimplemente a sua aplicação.

> [SUGESTÃO] Para máquinas virtuais, uma alternativa para os passos anteriores baseados em JSON é para navegar no portal do Azure para **máquinas virtuais** > **definições de diagnóstico** > **Coletores** > **conjunto enviar dados de diagnóstico para o Application Insights para ativado** e, em seguida, selecione uma conta do Application Insights ou uma ikey específico.

## <a name="next-steps"></a>Passos Seguintes

* Gerar tráfego para a aplicação (por exemplo, iniciar uma [teste de disponibilidade](monitor-web-app-availability.md)). Em seguida, aguarde 10 a 15 minutos para que os rastreios começar a ser enviados para a instância do Application Insights.
* Ver [rastreios do Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal do Azure.
* Para obter ajuda com a resolução de problemas do Profiler, consulte [Profiler de resolução de problemas](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
