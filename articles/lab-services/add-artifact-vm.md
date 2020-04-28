---
title: Adicione um artefacto a um VM em Azure DevTest Labs [ Microsoft Docs
description: Aprenda a adicionar um artefacto a uma máquina virtual num laboratório em Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 27fec279582d845972b87ac635c87c16c239924e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73901311"
---
# <a name="add-an-artifact-to-a-vm"></a>Adicionar um artefacto a uma VM
Ao criar um VM, pode adicionar-lhe artefactos existentes. Estes artefactos podem ser do [repositório público de DevTest Labs Git](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) ou do seu próprio repositório Git. Este artigo mostra-lhe como adicionar artefactos no portal Azure, e utilizando o Azure PowerShell. 

Os artefactos do Azure DevTest *Labs* permitem especificar *as ações* que são executadas quando o VM é provisionado, tais como executar scripts Windows PowerShell, executar comandos Bash e instalar software. Os *parâmetros* do artefacto permitem personalizar o artefacto para o seu cenário particular.

Para saber como criar artefactos personalizados, consulte o artigo: [Criar artefactos personalizados.](devtest-lab-artifact-author.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Utilizar o portal do Azure 
1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Da lista de laboratórios, selecione o laboratório que contém o VM com o qual quer trabalhar.  
1. Selecione **As minhas máquinas virtuais.**
1. Selecione o VM desejado.
1. Selecione **Gerir artefactos**. 
1. Selecione **Aplicar artefactos**.
1. No painel **de artefactos Apply,** selecione o artefacto que pretende adicionar ao VM.
1. No painel **de artefactos Adicionar,** introduza os valores de parâmetro sinuoso e os parâmetros opcionais de que necessita.  
1. Selecione **Adicionar** para adicionar o artefacto e voltar ao painel de **artefactos Apply.**
1. Continue a adicionar artefactos conforme necessário para o seu VM.
1. Depois de adicionar os seus artefactos, pode [alterar a ordem em que os artefactos são executados.](#change-the-order-in-which-artifacts-are-run) Também pode voltar a [ver ou modificar um artefacto.](#view-or-modify-an-artifact)
1. Quando terminar de adicionar artefactos, selecione **Apply**

### <a name="change-the-order-in-which-artifacts-are-run"></a>Alterar a ordem em que os artefactos são executados
Por predefinição, as ações dos artefactos são executadas na ordem em que são adicionados ao VM. Os seguintes passos ilustram como alterar a ordem em que os artefactos são executados.

1. Na parte superior do painel de **artefactos Apply,** selecione o link indicando o número de artefactos que foram adicionados ao VM.
   
    ![Número de artefactos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No painel de **artefactos selecionados,** arraste e deixe os artefactos na ordem desejada. Se tiver problemas em arrastar o artefacto, certifique-se de que está a arrastar-se do lado esquerdo do artefacto. 
1. Selecione **OK** quando terminar.  

### <a name="view-or-modify-an-artifact"></a>Ver ou modificar um artefacto
Os seguintes passos ilustram como visualizar ou modificar os parâmetros de um artefacto:

1. Na parte superior do painel de **artefactos Apply,** selecione o link indicando o número de artefactos que foram adicionados ao VM.
   
    ![Número de artefactos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No painel de **artefactos Selecionados,** selecione o artefacto que pretende ver ou editar.  
1. No painel **de artefactos Adicionar,** faça as alterações necessárias e selecione **OK** para fechar o painel de **artefactos Adicionar.**
1. Selecione **OK** para fechar o painel de **artefactos selecionados.**

## <a name="use-powershell"></a>Utilizar o PowerShell
O seguinte script aplica o artefacto especificado ao VM especificado. O comando [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) é o que executa a operação.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos sobre artefactos:

- [Especifique artefactos obrigatórios para o seu laboratório](devtest-lab-mandatory-artifacts.md)
- [Criar artefacto personalizado](devtest-lab-artifact-author.md)
- [Adicionar um repositório de artefacto para um laboratório](devtest-lab-artifact-author.md)
- [Diagnosticar falhas de artefactos](devtest-lab-troubleshoot-artifact-failure.md)
