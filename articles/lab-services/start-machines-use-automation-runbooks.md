---
title: Inicie máquinas usando livros de automação em Laboratórios Azure DevTest
description: Aprenda a iniciar máquinas virtuais num laboratório em Azure DevTest Labs utilizando livros de execução da Azure Automation.
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
ms.openlocfilehash: df6d7943a5344b4288dfe369dcce9087b894984f
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580586"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Inicie máquinas virtuais em laboratório por ordem usando livros de execução da Automação Azure
A funcionalidade de [arranque automático](devtest-lab-set-lab-policy.md#set-autostart) de DevTest Labs permite configurar os VMs para começar automaticamente numa hora especificada. No entanto, esta funcionalidade não suporta que as máquinas comecem numa ordem específica. Existem vários cenários em que este tipo de automatização seria útil.  Um dos cenários é onde um VM Jumpbox dentro de um laboratório precisa de ser iniciado primeiro, antes dos outros VMs, uma vez que a Jumpbox é usada como ponto de acesso para os outros VMs.  Este artigo mostra-lhe como configurar uma conta De Automação Azure com um livro de execução PowerShell que executa um script. O script utiliza etiquetas em VMs no laboratório para permitir controlar a ordem de arranque sem ter de alterar o script.

## <a name="setup"></a>Configuração
Neste exemplo, os VMs no laboratório precisam de ter a etiqueta **StartupOrder** adicionada com o valor apropriado (0,1,2, etc.). Designe qualquer máquina que não precise de ser iniciada como -1.

## <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure
Crie uma conta De Automação Azure seguindo instruções [neste artigo](../automation/automation-create-standalone-account.md). Escolha a opção **Executar Como Contas** ao criar a conta. Assim que a conta de automatização for criada, abra a página **Módulos** e selecione **Módulos Update Azure** na barra de menus. Os módulos predefinidos são várias versões antigas e sem a atualização o script pode não funcionar.

## <a name="add-a-runbook"></a>Adicione um livro de corridas
Agora, para adicionar um livro de execução à conta de automação, selecione **Runbooks** no menu esquerdo. Selecione Adicionar um livro de **execução** no menu e siga as instruções para criar um livro de [execução PowerShell](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Script do PowerShell
O seguinte guião tem o nome de subscrição, o nome do laboratório como parâmetros. O fluxo do script é obter todos os VMs no laboratório, e, em seguida, analisar as informações de etiqueta para criar uma lista dos nomes vm e sua ordem de arranque. O guião percorre os VMs em ordem e inicia os VMs. Se houver vários VMs num número de encomenda específico, eles são iniciados sincronicamente usando trabalhos PowerShell. Para aqueles VMs que não têm etiqueta, definir o valor de arranque para ser o último (10), eles serão iniciados em último, por padrão.  Se o laboratório não quer que o VM seja iniciado automaticamente, defina o valor da etiqueta para 11 e será ignorado.

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
Para que este guião execute diariamente, [crie um horário](../automation/shared-resources/schedules.md#create-a-schedule) na conta de automação. Assim que o horário for criado, [ligue-o ao livro de execução.](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook) 

Numa situação em larga escala, onde existem múltiplas subscrições com vários laboratórios, guarde a informação do parâmetro num ficheiro para diferentes laboratórios e passe o ficheiro para o script em vez dos parâmetros individuais. O guião teria de ser modificado, mas a execução do núcleo seria a mesma. Enquanto esta amostra utiliza a Automatização Azure para executar o script PowerShell, existem outras opções como usar uma tarefa num pipeline Build/Release.

## <a name="next-steps"></a>Passos seguintes
Veja o seguinte artigo para saber mais sobre a Automação Azure: [Uma introdução à Automação Azure.](../automation/automation-intro.md)
