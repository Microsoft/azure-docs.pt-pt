---
title: Utilizar o Azure Policy para restringir a instalação da extensão de VM | Documentos da Microsoft
description: Utilize o Azure Policy para restringir as implementações de extensão.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: dfaeff5e8f90e72645293d15c454f78f17b08fb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387337"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Utilizar a política do Azure para restringir a instalação de extensões em VMs do Windows

Se quiser evitar que a utilização ou a instalação de determinadas extensões nas suas VMs do Windows, pode criar uma política do Azure com o PowerShell para restringir as extensões para as VMs dentro de um grupo de recursos. 

Este tutorial utiliza o Azure PowerShell no Cloud Shell, que é constantemente atualizada para a versão mais recente. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-rules-file"></a>Crie um ficheiro de regras

Para restringir que extensões podem ser instalados, tem de ter uma [regra](../../governance/policy/concepts/definition-structure.md#policy-rule) para fornecer a lógica para identificar a extensão.

Este exemplo mostra como negar extensões publicadas pela "Microsoft. Compute" através da criação de um arquivo de regras no Azure Cloud Shell, mas se estiver a trabalhar no PowerShell localmente, também pode criar um ficheiro local e substituir o caminho ($home/clouddrive) com o caminho para o ficheiro local no seu computador.

Num [Cloud Shell](https://shell.azure.com/powershell), tipo:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Copie e cole o seguinte. de JSON para o ficheiro.

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

Quando tiver terminado, prima a **Ctrl + S** e, em seguida **Enter** para guardar o ficheiro. Pressionar **Ctrl + X** para fechar o ficheiro e saia.

## <a name="create-a-parameters-file"></a>Crie um ficheiro de parâmetros

Também é necessário um [parâmetros](../../governance/policy/concepts/definition-structure.md#parameters) ficheiro que cria uma estrutura para que possa utilizar para passar numa lista das extensões para bloquear. 

Este exemplo mostra como criar um ficheiro de parâmetros para as VMs no Cloud Shell, mas se estiver a trabalhar no PowerShell localmente, também pode criar um ficheiro local e substituir o caminho ($home/clouddrive) com o caminho para o arquivo local no seu computador.

Na [Cloud Shell](https://shell.azure.com/powershell), tipo:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Copie e cole o seguinte. de JSON para o ficheiro.

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

Quando tiver terminado, prima a **Ctrl + S** e, em seguida **Enter** para guardar o ficheiro. Pressionar **Ctrl + X** para fechar o ficheiro e saia.

## <a name="create-the-policy"></a>Criar a política

Uma definição de política é um objeto usado para armazenar a configuração que pretende utilizar. A definição de política usa os arquivos de regras e parâmetros para definir a política. Criar uma definição de política com o [New-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition) cmdlet.

 As regras de política e os parâmetros são os arquivos criados e armazenados como arquivos. JSON no cloud shell.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Atribuir a política

Este exemplo atribui a política para um grupo de recursos utilizando [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment). Qualquer VM criada no **myResourceGroup** grupo de recursos não será possível instalar as extensões de agente de acesso à VM ou de Script personalizado. 

Utilize o [Get-AzSubscription | Format-Table](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) cmdlet para obter o seu ID de subscrição para utilizar no lugar no exemplo.

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

Para testar a política, tente utilizar a extensão de acesso à VM. O seguinte deve falhar com a mensagem "Set-AzVMAccessExtension: O recurso 'myVMAccess' não foi permitido pela política."

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

No portal, a alteração de palavra-passe deve falhar com o "a implementação do modelo falhou devido a violação de política." mensagem.

## <a name="remove-the-assignment"></a>Remover a atribuição

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Remover a política

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte [do Azure Policy](../../governance/policy/overview.md).
