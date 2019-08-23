---
title: Exemplos de logs de auditoria e definições no Azure Active Directory B2C | Microsoft Docs
description: Guia e exemplos sobre como acessar os logs de auditoria do Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d8cc67b8e243fb2b97cd1522a850adc63c84428e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69969636"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Acessando Azure AD B2C logs de auditoria

Azure Active Directory B2C (Azure AD B2C) emite logs de auditoria contendo informações de atividade sobre recursos do B2C, tokens emitidos e acesso de administrador. Este artigo fornece uma breve visão geral das informações disponíveis por meio de logs de auditoria e instruções sobre como acessar esses dados para seu locatário de Azure AD B2C.

> [!IMPORTANT]
> Os logs de auditoria são mantidos apenas por sete dias. Planeje baixar e armazenar seus logs usando um dos métodos mostrados abaixo se você precisar de um período de retenção maior.

> [!NOTE]
> Você não pode ver entradas de usuário para aplicativos de Azure AD B2C individuais na seção **usuários** das folhas de **Azure Active Directory** ou **Azure ad B2C** . As entradas exibirão a atividade do usuário, mas não poderão ser correlacionadas ao aplicativo B2C ao qual o usuário se conectou. Você deve usar os logs de auditoria para isso, conforme explicado mais adiante neste artigo.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Visão geral das atividades disponíveis na categoria B2C dos logs de auditoria
A categoria **B2C** nos logs de auditoria contém os seguintes tipos de atividades:

|Tipo de atividade |Descrição  |
|---------|---------|
|Autorização |Atividades relacionadas à autorização de um usuário para acessar os recursos do B2C (por exemplo, um administrador acessando uma lista de políticas B2C)         |
|Diretório |Atividades relacionadas a atributos de diretório recuperados quando um administrador entra usando o portal do Azure |
|Aplicação | Operações CRUD em aplicativos B2C |
|Chave |Operações CRUD em chaves armazenadas no contêiner de chave B2C |
|Resource |Operações CRUD em recursos do B2C (por exemplo, políticas e provedores de identidade)
|Authentication |Validação de credenciais de usuário e emissão de token|

> [!NOTE]
> Para as atividades CRUD do objeto de usuário, consulte a categoria **principal do diretório** .

## <a name="example-activity"></a>Atividade de exemplo
O exemplo a seguir mostra os dados capturados quando um usuário entra com um provedor de identidade externo:  
    ![Exemplo de página de detalhes da atividade de log de auditoria no portal do Azure](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

O painel detalhes da atividade contém as seguintes informações relevantes:

|Section|Campo|Descrição|
|-------|-----|-----------|
| Atividade | Name | Qual atividade ocorreu. Por exemplo, "emitir um id_token para o aplicativo" (que conclui a entrada do usuário real). |
| Iniciado por (ator) | ObjectId | A **ID de objeto** do aplicativo B2C ao qual o usuário está se conectando (esse identificador não é visível na portal do Azure, mas está acessível por meio do API do Graph, por exemplo). |
| Iniciado por (ator) | Spn | A **ID do aplicativo** B2C para o qual o usuário está entrando. |
| Destino (s) | ObjectId | A **ID de objeto** do usuário que está entrando. |
| Detalhes Adicionais | TenantId | A **ID do locatário** do locatário do Azure ad B2C. |
| Detalhes Adicionais | `PolicyId` | A **ID de política** do fluxo do usuário (política) que está sendo usada para conectar o usuário. |
| Detalhes Adicionais | ApplicationId | A **ID do aplicativo** B2C para o qual o usuário está entrando. |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Acessando logs de auditoria por meio do portal do Azure
1. Aceda ao [Portal do Azure](https://portal.azure.com). Verifique se você está no diretório B2C.
2. Clique em **Azure Active Directory** na barra favoritos à esquerda

    ![Botão de Azure Active Directory realçado no menu do portal esquerdo](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. Em **atividade**, clique em **logs de auditoria**

    ![Botão logs de auditoria realçado na seção atividade do menu](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. Na **categoria** Dropbox, selecione **B2C**
3. Clique em **aplicar**

    ![Botão categoria e aplicar realçado no filtro log de auditoria](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Você verá uma lista de atividades registradas nos últimos sete dias.
- Use a lista suspensa **tipo de recurso de atividade** para filtrar pelos tipos de atividade descritos acima
- Use a lista suspensa **intervalo de datas** para filtrar o intervalo de datas das atividades mostradas
- Se você clicar em uma linha específica na lista, uma caixa contextual à direita mostrará os atributos adicionais associados à atividade
- Clique em **baixar** para baixar as atividades como um arquivo CSV

> [!NOTE]
> Você também pode ver os logs de auditoria navegando até **Azure ad B2C** em vez de **Azure Active Directory** na barra de favoritos à esquerda. Em **atividades**, clique em **logs de auditoria**, onde você encontrará os mesmos logs com recursos de filtragem semelhantes.

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Acessando logs de auditoria por meio da API de relatórios do Azure AD
Os logs de auditoria são publicados no mesmo pipeline que outras atividades para Azure Active Directory, para que possam ser acessados por meio da [API de relatório Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference).

### <a name="prerequisites"></a>Pré-requisitos
Para se autenticar na API de relatório do Azure AD, primeiro você precisa registrar um aplicativo. Certifique-se de seguir as etapas em [pré-requisitos para acessar as APIs de relatório do Azure ad](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

### <a name="accessing-the-api"></a>Acessando a API
Para baixar os Azure AD B2C logs de auditoria por meio da API, convém filtrar os logs para a categoria **B2C** . Para filtrar por categoria, use o parâmetro de cadeia de caracteres de consulta ao chamar o ponto de extremidade da API de relatório do Azure AD, conforme mostrado abaixo:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>Script do PowerShell
O script a seguir fornece um exemplo de como usar o PowerShell para consultar a API de relatórios do Azure AD e armazenar os resultados como um arquivo JSON:

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago

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
