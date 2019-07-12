---
title: Cópia de segurança do Azure para SQL Server em execução na VM do Azure
description: Como registar o servidor de SQL de cópia de segurança do Azure em execução na VM do Azure
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: swatisachdeva
ms.openlocfilehash: 8710242e04156c8af6e5882a3cb4d42cc31e3677
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607614"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Cópia de segurança do Azure para SQL Server em execução na VM do Azure

Cópia de segurança do Azure, entre outras ofertas fornece suporte para criar cópias de segurança de cargas de trabalho como o SQL Server em execução em VMs do Azure. Uma vez que o aplicativo de SQL está em execução dentro de uma VM do Azure, o serviço de cópia de segurança tem permissão para aceder à aplicação e obter os detalhes necessários.
Para tal, o Azure Backup instala o **AzureBackupWindowsWorkload** extensão na VM, na qual o SQL Server está em execução, durante o processo de registo disparado pelo usuário.

## <a name="prerequisites"></a>Pré-requisitos

Para obter a lista de cenários suportados, consulte a [matriz de Suportabilidade](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support) suportadas pelo Azure Backup.

## <a name="network-connectivity"></a>Conectividade de rede

Cópia de segurança do Azure suporta etiquetas de NSG, implementar um servidor proxy ou listados intervalos IP; Para obter detalhes sobre cada um dos métodos, consulte [artigo](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Esquema de extensão

Os valores de esquema e a propriedade de extensão são os valores de configuração (definições de tempo de execução) que o serviço consiste na passagem para a API de CRP. Estes valores de configuração são utilizados durante o registo e a atualização. **AzureBackupWindowsWorkload** extensão também utilizam este esquema. O esquema previamente é definido; pode ser adicionado um novo parâmetro no campo objectStr

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

O JSON seguinte mostra o esquema para a extensão de WorkloadBackup.  

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

### <a name="property-values"></a>Valores de propriedade

Nome | Valor/exemplo | Tipo de dados
 --- | --- | ---
Localidade | en-us  |  cadeia
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | Cadeia de caracteres
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | cadeia
commandStartTimeUTCTicks | "636967192566036845"  | cadeia
vmType  | "microsoft.compute/virtualmachines"  | Cadeia de caracteres
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | Cadeia de caracteres
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | Cadeia de caracteres
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | cadeia


## <a name="template-deployment"></a>Implementação de modelos

Recomendamos adicionar a extensão de AzureBackupWindowsWorkload a uma máquina virtual é ao ativar a cópia de segurança do SQL Server na máquina virtual. Isso pode ser alcançado através da [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) concebido para automatizar a cópia de segurança numa VM do SQL Server.


## <a name="powershell-deployment"></a>Implementação do PowerShell

Tem de "Registar" a VM do Azure que contém a aplicação de SQL com um cofre dos serviços de recuperação. Durante o registo, a extensão de AzureBackupWindowsWorkload é instalada na VM. Uso [Register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet para registar a VM.
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
O comando irá devolver uma **contentor de cópia de segurança** isso recursos e o estado será **registado**.


## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) sobre a cópia de segurança de VM do Azure SQL Server diretrizes de resolução de problemas
- [Perguntas comuns](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) sobre como fazer backup de bancos de dados do SQL Server que execute em máquinas virtuais do Azure (VMs) e que utilizam o serviço de cópia de segurança do Azure.
