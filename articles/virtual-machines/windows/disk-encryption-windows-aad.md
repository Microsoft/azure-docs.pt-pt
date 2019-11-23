---
title: Azure Disk Encryption com o Azure AD para VMs do Windows (versão anterior)
description: Este artigo fornece instruções sobre como ativar o Microsoft disco encriptação para o Windows VMs IaaS do Azure.
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


Pode ativar vários cenários de encriptação de disco e os passos podem variar de acordo com o cenário. As secções seguintes abrangem os cenários mais detalhadamente para VMs de IaaS do Windows. Antes de poder utilizar a encriptação de disco, o [pré-requisitos do Azure Disk Encryption](disk-encryption-overview-aad.md) devem ser concluídas. 


>[!IMPORTANT]
> - Você deve [fazer um instantâneo](snapshot-copy-managed-disk.md) e/ou criar um backup antes que os discos sejam criptografados. As cópias de segurança Certifique-se de que uma opção de recuperação é possível que ocorra uma falha inesperada durante a encriptação. As VMs com discos geridos requerem uma cópia de segurança antes de ocorre de encriptação. Depois que um backup é feito, você pode usar o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para criptografar discos gerenciados especificando o parâmetro-skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs criptografadas, consulte [fazer backup e restaurar a VM do Azure criptografada](../../backup/backup-azure-vms-encryption.md). 
>
> - Criptografar ou desabilitar a criptografia pode fazer com que uma VM seja reinicializada.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Ativar a encriptação em novas VMs de IaaS criados a partir do Marketplace
Pode ativar a encriptação de disco na nova VM do Windows de IaaS do Marketplace no Azure através de um modelo do Resource Manager. O modelo cria uma nova VM Windows criptografado com a imagem de galeria do Windows Server 2012.

1. Sobre o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image), clique em **implementar no Azure**.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, os termos legais e contrato. Clique em **Compra** para implementar uma nova VM de IaaS em que a encriptação está ativada.

3. Depois de implementar o modelo, verifique se o estado de encriptação da VM com o seu método preferencial:
     - Certifique-se com a CLI do Azure utilizando o [show do az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-show) comando. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Verifique com Azure PowerShell usando o cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Selecione a VM, em seguida, clique em **discos** sob a **definições** cabeçalho para verificar o estado de encriptação no portal. No gráfico de no **Encryption**, verá se estiver ativada. 
           ![portal do Azure-criptografia de disco habilitada](../media/disk-encryption/disk-encryption-fig2.png)

A tabela a seguir lista os parâmetros de modelo do Resource Manager para novas VMs do cenário do Marketplace usando a ID do cliente do Azure AD:

| Parâmetro | Descrição | 
| --- | --- |
| adminUserName | Nome de utilizador de administrador para a máquina virtual. |
| adminPassword | Senha de usuário de administrador para a máquina virtual. |
| newStorageAccountName | Nome da conta do storage para armazenar dados VHDs e SO. |
| vmSize | Tamanho da VM. Atualmente, a série A Standard apenas, D e G é suportado. |
| virtualNetworkName | Nome da VNet ao qual a NIC de VM devem pertencer à. |
| subnetName | Nome da sub-rede na VNet que a NIC de VM devem pertencer à. |
| AADClientID | ID de cliente da aplicação do Azure AD que tenha permissões para escrever segredos para o seu Cofre de chaves. |
| AADClientSecret | Segredo do cliente de aplicação do Azure AD que tenha permissões para escrever segredos para o seu Cofre de chaves. |
| keyVaultURL | URL do que a chave do BitLocker deve ser carregada para o Cofre de chaves. Pode obtê-lo utilizando o cmdlet `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` ou a CLI do Azure `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | URL da chave de encriptação de chave que é utilizada para encriptar a chave gerada do BitLocker (opcional). </br> </br>KeyEncryptionKeyURL é um parâmetro opcional. Pode levar seu próprio KEK para proteger ainda mais a chave de encriptação de dados (segredo da frase de acesso) no seu Cofre de chaves. |
| keyVaultResourceGroup | Grupo de recursos do Cofre de chaves. |
| vmName | Nome da VM que a operação de encriptação está a ser efetuada numa. |


## <a name="bkmk_RunningWinVM"></a> Ativar a encriptação em existente ou executar VMs do Windows de IaaS
Neste cenário, pode ativar a encriptação com um modelo, cmdlets do PowerShell ou comandos da CLI. As secções seguintes explicam como ativar o Azure Disk Encryption em mais detalhes. 


### <a name="bkmk_RunningWinVMPSH"></a> Ativar a encriptação em existente ou executar VMs com o Azure PowerShell 
Use o cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para habilitar a criptografia em uma máquina virtual IaaS em execução no Azure. Para obter informações sobre como ativar a encriptação com Azure Disk Encryption utilizando cmdlets do PowerShell, consulte as mensagens de blogue [explorar o Azure Disk Encryption com o Azure PowerShell - parte 1](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [explorar o Azure Disk Encryption com o Azure PowerShell - parte 2](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Criptografar uma VM em execução usando um segredo do cliente:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM, Cofre de chaves, uma aplicação do AAD e segredo do cliente devem já foram criadas como pré-requisitos. Substitua MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, meu-AAD-Client-ID e meu-AAD-Client-Secret pelos valores.
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
- **Encriptar uma VM em execução usando KEK para encapsular o segredo do cliente:** Azure Disk Encryption permite-lhe especificar uma chave existente no seu Cofre de chaves para encapsular os segredos de encriptação de disco que foram gerados ao ativar a encriptação. Quando é especificada uma chave de encriptação de chave, o Azure Disk Encryption utiliza essa chave para encapsular os segredos de encriptação antes de escrever para o Key Vault. 

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
   > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão criptografados:** Para verificar o status de criptografia de uma VM IaaS, use o cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Desativar a encriptação de disco:** para desativar a encriptação, utilize o [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Ativar a encriptação em existente ou executar VMs com a CLI do Azure
Utilize o [ativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-enable) comando para ativar a encriptação numa máquina virtual de IaaS em execução no Azure.

- **Encripte uma VM em execução usando um segredo do cliente:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Encripte uma VM em execução usando KEK para encapsular o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão criptografados:** Para verificar o status de criptografia de uma VM IaaS, use o comando [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desativar a encriptação:** para desativar a encriptação, utilize o [desativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-disable) comando. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="bkmk_RunningWinVMwRM"> </a>Usando o modelo do Resource Manager
Pode ativar a encriptação de disco no existente ou executar VMs do Windows de IaaS no Azure utilizando o [modelo do Resource Manager para encriptar uma VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. No modelo de início rápido do Azure, clique em **implementar no Azure**.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, os termos legais e contrato. Clique em **Compra** para ativar a encriptação na VM de IaaS existente ou em execução.

A tabela seguinte lista os parâmetros de modelo do Resource Manager para existente ou executar VMs com um ID de cliente do Azure AD:

| Parâmetro | Descrição |
| --- | --- |
| AADClientID | ID de cliente da aplicação do Azure AD que tenha permissões para escrever segredos para o Cofre de chaves. |
| AADClientSecret | Segredo do cliente de aplicação do Azure AD que tenha permissões para escrever segredos para o Cofre de chaves. |
| keyVaultName | Nome do Cofre de chaves que a chave do BitLocker deve ser enviada para. Você pode obtê-lo usando o cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` ou o comando CLI do Azure `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | URL da chave de encriptação de chave que é utilizada para encriptar a chave gerada do BitLocker. Este parâmetro é opcional se selecionou **nokek** na lista pendente UseExistingKek. Se selecionou **kek** na lista pendente UseExistingKek, tem de introduzir o _keyEncryptionKeyURL_ valor. |
| VolumeType | Tipo de volume que a operação de criptografia é executada em. Os valores válidos são _SO_, _dados_, e _todos os_. |
| sequenceVersion | Versão de sequência da operação de disco BitLocker. Incremente este número de versão sempre que uma operação de encriptação de disco é realizada na mesma VM. |
| vmName | Nome da VM que a operação de encriptação está a ser efetuada numa. |


## <a name="bkmk_VHDpre"> </a>Novas VMs IaaS, criado a partir de chaves VHD de encriptação encriptadas de cliente
Neste cenário, pode ativar a encriptação com o modelo do Resource Manager, cmdlets do PowerShell ou comandos da CLI. As secções seguintes explicam em maior detalhe o modelo do Resource Manager e os comandos da CLI. 

Utilize as instruções no apêndice para preparar imagens encriptadas que podem ser utilizadas no Azure. Depois da imagem é criada, pode utilizar os passos na secção seguinte para criar uma VM do Azure encriptadas.

* [Preparar um VHD do Windows encriptadas](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="bkmk_VHDprePSH"> </a> Encriptar VMs com VHDs encriptadas com o Azure PowerShell
Você pode habilitar a criptografia de disco em seu VHD criptografado usando o cmdlet do PowerShell [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). O exemplo a seguir dá-lhe alguns parâmetros comuns. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados adicionados recentemente
Pode [adicione um novo disco a uma VM do Windows com o PowerShell](attach-disk-ps.md), ou [através do portal do Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco adicionado recentemente com o Azure PowerShell
 Quando utilizar o Powershell para encriptar um disco novo para VMs do Windows, uma nova versão de seqüência deve ser especificada. A versão de sequência tem de ser exclusivo. O script a seguir gera um GUID para a versão de sequência. Em alguns casos, um disco de dados adicionados recentemente pode estar encriptado automaticamente pela extensão do Azure Disk Encryption. Encriptação automática ocorre normalmente quando a VM for reiniciado depois do novo disco fica online. Isto é habitualmente causado porque "All" foi especificado para o tipo de volume, quando a encriptação de disco tiver sido executado na VM. Se a criptografia automática ocorrer em um disco de dados recém-adicionado, recomendamos executar o cmdlet Set-AzVmDiskEncryptionExtension novamente com a nova versão de sequência. Se o novo disco de dados é automaticamente encriptado e não pretender a encriptar, desencriptar todas as unidades em primeiro lugar, em seguida, criptografar novamente com uma nova versão de sequência especificando o sistema operacional para o tipo de volume. 
 

-  **Criptografar uma VM em execução usando um segredo do cliente:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM, Cofre de chaves, uma aplicação do AAD e segredo do cliente devem já foram criadas como pré-requisitos. Substitua MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, meu-AAD-Client-ID e meu-AAD-Client-Secret pelos valores. Este exemplo utiliza "All" para o parâmetro - VolumeType, que inclui volumes de dados e SO. Se quiser apenas criptografar o volume do sistema operacional, utilize o "SO" para o parâmetro - VolumeType. 

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
- **Encriptar uma VM em execução usando KEK para encapsular o segredo do cliente:** Azure Disk Encryption permite-lhe especificar uma chave existente no seu Cofre de chaves para encapsular os segredos de encriptação de disco que foram gerados ao ativar a encriptação. Quando é especificada uma chave de encriptação de chave, o Azure Disk Encryption utiliza essa chave para encapsular os segredos de encriptação antes de escrever para o Key Vault. Este exemplo utiliza "All" para o parâmetro - VolumeType, que inclui volumes de dados e SO. Se quiser apenas criptografar o volume do sistema operacional, utilize o "SO" para o parâmetro - VolumeType. 

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
    > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Ativar a encriptação num disco adicionado recentemente com a CLI do Azure
  O comando da CLI do Azure fornecerá automaticamente uma nova versão de sequência para quando executar o comando para ativar a encriptação. Os valores aceitáveis para o parâmetro de volume yype são todos os dados e SO. Terá de alterar o parâmetro de tipo de volume para dados de sistema operacional ou se está apenas a encriptar um tipo de disco para a VM. Os exemplos utilizam "All" para o parâmetro de tipo de volume. 

-  **Encripte uma VM em execução usando um segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Encripte uma VM em execução usando KEK para encapsular o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Ative a encriptação com a autenticação de baseada em certificados de cliente do Azure AD.
Pode utilizar a autenticação de certificado de cliente com ou sem KEK. Antes de utilizar os scripts do PowerShell, já deverá ter o certificado carregado para o Cofre de chaves e implementado na VM. Se estiver a utilizar demasiado KEK, a KEK já deve existir. Para obter mais informações, consulte a [autenticação baseada em certificado para o Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) secção do artigo de pré-requisitos.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Ativar a encriptação através da autenticação baseada em certificados com o Azure PowerShell

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
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Ativar a encriptação a utilizar autenticação baseada em certificado e um KEK com o Azure PowerShell

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
Pode desativar a encriptação com o Azure PowerShell, CLI do Azure, ou com um modelo do Resource Manager. 

- **Desativar a encriptação de disco com o Azure PowerShell:** para desativar a encriptação, utilize o [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Desativar a encriptação com a CLI do Azure:** para desativar a encriptação, utilize o [desativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-disable) comando. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Desative a encriptação com um modelo do Resource Manager:** 

    1. Clique em **implementar no Azure** partir do [desativar a encriptação de disco na VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) modelo.
    2. Selecione a subscrição, grupo de recursos, localização, VM, os termos legais e contrato.
    3.  Clique em **Compra** para desativar a encriptação de disco numa VM do Windows em execução. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visão geral de Azure Disk Encryption](disk-encryption-overview.md)
