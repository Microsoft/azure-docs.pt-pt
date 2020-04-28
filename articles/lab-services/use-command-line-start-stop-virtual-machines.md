---
title: Utilize ferramentas de linha de comando para iniciar e parar vMs Azure DevTest Labs
description: Aprenda a usar ferramentas de linha de comando para iniciar e parar máquinas virtuais em Laboratórios Azure DevTest.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: fd643559a09d5c75aad9be5f35c653994c8488cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169257"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Utilize ferramentas de linha de comando para iniciar e parar máquinas virtuais Azure DevTest Labs
Este artigo mostra-lhe como usar o Azure PowerShell ou o Azure CLI para iniciar ou parar máquinas virtuais num laboratório em Azure DevTest Labs. Pode criar scripts PowerShell/CLI para automatizar estas operações. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
A Azure DevTest Labs é uma forma de criar ambientes rápidos, fáceis e magros de v/teste. Permite-lhe gerir os custos, fornecer rapidamente VMs e minimizar o desperdício.  Existem funcionalidades incorporadas no portal Azure que permitem configurar VMs num laboratório para iniciar e parar automaticamente em momentos específicos. 

No entanto, em alguns cenários, poderá querer automatizar o arranque e a paragem de VMs a partir de scripts PowerShell/CLI. Dá-lhe alguma flexibilidade para iniciar e parar máquinas individuais a qualquer momento em vez de em momentos específicos. Eis algumas das situações em que executar estas tarefas utilizando scripts seria útil.

- Quando se utiliza uma aplicação de 3 camadas como parte de um ambiente de teste, os níveis precisam de ser iniciados numa sequência. 
- Desligue um VM quando um critério personalizado for cumprido para economizar dinheiro. 
- Use-a como tarefa dentro de um fluxo de trabalho CI/CD para iniciar no início do fluxo, use os VMs como máquinas de construção, máquinas de ensaio ou infraestrutura, e depois pare os VMs quando o processo estiver concluído. Um exemplo disso seria a fábrica de imagem personalizada com a Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> O seguinte script utiliza o módulo Azure PowerShell Az. 

O seguinte guião powerShell começa um VM em um laboratório. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) é o principal foco para este script. O parâmetro **ResourceId** é o ID de recursos totalmente qualificado para o VM no laboratório. O parâmetro **action** é onde as opções **iniciar** ou **parar** são definidas dependendo do necessário.

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
O [Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) é outra forma de automatizar o arranque e paragem dos VMs de DevTest Labs. O Azure CLI pode ser [instalado](/cli/azure/install-azure-cli?view=azure-cli-latest) em diferentes sistemas operativos. O seguinte guião dá-lhe ordens para começar e parar um VM em laboratório. 

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
