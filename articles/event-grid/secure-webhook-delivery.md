---
title: Entrega segura de webhook com o Azure AD na grade de eventos do Azure
description: Descreve como entregar eventos a pontos de extremidade HTTPS protegidos por Azure Active Directory usando a grade de eventos do Azure
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 9ee1dd74384725b3fffbfea7144c3a536b50c531
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174554"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publicar eventos para Azure Active Directory pontos de extremidade protegidos

Este artigo descreve como aproveitar Azure Active Directory para proteger a conexão entre sua assinatura de evento e seu ponto de extremidade do webhook. Para obter uma visão geral dos aplicativos do Azure AD e das entidades de serviço, consulte [visão geral da plataforma Microsoft Identity (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

Este artigo usa a portal do Azure para demonstração, no entanto, o recurso também pode ser habilitado usando a CLI, o PowerShell ou os SDKs.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-and-azure-ad-application"></a>Criar e aplicativo do Azure AD

Comece criando um aplicativo do Azure AD para seu ponto de extremidade protegido. Veja https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configure sua API protegida para ser chamada por um aplicativo daemon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Habilitar a grade de eventos para usar seu aplicativo do Azure AD

Use o script do PowerShell abaixo para criar uma função e uma entidade de serviço em seu aplicativo do Azure AD. Você precisará da ID do locatário e da ID de objeto do seu aplicativo do Azure AD:

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. Modifique o $myTenantId do script do PowerShell para usar sua ID de locatário do Azure AD.
1. Modificar o $myAzureADApplicationObjectId do script do PowerShell para usar a ID de objeto do seu aplicativo do Azure AD
1. Execute o script modificado.

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
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
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Configurar a assinatura de evento

No fluxo de criação para sua assinatura de evento, selecione tipo de ponto de extremidade ' gancho da Web '. Depois de receber o URI do ponto de extremidade, clique na guia recursos adicionais na parte superior da folha criar assinaturas de evento.

![Selecione o tipo de ponto de extremidade webhook](./media/secure-webhook-delivery/select-webhook.png)

Na guia recursos adicionais, marque a caixa "usar autenticação do AAD" e configure a ID do locatário e a ID do aplicativo:

* Copie a ID de locatário do Azure AD da saída do script e insira-a no campo ID de locatário do AAD.
* Copie a ID do aplicativo do Azure AD da saída do script e insira-a no campo ID do aplicativo do AAD.

    ![Ação proteger webhook](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre como monitorar entregas de eventos, consulte [monitorar a entrega de mensagens na grade de eventos](monitor-event-delivery.md).
* Para obter mais informações sobre a chave de autenticação, consulte [segurança e autenticação da grade de eventos](security-authentication.md).
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md).