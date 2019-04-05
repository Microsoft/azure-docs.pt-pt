---
title: Adicionar um artefato a uma VM no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como adicionar um artefato a uma máquina virtual num laboratório no Azure DevTest Labs
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
ms.openlocfilehash: 19a7d6052091f8889a88c61793186b7bf7d9d869
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047029"
---
# <a name="add-an-artifact-to-a-vm"></a>Adicionar um artefato a uma VM
Ao criar uma VM, pode adicionar os artefactos existentes ao mesmo. Estes artefactos podem ser de qualquer um a [repositório de Git de laboratórios DevTest público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) ou a partir do seu próprio repositório de Git. Este artigo mostra-lhe como adicionar os artefactos no portal do Azure e com o Azure PowerShell. 

O Azure DevTest Labs *artefactos* permitem-lhe especificar *ações* que são executadas quando a VM está aprovisionada, como executar scripts do Windows PowerShell, executar os comandos de Bash e instalação de software. Artefactos *parâmetros* permitem-lhe personalizar o artefacto para o seu cenário.

Para saber mais sobre como criar artefactos personalizados, consulte o artigo: [Criar artefactos personalizados](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Utilizar o portal do Azure 
1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório que contém a VM com a qual pretende trabalhar.  
1. Selecione **minhas máquinas virtuais**.
1. Selecione a VM pretendida.
1. Selecione **gerir artefactos**. 
1. Selecione **aplicam-se de artefactos**.
1. Sobre o **aplicam-se de artefactos** painel, selecione o artefacto que pretende adicionar à VM.
1. Sobre o **adicionar artefacto** painel, introduza os valores dos parâmetros necessários e quaisquer parâmetros opcionais que precisa.  
1. Selecione **Add** para adicionar o artefacto e voltar para o **aplicam-se de artefactos** painel.
1. Continue a adicionar os artefactos conforme necessário para a sua VM.
1. Depois de adicionar os artefactos, pode [alterar a ordem na qual são executados os artefactos](#change-the-order-in-which-artifacts-are-run). Também pode aceder novamente à [ver ou modificar um artefato](#view-or-modify-an-artifact).
1. Quando tiver terminado a adição de artefatos, selecione **aplicar**

### <a name="change-the-order-in-which-artifacts-are-run"></a>Alterar a ordem na qual são executados os artefactos
Por predefinição, as ações dos artefactos são executadas na ordem em que são adicionados à VM. Os passos seguintes mostram como alterar a ordem na qual são executados os artefactos.

1. Na parte superior a **aplicam-se de artefactos** painel, selecione a ligação que indica o número de artefactos que foram adicionados à VM.
   
    ![Número de artefactos adicionado à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Sobre o **selecionado artefactos** painel, arraste e largue os artefactos para a ordem pretendida. Se tiver problemas ao arrastar o artefacto, certifique-se de que está sendo arrastado do lado esquerdo do artefacto. 
1. Selecione **OK** quando terminar.  

### <a name="view-or-modify-an-artifact"></a>Ver ou modificar um artefacto
Os passos seguintes mostram como ver ou modificar os parâmetros de um artefato:

1. Na parte superior a **aplicam-se de artefactos** painel, selecione a ligação que indica o número de artefactos que foram adicionados à VM.
   
    ![Número de artefactos adicionado à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Sobre o **selecionado artefactos** painel, selecione o artefacto que pretende visualizar ou editar.  
1. Sobre o **adicionar artefacto** painel, faça qualquer necessárias alterações e selecione **OK** para fechar o **adicionar artefacto** painel.
1. Selecione **OK** para fechar a **selecionado artefactos** painel.

## <a name="use-powershell"></a>Utilizar o PowerShell
O script seguinte aplica-se o artefacto especificado para a VM especificada. O [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) comando é aquele que executa a operação.  

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
$resourceGroupName = (Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
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

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos sobre artefatos:

- [Especifique os artefactos obrigatórios para o laboratório](devtest-lab-mandatory-artifacts.md)
- [Criar artefacto personalizado](devtest-lab-artifact-author.md)
- [Adicionar um repositório de artefacto para um laboratório](devtest-lab-artifact-author.md)
- [Diagnosticar falhas de artefactos](devtest-lab-troubleshoot-artifact-failure.md)
