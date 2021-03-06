---
title: Ative o Snapshot Debugger para aplicações .NET no Azure App Service | Microsoft Docs
description: Ativar o Snapshot Debugger para aplicações .NET no Azure App Service
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 26538f48213d025c6fe71fb55abb17a025a23b45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025684"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Ativar o Snapshot Debugger para aplicações .NET no Azure App Service

A Snapshot Debugger suporta atualmente aplicações ASP.NET e ASP.NET Core que estão a ser executadas no Azure App Service em planos de serviço do Windows.

Recomendamos que execute a sua aplicação no nível de serviço Basic, ou superior, ao utilizar o depurar instantâneo.

Para a maioria das aplicações, os níveis de serviço Gratuito e Partilhado não têm memória ou espaço de disco suficiente para guardar instantâneos.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> Ativar Snapshot Debugger
Para ativar o Snapshot Debugger para uma aplicação, siga as instruções abaixo.

Se estiver a executar um tipo diferente de serviço Azure, aqui estão as instruções para permitir o Snapshot Debugger em outras plataformas suportadas:
* [Função do Azure](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Tecido de Serviço Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais Azure e conjuntos de escala de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais ou físicas no local](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> Se estiver a utilizar uma versão de pré-visualização de .NET Core, ou as referências à sua aplicação Application Insights SDK, direta ou indiretamente através de um conjunto dependente, siga as instruções para [Enable Snapshot Debugger para outros ambientes](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) que incluam o pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet com a aplicação e, em seguida, preencha as restantes instruções abaixo. 
>
> A instalação sem código do Application Insights Snapshot Debugger segue a política de suporte .NET Core.
> Para obter mais informações sobre os tempos de funcionação suportados, consulte [a Política de Suporte do Núcleo .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

O Snapshot Debugger está pré-instalado como parte do tempo de funcionação dos Serviços de Aplicações, mas precisa de o ligar para obter fotografias para a sua aplicação de Serviço de Aplicações.

Uma vez implementada uma aplicação, siga os passos abaixo para ativar o depurado instantâneo:

1. Navegue no painel de controlo Azure para o seu Serviço de Aplicações.
2. Aceda à página **'Definições >'s Application Insights.**

   ![Ativar insights de aplicativos no portal de serviços de aplicações](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Siga as instruções na página para criar um novo recurso ou selecione um recurso de App Insights existente para monitorizar a sua aplicação. Certifique-se também de que ambos os interruptores para Snapshot Debugger estão **ligados**.

   ![Adicionar extensão do site App Insights][Enablement UI]

4. O Snapshot Debugger está agora ativado através de uma Definição de Aplicações de Serviços de Aplicação.

    ![Definição de aplicativo para Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Ativar snapshot Debugger para outras nuvens

Atualmente, as únicas regiões que requerem modificações no ponto final são [o Governo Azure](../../azure-government/compare-azure-government-global-azure.md#application-insights) e [a Azure China](/azure/china/resources-developer-guide) através da Cadeia de Conexão de Insights de Aplicação.

|Propriedade de cadeia de conexão    | Nuvem do Governo dos EUA | Nuvem da China |   
|---------------|---------------------|-------------|
|Ponto SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Para obter mais informações sobre outras ligações, consulte a [documentação do Application Insights](./sdk-connection-string.md?tabs=net#connection-string-with-explicit-endpoint-overrides).

## <a name="disable-snapshot-debugger"></a>Depurar Snapshot Debugger

Siga os mesmos passos que para **Enable Snapshot Debugger**, mas troque ambos os interruptores para Snapshot Debugger para **Off**.

Recomendamos que tenha snapshot Debugger habilitado em todas as suas aplicações para facilitar o diagnóstico de exceções à aplicação.

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Para um Serviço de Aplicações Azure, pode definir as definições de aplicações dentro do modelo do Gestor de Recursos Azure para ativar o Snapshot Debugger e Profiler, consulte o corte do modelo abaixo:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Passos seguintes

- Gere tráfego para a sua aplicação que pode desencadear uma exceção. Em seguida, aguarde 10 a 15 minutos para que as imagens sejam enviadas para a instância De Insights de Aplicação.
- Veja [as fotos](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) no portal Azure.
- Para obter ajuda para resolver problemas com o Snapshot Debugger, consulte [snapshot Debugger resolução de problemas](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png