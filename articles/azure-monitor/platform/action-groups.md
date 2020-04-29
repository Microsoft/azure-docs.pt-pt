---
title: Criar e gerir grupos de ação no portal Azure
description: Aprenda a criar e gerir grupos de ação no portal Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 4/17/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 339b11664308962962c59b2e9386ff122681293a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116218"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerir grupos de ação no portal Azure
Um grupo de ação é uma coleção de preferências de notificação definidas pelo proprietário de uma subscrição Azure. Os alertas do Azure Monitor e da Saúde de Serviço usam grupos de ação para notificar os utilizadores de que foi desencadeado um alerta. Vários alertas podem utilizar o mesmo grupo de ação ou diferentes grupos de ação, dependendo dos requisitos do utilizador. Pode configurar até 2.000 grupos de ação numa subscrição.

Configura uma ação para notificar uma pessoa por e-mail ou SMS, recebem uma confirmação indicando que foram adicionadas ao grupo de ação.

Este artigo mostra-lhe como criar e gerir grupos de ação no portal Azure.

Cada ação é composta pelas seguintes propriedades:

* **Nome**: Um identificador único dentro do grupo de ação.  
* **Tipo**de ação : A ação executada. Exemplos incluem envio de uma chamada de voz, SMS, e-mail; ou desencadeando vários tipos de ações automatizadas. Consulte os tipos mais tarde neste artigo.
* **Detalhes**: Os detalhes correspondentes que variam por tipo de *ação*.

Para obter informações sobre como usar os modelos do Gestor de Recursos Azure para configurar grupos de ação, consulte [os modelos](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)do Gestor de Recursos do grupo de ação .

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Criar um grupo de ação utilizando o portal Azure

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Monitor**. O **painel monitor** consolida todas as suas definições de monitorização e dados numa só vista.

1. Selecione **Alertas** e, em seguida, selecione **Gerir ações**.

    ![Gerir o botão Ações](./media/action-groups/manage-action-groups.png)
    
1. Selecione **Adicionar grupo de ação**e preencher os campos.

    ![O comando "Adicionar grupo de ação"](./media/action-groups/add-action-group.png)
    
1. Introduza um nome na caixa de **nomes** do grupo Action e introduza um nome na caixa **de nomes curtos.** O nome abreviado é utilizado em vez de um nome de grupo de ação completo quando as notificações são enviadas através deste grupo.

      ![Caixa de diálogo do grupo de ação Add](./media/action-groups/action-group-define.png)

1. A caixa **de subscrição** enche automaticamente com a subscrição atual. Esta subscrição é aquela em que o grupo de ação é guardado.

1. Selecione o **grupo Derecursos** no qual o grupo de ação é guardado.

1. Defina uma lista de ações. Forneça o seguinte para cada ação:

    1. **Nome**: Introduza um identificador único para esta ação.

    1. **Tipo de ação**: Selecione O Livro de Execução da Automatização, Função Azure, Função E-mail Azure Resource Manager, Email/SMS/Push/Voice, ITSM, Logic App, Secure Webhook, Webhook.

    1. **Detalhes**: Com base no tipo de ação, introduza um número de telefone, endereço de e-mail, webhook URI, aplicação Azure, conexão ITSM ou Livro de Automação. Para a Ação ITSM, especifique adicionalmente **o Artigo** de Trabalho e outros campos que a sua ferramenta ITSM necessita.
    
    1. **Esquema de alerta comum**: Pode optar por ativar o esquema de [alerta comum,](https://aka.ms/commonAlertSchemaDocs)que proporciona a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta do Monitor Azure.

1. Selecione **OK** para criar o grupo de ação.

## <a name="manage-your-action-groups"></a>Gerencie os seus grupos de ação

Depois de criar um grupo de ação, pode ver **grupos** de ação selecionando ações de **Gestão** a partir da página de aterragem de **Alertas** no painel **Monitor.** Selecione o grupo de ação que pretende conseguir:

* Adicione, edite ou remova as ações.
* Apague o grupo de ação.

## <a name="action-specific-information"></a>Informação específica de ação

> [!NOTE]
> Consulte os limites do [serviço de subscrição para monitorização](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) para limites numéricos em cada um dos itens abaixo.  

### <a name="automation-runbook"></a>Livro de Execução da Automação
Consulte os limites do serviço de [subscrição Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) para limites nas cargas do Livro de Corridas.

Você pode ter um número limitado de ações de Runbook em um Grupo de Ação. 

### <a name="azure-app-push-notifications"></a>Notificações push da aplicação Azure
Você pode ter um número limitado de ações de aplicações Azure em um Grupo de Ação.

### <a name="email"></a>Email
Os e-mails serão enviados a partir dos seguintes endereços de e-mail. Certifique-se de que a sua filtragem de e-mail está configurada adequadamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Você pode ter um número limitado de ações de e-mail em um Grupo de Ação. Consulte a taxa que limita o artigo de [informação.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="email-azure-resource-manager-role"></a>Papel de Gestor de Recursos Azure de e-mail
Envie e-mail aos membros do papel da subscrição. O e-mail só será enviado aos utilizadores da **Azure AD.** O e-mail não será enviado para grupos da AD Azure ou diretores de serviço.

Você pode ter um número limitado de ações de e-mail em um Grupo de Ação. Consulte a taxa que limita o artigo de [informação.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="function"></a>Função
Chama um ponto final de gatilho HTTP existente nas [funções azure](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

Pode ter um número limitado de ações de Função num Grupo de Ação.

### <a name="itsm"></a>ITSM
A AÇÃO ITSM requer uma ligação ITSM. Aprenda a criar uma [Conexão ITSM](../../azure-monitor/platform/itsmc-overview.md).

Pode ter um número limitado de ações de ITSM num Grupo de Ação. 

### <a name="logic-app"></a>Aplicação Lógica
Você pode ter um número limitado de ações de Aplicações Lógicas em um Grupo de Ação.

### <a name="secure-webhook"></a>Webhook seguro
A ação Webhook dos Grupos de Ação permite-lhe tirar partido do Diretório Ativo Azure para garantir a ligação entre o seu grupo de ação e a sua Web API protegida (ponto final do webhook). O fluxo de trabalho global para tirar partido desta funcionalidade é descrito abaixo. Para uma visão geral das aplicações e principais de serviços da Azure AD, consulte a visão geral da [plataforma de identidade da Microsoft (v2.0).](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

1. Crie uma aplicação AD Azure para a sua API web protegida. Consulte https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configure a sua API protegida para ser chamada por uma aplicação daemon.
    
1. Ative os Grupos de Ação a utilizar em a sua Aplicação AD Azure.

    > [!NOTE]
    > Deve ser membro do papel de Administrador de [Aplicação Da AD Azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) para executar este script.
    
    - Modifique a chamada Connect-AzureAD do script PowerShell para utilizar o seu ID de Inquilino AD Azure.
    - Modifique a variável $myAzureADApplicationObjectId do script PowerShell para utilizar o ID do objeto da sua aplicação AD Azure
    - Executa o guião modificado.
    
1. Configure a ação Webhook segura do Grupo de Ação.
    - Copie o valor $myApp.ObjectId a partir do script e introduza-o no campo id do objeto de aplicação na definição de ação Webhook.
    
    ![Ação segura do Webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Script PowerShell de Webhook seguro

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

Pode ter um número limitado de ações de SMS num Grupo de Ação.

Se a interface de utilizador do grupo de ação do portal Azure não permitir selecionar o seu código de país, então o SMS não é suportado para o seu país. Os preços dos países apoiados constam da página de preços do [Monitor Do Azure.](https://azure.microsoft.com/pricing/details/monitor/) Se o seu código de país não estiver disponível, pode votar para que o seu país seja adicionado à voz do [utilizador](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  

  

### <a name="voice"></a>Voz
Consulte a taxa que limita o artigo de [informação.](./../../azure-monitor/platform/alerts-rate-limiting.md)

Pode ter um número limitado de ações de Voz num Grupo de Ação.

Se a interface de utilizador do grupo de ação do portal Azure não permitir selecionar o seu código de país, então as chamadas de voz não são suportadas para o seu país. Os preços dos países apoiados constam da página de preços do [Monitor Do Azure.](https://azure.microsoft.com/pricing/details/monitor/) Se o seu código de país não estiver disponível, pode votar para que o seu país seja adicionado à voz do [utilizador](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  

### <a name="webhook"></a>Webhook
Os webhooks são novamente experimentados usando as seguintes regras. A chamada webhook é novamente experimentada um máximo de 2 vezes quando os seguintes códigos de estado HTTP são devolvidos: 408, 429, 503, 504 ou o ponto final http não responde. A primeira repetição ocorre ao fim de 10 segundos. A segunda tentativa acontece após 100 segundos. Depois de duas falhas, nenhum grupo de ação ligará para o ponto final durante 30 minutos. 

Gamas de endereços IP de origem
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

Para receber atualizações sobre alterações nestes endereços IP, recomendamos que configure um alerta de Saúde de Serviço, que monitorize notificações informativas sobre o serviço de Grupos de Ação.

Você pode ter um número limitado de ações webhook em um Grupo de Ação.



## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o comportamento de [alerta de SMS.](../../azure-monitor/platform/alerts-sms-behavior.md)  
* Obtenha uma [compreensão do esquema webhook](../../azure-monitor/platform/activity-log-alerts-webhook.md)de alerta de registo de atividade .  
* Saiba mais sobre o [Conector ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Saiba mais sobre [a limitação da taxa](../../azure-monitor/platform/alerts-rate-limiting.md) nos alertas.
* Obtenha uma [visão geral dos alertas de registo de atividadee](../../azure-monitor/platform/alerts-overview.md)aprenda a receber alertas.  
* Saiba [configurar alertas sempre que é publicada uma notificação](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)de saúde de serviço.
