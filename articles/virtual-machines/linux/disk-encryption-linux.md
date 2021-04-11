---
title: Cenários do Azure Disk Encryption em VMs com Linux
description: Este artigo fornece instruções para permitir a Encriptação do Disco Azure da Microsoft para os VMs Linux para vários cenários
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 5c131f64c540c14830e13619e0e832bbe67b675e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443906"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Cenários do Azure Disk Encryption em VMs com Linux

A encriptação do disco Azure para máquinas virtuais Linux (VMs) utiliza a funcionalidade DM-Crypt do Linux para fornecer encriptação completa do disco de segurança e discos de dados. Além disso, fornece encriptação do disco temporário ao utilizar a função EncryptFormatAll.

A Azure Disk Encryption está [integrada com o Azure Key Vault](disk-encryption-key-vault.md) para ajudá-lo a controlar e gerir as chaves e segredos de encriptação do disco. Para uma visão geral do serviço, consulte [a encriptação do disco Azure para os VMs Linux](disk-encryption-overview.md).

Só é possível aplicar encriptação de discos a máquinas virtuais de [tamanhos e sistemas operativos VM suportados.](disk-encryption-overview.md#supported-vms-and-operating-systems) Deve também cumprir os seguintes pré-requisitos:

- [Requisitos adicionais para os VM](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de rede](disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chaves de encriptação](disk-encryption-overview.md#encryption-key-storage-requirements)

Em todos os casos, deve [tirar uma fotografia](snapshot-copy-managed-disk.md) e/ou criar uma cópia de segurança antes de os discos serem encriptados. As cópias de segurança garantem que uma opção de recuperação é possível se ocorrer uma falha inesperada durante a encriptação. VMs com discos geridos requerem uma cópia de segurança antes da encriptação ocorrer. Uma vez feita uma cópia de segurança, pode utilizar o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para encriptar os discos geridos especificando o parâmetro -skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs encriptados, consulte o artigo [de Backup Azure.](../../backup/backup-azure-vms-encryption.md) 

>[!WARNING]
> - Se já utilizou a Encriptação do Disco Azure com AZure AD para encriptar um VM, deve continuar a utilizar esta opção para encriptar o seu VM. Consulte [a encriptação do disco Azure com Azure AD (versão anterior)](disk-encryption-overview-aad.md) para obter mais detalhes. 
>
> - Ao encriptar os volumes linux OS, o VM deve ser considerado indisponível. Recomendamos vivamente evitar logins SSH enquanto a encriptação está em andamento para evitar problemas que bloqueiem quaisquer ficheiros abertos que precisem de ser acedidos durante o processo de encriptação. Para verificar o progresso, utilize o [comandante Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell ou o comando CLI [do programa de encriptação vm.](/cli/azure/vm/encryption#az-vm-encryption-show) Espera-se que este processo despente-se em algumas horas para um volume de SO de 30GB, mais tempo adicional para encriptar volumes de dados. O tempo de encriptação do volume de dados será proporcional ao tamanho e quantidade dos volumes de dados, a menos que o formato de encriptação seja utilizado. 
> - A encriptação incapacitante nos VMs Linux é suportada apenas para volumes de dados. Não é suportado em dados ou volumes de SO se o volume de SO tiver sido encriptado.  

## <a name="install-tools-and-connect-to-azure"></a>Instale ferramentas e ligue-se ao Azure

A encriptação do disco Azure pode ser ativada e gerida através do [Azure CLI](/cli/azure) e [da Azure PowerShell](/powershell/azure/new-azureps-module-az). Para tal, tem de instalar as ferramentas localmente e ligar-se à sua subscrição Azure.

### <a name="azure-cli"></a>CLI do Azure

O [Azure CLI 2.0](/cli/azure) é uma ferramenta de linha de comando para gerir os recursos da Azure. O CLI foi concebido para consultar os dados de forma flexível, apoiar operações de longo prazo como processos de não bloqueio e facilitar a scripting. Pode instalá-lo localmente seguindo os passos na [Instalação do Azure CLI](/cli/azure/install-azure-cli).

 

Para [iniciar sessão na sua conta Azure com o Azure CLI,](/cli/azure/authenticate-azure-cli)utilize o comando [de login az.](/cli/azure/reference-index#az_login)

```azurecli
az login
```

Se você gostaria de selecionar um inquilino para iniciar sôm, use:
    
```azurecli
az login --tenant <tenant>
```

Se tiver várias subscrições e quiser especificar uma específica, obtenha a sua lista de subscrições com [a lista de conta az](/cli/azure/account#az-account-list) e especifique com a conta [az definida](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Para mais informações, consulte [Começar com a Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
O [módulo Azure PowerShell az](/powershell/azure/new-azureps-module-az) fornece um conjunto de cmdlets que utiliza o modelo [Azure Resource Manager](../../azure-resource-manager/management/overview.md) para gerir os seus recursos Azure. Pode usá-lo no seu navegador com [a Azure Cloud Shell,](../../cloud-shell/overview.md)ou pode instalá-lo na sua máquina local utilizando as instruções no [Install the Azure PowerShell module](/powershell/azure/install-az-ps). 

Se já o tiver instalado localmente, certifique-se de que utiliza a versão mais recente da versão Azure PowerShell SDK para configurar a Encriptação do Disco Azure. Descarregue a versão mais recente do lançamento do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Para [iniciar súm na sua conta Azure com a Azure PowerShell,](/powershell/azure/authenticate-azureps)utilize o [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

```powershell
Connect-AzAccount
```

Se tiver várias subscrições e quiser especificar uma, utilize o cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) para listá-las, seguida da cmdlet [Set-AzContext:](/powershell/module/az.accounts/set-azcontext)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Executar o [cmdlet Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verificará se a subscrição correta foi selecionada.

Para confirmar a instalação dos cmdlets de encriptação do disco Azure, utilize o cmdlet [do comando Get-command:](/powershell/module/microsoft.powershell.core/get-command)
     
```powershell
Get-command *diskencryption*
```
Para obter mais informações, consulte [Começar com a Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Ativar a encriptação num VM Linux existente ou em execução
Neste cenário, pode ativar a encriptação utilizando o modelo de Gestor de Recursos, cmdlets PowerShell ou comandos CLI. Se precisar de informações sobre esquemas para a extensão da máquina virtual, consulte a Encriptação do Disco Azure para o artigo [de extensão Linux.](../extensions/azure-disk-enc-linux.md)

>[!IMPORTANT]
 >É obrigatório fazer instantâneo e/ou backup de uma instância VM baseada em disco gerido fora de, e antes de permitir a encriptação do disco Azure. Uma imagem do disco gerido pode ser tirada do portal, ou através da Cópia de [Segurança Azure](../../backup/backup-azure-vms-encryption.md). As cópias de segurança garantem que uma opção de recuperação é possível em caso de falha inesperada durante a encriptação. Uma vez feita uma cópia de segurança, o Set-AzVMDiskEncryptionExtension cmdlet pode ser usado para encriptar discos geridos especificando o parâmetro -skipVmBackup. O comando Set-AzVMDiskEncryptionExtension falhará contra VMs baseados em disco gerido até que uma cópia de segurança seja feita e este parâmetro tenha sido especificado. 
>
>Encriptar ou desativar a encriptação pode fazer com que o VM reinicie. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Ativar a encriptação num VM Linux existente ou em execução utilizando o Azure CLI 

Pode ativar a encriptação do disco no seu VHD encriptado instalando e utilizando a ferramenta de linha de comando [Azure CLI.](/cli/azure/) Pode utilizá-lo no seu browser com o [Azure Cloud Shell](../../cloud-shell/overview.md) ou pode instalá-lo no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Para permitir a encriptação em VMs Linux existentes ou em execução em Azure, utilize os seguintes comandos CLI:

Utilize a [encriptação az vm ativar](/cli/azure/vm/encryption#az_vm_encryption_show) o comando para permitir a encriptação numa máquina virtual em execução em Azure.

- **Criptografe um VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Criptografe um VM em execução utilizando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro de chave-chave de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
A sintaxe para o valor do parâmetro chave-chave de encriptação é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um VM, utilize o comando [do programa de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desativar a encriptação:** Para desativar a encriptação, utilize o comando [de desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) A encriptação incapacitante só é permitida em volumes de dados para Os VMs Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Ativar a encriptação num VM Linux existente ou em execução utilizando o PowerShell
Utilize o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para ativar a encriptação numa máquina virtual em funcionamento em Azure. Faça uma [snapshot](snapshot-copy-managed-disk.md) e/ou faça uma cópia de segurança do VM com [a Cópia de Segurança Azure](../../backup/backup-azure-vms-encryption.md) antes de os discos serem encriptados. O parâmetro -skipVmBackup já está especificado nos scripts PowerShell para encriptar um Linux VM em execução.

-  **Criptografe um VM em execução:** O script abaixo inicializa as suas variáveis e executa o Set-AzVMDiskEncryptionExtension cmdlet. O grupo de recursos, VM, e cofre chave, foram criados como pré-requisitos. Substitua o MyVirtualMachineResourceGroup, MySecureVM e MySecureVault com os seus valores. Modifique o parâmetro -VolumeType para especificar quais os discos que está a encriptar.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Criptografe um VM em execução utilizando KEK:** Pode ter de adicionar o parâmetro -VolumeType se estiver a encriptar discos de dados e não o disco DE. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro de chave-chave de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro chave-chave de encriptação é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um VM, utilize o [cmdlet Get-AzVmDiskEncrypationStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Desativar a encriptação do disco:** Para desativar a encriptação, utilize o [cmdlet de desencriptação Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) A encriptação incapacitante só é permitida em volumes de dados para Os VMs Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Ativar a encriptação num VM Linux existente ou em execução com um modelo

Pode ativar a encriptação do disco num VM Linux existente ou em execução em Azure utilizando o [modelo de Gestor de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Clique **em Implementar para Azure** no modelo de arranque rápido Azure.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, termos legais e acordo. Clique em **Criar** para ativar a encriptação no VM existente ou em execução.

A tabela que se segue lista os parâmetros do modelo do Gestor de Recursos para os VM existentes ou em execução:

| Parâmetro | Descrição |
| --- | --- |
| vmName | Nome do VM para executar a operação de encriptação. |
| keyVaultName | Nome do cofre chave para o que a chave de encriptação deve ser carregada. Pode obtê-lo utilizando o cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` ou o comando Azure CLI `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` .|
| keyVaultResourceGroup | Nome do grupo de recursos que contém o cofre da chave. |
|  keyEncryptionKeyURL | URL da chave de encriptação que é usada para encriptar a chave de encriptação. Este parâmetro é opcional se selecionar **nokek** na lista de drop-down UseExistingKek. Se selecionar **kek** na lista de drop-down UseExistingKek, deve introduzir o valor _KeyEncryptionKeyURL._ |
| volumeType | Tipo de volume em que a operação de encriptação é executada. Valores válidos são _OS,_ _Dados_ e _Todos._ 
| forceUpdateTag | Passe um valor único como um GUID sempre que a operação precisa de ser executada à força. |
| localização | Localização de todos os recursos. |

Para obter mais informações sobre a configuração do modelo de encriptação do disco Linux VM, consulte [a encriptação do disco Azure para Linux](../extensions/azure-disk-enc-linux.md).

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Use funcionalidade EncryptFormatAll para discos de dados em VMs Linux

O parâmetro **EncryptFormatAll** reduz o tempo para os discos de dados Do Linux serem encriptados. As partições que satisfazem determinados critérios serão formatadas, juntamente com os seus sistemas de ficheiros atuais, e depois remontadas de volta ao local onde estavam antes da execução do comando. Se desejar excluir um disco de dados que satisfaça os critérios, pode desmontá-lo antes de executar o comando.

 Depois de executar este comando, quaisquer unidades que foram montadas anteriormente serão formatadas, e a camada de encriptação será iniciada em cima da unidade agora vazia. Quando esta opção for selecionada, o disco temporário ligado ao VM também será encriptado. Se o disco temporário for reiniciado, será reformatado e reencrimado para o VM pela solução de encriptação do disco Azure na próxima oportunidade. Uma vez encriptado o disco de recursos, o Agente Linux do [Microsoft Azure](../extensions/agent-linux.md) não será capaz de gerir o disco de recursos e ativar o ficheiro swap, mas pode configurar manualmente o ficheiro swap.

>[!WARNING]
> O EncryptFormatAll não deve ser utilizado quando são necessários dados sobre os volumes de dados de um VM. Pode excluir os discos da encriptação desmontando-os. Primeiro deve experimentar o EncryptFormatAll primeiro num VM de teste, compreender o parâmetro de funcionalidade e a sua implicação antes de experimentá-lo no VM de produção. A opção EncryptFormatAll forma o disco de dados e todos os dados nele serão perdidos. Antes de prosseguir, verifique se os discos que pretende excluir estão devidamente desmontados. </br></br>
 >Se estiver a configurar este parâmetro enquanto atualiza as definições de encriptação, pode levar a um reboot antes da encriptação real. Neste caso, também irá querer remover o disco que não pretende formatado a partir do ficheiro fstab. Da mesma forma, deve adicionar a partição que pretende ter formatada encriptada no ficheiro fstab antes de iniciar a operação de encriptação. 

### <a name="encryptformatall-criteria"></a>EncriptaçãoTete todos os critérios
O parâmetro vai embora todas as divisórias e as encripta, desde que cumpram **todos os** critérios abaixo:
- Não é uma divisória raiz/OS/boot
- Ainda não está encriptado
- Não é um volume BEK
- Não é um volume RAID
- Não é um volume LVM
- Está montado

Criptografe os discos que compõem o volume RAID ou LVM em vez do volume RAID ou LVM.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Utilize o parâmetro EncryptFormatAll com Azure CLI
Utilize a [encriptação az vm ativar](/cli/azure/vm/encryption#az-vm-encryption-enable) o comando para permitir a encriptação numa máquina virtual em execução em Azure.

-  **Criptografe um VM em execução utilizando o EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Utilize o parâmetro EncryptFormatAll com um cmdlet PowerShell
Utilize o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) com o parâmetro EncryptFormatAll. 

**Criptografe um VM em execução utilizando o EncryptFormatAll:** Como exemplo, o script abaixo inicializa as suas variáveis e executa o Set-AzVMDiskEncryptionExtension cmdlet com o parâmetro EncryptFormatAll. O grupo de recursos, VM e cofre chave foram criados como pré-requisitos. Substitua o MyVirtualMachineResourceGroup, MySecureVM e MySecureVault com os seus valores.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "data" -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Utilize o parâmetro EncryptFormatAll com o Gestor de Volume Lógico (LVM) 
Recomendamos uma configuração LVM-on-crypt. Para todos os exemplos a seguir, substitua o caminho do dispositivo e os pontos de montagem por qualquer que seja a sua caixa de utilização. Esta configuração pode ser feita da seguinte forma:

1.  Adicione os discos de dados que compõem o VM.

1. Formato, montagem e adicione estes discos ao ficheiro fstab.

1. Escolha um padrão de partição, crie uma divisória que abra por todo o percurso e, em seguida, formate a partição. Usamos symlinks gerados pelo Azure aqui. A utilização de symlinks evita problemas relacionados com a alteração de nomes de dispositivos. Para obter mais informações, consulte o artigo ['Troubleshoot Device Names'.](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. Monte os discos:

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    Adicione ao ficheiro fstab:

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. Executar o Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet com -EncryptFormatAll para encriptar estes discos.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    Se desejar utilizar uma chave de encriptação chave (KEK), passe o URI do seu KEK e o ResourceID do seu cofre chave para os parâmetros -KeyEncrypationKeyUrl e -KeyEncryptionKeyVaultId, respectivamente:

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. Instale o LVM em cima destes novos discos. Note que as unidades encriptadas são desbloqueadas depois de o VM ter terminado o arranque. Assim, a montagem de LVM também terá de ser posteriormente adiada.

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Novos VMs criados a partir de VHD encriptados pelo cliente e chaves de encriptação
Neste cenário, pode ativar a encriptação utilizando cmdlets PowerShell ou comandos CLI. 

Utilize as instruções na encriptação do Disco Azure para preparar imagens pré-encriptadas que podem ser usadas no Azure. Após a criação da imagem, pode utilizar os passos na secção seguinte para criar um Azure VM encriptado.

* [Preparar um Linux VHD pré-encriptado](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >É obrigatório fazer instantâneo e/ou backup de uma instância VM baseada em disco gerido fora de, e antes de permitir a encriptação do disco Azure. Uma imagem do disco gerido pode ser tirada do portal, ou a Cópia de [Segurança Azure](../../backup/backup-azure-vms-encryption.md) pode ser usada. As cópias de segurança garantem que uma opção de recuperação é possível em caso de falha inesperada durante a encriptação. Uma vez feita uma cópia de segurança, o Set-AzVMDiskEncryptionExtension cmdlet pode ser usado para encriptar discos geridos especificando o parâmetro -skipVmBackup. O comando Set-AzVMDiskEncryptionExtension falhará contra VMs baseados em disco gerido até que uma cópia de segurança seja feita e este parâmetro tenha sido especificado. 
>
> Encriptar ou desativar a encriptação pode fazer com que o VM reinicie. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Use Azure PowerShell para encriptar VMs com VHDs pré-encriptados 
Pode ativar a encriptação do disco no seu VHD encriptado utilizando o [Conjunto de Cmdlet PowerShell Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). O exemplo abaixo dá-lhe alguns parâmetros comuns. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados recém-adicionado

Pode adicionar um novo disco de dados utilizando [o az vm disk attach](add-disk.md), ou através do portal [Azure](attach-disk-portal.md). Antes de conseguir encriptar, primeiro tem de montar o disco de dados recentemente anexado. Tem de solicitar a encriptação da unidade de dados, uma vez que a unidade será inutilizável enquanto a encriptação estiver em curso. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Ativar a encriptação num disco recém-adicionado com O Azure CLI

 Se o VM foi previamente encriptado com "All", então o parâmetro do tipo volume deve permanecer "All". Tudo inclui os discos de SO e de dados. Se o VM foi previamente encriptado com um tipo de volume de "SO", então o parâmetro do tipo volume deve ser alterado para "All" de modo a que tanto o SISTEMA como o novo disco de dados sejam incluídos. Se o VM foi encriptado apenas com o tipo de volume de "Dados", então pode permanecer "Dados" como demonstrado abaixo. Adicionar e anexar um novo disco de dados a um VM não é preparação suficiente para a encriptação. O disco recém-anexado também deve ser formatado e montado corretamente dentro do VM antes de permitir a encriptação. No Linux o disco deve ser montado em /etc/fstab com um [nome persistente do dispositivo de bloqueio](../troubleshooting/troubleshoot-device-names-problems.md).  

Em contraste com a sintaxe PowerShell, o CLI não requer que o utilizador forneça uma versão de sequência única ao permitir a encriptação. O CLI gera e utiliza automaticamente o seu próprio valor exclusivo de versão de sequência.

-  **Criptografe os volumes de dados de um VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Criptografe os volumes de dados de um VM em execução utilizando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco recém-adicionado com a Azure PowerShell
 Ao utilizar o PowerShell para encriptar um novo disco para o Linux, é necessário especificar uma nova versão de sequência. A versão da sequência tem de ser única. O script abaixo gera um GUID para a versão de sequência. Faça uma [snapshot](snapshot-copy-managed-disk.md) e/ou faça uma cópia de segurança do VM com [a Cópia de Segurança Azure](../../backup/backup-azure-vms-encryption.md) antes de os discos serem encriptados. O parâmetro -skipVmBackup já está especificado nos scripts PowerShell para encriptar um disco de dados recém-adicionado.
 

-  **Criptografe os volumes de dados de um VM em execução:** O script abaixo inicializa as suas variáveis e executa o Set-AzVMDiskEncryptionExtension cmdlet. O grupo de recursos, VM e cofre-chave já deveriam ter sido criados como pré-requisitos. Substitua o MyVirtualMachineResourceGroup, MySecureVM e MySecureVault com os seus valores. Valores aceitáveis para o parâmetro -VolumeType são Todos, OS e Dados. Se o VM foi previamente encriptado com um tipo de volume de "OS" ou "All", então o parâmetro -VolumeType deve ser alterado para "All" de modo a que tanto o SISTEMA como o novo disco de dados sejam incluídos.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Criptografe os volumes de dados de um VM em execução utilizando KEK:** Valores aceitáveis para o parâmetro -VolumeType são Todos, OS e Dados. Se o VM foi previamente encriptado com um tipo de volume de "OS" ou "All", então o parâmetro -VolumeType deve ser alterado para All de modo que tanto o SISTEMA como o novo disco de dados sejam incluídos.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro de chave-chave de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/grupos de recursos/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro chave-chave de encriptação é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Desativar encriptação para Os VMs Linux
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>Cenários não suportados

A Azure Disk Encryption não funciona para os seguintes cenários, funcionalidades e tecnologia Linux:

- Encriptação de VM ou VMs básicos criados através do método clássico de criação de VM.
- Desativar a encriptação numa unidade de SISTEMA ou unidade de dados de um Linux VM quando a unidade de SISTEMA estiver encriptada.
- Encriptar a unidade de SISTEMA para conjuntos de escala de máquina virtual Linux.
- Encriptar imagens personalizadas em VMs Linux.
- Integração com um sistema de gestão chave no local.
- Ficheiros Azure (sistema de ficheiros partilhados).
- Sistema de ficheiros de rede (NFS).
- Volumes dinâmicos.
- Discos efémeros de OS.
- Encriptação de sistemas de ficheiros partilhados/distribuídos como (mas não se limitando a): DFS, GFS, DRDB e CephFS.
- Mover um VM encriptado para outra subscrição ou região.
- Criar uma imagem ou instantâneo de um VM encriptado e usá-lo para implementar VMs adicionais.
- Lixeira de colisão de Kernel (kdump).
- Oracle ACFS (sistema de ficheiros de cluster ASM).
- Os discos NVMe da série Lsv2 VMs (ver: [série Lsv2](../lsv2-series.md)).
- Um VM com "pontos de montagem aninhados"; ou seja, múltiplos pontos de montagem num único caminho (como "/1stmountpoint/data/2stmountpoint").
- Um VM com uma unidade de dados montada em cima de uma pasta de SO.
- Um VM no qual um volume lógico raiz (disco de OS) foi estendido usando um disco de dados.
- VMs da série M com discos de acelerador de escrita.
- Aplicação de ADE a um VM que tenha discos encriptados com [encriptação do lado do servidor com teclas geridas pelo cliente](../disk-encryption.md) (SSE + CMK). Aplicar SSE + CMK a um disco de dados num VM encriptado com ADE também é um cenário não suportado.
- Migrar um VM que é encriptado com ADE, ou **que já** foi encriptado com ADE, para [encriptação do lado do servidor com teclas geridas pelo cliente](../disk-encryption.md).
- Encriptar VMs em clusters de failover.
- Encriptação dos [discos ultra Azure](../disks-enable-ultra-ssd.md).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da encriptação do disco Azure](disk-encryption-overview.md)
- [Scripts de exemplo do Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Resolução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)