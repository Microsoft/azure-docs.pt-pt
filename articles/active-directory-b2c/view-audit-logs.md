---
title: Registos de auditoria de acesso e revisão
titleSuffix: Azure AD B2C
description: Como aceder aos registos de auditoria Azure AD B2C programáticamente e no portal Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 83086fa2cb96eba423b9111134a0406d7256821f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185782"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Acesso a registos de auditoria Azure AD B2C

O Azure Ative Directory B2C (Azure AD B2C) emite registos de auditoria contendo informações de atividade sobre recursos B2C, fichas emitidas e acesso a administrador. Este artigo fornece uma breve visão geral das informações disponíveis nos registos de auditoria e instruções sobre como aceder a estes dados para o seu inquilino Azure AD B2C.

Os eventos de registo de auditoria só são retidos por **sete dias.** Planeie descarregar e armazenar os seus registos utilizando um dos métodos abaixo indicados se necessitar de um período de retenção mais longo.

> [!NOTE]
> Não é possível ver os sessão de inscrição dos utilizadores para aplicações individuais do Azure AD B2C no âmbito da secção **utilizadores** das páginas **Azure Ative Directory** ou **Azure AD B2C** no portal Azure. Os eventos de inscrição lá mostram a atividade do utilizador, mas não podem ser correlacionados de volta à aplicação B2C a que o utilizador assinou. Deve utilizar os registos de auditoria para isso, como explica do artigo.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Visão geral das atividades disponíveis na categoria B2C de registos de auditoria

A categoria **B2C** nos registos de auditoria contém os seguintes tipos de atividades:

|Tipo de atividade |Descrição  |
|---------|---------|
|Autorização |Atividades relativas à autorização de um utilizador para aceder aos recursos B2C (por exemplo, um administrador que aceda a uma lista de políticas B2C).         |
|Diretório |Atividades relacionadas com atributos de diretório recuperados quando um administrador assina na utilização do portal Azure. |
|Aplicação | Criar, ler, atualizar e eliminar operações (CRUD) em aplicações B2C. |
|Chave |Operações CRUD em chaves armazenadas num recipiente chave B2C. |
|Recurso |Operações crud em recursos B2C. Por exemplo, políticas e fornecedores de identidade.
|Autenticação |Validação das credenciais de utilizador e emissão de fichas.|

Para atividades CRUD de objeto de utilizador, consulte a categoria **Core Directy.**

## <a name="example-activity"></a>Atividade de exemplo

Esta imagem de exemplo do portal Azure mostra os dados capturados quando um utilizador assina com um fornecedor de identidade externo, neste caso, o Facebook:

![Exemplo de página de Detalhes de Atividade de Registo de Auditoria no portal Azure](./media/view-audit-logs/audit-logs-example.png)

O painel de detalhes da atividade contém as seguintes informações relevantes:

|Section|Campo|Descrição|
|-------|-----|-----------|
| Atividade | Nome | Que atividade teve lugar. Por exemplo, *emitir um id_token à aplicação*, que conclui o insessão do utilizador real. |
| Iniciado por (Ator) | Objectide | O ID do **objeto** da aplicação B2C a que o utilizador está a iniciar sessão. Este identificador não é visível no portal Azure, mas é acessível através da API do Microsoft Graph. |
| Iniciado por (Ator) | Spn | O ID de **aplicação** da aplicação B2C a que o utilizador está a iniciar sessão. |
| Alvo(s) | Objectide | O ID do **objeto** do utilizador que está a iniciar sessão. |
| Detalhes Adicionais | TenantId | A **identificação** do inquilino do inquilino Azure AD B2C. |
| Detalhes Adicionais | PolicyId | O ID de **política** do fluxo de utilizador (política) que está a ser utilizado para iniciar sessão do utilizador. |
| Detalhes Adicionais | ApplicationId | O ID de **aplicação** da aplicação B2C a que o utilizador está a iniciar sessão. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Ver registos de auditoria no portal Azure

O portal Azure dá acesso aos eventos de registo de auditoria no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Mude para o diretório que contém o seu inquilino Azure AD B2C e, em seguida, navegue até **Azure AD B2C**.
1. No âmbito **das Atividades** no menu esquerdo, selecione **registos de auditoria.**

Uma lista de eventos de atividade registados nos últimos sete dias é exibida.

![Filtro de exemplo com dois eventos de atividade no portal Azure](./media/view-audit-logs/audit-logs-example-filter.png)

Estão disponíveis várias opções de filtragem, incluindo:

* **Tipo de Recursos** de Atividade - Filtrar pelos tipos de atividade mostrados na tabela na [visão geral das atividades disponíveis.](#overview-of-activities-available-in-the-b2c-category-of-audit-logs)
* **Data** - Filtrar o intervalo de data das atividades mostradas.

Se selecionar uma linha na lista, os detalhes da atividade para o evento são apresentados.

Para descarregar a lista de eventos de atividade num ficheiro de valores separados pela vírlém (CSV), selecione **Download**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Obtenha registos de auditoria com a AD Azure reportando API

Os registos de auditoria são publicados no mesmo oleoduto que outras atividades para o Azure Ative Directory, para que possam ser acedidos através da API de [reporte de relatórios do Azure Ative Directory](https://docs.microsoft.com/graph/api/directoryaudit-list). Para mais informações, consulte [Iniciar com o Diretório Ativo Azure reportando API](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Ativar o acesso a API de reporte

Para permitir o acesso baseado em scripts ou aplicações à AD Azure reportando API, você precisa de uma aplicação registrada no seu inquilino Azure AD B2C com as seguintes permissões API. Pode ativar estas permissões num registo de candidatura existente dentro do seu inquilino B2C, ou criar uma nova especificamente para utilização com automatização de registos de auditoria.

* Microsoft Graph > Application permissions > AuditLog > AuditLog.Read.All

Siga os passos do seguinte artigo para registar um pedido com as permissões necessárias:

[Gerir o Azure AD B2C com o Microsoft Graph](microsoft-graph-get-started.md)

Depois de ter registado uma aplicação com as permissões apropriadas, consulte a secção de scripts PowerShell mais tarde neste artigo para um exemplo de como pode obter eventos de atividade com um script.

### <a name="access-the-api"></a>Acesso à API

Para baixar os eventos de registo de auditoria Azure AD B2C através da API, filtre os registos na categoria `B2C`. Para filtrar por categoria, utilize o parâmetro de cadeia de consulta `filter` quando ligar para o ponto final da API de reporte de API.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Script do PowerShell

O seguinte guião powerShell mostra um exemplo de como consultar a AD Azure reportando API. Depois de consultar a API, imprime os eventos registados para a saída padrão e, em seguida, escreve a saída JSON para um ficheiro.

Pode experimentar este guião na [Casca de Nuvem Azure.](overview.md) Certifique-se de atualizá-lo com o seu ID de aplicação, segredo de cliente e o nome do seu inquilino Azure AD B2C.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
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

Aqui está a representação da JSON do evento de atividade de exemplo mostrado anteriormente no artigo:

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

Pode automatizar outras tarefas de administração, por exemplo, gerir contas de [utilizadores Do Azure AD B2C com](manage-user-accounts-graph-api.md)o Microsoft Graph .
