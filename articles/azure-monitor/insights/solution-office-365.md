---
title: Solução de gestão do Office 365 no Azure | Documentos da Microsoft
description: Este artigo fornece detalhes sobre a configuração e utilização da solução do Office 365 no Azure.  Ele inclui uma descrição detalhada dos registos do Office 365 criados no Azure Monitor.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/24/2019
ms.author: bwren
ms.openlocfilehash: 6a13988af7a46ff6fafe352e850ee238cda79c08
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57996711"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Solução de gestão do Office 365 no Azure (pré-visualização)

![Logótipo do Office 365](media/solution-office-365/icon.png)

A solução de gestão do Office 365 permite-lhe monitorizar o seu ambiente do Office 365 no Azure Monitor.

- Monitorizar atividades de utilizadores em suas contas do Office 365 para analisar padrões de utilização, bem como para identificar tendências comportamentais. Por exemplo, pode extrair os cenários de utilização específicos, como ficheiros que são partilhados fora da sua organização ou os mais populares sites do SharePoint.
- Monitorizar atividades de administrador para controlar as alterações de configuração ou operações de alto privilégio.
- Detete e investigue o comportamento dos utilizadores indesejados, que pode ser personalizado para as suas necessidades organizacionais.
- Demonstre a conformidade e auditoria. Por exemplo, pode monitorizar operações de acesso de arquivos em ficheiros confidenciais, o que podem ajudá-lo com o processo de auditoria e conformidade.
- Resolver os problemas operacionais usando [registar as consultas](../log-query/log-query-overview.md) sobre dados de atividade do Office 365 da sua organização.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
É necessário o seguinte antes desta solução a ser instalado e configurado.

- Subscrição do Office 365 organizacional.
- Credenciais para uma conta de utilizador que seja um Administrador Global.
- Para receber dados de auditoria, deve [configurar a auditoria](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) na sua subscrição do Office 365.  Tenha em atenção que [auditoria da caixa de correio](https://technet.microsoft.com/library/dn879651.aspx) está configurado separadamente.  Pode ainda instalar a solução e recolher outros dados, se a auditoria não está configurada.
 

## <a name="management-packs"></a>Pacotes de gestão
Esta solução não instala os pacotes de gestão no [ligadas a grupos de gestão](../platform/om-agents.md).
  
## <a name="install-and-configure"></a>Instalar e configurar
Comece por adicionar o [solução do Office 365 à sua subscrição](solutions.md#install-a-monitoring-solution). Assim que for adicionado, tem de efetuar os passos de configuração nesta secção para fornecer acesso à sua subscrição do Office 365.

### <a name="required-information"></a>Informações necessárias
Antes de iniciar este procedimento, recolha as seguintes informações.

Na área de trabalho do Log Analytics:

- Nome da área de trabalho: A área de trabalho onde serão recolhidos os dados do Office 365.
- Nome do grupo de recursos: O grupo de recursos que contém a área de trabalho.
- ID de subscrição do Azure: A subscrição que contém a área de trabalho.

Da sua subscrição do Office 365:

- Nome de Utilizador: Endereço de e-mail de uma conta administrativa.
- ID do Inquilino: ID exclusivo para a subscrição do Office 365.
- ID de cliente: cadeia de caracteres de 16 que representa o cliente do Office 365.
- Segredo do cliente: Cadeia encriptada necessária para autenticação.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Criar uma aplicação do Office 365 no Azure Active Directory
A primeira etapa é criar uma aplicação no Azure Active Directory que a solução de gestão irá utilizar para aceder à sua solução do Office 365.

1. Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com/).
1. Selecione **do Azure Active Directory** e, em seguida **registos das aplicações**.
1. Clique em **Novo registo de aplicação**.

    ![Adicionar registo de aplicações](media/solution-office-365/add-app-registration.png)
1. Introduza um aplicativo **Name** e **URL de início de sessão**.  O nome deve ser clara.  Uso `http://localhost` para o URL e continue _aplicação Web / API_ para o **tipo de aplicação**
    
    ![Criar aplicação](media/solution-office-365/create-application.png)
1. Clique em **criar** e validar as informações da aplicação.

    ![Aplicação registada](media/solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Configurar a aplicação para o Office 365

1. Clique em **configurações** para abrir o **definições** menu.
1. Selecione **propriedades**. Alteração **vários inquilinos** ao _Sim_.

    ![Multi-inquilino de definições](media/solution-office-365/settings-multitenant.png)

1. Selecione **permissões obrigatórias** no **definições** menu e clique em **Add**.
1. Clique em **selecionar uma API** e, em seguida **APIs de gestão do Office 365**. Clique em **APIs de gestão do Office 365**. Clique em **Selecionar**.

    ![Selecionar API](media/solution-office-365/select-api.png)

1. Sob **selecionar permissões** Selecione as seguintes opções para ambos **permissões de aplicação** e **permissões delegadas**:
   - Ler informações do estado de funcionamento do serviço da sua organização
   - Ler dados de atividade para a sua organização
   - Ler relatórios de atividade da organização

     ![Selecionar API](media/solution-office-365/select-permissions.png)

1. Clique em **selecionar** e, em seguida **feito**.
1. Clique em **conceder permissões** e, em seguida, clique em **Sim** quando lhe for pedido para verificação.

    ![Conceder permissões](media/solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Adicionar uma chave para a aplicação

1. Selecione **chaves** no **definições** menu.
1. Escreva um **Descrição** e **duração** para a nova chave.
1. Clique em **salvar** e, em seguida, copie a **valor** que é gerado.

    ![Chaves](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Adicionar o consentimento de administrador
Para ativar a conta administrativa pela primeira vez, tem de fornecer consentimento administrativo para o aplicativo. Pode fazê-lo com um script do PowerShell. 

1. Guarde o seguinte script como *office365_consent.ps1*.

    ```
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

2. Execute o script com o seguinte comando. Será solicitado duas vezes para credenciais. Forneça as credenciais para a área de trabalho do Log Analytics primeiro e, em seguida, as credenciais de administrador global do Office 365 para o inquilino.
    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```
    Exemplo:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Será apresentada uma janela semelhante à abaixo. Clique em **aceitar**.
    
    ![Consentimento de admin](media/solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Subscrever a área de trabalho do Log Analytics
A última etapa é assinar a aplicação à sua área de trabalho do Log Analytics. Também fazer isso com um script do PowerShell.

1. Guarde o seguinte script como *office365_subscription.ps1*.

    ```
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

    ```
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>Resolução de problemas

Poderá ver o seguinte erro se o seu aplicativo já está subscrita para esta área de trabalho ou se este inquilino está subscrita em outra área de trabalho.

```
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Poderá ver o seguinte erro se forem fornecidos valores de parâmetro inválido.

```
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>Desinstalar
Pode remover a solução de gestão do Office 365 usando o processo em [remover uma solução de gestão](solutions.md#remove-a-monitoring-solution). Isso não interromperá os dados que está a ser recolhidos a partir do Office 365 no Azure Monitor entanto. Siga o procedimento abaixo para anular a subscrição do Office 365 e parar a recolha de dados.

1. Guarde o seguinte script como *office365_unsubscribe.ps1*.

    ```
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

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Recolha de dados
### <a name="supported-agents"></a>Agentes suportados
A solução do Office 365 não obter dados a partir de qualquer um da [agentes do Log Analytics](../platform/agent-data-sources.md).  Obtém dados diretamente a partir do Office 365.

### <a name="collection-frequency"></a>Frequência da recolha
Poderá demorar algumas horas para dados que inicialmente serão coletados. Assim que for iniciada a recolher, do Office 365, envia uma [webhook notificação](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) com dados detalhados para o Azure Monitor sempre que é criado um registo. Este registo está disponível no Azure Monitor dentro de alguns minutos após a ser recebidos.

## <a name="using-the-solution"></a>Utilizar a solução

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quando adiciona a solução do Office 365 à área de trabalho do Log Analytics, o **Office 365** mosaico será adicionado ao seu dashboard. Este mosaico apresenta uma contagem e uma representação gráfica do número de computadores no seu ambiente e a respetiva conformidade de atualização.<br><br>
![Mosaico de resumo do Office 365](media/solution-office-365/tile.png)  

Clique nas **Office 365** mosaico para abrir o **do Office 365** dashboard.

![Dashboard do Office 365](media/solution-office-365/dashboard.png)  

O dashboard inclui as colunas da tabela seguinte. Cada coluna apresenta os alertas de dez principais por contagem que satisfaçam os critérios dessa coluna para o âmbito especificado e o intervalo de tempo. Pode executar uma pesquisa de registos que fornece toda a lista ao clicar em ver tudo na parte inferior da coluna ou ao clicar no cabeçalho da coluna.

| Coluna | Descrição |
|:--|:--|
| Operações | Fornece informações sobre os utilizadores do Active Directory das suas subscrições do Office 365 tudo monitorizados. Também será capaz de ver o número de atividades que ocorrem ao longo do tempo.
| Troca | Mostra a divisão de atividades do Exchange Server, como caixa de correio de adicionar permissão ou Set-Mailbox. |
| SharePoint | Mostra as atividades principais que os utilizadores executar nos documentos do SharePoint. Quando fazer uma busca detalhada neste mosaico, a página de pesquisa mostra os detalhes dessas atividades, como o documento de destino e a localização desta atividade. Por exemplo, para um evento de aceder ao ficheiro, será capaz de ver o documento que está sendo acessado, seu nome de conta associada e o endereço IP. |
| Azure Active Directory | Inclui atividades de utilizador principais, como repor a palavra-passe de utilizador e de tentativas de início de sessão. Quando fazer uma busca detalhada, será capaz de ver os detalhes dessas atividades, como o estado do resultado. Isto é principalmente útil se pretender monitorizar atividades suspeitas no seu Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Registros de logs de Monitor do Azure

Todos os registos criados na área de trabalho do Log Analytics no Azure Monitor pela solução do Office 365 tem um **tipo** dos **OfficeActivity**.  O **OfficeWorkload** propriedade determina qual serviço do Office 365, o registo refere-se para - Exchange, o AzureActiveDirectory, o SharePoint ou o OneDrive.  O **RecordType** propriedade especifica o tipo de operação.  As propriedades irão variar para cada tipo de operação e são apresentadas nas tabelas abaixo.

### <a name="common-properties"></a>Propriedades comuns
As seguintes propriedades são comuns a todos os registos do Office 365.

| Propriedade | Descrição |
|:--- |:--- |
| Type | *OfficeActivity* |
| ClientIP | O endereço IP do dispositivo que foi utilizado quando a atividade foi registada. O endereço IP é apresentado no formato de endereço de um IPv4 ou IPv6. |
| OfficeWorkload | Serviço do Office 365 que referencia o registo.<br><br>AzureActiveDirectory<br>Troca<br>SharePoint|
| Operação | O nome da atividade de utilizador ou administrador.  |
| OrganizationId | O GUID para o inquilino do Office 365 da sua organização. Este valor será sempre o mesmo para a sua organização, independentemente do serviço do Office 365 em que ocorre. |
| RecordType | Tipo de operação realizada. |
| ResultStatus | Indica se a ação (especificada na propriedade operação) foi concluída com êxito ou não. Valores possíveis são com êxito, parcial ou com falhas. Para a atividade de administrador do Exchange, o valor é True ou False. |
| UserId | O UPN (nome Principal de utilizador) do utilizador que efetuou a ação que resultou no registo que está sendo registrado; Por exemplo, my_name@my_domain_name. Tenha em atenção que os registos de atividades realizadas por contas do sistema (como SHAREPOINT\system ou NTAUTHORITY\SYSTEM) também estão incluídos. | 
| UserKey | Um ID alternativo para o utilizador identificado na propriedade de ID de utilizador.  Por exemplo, esta propriedade é preenchida com o ID exclusivo do passport (PUID) para eventos realizadas por utilizadores no SharePoint, OneDrive para empresas e o Exchange. Esta propriedade também pode especificar o mesmo valor da propriedade de ID de utilizador para eventos que ocorrem noutros serviços e eventos realizados por contas do sistema|
| UserType | O tipo de utilizador que executou a operação.<br><br>Administração<br>Aplicação<br>DcAdmin<br>Normal<br>Reservado<br>ServicePrincipal<br>Sistema |


### <a name="azure-active-directory-base"></a>Base do Azure Active Directory
As seguintes propriedades são comuns a todos os registos do Azure Active Directory.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | O tipo de eventos do Azure AD. |
| ExtendedProperties | Propriedades expandidas do evento do Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Início de sessão de conta do Active Directory do Azure
Estes registos são criados quando um utilizador do Active Directory tenta fazer logon.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Aplicação | O aplicativo aciona o evento de início de sessão de conta, como o Office 15. |
| Cliente | Detalhes sobre o cliente do dispositivo, o SO do dispositivo e o browser do dispositivo que foi utilizado para do evento de início de sessão de conta. |
| LoginStatus | Esta propriedade é do OrgIdLogon.LoginStatus diretamente. O mapeamento de várias falhas de início de sessão interessante poderia ser feito por algoritmos de alerta. |
| UserDomain | As informações de identidade do inquilino (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Estes registos são criados quando forem feitas alterações ou adições de objetos do Active Directory do Azure.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | O utilizador que a ação (identificada por propriedade operação) foi executada em. |
| Ator | O utilizador ou principal de serviço que efetuou a ação. |
| ActorContextId | O GUID da organização que o ator pertence. |
| ActorIpAddress | Endereço IP do ator no formato de endereço IPV4 ou IPV6. |
| InterSystemsId | O GUID que controlam as ações em componentes no serviço do Office 365. |
| IntraSystemId |   O GUID que é gerado pelo Azure Active Directory para controlar a ação. |
| SupportTicketId | O cliente suporta o ID de permissão para a ação "act-em-nome-de" situações. |
| TargetContextId | O GUID da organização que o utilizador de destino pertence. |


### <a name="data-center-security"></a>Segurança do Centro de dados
Estes registos são criados a partir dos dados de auditoria de segurança do Centro de dados.  

| Propriedade | Descrição |
|:--- |:--- |
| EffectiveOrganization | O nome do inquilino que foi direcionado para o cmdlet/elevação. |
| ElevationApprovedTime | O carimbo de hora para quando a elevação foi aprovada. |
| ElevationApprover | O nome de um Gerenciador de Microsoft. |
| ElevationDuration | A duração para os quais a elevação estava ativa. |
| ElevationRequestId |  Um identificador exclusivo para o pedido de elevação. |
| ElevationRole | A função a elevação foi pedida para. |
| ElevationTime | A hora de início da elevação. |
| Start_Time | A hora de início da execução do cmdlet. |


### <a name="exchange-admin"></a>Administrador do Exchange
Estes registos são criados quando forem feitas alterações à configuração do Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Especifica se o cmdlet foi executado por um utilizador na sua organização, pela equipa de datacenter da Microsoft ou uma conta de serviço do Centro de dados ou por um administrador delegado. O valor False indica que o cmdlet foi executado por alguém na sua organização. O valor True indica que o cmdlet foi executado pela equipe do Centro de dados, uma conta de serviço do Centro de dados ou um administrador delegado. |
| ModifiedObjectResolvedName |  Este é o nome amigável de utilizador do objeto que foi modificado pelo cmdlet. Isto é registado apenas se o cmdlet modifica o objeto. |
| OrganizationName | O nome do inquilino. |
| OriginatingServer | O nome do servidor do qual foi executado o cmdlet. |
| Parâmetros | O nome e valor para todos os parâmetros que foram utilizados com o cmdlet que está identificado na propriedade de operações. |


### <a name="exchange-mailbox"></a>Caixa de correio do Exchange
Estes registos são criados quando forem feitas alterações ou adições de caixas de correio do Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| RecordType     | ExchangeItem |
| ClientInfoString | Informações sobre o cliente de e-mail que foi utilizado para efetuar a operação, como uma versão do navegador, versão do Outlook e informações de dispositivos móveis. |
| Client_IPAddress | O endereço IP do dispositivo que foi utilizado quando a operação foi iniciada. O endereço IP é apresentado no formato de endereço de um IPv4 ou IPv6. |
| ClientMachineName | O nome do computador que aloja o cliente do Outlook. |
| ClientProcessName | O cliente de e-mail que foi utilizado para aceder a caixa de correio. |
| ClientVersion | A versão do cliente de e-mail. |
| InternalLogonType | Reservado para utilização interna. |
| Logon_Type | Indica o tipo de utilizador que aceder a caixa de correio e executou a operação que foi registada. |
| LogonUserDisplayName |    O nome amigável de utilizador do utilizador que executou a operação. |
| LogonUserSid | O SID do utilizador que executou a operação. |
| MailboxGuid | O GUID do Exchange da caixa de correio que foi acedido. |
| MailboxOwnerMasterAccountSid | Conta principal SID da conta de proprietário a caixa de correio. |
| MailboxOwnerSid | O SID do proprietário de caixa de correio. |
| MailboxOwnerUPN | O endereço de e-mail da pessoa que detém a caixa de correio que foi acessada. |


### <a name="exchange-mailbox-audit"></a>Auditoria de caixa de correio do Exchange
Estes registos são criados quando é criada uma entrada de auditoria da caixa de correio.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| RecordType     | ExchangeItem |
| Item | Representa o item no qual foi executada a operação | 
| SendAsUserMailboxGuid | O GUID do Exchange da caixa de correio que foi acessado para enviar o e-mail. |
| SendAsUserSmtp | Endereço SMTP do utilizador que está a ser representado. |
| SendonBehalfOfUserMailboxGuid | O GUID do Exchange da caixa de correio que foi acessado para enviar correio em nome de. |
| SendOnBehalfOfUserSmtp | Endereço SMTP do utilizador em cujo nome é enviado o e-mail. |


### <a name="exchange-mailbox-audit-group"></a>Grupo de auditoria de caixa de correio do Exchange
Estes registos são criados quando forem feitas alterações ou adições de grupos do Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informações sobre cada item no grupo. |
| CrossMailboxOperations | Indica se a operação envolvida mais do que uma caixa de correio. |
| DestMailboxId | Defina apenas se o parâmetro CrossMailboxOperations for True. Especifica a caixa de correio de destino GUID. |
| DestMailboxOwnerMasterAccountSid | Defina apenas se o parâmetro CrossMailboxOperations for True. Especifica o SID para a conta mestre SID do proprietário da caixa de correio de destino. |
| DestMailboxOwnerSid | Defina apenas se o parâmetro CrossMailboxOperations for True. Especifica o SID da caixa de correio de destino. |
| DestMailboxOwnerUPN | Defina apenas se o parâmetro CrossMailboxOperations for True. Especifica o UPN do proprietário da caixa de correio de destino. |
| DestFolder | A pasta de destino, para operações como a mudança. |
| Pasta | A pasta onde está localizado a um grupo de itens. |
| Pastas |     Informações sobre as pastas de origem envolvidas numa operação; Por exemplo, se as pastas são selecionadas e, em seguida, eliminadas. |


### <a name="sharepoint-base"></a>Base do SharePoint
Estas propriedades são comuns a todos os registos do SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Identifica que um evento ocorreu no SharePoint. Valores possíveis são SharePoint ou ObjectModel. |
| ItemType | O tipo de objeto que tenha sido acedido ou modificado. Consulte a tabela de ItemType para obter detalhes sobre os tipos de objetos. |
| MachineDomainInfo | Informações sobre as operações de sincronização do dispositivo. Esta informação é comunicada apenas se estiver presente no pedido. |
| MachineId |   Informações sobre as operações de sincronização do dispositivo. Esta informação é comunicada apenas se estiver presente no pedido. |
| Site_ | O GUID do site onde está localizada o ficheiro ou pasta acedidos pelo utilizador. |
| Source_Name | A entidade que disparou a operação auditada. Valores possíveis são SharePoint ou ObjectModel. |
| UserAgent | Informações sobre o cliente ou browser do utilizador. Estas informações são fornecidas pelo cliente ou browser. |


### <a name="sharepoint-schema"></a>SharePoint Schema
Estes registos são criados quando forem feitas alterações de configuração para o SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Cadeia de caracteres opcional de eventos personalizados. |
| Event_Data |  Payload opcional para eventos personalizados. |
| ModifiedProperties | A propriedade é incluída para eventos de administrador, por exemplo, adicionar um utilizador como membro de um site ou um grupo de administrador da coleção de sites. A propriedade inclui o nome da propriedade que foi modificado (por exemplo, o grupo de administração de sites), o novo valor da propriedade modificada (tal o utilizador que foi adicionado como um administrador de site) e o valor anterior do objeto modificado. |


### <a name="sharepoint-file-operations"></a>Operações de ficheiros do SharePoint
Estes registos são criados em resposta às operações de ficheiro no SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | A extensão de ficheiro de um ficheiro que é copiado ou movido. Esta propriedade é apresentada apenas para eventos FileCopied e FileMoved. |
| DestinationFileName | O nome do ficheiro que é copiado ou movido. Esta propriedade é apresentada apenas para eventos FileCopied e FileMoved. |
| DestinationRelativeUrl | O URL da pasta de destino em que um ficheiro é copiado ou movido. A combinação dos valores para parâmetros SiteURL DestinationRelativeURL e DestinationFileName é igual ao valor da propriedade ObjectID, que é o nome de caminho completo para o ficheiro que foi copiado. Esta propriedade é apresentada apenas para eventos FileCopied e FileMoved. |
| SharingType | O tipo de permissões que foram atribuídas ao utilizador que o recurso foi partilhado com de partilha. Este utilizador é identificado pelo parâmetro UserSharedWith. |
| Site_Url | O URL do site onde está localizada o ficheiro ou pasta acedidos pelo utilizador. |
| SourceFileExtension | A extensão de ficheiro do ficheiro que o utilizador acedeu. Esta propriedade está em branco se o objeto que foi acessado é uma pasta. |
| SourceFileName |  O nome do ficheiro ou pasta acedidos pelo utilizador. |
| SourceRelativeUrl | O URL da pasta que contém o ficheiro acedido pelo utilizador. A combinação dos valores para os parâmetros SiteURL SourceRelativeURL e SourceFileName é igual ao valor da propriedade ObjectID, que é o nome de caminho completo para o ficheiro acedido pelo utilizador. |
| UserSharedWith |  O utilizador que um recurso que foi partilhado com. |




## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo
A tabela seguinte disponibiliza pesquisas de registos de exemplo para registos de atualizações que esta solução recolhe.

| Consulta | Descrição |
| --- | --- |
|Contagem de todas as operações na sua subscrição do Office 365 |OfficeActivity &#124; resumir contagem () por operação |
|Utilização de sites do SharePoint|OfficeActivity &#124; onde OfficeWorkload = ~ "sharepoint" &#124; resumir count () by SiteUrl \| ordenar por contagem asc|
|Operações de acesso de arquivo por tipo de utilizador|pesquisa em OfficeWorkload (OfficeActivity) = ~ "azureactivedirectory" e "MyTest"|
|Pesquise com uma palavra-chave específica|Tipo = OfficeActivity OfficeWorkload = azureactivedirectory "MyTest"|
|Monitorizar ações externas no Exchange|OfficeActivity &#124; onde OfficeWorkload = ~ "exchange" e ExternalAccess = = true|



## <a name="next-steps"></a>Passos Seguintes
* Uso [registar as consultas no Azure Monitor](../log-query/log-query-overview.md) para ver os dados de atualizações detalhados.
* [Criar seus próprios dashboards](../learn/tutorial-logs-dashboards.md) para exibir suas consultas de pesquisa favoritas do Office 365.
* [Criar alertas](../platform/alerts-overview.md) para ser notificado proativamente das atividades do Office 365 importantes.  
