---
title: Entrega segura do WebHook com AD Azure na Grelha de Eventos Azure
description: Descreve como entregar eventos a pontos finais HTTPS protegidos pelo Azure Ative Directory utilizando a Grelha de Eventos Azure
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 86d647ebfcf6e4c1ea8d05f58dd1f559d6e30cfc
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900460"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publique eventos para azure Ative Directory pontos finais protegidos

Este artigo descreve como aproveitar o Diretório Ativo do Azure para garantir a ligação entre a subscrição do evento e o seu ponto final do webhook. Para uma visão geral das aplicações e principais de serviços da Azure AD, consulte a visão geral da [plataforma de identidade da Microsoft (v2.0).](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

Este artigo utiliza o portal Azure para demonstração, no entanto a funcionalidade também pode ser ativada utilizando CLI, PowerShell ou os SDKs.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Criar uma aplicação Azure AD

Comece por criar uma Aplicação AD Azure para o seu ponto final protegido. Consulte https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configure a sua API protegida para ser chamada por uma aplicação daemon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Ativar a Grelha de Eventos para utilizar a sua aplicação Azure AD

Utilize o script PowerShell abaixo para criar um princípio de função e serviço na sua Aplicação AD Azure. Você precisará do ID do Inquilino e id do objeto da sua aplicação Azure AD:

   > [!NOTE]
   > Deve ser membro do papel de Administrador de [Aplicação Da AD Azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) para executar este script.
    
1. Modifique o $myTenantId do script PowerShell para utilizar o seu ID de Inquilino AD Azure.
1. Modifique o $myAzureADApplicationObjectId do script PowerShell para utilizar o ID do objeto da sua aplicação AD Azure
1. Executa o guião modificado.

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
    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
Write-Host "My Azure AD Application's Roles: "
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Configure a subscrição do evento

No fluxo de criação para a subscrição do evento, selecione o tipo de ponto final 'Web Hook'. Depois de ter dado o seu ponto final URI, clique no separador de funcionalidades adicionais na parte superior da lâmina de subscrição de eventos.

![Selecione webhook tipo ponto final](./media/secure-webhook-delivery/select-webhook.png)

No separador de funcionalidades adicionais, verifique a caixa para "Utilizar a autenticação AAD" e configurar o ID do Arrendatário e id de aplicação:

* Copie o ID do Inquilino Azure AD a partir da saída do script e insira-o no campo de identificação do Inquilino AAD.
* Copie o ID de aplicação Azure AD a partir da saída do script e introduza-o no campo de ID de aplicação AAD.

    ![Ação segura do Webhook](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a monitorização das entregas de eventos, consulte a entrega de [mensagens Monitor Event Grid](monitor-event-delivery.md).
* Para mais informações sobre a chave de autenticação, consulte [a segurança e a autenticação da Rede de Eventos.](security-authentication.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
