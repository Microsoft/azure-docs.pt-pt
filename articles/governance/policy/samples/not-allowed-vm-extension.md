---
title: Exemplo – não permitido extensões de VM
description: Esta política de exemplo proíbe a utilização de extensões especificadas.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 09/18/2018
ms.author: dacoulte
ms.openlocfilehash: d38b5e64e210b87dc4434a0dbb12670c49fa35f7
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318644"
---
# <a name="not-allowed-vm-extensions"></a>Extensões de VM não permitidas

Proíbe a utilização de extensões especificadas. Especifica uma matriz que contém os tipos de extensão proibidos.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modelo de exemplo

[!code-json[main](../../../../policy-templates/samples/compute/not-allowed-vmextension/azurepolicy.json "Not allowed VM Extensions")]

Pode implementar este modelo através do [portal do Azure](#deploy-with-the-portal), do [PowerShell](#deploy-with-powershell) ou da [CLI do Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Implementar com o portal

[![Implementar no Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fnot-allowed-vmextension%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Implementar com o PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition -Name "not-allowed-vmextension" -DisplayName "Not allowed VM Extensions" -description "This policy governs which VM extensions that are explicitly denied." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/not-allowed-vmextension/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/not-allowed-vmextension/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -notAllowedExtensions <Denied extension> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Limpar a implementação do PowerShell

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Implementar com a CLI do Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'not-allowed-vmextension' --display-name 'Not allowed VM Extensions' --description 'This policy governs which VM extensions that are explicitly denied.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/not-allowed-vmextension/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/not-allowed-vmextension/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "not-allowed-vmextension"
```

### <a name="clean-up-azure-cli-deployment"></a>Limpar a implementação da CLI do Azure

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos Seguintes

- Veja mais exemplos na página [Exemplos do Azure Policy](index.md)