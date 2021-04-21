---
title: Encriptação do disco Azure com Azure AD para VMs do Windows (versão anterior)
description: Este artigo fornece instruções para permitir a encriptação do disco do Microsoft Azure para o Windows IaaS VMs.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 081ead434dbdc3b9c348e3fa35068a638bab6e62
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776866"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Encriptação do disco Azure com Azure AD para VMs do Windows (versão anterior)

**A nova versão da Encriptação do Disco Azure elimina o requisito para fornecer um parâmetro de aplicação AD Azure para permitir a encriptação do disco VM. Com a nova versão, já não é obrigado a fornecer credenciais AZure AD durante o passo de encriptação ativa. Todos os novos VMs devem ser encriptados sem os parâmetros da aplicação AD Azure utilizando a nova versão. Para visualizar instruções para ativar a encriptação do disco VM utilizando a nova versão, consulte [Azure Disk Encryption for Windows VMS](disk-encryption-windows.md). Os VMs que já estavam encriptados com os parâmetros da aplicação AD Azure ainda são suportados e devem continuar a ser mantidos com a sintaxe AAD.**


Pode ativar muitos cenários de encriptação de discos, e os passos podem variar de acordo com o cenário. As secções seguintes cobrem os cenários em maior detalhe para os VMs do Windows IaaS. Antes de poder utilizar a encriptação do disco, os [pré-requisitos](disk-encryption-overview-aad.md) de encriptação do disco Azure têm de ser concluídos. 


>[!IMPORTANT]
> - Deve [tirar uma fotografia](snapshot-copy-managed-disk.md) e/ou criar uma cópia de segurança antes de os discos serem encriptados. As cópias de segurança garantem que uma opção de recuperação é possível se ocorrer uma falha inesperada durante a encriptação. VMs com discos geridos requerem uma cópia de segurança antes da encriptação ocorrer. Uma vez feita uma cópia de segurança, pode utilizar o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para encriptar os discos geridos especificando o parâmetro -skipVmBackup. Para obter mais informações sobre como fazer o back up e restaurar VMs encriptados, consulte [Back up e restaure O VM encriptado](../../backup/backup-azure-vms-encryption.md). 
>
> - Encriptar ou desativar a encriptação pode fazer com que um VM reinicie.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Ativar encriptação em novos IaaS VMs criados a partir do Marketplace
Pode ativar a encriptação do disco em novo IaaS Windows VM a partir do Marketplace in Azure utilizando um modelo de Gestor de Recursos. O modelo cria um novo VM encriptado do Windows utilizando a imagem da galeria Windows Server 2012.

1. No [modelo de Gestor de Recursos,](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)clique em **Implementar para Azure**.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, termos legais e acordo. Clique **em Comprar** para implementar um novo IaaS VM onde a encriptação está ativada.

3. Depois de implementar o modelo, verifique o estado de encriptação VM utilizando o seu método preferido:
     - Verifique com o CLI Azure utilizando o comando [do programa de encriptação az vm.](/cli/azure/vm/encryption#az_vm_encryption_show) 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Verifique com a Azure PowerShell utilizando o [cmdlet Get-AzVmDiskEncrypationStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Selecione o VM e, em seguida, clique em **Discos** sob a direção **de Definições** para verificar o estado de encriptação no portal. Na tabela em **Encriptação,** verá se está ativada. 
           ![Portal Azure - Encriptação do disco Ativada](../media/disk-encryption/disk-encryption-fig2.png)

A tabela que se segue lista os parâmetros do modelo do Gestor de Recursos para novos VMs do cenário Marketplace utilizando o ID do cliente Azure:

| Parâmetro | Descrição | 
| --- | --- |
| adminUserName | Nome de utilizador de administrador para a máquina virtual. |
| adminPassword | Senha de utilizador de administração para a máquina virtual. |
| newStorageAccountName | Nome da conta de armazenamento para armazenar OS e VHDs de dados. |
| vmSize | Tamanho do VM. Atualmente, apenas as séries Standard A, D e G são suportadas. |
| virtualNetworkName | Nome do VNet a que o VM NIC deve pertencer. |
| subnetName | Nome da sub-rede no VNet a que o VM NIC deve pertencer. |
| AADClientID | Identificação do cliente da aplicação AZure AD que tem permissões para escrever segredos para o seu cofre chave. |
| AADClientSecret | Segredo de cliente da aplicação AZure AD que tem permissões para escrever segredos para o seu cofre chave. |
| keyVaultURL | URL do cofre chave para o que a chave BitLocker deve ser carregada. Você pode obtê-lo usando o cmdlet `(Get-AzKeyVault -VaultName "MyKeyVault&quot; -ResourceGroupName &quot;MyKeyVaultResourceGroupName").VaultURI` ou o Azure CLI `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | URL da chave de encriptação que é usada para encriptar a chave BitLocker gerada (opcional). </br> </br>KeyEncryptionKeyURL é um parâmetro opcional. Pode trazer o seu próprio KEK para salvaguardar ainda mais a chave de encriptação de dados (segredo de palavras-passe) no seu cofre de chaves. |
| keyVaultResourceGroup | Grupo de recursos do cofre de chaves. |
| vmName | Nome do VM em que a operação de encriptação deve ser executada. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a> Ativar a encriptação em VMs do IaaS Windows existentes ou em execução
Neste cenário, pode ativar a encriptação utilizando um modelo, cmdlets PowerShell ou comandos CLI. As seguintes secções explicam com maior detalhe como ativar a encriptação do disco Azure. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a> Ativar a encriptação em VMs existentes ou em execução com Azure PowerShell 
Utilize o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para ativar a encriptação numa máquina virtual IaaS em funcionamento em Azure. Para obter informações sobre como permitir a encriptação com encriptação do disco Azure utilizando cmdlets PowerShell, consulte as publicações de blog [Explore Azure Disk Encryption with Azure PowerShell - Part 1](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell) e [Explore Azure Disk Encryption with Azure PowerShell - Part 2](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell-part-2).

-  **Criptografe um VM em execução usando um segredo de cliente:** O script abaixo inicializa as suas variáveis e executa o Set-AzVMDiskEncryptionExtension cmdlet. O grupo de recursos, VM, cofre-chave, app AAD e segredo de clientes já deveriam ter sido criados como pré-requisitos. Substitua o MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret pelos seus valores.
     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Criptografe um VM em execução usando KEK para embrulhar o segredo do cliente:** A encriptação do disco Azure permite especificar uma chave existente no cofre para embrulhar segredos de encriptação de discos que foram gerados enquanto permite a encriptação. Quando uma chave de encriptação chave é especificada, a Encriptação do Disco Azure usa essa chave para embrulhar os segredos de encriptação antes de escrever para Key Vault. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > A sintaxe para o valor do parâmetro de chave-chave de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro chave-chave de encriptação é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um IaaS VM, utilize o [cmdlet Get-AzVmDiskEncrypationStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Desativar a encriptação do disco:** Para desativar a encriptação, utilize o [cmdlet de desencriptação Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Ativar a encriptação em VMs existentes ou em execução com CLI Azure
Utilize a [encriptação az vm ativar](/cli/azure/vm/encryption#az_vm_encryption_enable) o comando para ativar a encriptação numa máquina virtual IaaS em execução em Azure.

- **Criptografe um VM em execução usando um segredo de cliente:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Criptografe um VM em execução usando KEK para embrulhar o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A sintaxe para o valor do parâmetro de chave-chave de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A sintaxe para o valor do parâmetro chave-chave de encriptação é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um IaaS VM, utilize o comando [do programa de encriptação az vm.](/cli/azure/vm/encryption#az_vm_encryption_show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desativar a encriptação:** Para desativar a encriptação, utilize o comando [de desativação de encriptação az vm.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Usando o modelo de gestor de recursos
Pode ativar a encriptação do disco em VMs do IaaS Windows existente ou em execução, utilizando o [modelo de Gestor de Recursos para encriptar um VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. No modelo de arranque rápido Azure, clique em **Implementar para Azure**.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, termos legais e acordo. Clique em **Comprar** para ativar a encriptação no IaaS VM existente ou em execução.

A tabela que se segue lista os parâmetros do modelo do Gestor de Recursos para VMs existentes ou em execução que utilizam um ID do cliente Azure:

| Parâmetro | Descrição |
| --- | --- |
| AADClientID | Identificação do cliente da aplicação AD Azure que tem permissões para escrever segredos para o cofre chave. |
| AADClientSecret | Segredo de cliente da aplicação AD Azure que tem permissões para escrever segredos para o cofre chave. |
| keyVaultName | Nome do cofre chave para o que a chave BitLocker deve ser carregada. Você pode obtê-lo usando o cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` ou o comando Azure CLI `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | URL da chave de encriptação que é usada para encriptar a chave BitLocker gerada. Este parâmetro é opcional se selecionar **nokek** na lista de drop-down UseExistingKek. Se selecionar **kek** na lista de drop-down UseExistingKek, deve introduzir o valor _KeyEncryptionKeyURL._ |
| volumeType | Tipo de volume em que a operação de encriptação é executada. Valores válidos são _OS,_ _Dados_ e _Todos._ |
| sequênciaVersão | Versão de sequência da operação BitLocker. Incremente este número de versão sempre que uma operação de encriptação de disco é realizada no mesmo VM. |
| vmName | Nome do VM em que a operação de encriptação deve ser executada. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Novos VMs iaas criados a partir de VHD encriptados pelo cliente e chaves de encriptação
Neste cenário, pode ativar a encriptação utilizando o modelo de Gestor de Recursos, cmdlets PowerShell ou comandos CLI. As seguintes secções explicam com maior detalhe o modelo de Gestor de Recursos e comandos CLI. 

Utilize as instruções no apêndice para preparar imagens pré-encriptadas que podem ser utilizadas em Azure. Após a criação da imagem, pode utilizar os passos na secção seguinte para criar um Azure VM encriptado.

* [Preparar um Windows VHD pré-encriptado](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"></a> Criptografe VMs com VHDs pré-encriptados com Azure PowerShell
Pode ativar a encriptação do disco no seu VHD encriptado utilizando o [Conjunto de Cmdlet PowerShell Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). O exemplo abaixo dá-lhe alguns parâmetros comuns. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados recém-adicionado
Pode [adicionar um novo disco a um Windows VM utilizando o PowerShell,](attach-disk-ps.md)ou [através do portal Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco recém-adicionado com a Azure PowerShell
 Ao utilizar o Powershell para encriptar um novo disco para VMs do Windows, deve ser especificada uma nova versão de sequência. A versão da sequência tem de ser única. O script abaixo gera um GUID para a versão de sequência. Em alguns casos, um disco de dados recém-adicionado pode ser encriptado automaticamente pela extensão de encriptação do disco Azure. A encriptação automática ocorre geralmente quando o VM reinicia após o novo disco estar online. Isto é normalmente causado porque "All" foi especificado para o tipo de volume quando a encriptação do disco foi previamente correu no VM. Se ocorrer uma encriptação automática num disco de dados recém-adicionado, recomendamos que volte a executar o cmdlet Set-AzVmDiskEncryptionExtension com nova versão de sequência. Se o seu novo disco de dados estiver encriptado automaticamente e não desejar ser encriptado, desencriptar todas as unidades primeiro, então reencrimar com uma nova versão de sequência especificando o SO para o tipo de volume. 
 

-  **Criptografe um VM em execução usando um segredo de cliente:** O script abaixo inicializa as suas variáveis e executa o Set-AzVMDiskEncryptionExtension cmdlet. O grupo de recursos, VM, cofre-chave, app AAD e segredo de clientes já deveriam ter sido criados como pré-requisitos. Substitua o MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret pelos seus valores. Este exemplo utiliza "All" para o parâmetro -VolumeType, que inclui volumes de SO e Dados. Se quiser apenas encriptar o volume de SO, utilize "OS" para o parâmetro -VolumeType. 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **Criptografe um VM em execução usando KEK para embrulhar o segredo do cliente:** A encriptação do disco Azure permite especificar uma chave existente no cofre para embrulhar segredos de encriptação de discos que foram gerados enquanto permite a encriptação. Quando uma chave de encriptação chave é especificada, a Encriptação do Disco Azure usa essa chave para embrulhar os segredos de encriptação antes de escrever para Key Vault. Este exemplo utiliza "All" para o parâmetro -VolumeType, que inclui volumes de SO e Dados. Se quiser apenas encriptar o volume de SO, utilize "OS" para o parâmetro -VolumeType. 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro de chave-chave de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro chave-chave de encriptação é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Ativar a encriptação num disco recém-adicionado com O Azure CLI
  O comando Azure CLI fornecerá automaticamente uma nova versão de sequência para si quando executar o comando para ativar a encriptação. Valores aceitáveis para o parâmetro volume-ype são All, OS e Data. Pode ser necessário alterar o parâmetro do tipo volume para OS ou Dados se estiver apenas a encriptar um tipo de disco para o VM. Os exemplos utilizam "All" para o parâmetro do tipo volume. 

-  **Criptografe um VM em execução usando um segredo de cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Criptografe um VM em execução usando KEK para embrulhar o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Ativar a encriptação utilizando a autenticação baseada em certificados de cliente Azure AD.
Pode utilizar a autenticação do certificado de cliente com ou sem KEK. Antes de utilizar os scripts PowerShell, já deve ter o certificado enviado para o cofre de chaves e implantado no VM. Se também estás a usar KEK, o KEK já devia existir. Para mais informações, consulte a autenticação baseada em Certificado para a secção  [AZure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) do artigo pré-requisitos.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Ativar a encriptação utilizando a autenticação baseada em certificados com a Azure PowerShell

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Ativar a encriptação utilizando a autenticação baseada em certificados e um KEK com Azure PowerShell

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Desativar a encriptação
Pode desativar a encriptação utilizando o Azure PowerShell, o Azure CLI ou com um modelo de Gestor de Recursos. 

- **Desative a encriptação do disco com a Azure PowerShell:** Para desativar a encriptação, utilize o [cmdlet de desencriptação Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Desative a encriptação com o Azure CLI:** Para desativar a encriptação, utilize o comando [de desativação de encriptação az vm.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Desative a encriptação com um modelo de gestor de recursos:** 

    1. Clique **em Implementar para Azure** a partir da encriptação do disco [desativar-se no modelo VM do Windows.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)
    2. Selecione a subscrição, grupo de recursos, localização, VM, termos legais e acordo.
    3.  Clique em **Comprar** para desativar a encriptação do disco num VM do Windows em execução. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visão geral da encriptação do disco Azure](disk-encryption-overview.md)
