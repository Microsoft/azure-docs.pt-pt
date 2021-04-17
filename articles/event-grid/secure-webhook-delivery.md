---
title: Entrega segura da WebHook com Azure AD na grelha de eventos Azure
description: Descreve como entregar eventos a pontos finais HTTPS protegidos pelo Azure Ative Directory usando a Azure Event Grid
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 6a0f9059e17d96d497b425abc9749e69c5ab4d41
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575552"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publicar eventos nos pontos finais protegidos do Azure Active Directory
Este artigo descreve como utilizar o Azure Ative Directory (Azure AD) para garantir a ligação entre a **subscrição** do seu evento e o ponto final do seu **webhook**. Para obter uma visão geral das aplicações azure e principais serviços, consulte a [plataforma de identidade da Microsoft (v2.0).](../active-directory/develop/v2-overview.md)

Este artigo utiliza o portal Azure para demonstração, no entanto a funcionalidade também pode ser ativada usando CLI, PowerShell ou os SDKs.

> [!IMPORTANT]
> Foi introduzido um controlo adicional de acesso como parte da criação ou atualização da subscrição de eventos em 30 de março de 2021 para resolver uma vulnerabilidade de segurança. O diretor de serviço do cliente assinante precisa de ser proprietário ou ter uma função atribuída no diretor do serviço de aplicação de destino. Por favor, reconfigure a sua Aplicação AAD seguindo as novas instruções abaixo.


## <a name="create-an-azure-ad-application"></a>Criar uma aplicação AD AZure
Registe o seu Webhook com Azure AD criando uma aplicação AD Azure para o seu ponto final protegido. Ver [Cenário: API web protegida](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview). Configuure a sua API protegida para ser chamada por uma app daemon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Ativar a Grelha de Eventos para utilizar a sua aplicação AD Azure
Esta secção mostra-lhe como ativar a Grelha de Eventos para utilizar a sua aplicação AD AZure. 

> [!NOTE]
> Você deve ser um membro da função de [Administrador de Aplicação AD Azure](../active-directory/roles/permissions-reference.md#all-roles) para executar este script.

### <a name="connect-to-your-azure-tenant"></a>Ligue-se ao seu inquilino Azure
Primeiro, ligue-se ao seu inquilino Azure usando o `Connect-AzureAD` comando. 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Criar o principal do serviço Microsoft.EventGrid
Execute o seguinte script para criar o principal de serviço para **a Microsoft.EventGrid** se ele já não existir. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else {
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Crie um papel para a sua aplicação   
Execute o seguinte script para criar um papel para a sua aplicação AZure AD. Neste exemplo, o nome da função é: **AzureEventGridSecureWebhookSubscriber**. Modifique o script `$myTenantId` PowerShell's para usar o seu ID do inquilino Azure AD, e com o `$myAzureADApplicationObjectId` ID do objeto da sua aplicação AD Azure

```PowerShell
# This is your Webhook's Azure AD Application's ObjectId. 
$myWebhookAadApplicationObjectId = "<Your webhook's aad application object id>"

# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhookSubscriber"
       
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.AllowedMemberTypes.Add("User");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
       
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myWebhookAadApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
       
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
} else {      
    # Add our new role to the Azure AD Application
    Write-Host "Creating the Azure Event Grid role in Azure Ad Application: " $myWebhookAadApplicationObjectId
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles

```

### <a name="create-role-assignment-for-the-client-creating-event-subscription"></a>Criar atribuição de funções para o cliente criar subscrição de eventos
A atribuição de funções deve ser criada na App AD Webhook Azure para a aplicação AAD ou utilizador AAD que cria a subscrição do evento. Utilize um dos scripts abaixo, dependendo se uma aplicação AAD ou utilizador AAD está a criar a subscrição do evento.

> [!IMPORTANT]
> Foi introduzido um controlo adicional de acesso como parte da criação ou atualização da subscrição de eventos em 30 de março de 2021 para resolver uma vulnerabilidade de segurança. O diretor de serviço do cliente assinante precisa de ser proprietário ou ter uma função atribuída no diretor do serviço de aplicação de destino. Por favor, reconfigure a sua Aplicação AAD seguindo as novas instruções abaixo.

#### <a name="create-role-assignment-for-an-event-subscription-aad-app"></a>Criar atribuição de funções para uma aplicação AAD de subscrição de eventos 

```powershell
# This is the app id of the application which will create event subscription. Set to $null if you are not assigning the role to app.
$eventSubscriptionWriterAppId = "<the app id of the application which will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")

$eventSubscriptionWriterSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventSubscriptionWriterAppId + "'")
if ($eventSubscriptionWriterSP -eq $null)
{
        $eventSubscriptionWriterSP = New-AzureADServicePrincipal -AppId $eventSubscriptionWriterAppId
}

Write-Host "Creating the Azure Ad App Role assignment for application: " $eventSubscriptionWriterAppId
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="create-role-assignment-for-an-event-subscription-aad-user"></a>Criar atribuição de funções para um utilizador AAD de subscrição de eventos 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADUserAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="create-role-assignment-for-event-grid-service-principal"></a>Criar atribuição de funções para o diretor do Serviço de Grelha de Eventos
Executar o comando New-AzureADServiceAppRoleAssignment para atribuir o principal do serviço de Grade de Eventos ao papel que criou no passo anterior.

```powershell
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Execute os seguintes comandos para obter informações de saída que utilizará mais tarde.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>Configurar a subscrição do evento
Ao criar uma subscrição de eventos, siga estes passos:

1. Selecione o tipo de ponto final como **Web Hook**. 
1. Especificar o ponto final **URI**.

    ![Selecione webhook do tipo ponto final](./media/secure-webhook-delivery/select-webhook.png)
1. Selecione o **separador funcionalidades adicionais** no topo da página **'Criar Subscrições de Eventos'.**
1. No separador **Funcionalidades Adicionais,** faça estes passos:
    1. Selecione **Aad autenticação**, e configuure o ID do inquilino e o ID da aplicação:
    1. Copie o ID do inquilino da Azure AD a partir da saída do script e insira-o no campo ID do **Inquilino AAD.**
    1. Copie o ID da aplicação Azure a partir da saída do script e insira-o no campo **ID da aplicação AAD.** Em alternativa, pode utilizar o ID URI da aplicação AAD. Para mais informações sobre o ID URI da aplicação, consulte [este artigo.](../app-service/configure-authentication-provider-aad.md)

        ![Ação Segura webhook](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a monitorização das entregas de eventos, consulte [a entrega de mensagens monitor a Grelha de Eventos](monitor-event-delivery.md).
* Para obter mais informações sobre a chave de autenticação, consulte [a segurança e a autenticação da Grade de Eventos.](security-authentication.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).
