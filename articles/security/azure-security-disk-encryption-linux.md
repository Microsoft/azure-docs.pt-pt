---
title: Azure Disk Encryption para VMs do Linux IaaS | Documentos da Microsoft
description: Este artigo fornece instruções sobre como ativar o Microsoft Azure Disk Encryption para VMs de IaaS Linux.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 09/19/2018
ms.openlocfilehash: 6330e187ce0a46744bc27eee3ee74ed125d49446
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51625985"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>Ativar a encriptação de disco do Azure para VMs Linux IaaS 

Pode ativar vários cenários de encriptação de disco e os passos podem variar de acordo com o cenário. As secções seguintes abrangem os cenários mais detalhadamente para VMs de IaaS Linux. Antes de poder utilizar a encriptação de disco, o [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) têm de ser concluídos e o [pré-requisitos adicionais para VMs de IaaS Linux](azure-security-disk-encryption-prerequisites.md#bkmk_LinuxPrereq) secção deve ser revista.

Efetuar uma [instantâneo](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou criar cópias de segurança antes dos discos são encriptados. As cópias de segurança Certifique-se de que uma opção de recuperação é possível que ocorra uma falha inesperada durante a encriptação. As VMs com discos geridos requerem uma cópia de segurança antes de ocorre de encriptação. Assim que for feita uma cópia de segurança, pode utilizar o cmdlet Set-AzureRmVMDiskEncryptionExtension encriptar discos geridos ao especificar o parâmetro - skipVmBackup. Para obter mais informações sobre como criar cópias de segurança e restaurar VMs encriptadas, consulte a [Azure Backup](../backup/backup-azure-vms-encryption.md) artigo. 

>[!WARNING]
 >O Azure Disk Encryption tem o Cofre de chaves e as VMs para estar colocalizados na mesma região. Criar e utilizar um cofre de chaves que está na mesma região que a VM ser encriptada.</br></br>

> Quando encriptar os volumes do SO Linux, o processo pode demorar algumas horas. É normal para volumes de SO Linux para a demorar mais do que os volumes de dados para encriptar. 

>A desativação da encriptação em VMs do Linux só é suportada em volumes de dados. Não é suportada em dados ou volumes de sistema operacional se o volume do sistema operacional tenha sido criptografado.  


## <a name="bkmk_RunningLinux"> </a> Ativar a encriptação numa VM do Linux IaaS existente ou em execução
Neste cenário, pode ativar a encriptação com o modelo do Resource Manager, cmdlets do PowerShell ou comandos da CLI. Se precisar de informações de esquema para a extensão de máquina virtual, consulte a [do Azure Disk Encryption para a extensão de Linux](../virtual-machines/extensions/azure-disk-enc-linux.md) artigo.

>[!IMPORTANT]
 >É obrigatório para instantâneo e/ou a cópia de segurança um disco gerido com base instância de VM fora do e antes de ativar o Azure Disk Encryption. Pode ser criado um instantâneo do disco gerido a partir do portal, ou [Azure Backup](../backup/backup-azure-vms-encryption.md) pode ser utilizado. As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Assim que for feita uma cópia de segurança, o cmdlet Set-AzureRmVMDiskEncryptionExtension pode servir para encriptar discos geridos ao especificar o parâmetro - skipVmBackup. O comando Set-AzureRmVMDiskEncryptionExtension irá falhar nas VMs de disco gerido com base em até que foi efetuada uma cópia de segurança e este parâmetro foi especificado. 
>
>Encriptar ou a desativação da encriptação pode fazer com que a VM a reiniciar. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Ativar a encriptação numa VM do Linux em execução ou existente com a CLI do Azure 
Pode ativar a encriptação de disco no seu VHD encriptado por instalar e utilizar o [CLI 2.0 do Azure](/cli/azure) ferramenta da linha de comandos. Pode utilizá-lo no seu browser com o [Azure Cloud Shell](../cloud-shell/overview.md) ou pode instalá-lo no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Para ativar a encriptação em existente ou executar VMs do Linux IaaS no Azure, utilize os seguintes comandos da CLI:

Utilize o [ativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-enable) comando para ativar a encriptação numa máquina virtual de IaaS em execução no Azure.

-  **Encripte uma VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Encripte uma VM em execução usando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Certifique-se de que os discos são encriptados:** para verificar o estado de encriptação de uma VM de IaaS, utilize o [show do az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-show) comando. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Desativar a encriptação:** para desativar a encriptação, utilize o [desativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-disable) comando. A desativação da encriptação só é permitida em volumes de dados para VMs do Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Ativar a encriptação numa VM do Linux em execução ou existente com o PowerShell
Utilize o [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) cmdlet para ativar a encriptação numa máquina virtual de IaaS em execução no Azure. 

-  **Encriptar uma VM em execução:** o script a seguir inicializa as variáveis e executa o cmdlet Set-AzureRmVMDiskEncryptionExtension. O grupo de recursos, a VM e o Cofre de chaves, devem já foram criadas como pré-requisitos. Substitua MySecureRg MySecureVM e MySecureVault pelos seus valores. Terá de adicionar o parâmetro - VolumeType se de que está a encriptar discos de dados e não o disco do SO. 

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Encriptar uma VM em execução usando KEK:** poderá ter de adicionar o parâmetro - VolumeType se de que está a encriptar discos de dados e não o disco do SO. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Certifique-se de que os discos são encriptados:** para verificar o estado de encriptação de uma VM de IaaS, utilize o [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) cmdlet. 
    
     ```azurepowershell-interactive 
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName MySecureRg -VMName MySecureVM
     ```
    
- **Desativar a encriptação de disco:** para desativar a encriptação, utilize o [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet. A desativação da encriptação só é permitida em volumes de dados para VMs do Linux.
     
     ```azurepowershell-interactive 
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Ativar a encriptação numa VM do Linux IaaS existente ou em execução com um modelo

Pode ativar a encriptação de disco numa VM do Linux IaaS existente ou em execução no Azure utilizando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Clique em **implementar no Azure** no modelo de início rápido do Azure.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, os termos legais e contrato. Clique em **criar** para ativar a encriptação na VM de IaaS existente ou em execução.

A tabela seguinte lista os parâmetros de modelo do Resource Manager para existente ou VMs em execução:

| Parâmetro | Descrição |
| --- | --- |
| vmName | Nome da VM para executar a operação de criptografia. |
| keyVaultName | Nome do Cofre de chaves que a chave do BitLocker deve ser enviada para. Pode obtê-lo utilizando o cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` ou o comando da CLI do Azure "lista de keyvault de az--resource-group"MySecureGroup" |Convertfrom-JSON "|
| keyVaultResourceGroup | Nome do grupo de recursos que contém o Cofre de chaves|
|  keyEncryptionKeyURL | URL da chave de encriptação de chave que é utilizada para encriptar a chave gerada do BitLocker. Este parâmetro é opcional se selecionou **nokek** na lista pendente UseExistingKek. Se selecionou **kek** na lista pendente UseExistingKek, tem de introduzir o _keyEncryptionKeyURL_ valor. |
| VolumeType | Tipo de volume que a operação de criptografia é executada em. Os valores válidos são _SO_, _dados_, e _todos os_. 
| forceUpdateTag | Transmita um valor exclusivo, como um GUID sempre que a operação tem de ser forçar a execução. |
| resizeOSDisk | A partição do sistema operacional deve ser redimensionada para ocupar o VHD do SO completo antes de a divisão de volume do sistema. |
| localização | Localização para todos os recursos. |



## <a name="encrypt-virtual-machine-scale-sets"></a>Encriptar os conjuntos de dimensionamento de máquinas virtuais
[Conjuntos de dimensionamento de máquina virtual do Azure](../virtual-machine-scale-sets/overview.md) permitem-lhe criar e gerir um grupo de idênticos, VMs com balanceamento de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente como resposta à procura ou a uma agenda definida. Utilize a CLI ou o Azure PowerShell para encriptar os conjuntos de dimensionamento de máquinas virtuais.

Pode encontrar um exemplo de ficheiro de batch para a encriptação de disco de dados de conjunto de dimensionamento Linux [aqui](https://github.com/Azure-Samples/azure-cli-samples/tree/master/disk-encryption/vmss). Este exemplo cria um grupo de recursos, o conjunto de dimensionamento do Linux, monta um disco de dados de 5 GB e encripta o conjunto de dimensionamento de máquina virtual.

### <a name="register-for-disk-encryption-preview-using-azure-cli"></a>Registar-se na pré-visualização da encriptação de disco com a CLI do Azure

A encriptação de disco do Azure para pré-visualização de conjuntos de dimensionamento de máquina virtual requer que Self-registar a sua subscrição com [Registre-se de funcionalidade de az](/cli/azure/feature#az_feature_register). Apenas terá de efetuar os passos seguintes na primeira vez que utilize a funcionalidade de pré-visualização da encriptação de disco:

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

Pode demorar até 10 minutos para que o pedido de registo propagar. Pode verificar o estado de registo com [show de funcionalidade de az](/cli/azure/feature#az_feature_show). Quando o `State` relatórios *registada*, volte a registar o *Mirosoft.Compute* fornecedor com [Registre-se fornecedor de az](/cli/azure/provider#az_provider_register):

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

###  <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Encriptar conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure
Utilize o [ativar a encriptação do az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-enable) para ativar a encriptação num conjunto de dimensionamento de máquinas virtuais do Windows. Se definir a política de atualização no conjunto de dimensionamento para manual, comece a encriptação com [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). O grupo de recursos, a VM e o Cofre de chaves devem já foram criadas como pré-requisitos. 

-  **Encriptar um conjunto de dimensionamento de máquinas virtuais em execução**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Encriptar um conjunto usando KEK para encapsular a chave de dimensionamento de máquina virtual em execução**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
- **Obter o estado de encriptação para um conjunto de dimensionamento de máquinas virtuais:** utilização [show de encriptação az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MySecureRG" --name "MySecureVmss"
    ```

- **Desative a encriptação num conjunto de dimensionamento de máquinas virtuais**: Utilize [desativar a encriptação do az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MySecureRG" --name "MySecureVmss"
    ```

### <a name="register-for-disk-encryption-preview-using-azure-powershell"></a>Registar-se na pré-visualização da encriptação de disco com o Azure Powershell

A encriptação de disco do Azure para pré-visualização de conjuntos de dimensionamento de máquina virtual requer que Self-registar a sua subscrição com [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature). Apenas terá de efetuar os passos seguintes na primeira vez que utilize a funcionalidade de pré-visualização da encriptação de disco:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Pode demorar até 10 minutos para que o pedido de registo propagar. Pode verificar o estado de registo com [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Quando o `RegistrationState` relatórios *registada*, volte a registar o *Mirosoft.Compute* fornecedor com [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

###  <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Encriptar conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell
Utilize o [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension) cmdlet para ativar a encriptação num conjunto de dimensionamento de máquinas virtuais do Windows. O grupo de recursos, a VM e o Cofre de chaves devem já foram criadas como pré-requisitos.

-  **Encriptar um conjunto de dimensionamento de máquinas virtuais em execução**:
    ```powershell
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:
    ```powershell
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

- **Obter o estado de encriptação para um conjunto de dimensionamento de máquinas virtuais:** utilize o [Get-AzureRmVmssVMDiskEncryption](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption) cmdlet.
    
    ```powershell
    get-AzureRmVmssVMDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

- **Desative a encriptação num conjunto de dimensionamento de máquinas virtuais**: Utilize o [Disable-AzureRmVmssDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption) cmdlet. 

    ```powershell
    Disable-AzureRmVmssDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```



## <a name="bkmk_EFA"> </a>Utilizar a funcionalidade de EncryptFormatAll para discos de dados em VMs do IaaS Linux
O **EncryptFormatAll** parâmetro reduz o tempo para discos de dados do Linux ser encriptada. Partições determinados critérios de reunião serão formatadas (com o seu atual sistema de ficheiros). Em seguida, irá ser remontadas para onde estava antes da execução do comando. Se desejar excluir um disco de dados que satisfaça os critérios, poder desmontá-la antes de executar o comando.

 Depois de executar este comando, todas as unidades que foram montadas anteriormente serão formatadas. Em seguida, a camada de encriptação será iniciada na parte superior da unidade agora vazia. Quando esta opção estiver selecionada, o disco de recurso efémeras anexado à VM também será encriptado. Se a unidade efémeras é reposta, será reformatada e encriptado novamente para a VM pela solução Azure Disk Encryption na próxima oportunidade.

>[!WARNING]
> EncryptFormatAll não deve ser usado quando há dados necessários em volumes de dados de uma VM. Pode excluir discos da encriptação pela desmontagem-los. Primeiro deve experimentar EncryptFormatAll pela primeira vez numa VM de teste, compreender o parâmetro de recurso e sua implicação antes de tentá-lo na VM de produção. A opção de EncryptFormatAll formata o disco de dados e todos os dados serão perdidos. Antes de continuar, certifique-se de que os discos que pretende excluir corretamente são unmouted. </br></br>
 >Se estiver definindo esse parâmetro ao atualizar as definições de encriptação, pode originar um reinício antes da encriptação real. Neste caso, também desejará remover o disco que não desejado do ficheiro fstab. Da mesma forma, deve adicionar a partição que pretende encriptar formatada para o ficheiro fstab antes de iniciar a operação de criptografia. 

### <a name="bkmk_EFACriteria"> </a> Critérios de EncryptFormatAll
O parâmetro vai, no entanto, todas as partições e criptografa-las, desde que cumpram **todos os** dos critérios abaixo: 
- Não é uma partição de raiz/sistema operacional/arranque
- Já não está encriptado
- Não é um volume BEK
- Não é um volume RAID
- Não é um volume LVM
- Está montado

Encripte os discos que compõem o volume RAID ou LVM em vez de volume RAID ou LVM.

### <a name="bkmk_EFAPSH"> </a> Utilize o parâmetro de EncryptFormatAll com a CLI do Azure
Utilize o [ativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-enable) comando para ativar a encriptação numa máquina virtual de IaaS em execução no Azure.

-  **Encripte uma VM em execução usando EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="bkmk_EFAPSH"> </a> Utilize o parâmetro de EncryptFormatAll com um cmdlet do PowerShell
Utilize o [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) cmdlet com o [parâmetro EncryptFormatAll](https://www.powershellgallery.com/packages/AzureRM/5.0.0). 

**Encriptar uma VM em execução usando EncryptFormatAll:** como exemplo, o script a seguir inicializa as variáveis e executa o cmdlet Set-AzureRmVMDiskEncryptionExtension com o parâmetro EncryptFormatAll. O grupo de recursos, a VM e o Cofre de chaves devem já foram criadas como pré-requisitos. Substitua MySecureRg MySecureVM e MySecureVault pelos seus valores.
  
   ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MySecureVM';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> Utilize o parâmetro de EncryptFormatAll com o Gestor de Volume lógicos (LVM) 
Recomendamos uma configuração de LVM-no-crypt. Para todos os exemplos seguintes, substitua o caminho do dispositivo e mountpoints tudo o que se adequa aos seus casos de utilização. Esta configuração pode ser feita da seguinte forma:

- Adicione os discos de dados que irão compor a VM.
- Formatar, montar e adicionar estes discos para o ficheiro fstab.

    1. Formate o disco adicionado recentemente. Utilizamos os links simbólicos gerados pelo Azure aqui. Usar os links simbólicos evita problemas relacionados com a alteração de nomes de dispositivo. Para obter mais informações, consulte a [problemas de resolução de problemas de nomes de dispositivo](../virtual-machines/linux/troubleshoot-device-names-problems.md) artigo.
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Monte os discos.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Adicione a fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Execute o cmdlet do PowerShell Set-AzureRmVMDiskEncryptionExtension com - EncryptFormatAll para encriptar estes discos.
         ```azurepowershell-interactive
         Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Configure LVM sobre esses novos discos. Tenha em atenção de que as unidades encriptadas são desbloqueadas depois da VM tem concluir a inicialização. Por isso, a montagem de LVM também terá, em seguida, ser atrasada.


## <a name="bkmk_VHDpre"> </a> Novas VMs IaaS, criado a partir de chaves VHD de encriptação encriptadas de cliente
Neste cenário, pode ativar a encriptação utilizando cmdlets do PowerShell ou comandos da CLI. 

Utilize as instruções no apêndice para preparar imagens encriptadas que podem ser utilizadas no Azure. Depois da imagem é criada, pode utilizar os passos na secção seguinte para criar uma VM do Azure encriptadas.

* [Preparar um VHD do Windows encriptadas](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Preparar um VHD do Linux encriptadas](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >É obrigatório para instantâneo e/ou a cópia de segurança um disco gerido com base instância de VM fora do e antes de ativar o Azure Disk Encryption. Pode ser criado um instantâneo do disco gerido a partir do portal, ou [Azure Backup](../backup/backup-azure-vms-encryption.md) pode ser utilizado. As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Assim que for feita uma cópia de segurança, o cmdlet Set-AzureRmVMDiskEncryptionExtension pode servir para encriptar discos geridos ao especificar o parâmetro - skipVmBackup. O comando Set-AzureRmVMDiskEncryptionExtension irá falhar nas VMs de disco gerido com base em até que foi efetuada uma cópia de segurança e este parâmetro foi especificado. 
>
>Encriptar ou a desativação da encriptação pode fazer com que a VM a reiniciar. 



### <a name="bkmk_VHDprePSH"> </a> Utilizar o Azure PowerShell para encriptar VMs de IaaS com VHDs previamente encriptados 
Pode ativar a encriptação de disco no seu VHD encriptada utilizando o cmdlet do PowerShell [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). O exemplo a seguir dá-lhe alguns parâmetros comuns. 

```azurepowershell-interactive
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName "MySecureRG"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados adicionados recentemente

Pode adicionar um novo através de disco de dados [anexar o disco da vm az](../virtual-machines/linux/add-disk.md), ou [através do portal do Azure](../virtual-machines/linux/attach-disk-portal.md). Pode criptografar, terá de montar o disco de dados anexado recentemente pela primeira vez. Tem de solicitar a encriptação da unidade de dados, uma vez que a unidade não poderão ser utilizada, enquanto a encriptação está em curso. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Ativar a encriptação num disco adicionado recentemente com a CLI do Azure

 Se a VM tiver sido anteriormente encriptada com "All", em seguida, o-- tipo de volume parâmetro deve permanecer todos. Inclui todos os discos de SO e dados. Se a VM tiver sido anteriormente encriptada com um tipo de volume de "SO", em seguida, o-- tipo de volume parâmetro deve ser alterado para todos, para que o sistema operacional e o novo disco de dados que serão incluídos. Se a VM tiver sido encriptada com o tipo de volume de "Dados", em seguida, ele pode permanecer "Data" como demonstrado a seguir. Adicionar e anexar um disco de dados novo a uma VM não é suficiente preparação para a encriptação. O disco anexado recentemente também tem de ser formatado e corretamente montada dentro da VM antes de ativar a encriptação. No Linux, o disco deve ser montado no/etc/fstab com um [nome do dispositivo de bloco persistente](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

Em contraste com a sintaxe do Powershell, a CLI não exigir que o utilizador fornecer uma versão de seqüência única ao ativar a encriptação. A CLI gera e usa seu próprio valor de versão de seqüência única automaticamente.

-  **Criptografe volumes de dados de uma VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Criptografe volumes de dados de uma VM em execução usando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco adicionado recentemente com o Azure PowerShell
 Quando utilizar o Powershell para encriptar um disco novo para Linux, uma nova versão de sequência tem de ser especificado. A versão de sequência tem de ser exclusivo. O script a seguir gera um GUID para a versão de sequência. 
 

-  **Criptografar volumes de dados de uma VM em execução:** o script a seguir inicializa as variáveis e executa o cmdlet Set-AzureRmVMDiskEncryptionExtension. O grupo de recursos, a VM e o Cofre de chaves devem já foram criadas como pré-requisitos. Substitua MySecureRg MySecureVM e MySecureVault pelos seus valores. Os valores aceitáveis para o parâmetro - VolumeType são todos os dados e SO. Se a VM foi anteriormente encriptada com um tipo de volume de "SO" ou "Todos", em seguida, o parâmetro - VolumeType deve ser alterado para todos, para que o sistema operacional e o novo disco de dados que serão incluídos.

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
    ```
- **Criptografar volumes de dados de uma VM em execução usando KEK:** os valores aceitáveis para o parâmetro - VolumeType são todos os dados e SO. Se a VM foi anteriormente encriptada com um tipo de volume de "SO" ou "Todos", em seguida, o parâmetro - VolumeType deve ser alterado para todos, para que o sistema operacional e o novo disco de dados que serão incluídos.

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data';

     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Desativar a encriptação para VMs do Linux
Pode desativar a encriptação com o Azure PowerShell, CLI do Azure, ou com um modelo do Resource Manager. 

>[!IMPORTANT]
>A desativação da encriptação com o Azure Disk Encryption em VMs do Linux só é suportada em volumes de dados. Não é suportada em dados ou volumes de sistema operacional se o volume do sistema operacional tenha sido criptografado.  

- **Desativar a encriptação de disco com o Azure PowerShell:** para desativar a encriptação, utilize o [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Desativar a encriptação com a CLI do Azure:** para desativar a encriptação, utilize o [desativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-disable) comando. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Desativar a encriptação com um modelo do Resource Manager:** utilize o [desativar a encriptação numa VM do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) modelo para desativar a encriptação.
     1. Clique em **Implementar no Azure**.
     2. Selecione a subscrição, grupo de recursos, localização, VM, os termos legais e contrato.
     3.  Clique em **Compra** para desativar a encriptação de disco numa VM do Windows em execução. 


## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Ativar a encriptação de disco do Azure para Windows](azure-security-disk-encryption-windows.md)
