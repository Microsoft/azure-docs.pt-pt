---
title: Adicione um artefacto a um VM em Azure DevTest Labs | Microsoft Docs
description: Saiba como adicionar um artefacto a uma máquina virtual num laboratório em Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4772755d8077f7a659c4d403961ffaeb9e1d483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483895"
---
# <a name="add-an-artifact-to-a-vm"></a>Adicionar um artefacto a uma VM
Ao criar um VM, pode adicionar-lhe artefactos existentes. Estes artefactos podem ser do [repositório público de DevTest Labs Git](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) ou do seu próprio repositório de Git. Este artigo mostra-lhe como adicionar artefactos no portal Azure, e usando Azure PowerShell. 

Os artefactos da Azure DevTest Labs permitem especificar *as ações* que são *executadas* quando o VM é a provisionado, tais como executar scripts Windows PowerShell, executar comandos Bash e instalar software. Os *parâmetros* do artefacto permitem personalizar o artefacto para o seu cenário particular.

Para aprender como criar artefactos personalizados, consulte o artigo: [Crie artefactos personalizados.](devtest-lab-artifact-author.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Utilizar o portal do Azure 
1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório que contém o VM com o qual pretende trabalhar.  
1. Selecione **As minhas máquinas virtuais.**
1. Selecione o VM desejado.
1. **Selecione Gerir artefactos**. 
1. **Selecione Aplicar artefactos**.
1. No painel de **artefactos Apply,** selecione o artefacto que pretende adicionar ao VM.
1. No painel **de artefactos Add,** introduza os valores de parâmetros necessários e quaisquer parâmetros opcionais de que necessita.  
1. **Selecione Adicione** para adicionar o artefacto e volte ao painel de **artefactos Aplicar.**
1. Continue a adicionar artefactos conforme necessário para o seu VM.
1. Depois de adicionar os seus artefactos, pode [alterar a ordem em que os artefactos são executados.](#change-the-order-in-which-artifacts-are-run) Também pode voltar a [ver ou modificar um artefacto.](#view-or-modify-an-artifact)
1. Quando terminar de adicionar artefactos, **selecione Aplicar**

### <a name="change-the-order-in-which-artifacts-are-run"></a>Alterar a ordem em que os artefactos são executados
Por predefinição, as ações dos artefactos são executadas na ordem em que são adicionadas ao VM. Os seguintes passos ilustram como alterar a ordem em que os artefactos são executados.

1. No topo do painel de **artefactos Apply,** selecione o link indicando o número de artefactos que foram adicionados ao VM.
   
    ![Número de artefactos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na vidraça de **artefactos selecionados,** arraste e deixe cair os artefactos na ordem desejada. Se tiver problemas em arrastar o artefacto, certifique-se de que está a arrastar-se pelo lado esquerdo do artefacto. 
1. Selecione **OK** quando terminar.  

### <a name="view-or-modify-an-artifact"></a>Ver ou modificar um artefacto
Os seguintes passos ilustram como ver ou modificar os parâmetros de um artefacto:

1. No topo do painel de **artefactos Apply,** selecione o link indicando o número de artefactos que foram adicionados ao VM.
   
    ![Número de artefactos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No painel de **artefactos selecionados,** selecione o artefacto que pretende visualizar ou editar.  
1. No painel **de artefactos Add,** faça as alterações necessárias e selecione **OK** para fechar o painel de **artefactos Add.**
1. Selecione **OK** para fechar o painel **de artefactos selecionados.**

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
