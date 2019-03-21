---
title: Gerir contas Run As de automatização
description: Este artigo descreve como gerir as contas Run As com o PowerShell ou a partir do portal.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 09/12/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b3c9f2f8671d5a7aa313a9f49e07230a4f9b6220
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109346"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Gerir contas Run As de automatização

Contas Run As de automatização do Azure são utilizadas para fornecer autenticação para gerir recursos no Azure com os cmdlets do Azure.

Quando cria uma conta Run As, ele cria um novo utilizador principal de serviço no Azure Active Directory e atribui a função de contribuinte a este utilizador ao nível da subscrição. Para runbooks que utilizam os Runbook Workers híbridos em máquinas virtuais do Azure, pode utilizar [geridos identidades para recursos do Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) em vez de contas Run As para autenticar-se aos seus recursos do Azure.

Existem dois tipos de contas Run as:

* **Do Azure conta Run As** -esta conta é utilizada para gerir recursos de modelo de implementação do Resource Manager.
  * Cria uma aplicação do Azure AD com um certificado autoassinado, cria uma conta de principal de serviço para a aplicação no Azure AD e atribui a função Contribuidor à conta na sua subscrição atual. Pode alterar esta definição para Proprietário ou qualquer outra função. Para obter mais informações, veja [Controlo de acesso baseado em funções na Automatização do Azure](automation-role-based-access-control.md).
  * Cria um recurso de certificado da Automatização com o nome *AzureRunAsCertificate* na conta de Automatização especificada. O recurso do certificado contém a chave privada do certificado que a aplicação do Azure AD utiliza.
  * Cria um recurso de ligação da Automatização com o nome *AzureRunAsConnection* na conta de Automatização especificada. O recurso de ligação contém o applicationId, o tenantId, o subscriptionId e o thumbprint do certificado.

* **Do Azure conta Run as clássica** -esta conta é utilizada para gerir os recursos de modelo de implementação clássico.
  * Cria um recurso de certificado da Automatização com o nome *AzureClassicRunAsCertificate* na conta de Automatização especificada. O recurso do certificado contém a chave privada do certificado que o certificado de gestão utiliza.
  * Cria um recurso de ligação da Automatização com o nome *AzureClassicRunAsConnection* na conta de Automatização especificada. O recurso de ligação contém o nome da subscrição, o subscriptionid e o nome de recurso do certificado.
  
  > [!NOTE]
  > Subscrições do fornecedor de soluções Cloud (Azure CSP) do Azure suportam apenas o modelo Azure Resource Manager, serviços de não - Azure Resource Manager não estão disponíveis no programa. Quando utilizar uma subscrição do CSP do Azure clássico a conta Run as não criada. A conta Run as Azure ainda é criada. Para saber mais sobre as subscrições de CSP, veja [serviços disponíveis em subscrições de CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments).

## <a name="permissions"></a>Permissões para configurar contas Run as

Para criar ou atualizar uma conta Run As, tem de ter privilégios específicos e as permissões. Um Global/Coadministrador administrador pode concluir todas as tarefas. Numa situação em que tem a separação de funções, a tabela seguinte mostra uma lista das tarefas, o cmdlet equivalente e as permissões necessárias:

|Tarefa|Cmdlet  |Permissões mínimas  |Em que defina as permissões|
|---|---------|---------|---|
|Criar aplicação do Azure AD|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Função de desenvolvedor do aplicativo<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home page > o Azure Active Directory > registos de aplicações |
|Adicione uma credencial para a aplicação.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | ADMINISTRADOR GLOBAL ou de administrador da aplicação<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home page > o Azure Active Directory > registos de aplicações|
|Criar e obter um principal de serviço do Azure AD|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | ADMINISTRADOR GLOBAL ou de administrador da aplicação        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home page > o Azure Active Directory > registos de aplicações|
|Atribuir ou obter a função RBAC para a entidade de segurança especificada|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Administrador de acesso de utilizador ou proprietário        | [Subscrição](../role-based-access-control/role-assignments-portal.md)</br>Home page > subscrições > \<nome da subscrição\> -controlo de acesso (IAM)|
|Criar ou remover um certificado da automatização|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Contribuinte no grupo de recursos         |Grupo de recursos de conta de automatização|
|Criar ou remover uma ligação da automatização|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Contribuinte no grupo de recursos |Grupo de recursos de conta de automatização|

<sup>1</sup> utilizadores não administradores no inquilino do Azure AD podem [registar aplicações AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) se do inquilino do Azure AD **os utilizadores podem registar aplicações** opção **as definições de utilizador**página está definida como **Sim**. Se a definição dos registos da aplicação for **Não**, o utilizador que executa esta ação tem de ser um administrador global no Azure AD.

Se não é um membro de instância do Active Directory da subscrição antes de serão adicionados para a global/coadministrador função de administrador da subscrição, é adicionado como convidado. Nesta situação, recebe uma `You do not have permissions to create…` aviso sobre a **adicionar conta de automatização** página. Os utilizadores que foram adicionados primeiro à função de administrador global/coadministrador podem ser removidos da instância do Active Directory da subscrição e adicionados novamente, para que se tornem em Utilizadores completos no Active Directory. Para verificar esta situação, no painel **Azure Active Directory**, no portal do Azure, selecione **Utilizadores e grupos**, **Todos os utilizadores** e, depois de selecionar o utilizador específico, selecione **Perfil**. O valor do atributo **Tipo de utilizador** sob o perfil de utilizadores não deve ser igual a **Convidado**.

## <a name="create-a-run-as-account-in-the-portal"></a>Criar uma conta Run As no Portal

Nesta secção, execute os seguintes passos para atualizar a sua conta de Automatização do Azure no portal do Azure. Crie as contas Run As e Classic Run As individualmente. Se não precisar de gerir os recursos clássicos, pode simplesmente criar a conta Run As do Azure.  

1. Inicie sessão no portal do Azure com uma conta que seja membro da função Administradores da Subscrição e coadministrador da subscrição.
2. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, escreva **Automatização**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Contas de Automatização**.
3. Na página **Contas de Automatização**, selecione a sua conta de Automatização a partir da lista de contas de Automatização.
4. No painel da esquerda, selecione **Contas Run As** na secção **Definições da Conta**.  
5. Consoante a conta que precisar, selecione **Conta Run As do Azure** ou **Conta Run As Clássica do Azure**. Depois de selecionar **Adicionar Conta Run As do Azure** ou **Adicionar Conta Run As Clássica do Azure**, é apresentado o painel e, depois de rever as informações de descrição geral, clique em **Criar** para prosseguir com a criação da conta Run As.  
6. Enquanto o Azure cria a conta Run As, pode acompanhar o progresso em **Notificações** a partir do menu. Também é apresentada uma faixa a indicar que a conta está a ser criada. Este processo pode demorar alguns minutos a concluir.  

## <a name="create-run-as-account-using-powershell"></a>Criar conta Run As com o PowerShell

## <a name="prerequisites"></a>Pré-requisitos

A lista seguinte fornece os requisitos para criar uma conta Run As no PowerShell:

* Windows 10 ou Windows Server 2016 com módulos do Azure Resource Manager 3.4.1 e posterior. O script do PowerShell não suporta versões anteriores do Windows.
* Azure PowerShell 1.0 e posterior. Para obter mais informações sobre a versão do PowerShell 1.0, veja [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Como instalar e configurar o Azure PowerShell).
* Uma conta de Automatização, que é referenciada como o valor para os parâmetros *–AutomationAccountName* e *-ApplicationDisplayName*.
* Permissões equivalentes à que está listado no [permissões necessárias para configurar contas Run as](#permissions)

Para obter os valores para *SubscriptionID*, *ResourceGroup*, e *AutomationAccountName*, que são parâmetros necessários para o script, conclua os seguintes passos:

1. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, escreva **Automatização**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Contas de Automatização**.
1. Na página da conta de Automatização, selecione a sua conta de Automatização e, em **Definições da Conta** selecione **Propriedades**.  
1. Tenha em atenção a **ID de subscrição**, **nome**, e **grupo de recursos** valores no **propriedades** página.

   ![A página de "Propriedades" da conta de automatização](media/manage-runas-account/automation-account-properties.png)

Este script do PowerShell inclui suporte para as seguintes configurações:

* Utilizar um certificado autoassinado para criar uma conta Run As.
* Utilizar um certificado autoassinado para criar uma conta Run As e uma conta Run As Clássica.
* Criar uma conta Run As e uma conta Run As Clássica mediante a utilização de um certificado emitido pela sua autoridade de certificação empresarial (AC).
* Utilizar um certificado autoassinado na cloud do Azure Government para criar uma conta Run As e uma conta Run As Clássica.

>[!NOTE]
> Se selecionar uma destas opções para criar uma conta Run As Clássica, após o script ser executado, carregue o certificado público (extensão de nome de ficheiro .cer) para o arquivo de gestão da subscrição na qual a conta de Automatização foi criada.

1. Guarde o seguinte script no seu computador. Neste exemplo, guarde-o com o nome de ficheiro *New-RunAsAccount.ps1*.

   O script usa vários cmdlets do Azure Resource Manager para criar recursos. A tabela seguinte mostra os cmdlets e as respetivas permissões necessárias.

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
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous two lines to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias 


    Connect-AzureRmAccount -Environment $EnvironmentName 
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

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
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
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

    > [!IMPORTANT]
    > **Add-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Quando pesquisar a sua biblioteca de itens, se não vir **Connect-AzureRMAccount**, pode utilizar **Add-AzureRmAccount**, ou pode [atualizar seus módulos](automation-update-azure-modules.md) na sua Automação Conta.

1. No seu computador, inicie o **Windows PowerShell** a partir do ecrã **Iniciar** ecrã com direitos de utilizador elevados.
1. A partir da shell da linha de comandos elevada, aceda à pasta que contém o script que criou no passo 1.  
1. Execute o script, utilizando os valores de parâmetros da configuração de que precisa.

    **Utilizar um certificado autoassinado para criar uma conta Run As**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **Utilizar um certificado autoassinado para criar uma conta Run As e uma conta Run As Clássica**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **Utilizar um certificado empresarial para criar uma conta Run As e uma conta Run As Clássica**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Utilizar um certificado autoassinado na cloud do Azure Government para criar uma conta Run As e uma conta Run As Clássica**
  
    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > Após o script ser executado, ser-lhe-á pedido para autenticar com o Azure. Inicie sessão com uma conta que seja membro da função de administradores da subscrição e coadministrador da subscrição.

Depois de o script ser executado com êxito, tenha em conta o seguinte:

* Se tiver criado uma conta Run As Clássica com um certificado público autoassinado (ficheiro .cer), o script cria-o e guarda-o na pasta de ficheiros temporários no seu computador, no perfil de utilizador *%USERPROFILE%\AppData\Local\Temp* que utilizou para executar a sessão do PowerShell.

* Se tiver criado uma conta Run As Clássica com um certificado público empresarial (ficheiro .cer), utilize esse certificado. Siga as instruções para [carregar um certificado de gestão de API para o portal do Azure](../azure-api-management-certs.md).

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminar uma conta Run As ou Run As Clássica

Esta secção descreve como eliminar e recriar uma conta Run As ou Run As Clássica. Quando executar esta ação, a conta de Automatização é mantida. Depois de eliminar uma conta Run As ou Run As Clássica, pode voltar a criá-la no portal do Azure.

1. No portal do Azure, abra a conta de Automatização.

2. Na página **Conta de automatização**, selecione **Contas Run as**.

3. Nas propriedades **Contas Run As**, selecione a conta Run As ou a conta Run As Clássica que quer eliminar. Em seguida, no painel **Propriedades** da conta selecionada, clique em **Eliminar**.

   ![Eliminar a conta Run As](media/manage-runas-account/automation-account-delete-runas.png)

1. Enquanto a conta estiver a ser eliminada, pode acompanhar o progresso em **Notificações** a partir do menu.

1. Assim que a conta tiver sido eliminada, pode voltar a criá-la na página de propriedades **Contas Run As**, ao selecionar a opção de criação **Conta Run As do Azure**.

   ![Recriar a conta Run As de Automatização](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Renovação do certificado autoassinado

Em algum momento antes de expira a sua conta Run As, terá de renovar o certificado. Se considerar que a conta Run As tiver sido comprometida, pode eliminá-la e voltar a criá-la. Esta secção mostra como executar estas operações.

O certificado autoassinado que criou para a conta Run As expira ao fim de um ano após a data de criação. Pode renová-lo em qualquer altura antes de expirar. Quando o renovar, o certificado atual válido é mantido, para garantir que todos os runbooks que são colocados em fila ou ativamente em execução e que se autenticam com a conta Run As, não são afetados negativamente. O certificado permanece válido até à data de expiração.

> [!NOTE]
> Se tiver configurado a sua conta Run As de Automatização para utilizar um certificado emitido pela sua autoridade de certificação empresarial e utilizar esta opção, esse certificado empresarial é substituído por um certificado autoassinado.

Para renovar o certificado, faça o seguinte:

1. No portal do Azure, abra a conta de Automatização.

1. Selecione **contas Run as** sob **definições da conta**.

    ![Painel Propriedades da conta de automatização](media/manage-runas-account/automation-account-properties-pane.png)

1. Nas propriedades **Contas Run As**, selecione a conta Run As ou a conta Run As Clássica para a qual pretende renovar o certificado.

1. No painel **Propriedades** da conta selecionada, clique em **Renovar certificado**.

    ![Renovar certificado da conta Run As](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Enquanto o certificado estiver a ser renovado, pode acompanhar o progresso em **Notificações** a partir do menu. 

## <a name="limiting-run-as-account-permissions"></a>Limitar as permissões de conta Run As

Para controlar o direcionamento de automatização relativamente aos recursos na automatização do Azure, a conta Run As, por padrão é concedida direitos de contribuinte na subscrição. Se precisar de restringir o que o principal de serviço RunAs pode fazer, pode remover a conta da função de contribuinte à subscrição e adicioná-lo como um contribuinte para os grupos de recursos que pretende especificar.

No portal do Azure, selecione **subscrições** e escolha a subscrição da sua conta de automatização. Selecione **controlo de acesso (IAM)** e, em seguida, selecione a **atribuições de funções** separador. Procure o principal de serviço para a sua conta de automatização (parece \<AutomationAccountName\>_unique identificador). Selecione a conta e clique em **remover** removê-lo a partir da subscrição.

![Contribuintes de subscrição](media/manage-runas-account/automation-account-remove-subscription.png)

Para adicionar o principal de serviço para um grupo de recursos, selecione o grupo de recursos no portal do Azure e selecione **controlo de acesso (IAM)**. Selecione **adicionar atribuição de função**, esta ação abre o **adicionar atribuição de função** página. Para **função**, selecione **contribuinte**. Na **selecione** escreva o nome do principal de serviço para a conta Run As de caixa de texto e, selecione-o na lista. Clique em **Guardar** para guardar as alterações. Conclua estes passos para os grupos de recursos que pretende dar a Run As de automatização principal de serviço acesso para.

## <a name="misconfiguration"></a>Configuração incorreta

É possível que alguns itens de configuração necessários para a conta Run As ou Run As Clássica funcionar corretamente tenham sido eliminados ou criados de forma incorreta durante a configuração inicial. Os itens incluem:

* Recurso de certificado
* Recurso de ligação
* A conta Run As foi removida da função de contribuinte
* Principal de serviço ou aplicação no Azure AD

Nas configurações incorretas anteriores e noutras, a conta de Automatização deteta as alterações e apresenta o estado *Não concluída*, no painel de propriedades **Contas Run As** da conta.

![Estado de configuração Não concluída da conta Run As](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Quando selecionar a conta Run As, o painel **Propriedades** da conta apresenta a mensagem de erro seguinte:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Pode resolver rapidamente estes problemas da conta Run As, ao eliminar e recriar a conta.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre principais de serviço, consulte [objectos da aplicação e objetos de Principal de serviço](../active-directory/develop/app-objects-and-service-principals.md).
* Para obter mais informações sobre certificados e serviços do Azure, consulte [descrição geral de certificados para serviços Cloud do Azure](../cloud-services/cloud-services-certs-create.md).

