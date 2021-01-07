---
title: Amostra de script PowerShell - Ressalta um VM Azure
description: Neste artigo, aprenda a usar uma amostra do Azure PowerShell Script para apoiar uma máquina virtual Azure.
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 73dc119e8db34aed04ce8926bfa85f557027c8e2
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967332"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Fazer o back up de uma máquina virtual Azure encriptada com PowerShell

Este script cria um cofre de Serviços de Recuperação com armazenamento geo-redundante (GRS) para uma máquina virtual Azure encriptada. A política de proteção por defeito é aplicada ao cofre. A política gera uma cópia de segurança diária para a máquina virtual e mantém cada backup durante 365 dias. O script também aciona o ponto de recuperação inicial da máquina virtual e mantém esse ponto de recuperação durante 30 dias.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar a implementação. Cada item na tabela liga a documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Cria um cofre dos Serviços de Recuperação para armazenar backups. |
| [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Define propriedades de armazenamento de backup no cofre dos Serviços de Recuperação. |
| [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Cria uma política de proteção utilizando a política de agendamento e a política de retenção no cofre dos Serviços de Recuperação. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Define as permissões no Key Vault para conceder ao principal do serviço acesso às chaves de encriptação. |
| [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Permite a cópia de segurança de um item com uma política de proteção de cópia de segurança especificada. |
| [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Modifica uma política de proteção de backup existente. |
| [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Inicia uma cópia de segurança para um item de backup protegido do Azure que não está ligado ao horário de reserva. |
| [Wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Espera que um trabalho de reserva do Azure termine. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/new-azureps-module-az).
