---
title: Ativar a extensão VM utilizando a Azure PowerShell
description: Este artigo descreve como implementar extensões de máquinas virtuais para O Arco Azure habilitados a funcionar em ambientes de nuvem híbrida usando Azure PowerShell.
ms.date: 10/23/2020
ms.topic: conceptual
ms.openlocfilehash: d2408f75c7b6d81ba297de6dcdb85a712cd8908f
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495440"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Ativar extensões Azure VM usando Azure PowerShell

Este artigo mostra-lhe como implantar e desinstalar extensões Azure VM, suportadas por servidores ativados pelo Azure Arc, para uma máquina híbrida Linux ou Windows utilizando a Azure PowerShell.

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
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType OmsAgentforLinux"
```

O exemplo a seguir permite a extensão de script personalizado num servidor ativado pelo Arco:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
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
