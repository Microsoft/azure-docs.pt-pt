---
title: Azure Disk Encryption com o Azure AD para VMs do Windows (versão anterior)
description: Este artigo fornece instruções sobre como habilitar a criptografia de disco Microsoft Azure para VMs IaaS do Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: d06be85e4d18bc0867835a307b44ec8813c79d7d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245009"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure Disk Encryption com o Azure AD para VMs do Windows (versão anterior)

**A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicativo do Azure AD para habilitar a criptografia de disco de VM. Com a nova versão, você não precisa mais fornecer as credenciais do Azure AD durante a etapa habilitar criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros de aplicativo do Azure AD usando a nova versão. Para exibir instruções para habilitar a criptografia de disco de VM usando a nova versão, consulte [Azure Disk Encryption para VMs do Windows](disk-encryption-windows.md). As VMs que já foram criptografadas com parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD.**


Você pode habilitar muitos cenários de criptografia de disco e as etapas podem variar de acordo com o cenário. As seções a seguir abrangem os cenários mais detalhadamente para VMs IaaS do Windows. Para poder usar a criptografia de disco, os [pré-requisitos de Azure Disk Encryption](disk-encryption-overview-aad.md) precisam ser concluídos. 


>[!IMPORTANT]
> - Você deve [fazer um instantâneo](snapshot-copy-managed-disk.md) e/ou criar um backup antes que os discos sejam criptografados. Os backups garantem que uma opção de recuperação seja possível se ocorrer uma falha inesperada durante a criptografia. As VMs com discos gerenciados exigem um backup antes que a criptografia ocorra. Depois que um backup é feito, você pode usar o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para criptografar discos gerenciados especificando o parâmetro-skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs criptografadas, consulte [fazer backup e restaurar a VM do Azure criptografada](../../backup/backup-azure-vms-encryption.md). 
>
> - Criptografar ou desabilitar a criptografia pode fazer com que uma VM seja reinicializada.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Habilitar a criptografia em novas VMs IaaS criadas no Marketplace
Você pode habilitar a criptografia de disco em uma nova VM IaaS Windows do Marketplace no Azure usando um modelo do Resource Manager. O modelo cria uma nova VM do Windows criptografada usando a imagem da galeria do Windows Server 2012.

1. No [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image), clique em **implantar no Azure**.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os parâmetros, os termos legais e o contrato. Clique em **comprar** para implantar uma nova VM IaaS na qual a criptografia está habilitada.

3. Depois de implantar o modelo, verifique o status de criptografia da VM usando seu método preferido:
     - Verifique com o CLI do Azure usando o comando [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Verifique com Azure PowerShell usando o cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Selecione a VM e clique em **discos** no título **configurações** para verificar o status de criptografia no Portal. No gráfico em **criptografia**, você verá se ele está habilitado. 
           Portal de ![Azure-criptografia de disco habilitada @ no__t-1

A tabela a seguir lista os parâmetros de modelo do Resource Manager para novas VMs do cenário do Marketplace usando a ID do cliente do Azure AD:

| Parâmetro | Descrição | 
| --- | --- |
| adminUserName | Nome de usuário do administrador para a máquina virtual. |
| adminPassword | Senha de usuário do administrador para a máquina virtual. |
| newStorageAccountName | Nome da conta de armazenamento para armazenar OS VHDs de dados e do sistema operacional. |
| vmSize | Tamanho da VM. Atualmente, há suporte apenas para as séries Standard A, D e G. |
| virtualNetworkName | Nome da VNet à qual a NIC da VM deve pertencer. |
| subnetName | Nome da sub-rede na VNet à qual a NIC da VM deve pertencer. |
| AADClientID | ID do cliente do aplicativo do Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| AADClientSecret | Segredo do cliente do aplicativo do Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| keyVaultURL | URL do cofre de chaves para o qual a chave do BitLocker deve ser carregada. Você pode obtê-lo usando o cmdlet `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` ou o CLI do Azure `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | URL da chave de criptografia de chave usada para criptografar a chave do BitLocker gerada (opcional). </br> </br>KeyEncryptionKeyURL é um parâmetro opcional. Você pode trazer seu próprio KEK para proteger ainda mais a chave de criptografia de dados (senha secreta) no cofre de chaves. |
| keyVaultResourceGroup | Grupo de recursos do cofre de chaves. |
| vmName | Nome da VM na qual a operação de criptografia deve ser executada. |


## <a name="bkmk_RunningWinVM"></a>Habilitar a criptografia em VMs IaaS Windows existentes ou em execução
Nesse cenário, você pode habilitar a criptografia usando um modelo, cmdlets do PowerShell ou comandos da CLI. As seções a seguir explicam mais detalhadamente como habilitar Azure Disk Encryption. 


### <a name="bkmk_RunningWinVMPSH"></a>Habilitar a criptografia em VMs existentes ou em execução com Azure PowerShell 
Use o cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para habilitar a criptografia em uma máquina virtual IaaS em execução no Azure. Para obter informações sobre como habilitar a criptografia com Azure Disk Encryption usando cmdlets do PowerShell, consulte as postagens de blog [explorar Azure Disk Encryption com Azure PowerShell-parte 1](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [explorar Azure Disk Encryption com Azure PowerShell parte 2](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Criptografar uma VM em execução usando um segredo do cliente:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM, o cofre de chaves, o aplicativo do AAD e o segredo do cliente já devem ter sido criados como pré-requisitos. Substitua MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, meu-AAD-Client-ID e meu-AAD-Client-Secret pelos valores.
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
- **Criptografe uma VM em execução usando Kek para encapsular o segredo do cliente:** Azure Disk Encryption permite especificar uma chave existente no cofre de chaves para encapsular os segredos de criptografia de disco que foram gerados ao habilitar a criptografia. Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para encapsular os segredos de criptografia antes de gravar em Key Vault. 

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
   > A sintaxe para o valor do parâmetro Disk-Encryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[Subscription-ID-GUID]/resourceGroups/[nome-do-grupo-de-recursos]/providers/Microsoft.KeyVault/vaults/[keyvault-nome]</br> A sintaxe para o valor do parâmetro Key-Encryption-Key é o URI completo para o KEK como em: https://[keyvault-Name]. Vault. Azure. net/Keys/[kekname]/[Kek-Unique-ID] 

- **Verifique se os discos estão criptografados:** Para verificar o status de criptografia de uma VM IaaS, use o cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Desabilitar a criptografia de disco:** Para desabilitar a criptografia, use o cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Habilitar a criptografia em VMs existentes ou em execução com CLI do Azure
Use o comando [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) para habilitar a criptografia em uma máquina virtual IaaS em execução no Azure.

- **Criptografar uma VM em execução usando um segredo do cliente:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Criptografe uma VM em execução usando KEK para encapsular o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A sintaxe para o valor do parâmetro Disk-Encryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[Subscription-ID-GUID]/resourceGroups/[nome-do-grupo-de-recursos]/providers/Microsoft.KeyVault/vaults/[keyvault-nome] </br> A sintaxe para o valor do parâmetro Key-Encryption-Key é o URI completo para o KEK como em: https://[keyvault-Name]. Vault. Azure. net/Keys/[kekname]/[Kek-Unique-ID] 

- **Verifique se os discos estão criptografados:** Para verificar o status de criptografia de uma VM IaaS, use o comando [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desabilitar criptografia:** Para desabilitar a criptografia, use o comando [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="bkmk_RunningWinVMwRM"> </a>Usando o modelo do Resource Manager
Você pode habilitar a criptografia de disco em VMs do Windows IaaS existentes ou em execução no Azure usando o [modelo do Resource Manager para criptografar uma VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. No modelo de início rápido do Azure, clique em **implantar no Azure**.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os parâmetros, os termos legais e o contrato. Clique em **comprar** para habilitar a criptografia na VM IaaS existente ou em execução.

A tabela a seguir lista os parâmetros de modelo do Resource Manager para VMs existentes ou em execução que usam uma ID de cliente do Azure AD:

| Parâmetro | Descrição |
| --- | --- |
| AADClientID | ID do cliente do aplicativo do Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| AADClientSecret | Segredo do cliente do aplicativo do Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| keyVaultName | Nome do cofre de chaves para o qual a chave do BitLocker deve ser carregada. Você pode obtê-lo usando o cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` ou o comando CLI do Azure `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | URL da chave de criptografia de chave usada para criptografar a chave do BitLocker gerada. Esse parâmetro é opcional se você selecionar **nokek** na lista suspensa UseExistingKek. Se você selecionar **Kek** na lista suspensa UseExistingKek, deverá inserir o valor de _keyEncryptionKeyURL_ . |
| volumeType | Tipo de volume no qual a operação de criptografia é executada. Os valores válidos são _os_, _Data_e _All_. |
| sequenceVersion | Versão de sequência da operação do BitLocker. Incrementar esse número de versão sempre que uma operação de criptografia de disco for executada na mesma VM. |
| vmName | Nome da VM na qual a operação de criptografia deve ser executada. |


## <a name="bkmk_VHDpre"> </a>Novas VMs IaaS criadas do VHD criptografado pelo cliente e chaves de criptografia
Nesse cenário, você pode habilitar a criptografia usando o modelo do Resource Manager, cmdlets do PowerShell ou comandos da CLI. As seções a seguir explicam mais detalhadamente o modelo do Resource Manager e os comandos da CLI. 

Use as instruções no apêndice para preparar imagens previamente criptografadas que podem ser usadas no Azure. Depois que a imagem for criada, você poderá usar as etapas na próxima seção para criar uma VM do Azure criptografada.

* [Preparar um VHD do Windows previamente criptografado](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="bkmk_VHDprePSH"></a> Criptografar VMs com VHDs previamente criptografados com Azure PowerShell
Você pode habilitar a criptografia de disco em seu VHD criptografado usando o cmdlet do PowerShell [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). O exemplo a seguir fornece alguns parâmetros comuns. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Habilitar a criptografia em um disco de dados recém-adicionado
Você pode [Adicionar um novo disco a uma VM do Windows usando o PowerShell](attach-disk-ps.md)ou [por meio do portal do Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Habilitar a criptografia em um disco recém-adicionado com Azure PowerShell
 Ao usar o PowerShell para criptografar um novo disco para VMs do Windows, uma nova versão de sequência deve ser especificada. A versão da sequência deve ser exclusiva. O script a seguir gera um GUID para a versão de sequência. Em alguns casos, um disco de dados recém-adicionado pode ser criptografado automaticamente pela extensão de Azure Disk Encryption. A criptografia automática geralmente ocorre quando a VM é reiniciada depois que o novo disco fica online. Isso geralmente é causado porque "All" foi especificado para o tipo de volume quando a criptografia de disco foi executada anteriormente na VM. Se a criptografia automática ocorrer em um disco de dados recém-adicionado, recomendamos executar o cmdlet Set-AzVmDiskEncryptionExtension novamente com a nova versão de sequência. Se o novo disco de dados for criptografado automaticamente e você não quiser ser criptografado, descriptografe todas as unidades primeiro e criptografe novamente com uma nova versão de sequência especificando o sistema operacional para o tipo de volume. 
 

-  **Criptografar uma VM em execução usando um segredo do cliente:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM, o cofre de chaves, o aplicativo do AAD e o segredo do cliente já devem ter sido criados como pré-requisitos. Substitua MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, meu-AAD-Client-ID e meu-AAD-Client-Secret pelos valores. Este exemplo usa "All" para o parâmetro-Volumetype, que inclui o sistema operacional e os volumes de dados. Se você quiser criptografar apenas o volume do sistema operacional, use "OS" para o parâmetro-Volumetype. 

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
- **Criptografe uma VM em execução usando Kek para encapsular o segredo do cliente:** Azure Disk Encryption permite especificar uma chave existente no cofre de chaves para encapsular os segredos de criptografia de disco que foram gerados ao habilitar a criptografia. Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para encapsular os segredos de criptografia antes de gravar em Key Vault. Este exemplo usa "All" para o parâmetro-Volumetype, que inclui o sistema operacional e os volumes de dados. Se você quiser criptografar apenas o volume do sistema operacional, use "OS" para o parâmetro-Volumetype. 

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
    > A sintaxe para o valor do parâmetro Disk-Encryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[Subscription-ID-GUID]/resourceGroups/[nome-do-grupo-de-recursos]/providers/Microsoft.KeyVault/vaults/[keyvault-nome]</br> A sintaxe para o valor do parâmetro Key-Encryption-Key é o URI completo para o KEK como em: https://[keyvault-Name]. Vault. Azure. net/Keys/[kekname]/[Kek-Unique-ID] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Habilitar a criptografia em um disco recém-adicionado com CLI do Azure
  O comando CLI do Azure fornecerá automaticamente uma nova versão de sequência para você quando você executar o comando para habilitar a criptografia. Os valores aceitáveis para o parâmetro volume-yype são todos, so e dados. Talvez seja necessário alterar o parâmetro de tipo de volume para os dados ou o sistema operacional se você estiver criptografando apenas um tipo de disco para a VM. Os exemplos usam "All" para o parâmetro de tipo de volume. 

-  **Criptografar uma VM em execução usando um segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Criptografe uma VM em execução usando KEK para encapsular o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Habilite a criptografia usando a autenticação baseada em certificado do cliente do Azure AD.
Você pode usar a autenticação de certificado de cliente com ou sem KEK. Antes de usar os scripts do PowerShell, você já deve ter o certificado carregado no cofre de chaves e implantado na VM. Se você estiver usando KEK também, o KEK já deverá existir. Para obter mais informações, consulte a seção [autenticação baseada em certificado para o Azure ad](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) do artigo pré-requisitos.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Habilitar a criptografia usando a autenticação baseada em certificado com o Azure PowerShell

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
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Habilitar a criptografia usando a autenticação baseada em certificado e uma KEK com Azure PowerShell

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

## <a name="disable-encryption"></a>Desabilitar criptografia
Você pode desabilitar a criptografia usando Azure PowerShell, o CLI do Azure ou com um modelo do Resource Manager. 

- **Desabilitar a criptografia de disco com o Azure PowerShell:** Para desabilitar a criptografia, use o cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Desabilite a criptografia com o CLI do Azure:** Para desabilitar a criptografia, use o comando [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Desabilitar a criptografia com um modelo do Resource Manager:** 

    1. Clique em **implantar no Azure** no modelo desabilitar a criptografia de [disco no Windows VM em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) .
    2. Selecione a assinatura, o grupo de recursos, o local, a VM, os termos legais e o contrato.
    3.  Clique em **comprar** para desabilitar a criptografia de disco em uma VM do Windows em execução. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visão geral de Azure Disk Encryption](disk-encryption-overview.md)
