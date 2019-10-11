---
title: Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)
description: Este artigo fornece pré-requisitos para usar o Microsoft Azure a criptografia de disco para VMs IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5a42b1e0dc82b3340bbebe176c71cb1754d00664
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245230"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)

**A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicativo do Azure AD para habilitar a criptografia de disco de VM. Com a nova versão, você não precisa mais fornecer as credenciais do Azure AD durante a etapa habilitar criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros de aplicativo do Azure AD usando a nova versão. Para exibir instruções para habilitar a criptografia de disco de VM usando a nova versão, consulte [Azure Disk Encryption](disk-encryption-overview.md). As VMs que já foram criptografadas com parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD.**

Azure Disk Encryption usa Azure Key Vault para controlar e gerenciar chaves de criptografia de disco e segredos.  Para obter mais informações sobre cofres de chaves, consulte [introdução ao Azure Key Vault](../../key-vault/key-vault-get-started.md) e [proteger o cofre de chaves](../../key-vault/key-vault-secure-your-key-vault.md). 

Criar e configurar um cofre de chaves para uso com o Azure Disk Encryption com o Azure AD (versão anterior) envolve três etapas:

1. Criar um cofre de chaves. 
2. Configure um aplicativo do Azure AD e uma entidade de serviço.
3. Defina a política de acesso do cofre de chaves para o aplicativo do Azure AD.
4. Defina as políticas de acesso avançado do cofre de chaves.
 
Você também pode, se desejar, gerar ou importar uma chave de criptografia de chave (KEK).

Consulte o artigo principal [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md) para obter as etapas de como [instalar as ferramentas e conectar-se ao Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure).

> [!Note]
> As etapas neste artigo são automatizadas no [script da CLI de Azure Disk Encryption pré-requisitos](https://github.com/ejarvi/ade-cli-getting-started) e [Azure Disk Encryption script do PowerShell de pré-requisitos](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Criar um cofre de chaves 
O Azure Disk Encryption é integrado ao [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e os segredos em sua assinatura do cofre de chaves. Você pode criar um cofre de chaves ou usar um existente para Azure Disk Encryption. Para obter mais informações sobre cofres de chaves, consulte [introdução ao Azure Key Vault](../../key-vault/key-vault-get-started.md) e [proteger o cofre de chaves](../../key-vault/key-vault-secure-your-key-vault.md). Você pode usar um modelo do Resource Manager, Azure PowerShell ou o CLI do Azure para criar um cofre de chaves. 


>[!WARNING]
>Para ter certeza de que os segredos de criptografia não cruzam limites regionais, Azure Disk Encryption precisa que o Key Vault e as VMs sejam colocalizadas na mesma região. Crie e use um Key Vault que esteja na mesma região que a VM a ser criptografada. 


### <a name="create-a-key-vault-with-powershell"></a>Criar um cofre de chaves com o PowerShell

Você pode criar um cofre de chaves com Azure PowerShell usando o cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Para obter cmdlets adicionais para Key Vault, consulte [AZ. keyvault](/powershell/module/az.keyvault/). 

1. Crie um novo grupo de recursos, se necessário, com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Para listar locais de data center, use [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Criar um novo cofre de chaves usando [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Observe o **nome do cofre**, o **nome do grupo de recursos**, a ID do **recurso**, o **URI do cofre**e a **ID do objeto** que são retornados para uso posterior quando você criptografa os discos. 


### <a name="create-a-key-vault-with-azure-cli"></a>Criar um cofre de chaves com CLI do Azure
Você pode gerenciar seu cofre de chaves com CLI do Azure usando os comandos [AZ keyvault](/cli/azure/keyvault#commands) . Para criar um cofre de chaves, use [AZ keyvault Create](/cli/azure/keyvault#az-keyvault-create).

1. Crie um novo grupo de recursos, se necessário, com [AZ Group Create](/cli/azure/group#az-group-create). Para listar locais, use [AZ Account List-Locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Crie um novo cofre de chaves usando [AZ keyvault Create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Observe o **nome do cofre** (nome), o **nome do grupo de recursos**, a ID do **recurso** (ID), o **URI do cofre**e a **ID do objeto** que são retornados para uso posterior. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Criar um cofre de chaves com um modelo do Resource Manager

Você pode criar um cofre de chaves usando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. No modelo de início rápido do Azure, clique em **implantar no Azure**.
2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, o nome do Key Vault, a ID do objeto, os termos legais e o contrato e clique em **comprar**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Configurar um aplicativo do Azure AD e uma entidade de serviço 
Quando você precisar que a criptografia seja habilitada em uma VM em execução no Azure, o Azure Disk Encryption gerar e gravar as chaves de criptografia em seu cofre de chaves. O gerenciamento de chaves de criptografia em seu cofre de chaves requer a autenticação do Azure AD. Crie um aplicativo do Azure AD para essa finalidade. Para fins de autenticação, você pode usar a autenticação baseada em segredo do cliente ou a [autenticação do Azure ad baseada em certificado do cliente](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Configurar um aplicativo do Azure AD e uma entidade de serviço com Azure PowerShell 
Para executar os comandos a seguir, obtenha e use o [módulo do PowerShell do Azure ad](/powershell/azure/active-directory/install-adv2). 

1. Use o cmdlet do PowerShell [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) para criar um aplicativo do Azure AD. MyApplicationHomePage e MyApplicationUri podem ser quaisquer valores desejados.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. O $azureAdApplication. ApplicationId é o ClientID do Azure AD e o $aadClientSecret é o segredo do cliente que será usado posteriormente para habilitar o Azure Disk Encryption. Proteja adequadamente o segredo do cliente do Azure AD. A execução de `$azureAdApplication.ApplicationId` mostrará o ApplicationID.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Configurar um aplicativo do Azure AD e uma entidade de serviço com CLI do Azure

Você pode gerenciar suas entidades de serviço com CLI do Azure usando os comandos [AZ ad SP](/cli/azure/ad/sp) . Para obter mais informações, consulte [criar uma entidade de serviço do Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Crie uma nova entidade de serviço.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  O appId retornado é o ClientID do Azure AD usado em outros comandos. Também é o SPN que você usará para AZ keyvault Set-Policy. A senha é o segredo do cliente que você deve usar posteriormente para habilitar Azure Disk Encryption. Proteja adequadamente o segredo do cliente do Azure AD.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Configurar um aplicativo do Azure AD e uma entidade de serviço por meio do portal do Azure
Use as etapas no artigo [usar o portal para criar um aplicativo Azure Active Directory e uma entidade de serviço que pode acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md) para criar um aplicativo do Azure AD. Cada etapa listada abaixo levará você diretamente à seção do artigo para ser concluída. 

1. [Verificar as permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Criar um aplicativo Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Você pode usar qualquer nome e URL de logon que desejar ao criar o aplicativo.
3. [Obtenha a ID do aplicativo e a chave de autenticação](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - A chave de autenticação é o segredo do cliente e é usada como AadClientSecret para Set-AzVMDiskEncryptionExtension. 
        - A chave de autenticação é usada pelo aplicativo como uma credencial para entrar no Azure AD. No portal do Azure, esse segredo é chamado de chaves, mas não tem relação com os cofres de chaves. Proteja esse segredo adequadamente. 
     - A ID do aplicativo será usada posteriormente como AadClientId para Set-AzVMDiskEncryptionExtension e como o servicePrincipalName para Set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Definir a política de acesso do cofre de chaves para o aplicativo do Azure AD
Para gravar segredos de criptografia em um Key Vault especificado, Azure Disk Encryption precisa da ID do cliente e do segredo do cliente do aplicativo Azure Active Directory que tenha permissões para gravar segredos no Key Vault. 

> [!NOTE]
> Azure Disk Encryption exige que você configure as seguintes políticas de acesso para seu aplicativo cliente do Azure AD: _WrapKey_ e _defina_ as permissões.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Definir a política de acesso do cofre de chaves para o aplicativo do Azure AD com Azure PowerShell
Seu aplicativo do Azure AD precisa de direitos para acessar as chaves ou segredos no cofre. Use o cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para conceder permissões ao aplicativo, usando a ID do cliente (que foi gerada quando o aplicativo foi registrado) como o valor do parâmetro _– servicePrincipalName_ . Para saber mais, confira a postagem no blog Azure Key Vault passo a [passo](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Defina a política de acesso do cofre de chaves para o aplicativo do AD com o PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Definir a política de acesso do cofre de chaves para o aplicativo do Azure AD com CLI do Azure
Use [AZ keyvault Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) para definir a política de acesso. Para obter mais informações, consulte [gerenciar Key Vault usando a CLI 2,0](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Forneça a entidade de serviço que você criou por meio do acesso CLI do Azure para obter segredos e Encapsular chaves com o seguinte comando:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Definir a política de acesso do cofre de chaves para o aplicativo do Azure AD com o portal

1. Abra o grupo de recursos com o cofre de chaves.
2. Selecione o cofre de chaves, acesse **políticas de acesso**e clique em **Adicionar novo**.
3. Em **selecionar entidade de segurança**, procure o aplicativo do Azure AD que você criou e selecione-o. 
4. Para **permissões de chave**, verifique a **chave de encapsulamento** em **operações de criptografia**.
5. Para **permissões de segredo**, marque **definir** em operações de **Gerenciamento de segredo**.
6. Clique em **OK** para salvar a política de acesso. 

![Operações de criptografia de Azure Key Vault – encapsular chave](../media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault permissões de segredo-definir](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Definir políticas de acesso avançado do cofre de chaves
A plataforma Azure precisa acessar as chaves de criptografia ou segredos no cofre de chaves para disponibilizá-los para a VM para inicializar e descriptografar os volumes. A habilitação da criptografia de disco no cofre de chaves ou nas implantações falhará.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Definir políticas de acesso avançado do cofre de chaves com o Azure PowerShell
 Use o cmdlet do PowerShell do cofre de chaves [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para habilitar a criptografia de disco para o cofre de chaves.

  - **Habilitar Key Vault para criptografia de disco:** O EnabledForDiskEncryption é necessário para a criptografia de disco do Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Habilite Key Vault para implantação, se necessário:** Permite que o provedor de recursos Microsoft. Compute recupere segredos desse cofre de chaves quando esse cofre de chaves é referenciado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Habilite Key Vault para implantação de modelo, se necessário:** Permite que Azure Resource Manager obtenha segredos desse cofre de chaves quando esse cofre de chaves é referenciado em uma implantação de modelo.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Definir políticas de acesso avançado do cofre de chaves usando o CLI do Azure
Use a [atualização AZ keyvault](/cli/azure/keyvault#az-keyvault-update) para habilitar a criptografia de disco para o cofre de chaves. 

 - **Habilitar Key Vault para criptografia de disco:** Habilitado para criptografia de disco é necessário. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Habilite Key Vault para implantação, se necessário:** Permitir que máquinas virtuais recuperem certificados armazenados como segredos do cofre.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Habilite Key Vault para implantação de modelo, se necessário:** Permitir que o Gerenciador de recursos recupere segredos do cofre.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Definir políticas de acesso avançado do Key Vault por meio do portal do Azure

1. Selecione seu cofre de chaves, vá para **políticas de acesso**e **clique para mostrar políticas de acesso avançadas**.
2. Selecione a caixa rotulada **habilitar acesso a Azure Disk Encryption para criptografia de volume**.
3. Selecione **habilitar o acesso às máquinas virtuais do Azure para implantação** e/ou **habilitar o acesso a Azure Resource Manager para implantação de modelo**, se necessário. 
4. Clique em **Guardar**.

![Políticas de acesso avançado do Azure Key Vault](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Configurar uma chave de criptografia de chave (opcional)
Se você quiser usar uma chave de criptografia de chave (KEK) para obter uma camada adicional de segurança para chaves de criptografia, adicione um KEK ao cofre de chaves. Use o cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) para criar uma chave de criptografia de chave no cofre de chaves. Você também pode importar um KEK do HSM de gerenciamento de chaves local. Para obter mais informações, consulte a [documentação do Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para encapsular os segredos de criptografia antes de gravar em Key Vault. 

* Ao gerar chaves, use um tipo de chave RSA. Azure Disk Encryption ainda não dá suporte ao uso de chaves de curva elíptica.

* O segredo do cofre de chaves e as URLs de KEK devem ter controle de versão. O Azure impõe essa restrição de controle de versão. Para obter as URLs válidas de segredo e KEK, consulte os exemplos a seguir:

  * Exemplo de uma URL secreta válida: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exemplo de uma URL KEK válida: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption não dá suporte à especificação de números de porta como parte de segredos do cofre de chaves e URLs de KEK. Para obter exemplos de URLs do cofre de chaves com suporte e sem suporte, consulte os exemplos a seguir:

  * URL do cofre de chaves inaceitável *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL do cofre de chaves aceitável: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Configurar uma chave de criptografia de chave com Azure PowerShell 
Antes de usar o script do PowerShell, você deve estar familiarizado com os pré-requisitos de Azure Disk Encryption para entender as etapas no script. O script de exemplo pode precisar de alterações para o seu ambiente. Esse script cria todos os pré-requisitos de Azure Disk Encryption e criptografa uma VM IaaS existente, encapsulando a chave de criptografia de disco usando uma chave de criptografia de chave. 

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
Se você quiser usar a autenticação de certificado, poderá carregar uma para o cofre de chaves e implantá-la no cliente. Antes de usar o script do PowerShell, você deve estar familiarizado com os pré-requisitos de Azure Disk Encryption para entender as etapas no script. O script de exemplo pode precisar de alterações para o seu ambiente.

     
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

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Autenticação baseada em certificado e um KEK (opcional)

Se você quiser usar a autenticação de certificado e encapsular a chave de criptografia com um KEK, poderá usar o script abaixo como exemplo. Antes de usar o script do PowerShell, você deve estar familiarizado com todos os pré-requisitos de Azure Disk Encryption anteriores para entender as etapas no script. O script de exemplo pode precisar de alterações para o seu ambiente.

     
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

[Habilitar Azure Disk Encryption com o Azure AD em VMs do Windows (versão anterior)](disk-encryption-windows-aad.md)
