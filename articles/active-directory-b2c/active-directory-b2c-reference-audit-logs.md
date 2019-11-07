---
title: Exemplos de logs de auditoria e definições no Azure Active Directory B2C
description: Guia e exemplos sobre como acessar os logs de auditoria do Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: b1070314767f280ec9d15390dc838fa80b5508e2
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643610"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Acessando Azure AD B2C logs de auditoria

Azure Active Directory B2C (Azure AD B2C) emite logs de auditoria contendo informações de atividade sobre recursos B2C, tokens emitidos e acesso de administrador. Este artigo fornece uma breve visão geral das informações disponíveis em logs de auditoria e instruções sobre como acessar esses dados para seu locatário de Azure AD B2C.

Os eventos do log de auditoria são mantidos apenas por **sete dias**. Planeje baixar e armazenar seus logs usando um dos métodos mostrados abaixo se você precisar de um período de retenção maior.

> [!NOTE]
> Você não pode ver entradas de usuário para aplicativos de Azure AD B2C individuais na seção **usuários** das páginas **Azure Active Directory** ou **Azure ad B2C** no portal do Azure. Os eventos de entrada mostram a atividade do usuário, mas não podem ser correlacionados de volta ao aplicativo B2C no qual o usuário se conectou. Você deve usar os logs de auditoria para isso, conforme explicado mais adiante neste artigo.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Visão geral das atividades disponíveis na categoria B2C dos logs de auditoria

A categoria **B2C** nos logs de auditoria contém os seguintes tipos de atividades:

|Tipo de atividade |Descrição  |
|---------|---------|
|Autorização |Atividades relacionadas à autorização de um usuário para acessar os recursos do B2C (por exemplo, um administrador acessando uma lista de políticas do B2C).         |
|Diretório |Atividades relacionadas aos atributos de diretório recuperados quando um administrador entra usando o portal do Azure. |
|Aplicação | Operações CRUD (criar, ler, atualizar e excluir) em aplicativos B2C. |
|Chave |Operações CRUD em chaves armazenadas em um contêiner de chave B2C. |
|Recurso |Operações CRUD em recursos do B2C. Por exemplo, políticas e provedores de identidade.
|Autenticação |Validação de credenciais de usuário e emissão de tokens.|

Para as atividades CRUD do objeto de usuário, consulte a categoria **principal do diretório** .

## <a name="example-activity"></a>Atividade de exemplo

Esta imagem de exemplo da portal do Azure mostra os dados capturados quando um usuário entra com um provedor de identidade externo, neste caso, o Facebook:

![Exemplo de página de detalhes da atividade de log de auditoria no portal do Azure](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

O painel detalhes da atividade contém as seguintes informações relevantes:

|Section|Campo|Descrição|
|-------|-----|-----------|
| Atividade | Nome | Qual atividade ocorreu. Por exemplo, *emita um id_token para o aplicativo*, que conclui a entrada do usuário real. |
| Iniciado por (ator) | objectId | A **ID de objeto** do aplicativo B2C ao qual o usuário está se conectando. Esse identificador não é visível no portal do Azure, mas pode ser acessado por meio da API do Microsoft Graph. |
| Iniciado por (ator) | SPNs | A **ID do aplicativo** B2C para o qual o usuário está entrando. |
| Destino (s) | objectId | A **ID de objeto** do usuário que está entrando. |
| Detalhes Adicionais | tenantId | A **ID do locatário** do locatário do Azure ad B2C. |
| Detalhes Adicionais | policyId | A **ID de política** do fluxo do usuário (política) que está sendo usada para conectar o usuário. |
| Detalhes Adicionais | applicationId | A **ID do aplicativo** B2C para o qual o usuário está entrando. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Exibir logs de auditoria no portal do Azure

O portal do Azure fornece acesso aos eventos do log de auditoria em seu locatário do Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Alterne para o diretório que contém seu locatário Azure AD B2C e, em seguida, navegue até **Azure ad B2C**.
1. Em **atividades** no menu à esquerda, selecione **logs de auditoria**.

Uma lista de eventos de atividade registrados nos últimos sete dias é exibida.

![Filtro de exemplo com dois eventos de atividade no portal do Azure](media/active-directory-b2c-reference-audit-logs/audit-logs-example-filter.png)

Várias opções de filtragem estão disponíveis, incluindo:

* **Tipo de recurso de atividade** – filtre pelos tipos de atividade mostrados na tabela na seção [visão geral da atividades disponíveis](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) .
* **Data** – filtre o intervalo de datas das atividades mostradas.

Se você selecionar uma linha na lista, os detalhes da atividade do evento serão exibidos.

Para baixar a lista de eventos de atividade em um arquivo CSV (valores separados por vírgula), selecione **baixar**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Obter logs de auditoria com a API de relatórios do Azure AD

Os logs de auditoria são publicados no mesmo pipeline que outras atividades para Azure Active Directory, para que possam ser acessados por meio da [API de relatório Azure Active Directory](https://docs.microsoft.com/graph/api/directoryaudit-list). Para obter mais informações, consulte Introdução [à API de relatório do Azure Active Directory](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Habilitar o acesso à API de relatórios

Para permitir o acesso baseado em script ou aplicativo à API de relatórios do Azure AD, você precisa de um aplicativo Azure Active Directory registrado em seu locatário Azure AD B2C com as seguintes permissões de API:

* Microsoft Graph permissões de aplicativo > > AuditLog. Read. All

Você pode habilitar essas permissões em um registro de aplicativo Azure Active Directory existente em seu locatário B2C ou criar um novo especificamente para uso com a automação do log de auditoria.

Siga estas etapas para registrar um aplicativo, conceda a ele as permissões de API de Microsoft Graph necessárias e, em seguida, crie um segredo do cliente.

### <a name="register-application-in-azure-active-directory"></a>Registrar aplicativo no Azure Active Directory

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>Atribuir permissões de acesso à API

#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Na página Visão geral do **aplicativo registrado** , selecione **configurações**.
1. Em **acesso à API**, selecione **permissões necessárias**.
1. Selecione **Adicionar**e, em seguida, **Selecione uma API**.
1. Selecione **Microsoft Graph**e, em seguida, **selecione**.
1. Em **permissões do aplicativo**, selecione **ler todos os dados do log de auditoria**.
1. Selecione o botão **selecionar** e, em seguida, selecione **concluído**.
1. Selecione **conceder permissões**e, em seguida, selecione **Sim**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Em **gerenciar**, selecione **permissões de API**.
1. Em **permissões configuradas**, selecione **Adicionar uma permissão**.
1. Selecione a guia **APIs da Microsoft** .
1. Selecione **Microsoft Graph**.
1. Selecione **permissões de aplicativo**.
1. Expanda **auditlog** e marque a caixa de seleção **auditlog. Read. All** .
1. Selecione **adicionar permissões**. Conforme indicado, aguarde alguns minutos antes de prosseguir para a próxima etapa.
1. Selecione **conceder consentimento de administrador para (seu nome de locatário)** .
1. Selecione sua conta conectada no momento se ela tiver sido atribuída à função de *administrador global* ou entre com uma conta em seu locatário de Azure ad B2C que tenha sido atribuído à função de *administrador global* .
1. Selecione **Aceitar**.
1. Selecione **Atualizar**e, em seguida, verifique se "concedido para..." aparece em **status** para a permissão *AuditLog. Read. All* . Pode levar alguns minutos para que as permissões se propaguem.

* * *

### <a name="create-client-secret"></a>Criar segredo do cliente

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Agora você tem um aplicativo com o acesso necessário à API, uma ID do aplicativo e uma chave que você pode usar em seus scripts de automação. Consulte a seção script do PowerShell mais adiante neste artigo para obter um exemplo de como você pode obter eventos de atividade com um script.

### <a name="access-the-api"></a>Acessar a API

Para baixar Azure AD B2C eventos do log de auditoria por meio da API, filtre os logs na categoria `B2C`. Para filtrar por categoria, use o parâmetro de cadeia de caracteres de consulta `filter` ao chamar o ponto de extremidade da API de relatórios do Azure AD.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Script do PowerShell

O script do PowerShell a seguir mostra um exemplo de como consultar a API de relatórios do Azure AD. Depois de consultar a API, ela imprime os eventos registrados para a saída padrão e grava a saída JSON em um arquivo.

Você pode experimentar esse script na [Azure cloud Shell](../cloud-shell/overview.md). Certifique-se de atualizá-lo com a ID do aplicativo, o segredo do cliente e o nome do seu locatário de Azure AD B2C.

```powershell
# This script requires the registration of a Web Application in Azure Active Directory:
# https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant; for example, contoso.onmicrosoft.com
$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Aqui está a representação JSON do evento de atividade de exemplo mostrado anteriormente neste artigo:

```JSON
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Passos seguintes

Você pode automatizar outras tarefas de administração, por exemplo, [gerenciar usuários com o .net](active-directory-b2c-devquickstarts-graph-dotnet.md).
