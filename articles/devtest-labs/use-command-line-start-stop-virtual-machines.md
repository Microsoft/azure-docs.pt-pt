---
title: Use ferramentas de linha de comando para iniciar e parar VMs Azure DevTest Labs
description: Aprenda a usar ferramentas de linha de comando para iniciar e parar máquinas virtuais em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2ddc1620cf86fa203b2f0e31359f9fd262df8916
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499548"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Use ferramentas de linha de comando para iniciar e parar máquinas virtuais Azure DevTest Labs
Este artigo mostra-lhe como usar a Azure PowerShell ou Azure CLI para iniciar ou parar máquinas virtuais num laboratório em Azure DevTest Labs. Pode criar scripts PowerShell/CLI para automatizar estas operações. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
A azure DevTest Labs é uma forma de criar ambientes rápidos, fáceis e magros dev/teste. Permite-lhe gerir custos, a provisionar rapidamente VMs e minimizar o desperdício.  Existem funcionalidades incorporadas no portal Azure que permitem configurar VMs num laboratório para iniciar e parar automaticamente em momentos específicos. 

No entanto, em alguns cenários, poderá querer automatizar o arranque e paragem de VMs a partir de scripts PowerShell/CLI. Dá-lhe alguma flexibilidade para iniciar e parar máquinas individuais a qualquer momento em vez de em momentos específicos. Estas são algumas das situações em que executar estas tarefas usando scripts seria útil.

- Quando utilizar uma aplicação de 3 níveis como parte de um ambiente de teste, os níveis precisam de ser iniciados numa sequência. 
- Desligue um VM quando um critério personalizado é cumprido para economizar dinheiro. 
- Utilize-o como uma tarefa dentro de um fluxo de trabalho CI/CD para iniciar no início do fluxo, utilize os VM como máquinas de construção, máquinas de teste ou infraestruturas, e depois pare os VMs quando o processo estiver concluído. Um exemplo disso seria a fábrica de imagem personalizada com a Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> O seguinte script utiliza o módulo Azure PowerShell Az. 

O seguinte guião PowerShell começa um VM num laboratório. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) é o principal foco para este script. O parâmetro **ResourceId** é o ID de recursos totalmente qualificado para o VM no laboratório. O parâmetro **Ação** é onde as opções **Iniciar** ou **Parar** são definidas dependendo do necessário.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>CLI do Azure
O [Azure CLI](/cli/azure/get-started-with-azure-cli) é outra forma de automatizar o arranque e paragem dos VMs da DevTest Labs. O Azure CLI pode ser [instalado](/cli/azure/install-azure-cli) em diferentes sistemas operativos. O seguinte guião dá-lhe ordens para iniciar e parar um VM num laboratório. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte artigo para utilizar o portal Azure para fazer estas operações: [Reiniciar um VM](devtest-lab-restart-vm.md).
