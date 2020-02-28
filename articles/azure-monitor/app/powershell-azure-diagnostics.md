---
title: Utilizar o PowerShell para configurar o Application Insights no Azure | Microsoft Docs
description: Automatizar a configuração do Azure Diagnostics para canalizar dados para insights de aplicação.
ms.topic: conceptual
ms.date: 08/06/2019
ms.openlocfilehash: da1796c8af5b9463d8223615f4b0629ba65eb3e8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669808"
---
# <a name="using-powershell-to-set-up-application-insights-for-azure-cloud-services"></a>Usando a PowerShell para configurar insights de aplicação para serviços de nuvem azure

O [Microsoft Azure](https://azure.com) pode ser [configurado para enviar Diagnósticos do Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) ao [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). O diagnóstico refere-se aos Cloud Services e às VMs do Azure. Complementa a telemetria que envia da aplicação com o Application Insights SDK. Como parte da automatização do processo de criação de novos recursos no Azure, pode configurar o diagnóstico com o PowerShell.

## <a name="azure-template"></a>Modelo do Azure
Se a aplicação Web estiver no Azure e criar os seus recursos através de um modelo do Azure Resource Manager, pode configurar o Application Insights, adicionando o seguinte ao nó de recursos:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` – um nome para o recurso do Application Insights
* `myWebAppName` - a identificação da aplicação web

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Ativar a extensão de diagnóstico como parte da implementação de um Serviço Cloud
O cmdlet `New-AzureDeployment` tem um parâmetro `ExtensionConfiguration`, que assume uma matriz de configurações de diagnóstico. Estas configurações podem ser criadas com o cmdlet `New-AzureServiceDiagnosticsExtensionConfig`. Por exemplo:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Ativar a extensão de diagnóstico num Serviço Cloud existente
Num serviço existente, utilize `Set-AzureServiceDiagnosticsExtension`.

```ps

    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Obter a configuração atual da extensão de diagnóstico
```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Remover a extensão de diagnóstico
```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se tiver ativado a extensão de diagnóstico com `Set-AzureServiceDiagnosticsExtension` ou `New-AzureServiceDiagnosticsExtensionConfig` sem o parâmetro Função, pode remover a extensão com `Remove-AzureServiceDiagnosticsExtension` sem o parâmetro Função. Se o parâmetro Função tiver sido utilizado ao ativar a extensão, também o deverá utilizar para remover a extensão.

Para remover a extensão de diagnóstico de cada função individual:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Consulte também
* [Monitor Azure Cloud Services apps with Application Insights (Monitorizar aplicações Serviços Cloud do Azure com o Application Insights)](../../azure-monitor/app/cloudservices.md)
* [Send Azure Diagnostics to Application Insights (Enviar o Diagnóstico do Azure para o Application Insights)](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Automate configuring alerts (Automatizar alertas de configuração)](powershell-alerts.md)

