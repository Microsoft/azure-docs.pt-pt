---
title: Criar e gerir grupos de ação no portal Azure
description: Saiba como criar e gerir grupos de ação no portal Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 6/5/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: dbc810ad7227d9d47099fe85e89a92c8fa750302
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465257"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerir grupos de ação no portal Azure
Um grupo de ação é uma coleção de preferências de notificação definidas pelo proprietário de uma subscrição Azure. Os alertas Azure Monitor e Service Health utilizam grupos de ação para notificar os utilizadores de que foi desencadeado um alerta. Vários alertas podem utilizar o mesmo grupo de ação ou diferentes grupos de ação, dependendo dos requisitos do utilizador. Pode configurar até 2.000 grupos de ação numa subscrição.

Configura uma ação para notificar uma pessoa por e-mail ou SMS, recebendo uma confirmação indicando que foram adicionadas ao grupo de ação.

Este artigo mostra-lhe como criar e gerir grupos de ação no portal Azure.

Cada ação é constituída pelas seguintes propriedades:

* **Nome**: Um identificador único dentro do grupo de ação.  
* **Tipo de ação**: A ação executada. Exemplos incluem envio de uma chamada de voz, SMS, e-mail; ou desencadeando vários tipos de ações automatizadas. Veja os tipos mais tarde neste artigo.
* **Detalhes**: Os detalhes correspondentes que variam por *tipo de ação.*

Para obter informações sobre como utilizar modelos do Gestor de Recursos Azure para configurar grupos de ação, consulte [os modelos do Gestor de Recursos do grupo action](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Criar um grupo de ação utilizando o portal Azure

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Monitor**. O **painel monitor** consolida todas as suas definições e dados de monitorização numa única vista.

1. Selecione **Alertas** e, em seguida, selecione **Gerir ações**.

    ![Gerir botão de ações](./media/action-groups/manage-action-groups.png)
    
1. **Selecione Adicionar grupo de ação**e preencha os campos.

    ![O comando "Adicionar grupo de ação"](./media/action-groups/add-action-group.png)
    
1. Introduza um nome na caixa **de nome do grupo Action** e introduza um nome na caixa de **nomes Curto.** O nome abreviado é utilizado em vez de um nome de grupo de ação completo quando as notificações são enviadas através deste grupo.

      ![Caixa de diálogo do grupo de ação Add](./media/action-groups/action-group-define.png)

1. A **caixa de subscrição** enche-se automaticamente com a sua subscrição atual. Esta subscrição é aquela em que o grupo de ação é salvo.

1. Selecione o **grupo de Recursos** no qual o grupo de ação é guardado.

1. Defina uma lista de ações. Fornecer o seguinte para cada ação:

    1. **Nome**: Introduza um identificador único para esta ação.

    1. **Tipo de Ação**: Selecione o Runbook de Automação, Função Azure, Função de Gestor de Recursos do E-mail Azure, Email/SMS/Push/Voice, ITSM, Logic App, Secure Webhook, Webhook.

    1. **Detalhes**: Com base no tipo de ação, introduza um número de telefone, endereço de e-mail, webhook URI, app Azure, conexão ITSM ou livro de registo de automação. Para a AÇÃO ITSM, especifique ainda o **Produto de Trabalho** e outros campos que a sua ferramenta ITSM necessita.
    
    1. **Esquema comum de alerta**: Pode optar por ativar o esquema comum de [alerta,](https://aka.ms/commonAlertSchemaDocs)que proporciona a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta do Monitor Azure.

1. Selecione **OK** para criar o grupo de ação.

## <a name="manage-your-action-groups"></a>Gerir os seus grupos de ação

Depois de criar um grupo de ação, pode ver **grupos de ação** selecionando **As ações** de Gerir a partir da página de aterragem **alertas** no painel **monitor.** Selecione o grupo de ação que pretende gerir para:

* Adicione, edite ou remova as ações.
* Apague o grupo de ação.

## <a name="action-specific-information"></a>Informação específica da ação

> [!NOTE]
> Consulte [os limites do serviço de subscrição para monitorização](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) dos limites numéricos em cada um dos itens abaixo.  

### <a name="automation-runbook"></a>Runbook de Automatização
Consulte os limites do [serviço de subscrição Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) para limites nas cargas executadas.

Você pode ter um número limitado de ações Runbook em um Grupo de Ação. 

### <a name="azure-app-push-notifications"></a>Notificações push da aplicação Azure
Você pode ter um número limitado de ações de aplicativoS Azure em um Grupo de Ação.

### <a name="email"></a>Email
Os e-mails serão enviados a partir dos seguintes endereços de e-mail. Certifique-se de que a sua filtragem de e-mail está configurada adequadamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Você pode ter um número limitado de ações de e-mail em um Grupo de Ação. Consulte o artigo [de informação limitativo da taxa.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="email-azure-resource-manager-role"></a>E-mail da Função do Azure Resource Manager
Envie e-mail aos membros da função da subscrição. O e-mail só será enviado para os membros **do utilizador AZure AD** da função. O e-mail não será enviado aos grupos ou principais de serviço do Azure Active Directory.

Você pode ter um número limitado de ações de e-mail em um Grupo de Ação. Consulte o artigo [de informação limitativo da taxa.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="function"></a>Função
Chama um ponto final de gatilho HTTP existente em [Funções Azure](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

Pode ter um número limitado de ações de função num Grupo de Ação.

### <a name="itsm"></a>ITSM
A AÇÃO ITSM requer uma Ligação ITSM. Saiba como criar uma [ligação ITSM](../../azure-monitor/platform/itsmc-overview.md).

Poderá ter um número limitado de ações ITSM num Grupo de Ação. 

### <a name="logic-app"></a>Aplicação Lógica
Você pode ter um número limitado de ações de Aplicação Lógica em um Grupo de Ação.

### <a name="secure-webhook"></a>Webhook Seguro
A ação Action Groups Webhook permite-lhe aproveitar o Azure Ative Directory para garantir a ligação entre o seu grupo de ação e a sua API web protegida (webhook endpoint). O fluxo de trabalho global para tirar partido desta funcionalidade é descrito abaixo. Para obter uma visão geral das aplicações ad da Azure e dos principais serviços, consulte a [plataforma de identidade da Microsoft (v2.0).](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

1. Crie uma Aplicação AD Azure para a sua API web protegida. Consulte https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configuure a sua API protegida para ser chamada por uma app daemon.
    
1. Ative os Grupos de Ação para utilizar a sua aplicação AD Azure.

    > [!NOTE]
    > Você deve ser um membro da função de [Administrador de Aplicação AD Azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) para executar este script.
    
    - Modifique a chamada connect-AzureAD do script PowerShell para utilizar o seu ID do inquilino Azure.
    - Modifique a variável $myAzureADApplicationObjectId do script PowerShell para utilizar o ID do objeto da sua aplicação AD Azure.
    - Executar o script modificado.
    
1. Configure a ação do Action Group Secure Webhook.
    - Copie o valor $myApp.ObjectId a partir do script e introduza-o no campo de ID do objeto de aplicação na definição de ação webhook.
    
    ![Ação Segura webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Script Seguro Webhook PowerShell

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
Consulte a [taxa de limitação de informações](./../../azure-monitor/platform/alerts-rate-limiting.md) e [o comportamento de alerta de SMS](../../azure-monitor/platform/alerts-sms-behavior.md) para obter informações adicionais importantes. 

Pode ter um número limitado de ações de SMS num Grupo de Ação.

> [!NOTE]
> Se a interface de utilizador do grupo de ação do portal Azure não o permitir selecionar o seu código país/região, então o SMS não é suportado para o seu país/região.  Se o código país/região não estiver disponível, pode votar para que o seu país/região seja adicionado à [voz do utilizador.](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice) Entretanto, um trabalho em torno é fazer com que o seu grupo de ação ligue para um webhook a um fornecedor de SMS de terceiros com apoio no seu país/região.  

Os preços dos países/regiões apoiados constam da página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
  

### <a name="voice"></a>Voz
Consulte o artigo [de informação limitativo](./../../azure-monitor/platform/alerts-rate-limiting.md) da taxa para um comportamento adicional importante.

Pode ter um número limitado de ações de voz num Grupo de Ação.

> [!NOTE]
> Se a interface de utilizador do grupo de ação do portal Azure não permitir que selecione o seu código país/região, então as chamadas de voz não são suportadas para o seu país/região. Se o código país/região não estiver disponível, pode votar para que o seu país/região seja adicionado à [voz do utilizador.](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)  Entretanto, um trabalho em torno é fazer com que o seu grupo de ação ligue para um webhook para um fornecedor de chamadas de voz de terceiros com apoio no seu país/região.  

Os preços dos países/regiões apoiados constam da página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>Webhook
Webhooks são processados usando as seguintes regras
- Uma chamada webhook é tentada no máximo 3 vezes.
- A chamada será novamente experimentada se não for recebida uma resposta dentro do prazo de tempo ou se um dos seguintes códigos de estado HTTP for devolvido: 408, 429, 503 ou 504.
- A primeira chamada esperará 10 segundos por uma resposta.
- A segunda e terceira tentativas esperarão 30 segundos por uma resposta.
- Após as 3 tentativas de ligar para o webhook, nenhum grupo de ação ligará para o ponto final durante 15 minutos.

Intervalos de endereço IP de origem
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

Para receber atualizações sobre alterações a estes endereços IP, recomendamos que configuize um alerta de Saúde de Serviço, que monitoriza notificações informativas sobre o serviço Grupos de Ação.

Você pode ter um número limitado de ações Webhook em um Grupo de Ação.



## <a name="next-steps"></a>Próximos passos
* Saiba mais sobre [o comportamento do alerta por SMS.](../../azure-monitor/platform/alerts-sms-behavior.md)  
* Obtenha uma [compreensão do esquema de alerta de alerta de atividade webhook](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Saiba mais sobre [o CONECTOR ITSM](../../azure-monitor/platform/itsmc-overview.md).
* Saiba mais sobre [a limitação da taxa](../../azure-monitor/platform/alerts-rate-limiting.md) em alertas.
* Obtenha uma [visão geral dos alertas de registo de atividades](../../azure-monitor/platform/alerts-overview.md)e aprenda a receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de saúde do serviço for publicada](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
