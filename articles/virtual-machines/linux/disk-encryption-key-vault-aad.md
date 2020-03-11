---
title: Criação e configuração de um cofre chave para encriptação de disco azure com AD Azure (versão anterior)
description: Este artigo fornece pré-requisitos para utilizar o Microsoft Azure Disk Encryption para VMs de IaaS.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 1fa9f4e790b49e83ed4c46e92242ff182d9a47b5
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970650"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Criação e configuração de um cofre chave para encriptação de disco azure com AD Azure (versão anterior)

**O novo lançamento da Encriptação do Disco Azure elimina a exigência de fornecer um parâmetro de aplicação Azure AD para permitir a encriptação do disco VM. Com o novo lançamento, já não é necessário fornecer credenciais de AD Azure durante a fase de encriptação ativa. Todos os novos VMs devem ser encriptados sem os parâmetros da aplicação Azure AD utilizando o novo lançamento. Para visualizar instruções para ativar a encriptação do disco VM utilizando a nova versão, consulte a [Encriptação do Disco Azure](disk-encryption-overview.md). Os VMs que já estavam encriptados com parâmetros de aplicação Azure AD ainda são suportados e devem continuar a ser mantidos com a sintaxe AAD.**

A Encriptação azure Disk usa o Cofre de Chave Azure para controlar e gerir chaves e segredos de encriptação do disco.  Para mais informações sobre os cofres chave, consulte [Start start with Azure Key Vault](../../key-vault/key-vault-get-started.md) e Secure your key [vault](../../key-vault/key-vault-secure-your-key-vault.md). 

Criar e configurar um cofre chave para utilização com encriptação de disco azure com AD Azure (versão anterior) envolve três passos:

1. Criar um cofre de chaves. 
2. Configure uma aplicação do Azure AD e principal de serviço.
3. Defina a política de acesso do Cofre de chaves para a aplicação do Azure AD.
4. Cofre de chaves conjunto avançado de políticas de acesso.
 
Também pode, se desejar, gerar ou importar uma chave de encriptação (KEK).

Consulte o principal [Criar e configurar um cofre chave para](disk-encryption-key-vault.md) o artigo de encriptação do disco Azure para obter passos sobre como instalar [ferramentas e ligar-se ao Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure).

> [!Note]
> Os passos neste artigo são automatizados no [pré-requisito de encriptação](https://github.com/ejarvi/ade-cli-getting-started) do disco Azure e na [encriptação do disco Azure pré-requisitos do script PowerShell](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Criar um cofre de chaves 
A Encriptação azure Disk está integrada com o [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerir as chaves e segredos de encriptação do disco na subscrição do cofre chave. Pode criar um cofre de chaves ou utilize um já existente para a encriptação de disco do Azure. Para mais informações sobre os cofres chave, consulte [Start start with Azure Key Vault](../../key-vault/key-vault-get-started.md) e Secure your key [vault](../../key-vault/key-vault-secure-your-key-vault.md). Pode utilizar um modelo do Resource Manager, o Azure PowerShell ou a CLI do Azure para criar um cofre de chaves. 


>[!WARNING]
>Para certificar-se de que os segredos de encriptação, não ultrapassam limites regionais, o Azure Disk Encryption tem o Cofre de chaves e as VMs para estar colocalizados na mesma região. Criar e utilizar um cofre de chaves que está na mesma região que a VM ser encriptada. 


### <a name="bkmk_KVPSH"></a>Crie um cofre chave com powerShell

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


### <a name="bkmk_KVCLI"></a>Crie um cofre chave com Azure CLI
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

### <a name="bkmk_KVRM"></a>Crie um cofre chave com um modelo de Gestor de Recursos

Pode criar um cofre chave utilizando o [modelo de Gestor](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)de Recursos .

1. No modelo de arranque rápido azure, clique em **Implementar para Azure**.
2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, nome key vault, ID do objeto, termos legais e acordo, e, em seguida, clique em **Comprar**. 


## <a name="bkmk_ADapp"></a>Criar uma app e diretor de serviço da Azure AD 
Quando precisar de criptografia para ser ativada numa VM em execução no Azure, o Azure Disk Encryption gera e escreve as chaves de encriptação para o seu Cofre de chaves. A gestão de chaves de encriptação no seu Cofre de chaves requer autenticação do Azure AD. Crie uma aplicação do Azure AD para esta finalidade. Para efeitos de autenticação, pode utilizar a autenticação baseada em segredo do cliente ou a [autenticação Azure AD baseada em certificadode cliente.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)


### <a name="bkmk_ADappPSH"></a>Criar uma app e diretor de serviço azure AD com a Azure PowerShell 
Para executar os seguintes comandos, obtenha e utilize o [módulo PowerShell AD Azure](/powershell/azure/active-directory/install-adv2). 

1. Utilize o cmdlet PowerShell de [Aplicação PowerShell new-AzADApplication](/powershell/module/az.resources/new-azadapplication) para criar uma aplicação Azure AD. MyApplicationHomePage e o MyApplicationUri podem ser quaisquer valores que desejar.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. O $azureAdApplication.ApplicationId é o ID de cliente do Azure AD e o $aadClientSecret é o segredo de cliente que irá utilizar mais tarde para ativar a encriptação de disco do Azure. Proteger adequadamente o segredo do cliente do Azure AD. Executar `$azureAdApplication.ApplicationId` mostrar-lhe-á o ApplicationID.


### <a name="bkmk_ADappCLI"></a>Criar uma app e diretor de serviço azure AD com o Azure CLI

Pode gerir os seus principais de serviço com o Azure CLI utilizando os comandos [az ad sp.](/cli/azure/ad/sp) Para mais informações, consulte [Criar um serviço Azure principal.](/cli/azure/create-an-azure-service-principal-azure-cli)

1. Crie um novo principal de serviço.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  O appId devolveu o ID de cliente do Azure AD é utilizada nos outros comandos. Também é o SPN que irá utilizar para o conjunto de keyvault de az-policy. A palavra-passe é o segredo do cliente que deve usar mais tarde para ativar a encriptação de disco do Azure. Proteger adequadamente o segredo do cliente do Azure AD.
 
### <a name="bkmk_ADappRM"></a>Criar uma app e diretor de serviço da Azure AD embora o portal Azure
Utilize os passos do [portal Use para criar uma aplicação e diretoria de serviço sinuoso azure ative que possa aceder](../../active-directory/develop/howto-create-service-principal-portal.md) ao artigo de recursos para criar uma aplicação Azure AD. Cada passo listado abaixo leva-o diretamente para a secção do artigo para concluir. 

1. [Verifique as permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Criar uma aplicação azure ative diretório](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Pode utilizar qualquer nome e início de sessão no URL que desejar ao criar a aplicação.
3. [Obtenha o ID da aplicação e a chave de autenticação](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - A chave de autenticação é o segredo do cliente e é usada como AadClientSecret para Set-AzVMDiskEncryptionExtension. 
        - A chave de autenticação é utilizada pela aplicação como uma credencial para iniciar sessão Azure AD. No portal do Azure, este segredo é chamado de chaves, mas tem sem relação aos cofres de chaves. Proteja Este segredo adequadamente. 
     - O ID da aplicação será usado mais tarde como AadClientId para Set-AzVMDiskEncryptionExtension E como o Nome principal do serviço para Set-AzKeyVaultAccessPolicy. 

## <a name="bkmk_KVAP"></a>Desdefinir a política de acesso ao cofre chave para a aplicação Azure AD
Para escrever segredos de encriptação para um cofre de chaves especificada, o Azure Disk Encryption tem o ID de cliente e o segredo de cliente da aplicação do Azure Active Directory que tem permissões para escrever segredos para o Key Vault. 

> [!NOTE]
> A Encriptação do Disco Azure requer que configure as seguintes políticas de acesso à sua aplicação de cliente Azure AD: _WrapKey_ e _set_ permissions.

### <a name="bkmk_KVAPPSH"></a>Desdefinir a política de acesso ao cofre chave para a app Azure AD com azure PowerShell
A aplicação do Azure AD tem direitos de acesso a chaves ou segredos no cofre. Utilize o [cmdlet Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para conceder permissões à aplicação, utilizando o ID do cliente (que foi gerado quando a aplicação foi registada) como o valor do parâmetro _do "ServicePrincipalName"._ Para saber mais, consulte o blog post [Azure Key Vault - Step by Step](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Defina a política de acesso do Cofre de chaves para a aplicação do AD com o PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a>Desdefinir a política de acesso ao cofre chave para a app Azure AD com o Azure CLI
Use a política de definição de [az keyvault](/cli/azure/keyvault#az-keyvault-set-policy) para definir a política de acesso. Para mais informações, consulte Gerir o [Cofre da Chave utilizando o CLI 2.0](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Forneça o principal de serviço que criou através do acesso a CLI do Azure para obter segredos e moldagem de chaves com o seguinte comando:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a>Desdefinir a política de acesso ao cofre chave para a app Azure AD com o portal

1. Abra o grupo de recursos com o seu Cofre de chaves.
2. Selecione o seu cofre de chaves, vá para **as Políticas**de Acesso e clique em **Adicionar novo**.
3. Em **'Selecionar principal',** procure a aplicação Azure AD que criou e selecione-a. 
4. Para **obter permissões chave,** verifique **a chave de embrulho** em **operações criptográficas**.
5. Para **obter permissões secretas,** consulte **o Conjunto de Operações** **de Gestão Secreta**.
6. Clique em **OK** para salvar a política de acesso. 

![O Azure Key Vault operações criptográficas - moldar chave](./media/disk-encryption/keyvault-portal-fig3.png)

![Definir as permissões do segredo do Cofre de chaves do Azure-](./media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a>Definir políticas de acesso avançado do cofre chave
A plataforma do Azure precisa de aceder a chaves de encriptação ou segredos no Cofre de chaves para que fiquem disponíveis para a VM para o arranque e a desencriptação de volumes. Ativar a encriptação de disco no Cofre de chaves ou implementações irão falhar.  

### <a name="bkmk_KVperPSH"></a>Definir políticas de acesso avançado do cofre chave com a Azure PowerShell
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

### <a name="bkmk_KVperCLI"></a>Definir as políticas de acesso avançados do cofre chave utilizando o Azure CLI
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


### <a name="bkmk_KVperrm"></a>Definir políticas de acesso avançado do cofre chave através do portal Azure

1. Selecione o seu cofre de chaves, vá às Políticas de **Acesso**e **Clique para mostrar políticas de acesso avançadas**.
2. Selecione a caixa rotulada **Ativar o acesso à encriptação do disco Azure para encriptação de volume**.
3. Selecione **Ativar o acesso a Máquinas Virtuais Azure para implementação** e/ou ativar o acesso ao Gestor de Recursos **Azure para implementação**do modelo, se necessário. 
4. Clique em **Guardar**.

![Cofre de chaves do Azure, as políticas de acesso avançadas](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a>Configurar uma chave de encriptação (opcional)
Se pretender utilizar uma chave de encriptação de chaves (KEK) para uma camada adicional de segurança para as chaves de encriptação, adicione um KEK ao seu Cofre de chaves. Utilize o cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) para criar uma chave de encriptação no cofre da chave. Também pode importar um KEK de sua gestão de chaves HSM no local. Para mais informações, consulte [a Documentação do Cofre de Chaves](../../key-vault/key-vault-hsm-protected-keys.md). Quando é especificada uma chave de encriptação de chave, o Azure Disk Encryption utiliza essa chave para encapsular os segredos de encriptação antes de escrever para o Key Vault. 

* Ao gerar teclas, utilize um tipo de tecla RSA. A encriptação do disco azure ainda não suporta a utilização de teclas Elípticas Curve.

* O segredo do Cofre de chaves e URLs de KEK devem ter a versão. Azure impõe essa restrição de controle de versão. Para segredo válido e KEK URLs, consulte os exemplos seguintes:

  * Exemplo de um URL secreto válido: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exemplo de um URL KEK válido: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* O Azure Disk Encryption não suporta a especificação de números de porta como parte de segredos do Cofre de chaves e URLs de KEK. Para obter exemplos de URLs de Cofre de chaves suportadas e não suportados, consulte os exemplos seguintes:

  * Url https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*de cofre* de chave inaceitável
  * URL aceitável do cofre da chave: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a>Configurar uma chave de encriptação com o Azure PowerShell 
Antes de utilizar o script do PowerShell, deve estar familiarizado com os pré-requisitos do Azure Disk Encryption para compreender os passos no script. O script de exemplo poderá ter as alterações para o seu ambiente. Este script cria todos os pré-requisitos do Azure Disk Encryption e criptografa uma VM de IaaS existente, a chave de encriptação de disco de encapsulamento de aplicações com uma chave de encriptação de chave. 

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

## <a name="bkmk_Cert"></a>Autenticação baseada em certificado (opcional)
Se gostaria de utilizar a autenticação de certificado, pode carregar um para o seu Cofre de chaves e implementá-la para o cliente. Antes de utilizar o script do PowerShell, deve estar familiarizado com os pré-requisitos do Azure Disk Encryption para compreender os passos no script. O script de exemplo poderá ter as alterações para o seu ambiente.

     
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

## <a name="bkmk_CertKEK"></a>Autenticação baseada em certificado e kek (opcional)

Se gostaria de utilizar a autenticação de certificado e encapsular a chave de encriptação com um KEK, pode utilizar o script como um exemplo abaixo. Antes de utilizar o script do PowerShell, deve estar familiarizado com todos os pré-requisitos do Azure Disk Encryption anteriores para compreender os passos no script. O script de exemplo poderá ter as alterações para o seu ambiente.

> [!IMPORTANT]
> Autenticação baseada em certificados. do Azure AD não é atualmente suportada em VMs do Linux.



     
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

[Ativar encriptação de disco Azure com AD Azure em VMs Linux (versão anterior)](disk-encryption-linux-aad.md)
