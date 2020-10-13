---
title: Entrega segura da WebHook com Azure AD na grelha de eventos Azure
description: Descreve como entregar eventos a pontos finais HTTPS protegidos pelo Azure Ative Directory usando a Azure Event Grid
ms.topic: how-to
ms.date: 10/05/2020
ms.openlocfilehash: 0320e78e6b436f6ba1c0a6ca1bfec81eb974e106
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812204"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publicar eventos nos pontos finais protegidos do Azure Active Directory

Este artigo descreve como aproveitar o Azure Ative Directory para garantir a ligação entre a subscrição do evento e o ponto final do seu webhook. Para obter uma visão geral das aplicações ad da Azure e dos principais serviços, consulte a [plataforma de identidade da Microsoft (v2.0).](../active-directory/develop/v2-overview.md)

Este artigo utiliza o portal Azure para demonstração, no entanto a funcionalidade também pode ser ativada usando CLI, PowerShell ou os SDKs.


## <a name="create-an-azure-ad-application"></a>Criar uma aplicação AD AZure

Comece por criar uma aplicação AD AZure para o seu ponto final protegido. Consulte https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configuure a sua API protegida para ser chamada por uma app daemon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Ativar a Grelha de Eventos para utilizar a sua aplicação AD Azure
Esta secção mostra-lhe como ativar a Grelha de Eventos para utilizar a sua aplicação AD AZure. 

> [!NOTE]
> Você deve ser um membro da função de [Administrador de Aplicação AD Azure](../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles) para executar este script.

### <a name="connect-to-your-azure-tenant"></a>Ligue-se ao seu inquilino Azure
Primeiro, ligue-se ao seu inquilino Azure usando o `Connect-AzureAD` comando. 

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"
Connect-AzureAD -TenantId $myTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Criar o principal do serviço Microsoft.EventGrid
Execute o seguinte script para criar o principal de serviço para **a Microsoft.EventGrid** se ele já não existir. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Crie um papel para a sua aplicação   
Execute o seguinte script para criar um papel para a sua aplicação AZure AD. Neste exemplo, o nome da função é: **AzureEventGridSecureWebhook**. Modifique o script `$myTenantId` PowerShell's para usar o seu ID do inquilino Azure AD, e com o `$myAzureADApplicationObjectId` ID do objeto da sua aplicação AD Azure

```PowerShell
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}

# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
} else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles
```

### <a name="add-event-grid-service-principal-to-the-role"></a>Adicione o principal do serviço de Grade de Eventos ao papel    
Agora, executar o comando para atribuir o `New-AzureADServiceAppRoleAssignment` principal do serviço de Grade de Eventos ao papel que criou no passo anterior. 

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Execute os seguintes comandos para obter informações de saída que utilizará os próximos passos. 

```powershell    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
```
    
## <a name="configure-the-event-subscription"></a>Configurar a subscrição do evento

No fluxo de criação para a subscrição do seu evento, selecione o tipo de ponto final 'Web Hook'. Depois de ter dado o seu ponto final URI, clique no separador funcionalidades adicionais no topo da lâmina de subscrições de eventos criar.

![Selecione webhook do tipo ponto final](./media/secure-webhook-delivery/select-webhook.png)

No separador funcionalidades adicionais, verifique a caixa para "Utilizar a autenticação AAD" e configuure o ID do Inquilino e o ID da aplicação:

* Copie o ID do Inquilino Azure AD a partir da saída do script e insira-o no campo ID do Inquilino AAD.
* Copie o ID da aplicação AZure a partir da saída do script e insira-o no campo ID da aplicação AAD.

    ![Ação Segura webhook](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a monitorização das entregas de eventos, consulte [a entrega de mensagens monitor a Grelha de Eventos](monitor-event-delivery.md).
* Para obter mais informações sobre a chave de autenticação, consulte [a segurança e a autenticação da Grade de Eventos.](security-authentication.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).
