---
title: Ativar a encriptação de disco do Azure para VMs de IaaS do Windows
description: Este artigo fornece instruções sobre como ativar o Microsoft disco encriptação para o Windows VMs IaaS do Azure.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 1/31/2019
ms.custom: seodec18
ms.openlocfilehash: a5a42f544fa2552dc7bc85b4bee67925291bef00
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992419"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Ativar a encriptação de disco do Azure para VMs de IaaS do Windows

Pode ativar vários cenários de encriptação de disco e os passos podem variar de acordo com o cenário. As secções seguintes abrangem os cenários mais detalhadamente para VMs de IaaS do Windows. Antes de poder utilizar a encriptação de disco, o [pré-requisitos do Azure Disk Encryption](../security/azure-security-disk-encryption-prerequisites.md) devem ser concluídas. 

Efetuar uma [instantâneo](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou criar cópias de segurança antes dos discos são encriptados. As cópias de segurança Certifique-se de que uma opção de recuperação é possível que ocorra uma falha inesperada durante a encriptação. As VMs com discos geridos requerem uma cópia de segurança antes de ocorre de encriptação. Assim que for feita uma cópia de segurança, pode utilizar o cmdlet Set-AzVMDiskEncryptionExtension encriptar discos geridos ao especificar o parâmetro - skipVmBackup. Para obter mais informações sobre como criar cópias de segurança e restaurar VMs encriptadas, consulte a [Azure Backup](../backup/backup-azure-vms-encryption.md) artigo. 

>[!WARNING]
> - Se tiver utilizado anteriormente [do Azure Disk Encryption, com a aplicação do Azure AD](azure-security-disk-encryption-prerequisites-aad.md) para encriptar esta VM, terá de continuar a utilizar esta opção para encriptar a sua VM. Não é possível usar [do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) nesta VM encriptada como isso não é um cenário suportado, significado mudar para fora da aplicação do AAD para encriptados esta VM não é suportada ainda. 
> - O Azure Disk Encryption tem o Cofre de chaves e as VMs para estar colocalizados na mesma região. Criar e utilizar um cofre de chaves que está na mesma região que a VM ser encriptada. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningWinVM"></a> Ativar a encriptação em existente ou executar VMs do Windows de IaaS
Neste cenário, pode ativar a encriptação com um modelo, cmdlets do PowerShell ou comandos da CLI. As secções seguintes explicam como ativar o Azure Disk Encryption em mais detalhes. Se precisar de informações de esquema para a extensão de máquina virtual, consulte a [extensão de encriptação de disco do Azure para Windows](../virtual-machines/extensions/azure-disk-enc-windows.md) artigo.

>[!IMPORTANT]
 >É obrigatório para instantâneo e/ou a cópia de segurança um disco gerido com base instância de VM fora do e antes de ativar o Azure Disk Encryption. Pode ser criado um instantâneo do disco gerido a partir do portal, ou [Azure Backup](../backup/backup-azure-vms-encryption.md) pode ser utilizado. As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Assim que for feita uma cópia de segurança, o cmdlet Set-AzVMDiskEncryptionExtension pode servir para encriptar discos geridos ao especificar o parâmetro - skipVmBackup. O comando Set-AzVMDiskEncryptionExtension irá falhar nas VMs de disco gerido com base em até que foi efetuada uma cópia de segurança e este parâmetro foi especificado. 
>
>Encriptar ou a desativação da encriptação pode fazer com que a VM a reiniciar. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Ativar a encriptação em existente ou executar VMs com o Azure PowerShell 
Utilize o [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet para ativar a encriptação numa máquina virtual de IaaS em execução no Azure. 

-  **Encripte uma VM em execução:** O script a seguir inicializa as variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM e o Cofre de chaves devem já foram criadas como pré-requisitos. Substitua MySecureRg MySecureVM e MySecureVault pelos seus valores.

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Encripte uma VM em execução usando KEK:** 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Certifique-se de que os discos são encriptados:** Para verificar o estado de encriptação de uma VM de IaaS, utilize o [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
     ```
    
- **Desative a encriptação de disco:** Para desativar a encriptação, utilize o [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. A desativação da encriptação de disco de dados numa VM do Windows quando criptografados discos de SO e dados não funciona conforme esperado. Desative a encriptação em todos os discos em vez disso.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Ativar a encriptação em existente ou executar VMs com a CLI do Azure
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
     > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Certifique-se de que os discos são encriptados:** Para verificar o estado de encriptação de uma VM de IaaS, utilize o [show do az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-show) comando. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Desative a encriptação:** Para desativar a encriptação, utilize o [desativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-disable) comando. A desativação da encriptação de disco de dados numa VM do Windows quando criptografados discos de SO e dados não funciona conforme esperado. Desative a encriptação em todos os discos em vez disso.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >É obrigatório para instantâneo e/ou a cópia de segurança um disco gerido com base instância de VM fora do e antes de ativar o Azure Disk Encryption. Pode ser criado um instantâneo do disco gerido a partir do portal, ou [Azure Backup](../backup/backup-azure-vms-encryption.md) pode ser utilizado. As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Assim que for feita uma cópia de segurança, o cmdlet Set-AzVMDiskEncryptionExtension pode servir para encriptar discos geridos ao especificar o parâmetro - skipVmBackup. Este comando irá falhar nas VMs de disco gerido com base em até que foi efetuada uma cópia de segurança e este parâmetro foi especificado. 
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
| keyVaultName | Nome do Cofre de chaves que a chave do BitLocker deve ser enviada para. Pode obtê-lo utilizando o cmdlet `(Get-AzKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` ou o comando da CLI do Azure `az keyvault list --resource-group "MySecureGroup" |ConvertFrom-JSON`|
| keyVaultResourceGroup | Nome do grupo de recursos que contém o Cofre de chaves|
|  keyEncryptionKeyURL | URL da chave de encriptação de chave que é utilizada para encriptar a chave gerada do BitLocker. Este parâmetro é opcional se selecionou **nokek** na lista pendente UseExistingKek. Se selecionou **kek** na lista pendente UseExistingKek, tem de introduzir o _keyEncryptionKeyURL_ valor. |
| VolumeType | Tipo de volume que a operação de criptografia é executada em. Os valores válidos são _SO_, _dados_, e _todos os_. 
| forceUpdateTag | Transmita um valor exclusivo, como um GUID sempre que a operação tem de ser forçar a execução. |
| resizeOSDisk | A partição do sistema operacional deve ser redimensionada para ocupar o VHD do SO completo antes de a divisão de volume do sistema. |
| localização | Localização para todos os recursos. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Encriptar os conjuntos de dimensionamento de máquinas virtuais

[Conjuntos de dimensionamento de máquina virtual do Azure](../virtual-machine-scale-sets/overview.md) permitem-lhe criar e gerir um grupo de idênticos, VMs com balanceamento de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente como resposta à procura ou a uma agenda definida. Utilize a CLI ou o Azure PowerShell para encriptar os conjuntos de dimensionamento de máquinas virtuais.


### <a name="register-for-disk-encryption-preview-using-azure-powershell"></a>Registar-se na pré-visualização da encriptação de disco com o Azure Powershell

A encriptação de disco do Azure para pré-visualização de conjuntos de dimensionamento de máquina virtual requer que Self-registar a sua subscrição com [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature). Apenas terá de efetuar os passos seguintes na primeira vez que utilize a funcionalidade de pré-visualização da encriptação de disco:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Pode demorar até 10 minutos para que o pedido de registo propagar. Pode verificar o estado de registo com [Get-AzProviderFeature](/powershell/module/az.Resources/Get-azProviderFeature). Quando o `RegistrationState` relatórios *registada*, volte a registar o *Microsoft. Compute* fornecedor com [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider):

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Encriptar conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell

Utilize o [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) cmdlet para ativar a encriptação num conjunto de dimensionamento de máquinas virtuais do Windows. O grupo de recursos, a VM e o Cofre de chaves devem já foram criadas como pré-requisitos.

-  **Encriptar um conjunto de dimensionamento de máquinas virtuais em execução**:
    ```azurepowershell-interactive
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell-interactive
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     Set-AzRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

   >[!NOTE]
   > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Obter o estado de encriptação para um conjunto de dimensionamento de máquinas virtuais:** Utilize o [Get-AzRmVmssVMDiskEncryption](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption) cmdlet.
    
    ```azurepowershell-interactive
    get-AzVmssVMDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

- **Desative a encriptação num conjunto de dimensionamento de máquinas virtuais**: Utilize o [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) cmdlet. 

    ```azurepowershell-interactive
    Disable-AzVmssDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

### <a name="register-for-disk-encryption-preview-using-azure-cli"></a>Registar-se na pré-visualização da encriptação de disco com a CLI do Azure

A encriptação de disco do Azure para pré-visualização de conjuntos de dimensionamento de máquina virtual requer que Self-registar a sua subscrição com [Registre-se de funcionalidade de az](/cli/azure/feature#az-feature-register). Apenas terá de efetuar os passos seguintes na primeira vez que utilize a funcionalidade de pré-visualização da encriptação de disco:

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

Pode demorar até 10 minutos para que o pedido de registo propagar. Pode verificar o estado de registo com [show de funcionalidade de az](/cli/azure/feature#az-feature-show). Quando o `State` relatórios *registada*, volte a registar o *Microsoft. Compute* fornecedor com [Registre-se fornecedor de az](/cli/azure/provider#az-provider-register):

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
     
   >[!NOTE]
   > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Obter o estado de encriptação para um conjunto de dimensionamento de máquinas virtuais:** Utilize [show de encriptação az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MySecureRG" --name "MySecureVmss"
    ```

- **Desative a encriptação num conjunto de dimensionamento de máquinas virtuais**: Utilize [desativar a encriptação do az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MySecureRG" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Define modelos Azure Resource Manager para o dimensionamento de máquinas virtuais do Windows

Para encriptar ou desencriptar o dimensionamento de máquinas virtuais do Windows define, utilize os modelos Azure Resource Manager e as instruções abaixo:

- [Ativar a encriptação num conjunto de dimensionamento de máquinas virtuais do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [Implementar um conjunto de dimensionamento de VMs do Windows com uma jumpbox e ativar a encriptação no conjunto de dimensionamento de VM do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)
- [Desative a encriptação num conjunto de dimensionamento de VM do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. Clique em **Implementar no Azure**.
     2. Preencha os campos obrigatórios em seguida, os Concordo com os termos e condições.
     3. Clique em **Compra** para implementar o modelo.

## <a name="bkmk_VHDpre"> </a> Novas VMs IaaS, criado a partir de chaves VHD de encriptação encriptadas de cliente

Neste cenário, pode ativar a encriptação utilizando cmdlets do PowerShell ou comandos da CLI. 

Utilize as instruções no apêndice para preparar imagens encriptadas que podem ser utilizadas no Azure. Depois da imagem é criada, pode utilizar os passos na secção seguinte para criar uma VM do Azure encriptadas.

* [Preparar um VHD do Windows encriptadas](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Preparar um VHD do Linux encriptadas](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >É obrigatório para instantâneo e/ou a cópia de segurança um disco gerido com base instância de VM fora do e antes de ativar o Azure Disk Encryption. Pode ser criado um instantâneo do disco gerido a partir do portal, ou [Azure Backup](../backup/backup-azure-vms-encryption.md) pode ser utilizado. As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Assim que for feita uma cópia de segurança, o cmdlet Set-AzVMDiskEncryptionExtension pode servir para encriptar discos geridos ao especificar o parâmetro - skipVmBackup. O comando Set-AzVMDiskEncryptionExtension irá falhar nas VMs de disco gerido com base em até que foi efetuada uma cópia de segurança e este parâmetro foi especificado. 
>
>Encriptar ou a desativação da encriptação pode fazer com que a VM a reiniciar. 


### <a name="bkmk_VHDprePSH"> </a> Encriptar VMs com VHDs encriptadas com o Azure PowerShell
Pode ativar a encriptação de disco no seu VHD encriptada utilizando o cmdlet do PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). O exemplo a seguir dá-lhe alguns parâmetros comuns. 

```azurepowershell-interactive
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MySecureRG"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados adicionados recentemente
Pode [adicione um novo disco a uma VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md), ou [através do portal do Azure](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco adicionado recentemente com o Azure PowerShell
 Quando utilizar o Powershell para encriptar um disco novo para VMs do Windows, uma nova versão de seqüência deve ser especificada. A versão de sequência tem de ser exclusivo. O script a seguir gera um GUID para a versão de sequência. Em alguns casos, um disco de dados adicionados recentemente pode estar encriptado automaticamente pela extensão do Azure Disk Encryption. Encriptação automática ocorre normalmente quando a VM for reiniciado depois do novo disco fica online. Isto é habitualmente causado porque "All" foi especificado para o tipo de volume, quando a encriptação de disco tiver sido executado na VM. Se a encriptação automática ocorre num disco de dados adicionados recentemente, recomendamos executar o cmdlet Set-AzVmDiskEncryptionExtension novamente com a nova versão de sequência. Se o novo disco de dados é automaticamente encriptado e não pretender a encriptar, desencriptar todas as unidades em primeiro lugar, em seguida, criptografar novamente com uma nova versão de sequência especificando o sistema operacional para o tipo de volume. 
  
 

-  **Encripte uma VM em execução:** O script a seguir inicializa as variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM e o Cofre de chaves devem já foram criadas como pré-requisitos. Substitua MySecureRg MySecureVM e MySecureVault pelos seus valores. Este exemplo utiliza "All" para o parâmetro - VolumeType, que inclui volumes de dados e SO. Se quiser apenas criptografar o volume do sistema operacional, utilize o "SO" para o parâmetro - VolumeType. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Encripte uma VM em execução usando KEK:** Este exemplo utiliza "All" para o parâmetro - VolumeType, que inclui volumes de dados e SO. Se quiser apenas criptografar o volume do sistema operacional, utilize o "SO" para o parâmetro - VolumeType.

     ```azurepowershell-interactive
     $sequenceVersion = [Guid]::NewGuid();
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Ativar a encriptação num disco adicionado recentemente com a CLI do Azure
 O comando da CLI do Azure fornecerá automaticamente uma nova versão de sequência para quando executar o comando para ativar a encriptação. O exemplo utiliza "All" para o parâmetro de tipo de volume. Terá de alterar o parâmetro de tipo de volume para o sistema operacional, se está apenas a encriptar o disco do SO. Em contraste com a sintaxe do Powershell, a CLI não exigir que o utilizador fornecer uma versão de seqüência única ao ativar a encriptação. A CLI gera e usa seu próprio valor de versão de seqüência única automaticamente.   

-  **Encripte uma VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Encripte uma VM em execução usando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Desativar a encriptação
Pode desativar a encriptação com o Azure PowerShell, CLI do Azure, ou com um modelo do Resource Manager. A desativação da encriptação de disco de dados numa VM do Windows quando criptografados discos de SO e dados não funciona conforme esperado. Desative a encriptação em todos os discos em vez disso.

- **Desative a encriptação de disco com o Azure PowerShell:** Para desativar a encriptação, utilize o [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Desative a encriptação com a CLI do Azure:** Para desativar a encriptação, utilize o [desativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-disable) comando. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type "all"
     ```
- **Desative a encriptação com um modelo do Resource Manager:** 

    1. Clique em **implementar no Azure** partir do [desativar a encriptação de disco na VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) modelo.
    2. Selecione a subscrição, grupo de recursos, localização, VM, tipo de volume, os termos legais e contrato.
    3.  Clique em **Compra** para desativar a encriptação de disco numa VM do Windows em execução. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ativar a encriptação de disco do Azure para Linux](azure-security-disk-encryption-linux.md)
