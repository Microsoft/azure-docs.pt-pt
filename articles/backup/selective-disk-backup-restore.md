---
title: Backup seletivo de disco e restauro para máquinas virtuais Azure
description: Neste artigo, aprenda sobre a cópia de segurança seletiva do disco e restaure utilizando a solução de backup da máquina virtual Azure.
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions
ms.openlocfilehash: 1052e7e531f6762de660ba89e22c7fbb0d01f808
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92628767"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Backup seletivo de disco e restauro para máquinas virtuais Azure

O Azure Backup suporta o backup de todos os discos (sistema operativo e dados) num VM em conjunto utilizando a solução de backup da máquina virtual. Agora, usando a cópia de segurança dos discos seletivos e restaurar a funcionalidade, pode fazer backup de um subconjunto dos discos de dados num VM. Isto fornece uma solução eficiente e rentável para as suas necessidades de backup e restauro. Cada ponto de recuperação contém apenas os discos incluídos na operação de backup. Isto permite-lhe ainda ter um subconjunto de discos restaurados a partir do ponto de recuperação dado durante a operação de restauro. Isto aplica-se tanto ao restauro a partir de instantâneos como ao cofre.

## <a name="scenarios"></a>Cenários

Esta solução é útil particularmente nos seguintes cenários:

1. Se tiver dados críticos para ser apoiado em apenas um disco, ou um subconjunto dos discos e não quiser fazer cópias de segurança do resto dos discos ligados a um VM para minimizar os custos de armazenamento de backup.
2. Se tiver outras soluções de backup para uma parte do seu VM ou dados. Por exemplo, se fizer o backup das suas bases de dados ou dados utilizando uma solução de backup de carga de trabalho diferente e pretender utilizar a cópia de segurança de nível Azure VM para o resto dos dados ou discos para construir um sistema eficiente e robusto utilizando as melhores capacidades disponíveis.

Utilizando o PowerShell ou o Azure CLI, pode configurar a cópia de segurança seletiva do Azure VM.  Utilizando um script, pode incluir ou excluir discos de dados utilizando os seus números LUN.  Atualmente, a capacidade de configurar a cópia de segurança dos discos seletivos através do portal Azure está limitada à opção **de backup OS Disk.** Assim, pode configurar a cópia de segurança do seu VM Azure com o disco OS e excluir todos os discos de dados que lhe estão ligados.

>[!NOTE]
> O disco DE é adicionado por defeito à cópia de segurança VM e não pode ser excluído.

## <a name="using-azure-cli"></a>Utilizar a CLI do Azure

Certifique-se de que está a utilizar a versão 2.0.80 ou superior do Az CLI. Pode obter a versão CLI com este comando:

```azurecli
az --version
```

Inscreva-se no ID de assinatura onde existe o cofre dos Serviços de Recuperação e o VM:

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>Apenas o nome do **grupo de recursos** (não o objeto) correspondente ao cofre é necessário em cada comando abaixo.

### <a name="configure-backup-with-azure-cli"></a>Configure backup com Azure CLI

Durante a operação de proteção de configuração, é **inclusion** necessário especificar a definição da lista de discos com um parâmetro de  /  **exclusão** de inclusão, dando aos números LUN dos discos a incluir ou excluídos na cópia de segurança.

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

Se o VM não estiver no mesmo grupo de recursos que o cofre, então o **ResourceGroup** refere-se ao grupo de recursos onde o cofre foi criado. Em vez do nome VM, forneça o ID VM como indicado abaixo.

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id --output tsv) --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>Modifique a proteção para VMs já apoiados com Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Backup apenas disco DE DURANTE Configurar backup com Azure CLI

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Backup apenas disco DE DURANTE A modificação de proteção com Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Restaurar discos com Azure CLI

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Restaurar apenas o disco DE com Azure CLI

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Obtenha um item protegido para obter detalhes de exclusão de disco com Azure CLI

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Há um parâmetro adicional de **disclusionExclusionProperties** adicionado ao item protegido, como mostrado abaixo:

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Conseguir trabalho de reserva com a Azure CLI

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

Este comando ajuda a obter os detalhes dos discos retrossativos e discos excluídos, como mostrado abaixo:

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>Listar pontos de recuperação com Azure CLI

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

Isto dá a informação do número de discos ligados e apoiados no VM.

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Obtenha o ponto de recuperação com O Azure CLI

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

Cada ponto de recuperação tem a informação dos discos incluídos e excluídos:

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Remova as definições de exclusão do disco e obtenha um item protegido com O Azure CLI

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Quando executar estes comandos, `"diskExclusionProperties": null` verá.

## <a name="using-powershell"></a>Com o PowerShell

Certifique-se de que está a utilizar a versão 3.7.0 ou superior do Azure PowerShell.

### <a name="enable-backup-with-powershell"></a>Ativar backup com PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>Backup apenas disco DE DURANTE Configurar backup com PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>Obtenha objeto de item de backup para ser passado em proteção de modificação com PowerShell

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $Vault.ID -FriendlyName "V2VM"
```

Tem de passar o objeto **$item** obtido acima para o parâmetro **-Item** nos cmdlets seguintes.

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>Modifique a proteção para VMs já apoiados com PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>Backup apenas disco DE DURANTE Modificar proteção com PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>Redefinir a definição de exclusão do disco com o PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ResetExclusionSettings -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>Restaurar discos seletivos com PowerShell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [Strings]
```

### <a name="restore-only-os-disk-with-powershell"></a>Restaurar apenas o disco DE com PowerShell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

Utilizando o portal Azure, pode ver os discos incluídos e excluídos do painel de detalhes de backup VM e o painel de detalhes do trabalho de backup.  Durante a restauração, quando selecionar o ponto de recuperação para restaurar, pode visualizar os discos retrossacos nesse ponto de recuperação.

Aqui pode ver os discos incluídos e excluídos para uma máquina virtual no portal a partir do painel de detalhes de backup VM:

![Ver discos incluídos e excluídos do painel de detalhes de backup](./media/selective-disk-backup-restore/backup-details.png)

Aqui pode ver os discos incluídos e excluídos numa cópia de segurança do painel de detalhes do trabalho:

![Ver discos incluídos e excluídos do painel de detalhes do trabalho](./media/selective-disk-backup-restore/job-details.png)

Aqui pode visualizar os discos retrossativo durante a restauração, quando selecionar o ponto de recuperação para restaurar a partir de:

![Ver discos com apoio durante a restauração](./media/selective-disk-backup-restore/during-restore.png)

Configurar a experiência de backup de discos seletivos para um VM através do portal Azure está limitada à opção **de backup OS Disk.** Para utilizar a cópia de segurança seletiva em um VM já apoiado ou para inclusão avançada ou exclusão de discos de dados específicos de um VM, utilize o PowerShell ou o Azure CLI.

>[!NOTE]
>Se os dados se estenderem por discos, certifique-se de que todos os discos dependentes estão incluídos na cópia de segurança. Se não fizer cópia de segurança de todos os discos dependentes num volume, durante a restauração o volume composto por alguns discos não apoiados não será criado.

### <a name="backup-os-disk-only-in-the-azure-portal"></a>Disco de backup OS apenas no portal Azure

Quando ativa a cópia de segurança utilizando o portal Azure, pode escolher a opção **"Backup OS Disk".** Assim, pode configurar a cópia de segurança do seu VM Azure com o disco OS e excluir todos os discos de dados ligados ao mesmo.

![Configurar backup apenas para o disco OS](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="using-azure-rest-api"></a>Usando Azure REST API

Pode configurar a Cópia de Segurança VM do Azure com alguns discos selecionados ou pode modificar a proteção de um VM existente para incluir/excluir alguns discos como documentado [aqui.](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)

## <a name="selective-disk-restore"></a>Restauração seletiva do disco

A restauração seletiva do disco é uma funcionalidade adicional que obtém quando ativa a funcionalidade de backup dos discos seletivos. Com esta funcionalidade, pode restaurar discos seletivos de todos os discos apoiados num ponto de recuperação. É mais eficiente e ajuda a poupar tempo em cenários onde sabe qual dos discos precisa de ser restaurado.

- O disco SO está incluído por padrão na cópia de segurança e restauro do VM, e não pode ser excluído.
- A restauração seletiva do disco é suportada apenas para pontos de recuperação criados após a capacidade de exclusão do disco estar ativada.
- As cópias de segurança com o disco excluem a definição **ON** suportam apenas a opção de restauro do **disco.** As opções de **restauro vM** ou **substituição das** opções de restauro existentes não são suportadas neste caso.

![A opção de restaurar o VM e substituir os existentes não está disponível durante a operação de restauro](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>Limitações

A funcionalidade de backup de discos seletivos não é suportada para máquinas virtuais clássicas e máquinas virtuais encriptadas. Assim, os VMs Azure que são encriptados com encriptação de disco Azure (ADE) usando BitLocker para encriptação do Windows VM, e a funcionalidade dm-crypt para Linux VMs não são suportados.

As opções de restauro para **criar novos VM** e **Substituir os existentes** não são suportadas para o VM para o qual a funcionalidade de backup de discos seletivos está ativada.

Atualmente, a cópia de segurança Azure VM não suporta VMs com ultra-discos ou discos partilhados ligados a eles. A cópia de segurança seletiva do disco não pode ser usada nestes casos, que excluem o disco e fazem backup do VM.

## <a name="billing"></a>Faturação

A cópia de segurança da máquina virtual Azure segue o modelo de preços existente, explicado em detalhe [aqui.](https://azure.microsoft.com/pricing/details/backup/)

**O custo de Instância Protegida (PI)** é calculado apenas para o disco so se optar por fazer o back up utilizando a opção **de disco OS.**  Se configurar a cópia de segurança e selecionar pelo menos um disco de dados, o custo de PI será calculado para todos os discos ligados ao VM. **O custo de armazenamento de backup** é calculado com base apenas nos discos incluídos e assim você pode economizar no custo de armazenamento. **O custo instantâneo** é sempre calculado para todos os discos do VM (tanto os discos incluídos como os excluídos).

Se escolheu a função Cross Region Restore (CRR), então o [preço crr](https://azure.microsoft.com/pricing/details/backup/) aplica-se no custo de armazenamento de cópia de segurança após excluir o disco.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-is-protected-instance-pi-cost-calculated-for-only-os-disk-backup-in-windows-and-linux"></a>Como é calculado o custo de Instância Protegida (PI) calculado apenas para a cópia de segurança do disco oss no Windows e linux?

O custo do PI é calculado com base no tamanho real (utilizado) do VM.

- Para o Windows: O cálculo do espaço usado baseia-se na unidade que armazena o sistema operativo (que normalmente é C:).
- Para o Linux: O cálculo do espaço usado baseia-se no dispositivo onde o sistema de ficheiros raiz (/ ) é montado.

### <a name="i-have-configured-only-os-disk-backup-why-is-the-snapshot-happening-for-all-the-disks"></a>Eu tenho configurado apenas backup de disco DE, porque é que a foto está a acontecer para todos os discos?

As funcionalidades de backup seletiva do disco permitem economizar no custo de armazenamento do cofre de reserva, endurecendo os discos incluídos que fazem parte da cópia de segurança. No entanto, a fotografia é tirada para todos os discos que estão ligados ao VM. Assim, o custo do instantâneo é sempre calculado para todos os discos do VM (ambos os discos incluídos e excluídos). Para mais informações, consulte [a faturação.](#billing)

### <a name="i-cant-configure-backup-for-the-azure-virtual-machine-by-excluding-ultra-disk-or-shared-disks-attached-to-the-vm"></a>Não posso configurar a cópia de segurança para a máquina virtual Azure, excluindo discos ultra-discos ou discos partilhados ligados ao VM

A funcionalidade de backup de disco seletiva é uma capacidade fornecida em cima da solução de backup da máquina virtual Azure. Atualmente, a cópia de segurança Azure VM não suporta VMs com disco ultra-disco ou disco partilhado ligado a eles.

## <a name="next-steps"></a>Passos seguintes

- [Matriz de suporte para a cópia de segurança da VM do Azure](backup-support-matrix-iaas.md)
- [Perguntas frequentes-Back up Azure VMs](backup-azure-vm-backup-faq.md)
