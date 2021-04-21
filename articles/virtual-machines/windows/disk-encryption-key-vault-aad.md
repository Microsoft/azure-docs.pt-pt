---
title: Criar e configurar um cofre chave para encriptação de disco Azure com Azure AD (versão anterior)
description: Neste artigo, aprenda a criar e configurar um cofre chave para encriptação de disco Azure com Azure AD.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: f2f301556bd24adb5e4a18f15717374ef26c400b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777892"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Criar e configurar um cofre chave para encriptação de disco Azure com Azure AD (versão anterior)

**A nova versão da Encriptação do Disco Azure elimina o requisito para fornecer um parâmetro de aplicação AD Azure para permitir a encriptação do disco VM. Com a nova versão, já não é obrigado a fornecer credenciais AZure AD durante o passo de encriptação ativa. Todos os novos VMs devem ser encriptados sem os parâmetros da aplicação AD Azure utilizando a nova versão. Para visualizar instruções para ativar a encriptação do disco VM utilizando a nova versão, consulte [Azure Disk Encryption](disk-encryption-overview.md). Os VMs que já estavam encriptados com os parâmetros da aplicação AD Azure ainda são suportados e devem continuar a ser mantidos com a sintaxe AAD.**

A Azure Disk Encryption usa o Cofre da Chave Azure para controlar e gerir chaves e segredos de encriptação de discos.  Para obter mais informações sobre cofres chave, consulte [Começar com o Cofre da Chave Azure](../../key-vault/general/overview.md) e Proteger o cofre da [chave.](../../key-vault/general/security-overview.md) 

Criar e configurar um cofre chave para uso com encriptação de disco Azure com Azure AD (versão anterior) envolve três passos:

1. Criar um cofre de chaves. 
2. Crie um azure AD application and service principal.
3. Definir a política de acesso do cofre de chaves para a aplicação Azure AD.
4. Definir as políticas avançadas de acesso do cofre de chaves.
 
Pode também, se desejar, gerar ou importar uma chave de encriptação chave (KEK).

Consulte o principal Criando e configurando um cofre chave para o artigo [de encriptação do disco Azure](disk-encryption-key-vault.md) para etapas sobre como [instalar ferramentas e ligar ao Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure).

> [!Note]
> Os passos deste artigo são automatizados na encriptação do [disco Azure pré-requisitos do script CLI](https://github.com/ejarvi/ade-cli-getting-started) e da encriptação do [disco Azure pré-requisitos do script PowerShell](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Criar um cofre de chaves 
A Azure Disk Encryption está integrada com [o Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerir as chaves e segredos de encriptação do disco na subscrição do cofre chave. Pode criar um cofre de chaves ou utilizar um existente para encriptação de disco Azure. Para obter mais informações sobre cofres chave, consulte [Começar com o Cofre da Chave Azure](../../key-vault/general/overview.md) e Proteger o cofre da [chave.](../../key-vault/general/security-overview.md) Pode utilizar um modelo de Gestor de Recursos, Azure PowerShell ou o CLI Azure para criar um cofre de chaves. 


>[!WARNING]
>Para garantir que os segredos de encriptação não ultrapassem os limites regionais, a Encriptação do Disco Azure precisa que o Cofre-Chave e os VMs sejam co-localizados na mesma região. Crie e use um Cofre-Chave que esteja na mesma região que o VM para ser encriptado. 


### <a name="create-a-key-vault-with-powershell"></a>Criar um cofre com PowerShell

Pode criar um cofre com Azure PowerShell utilizando o cmdlet [New-AzKeyVault.](/powershell/module/az.keyvault/New-azKeyVault) Para obter mais cmdlets para Key Vault, consulte [Az.KeyVault](/powershell/module/az.keyvault/). 

1. Criar um novo grupo de recursos, se necessário, com [o New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Para listar as localizações do data center, utilize [o Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Criar um novo cofre de chaves usando [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Note o **Nome** do Cofre , **Nome do Grupo de Recursos,** **ID de recurso,** **Cofre URI** e o **ID** do objeto que são devolvidos para posterior utilização quando encriptar os discos. 


### <a name="create-a-key-vault-with-azure-cli"></a>Crie um cofre com Azure CLI
Pode gerir o seu cofre com o Azure CLI utilizando os comandos [az keyvault.](/cli/azure/keyvault#commands) Para criar um cofre chave, use [az keyvault create](/cli/azure/keyvault#az_keyvault_create).

1. Criar um novo grupo de recursos, se necessário, com [a criação do grupo AZ](/cli/azure/group#az_group_create). Para listar locais, use [as localizações da lista de conta az](/cli/azure/account#az_account_list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Crie um novo cofre de chaves utilizando [a criação de keyvault az](/cli/azure/keyvault#az_keyvault_create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Note o Nome do **Cofre** (nome), **Nome do Grupo de Recursos,** **ID de recurso** (ID), Cofre **URI,** e o **ID** do objeto que são devolvidos para utilização posterior. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Crie um cofre chave com um modelo de Gestor de Recursos

Pode criar um cofre de chaves utilizando o [modelo de Gestor de Recursos.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)

1. No modelo de arranque rápido Azure, clique em **Implementar para Azure**.
2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, nome do Cofre chave, ID do objeto, termos legais e acordo, e, em seguida, clique em **Comprar**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Crie uma app AD AD E um diretor de serviços 
Quando precisa de encriptação para ser ativado num VM em execução em Azure, a Encriptação do Disco Azure gera e escreve as chaves de encriptação do cofre da chave. A gestão das chaves de encriptação no cofre requer autenticação AZure AD. Crie uma aplicação AD AZure para este fim. Para efeitos de autenticação, pode utilizar a autenticação [AZure AD baseada em clientes ou o certificado de cliente AZure AD.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Crie uma app AD AD E um diretor de serviço com a Azure PowerShell 
Para executar os seguintes comandos, obtenha e utilize o [módulo Azure AD PowerShell](/powershell/azure/active-directory/install-adv2). 

1. Utilize o cmdlet PowerShell [new-AzADApplication](/powershell/module/az.resources/new-azadapplication) para criar uma aplicação AD Azure. MyApplicationHomePage e o MyApplicationUri podem ser os valores que desejar.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. O $azureAdApplication.ApplicationId é o Azure AD ClientID e o $aadClientSecret é o segredo do cliente que usará mais tarde para ativar a encriptação do disco Azure. Proteja o segredo do cliente Azure AD adequadamente. A execução `$azureAdApplication.ApplicationId` irá mostrar-lhe o ApplicationID.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Crie uma app AD AD E um diretor de serviço com a Azure CLI

Pode gerir os seus principais serviços com o Azure CLI utilizando os comandos [ad sp az.](/cli/azure/ad/sp) Para mais informações, consulte [Criar um responsável de serviço Azure.](/cli/azure/create-an-azure-service-principal-azure-cli)

1. Criar um novo diretor de serviço.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  O appId devolvido é o Azure AD ClientID usado em outros comandos. É também o SPN que você vai usar para a política de set-set az keyvault. A palavra-passe é o segredo do cliente que deve utilizar mais tarde para ativar a Encriptação do Disco Azure. Proteja o segredo do cliente Azure AD adequadamente.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Crie uma app AD AD E um diretor de serviços, embora o portal Azure
Utilize os passos do [portal Usar para criar uma aplicação e um chefe de serviço do Azure Ative Que possa aceder](../../active-directory/develop/howto-create-service-principal-portal.md) a um artigo de recursos para criar uma aplicação AD Azure. Cada passo listado abaixo irá levá-lo diretamente para a secção de artigos para completar. 

1. [Verificar as permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)
2. [Criar uma aplicação de Diretório Ativo Azure](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 
     - Pode utilizar qualquer nome e URL de inscrição que queira ao criar a aplicação.
3. [Obtenha o ID da aplicação e a chave de autenticação.](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) 
     - A chave de autenticação é o segredo do cliente e é usada como AadClientSecret para Set-AzVMDiskEncryptionExtension. 
        - A chave de autenticação é utilizada pela aplicação como credencial para iniciar sessão no Azure AD. No portal Azure, este segredo chama-se chaves, mas não tem relação com cofres chave. Proteja este segredo apropriadamente. 
     - O ID da aplicação será usado mais tarde como AadClientId para Set-AzVMDiskEncryptionExtension e como o ServicePrincipalName para Set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Definir a política de acesso do cofre de chaves para a aplicação Azure AD
Para escrever segredos de encriptação para um cofre de chaves especificado, a Encriptação do Disco Azure necessita do ID do Cliente e do Segredo de Cliente da aplicação Azure Ative Directory que tem permissões para escrever segredos para o Cofre de Chaves. 

> [!NOTE]
> A Azure Disk Encryption requer que configuure as seguintes políticas de acesso à sua aplicação de cliente AZure AD: _Permissões WrapKey_ e _set._

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Desconfiem da política de acesso ao cofre da chave para a aplicação AD Azure com a Azure PowerShell
A sua aplicação AZure AD precisa de direitos para aceder às chaves ou segredos no cofre. Utilize o [cmdlet Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para conceder permissões à aplicação, utilizando o ID do cliente (que foi gerado quando a aplicação foi registada) como o valor do parâmetro _-ServicePrincipalName._ Para saber mais, consulte o blog post [Azure Key Vault - Step by Step](/archive/blogs/kv/azure-key-vault-step-by-step). 

1. Desa estade a política de acesso ao cofre de chaves para a aplicação AD com o PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Desconfiem da política de acesso ao cofre da chave para a app Azure AD com o Azure CLI
Utilize a política de definição de [keyvault az](/cli/azure/keyvault#az_keyvault_set_policy) para definir a política de acesso. Para obter mais informações, consulte [Manage Key Vault utilizando o CLI 2.0](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Dê ao diretor de serviço que criou através do Azure CLI acesso para obter segredos e embrulhar chaves com o seguinte comando:

```azurecli-interactive
az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Desconfiem da política de acesso ao cofre chave para a app AZure AD com o portal

1. Abra o grupo de recursos com o cofre da chave.
2. Selecione o cofre da chave, vá às **Políticas de Acesso** e, em seguida, clique em Adicionar **novo**.
3. Em **Select principal,** procure a aplicação AD Azure que criou e selecione-a. 
4. Para **obter permissões de chave**, verifique a chave de **embrulho** em **operações criptográficas**.
5. Para **permissões secretas**, verifique **set** under **Secret Management Operations**.
6. Clique **em OK** para guardar a política de acesso. 

![Operações criptográficas do Cofre de Chaves Azure - Chave de embrulho](../media/disk-encryption/keyvault-portal-fig3.png)

![Permissões secretas do cofre da chave Azure - Conjunto](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Definir as políticas avançadas de acesso do cofre de chaves
A plataforma Azure precisa de acesso às chaves de encriptação ou segredos no seu cofre chave para os disponibilizar ao VM para iniciar e desencriptar os volumes. Ativar a encriptação do disco no cofre de teclas ou as implementações falharão.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Definir políticas de acesso avançada de cofre de chaves com Azure PowerShell
 Utilize o cofre powerShell cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para ativar a encriptação do disco para o cofre da chave.

  - **Ativar o Cofre da Chave para encriptação do disco:** EnablediskEncryption é necessário para encriptação do Disco Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Ativar o cofre da chave para a implantação, se necessário:** Permite que o fornecedor de recursos Microsoft.Compute recupere segredos a partir deste cofre chave quando este cofre chave é referenciado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Ativar o cofre da chave para a implementação do modelo, se necessário:** Permite ao Azure Resource Manager obter segredos a partir deste cofre chave quando este cofre de chaves é referenciado numa implementação de modelo.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Definir políticas de acesso avançado de cofre de chaves usando o CLI Azure
Utilize [a atualização do keyvault az](/cli/azure/keyvault#az_keyvault_update) para ativar a encriptação do disco para o cofre da chave. 

 - **Ativar o Cofre da Chave para encriptação do disco:** É necessária encriptação ativada para o disco. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Ativar o cofre da chave para a implantação, se necessário:** Permitir que as Máquinas Virtuais recuperem certificados armazenados como segredos do cofre.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Ativar o cofre da chave para a implementação do modelo, se necessário:** Permita ao Gestor de Recursos recuperar segredos do cofre.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Definir as políticas avançadas de acesso do cofre de chaves através do portal Azure

1. Selecione o seu keyvault, vá às **Políticas de Acesso** e clique para mostrar políticas de acesso **avançadas.**
2. Selecione a caixa com a etiqueta **Ativar o acesso à Encriptação do Disco Azure para encriptação de volume**.
3. **Selecione Permitir o acesso a Máquinas Virtuais Azure para implantação** e/ou Permitir o Acesso ao Gestor de Recursos **Azure para a implementação do modelo,** se necessário. 
4. Clique em **Guardar**.

![Azure key vault políticas avançadas de acesso](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Configurar uma chave de encriptação chave (opcional)
Se pretender utilizar uma chave de encriptação (KEK) para uma camada adicional de segurança para chaves de encriptação, adicione um KEK ao seu cofre de chaves. Utilize o [cmdlet Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) para criar uma chave de encriptação chave no cofre da chave. Também pode importar um KEK da sua gestão de chaves no local HSM. Para obter mais informações, consulte [a Documentação do Cofre-Chave](../../key-vault/keys/hsm-protected-keys.md). Quando uma chave de encriptação chave é especificada, a Encriptação do Disco Azure usa essa chave para embrulhar os segredos de encriptação antes de escrever para Key Vault. 

* Ao gerar teclas, utilize um tipo de chave RSA. A encriptação do disco Azure ainda não suporta a utilização de teclas elípticas da curva.

* O seu segredo chave do cofre e OS URLs KEK devem ser versados. A Azure impõe esta restrição de versão. Para obter URLs secretos e KEK válidos, consulte os seguintes exemplos:

  * Exemplo de um URL secreto válido:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exemplo de um URL KEK válido:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* A encriptação do disco Azure não suporta especificar números de porta como parte de segredos chave do cofre e URLs KEK. Por exemplo, os URLs de cofre de chaves não suportados e suportados, consulte os seguintes exemplos:

  * URL de cofre de chaves inaceitável  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL de cofre de chaves aceitável:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Configurar uma chave de encriptação chave com a Azure PowerShell 
Antes de utilizar o script PowerShell, deve estar familiarizado com os pré-requisitos de Encriptação do Disco Azure para entender os passos no script. O script da amostra pode precisar de alterações para o seu ambiente. Este script cria todos os pré-requisitos de encriptação do disco Azure e encripta um IaaS VM existente, envolvendo a chave de encriptação do disco utilizando uma chave de encriptação chave. 

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

## <a name="certificate-based-authentication-optional"></a>Autenticação baseada em certificados (opcional)
Se quiser utilizar a autenticação de certificados, pode enviar um para o cofre e implantá-lo para o cliente. Antes de utilizar o script PowerShell, deve estar familiarizado com os pré-requisitos de Encriptação do Disco Azure para entender os passos no script. O script da amostra pode precisar de alterações para o seu ambiente.

     
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

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Autenticação baseada em certificados e um KEK (opcional)

Se quiser utilizar a autenticação do certificado e embrulhar a chave de encriptação com um KEK, pode usar o script abaixo como exemplo. Antes de utilizar o script PowerShell, deve estar familiarizado com todos os pré-requisitos anteriores de Encriptação do Disco Azure para entender os passos no script. O script da amostra pode precisar de alterações para o seu ambiente.

     
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

[Ativar encriptação do disco Azure com Azure AD em VMs do Windows (versão anterior)](disk-encryption-windows-aad.md)
