---
title: Ativar a encriptação de disco do Azure para VMs de IaaS do Windows
description: Este artigo fornece instruções sobre como ativar o Microsoft disco encriptação para o Windows VMs IaaS do Azure.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 109c954ab877d0b8d348fc4b9d7de01c19e41344
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958826"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Ativar a encriptação de disco do Azure para VMs de IaaS do Windows

Este artigo fornece instruções sobre como habilitar a criptografia de disco Microsoft Azure para VMs (máquinas virtuais) IaaS do Windows. Para poder usar a criptografia de disco, você deve primeiro concluir os [pré-requisitos de Azure Disk Encryption](../security/azure-security-disk-encryption-prerequisites.md). 

Também é altamente recomendável que você [crie um instantâneo](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou faça backup dos discos antes da criptografia. As cópias de segurança Certifique-se de que uma opção de recuperação é possível que ocorra uma falha inesperada durante a encriptação. As VMs com discos geridos requerem uma cópia de segurança antes de ocorre de encriptação. Depois que um backup é feito, você pode usar o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para criptografar discos gerenciados especificando o parâmetro-skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs criptografadas, consulte o artigo [fazer backup e restaurar a VM criptografada do Azure](../backup/backup-azure-vms-encryption.md) .

>[!WARNING]
> - Se você tiver usado anteriormente [Azure Disk Encryption com o aplicativo do Azure ad](azure-security-disk-encryption-prerequisites-aad.md) para criptografar essa VM, será necessário continuar usando essa opção para criptografar sua VM. Você não pode usar [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) nessa VM criptografada, pois esse não é um cenário com suporte, o que significa que não há suporte para a alternância do aplicativo AAD para essa VM criptografada ainda. 
> - O Azure Disk Encryption tem o Cofre de chaves e as VMs para estar colocalizados na mesma região. Criar e utilizar um cofre de chaves que está na mesma região que a VM ser encriptada. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningWinVM"></a> Ativar a encriptação em existente ou executar VMs do Windows de IaaS
Você pode habilitar a criptografia usando um modelo, cmdlets do PowerShell ou comandos da CLI. Se precisar de informações de esquema para a extensão de máquina virtual, consulte a [extensão de encriptação de disco do Azure para Windows](../virtual-machines/extensions/azure-disk-enc-windows.md) artigo.

>[!IMPORTANT]
 > É obrigatório para instantâneo e/ou a cópia de segurança um disco gerido com base instância de VM fora do e antes de ativar o Azure Disk Encryption. Pode ser criado um instantâneo do disco gerido a partir do portal, ou [Azure Backup](../backup/backup-azure-vms-encryption.md) pode ser utilizado. As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Depois que um backup é feito, o cmdlet Set-AzVMDiskEncryptionExtension pode ser usado para criptografar discos gerenciados especificando o parâmetro-skipVmBackup. O comando Set-AzVMDiskEncryptionExtension irá falhar em VMs baseadas em disco gerenciado até que um backup tenha sido feito e esse parâmetro tenha sido especificado. 
>
> Encriptar ou a desativação da encriptação pode fazer com que a VM a reiniciar. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Ativar a encriptação em existente ou executar VMs com o Azure PowerShell 
Use o cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para habilitar a criptografia em uma máquina virtual IaaS em execução no Azure. 

-  **Encripte uma VM em execução:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM e o Cofre de chaves devem já foram criadas como pré-requisitos. Substitua MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM e MySecureVault por seus valores.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Encripte uma VM em execução usando KEK:** 

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão criptografados:** Para verificar o status de criptografia de uma VM IaaS, use o cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Desabilitar a criptografia de disco:** Para desabilitar a criptografia, use o cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Desabilitar a criptografia de disco de dados na VM do Windows quando o sistema operacional e os discos de dados foram criptografados não funciona conforme o esperado. Desative a encriptação em todos os discos em vez disso.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Ativar a encriptação em existente ou executar VMs com a CLI do Azure
Utilize o [ativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-enable) comando para ativar a encriptação numa máquina virtual de IaaS em execução no Azure.

- **Encripte uma VM em execução:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Encripte uma VM em execução usando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão criptografados:** Para verificar o status de criptografia de uma VM IaaS, use o comando [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desabilitar criptografia:** Para desabilitar a criptografia, use o comando [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Desabilitar a criptografia de disco de dados na VM do Windows quando o sistema operacional e os discos de dados foram criptografados não funciona conforme o esperado. Desative a encriptação em todos os discos em vez disso.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
 
  > [!NOTE]
  >É obrigatório para instantâneo e/ou a cópia de segurança um disco gerido com base instância de VM fora do e antes de ativar o Azure Disk Encryption. Pode ser criado um instantâneo do disco gerido a partir do portal, ou [Azure Backup](../backup/backup-azure-vms-encryption.md) pode ser utilizado. As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Depois que um backup é feito, o cmdlet Set-AzVMDiskEncryptionExtension pode ser usado para criptografar discos gerenciados especificando o parâmetro-skipVmBackup. Este comando irá falhar nas VMs de disco gerido com base em até que foi efetuada uma cópia de segurança e este parâmetro foi especificado. 
  >
  >Encriptar ou a desativação da encriptação pode fazer com que a VM a reiniciar. 

### <a name="bkmk_RunningWinVMwRM"> </a>Usando o modelo do Resource Manager

Pode ativar a encriptação de disco no existente ou executar VMs do Windows de IaaS no Azure utilizando o [modelo do Resource Manager para encriptar uma VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. No modelo de início rápido do Azure, clique em **implementar no Azure**.

2. Selecione a subscrição, grupo de recursos, localização, definições, os termos legais e contrato. Clique em **Compra** para ativar a encriptação na VM de IaaS existente ou em execução.

A tabela seguinte lista os parâmetros de modelo do Resource Manager para existente ou VMs em execução:

| Parâmetro | Descrição |
| --- | --- |
| vmName | Nome da VM para executar a operação de criptografia. |
| keyVaultName | Nome do Cofre de chaves que a chave do BitLocker deve ser enviada para. Você pode obtê-lo usando o cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` ou o comando CLI do Azure `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Nome do grupo de recursos que contém o Cofre de chaves|
|  keyEncryptionKeyURL | A URL da chave de criptografia de chave, no formato https://@no__t -0keyvault-name&gt;.vault.azure.net/Key/&lt;key-Name @ no__t-3. Se você não quiser usar um KEK, deixe esse campo em branco. |
| VolumeType | Tipo de volume que a operação de criptografia é executada em. Os valores válidos são _SO_, _dados_, e _todos os_. 
| forceUpdateTag | Transmita um valor exclusivo, como um GUID sempre que a operação tem de ser forçar a execução. |
| resizeOSDisk | A partição do sistema operacional deve ser redimensionada para ocupar o VHD do SO completo antes de a divisão de volume do sistema. |
| localização | Localização para todos os recursos. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Encriptar os conjuntos de dimensionamento de máquinas virtuais

[Conjuntos de dimensionamento de máquina virtual do Azure](../virtual-machine-scale-sets/overview.md) permitem-lhe criar e gerir um grupo de idênticos, VMs com balanceamento de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente como resposta à procura ou a uma agenda definida. Utilize a CLI ou o Azure PowerShell para encriptar os conjuntos de dimensionamento de máquinas virtuais.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Encriptar conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell

Use o cmdlet [set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) para habilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais do Windows. O grupo de recursos, o conjunto de dimensionamento de máquinas virtuais e o cofre de chaves já devem ter sido criados como pré-requisitos.

-  **Encriptar um conjunto de dimensionamento de máquinas virtuais em execução**:
    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     Set-AzRmVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

   >[!NOTE]
   > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Obter status de criptografia para um conjunto de dimensionamento de máquinas virtuais:** Use o cmdlet [Get-AzVmssDiskEncryption](/powershell/module/az.compute/get-azvmssdiskencryption) .
    
    ```azurepowershell-interactive
    get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Desabilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais**: Use o cmdlet [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) . 

    ```azurepowershell-interactive
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Encriptar conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure

Utilize o [ativar a encriptação do az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-enable) para ativar a encriptação num conjunto de dimensionamento de máquinas virtuais do Windows. Se definir a política de atualização no conjunto de dimensionamento para manual, comece a encriptação com [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). O grupo de recursos, o conjunto de dimensionamento de máquinas virtuais e o cofre de chaves já devem ter sido criados como pré-requisitos.

-  **Encriptar um conjunto de dimensionamento de máquinas virtuais em execução**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Encriptar um conjunto usando KEK para encapsular a chave de dimensionamento de máquina virtual em execução**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
     
   >[!NOTE]
   > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Obter status de criptografia para um conjunto de dimensionamento de máquinas virtuais:** Usar [AZ vmss Encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Desabilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais**: Usar a [desabilitação de criptografia AZ vmss](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Modelos de Azure Resource Manager para conjuntos de dimensionamento de máquinas virtuais do Windows

Para criptografar ou descriptografar os conjuntos de dimensionamento de máquinas virtuais do Windows, use os modelos de Azure Resource Manager e as instruções abaixo:

- [Habilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [Desabilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. Clique em **Implementar no Azure**.
     2. Preencha os campos obrigatórios e concorde com os termos e condições.
     3. Clique em **comprar** para implantar o modelo.

## <a name="bkmk_VHDpre"> </a> Novas VMs IaaS, criado a partir de chaves VHD de encriptação encriptadas de cliente

Neste cenário, pode ativar a encriptação utilizando cmdlets do PowerShell ou comandos da CLI. 

Utilize as instruções no apêndice para preparar imagens encriptadas que podem ser utilizadas no Azure. Depois da imagem é criada, pode utilizar os passos na secção seguinte para criar uma VM do Azure encriptadas.

* [Preparar um VHD do Windows encriptadas](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Preparar um VHD do Linux encriptadas](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >É obrigatório para instantâneo e/ou a cópia de segurança um disco gerido com base instância de VM fora do e antes de ativar o Azure Disk Encryption. Pode ser criado um instantâneo do disco gerido a partir do portal, ou [Azure Backup](../backup/backup-azure-vms-encryption.md) pode ser utilizado. As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Depois que um backup é feito, o cmdlet Set-AzVMDiskEncryptionExtension pode ser usado para criptografar discos gerenciados especificando o parâmetro-skipVmBackup. O comando Set-AzVMDiskEncryptionExtension irá falhar em VMs baseadas em disco gerenciado até que um backup tenha sido feito e esse parâmetro tenha sido especificado. 
>
>Encriptar ou a desativação da encriptação pode fazer com que a VM a reiniciar. 


### <a name="bkmk_VHDprePSH"> </a> Encriptar VMs com VHDs encriptadas com o Azure PowerShell
Você pode habilitar a criptografia de disco em seu VHD criptografado usando o cmdlet do PowerShell [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). O exemplo a seguir dá-lhe alguns parâmetros comuns. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados adicionados recentemente
Pode [adicione um novo disco a uma VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md), ou [através do portal do Azure](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco adicionado recentemente com o Azure PowerShell
 Quando utilizar o Powershell para encriptar um disco novo para VMs do Windows, uma nova versão de seqüência deve ser especificada. A versão de sequência tem de ser exclusivo. O script a seguir gera um GUID para a versão de sequência. Em alguns casos, um disco de dados adicionados recentemente pode estar encriptado automaticamente pela extensão do Azure Disk Encryption. Encriptação automática ocorre normalmente quando a VM for reiniciado depois do novo disco fica online. Isto é habitualmente causado porque "All" foi especificado para o tipo de volume, quando a encriptação de disco tiver sido executado na VM. Se a criptografia automática ocorrer em um disco de dados recém-adicionado, recomendamos executar o cmdlet Set-AzVmDiskEncryptionExtension novamente com a nova versão de sequência. Se o novo disco de dados é automaticamente encriptado e não pretender a encriptar, desencriptar todas as unidades em primeiro lugar, em seguida, criptografar novamente com uma nova versão de sequência especificando o sistema operacional para o tipo de volume. 
  
 

-  **Encripte uma VM em execução:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM e o Cofre de chaves devem já foram criadas como pré-requisitos. Substitua MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM e MySecureVault por seus valores. Este exemplo utiliza "All" para o parâmetro - VolumeType, que inclui volumes de dados e SO. Se quiser apenas criptografar o volume do sistema operacional, utilize o "SO" para o parâmetro - VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Encripte uma VM em execução usando KEK:** Este exemplo utiliza "All" para o parâmetro - VolumeType, que inclui volumes de dados e SO. Se quiser apenas criptografar o volume do sistema operacional, utilize o "SO" para o parâmetro - VolumeType.

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Ativar a encriptação num disco adicionado recentemente com a CLI do Azure
 O comando da CLI do Azure fornecerá automaticamente uma nova versão de sequência para quando executar o comando para ativar a encriptação. O exemplo utiliza "All" para o parâmetro de tipo de volume. Terá de alterar o parâmetro de tipo de volume para o sistema operacional, se está apenas a encriptar o disco do SO. Em contraste com a sintaxe do Powershell, a CLI não exigir que o utilizador fornecer uma versão de seqüência única ao ativar a encriptação. A CLI gera e usa seu próprio valor de versão de seqüência única automaticamente.   

-  **Encripte uma VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Encripte uma VM em execução usando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Desativar a encriptação
Pode desativar a encriptação com o Azure PowerShell, CLI do Azure, ou com um modelo do Resource Manager. Desabilitar a criptografia de disco de dados na VM do Windows quando o sistema operacional e os discos de dados foram criptografados não funciona conforme o esperado. Desative a encriptação em todos os discos em vez disso.

- **Desabilitar a criptografia de disco com o Azure PowerShell:** Para desabilitar a criptografia, use o cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Desabilite a criptografia com o CLI do Azure:** Para desabilitar a criptografia, use o comando [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Desative a encriptação com um modelo do Resource Manager:** 

    1. Clique em **implementar no Azure** partir do [desativar a encriptação de disco na VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) modelo.
    2. Selecione a subscrição, grupo de recursos, localização, VM, tipo de volume, os termos legais e contrato.
    3.  Clique em **Compra** para desativar a encriptação de disco numa VM do Windows em execução. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ativar a encriptação de disco do Azure para Linux](azure-security-disk-encryption-linux.md)
