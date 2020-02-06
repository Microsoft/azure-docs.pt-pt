---
title: Criar e gerenciar grupos de ação no portal do Azure
description: Saiba como criar e gerenciar grupos de ações no portal do Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 8/19/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 01d3edb3de9e57fa7fa8db2ede863c2aa3e100ed
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030750"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerenciar grupos de ação no portal do Azure
Um grupo de ações é uma coleção de preferências de notificação definidas pelo proprietário de uma assinatura do Azure. Azure Monitor e alertas de integridade do serviço usam grupos de ação para notificar os usuários de que um alerta foi disparado. Vários alertas podem usar o mesmo grupo de ações ou grupos de ação diferentes, dependendo dos requisitos do usuário. Você pode configurar até 2.000 grupos de ação em uma assinatura.

Você configura uma ação para notificar uma pessoa por email ou SMS, elas recebem uma confirmação indicando que foram adicionadas ao grupo de ações.

Este artigo mostra como criar e gerenciar grupos de ações no portal do Azure.

Cada ação é composta das seguintes propriedades:

* **Nome**: Um identificador único dentro do grupo de ação.  
* **Tipo**de ação : A ação executada. Os exemplos incluem enviar uma chamada de voz, SMS, email; ou disparando vários tipos de ações automatizadas. Veja os tipos mais adiante neste artigo.
* **Detalhes**: Os detalhes correspondentes que variam por tipo de *ação*.

Para obter informações sobre como usar os modelos do Gestor de Recursos Azure para configurar grupos de ação, consulte [os modelos](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)do Gestor de Recursos do grupo de ação .

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Criar um grupo de ação usando o portal do Azure

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Monitor**. O **painel monitor** consolida todas as suas definições de monitorização e dados numa só vista.

1. Selecione **Alertas** e, em seguida, selecione **Gerir ações**.

    ![Botão Gerenciar ações](./media/action-groups/manage-action-groups.png)
    
1. Selecione **Adicionar grupo de ação**e preencher os campos.

    ![O comando "Adicionar grupo de ações"](./media/action-groups/add-action-group.png)
    
1. Introduza um nome na caixa de **nomes** do grupo Action e introduza um nome na caixa **de nomes curtos.** O nome abreviado é utilizado em vez de um nome de grupo de ação completo quando as notificações são enviadas através deste grupo.

      ![A caixa de diálogo Adicionar grupo de ações](./media/action-groups/action-group-define.png)

1. A caixa **de subscrição** enche automaticamente com a subscrição atual. Essa assinatura é aquela na qual o grupo de ações é salvo.

1. Selecione o **grupo Derecursos** no qual o grupo de ação é guardado.

1. Defina uma lista de ações. Forneça o seguinte para cada ação:

    1. **Nome**: Introduza um identificador único para esta ação.

    1. **Tipo de ação**: Selecione email/SMS/Push/Voice, Logic App, Webhook, ITSM ou Automation Runbook.

    1. **Detalhes**: Com base no tipo de ação, introduza um número de telefone, endereço de e-mail, webhook URI, aplicação Azure, conexão ITSM ou Livro de Automação. Para a Ação ITSM, especifique adicionalmente **o Artigo** de Trabalho e outros campos que a sua ferramenta ITSM necessita.
    
    1. **Esquema de alerta comum**: Pode optar por ativar o esquema de [alerta comum,](https://aka.ms/commonAlertSchemaDocs)que proporciona a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta do Monitor Azure.

1. Selecione **OK** para criar o grupo de ação.

## <a name="manage-your-action-groups"></a>Gerenciar seus grupos de ação

Depois de criar um grupo de ação, pode ver **grupos** de ação selecionando ações de **Gestão** a partir da página de aterragem de **Alertas** no painel **Monitor.** Selecione o grupo de ações que você deseja gerenciar para:

* Adicionar, editar ou remover ações.
* Exclua o grupo de ações.

## <a name="action-specific-information"></a>Informações específicas da ação

> [!NOTE]
> Consulte os limites do [serviço de subscrição para monitorização](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) para limites numéricos em cada um dos itens abaixo.  

### <a name="automation-runbook"></a>Livro de Execução da Automação
Consulte os limites do serviço de [subscrição Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) para limites nas cargas do Livro de Corridas.

Você pode ter um número limitado de ações de runbook em um grupo de ações. 

### <a name="azure-app-push-notifications"></a>Notificações push da aplicação Azure
Você pode ter um número limitado de ações de aplicativo do Azure em um grupo de ação.

### <a name="email"></a>Email
Os emails serão enviados dos endereços de email a seguir. Verifique se a filtragem de email está configurada corretamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Você pode ter um número limitado de ações de email em um grupo de ações. Consulte a taxa que limita o artigo de [informação.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="email-azure-resource-manager-role"></a>Papel de Gestor de Recursos Azure de e-mail
Envie um email para os membros da função da assinatura.

Você pode ter um número limitado de ações de email em um grupo de ações. Consulte a taxa que limita o artigo de [informação.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="function"></a>Função
Chama um ponto final de gatilho HTTP existente nas [funções azure](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

Você pode ter um número limitado de ações de função em um grupo de ações.

### <a name="itsm"></a>ITSM
A ação de ITSM requer uma conexão de ITSM. Aprenda a criar uma [Conexão ITSM](../../azure-monitor/platform/itsmc-overview.md).

Você pode ter um número limitado de ações de ITSM em um grupo de ações. 

### <a name="logic-app"></a>Aplicação Lógica
Você pode ter um número limitado de ações de aplicativo lógico em um grupo de ação.

### <a name="secure-webhook"></a>Proteger webhook
**A funcionalidade Secure Webhook encontra-se atualmente em Pré-visualização.**

A ação de webhook de grupos de ações permite que você aproveite Azure Active Directory para proteger a conexão entre o grupo de ação e a API Web protegida (ponto de extremidade do webhook). O fluxo de trabalho geral para aproveitar essa funcionalidade é descrito abaixo. Para uma visão geral das aplicações e principais de serviços da Azure AD, consulte a visão geral da [plataforma de identidade da Microsoft (v2.0).](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

1. Crie um aplicativo do Azure AD para sua API Web protegida. Veja https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configure sua API protegida para ser chamada por um aplicativo daemon.
    
1. Habilite grupos de ação para usar seu aplicativo do Azure AD.

    > [!NOTE]
    > Deve ser membro do papel de Administrador de [Aplicação Da AD Azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) para executar este script.
    
    - Modifique a chamada Connect-AzureAD do script do PowerShell para usar sua ID de locatário do Azure AD.
    - Modifique a variável do script do PowerShell $myAzureADApplicationObjectId para usar a ID de objeto do seu aplicativo do Azure AD
    - Execute o script modificado.
    
1. Configure a ação de webhook segura do grupo de ações.
    - Copie o valor $myApp. ObjectId do script e insira-o no campo ID de objeto do aplicativo na definição de ação do webhook.
    
    ![Ação proteger webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Script do PowerShell de webhook seguro

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
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
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
Consulte a [taxa que limita a informação](./../../azure-monitor/platform/alerts-rate-limiting.md) e o comportamento de alerta de [SMS](../../azure-monitor/platform/alerts-sms-behavior.md) para obter informações importantes adicionais.

Você pode ter um número limitado de ações de SMS em um grupo de ações.  

### <a name="voice"></a>Voz
Consulte a taxa que limita o artigo de [informação.](./../../azure-monitor/platform/alerts-rate-limiting.md)

Você pode ter um número limitado de ações de voz em um grupo de ações.

### <a name="webhook"></a>Webhook
Os WebHooks são repetidos usando as regras a seguir. A chamada de webhook é repetida no máximo duas vezes quando os seguintes códigos de status HTTP são retornados: 408, 429, 503, 504 ou o ponto de extremidade HTTP não responde. A primeira repetição ocorre ao fim de 10 segundos. A segunda repetição ocorre após 100 segundos. Após duas falhas, nenhum grupo de ação chamará o ponto de extremidade por 30 minutos. 

Intervalos de endereços IP de origem
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Para receber atualizações sobre alterações nesses endereços IP, recomendamos que você configure um alerta de integridade do serviço, que monitora as notificações informativas sobre o serviço de grupos de ação.

Você pode ter um número limitado de ações de webhook em um grupo de ações.



## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o comportamento de [alerta de SMS.](../../azure-monitor/platform/alerts-sms-behavior.md)  
* Obtenha uma [compreensão do esquema webhook](../../azure-monitor/platform/activity-log-alerts-webhook.md)de alerta de registo de atividade .  
* Saiba mais sobre o [Conector ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Saiba mais sobre [a limitação da taxa](../../azure-monitor/platform/alerts-rate-limiting.md) nos alertas.
* Obtenha uma [visão geral dos alertas de registo de atividadee](../../azure-monitor/platform/alerts-overview.md)aprenda a receber alertas.  
* Saiba [configurar alertas sempre que é publicada uma notificação](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)de saúde de serviço.
