---
title: Gerenciar contas Executar como da automação do Azure
description: Este artigo descreve como gerenciar suas contas Executar como com o PowerShell ou no Portal.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2ce6c27487e834325b59fb21ba0d54d1e788ea57
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951382"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Gerenciar contas Executar como da automação do Azure

As contas Executar como na automação do Azure são usadas para fornecer autenticação para gerenciar recursos no Azure com os cmdlets do Azure.

Quando você cria uma conta Executar como, ela cria um novo usuário de entidade de serviço no Azure Active Directory e atribui a função colaborador a esse usuário no nível de assinatura. Para runbooks que usam Hybrid runbook Workers em máquinas virtuais do Azure, você pode usar [identidades gerenciadas para recursos do Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) em vez de contas Executar como para autenticar os recursos do Azure.

Há dois tipos de contas Executar como:

* **Conta Executar como do Azure** -essa conta é usada para gerenciar recursos do [modelo de implantação do Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) .
  * Cria uma aplicação do Azure AD com um certificado autoassinado, cria uma conta de principal de serviço para a aplicação no Azure AD e atribui a função Contribuidor à conta na sua subscrição atual. Pode alterar esta definição para Proprietário ou qualquer outra função. Para obter mais informações, veja [Controlo de acesso baseado em funções na Automatização do Azure](automation-role-based-access-control.md).
  * Cria um recurso de certificado da Automatização com o nome *AzureRunAsCertificate* na conta de Automatização especificada. O recurso do certificado contém a chave privada do certificado que a aplicação do Azure AD utiliza.
  * Cria um recurso de ligação da Automatização com o nome *AzureRunAsConnection* na conta de Automatização especificada. O recurso de ligação contém o applicationId, o tenantId, o subscriptionId e o thumbprint do certificado.

* **Conta Executar como clássica do Azure** -essa conta é usada para gerenciar os recursos do [modelo de implantação clássico](../azure-resource-manager/resource-manager-deployment-model.md) .
  * Cria um certificado de gerenciamento na assinatura
  * Cria um recurso de certificado da Automatização com o nome *AzureClassicRunAsCertificate* na conta de Automatização especificada. O recurso do certificado contém a chave privada do certificado que o certificado de gestão utiliza.
  * Cria um recurso de ligação da Automatização com o nome *AzureClassicRunAsConnection* na conta de Automatização especificada. O recurso de ligação contém o nome da subscrição, o subscriptionid e o nome de recurso do certificado.
  * Deve ser um coadministrador na assinatura para criar ou renovar

  > [!NOTE]
  > As assinaturas do provedor de soluções na nuvem do Azure (CSP do Azure) dão suporte apenas ao modelo de Azure Resource Manager, os serviços não Azure Resource Manager não estão disponíveis no programa. Ao usar uma assinatura do CSP, a conta Executar como clássica do Azure não é criada. A conta Executar como do Azure ainda é criada. Para saber mais sobre assinaturas do CSP, confira [serviços disponíveis em assinaturas do CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments).

  > [!NOTE]
  > A entidade de serviço para uma conta Executar como não tem permissões para ler Azure Active Directory por padrão. Se você quiser adicionar permissões para ler ou gerenciar o Azure Active Directory, será necessário conceder essa permissão na entidade de serviço sob permissões de **API**. Para saber mais, consulte [adicionar permissões para acessar APIs da Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="permissions"></a>Permissões para configurar contas Executar como

Para criar ou atualizar uma conta Executar como, você deve ter privilégios e permissões específicas. Um administrador de aplicativos no Azure Active Directory e um proprietário em uma assinatura podem concluir todas as tarefas. Em uma situação em que você tem separação de tarefas, a tabela a seguir mostra uma listagem das tarefas, o cmdlet equivalente e as permissões necessárias:

|Tarefa|Cmdlet  |Permissões mínimas  |Onde você define as permissões|
|---|---------|---------|---|
|Criar aplicativo do Azure AD|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Função de desenvolvedor de aplicativo<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>> Página inicial Azure Active Directory > registros de aplicativo |
|Adicione uma credencial ao aplicativo.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Administrador de aplicativos ou administrador GLOBAL<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>> Página inicial Azure Active Directory > registros de aplicativo|
|Criar e obter uma entidade de serviço do Azure AD|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Administrador de aplicativos ou administrador GLOBAL<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>> Página inicial Azure Active Directory > registros de aplicativo|
|Atribuir ou obter a função de RBAC para a entidade de segurança especificada|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Você deve ter as seguintes permissões:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br>Ou ser um:</br></br>Administrador ou proprietário de acesso do usuário        | [Subscrição](../role-based-access-control/role-assignments-portal.md)</br>> De assinaturas de página inicial > \<nome da assinatura\>-controle de acesso (IAM)|
|Criar ou remover um certificado de automação|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Colaborador no grupo de recursos         |Grupo de recursos da conta de automação|
|Criar ou remover uma conexão de automação|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Colaborador no grupo de recursos |Grupo de recursos da conta de automação|

<sup>1</sup> os usuários não administradores em seu locatário do Azure ad podem [registrar aplicativos do AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) se a opção os usuários do locatário do Azure ad **podem registrar aplicativos** na página **configurações do usuário** estiver definida como **Sim**. Se a configuração registros do aplicativo estiver definida como **não**, o usuário que executa essa ação deverá ser o que está definido na tabela anterior.

Se você não for um membro da instância de Active Directory da assinatura antes de ser adicionado à função de **administrador global** da assinatura, você será adicionado como convidado. Nessa situação, você receberá uma `You do not have permissions to create…` aviso na página **adicionar conta de automação** . Os usuários que foram adicionados à função de **administrador global** primeiro podem ser removidos da instância de Active Directory da assinatura e adicionados novamente para torná-los um usuário completo no Active Directory. Para verificar esta situação, no painel **Azure Active Directory**, no portal do Azure, selecione **Utilizadores e grupos**, **Todos os utilizadores** e, depois de selecionar o utilizador específico, selecione **Perfil**. O valor do atributo **Tipo de utilizador** sob o perfil de utilizadores não deve ser igual a **Convidado**.

## <a name="permissions-classic"></a>Permissões para configurar contas Executar como clássicas

Para configurar ou renovar contas Executar como clássicas, você deve ter a função de **coadministrador** no nível de assinatura. Para saber mais sobre permissões clássicas, confira [Administradores de assinatura clássica do Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-the-portal"></a>Criar uma conta Executar como no portal

Nesta secção, execute os seguintes passos para atualizar a sua conta de Automatização do Azure no portal do Azure. Crie as contas Run As e Classic Run As individualmente. Se não precisar de gerir os recursos clássicos, pode simplesmente criar a conta Run As do Azure.

1. Inicie sessão no portal do Azure com uma conta que seja membro da função Administradores da Subscrição e coadministrador da subscrição.
2. Em portal do Azure, procure e selecione **contas de automação**.
3. Na página **Contas de Automatização**, selecione a sua conta de Automatização a partir da lista de contas de Automatização.
4. No painel da esquerda, selecione **Contas Run As** na secção **Definições da Conta**.
5. Consoante a conta que precisar, selecione **Conta Run As do Azure** ou **Conta Run As Clássica do Azure**. Depois de selecionar **Adicionar Conta Run As do Azure** ou **Adicionar Conta Run As Clássica do Azure**, é apresentado o painel e, depois de rever as informações de descrição geral, clique em **Criar** para prosseguir com a criação da conta Run As.
6. Enquanto o Azure cria a conta Run As, pode acompanhar o progresso em **Notificações** a partir do menu. Também é apresentada uma faixa a indicar que a conta está a ser criada. Este processo pode demorar alguns minutos a concluir.

## <a name="create-run-as-account-using-powershell"></a>Criar conta Executar como usando o PowerShell

## <a name="prerequisites"></a>Pré-requisitos

A lista a seguir fornece os requisitos para criar uma conta Executar como no PowerShell:

* Windows 10 ou Windows Server 2016 com módulos Azure Resource Manager 3.4.1 e posterior. O script do PowerShell não suporta versões anteriores do Windows.
* Azure PowerShell 1.0 e posterior. Para obter mais informações sobre a versão do PowerShell 1.0, veja [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Como instalar e configurar o Azure PowerShell).
* Uma conta de Automatização, que é referenciada como o valor para os parâmetros *–AutomationAccountName* e *-ApplicationDisplayName*.
* Permissões equivalentes ao que está listado em [permissões necessárias para configurar contas Executar como](#permissions)

Para obter os valores de *SubscriptionId*, *resourcegroup*e *AutomationAccountName*, que são parâmetros necessários para o script, conclua as seguintes etapas:

1. Em portal do Azure, procure e selecione **contas de automação**.
1. Na página da conta de Automatização, selecione a sua conta de Automatização e, em **Definições da Conta** selecione **Propriedades**.
1. Observe os valores de **ID da assinatura**, **nome**e **grupo de recursos** na página **Propriedades** .

   ![A página de "Propriedades" da conta de automação](media/manage-runas-account/automation-account-properties.png)

Este script do PowerShell inclui suporte para as seguintes configurações:

* Utilizar um certificado autoassinado para criar uma conta Run As.
* Utilizar um certificado autoassinado para criar uma conta Run As e uma conta Run As Clássica.
* Criar uma conta Run As e uma conta Run As Clássica mediante a utilização de um certificado emitido pela sua autoridade de certificação empresarial (AC).
* Utilizar um certificado autoassinado na cloud do Azure Government para criar uma conta Run As e uma conta Run As Clássica.

>[!NOTE]
> Se selecionar uma destas opções para criar uma conta Run As Clássica, após o script ser executado, carregue o certificado público (extensão de nome de ficheiro .cer) para o arquivo de gestão da subscrição na qual a conta de Automatização foi criada.

1. Guarde o seguinte script no seu computador. Neste exemplo, guarde-o com o nome de ficheiro *New-RunAsAccount.ps1*.

   O script usa vários cmdlets Azure Resource Manager para criar recursos. A tabela de [permissões](#permissions) precedentes mostra os cmdlets e suas permissões necessárias.

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

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

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
    > **Add-AzureRmAccount** agora é um alias para **Connect-AzureRmAccount**. Ao pesquisar os itens de biblioteca, se você não vir **Connect-AzureRMAccount**, poderá usar **Add-AzureRMAccount**ou poderá [atualizar seus módulos](automation-update-azure-modules.md) na sua conta de automação.

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

* Se tiver criado uma conta Run As Clássica com um certificado público empresarial (ficheiro .cer), utilize esse certificado. Siga as instruções para [carregar um certificado de API de gerenciamento para o portal do Azure](../azure-api-management-certs.md).

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminar uma conta Run As ou Run As Clássica

Esta secção descreve como eliminar e recriar uma conta Run As ou Run As Clássica. Quando executar esta ação, a conta de Automatização é mantida. Depois de eliminar uma conta Run As ou Run As Clássica, pode voltar a criá-la no portal do Azure.

1. No portal do Azure, abra a conta de Automatização.

2. Na página **Conta de automatização**, selecione **Contas Run as**.

3. Nas propriedades **Contas Run As**, selecione a conta Run As ou a conta Run As Clássica que quer eliminar. Em seguida, no painel **Propriedades** da conta selecionada, clique em **Eliminar**.

   ![Eliminar a conta Run As](media/manage-runas-account/automation-account-delete-runas.png)

1. Enquanto a conta estiver a ser eliminada, pode acompanhar o progresso em **Notificações** a partir do menu.

1. Assim que a conta tiver sido eliminada, pode voltar a criá-la na página de propriedades **Contas Run As**, ao selecionar a opção de criação **Conta Run As do Azure**.

   ![Recriar a conta Run As de Automatização](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Renovação de certificado autoassinado

Em algum momento antes de a conta Executar como expirar, você precisará renovar o certificado. Se considerar que a conta Run As tiver sido comprometida, pode eliminá-la e voltar a criá-la. Esta secção mostra como executar estas operações.

O certificado autoassinado que criou para a conta Run As expira ao fim de um ano após a data de criação. Pode renová-lo em qualquer altura antes de expirar. Ao renová-lo, o certificado válido atual é mantido para garantir que todos os runbooks que estiverem na fila ou que estejam em execução ativamente, e que sejam autenticados com a conta Executar como, não sejam afetados negativamente. O certificado permanece válido até à data de expiração.

> [!NOTE]
> Se tiver configurado a sua conta Run As de Automatização para utilizar um certificado emitido pela sua autoridade de certificação empresarial e utilizar esta opção, esse certificado empresarial é substituído por um certificado autoassinado.

Para renovar o certificado, faça o seguinte:

1. No portal do Azure, abra a conta de Automatização.

1. Selecione **contas Executar como** em **configurações da conta**.

    ![Painel Propriedades da conta de automatização](media/manage-runas-account/automation-account-properties-pane.png)

1. Nas propriedades **Contas Run As**, selecione a conta Run As ou a conta Run As Clássica para a qual pretende renovar o certificado.

1. No painel **Propriedades** da conta selecionada, clique em **Renovar certificado**.

    ![Renovar certificado da conta Run As](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Enquanto o certificado estiver a ser renovado, pode acompanhar o progresso em **Notificações** a partir do menu.

## <a name="auto-cert-renewal"></a>Configurar a renovação automática de certificado com um runbook de automação

Para renovar certificados automaticamente, você pode usar um runbook de automação. O script a seguir no [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) habilita essa funcionalidade em sua conta de automação.

- O script de `GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1` cria uma agenda semanal para renovar os certificados da conta Executar como.
- O script adiciona um runbook **Update-AutomationRunAsCredential** à sua conta de automação.
  - Você também pode exibir o código do runbook no GitHub, no script: [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1).
  - Você também pode usar o código do PowerShell no arquivo para renovar certificados manualmente, conforme necessário.

Para testar o processo de renovação imediatamente, use as seguintes etapas:

1. Edite o runbook **Update-AutomationRunAsCredential** e coloque um caractere de comentário (`#`) na linha 122, na frente do comando `Exit(1)`, conforme mostrado abaixo.

   ```powershell
   #Exit(1)
   ```

2. Publique o runbook.
3. Inicie o runbook.
4. Verifique a renovação bem-sucedida com o seguinte código:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Após o teste, edite o runbook e remova o caractere de comentário que você adicionou na **etapa 1**.
6. **Publique** o runbook.

> [!NOTE]
> Você precisa ser um administrador **global** ou **administrador da empresa** no Azure Active Directory, para executar o script.

## <a name="limiting-run-as-account-permissions"></a>Limitando permissões de conta Executar como

Para controlar o direcionamento de automação contra recursos no Azure, você pode executar o script [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) na galeria do PowerShell para alterar a entidade de serviço da conta Executar como existente para criar e usar uma definição de função personalizada. Essa função terá permissões para todos os recursos, exceto [Key Vault](https://docs.microsoft.com/azure/key-vault/).

> [!IMPORTANT]
> Depois de executar o script de `Update-AutomationRunAsAccountRoleAssignments.ps1`, os runbooks que acessam o keyvault por meio do uso de contas Executar como não funcionarão mais. Você deve examinar os runbooks em sua conta para chamadas para o Azure keyvault.
>
> Para habilitar o acesso ao keyvault por meio de runbooks de automação do Azure, você precisaria [Adicionar a conta Executar como às permissões do keyvault](#add-permissions-to-key-vault).

Se você precisar restringir o que a entidade de serviço RunAs pode fazer mais, você pode adicionar outros tipos de recursos ao `NotActions` da definição de função personalizada. O exemplo a seguir restringe o acesso a `Microsoft.Compute`. Se você adicionar isso às minhas **ações** da definição de função, essa função não poderá acessar nenhum recurso de computação. Para saber mais sobre definições de função, confira [noções básicas sobre definições de função para recursos do Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzureRmRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzureRMRoleDefinition
```

Para determinar se a entidade de serviço usada pela sua conta Executar como está no **colaborador** ou em uma definição de função personalizada, vá para sua conta de automação e, em **configurações de conta**, selecione **contas Executar como** > **conta Executar como do Azure**. Em **função** , você encontrará a definição de função que está sendo usada.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Para determinar a definição de função usada pelas contas Executar como de automação para várias assinaturas ou contas de automação, você pode usar o script [check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) no Galeria do PowerShell.

### <a name="add-permissions-to-key-vault"></a>Adicionar permissões a Key Vault

Se você quiser permitir que a automação do Azure gerencie Key Vault e sua entidade de serviço da conta Executar como esteja usando uma definição de função personalizada, você precisará executar etapas adicionais para permitir esse comportamento:

* Conceder permissões para o Key Vault
* Definir a política de acesso

Você pode usar o script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) no Galeria do PowerShell para dar permissões à conta Executar como ao keyvault ou visitar [conceder aplicativos acesso a um cofre de chaves](../key-vault/key-vault-group-permissions-for-apps.md) para obter mais detalhes sobre as permissões de configurações no keyvault.

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

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre entidades de serviço, consulte [objetos de aplicativo e objetos de entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md).
* Para obter mais informações sobre certificados e serviços do Azure, consulte [visão geral de certificados para serviços de nuvem do Azure](../cloud-services/cloud-services-certs-create.md).
