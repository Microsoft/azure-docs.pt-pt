---
title: Backup Azure para O Servidor SQL em execução em Azure VM
description: Neste artigo, aprenda a registar a Azure Backup no SQL Server a funcionar numa máquina virtual Azure.
author: dcurwin
manager: carmonm
ms.service: virtual-machines
ms.subservice: extensions
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: c6a071956565a8bbc31e5be362c41a7c39d8f551
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98738055"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Backup Azure para O Servidor SQL em execução em Azure VM

O Azure Backup, entre outras ofertas, fornece suporte para o backup de cargas de trabalho, como o SQL Server em execução em VMs Azure. Uma vez que a aplicação SQL está em execução dentro de um Azure VM, o serviço de backup precisa de permissão para aceder à aplicação e obter os detalhes necessários.
Para isso, o Azure Backup instala a extensão **AzureBackupWindowsWorkloadload** no VM, no qual o SQL Server está em execução, durante o processo de registo desencadeado pelo utilizador.

## <a name="prerequisites"></a>Pré-requisitos

Para a lista de cenários suportados, consulte a matriz de [suporte](../../backup/sql-support-matrix.md#scenario-support) suportada por Azure Backup.

## <a name="network-connectivity"></a>Conectividade de rede

O Azure Backup suporta tags NSG, implantando um servidor proxy ou intervalos IP listados; para mais informações sobre cada um dos métodos, consulte este [artigo](../../backup/backup-sql-server-database-azure-vms.md#establish-network-connectivity).

## <a name="extension-schema"></a>Esquema de extensão

O esquema de extensão e os valores de propriedade são os valores de configuração (configurações de tempo de execução) que o serviço está a passar para a API CRP. Estes valores config são utilizados durante o registo e atualização. A extensão **AzureBackupWindowsWorkload** também utiliza este esquema. O esquema é pré-definido; um novo parâmetro pode ser adicionado no campo objectStr

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

O JSON seguinte mostra o esquema para a extensão WorkloadBackup.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Valores patrimoniais

Name | Valor/exemplo | Tipo de dados
 --- | --- | ---
região | pt-pt  |  string
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | string
objetoStr <br/> (publicidadeSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzJQxYTItOGRJNy000ZGE5LWITMjdjYJHJHNDLM2ZkwisWRNZ210Q29udGFpbmVySWOjM0NTY3ODg5LCJSZXXJJJJJZUlkIMUlkIMUiMUyIMUYUW IWOGETYzI4Zi00ZmFlLWE5ODItuTWOWMYVJNJHHHIiwiU3Vic2NyaXB0aW9uSWQiOikNGEzOTliNy1iYjAyLTQ2MTMDDMYS1jNTM5ODI3ZTtNT CNLJVBldLDDb250YWLuZLJOYW1lIjoiODMDMDZJODUTTTTTT4OS00NmNHHHHHTNHDNg3OTcyInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOusIlNlcnZpY2VTdGFtCFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmuY29tIn1dfQ=" | string
commandStartTimeUTCTicks | "636967192566036845"  | string
vmType  | "microsoft.compute/virtualmachines"  | string
objetoStr <br/> (proteção de sesesagens) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzJQxYTItOGRJNy000ZGE5LWITMjdjYJHJHNDLM2ZkwisWRNZ210Q29udGFpbmVySWOjM0NTY3ODg5LCJSZXXJJJJJZUlkIMUlkIMUiMUyIMUYUW IWOGETYzI4Zi00ZmFlLWE5ODItuTWOWMYVJNJHHHIiwiU3Vic2NyaXB0aW9uSWQiOikNGEzOTliNy1iYjAyLTQ2MTMDDMYS1jNTM5ODI3ZTtNT CNLJVBldLDDb250YWLuZLJOYW1lIjoiODMDMDZJODUTTTTTT4OS00NmNHHHHHTNHDNg3OTcyInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOusIlNlcnZpY2VTdGFtCFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmuY29tIn1dfQ=" | string
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string

## <a name="template-deployment"></a>Implementação de modelos

Recomendamos que a adição da extensão AzureBackupWindowsWorkload a uma máquina virtual seja ativada através da cópia de segurança do SQL Server na máquina virtual. Isto pode ser alcançado através do [modelo de Gestor de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) projetado para automatizar backup em um SQL Server VM.

## <a name="powershell-deployment"></a>Implementação do PowerShell

É necessário 'registar' o Azure VM que contém a aplicação SQL com um cofre de serviços de Recuperação. Durante o registo, a extensão AzureBackupWindowsWorkload é instalada no VM. Utilize [o Register-AzRecoveryServicesBackupContainerPS](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) cmdlet para registar o VM.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

O comando devolverá um contentor de **reserva** deste recurso e o estado será **registado**.

## <a name="next-steps"></a>Próximos passos

- [Saiba mais](../../backup/backup-sql-server-azure-troubleshoot.md) sobre as diretrizes de resolução de problemas de backup do Azure SQL Server VM
- [Perguntas comuns](../../backup/faq-backup-sql-server.md) sobre o backup das bases de dados do SQL Server que funcionam em máquinas virtuais Azure (VMs) e que utilizam o serviço de Backup Azure.
