---
title: Ativar diagnósticos nos serviços de Cloud do Azure com o PowerShell | Documentos da Microsoft
description: Saiba como ativar os diagnósticos para serviços em nuvem com o PowerShell
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 66e08754-8639-4022-ae18-4237749ba17d
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: jeconnoc
ms.openlocfilehash: 13a855c5770281e2578523bfc1813b2e03df6651
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65539236"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Ativar diagnósticos nos serviços de Cloud do Azure com o PowerShell
Pode recolher dados de diagnóstico, como registos de aplicações, contadores de desempenho etc. do serviço Cloud com a extensão de diagnóstico do Azure. Este artigo descreve como ativar a extensão de diagnóstico do Azure para um serviço em nuvem com o PowerShell.  Ver [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para os pré-requisitos necessários para este artigo.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Ativar a extensão de diagnóstico como parte da implementação de um Serviço Cloud
Essa abordagem é aplicável ao tipo de integração contínua de cenários, em que a extensão de diagnóstico pode ser ativada como parte da implementação de serviço em nuvem. Ao criar uma nova implementação de serviço em nuvem, pode ativar a extensão de diagnóstico ao transmitir os *ExtensionConfiguration* parâmetro para o [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) cmdlet. O *ExtensionConfiguration* parâmetro assume uma matriz de configurações de diagnóstico que podem ser criadas utilizando o [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) cmdlet.

O exemplo seguinte mostra como ativar o diagnóstico para um serviço cloud com um WebRole e WorkerRole, contendo cada uma configuração de diagnósticos diferentes.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Se o ficheiro de configuração de diagnósticos Especifica um `StorageAccount` elemento com um nome de conta de armazenamento, em seguida, o `New-AzureServiceDiagnosticsExtensionConfig` cmdlet automaticamente irá utilizar essa conta de armazenamento. Para isso funcionar, a conta de armazenamento tem de estar na mesma subscrição que o serviço em nuvem que está sendo implantado.

Azure SDK versão 2.6 ou superior a publicar os ficheiros de configuração de extensão gerados do MSBuild, saída de destino incluirá o nome da conta de armazenamento com base na cadeia de configuração de diagnósticos especificada no ficheiro de configuração do serviço (. cscfg). O script a seguir mostra-lhe como analisar os ficheiros de configuração de extensão da saída do destino de publicação e configurar a extensão de diagnóstico para cada função, ao implementar o serviço em nuvem.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online usa uma abordagem semelhante para Implantações automatizadas dos serviços Cloud com a extensão de diagnóstico. Ver [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) para obter um exemplo completo.

Se nenhum `StorageAccount` foi especificado na configuração de diagnósticos, tem de passar o *StorageAccountName* parâmetro para o cmdlet. Se o *StorageAccountName* parâmetro for especificado, em seguida, o cmdlet irá sempre utilizar a conta de armazenamento especificada no parâmetro e não o que é especificado no ficheiro de configuração de diagnósticos.

Se a conta de armazenamento de diagnóstico está numa subscrição diferente do serviço em nuvem, em seguida, precisa transmitir explicitamente o *StorageAccountName* e *StorageAccountKey* parâmetros para o cmdlet. O *StorageAccountKey* parâmetro não é necessária quando a conta de armazenamento de diagnóstico está na mesma subscrição, como o cmdlet automaticamente. pode consultar e definir o valor da chave ao ativar a extensão de diagnóstico. No entanto, se a conta de armazenamento de diagnóstico está numa subscrição diferente, em seguida, o cmdlet poderá não conseguir obter a chave automaticamente e precisa explicitamente especificar a chave através da *StorageAccountKey* parâmetro.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Ativar a extensão de diagnóstico num Serviço Cloud existente
Pode utilizar o [Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet para ativar ou Atualize a configuração de diagnósticos num serviço Cloud que já está em execução.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Obter a configuração atual da extensão de diagnóstico
Utilize o [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet para obter a configuração de diagnóstico atual para um serviço cloud.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Remover a extensão de diagnóstico
Para desativar o diagnóstico num serviço cloud, pode utilizar o [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se tiver ativado a extensão de diagnóstico com *Set-AzureServiceDiagnosticsExtension* ou o *New-AzureServiceDiagnosticsExtensionConfig* sem o *função*parâmetro, em seguida, pode remover a extensão com *Remove-AzureServiceDiagnosticsExtension* sem o *função* parâmetro. Se o *função* parâmetro foi utilizado ao ativar a extensão, em seguida, ele também deve ser usado ao remover a extensão.

Para remover a extensão de diagnóstico de cada função individual:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Próximos Passos
* Para obter orientações adicionais sobre como utilizar o diagnóstico do Azure e outras técnicas para solucionar problemas, consulte [ativar diagnósticos nos serviços Cloud do Azure e máquinas virtuais](cloud-services-dotnet-diagnostics.md).
* O [esquema de configuração de diagnósticos](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot2) explica as várias opções de configurações de xml para a extensão de diagnóstico.
* Para saber como ativar a extensão de diagnóstico para máquinas virtuais, veja [criar uma Máquina Virtual do Windows com monitorização e diagnóstico com o modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md)
