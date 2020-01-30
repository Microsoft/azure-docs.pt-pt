---
title: Utilização que reporta amostras e definições de API
titleSuffix: Azure AD B2C
description: Guia e amostras sobre a obtenção de relatórios sobre os utilizadores de inquilinos Azure AD B2C, autenticações e autenticações de vários fatores.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b4486c1459024d60b53292f070dd565d20d10c52
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847735"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Aceder a relatórios de utilização em Azure AD B2C através da API reportando

O Diretório Ativo Azure B2C (Azure AD B2C) fornece a autenticação com base no início do utilizador e na autenticação multi-factor Azure. A autenticação é fornecida para utilizadores finais da sua família de aplicações através de fornecedores de identidade. Quando se sabe o número de utilizadores registados no arrendatário, os prestadores que costumavam registar-se e o número de autenticações por tipo, pode responder a questões como:
* Quantos utilizadores de cada tipo de fornecedor de identidade (por exemplo, uma conta Microsoft ou LinkedIn) registaram nos últimos 10 dias?
* Quantas autenticações utilizando a Autenticação Multi-Factor concluíram com sucesso no último mês?
* Quantas autenticações baseadas em inscrições foram concluídas este mês? Por dia? Por aplicação?
* Como posso estimar o custo mensal esperado da minha atividade de inquilino Sab2C Azure AD?

Este artigo centra-se em relatórios ligados à atividade de faturação, que se baseia no número de utilizadores, autenticações de entrada em faturação baseadas e autenticações multi-factor.


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, precisa completar os passos em [Pré-requisitos para aceder às APIs de reporte de AD Azure](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Crie uma candidatura, obtenha um segredo para o mesmo e conceda-lhe direitos de acesso aos relatórios do seu inquilino Azure AD B2C. *Os* exemplos do *guião* bash e python também são fornecidos aqui.

## <a name="powershell-script"></a>Script do PowerShell
Este script demonstra a criação de quatro relatórios de utilização utilizando o parâmetro `TimeStamp` e o filtro `ApplicationId`.

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


## <a name="usage-report-definitions"></a>Definições de relatório de utilização
* **inquilinoSUserCount**: O número de utilizadores no inquilino por tipo de fornecedor de identidade, por dia nos últimos 30 dias. (Opcionalmente, um filtro `TimeStamp` fornece contagens de utilizador desde uma data especificada até à data atual). O relatório fornece:
  * **TotalUserCount**: O número de todos os objetos do utilizador.
  * **OutrosUserCount**: O número de utilizadores do Diretório Ativo Azure (não utilizadores Azure AD B2C).
  * **LocalUserCount**: O número de contas de utilizadores Azure AD B2C criadas com credenciais locais para o inquilino Azure AD B2C.

* **AlternateIdUserCount**: O número de utilizadores do Azure AD B2C registados com fornecedores de identidade externa (por exemplo, Facebook, uma conta Microsoft, ou outro inquilino do Azure Ative Directory, também referido como um `OrgId`).

* **b2cAutenticaçãoCountSummary**: Resumo do número diário de autenticações faturadas ao longo dos últimos 30 dias, por dia e tipo de fluxo de autenticação.

* **b2cAutenticaçãoContagem**: O número de autenticações dentro de um período de tempo. O padrão é os últimos 30 dias.  (Opcional: Os parâmetros de início e fim `TimeStamp` definem um período de tempo específico.) A saída inclui `StartTimeStamp` (data mais precoce de atividade para este inquilino) e `EndTimeStamp` (última atualização).

* **b2cMfaRequestCountSummary**: Resumo do número diário de autenticações multifactor, de dia e tipo (SMS ou voz).


## <a name="limitations"></a>Limitações
Os dados da contagem do utilizador são atualizados a cada 24 a 48 horas. As autenticações são atualizadas várias vezes ao dia. Ao utilizar o filtro `ApplicationId`, uma resposta de relatório vazia pode ser devido a uma das seguintes condições:
  * O ID da aplicação não existe no inquilino. Certifique-se de que está correto.
  * O ID da aplicação existe, mas não foram encontrados dados no período de reporte. Reveja os seus parâmetros de data/hora.


## <a name="next-steps"></a>Passos seguintes
### <a name="monthly-bill-estimates-for-azure-ad"></a>Estimativas mensais de faturas para a AD Azure
Quando combinado com os preços mais atuais do [Azure AD B2C disponíveis,](https://azure.microsoft.com/pricing/details/active-directory-b2c/)pode estimar o consumo diário, semanal e mensal do Azure.  Uma estimativa é especialmente útil quando se planeia mudanças no comportamento dos inquilinos que podem ter impacto no custo global. Pode rever os custos reais na [subscrição do Azure ligado.](billing.md)

### <a name="options-for-other-output-formats"></a>Opções para outros formatos de saída
O seguinte código mostra exemplos de envio de saída para jSON, uma lista de valor de nome, e XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
