---
title: Cópia de segurança do Azure Stack | Documentos da Microsoft
description: Efetue uma cópia de segurança a pedido no Azure Stack com cópia de segurança no local.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: 685183f61c1574d8c533efad8a7e0c46b1e2d23c
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870317"
---
# <a name="back-up-azure-stack"></a>Criar cópias de segurança do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Efetue uma cópia de segurança a pedido no Azure Stack. Para obter instruções sobre como configurar o ambiente do PowerShell, consulte [instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md). Para iniciar sessão no Azure Stack, veja [com o portal de administrador no Azure Stack](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Iniciar cópia de segurança do Azure Stack

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Iniciar uma nova cópia de segurança sem o progresso da tarefa de controlo
Utilize o início AzSBackup para iniciar uma nova cópia de segurança imediatamente com nenhum progresso da tarefa de controlo.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Iniciar a cópia de segurança do Azure Stack com o progresso da tarefa de controlo
Utilize AzSBackup iniciar para iniciar uma nova cópia de segurança com o **- AsJob** parâmetro e salvá-la como uma variável para acompanhar o progresso da tarefa de cópia de segurança.

> [!NOTE]
> Tarefa de cópia de segurança irá aparecer com êxito concluída no portal do cerca de 10 a 15 minutos antes da tarefa será concluída.
>
> Por conseguinte, o estado real melhor é observado por meio de código abaixo.

> [!IMPORTANT]
> O atraso de 1 milissegundo inicial é introduzido uma vez que o código é muito rápido para a tarefa de registo corretamente e volta e sem qualquer **PSBeginTime** e, por sua vez, sem qualquer **estado** da tarefa.

```powershell
    $BackupJob = Start-AzsBackup -Force -AsJob
    While (!$BackupJob.PSBeginTime) {
        Start-Sleep -Milliseconds 1
    }
    Write-Host "Start time: $($BackupJob.PSBeginTime)"
    While ($BackupJob.State -eq "Running") {
        Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
        Start-Sleep -Seconds 30
    }

    If ($BackupJob.State -eq "Completed") {
        Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
        $Duration = $BackupJob.Output.TimeTakenToCreate
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
        If ($Duration -match $Pattern) {
            If (!$Matches.ContainsKey("Hours")) {
                $Hours = ""
            } 
            Else {
                $Hours = ($Matches.Hours).ToString + 'h '
            }
            $Minutes = ($Matches.Minutes)
            $Seconds = [math]::round(($Matches.Seconds))
            $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
        }
        Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
    }
    ElseIf ($BackupJob.State -ne "Completed") {
        $BackupJob
        $BackupJob.Output
    }
```

## <a name="confirm-backup-has-completed"></a>Confirmar cópia de segurança foi concluída

### <a name="confirm-backup-has-completed-using-powershell"></a>Confirmar cópia de segurança foi concluída com o PowerShell
Utilize os seguintes comandos do PowerShell para se certificar que cópia de segurança foi concluída com êxito:

```powershell
   Get-AzsBackup
```

O resultado deverá ser semelhante a seguinte saída:

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>Confirmar cópia de segurança foi concluída no portal de administração
Utilize o portal de administração do Azure Stack para verificar que essa cópia de segurança foi concluída com êxito ao seguir estes passos:

1. Abra o [portal de administração do Azure Stack](azure-stack-manage-portals.md).
2. Selecione **todos os serviços**e, no **administração** categoria selecione > **cópia de segurança da infraestrutura**. Escolher **Configuration** no **cópia de segurança da infraestrutura** painel.
3. Encontrar o **Name** e **data concluída** da cópia de segurança no **cópias de segurança disponíveis** lista.
4. Verifique se o **estado** é **Succeeded**.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o fluxo de trabalho para [recuperar a partir de um evento de perda de dados](azure-stack-backup-recover-data.md).
