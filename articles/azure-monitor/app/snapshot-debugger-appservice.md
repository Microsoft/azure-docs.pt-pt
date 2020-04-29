---
title: Ativar o Snapshot Debugger para aplicações .NET no Serviço de Aplicações Azure [ Microsoft Docs
description: Ativar snapshot Debugger para .NET apps no Serviço de Aplicações Azure
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8af688e38003e0613a06d7d8622ce279a3838589
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80298278"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Ativar snapshot Debugger para .NET apps no Serviço de Aplicações Azure

O Snapshot Debugger trabalha atualmente para ASP.NET e ASP.NET aplicações Core que estão a funcionar no Azure App Service nos planos de serviço do Windows.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a>Ativar o Debugger Snapshot
Para ativar o Snapshot Debugger para uma aplicação, siga as instruções abaixo. Se estiver a executar um tipo diferente de serviço Azure, aqui estão as instruções para ativar o Snapshot Debugger noutras plataformas suportadas:
* [Cloud Services do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviço Azure Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais azure e conjuntos de escala de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [No local máquinas virtuais ou físicas](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Se estiver a utilizar uma versão de pré-visualização de .NET Core, siga as instruções para [enable Snapshot Debugger para outros ambientes](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) primeiro para incluir o pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet com a aplicação e, em seguida, complete o resto das instruções abaixo. 

A aplicação Insights Snapshot Debugger está pré-instalada como parte do tempo de funcionamento dos Serviços de Aplicações, mas precisa de o ligar para obter fotografias para a sua aplicação App Service. Depois de ter implementado uma aplicação, mesmo que tenha incluído o SDK de Insights de Aplicação no código fonte, siga os passos abaixo para ativar o desbugger instantâneo.

1. Vá ao painel de Serviços de **Aplicações** no portal Azure.
2. Navegue para Definições > painel **insights de aplicação.**

   ![Ativar insights de aplicações no portal de serviços de aplicações](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Siga as instruções no painel para criar um novo recurso ou selecione um recurso app Insights existente para monitorizar a sua aplicação. Certifique-se também de que os dois interruptores para Snapshot Debugger estão **ligados**.

   ![Adicionar extensão do site App Insights][Enablement UI]

4. O Snapshot Debugger está agora ativado através de uma definição de app services.

    ![Definição de aplicativo saqueado para debugger instantâneo][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Desativar o Debugger Snapshot

Siga os mesmos passos que para **Enable Snapshot Debugger**, mas mude ambos os interruptores para Snapshot Debugger to **Off**.
Recomendamos que tenha o Snapshot Debugger habilitado em todas as suas aplicações para facilitar o diagnóstico das exceções à aplicação.

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Para um Serviço de Aplicações Azure, pode definir as definições de aplicativos num modelo de Gestor de Recursos Azure para ativar o Snapshot Debugger e o Profiler. Você adiciona um recurso config que contém as definições da aplicação como um recurso infantil do site:

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

- Gere tráfego para a sua aplicação que pode desencadear uma exceção. Em seguida, aguarde 10 a 15 minutos para que as fotos sejam enviadas para a instância Deinsights de Aplicação.
- Veja [as fotos](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) no portal Azure.
- Para ajuda na resolução de problemas de problemas, consulte [a resolução de problemas do Snapshot Debugger.](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

