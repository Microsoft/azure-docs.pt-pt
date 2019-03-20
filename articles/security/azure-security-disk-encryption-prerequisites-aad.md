---
title: O Azure Disk Encryption, com os pré-requisitos de aplicação do Azure AD (versão anterior)
description: Este artigo fornece pré-requisitos para utilizar o Microsoft Azure Disk Encryption para VMs de IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 70cf7876a0c8979467da7cc4d38c31e21a234d4e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861184"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>O Azure Disk Encryption pré-requisitos (versão anterior)

**A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicação do Azure AD para ativar a encriptação de disco da VM. Com a nova versão, já não tem de fornecer credenciais do Azure AD durante o passo da criptografia de ativação. Todas as novas VMs tem de estar encriptadas sem os parâmetros da aplicação do Azure AD com a nova versão. Para ver instruções para ativar a encriptação de disco VM com a nova versão, consulte [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md). As VMs que já foram encriptadas com parâmetros de aplicação do Azure AD ainda são suportadas e devem continuar a ser mantido com a sintaxe do AAD.**

 Neste artigo, os pré-requisitos do Azure Disk Encryption, explica os itens que precisam de estar em vigor antes de poder utilizar o Azure Disk Encryption. Juntamente com a pré-requisitos gerais, está integrado do Azure Disk Encryption [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) e utiliza uma aplicação do Azure AD para fornecer autenticação para gerir as chaves de encriptação no Cofre de chaves. Também pode pretender utilizar [do Azure PowerShell](/powershell/azure/overview) ou o [CLI do Azure](/cli/azure/) para configurar ou configurar o Cofre de chaves e a aplicação do Azure AD.

Antes de ativar o Azure Disk Encryption em VMs de IaaS do Azure para os cenários suportados que foram abordados os [descrição geral de encriptação de disco do Azure](azure-security-disk-encryption-overview.md) article, certifique-se de que tem os pré-requisitos necessários. 

> [!WARNING]
> - Algumas recomendações podem aumentar de dados, a rede ou a utilização de recursos de computação, resultando em custos adicionais de licença ou subscrição. Tem de ter uma subscrição do Azure Active Directory válida para criar recursos no Azure nas regiões suportadas.
> - Se tiver utilizado anteriormente [do Azure Disk Encryption, com a aplicação do Azure AD](azure-security-disk-encryption-prerequisites-aad.md) para encriptar esta VM, terá de continuar a utilizar esta opção para encriptar a sua VM. Não é possível usar [do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) nesta VM encriptada como isso não é um cenário suportado, significado mudar para fora da aplicação do AAD para encriptados esta VM não é suportada ainda. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_OSs"></a> Sistemas operativos suportados
O Azure Disk Encryption tem suporte nos seguintes sistemas operativos:

- Versões do Windows Server: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016.
  - Para o Windows Server 2008 R2, tem de ter o .NET Framework 4.5 instalado antes de ativar a encriptação no Azure. Instale-o do Windows Update com a atualização opcional Microsoft .NET Framework 4.5.2 para sistemas baseados em x64 do Windows Server 2008 R2 ([KB2901983](https://support.microsoft.com/kb/2901983)).    
- Versões de cliente do Windows: Cliente Windows 8 e o cliente do Windows 10.
- O Azure Disk Encryption é apenas suportado em específico galeria do Azure com base em distribuições de servidor Linux e versões. Para obter a lista de versões atualmente suportadas, consulte a [FAQ de encriptação de disco do Azure](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).
- O Azure Disk Encryption requer que o Cofre de chaves e VMs residem na mesma região do Azure e subscrição. Configurar os recursos em regiões separadas faz com que uma falha na ativação da funcionalidade do Azure Disk Encryption.

## <a name="bkmk_LinuxPrereq"></a> Pré-requisitos adicionais para VMs de IaaS Linux 

- O Azure Disk Encryption para o Linux requer 7 GB de RAM na VM para ativar a encriptação de disco de SO no [suportadas imagens](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport). Quando o processo de encriptação de disco do SO estiver concluído, a VM pode ser configurada para executar com menos memória.
- Antes de ativar a encriptação, os discos de dados sejam encriptados tem de estar corretamente listado em /etc/fstab. Utilize um nome de dispositivo de bloco persistente para esta entrada, como nomes no formato "/ desenvolvimento/sdX" não podem ser confiados para ser associado com o mesmo disco em reinícios, particularmente depois de é aplicada a encriptação de dispositivo. Para obter mais detalhes sobre esse comportamento, consulte: [Resolver problemas relacionados com alterações de nome de dispositivo de VM do Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Certifique-se de que as definições de /etc/fstab. estão configuradas corretamente para a montagem. Para configurar estas definições, execute o comando mount - a ou reinicie a VM e acionar a remontagem dessa forma. Quando terminar, verifique a saída do comando lsblk para verificar que a unidade pretendida ainda está instalada. 
  - Se o ficheiro de /etc/fstab. não montar a unidade corretamente antes de ativar a encriptação, Azure Disk Encryption não será capaz de montá-la corretamente.
  - O processo de Azure Disk Encryption irá mover as informações de montagem fora /etc/fstab. e em seu próprio arquivo de configuração como parte do processo de encriptação. Não se assuste para ver a entrada em falta de /etc/fstab. depois de encriptação de unidade de dados é concluída.
  -  Após a reinicialização, levará tempo para o processo de encriptação de disco do Azure montar os discos encriptados recentemente. Eles não imediatamente estarão disponíveis após um reinício. O processo tem tempo para iniciar, desbloquear e, em seguida, montar os discos encriptados antes de ser disponível para outros processos aceder. Este processo pode demorar mais de um minuto após a reinicialização, dependendo das características do sistema.

Um exemplo de comandos que podem ser usados para montar os discos de dados e criar o necessário/etc/fstab entradas pode ser encontrado na [linhas 197-205 deste ficheiro de script](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L197-L205). 


## <a name="bkmk_GPO"></a> Funcionamento em rede e a política de grupo

**Para ativar a funcionalidade de encriptação de disco do Azure com o AAD mais antigo sintaxe do parâmetro, as VMs de IaaS tem de cumprir os seguintes requisitos de configuração do ponto final de rede:** 
  - Para obter um token para ligar ao seu Cofre de chaves, a VM de IaaS tem de ser capaz de ligar a um ponto de extremidade do Azure Active Directory \[login.microsoftonline.com\].
  - Para escrever as chaves de encriptação para o seu Cofre de chaves, a VM de IaaS tem de conseguir ligar ao ponto final do Cofre de chaves.
  - A VM de IaaS tem de ser capaz de ligar a um ponto de extremidade de armazenamento do Azure que aloja o repositório de extensão do Azure e uma conta de armazenamento do Azure que aloja os ficheiros VHD.
  -  Se a política de segurança limita o acesso a partir de VMs do Azure para a Internet, pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída para os IPs. Para obter mais informações, consulte [do Azure Key Vault protegido por uma firewall](../key-vault/key-vault-access-behind-firewall.md).
  - No Windows, se TLS 1.0 tem sido explicitamente desabilitado e a versão do .NET não foi atualizada para 4.6 ou superior, a alteração de registo seguinte irá permitir ADE selecionar a versão mais recente do TLS:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

 


**Política de grupo:**
 - A solução Azure Disk Encryption utiliza o protetor de chave externo do BitLocker para VMs de IaaS do Windows. Para VMs associados ao domínio, não enviar por push as políticas de grupo que impõem protetores TPM. Para informações sobre a política de grupo para "Permitir BitLocker sem um TPM compatível", consulte [referência de política de grupo de BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#a-href-idbkmk-unlockpol1arequire-additional-authentication-at-startup).

-  Política do BitLocker em máquinas de virtuais associados a um domínio com a política de grupo personalizado tem de incluir a definição seguinte: [Configurar o armazenamento de usuário do bitlocker informações de recuperação -> chave de recuperação permitem 256 bits](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). O Azure Disk Encryption irá falhar quando as definições de política de grupo personalizado para o BitLocker são incompatíveis. Nas máquinas que não tinham a definição de política correta, aplicar a nova política, forçar a nova política de atualização (gpupdate.exe /force) e, em seguida, reiniciar poderá ser necessário.  


## <a name="bkmk_PSH"></a> O Azure PowerShell
[O Azure PowerShell](/powershell/azure/overview) fornece um conjunto de cmdlets que utiliza o [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) modelos para gerir os recursos do Azure. Pode usá-lo no seu browser com [Azure Cloud Shell](../cloud-shell/overview.md), ou pode instalá-lo no seu computador local com as instruções abaixo para utilizá-lo em qualquer sessão do PowerShell. Se já tiver instalado localmente, certifique-se de que utilizar a versão mais recente do Azure PowerShell para configurar o Azure Disk Encryption.

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Instale o Azure PowerShell para utilização no seu computador local (opcional):  
1. [Instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps). 

2. Instalar o [módulo do Azure Active Directory PowerShell](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module). 

     ```powershell
     Install-Module AzureAD
     ```

3. Verifique se as versões instaladas dos módulos.
      ```powershell
      Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. Inicie sessão no Azure com o [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet.
     
     ```powershell
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  Ligar ao Azure AD [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```

6. Revisão [introdução ao Azure PowerShell](/powershell/azure/get-started-azureps) e [AzureAD](/powershell/module/azuread), se for necessário.

## <a name="bkmk_CLI"></a> CLI do Azure

O [CLI 2.0 do Azure](/cli/azure) é uma ferramenta da linha de comandos para gerir recursos do Azure. A CLI é concebida para consultar dados com flexibilidade, oferecem suporte a operações de longa execução como processos sem bloquear e tornar a escrita de scripts fácil. Pode utilizá-lo no seu browser com o [Azure Cloud Shell](../cloud-shell/overview.md) ou pode instalá-lo no seu computador local e utilizá-lo em qualquer sessão do PowerShell.

1. [Instalar a CLI do Azure](/cli/azure/install-azure-cli) para utilização no seu computador local (opcional):

2. Certifique-se a versão instalada.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Inicie sessão no Azure com [início de sessão az](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Revisão [introdução à CLI 2.0 do Azure](/cli/azure/get-started-with-azure-cli) se for necessário. 


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>Fluxo de trabalho dos pré-requisitos para o Cofre de chaves e a aplicação do Azure AD

Se já estiver familiarizado com os pré-requisitos do Key Vault e o Azure AD para a encriptação de disco do Azure, pode utilizar o [script do PowerShell de pré-requisitos de Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para obter mais informações sobre como utilizar o script de pré-requisitos, consulte a [encriptar um início rápido de VM](quick-encrypt-vm-powershell.md) e o [apêndice de encriptação de disco do Azure](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Criar um cofre de chaves. 
2. Configure uma aplicação do Azure AD e principal de serviço.
3. Defina a política de acesso do Cofre de chaves para a aplicação do Azure AD.
4. Cofre de chaves conjunto avançado de políticas de acesso.
 
## <a name="bkmk_KeyVault"></a> Criar um cofre de chaves 
O Azure Disk Encryption está integrado [do Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para o ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves. Pode criar um cofre de chaves ou utilize um já existente para a encriptação de disco do Azure. Para obter mais informações sobre os cofres de chaves, consulte [introdução ao Azure Key Vault](../key-vault/key-vault-get-started.md) e [proteger o seu Cofre de chave](../key-vault/key-vault-secure-your-key-vault.md). Pode utilizar um modelo do Resource Manager, o Azure PowerShell ou a CLI do Azure para criar um cofre de chaves. 


>[!WARNING]
>Para certificar-se de que os segredos de encriptação, não ultrapassam limites regionais, o Azure Disk Encryption tem o Cofre de chaves e as VMs para estar colocalizados na mesma região. Criar e utilizar um cofre de chaves que está na mesma região que a VM ser encriptada. 


### <a name="bkmk_KVPSH"></a> Criar um cofre de chaves com o PowerShell

Pode criar um cofre de chaves com o Azure PowerShell através da [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) cmdlet. Para obter cmdlets adicionais para o Key Vault, consulte [Az.KeyVault](/powershell/module/az.keyvault/). 

1. Se for necessário, [ligar à sua subscrição do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Criar um novo grupo de recursos, se necessário, com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Para listar localizações de Datacenter, utilize [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Criar um novo cofre de chaves utilizando [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Tenha em atenção a **nome do cofre**, **nome do grupo de recursos**, **ID de recurso**, **URI do cofre**e o **ID de objeto** que são devolvidos para utilização posterior ao encriptar os discos. 


### <a name="bkmk_KVCLI"></a> Criar um cofre de chaves com a CLI do Azure
Pode gerir o seu Cofre de chaves com a CLI do Azure com o [az keyvault](/cli/azure/keyvault#commands) comandos. Para criar um cofre de chaves, utilize [az keyvault criar](/cli/azure/keyvault#az-keyvault-create).

1. Se for necessário, [ligar à sua subscrição do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Criar um novo grupo de recursos, se necessário, com [criar grupo az](/cli/azure/group#az-group-create). Para listar localizações, utilize [lista de conta de az-locations](/cli/azure/account#az-account-list) 
     
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
Quando precisar de criptografia para ser ativada numa VM em execução no Azure, o Azure Disk Encryption gera e escreve as chaves de encriptação para o seu Cofre de chaves. A gestão de chaves de encriptação no seu Cofre de chaves requer autenticação do Azure AD. Crie uma aplicação do Azure AD para esta finalidade. Para fins de autenticação, pode utilizar a autenticação com base no segredo do cliente ou [autenticação de cliente baseada em certificado do Azure AD](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a> Configurar uma aplicação do Azure AD e o serviço principal com o Azure PowerShell 
Para executar os comandos seguintes, obter e utilizar o [módulo do Azure AD PowerShell](/powershell/azure/active-directory/install-adv2). 

1. Se for necessário, [ligar à sua subscrição do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Utilize o [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) cmdlet do PowerShell para criar uma aplicação do Azure AD. MyApplicationHomePage e o MyApplicationUri podem ser quaisquer valores que desejar.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. O $azureAdApplication.ApplicationId é o ID de cliente do Azure AD e o $aadClientSecret é o segredo de cliente que irá utilizar mais tarde para ativar a encriptação de disco do Azure. Proteger adequadamente o segredo do cliente do Azure AD. Executar `$azureAdApplication.ApplicationId` mostrará a o ApplicationID.


### <a name="bkmk_ADappCLI"></a> Configurar uma aplicação do Azure AD e o serviço principal com a CLI do Azure

Pode gerenciar seus principais de serviço com a CLI do Azure com o [az ad sp](/cli/azure/ad/sp) comandos. Para obter mais informações, consulte [criar um Azure principal de serviço](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Se for necessário, [ligar à sua subscrição do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Crie um novo principal de serviço.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  O appId devolveu o ID de cliente do Azure AD é utilizada nos outros comandos. Também é o SPN que irá utilizar para o conjunto de keyvault de az-policy. A palavra-passe é o segredo do cliente que deve usar mais tarde para ativar a encriptação de disco do Azure. Proteger adequadamente o segredo do cliente do Azure AD.
 
### <a name="bkmk_ADappRM"></a> Configurar uma aplicação do Azure AD e o serviço principal através do portal do Azure
Utilize os passos a [utilize o portal para criar um Azure Active Directory principal de aplicações e serviço que pode aceder a recursos](../active-directory/develop/howto-create-service-principal-portal.md) artigo para criar uma aplicação do Azure AD. Cada passo listado abaixo leva-o diretamente para a secção do artigo para concluir. 

1. [Verifique se as permissões necessárias](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Criar uma aplicação do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Pode utilizar qualquer nome e início de sessão no URL que desejar ao criar a aplicação.
3. [Obtenha o ID da aplicação e a chave de autenticação](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key). 
     - A chave de autenticação é o segredo do cliente e é utilizada como o AadClientSecret para Set-AzVMDiskEncryptionExtension. 
        - A chave de autenticação é utilizada pela aplicação como uma credencial para iniciar sessão Azure AD. No portal do Azure, este segredo é chamado de chaves, mas tem sem relação aos cofres de chaves. Proteja Este segredo adequadamente. 
     - O ID da aplicação será utilizado mais tarde como AadClientId para Set-AzVMDiskEncryptionExtension e como ServicePrincipalName para Set-AzKeyVaultAccessPolicy. 

## <a name="bkmk_KVAP"></a> Definir a política de acesso do Cofre de chaves para a aplicação do Azure AD
Para escrever segredos de encriptação para um cofre de chaves especificada, o Azure Disk Encryption tem o ID de cliente e o segredo de cliente da aplicação do Azure Active Directory que tem permissões para escrever segredos para o Key Vault. 

> [!NOTE]
> O Azure Disk Encryption exige que configure as seguintes políticas de acesso à sua aplicação de cliente do Azure AD: _WrapKey_ e _definir_ permissões.

### <a name="bkmk_KVAPPSH"></a> Definir a política de acesso do Cofre de chaves para a aplicação do Azure AD com o Azure PowerShell
A aplicação do Azure AD tem direitos de acesso a chaves ou segredos no cofre. Utilize o [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet para conceder permissões para a aplicação, com o ID de cliente (o que foi gerado quando o aplicativo foi registrado) como o _– ServicePrincipalName_ valor do parâmetro. Para obter mais informações, consulte a mensagem de blogue [do Azure Key Vault - passo a passo](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Se for necessário, [ligar à sua subscrição do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Defina a política de acesso do Cofre de chaves para a aplicação do AD com o PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a> Definir a política de acesso do Cofre de chaves para a aplicação do Azure AD com a CLI do Azure
Uso [az keyvault conjunto-policy](/cli/azure/keyvault#az-keyvault-set-policy) para definir a política de acesso. Para obter mais informações, consulte [gerir Key Vault com a CLI 2.0](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

1. Se for necessário, [ligar à sua subscrição do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Forneça o principal de serviço que criou através do acesso a CLI do Azure para obter segredos e moldagem de chaves com o seguinte comando:
 
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

![O Azure Key Vault operações criptográficas - moldar chave](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Definir as permissões do segredo do Cofre de chaves do Azure-](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a> Cofre de chaves de conjunto de políticas de acesso avançadas
A plataforma do Azure precisa de aceder a chaves de encriptação ou segredos no Cofre de chaves para que fiquem disponíveis para a VM para o arranque e a desencriptação de volumes. Ativar a encriptação de disco no Cofre de chaves ou implementações irão falhar.  

### <a name="bkmk_KVperPSH"></a> Cofre de chaves conjunto avançado de políticas de acesso com o Azure PowerShell
 Utilize o cmdlet PowerShell do Cofre de chaves [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para ativar a encriptação de disco para o Cofre de chaves.

  - **Ative o Key Vault para encriptação de disco:** EnabledForDiskEncryption é necessário para a encriptação de disco do Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Ative o Key Vault para a implementação, se necessário:** Permite que o fornecedor de recursos Microsoft. Compute obter segredos a partir deste cofre de chaves, quando este Cofre de chaves é referenciado na criação de recursos, por exemplo, quando criar uma máquina virtual.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Ative o Key Vault para a implementação de modelo, se necessário:** Permite que o Azure Resource Manager para obter segredos a partir deste cofre de chaves, quando este Cofre de chaves é referenciado numa implementação do modelo.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Cofre de chaves conjunto avançado de políticas de acesso com a CLI do Azure
Uso [atualização do Cofre de chaves de az](/cli/azure/keyvault#az-keyvault-update) para ativar a encriptação de disco para o Cofre de chaves. 

 - **Ative o Key Vault para encriptação de disco:** Habilitado-para--encriptação de disco é necessário. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Ative o Key Vault para a implementação, se necessário:** Permitir que as máquinas virtuais obter certificados armazenados como segredos do cofre.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Ative o Key Vault para a implementação de modelo, se necessário:** Permitir Gestor de recursos para obter segredos a partir do cofre.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Cofre de chaves conjunto avançado de políticas de acesso através do portal do Azure

1. Selecione o seu Cofre de chaves, aceda ao **políticas de acesso**, e **clique para mostrar as políticas de acesso avançadas**.
2. Selecione a caixa rotulada **ativar o acesso ao Azure Disk Encryption para encriptação de volume**.
3. Selecione **ativar o acesso às máquinas de virtuais do Azure para a implantação** e/ou **ativar o acesso ao Azure Resource Manager para a implementação de modelo**, se for necessário. 
4. Clique em **Guardar**.

![Cofre de chaves do Azure, as políticas de acesso avançadas](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Configurar uma chave de encriptação de chave (opcional)
Se pretender utilizar uma chave de encriptação de chaves (KEK) para uma camada adicional de segurança para as chaves de encriptação, adicione um KEK ao seu Cofre de chaves. Utilize o [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet para criar uma chave de encriptação de chave no Cofre de chaves. Também pode importar um KEK de sua gestão de chaves HSM no local. Para obter mais informações, consulte [documentação do Cofre de chave](../key-vault/key-vault-hsm-protected-keys.md). Quando é especificada uma chave de encriptação de chave, o Azure Disk Encryption utiliza essa chave para encapsular os segredos de encriptação antes de escrever para o Key Vault. 

* Ao gerar as chaves, utilize um tipo de chave RSA. O Azure Disk Encryption não suporta ainda a utilização de chaves de criptografia de curva elíptica.

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
     
 #Step 4: Create a new key in the key vault with the Add-AzureKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
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
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
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
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Ativar a encriptação de disco do Azure para Windows](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [Ativar a encriptação de disco do Azure para Linux](azure-security-disk-encryption-linux-aad.md)
