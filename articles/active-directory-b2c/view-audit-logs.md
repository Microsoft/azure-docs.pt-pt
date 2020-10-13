---
title: Aceder e rever registos de auditoria
titleSuffix: Azure AD B2C
description: Como aceder aos registos de auditoria do Azure AD B2C programático e no portal Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 4fc25edb873a2dfe84f6ca716a71cf028c74cb2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85383942"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Aceder aos registos de auditoria do Azure AD B2C

O Azure Ative Directory B2C (Azure AD B2C) emite registos de auditoria contendo informações de atividade sobre recursos B2C, fichas emitidas e acesso ao administrador. Este artigo fornece uma breve visão geral das informações disponíveis nos registos de auditoria e instruções sobre como aceder a estes dados para o seu inquilino Azure AD B2C.

Os eventos de registo de auditoria só são mantidos por **sete dias**. Planeie descarregar e armazenar os seus registos utilizando um dos métodos apresentados abaixo se necessitar de um período de retenção mais longo.

> [!NOTE]
> Não é possível ver as entradas de utilizador para aplicações individuais Azure AD B2C na secção **Utilizadores** das páginas **Azure Ative Directory** ou **Azure AD B2C** no portal Azure. Os eventos de inscrição mostram a atividade do utilizador, mas não podem ser correlacionados com a aplicação B2C a que o utilizador assinou. Deve utilizar os registos de auditoria para isso, como explicado ainda neste artigo.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Panorâmica das atividades disponíveis na categoria B2C de registos de auditoria

A categoria **B2C** nos registos de auditoria contém os seguintes tipos de atividades:

|Tipo de atividade |Descrição  |
|---------|---------|
|Autorização |Atividades relativas à autorização de um utilizador para aceder aos recursos B2C (por exemplo, um administrador que aceda a uma lista de políticas B2C).         |
|Diretório |Atividades relacionadas com atributos de diretório recuperados quando um administrador assina na utilização do portal Azure. |
|Aplicação | Criar, ler, atualizar e eliminar operações (CRUD) em aplicações B2C. |
|Chave |Operações CRUD em chaves armazenadas num recipiente de chave B2C. |
|Recurso |Operações CRUD em recursos B2C. Por exemplo, políticas e fornecedores de identidade.
|Autenticação |Validação de credenciais de utilizador e emissão simbólica.|

Para atividades CRUD de objeto de utilizador, consulte a categoria **Core Directory.**

## <a name="example-activity"></a>Exemplo atividade

Esta imagem de exemplo do portal Azure mostra os dados capturados quando um utilizador assina com um fornecedor de identidade externo, neste caso, o Facebook:

![Exemplo da página detalhes da atividade do registo de auditoria no portal Azure](./media/view-audit-logs/audit-logs-example.png)

O painel de detalhes da atividade contém as seguintes informações relevantes:

|Section|Campo|Descrição|
|-------|-----|-----------|
| Atividade | Nome | Que atividade aconteceu. Por exemplo, *emita um id_token à aplicação*, que conclui o real pedido de inscrição do utilizador. |
| Iniciado por (Ator) | ObjectId | O **ID** do Objeto da aplicação B2C a que o utilizador está a iniciar sessão. Este identificador não é visível no portal Azure, mas é acessível através da API do Microsoft Graph. |
| Iniciado por (Ator) | Spn | O ID de **aplicação** da aplicação B2C a que o utilizador está a iniciar sessão. |
| Destino(s) | ObjectId | O **ID do objeto** do utilizador que está a iniciar sessão. |
| Detalhes Adicionais | TenantId | A **identificação** do inquilino do Azure AD B2C. |
| Detalhes Adicionais | PolicyId | O **ID** de política do fluxo de utilizador (política) que está a ser utilizado para iniciar a inscrição do utilizador. |
| Detalhes Adicionais | ApplicationID | O ID de **aplicação** da aplicação B2C a que o utilizador está a iniciar sessão. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Ver registos de auditoria no portal Azure

O portal Azure fornece acesso aos eventos de registo de auditoria no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Mude para o diretório que contém o seu inquilino Azure AD B2C e, em seguida, navegue para **Azure AD B2C**.
1. Em **Atividades** no menu esquerdo, selecione **registos de auditoria**.

É apresentada uma lista de eventos de atividade registados nos últimos sete dias.

![Filtro de exemplo com dois eventos de atividade no portal Azure](./media/view-audit-logs/audit-logs-example-filter.png)

Estão disponíveis várias opções de filtragem, incluindo:

* **Tipo de Recurso de Atividade** - Filtrar pelos tipos de atividade indicados na tabela na secção Geral das [atividades disponíveis.](#overview-of-activities-available-in-the-b2c-category-of-audit-logs)
* **Data** - Filtrar o intervalo de datas das atividades apresentadas.

Se selecionar uma linha na lista, os detalhes da atividade do evento são apresentados.

Para descarregar a lista de eventos de atividade num ficheiro de valores separados por vírgula (CSV), selecione **Download**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Obtenha registos de auditoria com a AZure AD reportando API

Os registos de auditoria são publicados no mesmo oleoduto que outras atividades do Azure Ative Directory, para que possam ser acedidos através do [Azure Ative Directory reportando API](https://docs.microsoft.com/graph/api/directoryaudit-list). Para obter mais informações, consulte [Começar com o Diretório Ativo Azure reportando API](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Permitir reportar acesso a API

Para permitir o acesso baseado em scripts ou aplicações à API de reporte de Azure AD, precisa de uma aplicação registada no seu inquilino Azure AD B2C com as seguintes permissões API. Pode ativar estas permissões num registo de aplicação existente dentro do seu inquilino B2C ou criar um novo especificamente para uso com automatização de registos de auditoria.

* Microsoft Graph > Permissões de aplicação > AuditLog > AuditLog.Read.All

Siga as etapas do seguinte artigo para registar um pedido com as permissões necessárias:

[Gerir Azure AD B2C com o Microsoft Graph](microsoft-graph-get-started.md)

Depois de ter registado uma aplicação com as permissões apropriadas, consulte a secção de scripts PowerShell mais tarde neste artigo para um exemplo de como pode obter eventos de atividade com um script.

### <a name="access-the-api"></a>Aceder à API

Para descarregar os eventos de registo de auditoria Azure AD B2C através da API, filtre os registos na `B2C` categoria. Para filtrar por categoria, utilize o parâmetro de `filter` cadeia de consulta quando ligar para o ponto final da API que reporta AZure.

```http
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Script do PowerShell

O seguinte script PowerShell mostra um exemplo de como consultar a API de relatório a azure. Depois de consultar a API, imprime os eventos registados para a saída padrão e, em seguida, escreve a saída JSON para um ficheiro.

Pode experimentar este script na [Azure Cloud Shell.](overview.md) Certifique-se de atualizá-lo com o seu ID de aplicação, segredo de cliente, e o nome do seu inquilino Azure AD B2C.

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

Aqui está a representação JSON do evento de atividades exemplo mostrado anteriormente no artigo:

```json
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

Pode automatizar outras tarefas de administração, por exemplo, [gerir contas de utilizadores Azure AD B2C com o Microsoft Graph](manage-user-accounts-graph-api.md).
