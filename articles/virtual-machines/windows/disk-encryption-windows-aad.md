---
title: Encriptação de disco azure com AD Azure para VMs do Windows (versão anterior)
description: Este artigo fornece instruções sobre a ativação da encriptação do disco Microsoft Azure para VMs Windows IaaS.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 8677fa2620c1edc646dcffe120938f03fd13a0e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085626"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Encriptação de disco azure com AD Azure para VMs do Windows (versão anterior)

**O novo lançamento da Encriptação do Disco Azure elimina a exigência de fornecer um parâmetro de aplicação Azure AD para permitir a encriptação do disco VM. Com o novo lançamento, já não é necessário fornecer credenciais de AD Azure durante a fase de encriptação ativa. Todos os novos VMs devem ser encriptados sem os parâmetros da aplicação Azure AD utilizando o novo lançamento. Para visualizar instruções para ativar a encriptação do disco VM utilizando a nova versão, consulte a encriptação do [disco Azure para Windows VMS](disk-encryption-windows.md). Os VMs que já estavam encriptados com parâmetros de aplicação Azure AD ainda são suportados e devem continuar a ser mantidos com a sintaxe AAD.**


Pode ativar muitos cenários de encriptação de discos, e os passos podem variar de acordo com o cenário. As seguintes secções cobrem os cenários com maior detalhe para VMs Windows IaaS. Antes de poder utilizar a encriptação do disco, os [pré-requisitos](disk-encryption-overview-aad.md) de encriptação do disco Azure têm de ser preenchidos. 


>[!IMPORTANT]
> - Deve [tirar uma fotografia](snapshot-copy-managed-disk.md) e/ou criar uma cópia de segurança antes de os discos serem encriptados. As cópias de segurança garantem que uma opção de recuperação é possível se ocorrer uma falha inesperada durante a encriptação. VMs com discos geridos requerem uma cópia de segurança antes da encriptação ocorrer. Uma vez feita uma cópia de segurança, pode utilizar o [cmdlet set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para encriptar discos geridos especificando o parâmetro -skipVmBackup. Para obter mais informações sobre como fazer o back-up e restaurar os VMs encriptados, consulte [O Back up e restaure o Azure VM encriptado](../../backup/backup-azure-vms-encryption.md). 
>
> - Encriptar ou desativar encriptação pode fazer com que um VM reinicie.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Ativar a encriptação em novos VMs IaaS criados a partir do Marketplace
Pode ativar a encriptação do disco em novo IaaS Windows VM do Marketplace em Azure utilizando um modelo de Gestor de Recursos. O modelo cria um novo VM encriptado do Windows utilizando a imagem da galeria Windows Server 2012.

1. No [modelo de Gestor](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)de Recursos, clique em **Implementar para Azure**.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, termos legais e acordo. Clique em **Comprar** para implementar um novo VM IaaS onde a encriptação está ativada.

3. Depois de implementar o modelo, verifique o estado de encriptação VM utilizando o seu método preferido:
     - Verifique com o Azure CLI utilizando o comando de [encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-show) 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Verifique com o Azure PowerShell utilizando o [get-AzVmDiskCryptonStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet. 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Selecione o VM e, em seguida, clique em **Discos** sob a rubrica **Definições** para verificar o estado de encriptação no portal. Na tabela em **Encriptação,** verá se está ativada. 
           ![Portal Azure - Encriptação de disco ativada](../media/disk-encryption/disk-encryption-fig2.png)

A tabela seguinte lista os parâmetros do modelo do Gestor de Recursos para novos VMs do cenário do Marketplace utilizando o ID do cliente Azure AD:

| Parâmetro | Descrição | 
| --- | --- |
| adminUserName | Nome de utilizador administrativo para a máquina virtual. |
| adminPassword | Admin user password for the virtual machine. |
| newStorageAccountName | Nome da conta de armazenamento para armazenar OS e VHDs de dados. |
| vmSize | Tamanho do VM. Atualmente, apenas as séries Standard A, D e G são suportadas. |
| virtualNetworkName | Nome da VNet a que o VM NIC deve pertencer. |
| subnome | Nome da sub-rede na VNet a que o VM NIC deve pertencer. |
| AADClientID | Identificação do cliente da aplicação Azure AD que tem permissões para escrever segredos para o seu cofre chave. |
| AADClientSecret | Segredo do cliente da aplicação Azure AD que tem permissões para escrever segredos para o seu cofre chave. |
| keyVaultURL | URL do cofre chave para o que a chave BitLocker deve ser carregada. Pode obtê-lo utilizando `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` o cmdlet ou o Azure CLI`az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | URL da chave de encriptação que é usada para encriptar a chave BitLocker gerada (opcional). </br> </br>KeyEncryptionKeyURL é um parâmetro opcional. Pode trazer o seu próprio KEK para salvaguardar ainda mais a chave de encriptação de dados (segredo de palavra-passe) no seu cofre chave. |
| keyVaultResourceGroup | Grupo de recursos do cofre chave. |
| vmName | Nome do VM em que a operação de encriptação deve ser executada. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a>Ativar a encriptação em VMs iaaS existentes ou em execução
Neste cenário, pode ativar a encriptação utilizando um modelo, cmdlets PowerShell ou comandos CLI. As seguintes secções explicam mais detalhadamente como ativar a encriptação do disco Azure. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a>Ativar a encriptação em VMs existentes ou em execução com O PowerShell Azure 
Utilize o [cmdlet set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para permitir a encriptação numa máquina virtual IaaS em funcionamento em Azure. Para obter informações sobre a encriptação de habilitação à encriptação do disco Azure utilizando cmdlets PowerShell, consulte as publicações de blog [Explore Azure Disk Encryption com Azure PowerShell - Parte 1](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e Explore a Encriptação do Disco [Azure com a Azure PowerShell - Parte 2](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Criptografe um VM em execução usando um segredo** de cliente: O script abaixo inicializa as suas variáveis e executa o cmdlet set-AzVMDiskEncryptionExtension. O grupo de recursos, VM, cofre chave, app AAD e segredo de cliente já deveriam ter sido criados como pré-requisitos. Substitua o MyKeyVaultResourceGroup, o MyVirtualMachineResourceGroup, o MySecureVM, o MySecureVault, o My-AAD-client-ID e o my-AAD-client-secret com os seus valores.
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
- **Criptografe um VM em execução usando KEK para embrulhar o segredo do cliente:** A Encriptação do Disco Azure permite especificar uma chave existente no seu cofre chave para embrulhar os segredos de encriptação do disco que foram gerados ao mesmo tempo que permite a encriptação. Quando uma chave de encriptação é especificada, a Encriptação do Disco Azure usa essa chave para embrulhar os segredos de encriptação antes de escrever para key vault. 

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
   > A sintaxe para o valor do parâmetro de cofre de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um VM IaaS, utilize o [cmdlet Get-AzVmDiskCryptonStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Desativar a encriptação do disco:** Para desativar a encriptação, utilize o cmdlet [Desactivação-AzureRmVMDiskCrypton.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Ativar a encriptação em VMs existentes ou em execução com O CLI Azure
Utilize o comando [de encriptação az vm para](/cli/azure/vm/encryption#az-vm-encryption-enable) ativar a encriptação numa máquina virtual IaaS em funcionamento em Azure.

- **Criptografe um VM em execução usando um segredo de cliente:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Criptografe um VM em execução usando KEK para embrulhar o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A sintaxe para o valor do parâmetro de cofre de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um VM IaaS, use o comando de [encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desativar a encriptação:** Para desativar a encriptação, utilize o comando de [desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Usando o modelo de Gestor de Recursos
Pode ativar a encriptação do disco em VMs iaaS existentes ou em execução em Azure, utilizando o modelo de Gestor de [Recursos para encriptar um VM Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. No modelo de arranque rápido azure, clique em **Implementar para Azure**.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, termos legais e acordo. Clique em **Comprar** para ativar a encriptação no VM IaaS existente ou em execução.

A tabela seguinte lista os parâmetros do modelo do Gestor de Recursos para VMs existentes ou em execução que utilizam um ID de cliente Azure AD:

| Parâmetro | Descrição |
| --- | --- |
| AADClientID | Identificação do cliente da aplicação Azure AD que tem permissões para escrever segredos para o cofre chave. |
| AADClientSecret | Segredo do cliente da aplicação Azure AD que tem permissões para escrever segredos para o cofre chave. |
| keyVaultName | Nome do cofre chave para o que a chave BitLocker deve ser enviada. Pode obtê-lo utilizando `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` o cmdlet ou o comando Azure CLI`az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | URL da chave de encriptação que é usada para encriptar a chave BitLocker gerada. Este parâmetro é opcional se selecionar **nokek** na lista de drop-down UseExistingKek. Se selecionar **kek** na lista de drop-down UseExistingKek, deve introduzir o valor _keyEncryptionKeyURL._ |
| volumeType | Tipo de volume em que a operação de encriptação é realizada. Os valores válidos são _OS,_ _Dados,_ e _Tudo._ |
| sequênciaVersãoVersão | Versão de sequência da operação BitLocker. Incremente este número de versão sempre que uma operação de encriptação de disco é realizada no mesmo VM. |
| vmName | Nome do VM em que a operação de encriptação deve ser executada. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Novos VMs IaaS criados a partir de VHD encriptado pelo cliente e chaves de encriptação
Neste cenário, pode ativar a encriptação utilizando o modelo de Gestor de Recursos, cmdlets PowerShell ou comandos CLI. As seguintes secções explicam mais detalhadamente o modelo de Gestor de Recursos e comandos CLI. 

Utilize as instruções no apêndice para preparar imagens pré-encriptadas que possam ser utilizadas em Azure. Após a criação da imagem, pode utilizar os passos na secção seguinte para criar um Azure VM encriptado.

* [Prepare um VHD Windows pré-encriptado](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"> </a> Criptografe VMs com VHDs pré-encriptados com PowerShell Azure
Pode ativar a encriptação do disco no vHD encriptado utilizando o PowerShell cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). O exemplo abaixo dá-lhe alguns parâmetros comuns. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados recém-adicionado
Pode [adicionar um novo disco a um VM windows utilizando powerShell](attach-disk-ps.md), ou [através do portal Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco recém-adicionado com a Azure PowerShell
 Ao utilizar o Powershell para encriptar um novo disco para VMs do Windows, deve ser especificada uma nova versão de sequência. A versão da sequência tem de ser única. O script abaixo gera um GUID para a versão de sequência. Em alguns casos, um disco de dados recentemente adicionado pode ser encriptado automaticamente pela extensão de encriptação do disco Azure. A encriptação automática ocorre geralmente quando o VM reinicia após a onda do novo disco. Isto é normalmente causado porque "All" foi especificado para o tipo de volume quando a encriptação do disco anteriormente funcionava no VM. Se a encriptação automática ocorrer num disco de dados recém-adicionado, recomendamos que volte a executar o cmdlet Set-AzVmDiskEncryptionExtension com nova versão de sequência. Se o seu novo disco de dados estiver encriptado automaticamente e não pretender ser encriptado, desencriptar primeiro todas as unidades e depois voltar a encriptar com uma nova versão de sequência especificando OS para o tipo de volume. 
 

-  **Criptografe um VM em execução usando um segredo** de cliente: O script abaixo inicializa as suas variáveis e executa o cmdlet set-AzVMDiskEncryptionExtension. O grupo de recursos, VM, cofre chave, app AAD e segredo de cliente já deveriam ter sido criados como pré-requisitos. Substitua o MyKeyVaultResourceGroup, o MyVirtualMachineResourceGroup, o MySecureVM, o MySecureVault, o My-AAD-client-ID e o my-AAD-client-secret com os seus valores. Este exemplo utiliza "All" para o parâmetro -VolumeType, que inclui tanto os volumes de OS como os dados. Se pretender encriptar apenas o volume de OS, utilize "OS" para o parâmetro -VolumeType. 

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
- **Criptografe um VM em execução usando KEK para embrulhar o segredo do cliente:** A Encriptação do Disco Azure permite especificar uma chave existente no seu cofre chave para embrulhar os segredos de encriptação do disco que foram gerados ao mesmo tempo que permite a encriptação. Quando uma chave de encriptação é especificada, a Encriptação do Disco Azure usa essa chave para embrulhar os segredos de encriptação antes de escrever para key vault. Este exemplo utiliza "All" para o parâmetro -VolumeType, que inclui tanto os volumes de OS como os dados. Se pretender encriptar apenas o volume de OS, utilize "OS" para o parâmetro -VolumeType. 

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
    > A sintaxe para o valor do parâmetro de cofre de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Ativar a encriptação num disco recém-adicionado com o Azure CLI
  O comando Azure CLI fornecerá automaticamente uma nova versão de sequência para si quando executar o comando para ativar a encriptação. Os valores aceitáveis para o parâmetro volume-yype são All, OS e Data. Pode ser necessário alterar o parâmetro do tipo volume para OS ou Dados se estiver apenas a encriptar um tipo de disco para o VM. Os exemplos usam "All" para o parâmetro do tipo volume. 

-  **Criptografe um VM em execução usando um segredo de cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Criptografe um VM em execução usando KEK para embrulhar o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Ativar a encriptação utilizando a autenticação baseada em certificado de cliente Azure AD.
Pode utilizar a autenticação do certificado de cliente com ou sem KEK. Antes de utilizar os scripts PowerShell, já deve ter o certificado enviado para o cofre da chave e implantado para o VM. Se também está a usar kek, o KEK já deve existir. Para mais informações, consulte a autenticação baseada em Certificado para a secção [Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) do artigo pré-requisitos.


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
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Ativar a encriptação usando a autenticação baseada em certificadoe um KEK com Azure PowerShell

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

- **Desative a encriptação do disco com o Azure PowerShell:** Para desativar a encriptação, utilize o cmdlet [Desactivação-AzureRmVMDiskCrypton.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Desative a encriptação com o Azure CLI:** Para desativar a encriptação, utilize o comando de [desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Desative a encriptação com um modelo de gestor de recursos:** 

    1. Clique em **'Implementar para Azure'** a partir da encriptação do [disco desativar no modelo VM do Windows.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)
    2. Selecione a subscrição, grupo de recursos, localização, VM, termos legais e acordo.
    3.  Clique em **Comprar** para desativar a encriptação do disco num VM do Windows em execução. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visão geral da encriptação do disco azure](disk-encryption-overview.md)
