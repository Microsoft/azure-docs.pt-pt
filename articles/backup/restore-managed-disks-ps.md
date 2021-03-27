---
title: Restaurar Discos Geridos Azure via Azure PowerShell
description: Saiba como restaurar discos geridos Azure usando a Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 0ddf552947c39692ea01d0dea7e67f147d754fcc
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630446"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Restaurar discos geridos Azure usando Azure PowerShell

Este artigo explica como restaurar os [Discos Geridos Azure](../virtual-machines/managed-disks-overview.md) a partir de um ponto de restauração criado pela Azure Backup.

Atualmente, a opção de recuperação de Original-Location (OLR) de restauro substituindo o disco de origem existente de onde as cópias de segurança foram tomadas não é suportada. Pode restaurar a partir de um ponto de recuperação para criar um novo disco no mesmo grupo de recursos que o do disco de origem de onde as cópias de segurança foram tomadas ou em qualquer outro grupo de recursos. Isto é conhecido como Alternate-Location Recovery (ALR) e isto ajuda a manter tanto o disco de origem como o disco restaurado (novo).

Neste artigo, vai aprender a:

- Restaurar para criar um novo disco

- Acompanhe o estado da operação de restauro

Vamos referir-nos a um cofre de backup existente "TestBkpVault" sob o grupo de recursos "testBkpVaultRG" nos exemplos

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Restaurar para criar um novo disco

### <a name="setting-up-permissions"></a>Criação de permissões

Backup Vault usa Identidade Gerida para aceder a outros recursos Azure. Para restaurar a partir de cópia de segurança, a identidade gerida do cofre de backup requer um conjunto de permissões no grupo de recursos onde o disco deve ser restaurado.

O cofre de backup utiliza um sistema atribuído à identidade gerida, que é restrito a um por recurso e está ligado ao ciclo de vida deste recurso. Pode conceder permissões à identidade gerida utilizando o controlo de acesso baseado em funções Azure (Azure RBAC). A identidade gerida é um principal de serviço de um tipo especial que só pode ser usado com recursos Azure. Saiba mais sobre [identidades geridas.](../active-directory/managed-identities-azure-resources/overview.md)

Atribua as permissões relevantes para o sistema de cofre atribuído identidade gerida no grupo de recursos-alvo onde os discos serão restaurados/criados como mencionado [aqui](restore-managed-disks.md#restore-to-create-a-new-disk).

### <a name="fetching-the-relevant-recovery-point"></a>Buscar o ponto de recuperação relevante

Pegue em todas as instâncias usando o comando [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) e identifique a instância relevante.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Também pode utilizar o **Az.Resourcegraph** e o comando [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) para pesquisar em muitos cofres e subscrições.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

Uma vez identificada a instância, pegue o ponto de recuperação relevante.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>Preparação do pedido de restauro

Construa o ID ARM do novo disco a criar com o grupo de recursos-alvo, ao qual foram atribuídas permissões conforme descrito [acima](#setting-up-permissions), e o nome do disco necessário. Por exemplo, um disco pode ser nomeado **PSTestDisk2** sob um **targetrg** de grupo de recursos com uma subscrição diferente.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Utilize o comando [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) para preparar o pedido de restauro com todos os detalhes relevantes.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>Desencadear a restauração

Utilize o comando [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) para ativar a restauração com o pedido acima preparado.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Trabalho de rastreio

Acompanhe todos os trabalhos utilizando o comando [Get-AzDataProtectionJob.](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) Pode listar todos os empregos e obter um determinado destacamento de emprego.

Também pode usar **o Az.ResourceGraph** para rastrear todos os trabalhos em todos os cofres de reserva. Utilize o comando [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) para obter o trabalho relevante, que pode ser através de qualquer cofre de backup.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Passos seguintes

- [BACKUP DE DISCO Azure FAQ](disk-backup-faq.md)
