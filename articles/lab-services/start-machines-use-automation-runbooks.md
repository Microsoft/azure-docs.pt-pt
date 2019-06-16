---
title: Iniciar máquinas através de runbooks de automatização no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como iniciar máquinas virtuais num laboratório no Azure DevTest Labs utilizando runbooks de automatização do Azure.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: 8d3885ba25e479316f97ecbb0681a1680650fc09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61083623"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Iniciar máquinas virtuais num laboratório na ordem utilizando runbooks de automatização do Azure
O [início automático](devtest-lab-set-lab-policy.md#set-autostart) funcionalidade do DevTest Labs permite-lhe configurar as VMs para iniciar automaticamente a uma hora especificada. No entanto, esta funcionalidade não suporta máquinas para começar por uma ordem específica. Existem vários cenários nos quais esse tipo de automação pode ser útil.  Um cenário é onde uma VM da Jumpbox dentro de um laboratório tem de ser iniciados primeiro, antes de outras VMs, como a Jumpbox é utilizada como o ponto de acesso para as outras VMs.  Este artigo mostra-lhe como configurar uma conta de automatização do Azure com um runbook do PowerShell que executa um script. O script usa etiquetas em VMs no laboratório para permitir-lhe controlar a ordem de arranque sem ter de alterar o script.

## <a name="setup"></a>Configurar
Neste exemplo, as VMs no laboratório tem de ter a marca **StartupOrder** adicionada com o valor apropriado (0,1,2, etc.). Designe qualquer computador que não tem de ser iniciado como -1.

## <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure
Criar uma conta de automatização do Azure ao seguir as instruções em [este artigo](../automation/automation-create-standalone-account.md). Escolha o **contas Run as** opção ao criar a conta. Depois da conta de automatização é criada, abra a **módulos** página e selecione **módulos do Azure de atualização** na barra de menus. Os módulos do padrão têm que várias versões antigas e sem a atualização do script podem não funcionar.

## <a name="add-a-runbook"></a>Adicionar um runbook
Agora, para adicionar um runbook para a conta de automatização, selecione **Runbooks** no menu da esquerda. Selecione **adicionar um runbook** no menu e instruções para [criar um runbook do PowerShell](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Script do PowerShell
O script seguinte obtém o nome de subscrição, o nome de laboratório como parâmetros. O fluxo do script é obter todas as VMs do laboratório e, em seguida, analisar as informações de marca para criar uma lista de nomes de VMS e sua ordem de arranque. O script percorre as VMs pela ordem e inicia as VMs. Se existirem várias VMs num número de ordem específica, eles são iniciados de forma assíncrona com tarefas do PowerShell. Para essas VMs que não têm uma marca, o valor de inicialização de conjunto para ser o último (10), eles serão iniciados por último, por predefinição.  Se o laboratório não quiser que a VM ser autostarted, defina o valor de etiqueta a 11 e serão ignorada.

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

## <a name="create-a-schedule"></a>Criar uma agenda
Para que este script diariamente, execute [criar uma agenda](../automation/shared-resources/schedules.md#creating-a-schedule) na conta de automatização. Depois de criar a agenda, [ligá-lo para o runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

Numa situação em grande escala em que há várias subscrições com vários laboratórios, armazenar as informações de parâmetro num arquivo para laboratórios diferentes e passar o ficheiro para o script em vez dos parâmetros individuais. O script precisaria ser modificado, mas a execução de núcleo seria o mesmo. Embora este exemplo usa a automatização do Azure para executar o script do PowerShell, existem outras opções, como a utilização de uma tarefa num pipeline de compilação/liberação.

## <a name="next-steps"></a>Passos Seguintes
Veja o seguinte artigo para saber mais sobre a automatização do Azure: [Uma introdução à automatização do Azure](../automation/automation-intro.md).
