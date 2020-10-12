---
title: Ativar o Snapshot Debugger para aplicações .NET no Azure App Service [ Microsoft Docs
description: Ativar o Snapshot Debugger para aplicações .NET no Azure App Service
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6928da704236c4bb5492f99a4a5327bf297a323d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84676852"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Ativar o Snapshot Debugger para aplicações .NET no Azure App Service

A Snapshot Debugger trabalha atualmente para aplicações ASP.NET e ASP.NET Core que estão a ser executadas no Azure App Service em planos de serviço do Windows.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> Ativar Snapshot Debugger
Para ativar o Snapshot Debugger para uma aplicação, siga as instruções abaixo. Se estiver a executar um tipo diferente de serviço Azure, aqui estão as instruções para permitir o Snapshot Debugger em outras plataformas suportadas:
* [Cloud Services do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Tecido de Serviço Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais Azure e conjuntos de escala de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais ou físicas no local](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Se estiver a utilizar uma versão de pré-visualização de .NET Core, siga as instruções para [Ativar o Debugger Snapshot para outros ambientes](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) primeiro para incluir o pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet com a aplicação e, em seguida, preencha as restantes instruções abaixo. 

O Application Insights Snapshot Debugger está pré-instalado como parte do tempo de funcionamento dos Serviços de Aplicação, mas precisa de o ligar para obter fotografias para a sua aplicação de Serviço de Aplicações. Depois de ter implementado uma aplicação, mesmo que tenha incluído o SDK Application Insights no código fonte, siga os passos abaixo para ativar o depurar instantâneo.

1. Navegue no painel de controlo Azure para o seu Serviço de Aplicações.
2. Aceda à página **'Definições >'s Application Insights.**

   ![Ativar insights de aplicativos no portal de serviços de aplicações](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Siga as instruções na página para criar um novo recurso ou selecione um recurso de App Insights existente para monitorizar a sua aplicação. Certifique-se também de que ambos os interruptores para Snapshot Debugger estão **ligados**.

   ![Adicionar extensão do site App Insights][Enablement UI]

4. O Snapshot Debugger está agora ativado através de uma Definição de Aplicações de Serviços de Aplicação.

    ![Definição de aplicativo para Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Depurar Snapshot Debugger

Siga os mesmos passos que para **Enable Snapshot Debugger**, mas troque ambos os interruptores para Snapshot Debugger para **Off**.
Recomendamos que tenha o Snapshot Debugger habilitado em todas as suas aplicações para facilitar o diagnóstico das exceções à aplicação.

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Para um Serviço de Aplicações Azure, pode definir as definições de aplicações num modelo de Gestor de Recursos Azure para ativar o Snapshot Debugger e o Profiler. Você adiciona um recurso config que contém as definições da aplicação como um recurso infantil do site:

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

