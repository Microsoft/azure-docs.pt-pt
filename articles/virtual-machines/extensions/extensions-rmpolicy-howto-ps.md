---
title: Utilize a Política Azure para restringir a instalação de extensão VM
description: Utilize a Política Azure para restringir as implementações de extensões.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: b86429c90f436007116a45c6dbab443d6cc889e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188557"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Utilize a Política Azure para restringir a instalação de extensões em VMs do Windows

Se pretender evitar a utilização ou instalação de determinadas extensões nos seus VMs windows, pode criar uma definição de Política Azure utilizando o PowerShell para restringir as extensões para VMs dentro de um grupo de recursos. 

Este tutorial utiliza o Azure PowerShell dentro da Cloud Shell, que é constantemente atualizada para a versão mais recente. 

 

## <a name="create-a-rules-file"></a>Criar um ficheiro de regras

Para restringir as extensões que podem ser instaladas, é necessário ter uma [regra](../../governance/policy/concepts/definition-structure.md#policy-rule) que forneça a lógica para identificar a extensão.

Este exemplo mostra como negar extensões publicadas pela 'Microsoft.Compute' criando um ficheiro de regras no Azure Cloud Shell, mas se estiver a trabalhar no PowerShell localmente, também pode criar um ficheiro local e substituir o caminho ($home/clouddrive) pelo caminho para o ficheiro local na sua máquina.

Numa [Cloud Shell,](https://shell.azure.com/powershell)escreva:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Copie e cole o seguinte .json no ficheiro.

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

Quando terminar, acerte no **CTRL + O** e, em seguida, **introduza** para guardar o ficheiro. Acerte **ctrl + X** para fechar o ficheiro e sair.

## <a name="create-a-parameters-file"></a>Criar um ficheiro de parâmetros

Também precisa de um ficheiro de [parâmetros](../../governance/policy/concepts/definition-structure.md#parameters) que crie uma estrutura para que possa utilizar para passar uma lista das extensões a bloquear. 

Este exemplo mostra-lhe como criar um ficheiro de parâmetros para VMs em Cloud Shell, mas se estiver a trabalhar no PowerShell localmente, também pode criar um ficheiro local e substituir o caminho ($home/clouddrive) pelo caminho para o ficheiro local na sua máquina.

Em [Cloud Shell,](https://shell.azure.com/powershell)tipo:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Copie e cole o seguinte .json no ficheiro.

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

Quando terminar, acerte no **CTRL + O** e, em seguida, **introduza** para guardar o ficheiro. Acerte **ctrl + X** para fechar o ficheiro e sair.

## <a name="create-the-policy"></a>Criar a política

Uma definição de política é um objeto usado para armazenar a configuração que gostaria de usar. A definição de política utiliza as regras e os ficheiros de parâmetros para definir a política. Crie uma definição de política utilizando o cmdlet [New-AzPolicyDefinition.](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition)

 As regras e parâmetros de política são os ficheiros que criou e guardou como ficheiros .json na sua casca de nuvem.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Atribuir a política

Este exemplo atribui a política a um grupo de recursos que utiliza a [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment). Qualquer VM criado no grupo de recursos **myResourceGroup** não será capaz de instalar o Agente de Acesso VM ou as extensões de Script Personalizado. 

Utilize a [Subscrição Get-Az [ Formato-tabela](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) cmdlet para obter o seu ID de subscrição para usar no lugar do exemplo.

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

Para testar a política, tente utilizar a extensão de Acesso VM. O seguinte deve falhar com a mensagem "Set-AzVMAccessExtension : Recurso 'myVMAccess' foi proibido pela política."

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

No portal, a alteração da palavra-passe deve falhar com o "A implementação do modelo falhou devido a violação de políticas.". .

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
