---
title: Suporte de nível de arquivo (pré-visualização)
description: Saiba mais sobre o Suporte de Nível de Arquivo para backup Azure
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 7a42b8702cfdda14a18aa3cdd4e084ed78767b0a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012153"
---
# <a name="archive-tier-support-preview"></a>Suporte de nível de arquivo (pré-visualização)

Os clientes confiam na Azure Backup para armazenar dados de backup, incluindo os seus dados de backup de retenção de Long-Term (LTR) com necessidades de retenção definidas pelas regras de conformidade da organização. Na maioria dos casos, os dados de backup mais antigos raramente são acedidos e são armazenados apenas para necessidades de conformidade.

O Azure Backup suporta o backup de pontos de retenção a longo prazo no nível de arquivo, além de instantâneos e o nível Standard.

## <a name="scope-for-preview"></a>Possibilidade de pré-visualização

Cargas de trabalho suportadas:

- Máquinas virtuais do Azure
  - Apenas pontos de recuperação mensais e anual. Os pontos de recuperação diários e semanais não são suportados.
  - Idade >= 3 meses em Vault-Standard Tier
  - Retenção deixada >= 6 meses
  - Sem dependências diárias e semanais ativas
- Servidor SQL em máquinas virtuais Azure
  - Apenas pontos de recuperação completos. Registos e diferenciais não são suportados.
  - Idade >= 45 dias em Vault-Standard Tier
  - Retenção deixada >= 6 meses
  - Sem dependências

Clientes apoiados:

- A capacidade é fornecida usando PowerShell

>[!NOTE]
>O Suporte de Nível de Arquivo para VMs Azure e SQL Server em VMs Azure está em pré-visualização pública limitada com inscrições limitadas. Para se inscrever para suporte de arquivo utilize este [link](https://aka.ms/ArchivePreviewInterestForm).

## <a name="get-started-with-powershell"></a>Introdução ao PowerShell

1. Descarregue a [versão mais recente](https://github.com/PowerShell/PowerShell/releases) do PowerShell do GitHub.

1. Execute o seguinte comando no PowerShell:
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.0.0-preview -AllowPrerelease -force
    ```

1. Ligue-se ao Azure utilizando o [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)
1. Inscreva-se na sua subscrição:

   `Set-AzContext -Subscription "SubscriptionName"`

1. Pegue o cofre:

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. Obtenha a lista de itens de reserva:

    - Para máquinas virtuais Azure:

        `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM" -WorkloadType "AzureVM"`

    - Para o SQL Server em máquinas virtuais Azure:

        `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureWorkload" -WorkloadType "MSSQL"`

1. Pegue o item de reserva.

    - Para máquinas virtuais Azure:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - Para o SQL Server em máquinas virtuais Azure:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

## <a name="use-powershell"></a>Utilizar o PowerShell

### <a name="check-archivable-recovery-points"></a>Verifique pontos de recuperação arquiváveis

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm  -IsReadyForMove $true -TargetTier VaultArchive
```

Isto irá listar todos os pontos de recuperação associados a um determinado item de backup que estão prontos para serem transferidos para arquivar.

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>Verifique por que um ponto de recuperação não pode ser movido para arquivo

```azurepowershell
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Onde `$rp[0]` está o ponto de recuperação para o qual quer verificar por que não é arquivável.

Resultado do exemplo:

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Verifique o conjunto recomendado de pontos arquiváveis (apenas para os VMs Azure)

Os pontos de recuperação associados a uma máquina virtual são incrementais na natureza. Quando um determinado ponto de recuperação é movido para o arquivo, é convertido em uma cópia de segurança completa e depois transferido para o arquivo. Assim, a poupança de custos associada à mudança para o arquivo depende da origem dos dados.

Assim, o Azure Backup criou um conjunto recomendado de pontos de recuperação que podem resultar em poupanças de custos se forem movidos juntos.

>[!NOTE]
>A poupança de custos depende de uma variedade de razões e pode não ser a mesma em duas instâncias.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>Mover-se para o arquivo

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[2] -SourceTier VaultStandard -DestinationTier VaultArchive
```

Este comando move um ponto de recuperação arquivável para arquivar. Devolve um trabalho que pode ser usado para rastrear a operação de movimento tanto do portal como com o PowerShell.

### <a name="view-archived-recovery-points"></a>Ver pontos de recuperação arquivados

Este comando devolve todos os pontos de recuperação arquivados.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive
```

### <a name="restore-with-powershell"></a>Restaurar com PowerShell

Para pontos de recuperação no arquivo, o Azure Backup fornece uma metodologia integrada de restauro.

O restauro integrado é um processo em duas etapas. O primeiro passo consiste em reidratar os pontos de recuperação armazenados em arquivo e armazená-lo temporariamente no nível padrão do cofre por uma duração (também conhecida como duração da reidratação) que varia entre um período de 10 a 30 dias. O padrão é de 15 dias. Existem duas prioridades diferentes de reidratação – Standard e Alta prioridade. Saiba mais sobre [a prioridade de reidratação.](../storage/blobs/storage-blob-rehydration.md#rehydrate-an-archived-blob-to-an-online-tier)

>[!NOTE]
>
>- A duração da reidratação uma vez selecionada não pode ser alterada e os pontos de recuperação rehidratados permanecem no nível padrão para a duração da reidratação.
>- O passo adicional de reidratação incorre no custo.

Para obter mais informações sobre os vários métodos de restauro para máquinas virtuais Azure, consulte [Restaurar um VM Azure com PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

Para restaurar o SQL Server, siga [estes passos.](backup-azure-sql-automation.md#restore-sql-dbs) Os parâmetros adicionais necessários são **RehidrataçãoPrioridade** e **ReidrataçãoDuration**.

### <a name="view-jobs-from-powershell"></a>Ver empregos da PowerShell

Para visualizar o movimento e restaurar os postos de trabalho, utilize o seguinte cmdlet PowerShell:

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>Utilizar o portal

### <a name="check-archived-recovery-point"></a>Verifique o ponto de recuperação arquivado

Agora pode ver todos os pontos de recuperação que foram transferidos para o arquivo.

![Todos os pontos de recuperação.](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>Restaurar no portal

Para os pontos de recuperação que foram movidos para o arquivo, a restauração requer que adicione os parâmetros para a duração da reidratação e prioridade de reidratação.

![Restaurar no portal.](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>Ver empregos no portal

![Ver empregos no portal.](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>Modificar a proteção

Existem duas formas de modificar a proteção para um dadosource:

- Modificar uma política existente
- Proteger a fonte de dados com uma nova política

Em ambos os casos, a nova política é aplicada a todos os pontos de recuperação mais antigos, que se encontram no nível normal, e aos do nível de arquivo. Assim, os pontos de recuperação mais antigos podem ser apagados se houver uma mudança na política.

Quando os pontos de recuperação são transferidos para o arquivo, são submetidos a um período de eliminação precoce de 180 dias. As acusações são apresentadas. Se um ponto de recuperação que não tenha permanecido no arquivo durante 180 dias for eliminado, incorrerá num custo equivalente a 180 menos o número de dias que passou no nível padrão.

Os pontos de recuperação que não tenham permanecido arquivados há um mínimo de seis meses incorrerão em custos de supressão precoce na supressão.

## <a name="stop-protection-and-delete-data"></a>Parar a proteção e apagar dados

Parar a proteção e eliminar os dados elimina todos os pontos de recuperação. Para pontos de recuperação em arquivo que não tenham permanecido por uma duração de 180 dias no nível de arquivo, a supressão de pontos de recuperação levará a custos de eliminação precoce.

## <a name="error-codes-and-troubleshooting-steps"></a>Códigos de erro e etapas de resolução de problemas

Existem vários códigos de erro que surgem quando um ponto de recuperação não pode ser movido para arquivar.

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Error Message** - Recovery-Point Type não é elegível para Archive Move

**Descrição** – Este código de erro é mostrado quando o tipo de ponto de recuperação selecionado não é elegível para ser transferido para o arquivo.

**Ação recomendada** - Verifique a elegibilidade do ponto de recuperação [aqui](#scope-for-preview)

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Error Message** - Recovery-Point ter dependências ativas para restauro não é elegível para Archive Move

**Descrição -** O ponto de recuperação selecionado tem dependências ativas e por isso não pode ser movido para o arquivo.

**Ação recomendada** - Verifique a elegibilidade do ponto de recuperação [aqui](#scope-for-preview)

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**Error Message** - Recovery-Point não é elegível para Archive Move, uma vez que o tempo de vida gasto em Vault-Standard-Tier é inferior ao mínimo exigido

**Descrição** – O ponto de recuperação tem de permanecer no nível Standard durante um mínimo de três meses para máquinas virtuais Azure, e 45 dias para o SQL Server em máquinas virtuais Azure

**Ação recomendada** - Verifique a elegibilidade do ponto de recuperação [aqui](#scope-for-preview)

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Error Message** - Recovery-Point tempo de vida remanescente é inferior ao mínimo exigido.

**Descrição** – O tempo de vida mínimo necessário para um ponto de recuperação para a elegibilidade do movimento de arquivo é de seis meses.

**Ação recomendada** - Verifique a elegibilidade do ponto de recuperação [aqui](#scope-for-preview)

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Error Message** - Recovery-Point não é elegível para movimento de arquivo, uma vez que já foi movido para o nível de arquivo

**Descrição** – O ponto de recuperação selecionado já está arquivado. Então não é elegível para ser transferido para o arquivo.

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Error Message** - Datasource Type não é elegível para API de recomendação.

**Descrição** – A API de recomendação só é aplicável às máquinas virtuais Azure. Não é aplicável para o tipo de fonte de dados selecionado.

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRed

**Error Message** - Recovery Point já está rehidratado. Não é permitida a reidratação neste RP.

**Descrição** – O ponto de recuperação selecionado já está rehidratado.

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Error Message** -Recovery-Point não é elegível para o Movimento de Arquivo.

**Descrição** – O ponto de recuperação selecionado não é elegível para movimento de arquivo.

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRedado

**Error** **message** - Archive Recovery Point não é rehidratado. Ret reitor Restaurar após a reidratação concluída no Archive RP.

**Descrição** – O ponto de recuperação não é rehidratado. Tente restaurar depois de hidratar o ponto de recuperação.

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**Error** **message**- A reidratação só é suportada para Pontos de Recuperação de Arquivo- A reidratação só é suportada para Pontos de Recuperação de Arquivos

**Descrição** – A reidratação não é permitida para o ponto de recuperação selecionado.

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**Error message** – A reidratação já está In-Progress para o Ponto de Recuperação do Arquivo.

**Descrição** – A reidratação para o ponto de recuperação selecionado já está em curso.

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Error message** - O ponto de recuperação não pode ser transferido para o nível de Arquivo devido à duração de retenção insuficiente especificada na política

**Ação Recomendada** - Atualize a política sobre o item protegido com uma definição de retenção adequada e tente novamente.

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToDeterminado

**Error message** - Ainda estamos a determinar se este Ponto de Recuperação pode ser movido.

**Descrição** – A prontidão do movimento do ponto de recuperação ainda está por determinar.

**Ação Recomendada** - Verifique novamente depois de esperar por algum tempo.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>O que acontecerá com os pontos de recuperação de arquivo se eu parar a proteção e reter dados?

O ponto de recuperação permanecerá arquivado para sempre. Para obter mais informações, consulte [o Impacto da proteção contra paragem nos pontos de recuperação](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points).

## <a name="next-steps"></a>Passos seguintes

- [Preços do Azure Backup](azure-backup-pricing.md)