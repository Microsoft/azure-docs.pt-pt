---
title: Solução de gerenciamento do Office 365 no Azure | Microsoft Docs
description: Este artigo fornece detalhes sobre a configuração e o uso da solução Office 365 no Azure.  Ele inclui uma descrição detalhada dos registros do Office 365 criados no Azure Monitor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 032d52961b4867cad94d06802adb0a1f3eb00f5f
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553948"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Solução de gerenciamento do Office 365 no Azure (versão prévia)

![Logótipo do Office 365](media/solution-office-365/icon.png)


> [!NOTE]
> O método recomendado para instalar e configurar a solução do Office 365 é habilitar o [conector do office 365](../../sentinel/connect-office-365.md) no [Azure Sentinel](../../sentinel/overview.md) em vez de usar as etapas neste artigo. Esta é uma versão atualizada da solução do Office 365 com uma experiência de configuração aprimorada. Para conectar os logs do Azure AD, você pode usar o [Azure Azure ad Connector](../../sentinel/connect-azure-active-directory.md) ou [definir as configurações de diagnóstico do Azure ad](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), que fornece dados de log mais ricos do que os logs de gerenciamento do Office 365. 
>
> Ao integrar o [Azure Sentinel](../../sentinel/quickstart-onboard.md), especifique o espaço de trabalho log Analytics em que você deseja que a solução Office 365 seja instalada. Depois de habilitar o conector, a solução estará disponível no espaço de trabalho e usada exatamente da mesma forma que qualquer outra solução de monitoramento instalada.
>
> Os usuários da nuvem do Azure governamental devem instalar o Office 365 usando as etapas neste artigo, já que o Azure Sentinel ainda não está disponível na nuvem governamental.

A solução de gerenciamento do Office 365 permite monitorar o ambiente do Office 365 no Azure Monitor.

- Monitore as atividades do usuário em suas contas do Office 365 para analisar padrões de uso, bem como identificar tendências comportamentais. Por exemplo, você pode extrair cenários de uso específicos, como arquivos que são compartilhados fora da sua organização ou os sites mais populares do SharePoint.
- Monitore as atividades do administrador para acompanhar as alterações de configuração ou operações de alto privilégio.
- Detecte e investigue o comportamento indesejado do usuário, que pode ser personalizado para suas necessidades organizacionais.
- Demonstre auditoria e conformidade. Por exemplo, você pode monitorar as operações de acesso a arquivos em arquivos confidenciais, o que pode ajudá-lo com o processo de auditoria e conformidade.
- Execute a solução de problemas operacional usando [consultas de log](../log-query/log-query-overview.md) sobre os dados de atividade do Office 365 de sua organização.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

O seguinte é necessário antes que esta solução seja instalada e configurada.

- Assinatura organizacional do Office 365.
- Credenciais para uma conta de usuário que seja um administrador global.
- Para receber dados de auditoria, você deve [Configurar a auditoria](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) em sua assinatura do Office 365.  Observe que a [auditoria de caixa de correio](https://technet.microsoft.com/library/dn879651.aspx) é configurada separadamente.  Você ainda pode instalar a solução e coletar outros dados se a auditoria não estiver configurada.
 

## <a name="management-packs"></a>Pacotes de gestão

Essa solução não instala pacotes de gerenciamento em [grupos de gerenciamento conectados](../platform/om-agents.md).
  

## <a name="install-and-configure"></a>Instalar e configurar

Comece adicionando a [solução do Office 365 à sua assinatura](solutions.md#install-a-monitoring-solution). Depois de adicionado, você deve executar as etapas de configuração nesta seção para dar acesso à sua assinatura do Office 365.

### <a name="required-information"></a>Informações necessárias

Antes de iniciar este procedimento, reúna as informações a seguir.

Do seu espaço de trabalho do Log Analytics:

- Nome do espaço de trabalho: o espaço de trabalho em que os dados do Office 365 serão coletados.
- Nome do grupo de recursos: o grupo de recursos que contém o espaço de trabalho.
- ID da assinatura do Azure: a assinatura que contém o espaço de trabalho.

Da sua assinatura do Office 365:

- Nome de usuário: endereço de email de uma conta administrativa.
- ID do locatário: ID exclusiva para assinatura do Office 365.
- ID do cliente: cadeia de 16 caracteres que representa o cliente do Office 365.
- Segredo do cliente: cadeia de caracteres criptografada necessária para autenticação.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Criar um aplicativo do Office 365 no Azure Active Directory

A primeira etapa é criar um aplicativo no Azure Active Directory que a solução de gerenciamento usará para acessar sua solução do Office 365.

1. Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com/).
1. Selecione **Azure Active Directory** e **registros de aplicativo**.
1. Clique em **novo registro**.

    ![Adicionar registro de aplicativo](media/solution-office-365/add-app-registration.png)
1. Insira um **nome**de aplicativo. Selecione **contas em qualquer diretório organizacional (qualquer diretório do Azure ad-multilocatário)** para os **tipos de conta com suporte**.
    
    ![Criar aplicativo](media/solution-office-365/create-application.png)
1. Clique em **registrar** e validar as informações do aplicativo.

    ![Aplicativo registrado](media/solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Configurar o aplicativo para o Office 365

1. Selecione **autenticação** e verifique se **as contas em qualquer diretório organizacional (qualquer diretório do Azure ad-multilocatário)** estão selecionadas em **tipos de conta com suporte**.

    ![Configurações multilocatário](media/solution-office-365/settings-multitenant.png)

1. Selecione **permissões de API** e, em seguida, **adicione uma permissão**.
1. Clique em **APIs de gerenciamento do Office 365**. 

    ![Selecionar API](media/solution-office-365/select-api.png)

1. Em **que tipo de permissões seu aplicativo requer?** selecione as seguintes opções para permissões de **aplicativo** e **permissões delegadas**:
   - Ler informações de integridade do serviço para sua organização
   - Ler dados de atividade para sua organização
   - Ler relatórios de atividade para sua organização

     ![Selecionar API](media/solution-office-365/select-permissions-01.png)![Selecionar API](media/solution-office-365/select-permissions-02.png)

1. Clique em **adicionar permissões**.
1. Clique em **conceder consentimento do administrador** e, em seguida, clique em **Sim** quando solicitado para verificação.


### <a name="add-a-secret-for-the-application"></a>Adicionar um segredo para o aplicativo

1. Selecione **certificados & segredos** e, em seguida, **novo segredo do cliente**.

    ![Chaves](media/solution-office-365/secret.png)
 
1. Digite uma **Descrição** e uma **duração** para a nova chave.
1. Clique em **Adicionar** e copie o **valor** gerado.

    ![Chaves](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Adicionar consentimento de administrador

Para habilitar a conta administrativa pela primeira vez, você deve fornecer consentimento administrativo para o aplicativo. Você pode fazer isso com um script do PowerShell. 

1. Salve o script a seguir como *office365_consent. ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Execute o script com o comando a seguir. As credenciais serão solicitadas duas vezes. Forneça as credenciais para seu espaço de trabalho do Log Analytics primeiro e, em seguida, as credenciais de administrador global para seu locatário do Office 365.

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    Exemplo:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Será exibida uma janela semelhante à mostrada abaixo. Clique em **aceitar**.
    
    ![Consentimento do administrador](media/solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Assinar Log Analytics espaço de trabalho

A última etapa é inscrever o aplicativo em seu espaço de trabalho do Log Analytics. Você também faz isso com um script do PowerShell.

1. Salve o script a seguir como *office365_subscription. ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. Execute o script com o seguinte comando:

    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```

    Exemplo:

    ```powershell
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>Resolução de problemas

Você poderá ver o erro a seguir se seu aplicativo já tiver se inscrito nesse espaço de trabalho ou se esse locatário for inscrito em outro espaço de trabalho.

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Você poderá ver o erro a seguir se forem fornecidos valores de parâmetro inválidos.

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>Desinstalar

Você pode remover a solução de gerenciamento do Office 365 usando o processo em [remover uma solução de gerenciamento](solutions.md#remove-a-monitoring-solution). No entanto, isso não impedirá que os dados sejam coletados do Office 365 no Azure Monitor. Siga o procedimento abaixo para cancelar a assinatura do Office 365 e parar de coletar dados.

1. Salve o script a seguir como *office365_unsubscribe. ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Execute o script com o seguinte comando:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Exemplo:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Recolha de dados

### <a name="supported-agents"></a>Agentes suportados

A solução do Office 365 não recupera dados de nenhum dos [agentes de log Analytics](../platform/agent-data-sources.md).  Ele recupera dados diretamente do Office 365.

### <a name="collection-frequency"></a>Frequência da recolha

Pode levar algumas horas para que os dados sejam coletados inicialmente. Depois de começar a coletar, o Office 365 envia uma [notificação de webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) com dados detalhados para Azure monitor cada vez que um registro é criado. Esse registro está disponível em Azure Monitor em alguns minutos após ser recebido.

## <a name="using-the-solution"></a>Utilizar a solução

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quando você adicionar a solução do Office 365 ao seu espaço de trabalho do Log Analytics, o bloco do **office 365** será adicionado ao seu painel. Este mosaico apresenta uma contagem e uma representação gráfica do número de computadores no seu ambiente e a respetiva conformidade de atualização.<br><br>
Bloco de resumo do ![Office 365 ](media/solution-office-365/tile.png)  

Clique no bloco do **office 365** para abrir o painel do **Office 365** .

![Painel do Office 365](media/solution-office-365/dashboard.png)  

O dashboard inclui as colunas da tabela seguinte. Cada coluna lista os dez principais alertas por contagem que correspondem aos critérios da coluna para o escopo e o intervalo de tempo especificados. Você pode executar uma pesquisa de logs que fornece a lista inteira clicando em ver todos na parte inferior da coluna ou clicando no cabeçalho da coluna.

| Column | Descrição |
|:--|:--|
| Operations | Fornece informações sobre os usuários ativos de todas as suas assinaturas do Office 365 monitoradas. Você também poderá ver o número de atividades que ocorrem ao longo do tempo.
| Trocar | Mostra a divisão das atividades do Exchange Server, como a permissão Add-Mailbox, ou Set-Mailbox. |
| SharePoint | Mostra as principais atividades que os usuários executam em documentos do SharePoint. Quando você faz Drill-down desse bloco, a página de pesquisa mostra os detalhes dessas atividades, como o documento de destino e o local dessa atividade. Por exemplo, para um evento acessado por arquivo, você poderá ver o documento que está sendo acessado, o nome da conta associada e o endereço IP. |
| Azure Active Directory | Inclui as principais atividades do usuário, como redefinir tentativas de logon e senha de usuário. Ao fazer drill down, você poderá ver os detalhes dessas atividades, como o status do resultado. Isso será útil principalmente se você quiser monitorar atividades suspeitas em seu Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Azure Monitor registros de log

Todos os registros criados no espaço de trabalho Log Analytics no Azure Monitor pela solução do Office 365 têm um **tipo** de **OfficeActivity**.  A propriedade **OfficeWorkload** determina qual serviço do Office 365 o registro se refere a-Exchange, AzureActiveDirectory, SharePoint ou onedrive.  A propriedade **RecordType** especifica o tipo de operação.  As propriedades irão variar para cada tipo de operação e são mostradas nas tabelas a seguir.

### <a name="common-properties"></a>Propriedades comuns

As propriedades a seguir são comuns a todos os registros do Office 365.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo | *OfficeActivity* |
| clientIP | O endereço IP do dispositivo que foi usado quando a atividade foi registrada. O endereço IP é exibido em um formato de endereço IPv4 ou IPv6. |
| OfficeWorkload | Serviço do Office 365 ao qual o registro se refere.<br><br>AzureActiveDirectory<br>Trocar<br>SharePoint|
| Operação | O nome da atividade de usuário ou administrador.  |
| OrganizationId | O GUID do locatário do Office 365 da sua organização. Esse valor será sempre o mesmo para sua organização, independentemente do serviço do Office 365 no qual ele ocorre. |
| RecordType | Tipo de operação executada. |
| ResultStatus | Indica se a ação (especificada na Propriedade Operation) foi bem-sucedida ou não. Os valores possíveis são Succeeded, PartiallySucceeded ou Failed. Para a atividade de administração do Exchange, o valor é true ou false. |
| ID | O UPN (nome UPN) do usuário que realizou a ação que resultou no registro em log; por exemplo, my_name@my_domain_name. Observe que os registros para a atividade executada por contas do sistema (como SHAREPOINT\system ou NTAUTHORITY\SYSTEM) também estão incluídos. | 
| UserKey | Uma ID alternativa para o usuário identificado na propriedade UserId.  Por exemplo, essa propriedade é populada com a PUID (ID exclusiva) do Passport para eventos executados por usuários no SharePoint, OneDrive for Business e Exchange. Essa propriedade também pode especificar o mesmo valor que a Propriedade UserID para eventos que ocorrem em outros serviços e eventos executados por contas do sistema|
| userType | O tipo de usuário que realizou a operação.<br><br>ADM<br>Candidatura<br>DcAdmin<br>Regularmente<br>Reservado<br>ServicePrincipal<br>Sistema |


### <a name="azure-active-directory-base"></a>Base de Azure Active Directory

As propriedades a seguir são comuns a todos os registros de Azure Active Directory.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | O tipo de evento do Azure AD. |
| extendedProperties | As propriedades estendidas do evento do Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Logon da conta do Azure Active Directory

Esses registros são criados quando um usuário Active Directory tenta fazer logon.

| Propriedade | Descrição |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | O aplicativo que dispara o evento de logon da conta, como o Office 15. |
| `Client` | Detalhes sobre o dispositivo do cliente, o sistema operacional do dispositivo e o navegador do dispositivo que foi usado para o do evento de logon da conta. |
| `LoginStatus` | Essa propriedade é de OrgIdLogon. LoginStatus diretamente. O mapeamento de várias falhas de logon interessantes pode ser feito por algoritmos de alerta. |
| `UserDomain` | As informações de identidade do locatário (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Esses registros são criados quando alterações ou adições são feitas em objetos Azure Active Directory.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | O usuário que a ação (identificada pela Propriedade Operation) foi executada em. |
| Ator | O usuário ou a entidade de serviço que realizou a ação. |
| ActorContextId | O GUID da organização ao qual o ator pertence. |
| ActorIpAddress | O endereço IP do ator no formato de endereço IPV4 ou IPV6. |
| Intersystemid | O GUID que acompanha as ações entre os componentes no serviço do Office 365. |
| IntraSystemId |   O GUID gerado por Azure Active Directory para rastrear a ação. |
| SupportTicketId | A ID do tíquete de suporte ao cliente para a ação em situações "agir em nome de". |
| TargetContextId | O GUID da organização ao qual o usuário de destino pertence. |


### <a name="data-center-security"></a>Segurança do Data Center

Esses registros são criados a partir de dados de auditoria de segurança do Data Center.  

| Propriedade | Descrição |
|:--- |:--- |
| EffectiveOrganization | O nome do locatário ao qual a elevação/o cmdlet foi direcionado. |
| ElevationApprovedTime | O carimbo de data/hora para quando a elevação foi aprovada. |
| ElevationApprover | O nome de um Microsoft Manager. |
| ElevationDuration | A duração pela qual a elevação estava ativa. |
| ElevationRequestId |  Um identificador exclusivo para a solicitação de elevação. |
| ElevationRole | A função à qual a elevação foi solicitada. |
| Elevaçãotime | A hora de início da elevação. |
| Start_Time | A hora de início da execução do cmdlet. |


### <a name="exchange-admin"></a>Administrador do Exchange

Esses registros são criados quando são feitas alterações na configuração do Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Trocar |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Especifica se o cmdlet foi executado por um usuário em sua organização, pela equipe do datacenter da Microsoft ou por uma conta de serviço do Datacenter ou por um administrador delegado. O valor false indica que o cmdlet foi executado por alguém em sua organização. O valor true indica que o cmdlet foi executado pela equipe do datacenter, por uma conta de serviço do Datacenter ou por um administrador delegado. |
| ModifiedObjectResolvedName |  Esse é o nome de usuário amigável do objeto que foi modificado pelo cmdlet. Isso será registrado somente se o cmdlet modificar o objeto. |
| OrganizationName | O nome do locatário. |
| OriginatingServer | O nome do servidor do qual o cmdlet foi executado. |
| Parâmetros | O nome e o valor de todos os parâmetros que foram usados com o cmdlet identificado na propriedade Operations. |


### <a name="exchange-mailbox"></a>Caixa de correio do Exchange

Esses registros são criados quando alterações ou adições são feitas nas caixas de correio do Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Trocar |
| RecordType     | ExchangeItem |
| ClientInfoString | Informações sobre o cliente de email que foi usado para executar a operação, como uma versão do navegador, versão do Outlook e informações de dispositivo móvel. |
| Client_IPAddress | O endereço IP do dispositivo que foi usado quando a operação foi registrada. O endereço IP é exibido em um formato de endereço IPv4 ou IPv6. |
| ClientMachineName | O nome do computador que hospeda o cliente do Outlook. |
| ClientProcessName | O cliente de email que foi usado para acessar a caixa de correio. |
| ClientVersion | A versão do cliente de email. |
| InternalLogonType | Reservado para uso interno. |
| Logon_Type | Indica o tipo de usuário que acessou a caixa de correio e executou a operação que foi registrada. |
| LogonUserDisplayName |    O nome amigável do usuário que realizou a operação. |
| LogonUserSid | O SID do usuário que realizou a operação. |
| MailboxGuid | O GUID do Exchange da caixa de correio que foi acessada. |
| MailboxOwnerMasterAccountSid | SID da conta mestra da conta do proprietário da caixa de correio. |
| MailboxOwnerSid | O SID do proprietário da caixa de correio. |
| MailboxOwnerUPN | O endereço de email da pessoa que possui a caixa de correio que foi acessada. |


### <a name="exchange-mailbox-audit"></a>Auditoria de caixa de correio do Exchange

Esses registros são criados quando uma entrada de auditoria de caixa de correio é criada.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Trocar |
| RecordType     | ExchangeItem |
| Item | Representa o item no qual a operação foi executada | 
| SendAsUserMailboxGuid | O GUID do Exchange da caixa de correio que foi acessada para enviar email como. |
| SendAsUserSmtp | Endereço SMTP do usuário que está sendo representado. |
| SendonBehalfOfUserMailboxGuid | O GUID do Exchange da caixa de correio que foi acessada para enviar email em nome de. |
| SendOnBehalfOfUserSmtp | Endereço SMTP do usuário em cujo nome o email é enviado. |


### <a name="exchange-mailbox-audit-group"></a>Grupo de auditoria de caixa de correio do Exchange

Esses registros são criados quando alterações ou adições são feitas em grupos do Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Trocar |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informações sobre cada item no grupo. |
| CrossMailboxOperations | Indica se a operação envolvia mais de uma caixa de correio. |
| DestMailboxId | Defina somente se o parâmetro CrossMailboxOperations for true. Especifica o GUID da caixa de correio de destino. |
| DestMailboxOwnerMasterAccountSid | Defina somente se o parâmetro CrossMailboxOperations for true. Especifica o SID para o SID da conta mestra do proprietário da caixa de correio de destino. |
| DestMailboxOwnerSid | Defina somente se o parâmetro CrossMailboxOperations for true. Especifica o SID da caixa de correio de destino. |
| DestMailboxOwnerUPN | Defina somente se o parâmetro CrossMailboxOperations for true. Especifica o UPN do proprietário da caixa de correio de destino. |
| DestFolder | A pasta de destino, para operações como mover. |
| Pasta | A pasta em que um grupo de itens está localizado. |
| Pastas |     Informações sobre as pastas de origem envolvidas em uma operação; por exemplo, se as pastas forem selecionadas e, em seguida, excluídas. |


### <a name="sharepoint-base"></a>Base do SharePoint

Essas propriedades são comuns a todos os registros do SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| eventSource | Identifica que ocorreu um evento no SharePoint. Os valores possíveis são SharePoint ou ObjectModel. |
| ItemType | O tipo de objeto que foi acessado ou modificado. Consulte a tabela ItemType para obter detalhes sobre os tipos de objetos. |
| MachineDomainInfo | Informações sobre operações de sincronização de dispositivo. Essas informações serão informadas apenas se estiverem presentes na solicitação. |
| MachineId |   Informações sobre operações de sincronização de dispositivo. Essas informações serão informadas apenas se estiverem presentes na solicitação. |
| Locais | O GUID do site em que o arquivo ou pasta acessado pelo usuário está localizado. |
| Source_Name | A entidade que disparou a operação auditada. Os valores possíveis são SharePoint ou ObjectModel. |
| UserAgent | Informações sobre o cliente ou o navegador do usuário. Essas informações são fornecidas pelo cliente ou pelo navegador. |


### <a name="sharepoint-schema"></a>Esquema do SharePoint

Esses registros são criados quando são feitas alterações de configuração no SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Cadeia de caracteres opcional para eventos personalizados. |
| Event_Data |  Conteúdo opcional para eventos personalizados. |
| ModifiedProperties | A propriedade é incluída para eventos de administrador, como adicionar um usuário como um membro de um site ou um grupo de administradores de conjunto de sites. A propriedade inclui o nome da propriedade que foi modificada (por exemplo, o grupo de administradores do site), o novo valor da propriedade modificada (como o usuário que foi adicionado como administrador do site) e o valor anterior do objeto modificado. |


### <a name="sharepoint-file-operations"></a>Operações de arquivo do SharePoint

Esses registros são criados em resposta às operações de arquivo no SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | A extensão de arquivo de um arquivo que é copiado ou movido. Essa propriedade é exibida somente para eventos filecopied e filemoved. |
| DestinationFileName | O nome do arquivo que é copiado ou movido. Essa propriedade é exibida somente para eventos filecopied e filemoved. |
| DestinationRelativeUrl | A URL da pasta de destino onde um arquivo é copiado ou movido. A combinação dos valores dos parâmetros SiteURL, DestinationRelativeURL e DestinationFileName é a mesma do valor da propriedade ObjectID, que é o nome do caminho completo do arquivo que foi copiado. Essa propriedade é exibida somente para eventos filecopied e filemoved. |
| Compartilhatype | O tipo de permissões de compartilhamento que foram atribuídas ao usuário com o qual o recurso foi compartilhado. Esse usuário é identificado pelo parâmetro UserSharedWith. |
| Site_Url | A URL do site em que o arquivo ou pasta acessado pelo usuário está localizado. |
| SourceFileExtension | A extensão de arquivo do arquivo que foi acessado pelo usuário. Essa propriedade estará em branco se o objeto que foi acessado for uma pasta. |
| SourceFileName |  O nome do arquivo ou pasta acessado pelo usuário. |
| SourceRelativeUrl | A URL da pasta que contém o arquivo acessado pelo usuário. A combinação dos valores para os parâmetros SiteURL, SourceRelativeURL e SourceFileName é igual ao valor da propriedade ObjectID, que é o nome do caminho completo para o arquivo acessado pelo usuário. |
| UserSharedWith |  O usuário com o qual um recurso foi compartilhado. |




## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

A tabela seguinte disponibiliza pesquisas de registos de exemplo para registos de atualizações que esta solução recolhe.

| Consulta | Descrição |
| --- | --- |
|Contagem de todas as operações na sua assinatura do Office 365 |OfficeActivity &#124; resume contagem () por operação |
|Uso de sites do SharePoint|OfficeActivity &#124; em que OfficeWorkload = ~ "SharePoint &#124; " resumir contagem () por SiteUrl \| classificar por contagem ASC|
|Operações de acesso de arquivo por tipo de usuário|Pesquisar em (OfficeActivity) OfficeWorkload = ~ "azureactivedirectory" e "myTest"|
|Pesquisar com uma palavra-chave específica|Tipo = OfficeActivity OfficeWorkload = azureactivedirectory "myTest"|
|Monitorar ações externas no Exchange|OfficeActivity &#124; em que OfficeWorkload = ~ "Exchange" e ExternalAccess = = true|



## <a name="next-steps"></a>Passos seguintes

* Use [consultas de log em Azure monitor](../log-query/log-query-overview.md) para exibir dados de atualização detalhados.
* [Crie seus próprios painéis](../learn/tutorial-logs-dashboards.md) para exibir suas consultas de pesquisa favoritas do Office 365.
* [Crie alertas](../platform/alerts-overview.md) para que sejam notificados proativamente sobre atividades importantes do Office 365.  
