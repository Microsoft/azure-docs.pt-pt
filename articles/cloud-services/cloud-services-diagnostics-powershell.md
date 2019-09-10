---
title: Habilitar o diagnóstico nos serviços de nuvem do Azure usando o PowerShell | Microsoft Docs
description: Saiba como habilitar o diagnóstico para serviços de nuvem usando o PowerShell
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: gwallace
ms.openlocfilehash: f2b7e51971cc2e540ee7745b3b44571c58359613
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860212"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Habilitar o diagnóstico nos serviços de nuvem do Azure usando o PowerShell
Você pode coletar dados de diagnóstico como logs de aplicativo, contadores de desempenho, etc. de um serviço de nuvem usando a extensão Diagnóstico do Azure. Este artigo descreve como habilitar a extensão de Diagnóstico do Azure para um serviço de nuvem usando o PowerShell.  Consulte [como instalar e configurar Azure PowerShell](/powershell/azure/overview) para os pré-requisitos necessários para este artigo.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Ativar a extensão de diagnóstico como parte da implementação de um Serviço Cloud
Essa abordagem é aplicável ao tipo de cenários de integração contínua, em que a extensão de diagnóstico pode ser habilitada como parte da implantação do serviço de nuvem. Ao criar uma nova implantação de serviço de nuvem, você pode habilitar a extensão de diagnóstico passando o parâmetro *ExtensionConfiguration* para o cmdlet [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) . O parâmetro *ExtensionConfiguration* usa uma matriz de configurações de diagnóstico que podem ser criadas usando o cmdlet [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) .

O exemplo a seguir mostra como você pode habilitar o diagnóstico para um serviço de nuvem com uma função WebRole e WorkerRole, cada um com uma configuração de diagnóstico diferente.

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

Se o arquivo de configuração de diagnóstico `StorageAccount` especificar um elemento com um nome de conta de `New-AzureServiceDiagnosticsExtensionConfig` armazenamento, o cmdlet usará automaticamente essa conta de armazenamento. Para que isso funcione, a conta de armazenamento precisa estar na mesma assinatura que o serviço de nuvem que está sendo implantado.

Do SDK do Azure 2,6 em diante, os arquivos de configuração de extensão gerados pela saída de destino de publicação do MSBuild incluirão o nome da conta de armazenamento com base na cadeia de configuração de diagnóstico especificada no arquivo de configuração de serviço (. cscfg). O script a seguir mostra como analisar os arquivos de configuração de extensão da saída de destino de publicação e configurar a extensão de diagnóstico para cada função ao implantar o serviço de nuvem.

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

O Visual Studio online usa uma abordagem semelhante para implantações automatizadas de serviços de nuvem com a extensão de diagnóstico. Consulte [Publish-AzureCloudDeployment. ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) para obter um exemplo completo.

Se nenhum `StorageAccount` tiver sido especificado na configuração de diagnóstico, você precisará passar o parâmetro *StorageAccountName* para o cmdlet. Se o parâmetro *StorageAccountName* for especificado, o cmdlet sempre usará a conta de armazenamento especificada no parâmetro e não aquela especificada no arquivo de configuração de diagnóstico.

Se a conta de armazenamento de diagnóstico estiver em uma assinatura diferente do serviço de nuvem, você precisará passar explicitamente os parâmetros *StorageAccountName* e *StorageAccountKey* para o cmdlet. O parâmetro *StorageAccountKey* não é necessário quando a conta de armazenamento de diagnóstico está na mesma assinatura, uma vez que o cmdlet pode consultar e definir automaticamente o valor da chave ao habilitar a extensão de diagnóstico. No entanto, se a conta de armazenamento de diagnóstico estiver em uma assinatura diferente, o cmdlet poderá não conseguir obter a chave automaticamente e você precisará especificar explicitamente a chave por meio do parâmetro *StorageAccountKey* .

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Ativar a extensão de diagnóstico num Serviço Cloud existente
Você pode usar o cmdlet [set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) para habilitar ou atualizar a configuração de diagnóstico em um serviço de nuvem que já está em execução.

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
Use o cmdlet [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) para obter a configuração de diagnóstico atual para um serviço de nuvem.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Remover a extensão de diagnóstico
Para desativar o diagnóstico em um serviço de nuvem, você pode usar o cmdlet [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) .

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se você habilitou a extensão de diagnóstico usando *set-AzureServiceDiagnosticsExtension* ou *New-AzureServiceDiagnosticsExtensionConfig* sem o parâmetro *role* , poderá remover a extensão usando  *Remove-AzureServiceDiagnosticsExtension* sem o parâmetro *role* . Se o parâmetro de *função* foi usado ao habilitar a extensão, ele também deve ser usado ao remover a extensão.

Para remover a extensão de diagnóstico de cada função individual:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Próximos Passos
* Para obter orientações adicionais sobre como usar o diagnóstico do Azure e outras técnicas para solucionar problemas, consulte [habilitando o diagnóstico nos serviços de nuvem do Azure e máquinas virtuais](cloud-services-dotnet-diagnostics.md).
* O [esquema de configuração de diagnóstico](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot3) explica as várias opções de configurações de XML para a extensão de diagnóstico.
* Para saber como habilitar a extensão de diagnóstico para máquinas virtuais, consulte [criar uma máquina virtual do Windows com monitoramento e diagnóstico usando Azure Resource Manager modelo](../virtual-machines/windows/extensions-diagnostics-template.md)
