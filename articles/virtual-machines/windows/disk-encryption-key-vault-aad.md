---
title: Criação e configuração de um cofre chave para encriptação de disco azure com AD Azure (versão anterior)
description: Este artigo fornece pré-requisitos para a utilização da encriptação do disco Microsoft Azure para VMs IaaS.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: c8610beb8903c979f0d5f5e71bd6710a3ccb49bd
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081987"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Criação e configuração de um cofre chave para encriptação de disco azure com AD Azure (versão anterior)

**O novo lançamento da Encriptação do Disco Azure elimina a exigência de fornecer um parâmetro de aplicação Azure AD para permitir a encriptação do disco VM. Com o novo lançamento, já não é necessário fornecer credenciais de AD Azure durante a fase de encriptação ativa. Todos os novos VMs devem ser encriptados sem os parâmetros da aplicação Azure AD utilizando o novo lançamento. Para visualizar instruções para ativar a encriptação do disco VM utilizando a nova versão, consulte a [Encriptação do Disco Azure](disk-encryption-overview.md). Os VMs que já estavam encriptados com parâmetros de aplicação Azure AD ainda são suportados e devem continuar a ser mantidos com a sintaxe AAD.**

A Encriptação azure Disk usa o Cofre de Chave Azure para controlar e gerir chaves e segredos de encriptação do disco.  Para mais informações sobre os cofres chave, consulte [Start start with Azure Key Vault](../../key-vault/key-vault-get-started.md) e Secure your key [vault](../../key-vault/general/secure-your-key-vault.md). 

Criar e configurar um cofre chave para utilização com encriptação de disco azure com AD Azure (versão anterior) envolve três passos:

1. Criar um cofre de chaves. 
2. Criar uma aplicação e diretor de serviço azure AD.
3. Definir a política de acesso do cofre de chaves para a aplicação Azure AD.
4. Definir as políticas avançadas de acesso do cofre de chaves.
 
Também pode, se desejar, gerar ou importar uma chave de encriptação (KEK).

Consulte o principal [Criar e configurar um cofre chave para](disk-encryption-key-vault.md) o artigo de encriptação do disco Azure para obter passos sobre como instalar [ferramentas e ligar-se ao Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure).

> [!Note]
> Os passos neste artigo são automatizados no [pré-requisito de encriptação](https://github.com/ejarvi/ade-cli-getting-started) do disco Azure e na [encriptação do disco Azure pré-requisitos do script PowerShell](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Criar um cofre de chaves 
A Encriptação azure Disk está integrada com o [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerir as chaves e segredos de encriptação do disco na subscrição do cofre chave. Pode criar um cofre de chave ou utilizar um existente para encriptação de disco azure. Para mais informações sobre os cofres chave, consulte [Start start with Azure Key Vault](../../key-vault/key-vault-get-started.md) e Secure your key [vault](../../key-vault/general/secure-your-key-vault.md). Pode utilizar um modelo de Gestor de Recursos, Azure PowerShell ou o Azure CLI para criar um cofre chave. 


>[!WARNING]
>Para garantir que os segredos de encriptação não ultrapassem os limites regionais, a Encriptação do Disco Azure precisa que o Cofre chave e os VMs sejam co-localizados na mesma região. Crie e use um Cofre chave que esteja na mesma região que o VM para ser encriptado. 


### <a name="create-a-key-vault-with-powershell"></a>Crie um cofre chave com powerShell

Pode criar um cofre chave com o Azure PowerShell utilizando o cmdlet [New-AzKeyVault.](/powershell/module/az.keyvault/New-azKeyVault) Para obter cmdlets adicionais para key vault, consulte [Az.KeyVault](/powershell/module/az.keyvault/). 

1. Crie um novo grupo de recursos, se necessário, com o [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Para listar as localizações do centro de dados, utilize [o Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Crie um novo cofre-chave usando [o New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Note o **Nome do Cofre,** **Nome do Grupo de Recursos,** ID de **Recursos,** **Cofre URI,** e o ID do **Objeto** que são devolvidos para posterior utilização quando encriptar os discos. 


### <a name="create-a-key-vault-with-azure-cli"></a>Crie um cofre chave com Azure CLI
Você pode gerir o seu cofre chave com o Azure CLI usando os comandos [az keyvault.](/cli/azure/keyvault#commands) Para criar um cofre chave, use [az keyvault criar](/cli/azure/keyvault#az-keyvault-create).

1. Criar um novo grupo de recursos, se necessário, com o [grupo Az criar](/cli/azure/group#az-group-create). Para listar locais, utilize [alistamentos da conta az](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Crie um novo cofre de chaves usando a criação de [keyvault az](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Note o **Nome do Cofre** (nome), Nome do Grupo de **Recursos,** **ID de Recursos** (ID), Cofre **URI,** e o ID do **Objeto** que são devolvidos para uso posteriormente. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Crie um cofre chave com um modelo de Gestor de Recursos

Pode criar um cofre chave utilizando o [modelo de Gestor](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)de Recursos .

1. No modelo de arranque rápido azure, clique em **Implementar para Azure**.
2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, nome key vault, ID do objeto, termos legais e acordo, e, em seguida, clique em **Comprar**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Criar uma app e diretor de serviço da Azure AD 
Quando precisa de encriptação para ser ativada num VM em funcionamento em Azure, a Encriptação do Disco Azure gera e escreve as chaves de encriptação do seu cofre chave. Gerir chaves de encriptação no seu cofre de chaves requer autenticação Azure AD. Crie uma aplicação Azure AD para o efeito. Para efeitos de autenticação, pode utilizar a autenticação baseada em segredo do cliente ou a [autenticação Azure AD baseada em certificadode cliente.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Criar uma app e diretor de serviço azure AD com a Azure PowerShell 
Para executar os seguintes comandos, obtenha e utilize o [módulo PowerShell AD Azure](/powershell/azure/active-directory/install-adv2). 

1. Utilize o cmdlet PowerShell de [Aplicação PowerShell new-AzADApplication](/powershell/module/az.resources/new-azadapplication) para criar uma aplicação Azure AD. MyApplicationHomePage e myApplicationUri podem ser os valores que desejar.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. O $azureAdApplication.ApplicationId é o Cliente ID da AD Azure e o $aadClientSecret é o segredo do cliente que utilizará mais tarde para ativar a Encriptação do Disco Azure. Proteja o segredo do cliente Azure AD adequadamente. Correr `$azureAdApplication.ApplicationId` irá mostrar-lhe o ApplicationID.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Criar uma app e diretor de serviço azure AD com o Azure CLI

Pode gerir os seus principais de serviço com o Azure CLI utilizando os comandos [az ad sp.](/cli/azure/ad/sp) Para mais informações, consulte [Criar um serviço Azure principal.](/cli/azure/create-an-azure-service-principal-azure-cli)

1. Crie um novo diretor de serviço.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  O appId devolvido é o Id de Cliente Azure AD usado em outros comandos. É também o SPN que vais usar para a política de definição de cofre az keyvault. A palavra-passe é o segredo do cliente que deve usar mais tarde para ativar a Encriptação do Disco Azure. Proteja o segredo do cliente Azure AD adequadamente.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Criar uma app e diretor de serviço da Azure AD embora o portal Azure
Utilize os passos do [portal Use para criar uma aplicação e diretoria de serviço sinuoso azure ative que possa aceder](../../active-directory/develop/howto-create-service-principal-portal.md) ao artigo de recursos para criar uma aplicação Azure AD. Cada passo listado abaixo irá levá-lo diretamente para a secção de artigos para completar. 

1. [Verifique as permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Criar uma aplicação azure ative diretório](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Pode utilizar qualquer url de nome e inscrição que queira quando criar a aplicação.
3. [Obtenha o ID da aplicação e a chave de autenticação](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - A chave de autenticação é o segredo do cliente e é usada como AadClientSecret para Set-AzVMDiskEncryptionExtension. 
        - A chave de autenticação é utilizada pela aplicação como credencial para iniciar sessão no Azure AD. No portal Azure, este segredo chama-se chaves, mas não tem qualquer relação com cofres chave. Proteja este segredo apropriadamente. 
     - O ID da aplicação será usado mais tarde como AadClientId para Set-AzVMDiskEncryptionExtension E como o Nome principal do serviço para Set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Definir a política de acesso do cofre de chaves para a aplicação Azure AD
Para escrever segredos de encriptação para um cofre de chaves especificado, a Encriptação do Disco Azure precisa do ID do cliente e do cliente Secreto da aplicação De Diretório Ativo Azure que tem permissões para escrever segredos para o Cofre chave. 

> [!NOTE]
> A Encriptação do Disco Azure requer que configure as seguintes políticas de acesso à sua aplicação de cliente Azure AD: _WrapKey_ e _set_ permissions.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Desdefinir a política de acesso ao cofre chave para a app Azure AD com azure PowerShell
A sua aplicação Azure AD precisa de direitos para aceder às chaves ou segredos do cofre. Utilize o [cmdlet Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para conceder permissões à aplicação, utilizando o ID do cliente (que foi gerado quando a aplicação foi registada) como o valor do parâmetro _do "ServicePrincipalName"._ Para saber mais, consulte o blog post [Azure Key Vault - Step by Step](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Detete a política de acesso ao cofre chave para a aplicação AD com powerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Desdefinir a política de acesso ao cofre chave para a app Azure AD com o Azure CLI
Use a política de definição de [az keyvault](/cli/azure/keyvault#az-keyvault-set-policy) para definir a política de acesso. Para mais informações, consulte Gerir o [Cofre da Chave utilizando o CLI 2.0](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Dê ao diretor de serviço que criou através do Azure CLI acesso para obter segredos e embrulhar chaves com o seguinte comando:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Desdefinir a política de acesso ao cofre chave para a app Azure AD com o portal

1. Abra o grupo de recursos com o seu cofre chave.
2. Selecione o seu cofre de chaves, vá para **as Políticas**de Acesso e clique em **Adicionar novo**.
3. Em **'Selecionar principal',** procure a aplicação Azure AD que criou e selecione-a. 
4. Para **obter permissões chave,** verifique **a chave de embrulho** em **operações criptográficas**.
5. Para **obter permissões secretas,** consulte **o Conjunto de Operações** **de Gestão Secreta**.
6. Clique em **OK** para salvar a política de acesso. 

![Operações criptográficas do Cofre chave Azure - Chave de Embrulho](../media/disk-encryption/keyvault-portal-fig3.png)

![Permissões secretas do cofre de chaves azure - Conjunto](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Definir as políticas avançadas de acesso do cofre de chaves
A plataforma Azure precisa de acesso às chaves de encriptação ou segredos no seu cofre chave para disponibilizá-las ao VM para iniciar e desencriptar os volumes. Ativar a encriptação do disco no cofre da chave ou as implementações falharão.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Definir políticas de acesso avançado do cofre chave com a Azure PowerShell
 Utilize o cofre chave PowerShell cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para ativar a encriptação do disco para o cofre da chave.

  - **Ativar o Cofre de Chaves para encriptação de disco:** Ativada ForDiskEncryption é necessária para encriptação do Disco Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Ativar o cofre chave para a implantação, se necessário:** Permite ao fornecedor de recursos Microsoft.Compute recuperar segredos deste cofre chave quando este cofre chave é referenciado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Ativar o cofre de chaves para a implementação do modelo, se necessário:** Permite ao Gestor de Recursos Azure obter segredos deste cofre chave quando este cofre chave é referenciado numa implementação de modelo.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Definir as políticas de acesso avançados do cofre chave utilizando o Azure CLI
Utilize a [atualização do keyvault az](/cli/azure/keyvault#az-keyvault-update) para ativar a encriptação do disco para o cofre da chave. 

 - **Ativar o Cofre de Chaves para encriptação de disco:** É necessária encriptação ativa da encriptação do disco. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Ativar o cofre chave para a implantação, se necessário:** Permita que as Máquinas Virtuais recuperem certificados armazenados como segredos do cofre.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Ativar o cofre de chaves para a implementação do modelo, se necessário:** Permita que o Gestor de Recursos recupere segredos do cofre.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Definir políticas de acesso avançado do cofre chave através do portal Azure

1. Selecione o seu cofre de chaves, vá às Políticas de **Acesso**e **Clique para mostrar políticas de acesso avançadas**.
2. Selecione a caixa rotulada **Ativar o acesso à encriptação do disco Azure para encriptação de volume**.
3. Selecione **Ativar o acesso a Máquinas Virtuais Azure para implementação** e/ou ativar o acesso ao Gestor de Recursos **Azure para implementação**do modelo, se necessário. 
4. Clique em **Guardar**.

![Políticas de acesso avançados do cofre azure](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Configurar uma chave de encriptação (opcional)
Se quiser utilizar uma chave de encriptação (KEK) para uma camada adicional de segurança para chaves de encriptação, adicione um KEK ao seu cofre chave. Utilize o cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) para criar uma chave de encriptação no cofre da chave. Também pode importar um KEK da sua gestão chave HSM no local. Para mais informações, consulte [a Documentação do Cofre de Chaves](../../key-vault/keys/hsm-protected-keys.md). Quando uma chave de encriptação é especificada, a Encriptação do Disco Azure usa essa chave para embrulhar os segredos de encriptação antes de escrever para key vault. 

* Ao gerar teclas, utilize um tipo de tecla RSA. A encriptação do disco azure ainda não suporta a utilização de teclas Elípticas Curve.

* O seu segredo de cofre e URLs KEK devem ser versões. O Azure impõe esta restrição à versão. Para segredo válido e URLs KEK, consulte os seguintes exemplos:

  * Exemplo de um URL secreto válido:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exemplo de um URL KEK válido:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* A encriptação do disco azure não suporta especificar os números de porta como parte dos segredos do cofre chave e URLs KEK. Por exemplo, de URLs de cofre sem suporte e suportados, consulte os seguintes exemplos:

  * URL inaceitável do cofre chave*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL aceitável do cofre de chaves:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Configurar uma chave de encriptação com o Azure PowerShell 
Antes de utilizar o script PowerShell, deve estar familiarizado com os pré-requisitos de encriptação do disco Azure para compreender os passos do script. O script da amostra pode precisar de alterações para o seu ambiente. Este script cria todos os pré-requisitos de encriptação do disco Azure e encripta um VM IaaS existente, envolvendo a chave de encriptação do disco utilizando uma chave de encriptação chave. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="certificate-based-authentication-optional"></a>Autenticação baseada em certificado (opcional)
Se quiser usar a autenticação do certificado, pode enviar um para o seu cofre de chaves e implantá-lo para o cliente. Antes de utilizar o script PowerShell, deve estar familiarizado com os pré-requisitos de encriptação do disco Azure para compreender os passos do script. O script da amostra pode precisar de alterações para o seu ambiente.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Autenticação baseada em certificado e kek (opcional)

Se quiser utilizar a autenticação do certificado e embrulhar a chave de encriptação com um KEK, pode utilizar o script abaixo como exemplo. Antes de utilizar o script PowerShell, deve estar familiarizado com todos os pré-requisitos anteriores de encriptação do disco Azure para compreender os passos do script. O script da amostra pode precisar de alterações para o seu ambiente.

     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Passos seguintes

[Ativar encriptação do disco Azure com AD Azure em VMs do Windows (versão anterior)](disk-encryption-windows-aad.md)
