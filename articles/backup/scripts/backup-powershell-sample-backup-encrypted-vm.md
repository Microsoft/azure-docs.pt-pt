---
title: Exemplo de script Azure PowerShell – fazer backup de uma VM do Azure
description: Exemplo de script Azure PowerShell – fazer backup de uma máquina virtual do Azure
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 03/05/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 1e5e705d7e9ca6587c9836603db05978d7f04d4d
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968819"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Fazer backup de uma máquina virtual do Azure criptografada com o PowerShell

Esse script cria um cofre dos serviços de recuperação com armazenamento com redundância geográfica (GRS) para uma máquina virtual do Azure criptografada. A política de proteção padrão é aplicada ao cofre. A política gera um backup diário para a máquina virtual e retém cada backup por 30 dias. O script também dispara o ponto de recuperação inicial para a máquina virtual e retém esse ponto de recuperação por 365 dias.

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
| [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Cria um cofre dos serviços de recuperação para armazenar backups. |
| [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Define as propriedades de armazenamento de backup no cofre dos serviços de recuperação. |
| [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Cria a política de proteção usando a política de agendamento e a política de retenção no cofre dos serviços de recuperação. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Define as permissões no Key Vault para conceder ao principal do serviço acesso às chaves de encriptação. |
| [Habilitar-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Habilita o backup de um item com uma política de proteção de backup especificada. |
| [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Modifica uma política de proteção de backup existente. |
| [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Inicia um backup para um item de backup do Azure protegido que não está vinculado ao agendamento de backup. |
| [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Aguarda a conclusão de um trabalho de backup do Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).
