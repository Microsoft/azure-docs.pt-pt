---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 0aa62a76727f6f913c277100d8c5b36ed1b00110
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618469"
---
## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

*Se já tem um grupo de recursos, pode saltar para [Criar um cofre chave](#create-a-key-vault).*

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

*Se já tem um cofre chave, pode saltar para definir políticas de acesso avançado do cofre de [chaves.](#set-key-vault-advanced-access-policies)*

Criar um cofre chave utilizando o [cofre az criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) o comando Azure CLI, o comando [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell, o [portal Azure,](https://portal.azure.com)ou um modelo de Gestor de [Recursos.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)

>[!WARNING]
> Para garantir que os segredos de encriptação não cruzam os limites regionais, a Encriptação do Disco Azure requer que o Cofre chave e os VMs sejam co-localizados na mesma região. Crie e use um Cofre chave que esteja na mesma região que os VMs para serem encriptados. 

Cada cofre de chaves deve ter um nome único. Substitua <your-unique-keyvault-name> com o nome do seu cofre chave nos seguintes exemplos.

### <a name="azure-cli"></a>CLI do Azure

Ao criar um cofre chave utilizando o Azure CLI, adicione a bandeira de encriptação "-ativada para disco".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Ao criar um cofre de chaves utilizando o Azure PowerShell, adicione a bandeira "EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Modelo do Resource Manager

Também pode criar um cofre chave utilizando o [modelo de Gestor](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)de Recursos .

1. No modelo de arranque rápido azure, clique em **Implementar para Azure**.
2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, nome key vault, ID do objeto, termos legais e acordo, e, em seguida, clique em **Comprar**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Definir políticas de acesso avançado do cofre chave

A plataforma do Azure precisa de aceder a chaves de encriptação ou segredos no Cofre de chaves para que fiquem disponíveis para a VM para o arranque e a desencriptação de volumes. 

Se não permitiu o seu cofre chave para encriptação, implementação ou implantação de modelos de disco no momento da criação (como demonstrado no passo anterior), deve atualizar as suas políticas avançadas de acesso.  

### <a name="azure-cli"></a>CLI do Azure

Utilize a [atualização do keyvault az](/cli/azure/keyvault#az-keyvault-update) para ativar a encriptação do disco para o cofre da chave. 

 - **Ativar o Cofre de Chaves para encriptação de disco:** É necessária encriptação ativa da encriptação do disco. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Ativar o cofre chave para a implantação, se necessário:** Permite ao fornecedor de recursos Microsoft.Compute recuperar segredos deste cofre chave quando este cofre chave é referenciado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Ativar o cofre de chaves para a implementação do modelo, se necessário:** Permita que o Gestor de Recursos recupere segredos do cofre.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Utilize o cofre chave PowerShell cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para ativar a encriptação do disco para o cofre da chave.

  - **Ativar o Cofre de Chaves para encriptação de disco:** Ativada ForDiskEncryption é necessária para encriptação do Disco Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Ativar o cofre chave para a implantação, se necessário:** Permite ao fornecedor de recursos Microsoft.Compute recuperar segredos deste cofre chave quando este cofre chave é referenciado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Ativar o cofre de chaves para a implementação do modelo, se necessário:** Permite ao Gestor de Recursos Azure obter segredos deste cofre chave quando este cofre chave é referenciado numa implementação de modelo.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Portal do Azure

1. Selecione o seu cofre chave, vá às Políticas de **Acesso**e **Clique para mostrar políticas de acesso avançadas**.
2. Selecione a caixa rotulada **Ativar o acesso à encriptação do disco Azure para encriptação de volume**.
3. Selecione **Ativar o acesso a Máquinas Virtuais Azure para implementação** e/ou ativar o acesso ao Gestor de Recursos **Azure para implementação**do modelo, se necessário. 
4. Clique em **Guardar**.

    ![Cofre de chaves do Azure, as políticas de acesso avançadas](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Configurar uma chave de encriptação (KEK)

Se pretender utilizar uma chave de encriptação de chaves (KEK) para uma camada adicional de segurança para as chaves de encriptação, adicione um KEK ao seu Cofre de chaves. Quando é especificada uma chave de encriptação de chave, o Azure Disk Encryption utiliza essa chave para encapsular os segredos de encriptação antes de escrever para o Key Vault.

Pode gerar um novo KEK utilizando a chave azure CLI [az keyvault criar](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) comando, o Cmdlet [Add-AzKeyKey](/powershell/module/az.keyvault/add-azkeyvaultkey) Azure PowerShell, ou o [portal Azure](https://portal.azure.com/). Deve gerar um tipo de chave RSA; A encriptação do disco azure ainda não suporta a utilização de teclas Elípticas Curve.

Em vez disso, pode importar um KEK da sua gestão chave HSM no local. Para mais informações, consulte [a Documentação do Cofre de Chaves](/azure/key-vault/key-vault-hsm-protected-keys).

Os urLs kek do seu cofre devem ser versados. Azure impõe essa restrição de controle de versão. Para segredo válido e KEK URLs, consulte os exemplos seguintes:

* Exemplo de um URL secreto válido: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Exemplo de um URL KEK válido: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

O Azure Disk Encryption não suporta a especificação de números de porta como parte de segredos do Cofre de chaves e URLs de KEK. Para obter exemplos de URLs de Cofre de chaves suportadas e não suportados, consulte os exemplos seguintes:

  * URL aceitável do cofre da chave: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL inaceitável do cofre chave: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>CLI do Azure

Utilize a chave azure CLI [az para criar](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) um comando para gerar um novo KEK e guardá-lo no seu cofre chave.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

Em vez disso, pode importar uma chave privada utilizando o comando de importação de chaves Azure CLI [az keyvault:](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)

Em qualquer dos casos, fornecerá o nome do seu KEK ao ativador de encriptação Azure CLI [az vm](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) ----key-encryption-key--key.-key----key.-key---key.-key---key-parameter. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Utilize o cmdlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) para gerar um novo KEK e guarde-o no seu cofre chave.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Em vez disso, pode importar uma chave privada utilizando o comando de importação de chaves Azure PowerShell [az keyvault.](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)

Em qualquer dos casos, fornecerá o ID do seu cofre de chaves KEK e o URL do seu KEK para o Conjunto [De Conjunto-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId e -KeyCryptonKeyUrl. Note que este exemplo pressupõe que está a usar o mesmo cofre chave tanto para a chave de encriptação do disco como para o KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
