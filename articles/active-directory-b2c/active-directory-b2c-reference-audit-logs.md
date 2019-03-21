---
title: Registos de auditoria exemplos e definições no Azure Active Directory B2C | Documentos da Microsoft
description: Guia e exemplos sobre como acessar os registos de auditoria do Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 117d58f6f56fe1b24539c7bfe950f1a23d6de51a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003593"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Registos de auditoria do acesso ao Azure AD B2C

O Azure Active Directory B2C (Azure AD B2C) emite os registos de auditoria que contém informações de atividade sobre os recursos de B2C, emitidos tokens e acesso de administrador. Este artigo fornece uma breve descrição geral das informações disponíveis por meio de registos de auditoria e instruções sobre como aceder a estes dados para o seu inquilino do Azure AD B2C.

> [!IMPORTANT]
> Registos de auditoria só são mantidos durante sete dias. Plano para transferir e armazenar os registos através de um dos métodos abaixo se necessitar de um período de retenção mais longo.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Descrição geral das atividades disponíveis na categoria de B2C de registos de auditoria
O **B2C** categoria nos registos de auditoria contém os seguintes tipos de atividades:

|Tipo de atividade |Descrição  |
|---------|---------|
|Autorização |Atividades relacionadas a autorização de um utilizador aos recursos de acesso B2C (por exemplo, um administrador aceder a uma lista das políticas de B2C)         |
|Diretório |Atividades relacionadas com os atributos de diretório obtidos quando um administrador inicia sessão no Portal do Azure |
|Aplicação | Operações CRUD em aplicações B2C |
|Chave |Operações CRUD em chaves armazenadas no contentor de chaves do B2C |
|Recurso |Operações de CRUD nos recursos de B2C (por exemplo, políticas e fornecedores de identidade)
|Authentication |Validação de credenciais de utilizador e a emissão de token|

> [!NOTE]
> Para atividades CRUD do objeto de utilizador, consulte a **diretório principal** categoria.

## <a name="example-activity"></a>Atividade de exemplo
O exemplo abaixo mostra os dados capturados quando um utilizador inicia sessão com um fornecedor de identidade externo: ![Registos de auditoria - exemplo](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Aceder a registos de auditoria através do Portal do Azure
1. Aceda ao [Portal do Azure](https://portal.azure.com). Certifique-se de que está no diretório do B2C.
2. Clique em **do Azure Active Directory** na barra de Favoritos à esquerda
    
    ![Registos de auditoria - botão AAD](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. Sob **atividade**, clique em **registos de auditoria**

    ![Registos de auditoria - secção de registos](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. Na **categoria** dropbox, selecione **B2C**
3. Clique em **aplicam-se**

    ![Registos de auditoria - categoria](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Verá uma lista de atividades iniciadas durante os últimos sete dias.
- Utilize o **tipo de recurso de atividade** lista pendente para filtrar pelos tipos de atividade descritos acima
- Utilize o **intervalo de datas** lista pendente para filtrar o intervalo de datas das atividades mostradas
- Se clicar numa linha específica na lista, uma caixa contextual no lado direito irá mostrar-lhe atributos adicionais associados a atividade
- Clique em **transferir** para transferir as atividades como um ficheiro csv

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Aceder a registos de auditoria por meio da API de relatórios do Azure AD
Registos de auditoria são publicados no mesmo pipeline como outras atividades do Azure Active Directory, para que possam ser acedidos através da [do Azure Active Directory reporting API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference).

### <a name="prerequisites"></a>Pré-requisitos
Para autenticar com o Azure AD, API de relatórios tem primeiro de registar uma aplicação. Lembre-se de que siga os passos em [pré-requisitos para aceder a APIs de relatórios do Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

### <a name="accessing-the-api"></a>Aceder à API
Para transferir os registos de auditoria do Azure AD B2C através da API, vai querer filtrar os registos para o **B2C** categoria. Para filtrar por categoria, utilize o parâmetro de cadeia de caracteres de consulta ao chamar ponto final de API de relatórios do Azure AD, conforme mostrado abaixo:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>Script do PowerShell
O script seguinte fornece um exemplo de utilização do PowerShell para consultar a API de relatórios do Azure AD e armazenar os resultados como um ficheiro JSON:

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
