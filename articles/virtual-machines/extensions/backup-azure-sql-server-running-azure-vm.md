---
title: Backup Azure para Servidor SQL em execução em VM Azure
description: Neste artigo, aprenda a registar o Azure Backup no SQL Server a funcionar numa máquina virtual Azure.
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: b17e4031edaedc6b0a63d305d20a77e5b58f91ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247389"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Backup Azure para Servidor SQL em execução em VM Azure

O Azure Backup, entre outras ofertas, fornece suporte para o backup de cargas de trabalho como o SQL Server em execução em VMs Azure. Uma vez que a aplicação SQL está a funcionar dentro de um VM Azure, o serviço de backup precisa de autorização para aceder à aplicação e recolher os detalhes necessários.
Para tal, o Azure Backup instala a extensão **AzureBackupWindowsWorkload** no VM, no qual o Servidor SQL está em execução, durante o processo de registo desencadeado pelo utilizador.

## <a name="prerequisites"></a>Pré-requisitos

Para a lista de cenários suportados, consulte a [matriz](../../backup/sql-support-matrix.md#scenario-support) de suporte suportada pelo Azure Backup.

## <a name="network-connectivity"></a>Conectividade de rede

O Azure Backup suporta tags NSG, implementando um servidor proxy ou intervalos IP listados; para mais detalhes sobre cada um dos métodos, consulte este [artigo.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity)

## <a name="extension-schema"></a>Esquema de extensão

O esquema de extensão e os valores de propriedade são os valores de configuração (definições de tempo de execução) que o serviço está a passar para a CRP API. Estes valores de config são utilizados durante o registo e a atualização. A extensão **AzureBackupWindowsWorkload** também utiliza este esquema. O esquema é pré-definido; um novo parâmetro pode ser adicionado no campo objectStr

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

O Seguinte JSON mostra o esquema para a extensão WorkloadBackup.  

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

Nome | Valor/exemplo | Tipo de dados
 --- | --- | ---
região | pt-pt  |  string
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | string
objetoStr <br/> (definições públicas)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJNDLM2ZkIwiSWRNZ210Q29DGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWOQiiJkNGEzOTliNy1iYjAyLTQ2MWMTODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS0NmNhLWEyZTctNWMzNhNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlCncnzpY2VTdGFtcFVybCI6Mh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ ==" | string
comandoStartTimeUTCTicks | "636967192566036845"  | string
vmType  | "microsoft.compute/virtualmachines"  | string
objetoStr <br/> (Definições protegidas) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJNDLM2ZkIwiSWRNZ210Q29DGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWOQiiJkNGEzOTliNy1iYjAyLTQ2MWMTODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS0NmNhLWEyZTctNWMzNhNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlCncnzpY2VTdGFtcFVybCI6Mh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ ==" | string
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string

## <a name="template-deployment"></a>Implementação de modelos

Recomendamos adicionar a extensão AzureBackupWindowsWorkload a uma máquina virtual é permitindo a cópia de segurança do Servidor SQL na máquina virtual. Isto pode ser conseguido através do [modelo de Gestor](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) de Recursos projetado para automatizar a cópia de segurança num VM do Servidor SQL.

## <a name="powershell-deployment"></a>Implementação powerShell

É necessário 'registar' o Azure VM que contém a aplicação SQL com um cofre de serviços de recuperação. Durante o registo, a extensão AzureBackupWindowsWorkload é instalada no VM. Utilize o [Register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet para registar o VM.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

O comando devolverá um recipiente de **reserva** deste recurso e o estado será **registado**.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) sobre as diretrizes de resolução de problemas de backup do Servidor Azure SQL VM
- [Perguntas comuns](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) sobre o backup das bases de dados do SQL Server que funcionam em máquinas virtuais Azure (VMs) e que utilizam o serviço de backup Azure.
