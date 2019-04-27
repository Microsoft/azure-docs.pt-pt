---
title: Exemplos de API relatórios de utilização e as definições no Azure Active Directory B2C | Documentos da Microsoft
description: Guia e exemplos sobre como obter relatórios no inquilino do Azure AD B2C, os utilizadores, as autenticações e autenticações multi-factor Authentication.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5ccd9ea36fcf20c56684ac5892a7fa32e0345633
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316855"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Aceder a relatórios de utilização no Azure AD B2C através da API de geração de relatórios

O Azure Active Directory B2C (Azure AD B2C) fornece autenticação com base em sessão do utilizador e o Azure multi-factor Authentication. Autenticação é fornecida para os utilizadores finais da sua família de aplicativo entre fornecedores de identidade. Quando sabe o número de utilizadores registado no inquilino, os fornecedores que são utilizadas para registar e o número de autenticações por tipo, pode responder a perguntas como:
* O número de utilizadores de cada tipo de fornecedor de identidade (por exemplo, uma conta Microsoft ou LinkedIn) tê registado nos últimos 10 dias?
* Quantos autenticações com multi-factor Authentication foram concluídas com êxito no último mês?
* Autenticações de início de sessão no-com base em quantos foram concluídas deste mês? Por dia? Por aplicação?
* Como posso estimar o custo mensal esperado da minha atividade de inquilino do Azure AD B2C?

Este artigo se concentra em relatórios vinculados a atividade de faturação, o que é baseada no número de utilizadores, autenticações de início de sessão no-com base em cobrar e autenticações multi-factor Authentication.


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, terá de executar os passos descritos [pré-requisitos para aceder a APIs de relatórios do Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Criar uma aplicação, obter um segredo para ele e conceder acesso direitos para relatórios do seu inquilino do Azure AD B2C. *Script de bash* e *script de Python* também são fornecidos exemplos aqui. 

## <a name="powershell-script"></a>Script do PowerShell
Este script demonstra a criação de relatórios de utilização de quatro, utilizando o `TimeStamp` parâmetro e o `ApplicationId` filtro.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Definições de relatórios de utilização
* **tenantUserCount**: O número de utilizadores no inquilino por tipo de fornecedor de identidade, por dia nos últimos 30 dias. (Opcionalmente, um `TimeStamp` filtro fornece contagens de utilizadores a partir de uma data especificada para a data atual). O relatório fornece:
  * **TotalUserCount**: O número de todos os objetos de utilizador.
  * **OtherUserCount**: O número de utilizadores do Azure Active Directory (não os utilizadores do Azure AD B2C).
  * **LocalUserCount**: O número de contas de utilizador do Azure AD B2C criado com as credenciais locais para o inquilino do Azure AD B2C.

* **AlternateIdUserCount**: O número de utilizadores do Azure AD B2C registado com fornecedores de identidade externo (por exemplo, Facebook, uma conta Microsoft ou outro inquilino do Azure Active Directory, também conhecido como um `OrgId`).

* **b2cAuthenticationCountSummary**: Resumo do número diário de autenticações cobrar nos últimos 30 dias, por dia e o tipo de fluxo de autenticação.

* **b2cAuthenticationCount**: O número de autenticações num período de tempo. A predefinição é 30 últimos dias.  (Opcional: O início e fim `TimeStamp` parâmetros definem um período de tempo específico.) A saída inclui `StartTimeStamp` (data mais antiga da atividade para este inquilino) e `EndTimeStamp` (atualização mais recente).

* **b2cMfaRequestCountSummary**: Resumo do número diário de autenticações multifatores, por dia e o tipo (SMS ou voz).


## <a name="limitations"></a>Limitações
Dados de contagem de utilizador são atualizados a cada 24 para 48 horas. Autenticações são atualizadas várias vezes ao dia. Ao utilizar o `ApplicationId` filtro, uma resposta de relatório vazio pode ser devido a uma das seguintes condições:
  * O ID da aplicação não existe no inquilino. Certifique-se de que está correto.
  * O ID de aplicação existe, mas não foram encontrados dados no período de criação de relatórios. Reveja os parâmetros de data/hora.


## <a name="next-steps"></a>Passos Seguintes
### <a name="monthly-bill-estimates-for-azure-ad"></a>As estimativas de fatura mensal do Azure AD
Quando combinado com [a mais recente do Azure AD B2C estão disponíveis preços](https://azure.microsoft.com/pricing/details/active-directory-b2c/), pode fazer uma estimativa diárias, semanal e mensal de consumo do Azure.  Uma estimativa é especialmente útil quando planear de alterações de comportamento de inquilino que podem afetar o custo geral. Pode rever os custos reais no seu [subscrição do Azure associada](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Opções para outros formatos de saída
O código seguinte mostra exemplos de enviar a saída para JSON, uma lista de valores de nome e XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
