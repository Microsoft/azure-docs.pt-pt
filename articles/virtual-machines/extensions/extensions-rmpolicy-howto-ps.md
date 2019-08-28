---
title: Usar Azure Policy para restringir a instalação da extensão de VM | Microsoft Docs
description: Use Azure Policy para restringir implantações de extensão.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz
ms.reviewer: cynthn
ms.openlocfilehash: 1c8676fc2c806918117a2fb7273ecd4b999803cd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084545"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Usar Azure Policy para restringir a instalação de extensões em VMs do Windows

Se você quiser impedir o uso ou a instalação de determinadas extensões em suas VMs do Windows, poderá criar uma política do Azure usando o PowerShell para restringir as extensões para VMs em um grupo de recursos. 

Este tutorial usa Azure PowerShell no Cloud Shell, que é constantemente atualizado para a versão mais recente. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-rules-file"></a>Criar um arquivo de regras

Para restringir quais extensões podem ser instaladas, você precisa ter uma [regra](../../governance/policy/concepts/definition-structure.md#policy-rule) para fornecer a lógica para identificar a extensão.

Este exemplo mostra como negar extensões publicadas por ' Microsoft. Compute ' criando um arquivo de regras no Azure Cloud Shell, mas se você estiver trabalhando localmente no PowerShell, também poderá criar um arquivo local e substituir o caminho ($home/CloudDrive) pelo caminho para o arquivo local em seu computador.

Em um [Cloud Shell](https://shell.azure.com/powershell), digite:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Copie e cole o. JSON a seguir no arquivo.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Quando terminar, pressione **Ctrl + O** e, em seguida, **Enter** para salvar o arquivo. Pressione **Ctrl + X** para fechar o arquivo e sair.

## <a name="create-a-parameters-file"></a>Criar um arquivo de parâmetros

Você também precisa de um arquivo de [parâmetros](../../governance/policy/concepts/definition-structure.md#parameters) que cria uma estrutura a ser usada para passar uma lista das extensões a serem bloqueadas. 

Este exemplo mostra como criar um arquivo de parâmetros para VMs no Cloud Shell, mas se você estiver trabalhando localmente no PowerShell, também poderá criar um arquivo local e substituir o caminho ($home/CloudDrive) pelo caminho para o arquivo local em seu computador.

Em [Cloud Shell](https://shell.azure.com/powershell), digite:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Copie e cole o. JSON a seguir no arquivo.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Quando terminar, pressione **Ctrl + O** e, em seguida, **Enter** para salvar o arquivo. Pressione **Ctrl + X** para fechar o arquivo e sair.

## <a name="create-the-policy"></a>Criar a política

Uma definição de política é um objeto usado para armazenar a configuração que você deseja usar. A definição de política usa os arquivos de regras e parâmetros para definir a política. Crie uma definição de política usando o cmdlet [New-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition) .

 As regras de política e os parâmetros são os arquivos que você criou e armazenou como arquivos. JSON em seu Cloud Shell.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Atribuir a política

Este exemplo atribui a política a um grupo de recursos usando [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment). Qualquer VM criada no grupo de recursos MyResource Group não será capaz de instalar o agente de acesso da VM ou as extensões de script personalizado. 

Use o [Get-AzSubscription | Cmdlet Format-Table](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) para obter sua ID de assinatura para usar no lugar do exemplo.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>Testar a política

Para testar a política, tente usar a extensão de acesso à VM. O seguinte deve falhar com a mensagem "Set-AzVMAccessExtension: O recurso ' myVMAccess ' não foi permitido pela política. "

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

No portal, a alteração de senha deve falhar com a "falha na implantação do modelo devido a violação de política". Mensagem.

## <a name="remove-the-assignment"></a>Remover a atribuição

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Remover a política

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, veja [Azure Policy](../../governance/policy/overview.md).
