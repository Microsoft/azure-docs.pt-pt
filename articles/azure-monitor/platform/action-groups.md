---
title: Criar e gerir grupos de ação no portal do Azure
description: Saiba como criar e gerir grupos de ação no portal do Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 7/08/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 842965aa49ae4cd546fe9c107107d2a2ceebebbb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705260"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerir grupos de ação no portal do Azure
Um grupo de ação é uma coleção de preferências de notificações definidos pelo proprietário de uma subscrição do Azure. Alertas de monitorização e estado de funcionamento do serviço do Azure utilizam grupos de ação para notificar os utilizadores que foi acionado um alerta. Vários alertas podem utilizar o mesmo grupo de ação ou a grupos de ação diferentes consoante os requisitos do utilizador. Só pode configurar grupos de ação até 2000 numa subscrição.

Configure uma ação para notificar uma pessoa por e-mail ou SMS, recebem uma confirmação que indica que qual foi adicionado ao grupo de ação.

Este artigo mostra-lhe como criar e gerir grupos de ação no portal do Azure.

Cada ação é constituída pelas seguintes propriedades:

* **Nome**: Um identificador exclusivo dentro do grupo de ação.  
* **Tipo de ação**: A ação executada. Os exemplos incluem enviar um e-mail de chamada, SMS, voz; ou acionar vários tipos de ações automatizadas. Ver tipos neste artigo.
* **Detalhes**: Os detalhes de correspondentes que variam de acordo com *tipo de ação*.

Para obter informações sobre como utilizar os modelos Azure Resource Manager para configurar grupos de ação, veja [modelos do Resource Manager de grupo de ação](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Criar um grupo de ação com o portal do Azure

1. Na [portal do Azure](https://portal.azure.com), selecione **Monitor**. O **Monitor** painel consolida todas as suas monitorização definições e dados numa vista.

    ![O serviço de "Monitor"](./media/action-groups/home-monitor.png)
    
1. Selecione **alertas** , em seguida, selecione **gerir ações**.

    ![Gerir o botão de ações](./media/action-groups/manage-action-groups.png)
    
1. Selecione **grupo de ação de adicionar**e preencha os campos.

    ![O comando "Adicionar grupo de ação"](./media/action-groups/add-action-group.png)
    
1. Introduza um nome na **nome do grupo de ação** caixa e introduza um nome na **nome abreviado** caixa. O nome abreviado é utilizado em vez de um nome de grupo de ação completo quando as notificações são enviadas através deste grupo.

      ![Caixa de diálogo do grupo de ação de adicionar"](./media/action-groups/action-group-define.png)

1. O **subscrição** caixa autofills com a sua subscrição atual. Esta subscrição é aquela na qual o grupo de ação é guardado.

1. Selecione o **grupo de recursos** no qual o grupo de ação é guardado.

1. Defina uma lista de ações. Fornece o seguinte para cada ação:

    1. **Nome**: Introduza um identificador exclusivo para esta ação.

    1. **Tipo de ação**: Selecione o E-Mail/SMS/Push/voz, aplicação lógica, Webhook, ITSM ou Runbook de automatização.

    1. **Detalhes**: Com base no tipo de ação, introduza um número de telefone, endereço de e-mail, webhook URI, aplicações do Azure, a ligação ITSM ou runbook de automatização. Para a ação de ITSM, adicionalmente especificar **Item de trabalho** e requer a sua ferramenta ITSM outros campos.
    
    1. **Esquema comum do alerta**: Pode optar por ativar os [esquema comum do alerta](https://aka.ms/commonAlertSchemaDocs), que fornece a vantagem de ter um único extensível e payload de alerta unificada em todas as o alerta de serviços no Azure Monitor.

1. Selecione **OK** para criar o grupo de ação.

## <a name="manage-your-action-groups"></a>Gerir os seus grupos de ação

Depois de criar um grupo de ação, é visível na **grupos de ação** secção a **Monitor** painel. Selecione o grupo de ação que pretende gerir para:

* Adicionar, editar ou remover ações.
* Elimine o grupo de ação.

## <a name="action-specific-information"></a>Informações específicas da ação

> [!NOTE]
> Ver [limites de serviço de subscrição para monitorização](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) para limites numérico em cada um dos itens abaixo.  

### <a name="azure-app-push-notifications"></a>Notificações Push da aplicação do Azure
Pode ter um número limitado de ações de aplicação do Azure num grupo de ação.

### <a name="email"></a>Email
Mensagens de correio eletrónico serão enviadas dos seguintes endereços de e-mail. Certifique-se de que a filtragem de e-mail está configurado corretamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Pode ter um número limitado de ações de e-mail num grupo de ação. Consulte a [informações de limitação de taxas](./../../azure-monitor/platform/alerts-rate-limiting.md) artigo.

### <a name="itsm"></a>ITSM
Ação de ITSM requer uma ligação de ITSM. Saiba como criar uma [ligação de ITSM](../../azure-monitor/platform/itsmc-overview.md).

Pode ter um número limitado de ações do ITSM num grupo de ação. 

### <a name="logic-app"></a>Aplicação Lógica
Pode ter um número limitado de ações de aplicação lógica num grupo de ação.

### <a name="function"></a>Função
As teclas de função para aplicações de função configuradas como ações são lidas por meio da API de funções, que atualmente requer que as aplicações de função da v2 para configurar a definição "AzureWebJobsSecretStorageType" para "ficheiros" de aplicação. Para obter mais informações, consulte [é alterado para gestão de chaves no V2 funções]( https://aka.ms/funcsecrets).

Pode ter um número limitado de ações de função num grupo de ação.

### <a name="automation-runbook"></a>Runbook de automatização
Consulte a [limites de serviço de subscrição do Azure](../../azure-subscription-service-limits.md) para limites em cargas de Runbook.

Pode ter um número limitado de ações de Runbook num grupo de ação. 

### <a name="sms"></a>SMS
Consulte a [informações de limitação de taxas](./../../azure-monitor/platform/alerts-rate-limiting.md) e [comportamento de alertas do SMS](../../azure-monitor/platform/alerts-sms-behavior.md) para informação adicional importante.

Pode ter um número limitado de ações de SMS num grupo de ação.  

### <a name="voice"></a>Voz
Consulte a [informações de limitação de taxas](./../../azure-monitor/platform/alerts-rate-limiting.md) artigo.

Pode ter um número limitado de ações de voz num grupo de ação.

### <a name="webhook"></a>Webhook
Os Webhooks são repetidos utilizando as seguintes regras. A chamada de webhook é repetida um máximo de 2 horas quando os seguintes códigos de estado HTTP são devolvidos: 408, 429, 503, 504 ou o ponto final de HTTP não responde. A primeira repetição ocorre ao fim de 10 segundos. A segunda repetição acontece após 100 segundos. Após duas falhas, nenhum grupo de ação irá chamar o ponto de extremidade durante 30 minutos. 

Intervalos de endereços IP de origem
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Para receber atualizações sobre as alterações a estes endereços IP, recomendamos que configurar um alerta de estado de funcionamento do serviço, que monitora informativas notificações sobre o serviço de grupos de ação.

Pode ter um número limitado de ações de Webhook num grupo de ação.

#### <a name="secure-webhook"></a>Secure Webhook
**A funcionalidade de proteger o Webhook está atualmente em pré-visualização.**

A ação do Webhook de grupos de ação permite-lhe tirar partido do Azure Active Directory para proteger a ligação entre o grupo de ação e a API (ponto final do webhook) de web protegida. O fluxo de trabalho geral para tirar partido desta funcionalidade é descrito abaixo. Para uma descrição geral de aplicações do Azure AD e principais de serviço, consulte [visão geral de plataforma (v2.0) do Microsoft identity](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Crie uma aplicação do Azure AD para a API web protegida. Consulte https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configure a sua API protegida deve ser chamado por uma aplicação de daemon.
    
1. Ative grupos de ação utilizar a sua aplicação do Azure AD.

    > [!NOTE]
    > Tem de ser um membro do [função de administrador de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) para executar este script.
    
    - Modificar a chamada de Connect-AzureAD o script do PowerShell para utilizar o seu ID de inquilino do Azure AD.
    - Modificar variável o script do PowerShell $myAzureADApplicationObjectId para utilizar o ID de objeto da aplicação do Azure AD
    - Execute o script modificado.
    
1. Configure a ação de Webhook do grupo de ação.
    - Copie o valor $myApp.ObjectId de scripts e introduza-o no campo de ID de objeto de aplicativo na definição de ação do Webhook.
    
    ![Proteger a ação do Webhook](./media/action-groups/action-groups-secure-webhook.png)

##### <a name="secure-webhook-powershell-script"></a>Proteger o Script do PowerShell de Webhook

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


## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [comportamento de alertas do SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Obter um [compreensão sobre o esquema de webhook de alerta de registo de atividades](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Saiba mais sobre [conector ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Saiba mais sobre [limitação de velocidade](../../azure-monitor/platform/alerts-rate-limiting.md) em alertas.
* Obter um [descrição geral dos alertas de registo de atividade](../../azure-monitor/platform/alerts-overview.md)e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de estado de funcionamento do serviço é lançada](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
