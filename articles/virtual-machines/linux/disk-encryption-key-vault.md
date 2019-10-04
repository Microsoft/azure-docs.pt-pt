---
title: Criando e configurando um cofre de chaves para Azure Disk Encryption
description: Este artigo fornece etapas para criar e configurar um cofre de chaves para uso com Azure Disk Encryption
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 90306c55d976670f432d146d94764c4d90b8667d
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828493"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Criando e configurando um cofre de chaves para Azure Disk Encryption

Azure Disk Encryption usa Azure Key Vault para controlar e gerenciar chaves de criptografia de disco e segredos.  Para obter mais informações sobre os cofres de chaves, consulte [introdução ao Azure Key Vault](../../key-vault/key-vault-get-started.md) e [proteger o seu Cofre de chave](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Se você tiver usado anteriormente Azure Disk Encryption com o Azure AD para criptografar uma VM, você deve continuar usando essa opção para criptografar sua VM. Consulte [criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md) para obter detalhes.

Criar e configurar um cofre de chaves para uso com o Azure Disk Encryption envolve três etapas:

1. Criar um grupo de recursos, se necessário.
2. Criando um cofre de chaves. 
3. Definindo políticas de acesso avançado do cofre de chaves.

Essas etapas são ilustradas nos seguintes guias de início rápido:

- [Criar e criptografar uma VM Linux com CLI do Azure](disk-encryption-cli-quickstart.md)
- [Criar e criptografar uma VM Linux com Azure PowerShell](disk-encryption-cli-quickstart.md)

Você também pode, se desejar, gerar ou importar uma chave de criptografia de chave (KEK).

> [!Note]
> As etapas neste artigo são automatizadas no [script da CLI de Azure Disk Encryption pré-requisitos](https://github.com/ejarvi/ade-cli-getting-started) e [Azure Disk Encryption script do PowerShell de pré-requisitos](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Instalar ferramentas e conectar-se ao Azure

As etapas neste artigo podem ser concluídas com o [CLI do Azure](/cli/azure/), o [módulo Azure PowerShell Az](/powershell/azure/overview)ou o [portal do Azure](https://portal.azure.com). 

Embora o portal possa ser acessado por meio de seu navegador, CLI do Azure e Azure PowerShell exigem a instalação local; consulte criptografia de disco [Azure para Linux: Instale as ferramentas @ no__t-0 para obter detalhes.

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Antes de usar o CLI do Azure ou Azure PowerShell, você deve primeiro se conectar à sua assinatura do Azure. Você faz isso [entrando com CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [entrando com o Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)ou fornecendo suas credenciais para o portal do Azure quando solicitado.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

*Se você já tiver um grupo de recursos, poderá pular para [criar um cofre de chaves](#create-a-key-vault).*

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

Crie um grupo de recursos usando o comando [AZ Group create](/cli/azure/group?view=azure-cli-latest#az-group-create) CLI do Azure, o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell ou o [portal do Azure](https://portal.azure.com).

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

*Se você já tiver um cofre de chaves, poderá pular para [definir as políticas de acesso avançado do cofre de chaves](#set-key-vault-advanced-access-policies).*

Crie um cofre de chaves usando o comando [AZ keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) CLI do Azure, o comando [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) do Azure PowerShell, o [portal do Azure](https://portal.azure.com)ou um [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Para garantir que os segredos de criptografia não ultrapassem os limites regionais, Azure Disk Encryption exige que o Key Vault e as VMs sejam colocalizadas na mesma região. Crie e use um Key Vault que esteja na mesma região que as VMs a serem criptografadas. 

Cada Key Vault deve ter um nome exclusivo. Substitua < seu-Unique-keyvault-Name > pelo nome do seu cofre de chaves nos exemplos a seguir.

### <a name="azure-cli"></a>CLI do Azure

Ao criar um cofre de chaves usando CLI do Azure, adicione o sinalizador "--habilitado-para-criptografia de disco".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Ao criar um cofre de chaves usando Azure PowerShell, adicione o sinalizador "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Modelo do Resource Manager

Você também pode criar um cofre de chaves usando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. No modelo de início rápido do Azure, clique em **implementar no Azure**.
2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, o nome do Key Vault, a ID do objeto, os termos legais e o contrato e clique em **comprar**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Definir políticas de acesso avançado do cofre de chaves

A plataforma do Azure precisa de aceder a chaves de encriptação ou segredos no Cofre de chaves para que fiquem disponíveis para a VM para o arranque e a desencriptação de volumes. 

Se você não habilitou o cofre de chaves para criptografia de disco, implantação ou implantação de modelo no momento da criação (conforme demonstrado na etapa anterior), você deve atualizar suas políticas de acesso avançadas.  

### <a name="azure-cli"></a>CLI do Azure

Uso [atualização do Cofre de chaves de az](/cli/azure/keyvault#az-keyvault-update) para ativar a encriptação de disco para o Cofre de chaves. 

 - **Habilitar Key Vault para criptografia de disco:** Habilitado para criptografia de disco é necessário. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Habilite Key Vault para implantação, se necessário:** Permite que o provedor de recursos Microsoft. Compute recupere segredos desse cofre de chaves quando esse cofre de chaves é referenciado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Habilite Key Vault para implantação de modelo, se necessário:** Permitir que o Gerenciador de recursos recupere segredos do cofre.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Use o cmdlet do PowerShell do cofre de chaves [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para habilitar a criptografia de disco para o cofre de chaves.

  - **Habilitar Key Vault para criptografia de disco:** O EnabledForDiskEncryption é necessário para a criptografia de disco do Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Habilite Key Vault para implantação, se necessário:** Permite que o provedor de recursos Microsoft. Compute recupere segredos desse cofre de chaves quando esse cofre de chaves é referenciado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Habilite Key Vault para implantação de modelo, se necessário:** Permite que Azure Resource Manager obtenha segredos desse cofre de chaves quando esse cofre de chaves é referenciado em uma implantação de modelo.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Portal do Azure

1. Selecione o cofre de chaves, acesse **políticas de acesso**e **clique para mostrar políticas de acesso avançadas**.
2. Selecione a caixa rotulada **ativar o acesso ao Azure Disk Encryption para encriptação de volume**.
3. Selecione **ativar o acesso às máquinas de virtuais do Azure para a implantação** e/ou **ativar o acesso ao Azure Resource Manager para a implementação de modelo**, se for necessário. 
4. Clique em **Guardar**.

    ![Cofre de chaves do Azure, as políticas de acesso avançadas](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Configurar uma chave de criptografia de chave (KEK)

Se pretender utilizar uma chave de encriptação de chaves (KEK) para uma camada adicional de segurança para as chaves de encriptação, adicione um KEK ao seu Cofre de chaves. Quando é especificada uma chave de encriptação de chave, o Azure Disk Encryption utiliza essa chave para encapsular os segredos de encriptação antes de escrever para o Key Vault.

Você pode gerar um novo KEK usando o comando CLI do Azure [AZ keyvault Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) , o cmdlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) ou o [portal do Azure](https://portal.azure.com/). Você deve gerar um tipo de chave RSA; Azure Disk Encryption ainda não dá suporte ao uso de chaves de curva elíptica.

Em vez disso, você pode importar um KEK do HSM de gerenciamento de chaves local. Para obter mais informações, consulte [documentação do Cofre de chave](../../key-vault/key-vault-hsm-protected-keys.md). 

As URLs de KEK do cofre de chaves devem ter controle de versão. Azure impõe essa restrição de controle de versão. Para segredo válido e KEK URLs, consulte os exemplos seguintes:

* Exemplo de uma URL secreta válida: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Exemplo de uma URL KEK válida: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

O Azure Disk Encryption não suporta a especificação de números de porta como parte de segredos do Cofre de chaves e URLs de KEK. Para obter exemplos de URLs de Cofre de chaves suportadas e não suportados, consulte os exemplos seguintes:

  * URL do cofre de chaves aceitável: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL do cofre de chaves inaceitável: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>CLI do Azure

Use o comando CLI do Azure [AZ keyvault Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) para gerar um novo Kek e armazená-lo em seu cofre de chaves.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. Em vez disso, você pode importar uma chave privada usando o comando CLI do Azure [AZ keyvault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) :

Em ambos os casos, você fornecerá o nome de seu KEK para o parâmetro CLI do Azure [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --Key-Encryption-Key. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Use o cmdlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) para gerar um novo Kek e armazená-lo em seu cofre de chaves.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Em vez disso, você pode importar uma chave privada usando o comando Azure PowerShell [AZ keyvault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) .

Em ambos os casos, você fornecerá a ID do seu cofre de chaves KEK e a URL de seu KEK para os parâmetros Azure PowerShell [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId e-KeyEncryptionKeyUrl. Observe que este exemplo pressupõe que você esteja usando o mesmo cofre de chaves para a chave de criptografia de disco e o KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>Passos seguintes

- [Script da CLI de pré-requisitos Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script do PowerShell de Azure Disk Encryption pré-requisitos](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Saiba [Azure Disk Encryption cenários em VMs Linux](disk-encryption-linux.md)
- Saiba como [solucionar problemas Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Leia os [scripts de exemplo do Azure Disk Encryption](disk-encryption-sample-scripts.md)
