---
title: Ativar diagnósticos em Serviços Azure Cloud utilizando powerShell / Microsoft Docs
description: Saiba como ativar diagnósticos para serviços na nuvem usando powerShell
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: tagore
ms.openlocfilehash: 76cdffed813fd182980b36f848e0ae42f3226539
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75386549"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Ativar diagnósticos em Serviços Azure Cloud usando powerShell
Pode recolher dados de diagnóstico como registos de aplicações, contadores de desempenho, etc. de um Serviço cloud utilizando a extensão de Diagnóstico Azure. Este artigo descreve como ativar a extensão de Diagnóstico Sinuoso Azure para um Serviço de Cloud utilizando powerShell.  Veja como instalar e configurar o [Azure PowerShell](/powershell/azure/overview) para os pré-requisitos necessários para este artigo.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Ativar a extensão de diagnóstico como parte da implementação de um Serviço Cloud
Esta abordagem é aplicável ao tipo de cenários de integração contínua, onde a extensão de diagnóstico pode ser ativada como parte da implementação do serviço na nuvem. Ao criar uma nova implementação do Cloud Service, pode ativar a extensão de diagnóstico passando o parâmetro *de Configuração* de Extensão para o cmdlet [New-AzureDeployment.](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) O parâmetro *De Configuração* de Extensão requer uma série de configurações de diagnóstico que podem ser criadas utilizando o cmdlet [New-AzureServiceDiagnosticsExtensionDiagnosticsExtensionConfig.](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0)

O exemplo que se segue mostra como pode ativar diagnósticos para um serviço na nuvem com um WebRole e Um Trabalho, cada um com uma configuração de diagnóstico diferente.

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

Se o ficheiro de configuração de diagnóstico especificar `StorageAccount` um `New-AzureServiceDiagnosticsExtensionConfig` elemento com um nome de conta de armazenamento, então o cmdlet utilizará automaticamente essa conta de armazenamento. Para que isto funcione, a conta de armazenamento tem de estar na mesma subscrição que o Serviço cloud que está a ser implementado.

A partir do Azure SDK 2.6, os ficheiros de configuração de extensão gerados pela saída do alvo da publicação MSBuild incluirão o nome da conta de armazenamento com base na cadeia de configuração de diagnóstico especificada no ficheiro de configuração do serviço (.cscfg). O script abaixo mostra-lhe como analisar os ficheiros de configuração de extensão a partir da saída do alvo e configurar a extensão de diagnóstico para cada função ao implementar o serviço de nuvem.

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

O Visual Studio Online utiliza uma abordagem semelhante para implementações automatizadas de Serviços cloud com a extensão de diagnóstico. Consulte [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) para obter um exemplo completo.

Se `StorageAccount` não foi especificado na configuração de diagnóstico, então terá de passar o parâmetro *StorageAccountName* para o cmdlet. Se o parâmetro *StorageAccountName* for especificado, então o cmdlet utilizará sempre a conta de armazenamento especificada no parâmetro e não a especificada no ficheiro de configuração de diagnóstico.

Se a conta de armazenamento de diagnóstico sais de uma subscrição diferente do Serviço Cloud, então precisa de passar explicitamente os parâmetros *StorageAccountName* e *StorageAccountKey* para o cmdlet. O parâmetro *StorageAccountKey* não é necessário quando a conta de armazenamento de diagnósticos está na mesma subscrição, uma vez que o cmdlet pode automaticamente consultar e definir o valor-chave ao permitir a extensão de diagnóstico. No entanto, se a conta de armazenamento de diagnósticos estiver numa subscrição diferente, então o cmdlet pode não conseguir obter a chave automaticamente e precisa especificar explicitamente a chave através do parâmetro *StorageAccountKey.*

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Ativar a extensão de diagnóstico num Serviço Cloud existente
Pode utilizar o [cmdlet set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) para ativar ou atualizar a configuração de diagnóstico num Serviço cloud que já está em execução.

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
Utilize o [cmdlet de extensão de ampliação get-AzureServiceDiagnostics Para](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) obter a configuração de diagnóstico atual para um serviço na nuvem.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Remover a extensão de diagnóstico
Para desativar os diagnósticos num serviço na nuvem, pode utilizar o cmdlet [remove-AzureServiceDiagnosticsExtension.](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0)

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se ativou a extensão de diagnóstico utilizando a Extensão de *Diagnósticos Set-AzureService diagnostics* ou o *new-AzureServiceDiagnosticsExtensionConfig* sem o parâmetro *de função,* então pode remover a extensão utilizando a *extensão remove-AzureServiceDiagnosticsExtension* sem o parâmetro *de função.* Se o parâmetro *role* foi utilizado ao ativar a extensão, então também deve ser utilizado ao remover a extensão.

Para remover a extensão de diagnóstico de cada função individual:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter orientações adicionais sobre a utilização de diagnósticos Azure e outras técnicas para resolver problemas, consulte [A Habilitação de Diagnósticos em Serviços de Nuvem Azure e Máquinas Virtuais](cloud-services-dotnet-diagnostics.md).
* O Esquema de Configuração de [Diagnósticos](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot3) explica as várias opções de configurações xml para a extensão de diagnóstico.
* Para aprender a ativar a extensão de diagnóstico para máquinas virtuais, consulte [Criar uma máquina Virtual windows com monitorização e diagnóstico utilizando](../virtual-machines/windows/extensions-diagnostics-template.md) o modelo de gestor de recursos Azure



