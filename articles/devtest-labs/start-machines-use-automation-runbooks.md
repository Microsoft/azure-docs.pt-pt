---
title: Iniciar máquinas usando livros de automação em Azure DevTest Labs
description: Aprenda a iniciar máquinas virtuais num laboratório em Azure DevTest Labs utilizando os runbooks da Azure Automation.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 231e79d594aab7c59fa21f9ee512abaa9ac67043
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87282267"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Inicie máquinas virtuais em laboratório para usar os runbooks da Azure Automation
A funcionalidade de [arranque automático](devtest-lab-set-lab-policy.md#set-autostart) da DevTest Labs permite-lhe configurar VMs para iniciar automaticamente num determinado momento. No entanto, esta funcionalidade não suporta máquinas para iniciar uma ordem específica. Há vários cenários em que este tipo de automatização seria útil.  Um dos cenários é onde um VM jumpbox dentro de um laboratório precisa ser iniciado primeiro, antes dos outros VMs, uma vez que o Jumpbox é usado como ponto de acesso aos outros VMs.  Este artigo mostra-lhe como configurar uma conta Azure Automation com um livro de execução PowerShell que executa um script. O script usa tags em VMs no laboratório para permitir que você controle a ordem de arranque sem ter que alterar o script.

## <a name="setup"></a>Configuração
Neste exemplo, os VMs no laboratório precisam de ter a etiqueta **StartupOrder** adicionada com o valor apropriado (0,1,2, etc.). Designe qualquer máquina que não precise de ser iniciada como -1.

## <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure
Crie uma conta Azure Automation seguindo as instruções [deste artigo.](../automation/automation-create-standalone-account.md) Escolha a opção **'Executar Como Contas'** ao criar a conta. Assim que a conta de automação for criada, abra a página **Módulos** e selecione **Update Azure Modules** na barra de menus. Os módulos predefinidos são várias versões antigas e sem a atualização o script pode não funcionar.

## <a name="add-a-runbook"></a>Adicione um livro de execução
Agora, para adicionar um runbook à conta de automação, selecione **Runbooks** no menu esquerdo. **Selecione Adicione um livro de execução** no menu e siga as instruções para criar um livro de bordo [PowerShell](../automation/learn/automation-tutorial-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Script do PowerShell
O seguinte script tem o nome de subscrição, o nome do laboratório como parâmetros. O fluxo do script é obter todos os VMs no laboratório, e depois analisar a informação da etiqueta para criar uma lista dos nomes VM e sua ordem de arranque. O script percorre os VMs por ordem e inicia os VMs. Se existirem vários VMs num número de encomenda específico, são iniciados com trabalhos PowerShell. Para os VMs que não têm uma etiqueta, definir o valor de arranque para ser o último (10), eles serão iniciados em último, por padrão.  Se o laboratório não quiser que o VM seja automaticamente iniciado, desajuste o valor da etiqueta para 11 e será ignorado.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Criar um horário
Para que este script seja executado diariamente, [crie um horário](../automation/shared-resources/schedules.md#create-a-schedule) na conta de automação. Uma vez criado o horário, [ligue-o ao livro de recortes](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook). 

Numa situação em larga escala em que existem múltiplas subscrições com vários laboratórios, guarde a informação do parâmetro num ficheiro para diferentes laboratórios e passe o ficheiro para o script em vez dos parâmetros individuais. O guião teria de ser modificado, mas a execução do núcleo seria a mesma. Embora esta amostra utilize a Automatização Azure para executar o script PowerShell, existem outras opções como usar uma tarefa num pipeline Build/Release.

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte artigo para saber mais sobre a Azure Automation: [Uma introdução à Azure Automation](../automation/automation-intro.md).
