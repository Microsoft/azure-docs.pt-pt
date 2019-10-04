---
title: Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)
description: Este artigo fornece pré-requisitos para utilizar o Microsoft Azure Disk Encryption para VMs de IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: be709fcbb45c95f092b24b53fd0c506187fcd8b3
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828550"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)

**A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicação do Azure AD para ativar a encriptação de disco da VM. Com a nova versão, já não tem de fornecer credenciais do Azure AD durante o passo da criptografia de ativação. Todas as novas VMs tem de estar encriptadas sem os parâmetros da aplicação do Azure AD com a nova versão. Para exibir instruções para habilitar a criptografia de disco de VM usando a nova versão, consulte [Azure Disk Encryption](disk-encryption-overview.md). As VMs que já foram encriptadas com parâmetros de aplicação do Azure AD ainda são suportadas e devem continuar a ser mantido com a sintaxe do AAD.**

Azure Disk Encryption usa Azure Key Vault para controlar e gerenciar chaves de criptografia de disco e segredos.  Para obter mais informações sobre os cofres de chaves, consulte [introdução ao Azure Key Vault](../../key-vault/key-vault-get-started.md) e [proteger o seu Cofre de chave](../../key-vault/key-vault-secure-your-key-vault.md). 

Criar e configurar um cofre de chaves para uso com o Azure Disk Encryption com o Azure AD (versão anterior) envolve três etapas:

1. Criar um cofre de chaves. 
2. Configure uma aplicação do Azure AD e principal de serviço.
3. Defina a política de acesso do Cofre de chaves para a aplicação do Azure AD.
4. Cofre de chaves conjunto avançado de políticas de acesso.
 
Você também pode, se desejar, gerar ou importar uma chave de criptografia de chave (KEK).

Consulte o artigo principal [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md) para obter as etapas de como [instalar as ferramentas e conectar-se ao Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure).

> [!Note]
> As etapas neste artigo são automatizadas no [script da CLI de Azure Disk Encryption pré-requisitos](https://github.com/ejarvi/ade-cli-getting-started) e [Azure Disk Encryption script do PowerShell de pré-requisitos](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Criar um cofre de chaves 
O Azure Disk Encryption está integrado [do Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para o ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves. Pode criar um cofre de chaves ou utilize um já existente para a encriptação de disco do Azure. Para obter mais informações sobre os cofres de chaves, consulte [introdução ao Azure Key Vault](../../key-vault/key-vault-get-started.md) e [proteger o seu Cofre de chave](../../key-vault/key-vault-secure-your-key-vault.md). Pode utilizar um modelo do Resource Manager, o Azure PowerShell ou a CLI do Azure para criar um cofre de chaves. 


>[!WARNING]
>Para certificar-se de que os segredos de encriptação, não ultrapassam limites regionais, o Azure Disk Encryption tem o Cofre de chaves e as VMs para estar colocalizados na mesma região. Criar e utilizar um cofre de chaves que está na mesma região que a VM ser encriptada. 


### <a name="bkmk_KVPSH"></a> Criar um cofre de chaves com o PowerShell

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

4. Tenha em atenção a **nome do cofre**, **nome do grupo de recursos**, **ID de recurso**, **URI do cofre**e o **ID de objeto** que são devolvidos para utilização posterior ao encriptar os discos. 


### <a name="bkmk_KVCLI"></a> Criar um cofre de chaves com a CLI do Azure
Pode gerir o seu Cofre de chaves com a CLI do Azure com o [az keyvault](/cli/azure/keyvault#commands) comandos. Para criar um cofre de chaves, utilize [az keyvault criar](/cli/azure/keyvault#az-keyvault-create).

1. Criar um novo grupo de recursos, se necessário, com [criar grupo az](/cli/azure/group#az-group-create). Para listar localizações, utilize [lista de conta de az-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Criar um novo cofre de chaves utilizando [az keyvault criar](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Tenha em atenção a **nome do cofre** (nome), **nome do grupo de recursos**, **ID de recurso** (ID), **URI do cofre**e o **ID de objeto** que são devolvidos para utilização posterior. 

### <a name="bkmk_KVRM"></a> Criar um cofre de chaves com um modelo do Resource Manager

Pode criar um cofre de chaves utilizando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. No modelo de início rápido do Azure, clique em **implementar no Azure**.
2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, Key Vault nome, ID de objeto, os termos legais e contrato e, em seguida, clique em **Compra**. 


## <a name="bkmk_ADapp"></a> Configurar uma aplicação do Azure AD e o serviço principal 
Quando precisar de criptografia para ser ativada numa VM em execução no Azure, o Azure Disk Encryption gera e escreve as chaves de encriptação para o seu Cofre de chaves. A gestão de chaves de encriptação no seu Cofre de chaves requer autenticação do Azure AD. Crie uma aplicação do Azure AD para esta finalidade. Para fins de autenticação, pode utilizar a autenticação com base no segredo do cliente ou [autenticação de cliente baseada em certificado do Azure AD](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a> Configurar uma aplicação do Azure AD e o serviço principal com o Azure PowerShell 
Para executar os comandos seguintes, obter e utilizar o [módulo do Azure AD PowerShell](/powershell/azure/active-directory/install-adv2). 

1. Use o cmdlet do PowerShell [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) para criar um aplicativo do Azure AD. MyApplicationHomePage e o MyApplicationUri podem ser quaisquer valores que desejar.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. O $azureAdApplication.ApplicationId é o ID de cliente do Azure AD e o $aadClientSecret é o segredo de cliente que irá utilizar mais tarde para ativar a encriptação de disco do Azure. Proteger adequadamente o segredo do cliente do Azure AD. Executar `$azureAdApplication.ApplicationId` mostrará a o ApplicationID.


### <a name="bkmk_ADappCLI"></a> Configurar uma aplicação do Azure AD e o serviço principal com a CLI do Azure

Pode gerenciar seus principais de serviço com a CLI do Azure com o [az ad sp](/cli/azure/ad/sp) comandos. Para obter mais informações, consulte [criar uma entidade de serviço do Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Crie um novo principal de serviço.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  O appId devolveu o ID de cliente do Azure AD é utilizada nos outros comandos. Também é o SPN que irá utilizar para o conjunto de keyvault de az-policy. A palavra-passe é o segredo do cliente que deve usar mais tarde para ativar a encriptação de disco do Azure. Proteger adequadamente o segredo do cliente do Azure AD.
 
### <a name="bkmk_ADappRM"></a> Configurar uma aplicação do Azure AD e o serviço principal através do portal do Azure
Utilize os passos a [utilize o portal para criar um Azure Active Directory principal de aplicações e serviço que pode aceder a recursos](../../active-directory/develop/howto-create-service-principal-portal.md) artigo para criar uma aplicação do Azure AD. Cada passo listado abaixo leva-o diretamente para a secção do artigo para concluir. 

1. [Verifique se as permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Criar uma aplicação do Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Pode utilizar qualquer nome e início de sessão no URL que desejar ao criar a aplicação.
3. [Obtenha o ID da aplicação e a chave de autenticação](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - A chave de autenticação é o segredo do cliente e é usada como AadClientSecret para Set-AzVMDiskEncryptionExtension. 
        - A chave de autenticação é utilizada pela aplicação como uma credencial para iniciar sessão Azure AD. No portal do Azure, este segredo é chamado de chaves, mas tem sem relação aos cofres de chaves. Proteja Este segredo adequadamente. 
     - A ID do aplicativo será usada posteriormente como AadClientId para Set-AzVMDiskEncryptionExtension e como o servicePrincipalName para Set-AzKeyVaultAccessPolicy. 

## <a name="bkmk_KVAP"></a> Definir a política de acesso do Cofre de chaves para a aplicação do Azure AD
Para escrever segredos de encriptação para um cofre de chaves especificada, o Azure Disk Encryption tem o ID de cliente e o segredo de cliente da aplicação do Azure Active Directory que tem permissões para escrever segredos para o Key Vault. 

> [!NOTE]
> Azure Disk Encryption exige que você configure as seguintes políticas de acesso para seu aplicativo cliente do Azure AD: _WrapKey_ e _definir_ permissões.

### <a name="bkmk_KVAPPSH"></a> Definir a política de acesso do Cofre de chaves para a aplicação do Azure AD com o Azure PowerShell
A aplicação do Azure AD tem direitos de acesso a chaves ou segredos no cofre. Use o cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para conceder permissões ao aplicativo, usando a ID do cliente (que foi gerada quando o aplicativo foi registrado) como o valor do parâmetro _– servicePrincipalName_ . Para obter mais informações, consulte a mensagem de blogue [do Azure Key Vault - passo a passo](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Defina a política de acesso do Cofre de chaves para a aplicação do AD com o PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a> Definir a política de acesso do Cofre de chaves para a aplicação do Azure AD com a CLI do Azure
Uso [az keyvault conjunto-policy](/cli/azure/keyvault#az-keyvault-set-policy) para definir a política de acesso. Para obter mais informações, consulte [gerir Key Vault com a CLI 2.0](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Forneça o principal de serviço que criou através do acesso a CLI do Azure para obter segredos e moldagem de chaves com o seguinte comando:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a> Definir a política de acesso do Cofre de chaves para a aplicação do Azure AD com o portal

1. Abra o grupo de recursos com o seu Cofre de chaves.
2. Selecione o seu Cofre de chaves, aceda ao **políticas de acesso**, em seguida, clique em **adicionar novo**.
3. Sob **principal selecione**, procure a aplicação do Azure AD que criou e selecione-o. 
4. Para **permissões da chave**, verifique **moldar chave** sob **operações criptográficas**.
5. Para **permissões do segredo**, verifique **definir** sob **operações de gestão de segredo**.
6. Clique em **OK** para guardar a política de acesso. 

![O Azure Key Vault operações criptográficas - moldar chave](./media/disk-encryption/keyvault-portal-fig3.png)

![Definir as permissões do segredo do Cofre de chaves do Azure-](./media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a> Cofre de chaves de conjunto de políticas de acesso avançadas
A plataforma do Azure precisa de aceder a chaves de encriptação ou segredos no Cofre de chaves para que fiquem disponíveis para a VM para o arranque e a desencriptação de volumes. Ativar a encriptação de disco no Cofre de chaves ou implementações irão falhar.  

### <a name="bkmk_KVperPSH"></a> Cofre de chaves conjunto avançado de políticas de acesso com o Azure PowerShell
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

### <a name="bkmk_KVperCLI"></a> Cofre de chaves conjunto avançado de políticas de acesso com a CLI do Azure
Uso [atualização do Cofre de chaves de az](/cli/azure/keyvault#az-keyvault-update) para ativar a encriptação de disco para o Cofre de chaves. 

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


### <a name="bkmk_KVperrm"></a> Cofre de chaves conjunto avançado de políticas de acesso através do portal do Azure

1. Selecione o seu Cofre de chaves, aceda ao **políticas de acesso**, e **clique para mostrar as políticas de acesso avançadas**.
2. Selecione a caixa rotulada **ativar o acesso ao Azure Disk Encryption para encriptação de volume**.
3. Selecione **ativar o acesso às máquinas de virtuais do Azure para a implantação** e/ou **ativar o acesso ao Azure Resource Manager para a implementação de modelo**, se for necessário. 
4. Clique em **Guardar**.

![Cofre de chaves do Azure, as políticas de acesso avançadas](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Configurar uma chave de encriptação de chave (opcional)
Se pretender utilizar uma chave de encriptação de chaves (KEK) para uma camada adicional de segurança para as chaves de encriptação, adicione um KEK ao seu Cofre de chaves. Use o cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) para criar uma chave de criptografia de chave no cofre de chaves. Também pode importar um KEK de sua gestão de chaves HSM no local. Para obter mais informações, consulte [documentação do Cofre de chave](../../key-vault/key-vault-hsm-protected-keys.md). Quando é especificada uma chave de encriptação de chave, o Azure Disk Encryption utiliza essa chave para encapsular os segredos de encriptação antes de escrever para o Key Vault. 

* Ao gerar chaves, use um tipo de chave RSA. Azure Disk Encryption ainda não dá suporte ao uso de chaves de curva elíptica.

* O segredo do Cofre de chaves e URLs de KEK devem ter a versão. Azure impõe essa restrição de controle de versão. Para segredo válido e KEK URLs, consulte os exemplos seguintes:

  * Exemplo de um URL de segredo válido:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exemplo de um URL válido da KEK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* O Azure Disk Encryption não suporta a especificação de números de porta como parte de segredos do Cofre de chaves e URLs de KEK. Para obter exemplos de URLs de Cofre de chaves suportadas e não suportados, consulte os exemplos seguintes:

  * URL do Cofre de chaves inaceitável  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL do Cofre de chaves aceitável:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a> Configurar uma chave de encriptação de chave com o Azure PowerShell 
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

## <a name="bkmk_Cert"></a> Autenticação baseada em certificado (opcional)
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

## <a name="bkmk_CertKEK"></a> Autenticação baseada em certificado e um KEK (opcional)

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

[Habilitar Azure Disk Encryption com o Azure AD em VMs do Linux (versão anterior)](disk-encryption-linux-aad.md)
