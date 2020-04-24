---
title: Gerir a execução de automação azure como contas
description: Este artigo descreve como gerir o seu Run As contas com powerShell, ou a partir do portal.
services: automation
ms.subservice: shared-capabilities
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 5cb3752e5a74f26936efcbb9dba5cdcda76e01f4
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113311"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Gerir a execução de automação azure como contas

Executar Como contas na Azure Automation fornecem autenticação para gestão de recursos em Azure usando os cmdlets Azure. Ao criar uma conta Run As, cria um novo utilizador principal de serviço no Azure Ative Directory (AD) e atribui a função de Colaborador a este utilizador ao nível da subscrição.

## <a name="types-of-run-as-accounts"></a>Tipos de execução Como contas

A Azure Automation utiliza dois tipos de Run As contas:

* Conta Run As do Azure
* Corrida Clássica azure Como conta

>[!NOTE]
>As subscrições do Azure Cloud Solution Provider (CSP) suportam apenas o modelo Do Gestor de Recursos Azure. Os serviços de Gestor de Recursos Não-Azure não estão disponíveis no programa. Quando está a utilizar uma subscrição de CSP, a conta Azure Classic Run Como conta não é criada, mas a conta Azure Run As é criada. Para saber mais sobre subscrições de CSP, consulte [os serviços disponíveis nas subscrições do CSP.](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)

O diretor de serviço de uma Conta Não tem permissões para ler Azure AD por padrão. Se quiser adicionar permissões para ler ou gerir o Azure AD, terá de conceder as permissões no diretor de serviço sob **permissões DaPI**. Para saber mais, consulte [Adicionar permissões para aceder a APIs web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>Conta Run As

O Run As conta gere os recursos do modelo de [implementação do Gestor](../azure-resource-manager/management/deployment-models.md) de Recursos. Faz as seguintes tarefas.

* Cria uma aplicação do Azure AD com um certificado autoassinado, cria uma conta de principal de serviço para a aplicação no Azure AD e atribui a função Contribuidor à conta na sua subscrição atual. Pode alterar a definição de certificado para Proprietário ou qualquer outra função. Para obter mais informações, veja [Controlo de acesso baseado em funções na Automatização do Azure](automation-role-based-access-control.md).
  
* Cria um ativo `AzureRunAsCertificate` de certificado de automação nomeado na conta de Automação especificada. O ativo do certificado detém a chave privada do certificado que a aplicação Azure AD utiliza.
  
* Cria um ativo `AzureRunAsConnection` de ligação automation nomeado na conta de Automação especificada. O ativo de ligação detém o ID da aplicação, id do inquilino, ID de subscrição e impressão digital do certificado.

### <a name="azure-classic-run-as-account"></a>Conta Run As Clássica do Azure

O Azure Classic Run Como conta gere os recursos do modelo de [implementação clássico.](../azure-resource-manager/management/deployment-models.md) Deve ser coadministrador na subscrição para criar ou renovar este tipo de conta.

A conta Azure Classic Run As conta executa as seguintes tarefas.

  * Cria um certificado de gestão na subscrição.

  * Cria um ativo `AzureClassicRunAsCertificate` de certificado de automação nomeado na conta de Automação especificada. O recurso do certificado contém a chave privada do certificado que o certificado de gestão utiliza.

  * Cria um ativo `AzureClassicRunAsConnection` de ligação automation nomeado na conta de Automação especificada. O ativo de ligação detém o nome de subscrição, id de subscrição e nome do ativo do certificado.

>[!NOTE]
>Azure Classic Run Uma conta não é criada por padrão ao mesmo tempo que cria uma conta Automation. Esta conta é criada individualmente seguindo os passos descritos mais tarde neste artigo.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Executar Como permissões de conta

Esta secção define permissões tanto para o Run Regular Como contas como para o Classic Run As contas.

### <a name="permissions-to-configure-run-as-accounts"></a>Permissões para configurar Executar Como contas

Para criar ou atualizar uma conta Run As, você deve ter privilégios e permissões específicas. Um administrador de aplicação no Azure Ative Directory e um Proprietário numa subscrição podem completar todas as tarefas. Numa situação em que se tem separação de funções, a tabela seguinte apresenta uma listagem das tarefas, o equivalente cmdlet e permissões necessárias:

|Tarefa|Cmdlet  |Permissões Mínimas  |Onde você definir as permissões|
|---|---------|---------|---|
|Criar aplicação ad azure|[Nova AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Papel de desenvolvedor de aplicações<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Registos de aplicações da > Azure AD > |
|Adicione uma credencial à aplicação.|[Novo AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Administrador de Aplicação ou Administrador Global<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Registos de aplicações da > Azure AD >|
|Criar e obter um diretor de serviço azure AD|[Novo AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Administrador de Aplicação ou Administrador Global<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Registos de aplicações da > Azure AD >|
|Atribuir ou obter o papel RBAC para o principal especificado|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrador ou Proprietário de Acesso ao Utilizador, ou tem as seguintes permissões:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Subscrição](../role-based-access-control/role-assignments-portal.md)</br>Assinaturas de \<>\> > nome de subscrição - Controlo de Acesso (IAM)|
|Criar ou remover um certificado de Automação|[Novo Certificado de Automação](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remover-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Contribuinte no grupo de recursos         |Grupo de recursos de conta de automação|
|Criar ou remover uma ligação automação|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remover-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Contribuinte no grupo de recursos |Grupo de recursos de conta de automação|

<sup>1</sup> Os utilizadores não administradores do seu inquilino Azure AD podem [registar aplicações aD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) se os Utilizadores do inquilino da AD Azure **puderem registar** a opção de aplicações na página de definições do Utilizador está definida para **Sim**. Se a definição de registo da aplicação for **Não,** o utilizador que executa esta ação deve ser definido nesta tabela.

Se não for membro da instância de Diretório Ativo da subscrição antes de ser adicionado ao papel de Administrador Global da subscrição, é adicionado como convidado. Nesta situação, recebe `You do not have permissions to create…` um aviso na página da Conta Add Automation. 

Se for membro da instância de Diretório Ativo da subscrição quando a função `You do not have permissions to create…` de Administrador Global é atribuída, também pode receber um aviso na página da Conta de Automação Add. Neste caso, pode solicitar a remoção da instância de Diretório Ativo da subscrição e, em seguida, solicitar a sua readição, para que se torne um utilizador completo no Diretório Ativo.

Para verificar se a situação que produz a mensagem de erro foi corrigida:

1. A partir do painel de Diretório Ativo Azure no portal Azure, selecione **Utilizadores e grupos**. 
2. Selecione **todos os utilizadores**.
3. Escolha o seu nome e, em seguida, selecione **Perfil**. 
4. Certifique-se de que o valor do **atributo** do tipo Utilizador no perfil do utilizador não está definido para **o Hóspede**.

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Permissões para configurar Classic Run Como contas

Para configurar ou renovar o Classic Run As contas, deve ter o papel de Coadministrador ao nível da subscrição. Para saber mais sobre permissões clássicas de subscrição, consulte [os administradores de subscrição clássicos do Azure.](../role-based-access-control/classic-administrators.md#add-a-co-administrator)

## <a name="creating-a-run-as-account-in-azure-portal"></a>Criar uma conta Como Conta No portal Azure

Execute os seguintes passos para atualizar a sua conta Azure Automation no portal Azure. Crie a Run As e Classic Run Como contas individualmente. Se não precisar de gerir os recursos clássicos, pode simplesmente criar a conta Run As do Azure.

1. Inicie sessão no portal do Azure com uma conta que seja membro da função de Administradores da Subscrição e o coadministrador da subscrição.
2. Procure e selecione **Contas de Automação**.
3. Na página Contas de Automação, selecione a sua conta De automação na lista.
4. No painel esquerdo, selecione **Executar Como Contas** na secção de definições da conta.
5. Consoante a conta que precisar, selecione **Conta Run As do Azure** ou **Conta Run As Clássica do Azure**. 
6. Dependendo da conta de juros, use o **Add Azure Run As** ou Add **Azure Classic Run As Account** pane. Depois de rever as informações sobre a visão geral, clique em **Criar**.
6. Enquanto o Azure cria a conta Run As, pode acompanhar o progresso em **Notificações** a partir do menu. É também apresentado um banner afirmando que a conta está a ser criada. O processo pode levar alguns minutos para ser concluído.

## <a name="creating-a-run-as-account-using-powershell"></a>Criar uma conta Run As usando powerShell

A lista seguinte fornece os requisitos para criar uma conta Run As no PowerShell. Estes requisitos aplicam-se a ambos os tipos de Run As contas.

* Windows 10 ou Windows Server 2016 com módulos Azure Resource Manager 3.4.1 e posteriormente. O script PowerShell não suporta versões anteriores do Windows.
* Azure PowerShell 1.0 e posterior. Para obter mais informações sobre a versão do PowerShell 1.0, veja [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Como instalar e configurar o Azure PowerShell).
* Uma conta de Automação, que é `AutomationAccountName` `ApplicationDisplayName` referenciada como o valor para os e parâmetros.
* Permissões equivalentes às listadas nas [permissões necessárias para configurar executar como contas](#permissions).

Para obter os `SubscriptionId` `ResourceGroupName`valores para , e , que são necessários parâmetros para o script PowerShell, complete os próximos passos.

1. No portal Azure, selecione **Contas de Automação.**
1. Na página Contas de Automação, selecione a sua conta De automação.
1. Na secção de definições da conta, selecione **Propriedades**.
1. Note os valores para **NOME,** **ID DE SUBSCRIÇÃO**e **GRUPO DE RECURSOS** na página Propriedades. Estes valores correspondem `AutomationAccountName`aos `SubscriptionId`valores dos parâmetros do script , e `ResourceGroupName` do PowerShell, respectivamente.

   ![Página de propriedades de conta de automação](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Script PowerShell para criar uma conta Run As

Esta secção fornece um script PowerShell para criar uma conta Run As. O script inclui suporte para várias configurações.

* Utilizar um certificado autoassinado para criar uma conta Run As.
* Utilizar um certificado autoassinado para criar uma conta Run As e uma conta Run As Clássica.
* Criar uma conta Run As e uma conta Run As Clássica mediante a utilização de um certificado emitido pela sua autoridade de certificação empresarial (AC).
* Utilizar um certificado autoassinado na cloud do Azure Government para criar uma conta Run As e uma conta Run As Clássica.

O script utiliza vários cmdlets do Gestor de Recursos Azure para criar recursos. Para as cmdlets e as permissões que necessitam, consulte [permissões para configurar executar como contas](#permissions-to-configure-run-as-accounts).

Guarde o script no seu computador utilizando o nome de ficheiro **New-RunAsAccount.ps1**.

```powershell
#Requires -RunAsAdministrator
Param (
    [Parameter(Mandatory = $true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory = $true)]
    [String] $AutomationAccountName,

    [Parameter(Mandatory = $true)]
    [String] $ApplicationDisplayName,

    [Parameter(Mandatory = $true)]
    [String] $SubscriptionId,

    [Parameter(Mandatory = $true)]
    [Boolean] $CreateClassicRunAsAccount,

    [Parameter(Mandatory = $true)]
    [String] $SelfSignedCertPlainPassword,

    [Parameter(Mandatory = $false)]
    [string] $EnterpriseCertPathForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPathForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [ValidateSet("AzureCloud", "AzureUSGovernment")]
    [string]$EnvironmentName = "AzureCloud",

    [Parameter(Mandatory = $false)]
    [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
)

function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
    [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
    $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
        -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
        -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

    $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
    Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
}

function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {
    $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
    $keyId = (New-Guid).Guid

    # Create an Azure AD application, AD App Credential, AD ServicePrincipal

    # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
    $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
    # Requires Application administrator or GLOBAL ADMIN
    $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
    # Requires Application administrator or GLOBAL ADMIN
    $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
    $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

    # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
    Sleep -s 15
    # Requires User Access Administrator or Owner.
    $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 6) {
        Sleep -s 10
        New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
        $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries++;
    }
    return $Application.ApplicationId.ToString();
}

function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
    $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
    Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
    New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
}

function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
    Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
    New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
}

Import-Module AzureRm.Profile
Import-Module AzureRm.Resources

$AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
    Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
    return
}

# To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

# Import-Module Az.Automation
# Enable-AzureRmAlias


Connect-AzAccount -Environment $EnvironmentName
$Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

# Create a Run As account by using a service principal
$CertifcateAssetName = "AzureRunAsCertificate"
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionTypeName = "AzureServicePrincipal"

if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
    $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
    $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
}
else {
    $CertificateName = $AutomationAccountName + $CertifcateAssetName
    $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
    $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
    $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
    CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
}

# Create a service principal
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
$ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

# Create the Automation certificate asset
CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

# Populate the ConnectionFieldValues
$SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
$TenantID = $SubscriptionInfo | Select TenantId -First 1
$Thumbprint = $PfxCert.Thumbprint
$ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

# Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

if ($CreateClassicRunAsAccount) {
    # Create a Run As account by using a service principal
    $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
    $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
    $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
    $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
    "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
    "Then click Upload and upload the .cer format of #CERT#"

    if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
        $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
        $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
        $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
    }
    else {
        $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
        $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
        $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
        $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
        CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }
    
    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

    # Populate the ConnectionFieldValues
    $SubscriptionName = $subscription.Subscription.Name
    $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

    Write-Host -ForegroundColor red       $UploadMessage
}
```

>[!NOTE]
>`Add-AzAccount`e `Add-AzureRMAccount` são pseudónimos de [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Pode utilizar estes cmdlets ou pode [atualizar os seus módulos](automation-update-azure-modules.md) na sua conta Automation para as versões mais recentes. Pode precisar de atualizar os seus módulos mesmo que tenha acabado de criar uma nova conta Automation.

### <a name="execute-the-powershell-script"></a>Execute o script PowerShell

1. No seu computador, inicie o **Windows PowerShell** a partir do ecrã **Iniciar** ecrã com direitos de utilizador elevados.
1. A partir da concha de linha de comando elevada, vá para a pasta que contém o seu script.
1. Execute o script utilizando os valores do parâmetro para a configuração que necessita.
1. Se criar uma conta Classic Run As, após a execução do script, faça o upload do certificado público (extensão de nome de ficheiro **.cer)** para a loja de gestão para a subscrição em que a conta Automation foi criada.

Depois do guião ser executado, és solicitado a autenticar com o Azure. Inscreva-se numa conta que seja membro do papel de administradores de subscrição e coadministrador da subscrição.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Utilizar um certificado autoassinado para criar uma conta Run As

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Utilizar um certificado autoassinado para criar uma conta Run As e uma conta Run As Clássica

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Utilizar um certificado empresarial para criar uma conta Run As e uma conta Run As Clássica

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Se criou uma conta Classic Run As com um certificado público da empresa (ficheiro **.cer),** utilize este certificado. Consulte o upload de um certificado DePI de [gestão para o portal Azure](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Crie uma conta Run As e uma conta Classic Run As usando um certificado auto-assinado na nuvem do Governo Azure

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Se criou uma conta Classic Run As com um certificado público auto-assinado (ficheiro **.cer),** o script cria e guarda-o para a pasta de ficheiros temporários no seu computador. Pode ser encontrado no `%USERPROFILE%\AppData\Local\Temp`perfil do utilizador , que utilizou para executar a sessão PowerShell.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Apagar uma corrida como ou clássica corrida como conta

Esta secção descreve como eliminar uma conta Run As ou Classic Run As. Quando executar esta ação, a conta de Automatização é mantida. Depois de apagar a conta, pode recriá-la no portal Azure.

1. No portal do Azure, abra a conta de Automatização.

2. No painel esquerdo, selecione **Executar Como Contas** na secção de definições da conta.

3. Nas propriedades Contas Run As, selecione a conta Run As ou a conta Run As Clássica que quer eliminar. 

4. No painel 'Propriedades' para a conta selecionada, clique em **Apagar**.

   ![Eliminar a conta Run As](media/manage-runas-account/automation-account-delete-runas.png)

5. Enquanto a conta estiver a ser eliminada, pode acompanhar o progresso em **Notificações** a partir do menu.

6. Assim que a conta tiver sido eliminada, pode voltar a criá-la na página de propriedades Contas Run As, ao selecionar a opção de criação **Conta Run As do Azure**.

   ![Recriar a conta Run As de Automatização](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Renovação de um certificado auto-assinado

O certificado auto-assinado que criou para a conta Run As expira um ano a partir da data de criação. Em algum momento antes da sua conta Run As expirar, você deve renovar o certificado. Pode renová-lo a qualquer momento antes que expire. 

Quando renova o certificado auto-assinado, o certificado válido atual é mantido para garantir que quaisquer livros de execução que estejam em fila ou que estejam em execução ativa, e que autenticam com a conta Run As, não sejam negativamente afetados. O certificado permanece válido até à data de expiração.

>[!NOTE]
>Se acha que a conta Run As foi comprometida, pode eliminar e recriar o certificado auto-assinado.

>[!NOTE]
>Se configurar a sua conta Executar As como conta para utilizar um certificado emitido pela autoridade do certificado de empresa e utilizar a opção de renovar uma opção de certificado auto-assinado, o certificado de empresa é substituído por um certificado auto-assinado.

Utilize os seguintes passos para renovar o certificado auto-assinado.

1. No portal do Azure, abra a conta de Automatização.

1. Selecione **Executar Como Contas** na secção de definições da conta.

    ![Painel Propriedades da conta de automatização](media/manage-runas-account/automation-account-properties-pane.png)

1. Na página de propriedades Run As Accounts, selecione a conta Run As ou a Classic Run Como conta para renovar o certificado.

1. No painel de propriedades para a conta selecionada, clique em **Renovar o certificado**.

    ![Renovar certificado da conta Run As](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Enquanto o certificado estiver a ser renovado, pode acompanhar o progresso em **Notificações** a partir do menu.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Criação de renovação automática de certificados com um livro de execução de automação

Para renovar os certificados automaticamente, pode utilizar um livro de execução de automação. Este script no [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) permite esta funcionalidade na sua conta Automation.

>[!NOTE]
>Deve ser administrador global ou administrador de empresa em Azure AD para executar o script.

Este script cria um horário semanal para renovar os certificados de conta Run As. Adiciona um livro de execução **Update-AutomationRunAsCredential** à sua conta Automation. Pode visualizar o código do livro de executantes no GitHub, no script [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). Pode utilizar o código PowerShell no ficheiro para renovar os certificados manualmente, conforme necessário.

Utilize os seguintes passos para testar imediatamente o processo de renovação.

1. Editar o livro de execução **Update-AutomationRunAsCredential** e colocar um personagem de comentário (#) na linha 122, em frente ao comando **Exit(1).**

   ```powershell
   #Exit(1)
   ```

2. Publique o livro de corridas.
3. Inicie o livro de corridas.
4. Verifique a renovação bem sucedida com o seguinte código:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Saída:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Após o teste, edite o livro de execução e remova o personagem de comentário que adicionou no passo 1.
6. Publique o livro de corridas.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>Limitação do execução Como permissões de conta

Para controlar a segmentação da Automação contra recursos no Azure, pode executar o script [Update-AutomationRunAsAccountRoleAssignments.ps1.](https://aka.ms/AA5hug8) Este script altera o seu executar existente Como principal de serviço de conta para criar e usar uma definição de função personalizada. O papel tem permissões para todos os recursos, exceto [o Cofre Chave.](https://docs.microsoft.com/azure/key-vault/)

>[!IMPORTANT]
>Depois de executar o script **Update-AutomationRunAsAccountRoleAssignments.ps1,** os livros de execução que acedem ao Cofre chave através da utilização do Run Como as contas já não funcionam. Antes de executar o script, você deve rever os livros de execução na sua conta para chamadas para Azure Key Vault. Para permitir o acesso ao Key Vault dos livros de execução da Automação Azure, deve [adicionar o Run As conta às permissões do Key Vault](#add-permissions-to-key-vault).

Se precisar de restringir, ainda mais o que o diretor de serviço `NotActions` Run As pode fazer, pode adicionar outros tipos de recursos ao elemento da definição de função personalizada. O exemplo seguinte restringe `Microsoft.Compute/*`o acesso a . Se adicionar este tipo `NotActions` de recurso para a definição de função, a função não será capaz de aceder a qualquer recurso Compute. Para saber mais sobre definições de papéis, consulte Compreender as definições de [papéis para os recursos do Azure.](../role-based-access-control/role-definitions.md)

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Pode determinar se o principal de serviço utilizado pela sua conta Run As está na definição de função Do Colaborador ou numa personalizada. 

1. Vá à sua conta de Automação e selecione **Executar Como Contas** na secção de definições da conta.
2. Selecione **Azure Executar como conta**. 
3. Selecione **Função** para localizar a definição de função que está a ser usada.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Também pode determinar a definição de função utilizada pelo Run As contas de várias subscrições ou contas de Automação. Faça-o utilizando o script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) na PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Adicione permissões ao Cofre chave

Pode permitir que a Automação Azure verifique se o Key Vault e o seu diretor de serviço de conta Como principal de serviço de conta estão a usar uma definição de função personalizada. Tens de o fazer:

* Conceda permissões ao Cofre chave.
* Desestabeleça a política de acesso.

Pode utilizar o script [Extend-AutomationRunAsAccountRoleRoleToKeyVault.ps1](https://aka.ms/AA5hugb) na PowerShell Gallery para dar ao seu Run As permissões de conta para o Cofre de Chaves. Consulte o [acesso dos pedidos grant a um cofre chave](../key-vault/general/group-permissions-for-apps.md) para obter mais detalhes sobre a definição de permissões no Key Vault.

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Resolução de problemas de configuração errada para Executar Como contas

Alguns itens de configuração necessários para uma execução Como ou corrida clássica Como conta pode ter sido eliminada ou criada indevidamente durante a configuração inicial. Possíveis casos de configuração errada incluem:

* Recurso de certificado
* Recurso de ligação
* Executar Como conta removida da função contributiva
* Principal de serviço ou aplicação no Azure AD

Para tais casos de configuração errada, a conta Automation `Incomplete` deteta as alterações e apresenta um estado no painel de propriedades Run As Contas para a conta.

![Estado de configuração Não concluída da conta Run As](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Quando selecionar a conta 'Executar As', o painel de propriedades da conta apresenta a seguinte mensagem de erro:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Pode resolver rapidamente estes problemas da conta Run As, ao eliminar e recriar a conta.

## <a name="next-steps"></a>Passos seguintes

* Para mais informações sobre os principais de serviço, consulte Objetos de [Aplicação e Objetos Principais](../active-directory/develop/app-objects-and-service-principals.md)de Serviço .
* Para obter mais informações sobre certificados e serviços Azure, consulte a visão geral dos [Certificados para os Serviços Azure Cloud.](../cloud-services/cloud-services-certs-create.md)
