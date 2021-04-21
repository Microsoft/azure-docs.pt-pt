---
title: Ativar a extensão VM utilizando a Azure PowerShell
description: Este artigo descreve como implementar extensões de máquinas virtuais para O Arco Azure habilitados a funcionar em ambientes de nuvem híbrida usando Azure PowerShell.
ms.date: 04/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d5723655b61040c7ddf99e5f11488fff379d96a0
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832883"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Ativar extensões Azure VM usando Azure PowerShell

Este artigo mostra-lhe como implantar e desinstalar extensões Azure VM, suportadas por servidores ativados pelo Azure Arc, para uma máquina híbrida Linux ou Windows utilizando a Azure PowerShell.

> [!NOTE]
> Os servidores ativados pelo Azure Arc não suportam a implementação e gestão de extensões VM para máquinas virtuais Azure. Para Azure VMs, consulte o seguinte artigo [de visão geral da extensão VM.](../../virtual-machines/extensions/overview.md)

## <a name="prerequisites"></a>Pré-requisitos

- Um computador com a Azure PowerShell. Para obter instruções, consulte [instalar e configurar a Azure PowerShell](/powershell/azure/).

Antes de utilizar o Azure PowerShell para gerir as extensões VM no seu servidor híbrido gerido por servidores ativados pelo Arc, é necessário instalar o `Az.ConnectedMachine` módulo. Executar o seguinte comando no seu servidor ativado arc:

`Install-Module -Name Az.ConnectedMachine`.

Quando a instalação estiver concluída, a seguinte mensagem é devolvida:

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Permitir extensão

Para ativar uma extensão VM no seu servidor ativado pelo Arco, utilize [a extensão new-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) com os parâmetros , `-Name` , , , - `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` `ExtensionType` e. `-Settings`

O exemplo a seguir permite a extensão VM do Log Analytics num servidor Linux ativado pelo Arco:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Para ativar a extensão VM do Log Analytics num servidor Do Windows ativado pelo Arco, altere o valor do `-ExtensionType` parâmetro para o exemplo `"MicrosoftMonitoringAgent"` anterior.

O exemplo a seguir permite a extensão de script personalizado num servidor ativado pelo Arco:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Extensão VM do cofre chave (pré-visualização)

> [!WARNING]
> Os clientes PowerShell muitas vezes `\` adicionam-se ao `"` settings.jssobre o qual causará falhas akvvm_service com erro: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

O exemplo a seguir permite a extensão VM do Cofre de Chaves (pré-visualização) num servidor ativado pelo Arco:

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>Extensões de lista instaladas

Para obter uma lista das extensões VM no seu servidor ativado pelo Arco, utilize [a Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) com os `-MachineName` `-ResourceGroupName` parâmetros e parâmetros.

Exemplo:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Remover uma extensão instalada

Para remover uma extensão VM instalada no seu servidor ativado pelo Arco, utilize [remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) com os `-Name` , e `-MachineName` `-ResourceGroupName` parâmetros.

Por exemplo, para remover a extensão VM do Log Analytics para o Linux, executar o seguinte comando:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Passos seguintes

- Pode implementar, gerir e remover extensões VM utilizando o [Azure CLI](manage-vm-extensions-cli.md), a partir do [portal Azure](manage-vm-extensions-portal.md), ou [modelos Azure Resource Manager](manage-vm-extensions-template.md).

- As informações relativas à resolução de problemas podem ser encontradas no guia de [extensões VM de resolução de problemas.](troubleshoot-vm-extensions.md)
