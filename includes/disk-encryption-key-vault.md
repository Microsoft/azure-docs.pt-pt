---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 489ee630deb56aef6c004067f29779053fbcd3e7
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755749"
---
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

*Se já tiver um grupo de recursos, pode saltar para [criar um cofre de chaves.](#create-a-key-vault)*

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

Criar um grupo de recursos utilizando o [grupo Az criar](/cli/azure/group?view=azure-cli-latest#az-group-create) o comando Azure CLI, o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell, ou a partir do [portal Azure](https://portal.azure.com).

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

*Se já tiver um cofre de chaves, pode saltar para [definir as políticas avançadas de acesso do cofre](#set-key-vault-advanced-access-policies)de chaves .*

Crie um cofre-chave utilizando o [keyvault az criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) o comando Azure CLI, o comando [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell, o [portal Azure](https://portal.azure.com), ou um [modelo de Gestor de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> O cofre e os VMs devem estar na mesma subscrição. Além disso, para garantir que os segredos de encriptação não cruzem fronteiras regionais, a Encriptação do Disco Azure requer que o Cofre-Chave e os VMs sejam co-localizados na mesma região. Crie e use um Cofre-Chave que esteja na mesma subscrição e região que os VMs a serem encriptados. 

Cada Cofre-Chave deve ter um nome único. Substitua <o seu nome único de teclado> pelo nome do seu cofre-chave nos seguintes exemplos.

### <a name="azure-cli"></a>CLI do Azure

Ao criar um cofre de chaves utilizando o Azure CLI, adicione a bandeira "ativada para encriptação do disco".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Ao criar um cofre de chaves utilizando a Azure PowerShell, adicione a bandeira "EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Modelo do Resource Manager

Também pode criar um cofre de chaves utilizando o [modelo de Gestor de Recursos.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)

1. No modelo de arranque rápido Azure, clique em **Implementar para Azure** .
2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, nome do Cofre chave, ID do objeto, termos legais e acordo, e, em seguida, clique em **Comprar** . 


##  <a name="set-key-vault-advanced-access-policies"></a>Definir as políticas avançadas de acesso do cofre de chaves

A plataforma Azure precisa de acesso às chaves de encriptação ou segredos no seu cofre chave para os disponibilizar ao VM para iniciar e desencriptar os volumes. 

Se não ativou o cofre-chave para encriptação, implementação ou implementação do modelo no momento da criação (como demonstrado no passo anterior), deve atualizar as suas políticas avançadas de acesso.  

### <a name="azure-cli"></a>CLI do Azure

Utilize [a atualização do keyvault az](/cli/azure/keyvault#az-keyvault-update) para ativar a encriptação do disco para o cofre da chave. 

 - **Ativar o Cofre da Chave para encriptação do disco:** É necessária encriptação ativada para o disco. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Ativar o cofre da chave para a implantação, se necessário:** Permite que o fornecedor de recursos Microsoft.Compute recupere segredos a partir deste cofre chave quando este cofre chave é referenciado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Ativar o cofre da chave para a implementação do modelo, se necessário:** Permita ao Gestor de Recursos recuperar segredos do cofre.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Utilize o cofre powerShell cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para ativar a encriptação do disco para o cofre da chave.

  - **Ativar o Cofre da Chave para encriptação do disco:** EnablediskEncryption é necessário para encriptação do Disco Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Ativar o cofre da chave para a implantação, se necessário:** Permite que o fornecedor de recursos Microsoft.Compute recupere segredos a partir deste cofre chave quando este cofre chave é referenciado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Ativar o cofre da chave para a implementação do modelo, se necessário:** Permite ao Azure Resource Manager obter segredos a partir deste cofre chave quando este cofre de chaves é referenciado numa implementação de modelo.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Portal do Azure

1. Selecione o cofre de chaves, vá às **Políticas de Acesso** e clique para mostrar políticas de acesso **avançadas** .
2. Selecione a caixa com a etiqueta **Ativar o acesso à Encriptação do Disco Azure para encriptação de volume** .
3. **Selecione Permitir o acesso a Máquinas Virtuais Azure para implantação** e/ou Permitir o Acesso ao Gestor de Recursos **Azure para a implementação do modelo,** se necessário. 
4. Clique em **Guardar** .

    ![Azure key vault políticas avançadas de acesso](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Configurar uma chave de encriptação chave (KEK)

Se pretender utilizar uma chave de encriptação (KEK) para uma camada adicional de segurança para chaves de encriptação, adicione um KEK ao seu cofre de chaves. Quando uma chave de encriptação chave é especificada, a Encriptação do Disco Azure usa essa chave para embrulhar os segredos de encriptação antes de escrever para Key Vault.

Pode gerar um novo KEK utilizando a tecla Azure CLI [az keyvault criar](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) comando, o cmdlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) ou o [portal Azure](https://portal.azure.com/). Tem de gerar um tipo de chave RSA; A encriptação do disco Azure ainda não suporta a utilização de teclas elípticas da curva.

Em vez disso, pode importar um KEK da sua gestão de chaves HSM no local. Para obter mais informações, consulte [a Documentação do Cofre-Chave](/azure/key-vault/key-vault-hsm-protected-keys).

O seu cofre-chave KEK URLs tem de ser versão. A Azure impõe esta restrição de versão. Para obter URLs secretos e KEK válidos, consulte os seguintes exemplos:

* Exemplo de um URL secreto válido: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Exemplo de um URL KEK válido: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

A encriptação do disco Azure não suporta especificar números de porta como parte de segredos chave do cofre e URLs KEK. Por exemplo, os URLs de cofre de chaves não suportados e suportados, consulte os seguintes exemplos:

  * URL de cofre de chaves aceitável: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL inaceitável do cofre de chaves: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>CLI do Azure

Utilize a tecla Azure CLI [az keyvault criar](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) comando para gerar um novo KEK e armazená-lo no seu cofre de chaves.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

Em vez disso, pode importar uma chave privada utilizando o comando de importação chave Azure CLI [az:](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)

Em qualquer dos casos, irá fornecer o nome do seu KEK à encriptação Azure CLI [az vm ativar](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) o parâmetro chave de encriptação da chave. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Utilize o cmdlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) para gerar um novo KEK e guarde-o no cofre da chave.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Em vez disso, pode importar uma chave privada utilizando o comando de importação chave Azure PowerShell [az keyvault.](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)

Em qualquer dos casos, irá fornecer o ID do seu cofre de tecla KEK e o URL do seu KEK para os parâmetros Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId e -KeyEncryptionKeyUrl. Note que este exemplo pressupõe que está a usar o mesmo cofre chave tanto para a chave de encriptação do disco como para o KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
