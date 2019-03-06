---
title: Exemplo de Script do PowerShell do Azure - fazer cópia de segurança uma máquina virtual do Azure | Documentos da Microsoft
description: Exemplo de Script do PowerShell do Azure - fazer cópia de segurança uma máquina virtual do Azure
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3eeaf2491a9d5ee1067fca1cd80a0abdd686ea48
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409147"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Criar cópias de segurança encriptada máquinas virtuais do Azure com o PowerShell

Este script cria um cofre dos serviços de recuperação com armazenamento georredundante (GRS) para uma máquina virtual do Azure encriptada. A política de proteção predefinida é aplicada ao cofre. A política gera um backup diário para a máquina virtual e retém cada cópia de segurança durante 30 dias. O script também aciona o ponto de recuperação inicial da máquina virtual e mantém esse ponto de recuperação durante 365 dias.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

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
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | Cria um cofre dos serviços de recuperação para armazenar cópias de segurança. |
| [Set-AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | Conjuntos de cópia de segurança propriedades de armazenamento do cofre dos serviços de recuperação. |
| [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| Cria uma política de proteção através da política de agendamento e a política de retenção no cofre dos serviços de recuperação. |
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Define as permissões no Key Vault para conceder ao principal do serviço acesso às chaves de encriptação. |
| [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | Permite a cópia de segurança para um item com uma política de proteção de cópia de segurança especificado. |
| [Set-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| Modifica uma política de proteção de cópia de segurança existente. |
| [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | Inicia uma cópia de segurança para um item de cópia de segurança do Azure protegido não está associado ao agendamento de cópia de segurança. |
| [Wait-AzureRmRecoveryServicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | Aguarda uma tarefa de cópia de segurança do Azure concluir. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).
