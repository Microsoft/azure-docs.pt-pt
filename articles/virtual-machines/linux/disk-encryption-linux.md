---
title: Cenários de encriptação de disco azure em VMs Linux
description: Este artigo fornece instruções sobre a ativação da encriptação do disco Microsoft Azure para VMs Linux para vários cenários
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 19dcfb96f29939fd92f49ba288ddb6d9264e0f9a
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970601"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Cenários de encriptação de disco azure em VMs Linux

A Encriptação do Disco Azure utiliza a funcionalidade DM-Crypt do Linux para fornecer encriptação de volume para o OS e discos de dados de máquinas virtuais Azure (VMs), e está integrada com o Azure Key Vault para o ajudar a controlar e gerir as chaves e segredos de encriptação do disco. Para uma visão geral do serviço, consulte a [Encriptação do Disco Azure para VMs Linux](disk-encryption-overview.md).

Existem muitos cenários de encriptação de discos, e os passos podem variar de acordo com o cenário. As seguintes secções cobrem os cenários com maior detalhe para os VMs Linux.

Só é possível aplicar encriptação de disco a máquinas virtuais de [tamanhos vm suportados e sistemas operativos.](disk-encryption-overview.md#supported-vms-and-operating-systems) Deve também cumprir os seguintes pré-requisitos:

- [Requisitos adicionais para VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de networking](disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chaves de encriptação](disk-encryption-overview.md#encryption-key-storage-requirements)

Em todos os casos, deve [tirar uma fotografia](snapshot-copy-managed-disk.md) e/ou criar uma cópia de segurança antes de os discos serem encriptados. As cópias de segurança Certifique-se de que uma opção de recuperação é possível que ocorra uma falha inesperada durante a encriptação. As VMs com discos geridos requerem uma cópia de segurança antes de ocorre de encriptação. Uma vez feita uma cópia de segurança, pode utilizar o [cmdlet set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para encriptar discos geridos especificando o parâmetro -skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs encriptados, consulte o artigo [Azure Backup.](../../backup/backup-azure-vms-encryption.md) 

>[!WARNING]
> - Se já utilizou anteriormente a Encriptação do Disco Azure com a AD Azure para encriptar um VM, tem de continuar a utilizar esta opção para encriptar o seu VM. Consulte a Encriptação do [Disco Azure com AD Azure (versão anterior)](disk-encryption-overview-aad.md) para obter mais detalhes. 
>
> - Ao encriptar os volumes de Sistema sO, o VM deve ser considerado indisponível. Recomendamos vivamente que evite os logins do SSH enquanto a encriptação está em andamento para evitar problemas que bloqueiem quaisquer ficheiros abertos que precisem de ser acedidos durante o processo de encriptação. Para verificar o progresso, utilize o comando [Get-AzVMDiskCryptOnStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell ou o comando CLI de [encriptação vm.](/cli/azure/vm/encryption#az-vm-encryption-show) Espera-se que este processo derea algumas horas para um volume de 30GB de OS, além de tempo adicional para encriptar volumes de dados. O tempo de encriptação do volume de dados será proporcional ao tamanho e quantidade dos volumes de dados, a menos que o formato de encriptação seja utilizado. 
> - A desativação da encriptação em VMs do Linux só é suportada em volumes de dados. Não é suportada em dados ou volumes de sistema operacional se o volume do sistema operacional tenha sido criptografado.  

## <a name="install-tools-and-connect-to-azure"></a>Instale ferramentas e ligue-se ao Azure

A encriptação do disco Azure pode ser ativada e gerida através do [Azure CLI](/cli/azure) e [azure PowerShell](/powershell/azure/new-azureps-module-az). Para isso, tem de instalar as ferramentas localmente e ligar-se à subscrição do Azure.

### <a name="azure-cli"></a>CLI do Azure

O [Azure CLI 2.0](/cli/azure) é uma ferramenta de linha de comando para gerir os recursos azure. A CLI é concebida para consultar dados com flexibilidade, oferecem suporte a operações de longa execução como processos sem bloquear e tornar a escrita de scripts fácil. Pode instalá-lo localmente seguindo os passos em [Instalação do ClI Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

 

Para [iniciar sessão na sua conta Azure com o Azure CLI,](/cli/azure/authenticate-azure-cli)utilize o comando [de login az.](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli
az login
```

Se pretender selecionar um inquilino para iniciar sessão em, utilize:
    
```azurecli
az login --tenant <tenant>
```

Se tiver várias subscrições e pretender especificar uma específica, obtenha a sua lista de subscrição com a lista de [conta Az](/cli/azure/account#az-account-list) e especifique com o conjunto de [conta Az](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Para mais informações, consulte [Iniciar com O Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
O [módulo Azure PowerShell az](/powershell/azure/new-azureps-module-az) fornece um conjunto de cmdlets que utilizam o modelo [Azure Resource Manager](../../azure-resource-manager/management/overview.md) para gerir os seus recursos Azure. Pode utilizá-lo no seu navegador com [a Azure Cloud Shell,](../../cloud-shell/overview.md)ou pode instalá-la na sua máquina local utilizando as instruções no [módulo Deinstalação do módulo PowerShell Azure](/powershell/azure/install-az-ps). 

Se já tiver instalado localmente, certifique-se de que utilizar a versão mais recente da versão do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Descarregue a versão mais recente do lançamento do [Azure PowerShell.](https://github.com/Azure/azure-powershell/releases)

Para [iniciar sessão na sua conta Azure com a Azure PowerShell,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)utilize o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```powershell
Connect-AzAccount
```

Se tiver várias subscrições e pretender especificar uma, utilize o cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) para as listar, seguida do [cmdlet Set-AzContext:](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

A execução do cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verificará se a subscrição correta foi selecionada.

Para confirmar se estão instalados os cmdlets de encriptação do disco Azure, utilize o cmdlet [get-command:](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6)
     
```powershell
Get-command *diskencryption*
```
Para mais informações, consulte [Getting started with Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Ativar a encriptação de um VM Linux existente ou em execução
Neste cenário, pode ativar a encriptação com o modelo do Resource Manager, cmdlets do PowerShell ou comandos da CLI. Se precisar de informações de esquemapara a extensão virtual da máquina, consulte a encriptação do disco Azure para o artigo de [extensão do Linux.](../extensions/azure-disk-enc-linux.md)

>[!IMPORTANT]
 >É obrigatório para instantâneo e/ou a cópia de segurança um disco gerido com base instância de VM fora do e antes de ativar o Azure Disk Encryption. Uma imagem do disco gerido pode ser tirada do portal, ou através do [Azure Backup](../../backup/backup-azure-vms-encryption.md). As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Uma vez feita uma cópia de segurança, o cmdlet set-AzVMDiskCryptonExtension pode ser usado para encriptar discos geridos especificando o parâmetro -skipVmBackup. O comando set-AzVMDiskCryptonExtension falhará contra VMs baseados em disco gerido até que seja feita uma cópia de segurança e este parâmetro tenha sido especificado. 
>
>Encriptar ou a desativação da encriptação pode fazer com que a VM a reiniciar. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Ativar a encriptação de um VM Linux existente ou em execução usando o Azure CLI 

Pode ativar a encriptação do disco no vHD encriptado instalando e utilizando a ferramenta de linha de comando [Azure CLI.](/cli/azure/?view=azure-cli-latest) Pode utilizá-lo no seu browser com o [Azure Cloud Shell](../../cloud-shell/overview.md) ou pode instalá-lo no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Para permitir a encriptação de VMs Linux existentes ou em execução em Azure, utilize os seguintes comandos CLI:

Utilize o comando [de encriptação az vm](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) para ativar a encriptação numa máquina virtual em funcionamento em Azure.

- **Criptografe um VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Criptografe um VM em execução utilizando kek:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um VM, use o comando de [encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desativar a encriptação:** Para desativar a encriptação, utilize o comando de [desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) A desativação da encriptação só é permitida em volumes de dados para VMs do Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Ativar a encriptação de um VM Linux existente ou em execução usando powerShell
Utilize o [cmdlet set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para permitir a encriptação numa máquina virtual em funcionamento no Azure. Pegue uma [fotografia](snapshot-copy-managed-disk.md) e/ou volte a fazer o VM com [a Cópia de Segurança Azure](../../backup/backup-azure-vms-encryption.md) antes de os discos estarem encriptados. O parâmetro -skipVmBackup já está especificado nos scripts PowerShell para encriptar um VM Linux em execução.

-  **Criptografe um VM em execução:** O script abaixo inicializa as suas variáveis e executa o cmdlet set-AzVMDiskEncryptionExtension. O grupo de recursos, VM, e o cofre chave, foram criados como pré-requisitos. Substitua o MyVirtualMachineResourceGroup, mySecureVM e MySecureVault com os seus valores. Modifique o parâmetro -VolumeType para especificar quais os discos que está a encriptar.

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
- **Criptografe um VM em execução utilizando kek:** Pode ser necessário adicionar o parâmetro -VolumeType se estiver a encriptar discos de dados e não o disco OS. 

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
    > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um VM, utilize o [cmdlet Get-AzVmDiskCryptonStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Desativar a encriptação do disco:** Para desativar a encriptação, utilize o cmdlet [Desactivação-AzVMDiskCrypton.](/powershell/module/az.compute/disable-azvmdiskencryption) A desativação da encriptação só é permitida em volumes de dados para VMs do Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Ativar encriptação num VM Linux existente ou em execução com um modelo

Pode ativar a encriptação do disco num VM Linux existente ou em execução em Azure utilizando o [modelo de Gestor](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)de Recursos .

1. Clique em **Implementar para Azure** no modelo de arranque rápido do Azure.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, os termos legais e contrato. Clique em **Criar** para ativar a encriptação no VM existente ou em execução.

A tabela seguinte lista os parâmetros de modelo do Resource Manager para existente ou VMs em execução:

| Parâmetro | Descrição |
| --- | --- |
| vmName | Nome da VM para executar a operação de criptografia. |
| keyVaultName | Nome do cofre chave para o que a chave de encriptação deve ser enviada. Pode obtê-lo utilizando o `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` cmdlet ou o comando Azure CLI `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`.|
| keyVaultResourceGroup | Nome do grupo de recursos que contém o cofre chave. |
|  keyEncryptionKeyURL | URL da chave de encriptação que é usada para encriptar a chave de encriptação. Este parâmetro é opcional se selecionar **nokek** na lista de drop-down UseExistingKek. Se selecionar **kek** na lista de drop-down UseExistingKek, deve introduzir o valor _keyEncryptionKeyURL._ |
| VolumeType | Tipo de volume que a operação de criptografia é executada em. Os valores válidos são _OS,_ _Dados,_ e _Tudo._ 
| forceUpdateTag | Transmita um valor exclusivo, como um GUID sempre que a operação tem de ser forçar a execução. |
| resizeOSDisk | A partição do sistema operacional deve ser redimensionada para ocupar o VHD do SO completo antes de a divisão de volume do sistema. |
| localização | Localização para todos os recursos. |


## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Utilize a funcionalidade EncryptFormatAll para discos de dados em VMs Linux

O parâmetro **EncryptFormatAll** reduz o tempo para os discos de dados do Linux serem encriptados. As divisórias que satisfaçam determinados critérios serão formatadas (com o seu sistema de ficheiros atual), e depois remontadas ao local onde estava antes da execução do comando. Se desejar excluir um disco de dados que satisfaça os critérios, poder desmontá-la antes de executar o comando.

 Depois de executar este comando, quaisquer unidades que foram montadas anteriormente serão formatadas, e a camada de encriptação será iniciada em cima da unidade agora vazia. Quando esta opção estiver selecionada, o disco de recurso efémeras anexado à VM também será encriptado. Se a unidade efémeras é reposta, será reformatada e encriptado novamente para a VM pela solução Azure Disk Encryption na próxima oportunidade. Assim que o disco de recursos for encriptado, o [Agente Microsoft Azure Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) não será capaz de gerir o disco de recursos e ativar o ficheiro swap, mas poderá configurar manualmente o ficheiro swap.

>[!WARNING]
> EncryptFormatAll não deve ser usado quando há dados necessários em volumes de dados de uma VM. Pode excluir discos da encriptação pela desmontagem-los. Primeiro deve experimentar EncryptFormatAll pela primeira vez numa VM de teste, compreender o parâmetro de recurso e sua implicação antes de tentá-lo na VM de produção. A opção de EncryptFormatAll formata o disco de dados e todos os dados serão perdidos. Antes de continuar, certifique-se de que os discos que pretende excluir estão desmontados corretamente. </br></br>
 >Se estiver definindo esse parâmetro ao atualizar as definições de encriptação, pode originar um reinício antes da encriptação real. Neste caso, também desejará remover o disco que não desejado do ficheiro fstab. Da mesma forma, deve adicionar a partição que pretende encriptar formatada para o ficheiro fstab antes de iniciar a operação de criptografia. 

### <a name="encryptformatall-criteria"></a>EncryptFormatTodos os critérios
O parâmetro vai embora todas as divisórias e as criptografa desde que cumpram **todos os** critérios abaixo: 
- Não é uma partição de raiz/sistema operacional/arranque
- Já não está encriptado
- Não é um volume BEK
- Não é um volume RAID
- Não é um volume LVM
- Está montado

Encripte os discos que compõem o volume RAID ou LVM em vez de volume RAID ou LVM.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Utilize o parâmetro EncryptFormatAll com O CLI Azure
Utilize o comando [de encriptação az vm](/cli/azure/vm/encryption#az-vm-encryption-enable) para ativar a encriptação numa máquina virtual em funcionamento em Azure.

-  **Criptografe um VM em execução usando O Formato EncriptadoAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Utilize o parâmetro EncryptFormatAll com um cmdlet PowerShell
Utilize o cmdlet de extensão de [encriptação Set-AzVMDisk](/powershell/module/az.compute/set-azvmdiskencryptionextension) Com o parâmetro EncryptFormatAll. 

**Criptografe um VM em execução usando O Formato EncriptadoAll:** Como exemplo, o script abaixo inicializa as suas variáveis e executa o cmdlet set-AzVMDiskEncryptionExtension com o parâmetro EncryptFormatAll. O grupo de recursos, VM e cofre chave foram criados como pré-requisitos. Substitua o MyVirtualMachineResourceGroup, mySecureVM e MySecureVault com os seus valores.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Utilize o parâmetro EncryptFormatAll com Gestor de Volume Lógico (LVM) 
Recomendamos uma configuração de LVM-no-crypt. Para todos os exemplos seguintes, substitua o caminho do dispositivo e mountpoints tudo o que se adequa aos seus casos de utilização. Esta configuração pode ser feita da seguinte forma:

- Adicione os discos de dados que irão compor a VM.
- Formatar, montar e adicionar estes discos para o ficheiro fstab.

    1. Formate o disco adicionado recentemente. Utilizamos os links simbólicos gerados pelo Azure aqui. Usar os links simbólicos evita problemas relacionados com a alteração de nomes de dispositivo. Para mais informações, consulte o artigo Problemas de Nomes de Dispositivos de Resolução de [Problemas.](troubleshoot-device-names-problems.md)
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    1. Monte os discos.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    1. Adicione a fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    1. Executar o Set-AzVMDiskCryptonExtension Extension PowerShell cmdlet com -EncryptFormatAll para encriptar estes discos.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Configure LVM sobre esses novos discos. Tenha em atenção de que as unidades encriptadas são desbloqueadas depois da VM tem concluir a inicialização. Por isso, a montagem de LVM também terá, em seguida, ser atrasada.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Novos VMs criados a partir de Chaves VHD encriptadas pelo cliente e encriptação
Neste cenário, pode ativar a encriptação utilizando cmdlets do PowerShell ou comandos da CLI. 

Utilize as instruções na encriptação do Disco Azure os mesmos scripts para preparar imagens pré-encriptadas que podem ser usadas no Azure. Depois da imagem é criada, pode utilizar os passos na secção seguinte para criar uma VM do Azure encriptadas.

* [Prepare um Linux VHD pré-encriptado](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >É obrigatório para instantâneo e/ou a cópia de segurança um disco gerido com base instância de VM fora do e antes de ativar o Azure Disk Encryption. Uma imagem instantânea do disco gerido pode ser retirada do portal, ou [o Azure Backup](../../backup/backup-azure-vms-encryption.md) pode ser utilizado. As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Uma vez feita uma cópia de segurança, o cmdlet set-AzVMDiskCryptonExtension pode ser usado para encriptar discos geridos especificando o parâmetro -skipVmBackup. O comando set-AzVMDiskCryptonExtension falhará contra VMs baseados em disco gerido até que seja feita uma cópia de segurança e este parâmetro tenha sido especificado. 
>
> Encriptar ou a desativação da encriptação pode fazer com que a VM a reiniciar. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Utilize o Azure PowerShell para encriptar VMs com VHDs pré-encriptados 
Pode ativar a encriptação do disco no vHD encriptado utilizando o PowerShell cmdlet [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). O exemplo a seguir dá-lhe alguns parâmetros comuns. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados adicionados recentemente

Pode adicionar um novo disco de dados utilizando a fixação do [disco Az vm,](add-disk.md)ou [através do portal Azure](attach-disk-portal.md). Pode criptografar, terá de montar o disco de dados anexado recentemente pela primeira vez. Tem de solicitar a encriptação da unidade de dados, uma vez que a unidade não poderão ser utilizada, enquanto a encriptação está em curso. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Ativar a encriptação num disco adicionado recentemente com a CLI do Azure

 Se o VM foi previamente encriptado com "All" então o parâmetro do tipo volume deve permanecer "All". Inclui todos os discos de SO e dados. Se o VM foi previamente encriptado com um tipo de volume de "OS", então o parâmetro do tipo volume deve ser alterado para "All" para que tanto o SISTEMA como o novo disco de dados sejam incluídos. Se a VM tiver sido encriptada com o tipo de volume de "Dados", em seguida, ele pode permanecer "Data" como demonstrado a seguir. Adicionar e anexar um disco de dados novo a uma VM não é suficiente preparação para a encriptação. O disco anexado recentemente também tem de ser formatado e corretamente montada dentro da VM antes de ativar a encriptação. No Linux, o disco deve ser montado em /etc/fstab com um [nome de dispositivo](troubleshoot-device-names-problems.md)de bloco persistente .  

Em contraste com a sintaxe do Powershell, a CLI não exigir que o utilizador fornecer uma versão de seqüência única ao ativar a encriptação. A CLI gera e usa seu próprio valor de versão de seqüência única automaticamente.

-  **Criptografe volumes de dados de um VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Criptografe volumes de dados de um VM em execução utilizando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco adicionado recentemente com o Azure PowerShell
 Quando utilizar o Powershell para encriptar um disco novo para Linux, uma nova versão de sequência tem de ser especificado. A versão de sequência tem de ser exclusivo. O script a seguir gera um GUID para a versão de sequência. Pegue uma [fotografia](snapshot-copy-managed-disk.md) e/ou volte a fazer o VM com [a Cópia de Segurança Azure](../../backup/backup-azure-vms-encryption.md) antes de os discos estarem encriptados. O parâmetro -skipVmBackup já está especificado nos scripts PowerShell para encriptar um disco de dados recém-adicionado.
 

-  **Criptografe volumes de dados de um VM em execução:** O script abaixo inicializa as suas variáveis e executa o cmdlet set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM e o Cofre de chaves devem já foram criadas como pré-requisitos. Substitua o MyVirtualMachineResourceGroup, mySecureVM e MySecureVault com os seus valores. Os valores aceitáveis para o parâmetro - VolumeType são todos os dados e SO. Se o VM foi previamente encriptado com um tipo de volume de "OS" ou "All", então o parâmetro -VolumeType deve ser alterado para "All" para que tanto o SISTEMA como o novo disco de dados sejam incluídos.

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
- **Criptografe volumes de dados de um VM em execução utilizando KEK:** Os valores aceitáveis para o parâmetro -VolumeType são All, OS e Data. Se a VM foi anteriormente encriptada com um tipo de volume de "SO" ou "Todos", em seguida, o parâmetro - VolumeType deve ser alterado para todos, para que o sistema operacional e o novo disco de dados que serão incluídos.

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
    > A sintaxe para o valor do parâmetro de cofre de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Desativar a encriptação para VMs do Linux
Pode desativar a encriptação com o Azure PowerShell, CLI do Azure, ou com um modelo do Resource Manager. 

>[!IMPORTANT]
>A desativação da encriptação com o Azure Disk Encryption em VMs do Linux só é suportada em volumes de dados. Não é suportada em dados ou volumes de sistema operacional se o volume do sistema operacional tenha sido criptografado.  

- **Desative a encriptação do disco com o Azure PowerShell:** Para desativar a encriptação, utilize o cmdlet [Desactivação-AzVMDiskCrypton.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Desative a encriptação com o Azure CLI:** Para desativar a encriptação, utilize o comando de [desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Desative a encriptação com um modelo de Gestor de Recursos:** Utilize a [encriptação desativação num modelo De VM Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) para desativar a encriptação.
     1. Clique em **Implementar no Azure**.
     2. Selecione a subscrição, grupo de recursos, localização, VM, os termos legais e contrato.

## <a name="unsupported-scenarios"></a>Cenários não apoiados

A encriptação do disco azure não funciona para os seguintes cenários, funcionalidades e tecnologia do Linux:

- Encriptar VM ou VMs básicos criados através do método clássico de criação VM.
- Desativar a encriptação numa unidade de OS ou numa unidade de dados de um VM Linux quando a unidade de SO é encriptada.
- Encriptar a unidade de OS para conjuntos de escala de máquinas virtuais Linux.
- Encriptar imagens personalizadas em VMs Linux.
- Integração com um sistema de gestão chave no local.
- Ficheiros do Azure (sistema de ficheiros partilhados).
- Sistema de ficheiros de rede (NFS).
- Volumes dinâmicos.
- Discos efémeros osso.
- Encriptação de sistemas de ficheiros partilhados/distribuídos como (mas não se limitando a): DFS, GFS, DRDB e CephFS.
- Kernel Crash Dump (kdump).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da encriptação do disco azure](disk-encryption-overview.md)
- [Scripts de exemplo do Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Resolução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)
