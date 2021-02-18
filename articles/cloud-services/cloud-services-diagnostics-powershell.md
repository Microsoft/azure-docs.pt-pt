---
title: Ativar diagnósticos em Azure Cloud Services (clássico) utilizando o PowerShell | Microsoft Docs
description: Aprenda a utilizar o PowerShell para permitir a recolha de dados de diagnóstico a partir de um Serviço de Nuvem Azure com a extensão Azure Diagnostics.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0b6096f980234c09bf1c2da2b4621b8ed66b495d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578170"
---
# <a name="enable-diagnostics-in-azure-cloud-services-classic-using-powershell"></a>Ativar diagnósticos em Azure Cloud Services (clássico) usando PowerShell

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Pode recolher dados de diagnóstico como registos de aplicações, contadores de desempenho, etc. de um Serviço de Cloud utilizando a extensão Azure Diagnostics. Este artigo descreve como ativar a extensão Azure Diagnostics para um Serviço de Cloud utilizando o PowerShell.  Ver [Como instalar e configurar a Azure PowerShell](/powershell/azure/) para os pré-requisitos necessários para este artigo.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Ativar a extensão de diagnóstico como parte da implementação de um Serviço Cloud
Esta abordagem aplica-se ao tipo de cenários de integração contínua, onde a extensão de diagnóstico pode ser ativada como parte da implementação do serviço na nuvem. Ao criar uma nova implementação do Cloud Service, pode ativar a extensão de diagnóstico passando o parâmetro *ExtensionConfiguration* para o cmdlet [New-AzureDeployment.](/powershell/module/servicemanagement/azure.service/new-azuredeployment?view=azuresmps-3.7.0&preserve-view=true&preserve-view=true) O parâmetro *ExtensionConfiguration* requer uma série de configurações de diagnóstico que podem ser criadas usando o [cmdlet New-AzureServiceDiagnosticsExtensionConfig.](/powershell/module/servicemanagement/azure.service/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0&preserve-view=true)

O exemplo a seguir mostra como pode ativar diagnósticos para um serviço de nuvem com um WebRole e WorkerRole, cada um com uma configuração de diagnóstico diferente.

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

Se o ficheiro de configuração de diagnóstico especificar um `StorageAccount` elemento com um nome de conta de armazenamento, então o `New-AzureServiceDiagnosticsExtensionConfig` cmdlet utilizará automaticamente essa conta de armazenamento. Para que isto funcione, a conta de armazenamento precisa de estar na mesma subscrição que o Serviço Cloud que está a ser implantado.

A partir de Azure SDK 2.6, os ficheiros de configuração de extensão gerados pela saída-alvo da publicação MSBuild incluirão o nome da conta de armazenamento com base na cadeia de configuração de diagnóstico especificada no ficheiro de configuração de serviço (.cscfg). O script abaixo mostra-lhe como analisar os ficheiros de configuração de extensão a partir da saída do alvo de publicação e configurar a extensão de diagnóstico para cada função ao implementar o serviço na nuvem.

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

Visual Studio Online usa uma abordagem semelhante para implementações automatizadas de Serviços Cloud com a extensão de diagnóstico. Consulte [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) para obter um exemplo completo.

Se não `StorageAccount` foi especificado na configuração de diagnóstico, então você precisa passar no parâmetro *StorageAccountName* para o cmdlet. Se o parâmetro *StorageAccountName* for especificado, então o cmdlet utilizará sempre a conta de armazenamento que está especificada no parâmetro e não a que está especificada no ficheiro de configuração de diagnóstico.

Se a conta de armazenamento de diagnósticos estiver numa subscrição diferente do Serviço cloud, então você precisa passar explicitamente nos parâmetros *StorageAccountName* e *StorageAccountKey* para o cmdlet. O parâmetro *StorageAccountKey* não é necessário quando a conta de armazenamento de diagnósticos está na mesma subscrição, uma vez que o cmdlet pode automaticamente consultar e definir o valor chave ao permitir a extensão de diagnóstico. No entanto, se a conta de armazenamento de diagnósticos estiver numa subscrição diferente, então o cmdlet pode não conseguir obter a chave automaticamente e precisa especificar explicitamente a chave através do parâmetro *StorageAccountKey.*

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Ativar a extensão de diagnóstico num Serviço Cloud existente
Pode utilizar o [cmdlet Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azureservicediagnosticsextension?view=azuresmps-3.7.0&preserve-view=true) para ativar ou atualizar a configuração de diagnósticos num Serviço de Cloud que já está em execução.

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
Utilize o [cmdlet Get-AzureServiceDiagnosticsextension](/powershell/module/servicemanagement/azure.service/get-azureservicediagnosticsextension?view=azuresmps-3.7.0&preserve-view=true) para obter a configuração atual de diagnóstico para um serviço de cloud.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Remover a extensão de diagnóstico
Para desativar os diagnósticos num serviço de nuvem, pode utilizar o [cmdlet Remove-AzureServiceDiagnosticsExtension.](/powershell/module/servicemanagement/azure.service/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0&preserve-view=true)

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se ativou a extensão de diagnóstico utilizando quer *o Set-AzureServiceDiagnosticsExtension* quer o *New-AzureServiceDiagnosticsExtensionConfig* sem o parâmetro *Role,* então pode remover a extensão utilizando *remove-AzureServiceDiagnosticsExtension* sem o parâmetro *Role.* Se o parâmetro *Role* foi utilizado ao ativar a extensão, então também deve ser utilizado ao remover a extensão.

Para remover a extensão de diagnóstico de cada função individual:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter orientações adicionais sobre a utilização de diagnósticos Azure e outras técnicas para resolver [problemas, consulte Ativar diagnósticos em Azure Cloud Services e Máquinas Virtuais](cloud-services-dotnet-diagnostics.md).
* O [Esquema de Configuração de Diagnóstico](../azure-monitor/agents/diagnostics-extension-schema-windows.md) explica as várias opções de configurações xml para a extensão de diagnóstico.
* Para aprender a ativar a extensão de diagnóstico para máquinas virtuais, consulte [Criar uma máquina virtual do Windows com monitorização e diagnósticos utilizando o Modelo do Gestor de Recursos Azure](../virtual-machines/extensions/diagnostics-template.md)