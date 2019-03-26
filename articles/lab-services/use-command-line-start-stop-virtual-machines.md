---
title: Utilize ferramentas de linha de comandos para iniciar e parar as VMs do Azure DevTest Labs | Documentos da Microsoft
description: Saiba como utilizar as ferramentas da linha de comandos para iniciar e parar máquinas virtuais no Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 9b7df83b710bac0b37ac28c432f63a47ddda21d1
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58440051"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Utilize ferramentas de linha de comandos para iniciar e parar máquinas de virtuais do Azure DevTest Labs
Este artigo mostra-lhe como utilizar o Azure PowerShell ou a CLI do Azure para iniciar ou parar máquinas virtuais num laboratório no Azure DevTest Labs. Pode criar scripts do PowerShell/CLI para automatizar essas operações. 

## <a name="overview"></a>Descrição geral
O Azure DevTest Labs é uma forma de criar ambientes de desenvolvimento/teste rápidos, fáceis, rápidos e simples. Ele permite-lhe gerir o custo, rapidamente aprovisionar VMs e minimizar o desperdício.  Existem recursos incorporados no portal do Azure que permitem-lhe configurar as VMs num laboratório automaticamente iniciar e parar em alturas específicas. 

No entanto, em alguns cenários, talvez queira automatizar iniciar e parar de VMs a partir dos scripts do PowerShell/CLI. Isso permite certa flexibilidade com iniciar e parar máquinas individuais em qualquer altura, em vez de em alturas específicas. Aqui estão algumas das situações em que executar estas tarefas utilizando scripts seria útil.

- Quando utiliza uma aplicação de 3 camadas como parte de um ambiente de teste, as camadas tem de ser iniciada numa seqüência. 
- Desative a uma VM quando um critério de personalizados são cumpridos para poupar dinheiro. 
- Utilize-o como uma tarefa dentro de um fluxo de trabalho de CI/CD para começar do início do fluxo, utilize a VMs como máquinas de compilação, testar máquinas, nem a infraestrutura, em seguida, pare as VMs quando o processo estiver concluído. Um exemplo disso seria a fábrica de imagem personalizada com o Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell
O seguinte script do PowerShell inicia uma VM num laboratório. [AzureRmResourceAction invocar](/powershell/module/azurerm.resources/invoke-azurermresourceaction?view=azurermps-6.13.0) é o foco principal deste script. O **ResourceId** parâmetro é o ID de recurso completamente qualificado para a VM no laboratório. O **ação** parâmetro é o local onde o **iniciar** ou **parar** são definidas opções consoante o que é necessário.

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
Select-AzureRMSubscription -SubscriptionId $subscriptionId

# Get the lab information
if ($(Get-Module -Name AzureRM).Version.Major -eq 6) {
    $devTestLab = Get-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' -Name $devTestLabName
} else {
    $devTestLab = Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $devTestLabName
}

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzureRmResourceAction `
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
O [CLI do Azure](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) é outra forma de automatizar a iniciar e parar de VMs de laboratórios DevTest. CLI do Azure pode ser [instalado](/cli/azure/install-azure-cli?view=azure-cli-latest) em sistemas operativos diferentes. O script seguinte fornece comandos para iniciar e parar uma VM num laboratório. 

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


## <a name="next-steps"></a>Passos Seguintes
Consulte o artigo seguinte para utilizar o portal do Azure para fazer essas operações: [Reiniciar uma VM](devtest-lab-restart-vm.md).