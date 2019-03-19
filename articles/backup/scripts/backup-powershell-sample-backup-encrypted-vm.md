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
ms.openlocfilehash: 38460fc1dee3ea690b5c291f43fadf2fa9e511c6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886353"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Criar cópias de segurança encriptada máquinas virtuais do Azure com o PowerShell

Este script cria um cofre dos serviços de recuperação com armazenamento georredundante (GRS) para uma máquina virtual do Azure encriptada. A política de proteção predefinida é aplicada ao cofre. A política gera um backup diário para a máquina virtual e retém cada cópia de segurança durante 30 dias. O script também aciona o ponto de recuperação inicial da máquina virtual e mantém esse ponto de recuperação durante 365 dias.

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
| [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvaul) | Cria um cofre dos serviços de recuperação para armazenar cópias de segurança. | 
| [Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties) | Conjuntos de cópia de segurança propriedades de armazenamento do cofre dos serviços de recuperação. | 
| [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Cria uma política de proteção através da política de agendamento e a política de retenção no cofre dos serviços de recuperação. | 
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Define as permissões no Key Vault para conceder ao principal do serviço acesso às chaves de encriptação. | 
| [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Permite a cópia de segurança para um item com uma política de proteção de cópia de segurança especificado. | 
| [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Modifica uma política de proteção de cópia de segurança existente. | 
| [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Inicia uma cópia de segurança para um item de cópia de segurança do Azure protegido não está associado ao agendamento de cópia de segurança. |
| [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Aguarda uma tarefa de cópia de segurança do Azure concluir. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. | 

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

