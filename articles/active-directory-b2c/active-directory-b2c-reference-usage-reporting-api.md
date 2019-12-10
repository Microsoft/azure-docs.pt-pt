---
title: Exemplos e definições da API de relatório de uso
titleSuffix: Azure AD B2C
description: Guia e exemplos sobre como obter relatórios sobre usuários Azure AD B2C locatários, autenticações e autenticações multifator.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f81acf28b502965f896cd8b38767e7c2e925156c
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949343"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Acessando relatórios de uso em Azure AD B2C por meio da API de relatórios

O Azure Active Directory B2C (Azure AD B2C) fornece autenticação com base na entrada do usuário e na autenticação multifator do Azure. A autenticação é fornecida para usuários finais da família de aplicativos em provedores de identidade. Quando você sabe o número de usuários registrados no locatário, os provedores que eles usaram para se registrar e o número de autenticações por tipo, você pode responder a perguntas como:
* Quantos usuários de cada tipo de provedor de identidade (por exemplo, uma conta da Microsoft ou do LinkedIn) se registraram nos últimos 10 dias?
* Quantas autenticações que usam a autenticação multifator foram concluídas com êxito no último mês?
* Quantas autenticações baseadas em entrada foram concluídas neste mês? Por dia? Por aplicativo?
* Como posso estimar o custo mensal esperado de minha atividade de locatário do Azure AD B2C?

Este artigo se concentra em relatórios vinculados à atividade de cobrança, que se baseia no número de usuários, autenticações faturáveis baseadas em entrada e autenticações multifator.


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisa concluir as etapas em [pré-requisitos para acessar as APIs de relatório do Azure ad](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Crie um aplicativo, obtenha um segredo para ele e conceda a ele direitos de acesso a seus relatórios de Azure AD B2C locatário. *Scripts bash* e exemplos de *script Python* também são fornecidos aqui.

## <a name="powershell-script"></a>Script do PowerShell
Esse script demonstra a criação de quatro relatórios de uso usando o parâmetro `TimeStamp` e o filtro `ApplicationId`.

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


## <a name="usage-report-definitions"></a>Definições de relatório de uso
* **tenantUserCount**: o número de usuários no locatário por tipo de provedor de identidade, por dia nos últimos 30 dias. (Opcionalmente, um filtro de `TimeStamp` fornece contagens de usuário de uma data especificada para a data atual). O relatório fornece:
  * **TotalUserCount**: o número de todos os objetos de usuário.
  * **OtherUserCount**: o número de usuários Azure Active Directory (não Azure ad B2C usuários).
  * **LocalUserCount**: o número de contas de usuário Azure ad B2C criadas com credenciais locais para o locatário Azure ad B2C.

* **AlternateIdUserCount**: o número de Azure ad B2C usuários registrados com provedores de identidade externos (por exemplo, Facebook, um conta Microsoft ou outro locatário Azure Active Directory, também conhecido como um `OrgId`).

* **b2cAuthenticationCountSummary**: Resumo do número diário de autenticações faturáveis nos últimos 30 dias, por dia e tipo de fluxo de autenticação.

* **b2cAuthenticationCount**: o número de autenticações em um período de tempo. O padrão é os últimos 30 dias.  (Opcional: os parâmetros de `TimeStamp` inicial e final definem um período de tempo específico.) A saída inclui `StartTimeStamp` (primeira data de atividade para esse locatário) e `EndTimeStamp` (atualização mais recente).

* **b2cMfaRequestCountSummary**: Resumo do número diário de autenticações multifator, por dia e tipo (SMS ou voz).


## <a name="limitations"></a>Limitações
Os dados de contagem de usuários são atualizados a cada 24 a 48 horas. As autenticações são atualizadas várias vezes por dia. Ao usar o filtro de `ApplicationId`, uma resposta de relatório vazia pode ser devido a uma das seguintes condições:
  * A ID do aplicativo não existe no locatário. Verifique se está correto.
  * A ID do aplicativo existe, mas nenhum dado foi encontrado no período de relatório. Examine os parâmetros de data/hora.


## <a name="next-steps"></a>Passos seguintes
### <a name="monthly-bill-estimates-for-azure-ad"></a>Estimativas de cobrança mensal para o Azure AD
Quando combinado com [os preços de Azure ad B2C mais atuais disponíveis](https://azure.microsoft.com/pricing/details/active-directory-b2c/), você pode estimar o consumo diário, semanal e mensal do Azure.  Uma estimativa é especialmente útil quando você planeja alterações no comportamento do locatário que podem afetar o custo geral. Você pode examinar os custos reais em sua [assinatura do Azure vinculada](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Opções para outros formatos de saída
O código a seguir mostra exemplos de envio de saída para JSON, uma lista de valores de nome e XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
