---
title: Autenticação de serviço ao serviço com OAuth2.0 em nome do fluxo [ Microsoft Docs
description: Este artigo descreve como usar mensagens HTTP para implementar a autenticação serviço-a-serviço com o fluxo OAuth2.0 On-Behalf-Of.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 192c91f700dd82f453d52f6891f8aaaaeef8c7ef
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83642075"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Chamadas de serviço a serviço que utilizam a identidade de utilizador delegada no fluxo em nome

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O fluxo OAuth 2.0 On-Behalf-Of (OBO) permite que uma aplicação que invoque um serviço ou API web para passar a autenticação do utilizador para outro serviço ou API web. O fluxo OBO propaga a identidade de utilizador delegada e permissões através da cadeia de pedidos. Para que o serviço de nível médio evoque pedidos autenticados ao serviço a jusante, deve assegurar um sinal de acesso do Azure Ative Directory (Azure AD) em nome do utilizador.

> [!IMPORTANT]
> A partir de maio de 2018, não se pode usar um fluxo em `id_token` nome do em nome.  As aplicações de uma página única (SPAs) devem passar um sinal de acesso a um cliente confidencial de nível médio para executar fluxos OBO. Para mais detalhes sobre os clientes que podem realizar chamadas Em Nome-A-Favor, consulte [limitações](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>Diagrama de fluxo em nome

O fluxo OBO começa depois de o utilizador ter sido autenticado numa aplicação que utiliza o fluxo de concessão de código de [autorização OAuth 2.0](v1-protocols-oauth-code.md). Nessa altura, a aplicação envia um sinal de acesso (token A) à API (API A) de nível médio contendo as reclamações do utilizador e o consentimento para aceder à API A. Em seguida, a API A faz um pedido autenticado à API web a jusante (API B).

Estes passos constituem o fluxo em ![ nome:Mostra os passos no fluxo OAuth2.0 Em Nome-De-Behalf-Of](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. O pedido do cliente faz um pedido à API A com o símbolo A.
1. API A autentica o ponto final de emissão de fichas azure AD e solicita um sinal de acesso ao API B.
1. O ponto final de emissão de fichas Azure AD valida as credenciais da API A com token A e emite o sinal de acesso para API B (símbolo B).
1. O pedido à API B contém ficha B no cabeçalho de autorização.
1. APi B devolve dados do recurso seguro.

>[!NOTE]
>A reivindicação do público num sinal de acesso usado para solicitar um sinal de um serviço a jusante deve ser a identificação do serviço que faz o pedido da OBO. O símbolo também deve ser assinado com a chave de assinatura global do Diretório Ativo Azure (que é o padrão para aplicações registadas através de registos de **Aplicações** no portal).

## <a name="register-the-application-and-service-in-azure-ad"></a>Registe a aplicação e o serviço na Azure AD

Registe o serviço de nível médio e a aplicação do cliente na Azure AD.

### <a name="register-the-middle-tier-service"></a>Registe o serviço de nível médio

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na barra superior, selecione a sua conta e procure na lista **de Diretórios** para selecionar um inquilino de Diretório Ativo para a sua candidatura.
1. Selecione **Mais Serviços** no painel esquerdo e escolha **o Diretório Ativo Azure**.
1. Selecione **registos de aplicativos** e, em seguida, **novo registo**.
1. Introduza um nome amigável para a aplicação e selecione o tipo de aplicação.
1. Nos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .
1. Delineie o URI redirecionamento para o URL base.
1. Selecione **Registar** para criar a aplicação.
1. Gere um segredo de cliente antes de sair do portal Azure.
1. No portal Azure, escolha a sua aplicação e selecione **Certificados & segredos.**
1. Selecione **novo segredo de cliente** e adicione um segredo com uma duração de um ano ou dois anos.
1. Quando guarda esta página, o portal Azure mostra o valor secreto. Copie e guarde o valor secreto num local seguro.

> [!IMPORTANT]
> Precisa do segredo para configurar as definições de aplicação na sua implementação. Este valor secreto não é mostrado novamente, e não é recuperável por qualquer outro meio. Grave-o assim que estiver visível no portal Azure.

### <a name="register-the-client-application"></a>Registar o pedido de cliente

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na barra superior, selecione a sua conta e procure na lista **de Diretórios** para selecionar um inquilino de Diretório Ativo para a sua candidatura.
1. Selecione **Mais Serviços** no painel esquerdo e escolha **o Diretório Ativo Azure**.
1. Selecione **registos de aplicativos** e, em seguida, **novo registo**.
1. Introduza um nome amigável para a aplicação e selecione o tipo de aplicação.
1. Nos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .
1. Delineie o URI redirecionamento para o URL base.
1. Selecione **Registar** para criar a aplicação.
1. Configure permissões para a sua aplicação. Nas **permissões DaPI,** selecione **Adicionar uma permissão** e, em seguida, as minhas **APIs**.
1. Digite o nome do serviço de nível médio no campo de texto.
1. Escolha **selecionar Permissões** e, em seguida, selecione o nome do **serviço Access \<>**.

### <a name="configure-known-client-applications"></a>Configurar aplicações de clientes conhecidas

Neste cenário, o serviço de nível médio precisa de obter o consentimento do utilizador para aceder à API a jusante sem uma interação do utilizador. A opção de conceder acesso à API a jusante deve ser apresentada antecipadamente como parte da etapa de consentimento durante a autenticação.

Siga os passos abaixo para ligar explicitamente o registo da aplicação de clientes em Azure AD com o registo do serviço de nível médio. Esta operação une o consentimento exigido tanto pelo cliente como pelo meio num único diálogo.

1. Vá ao registo de serviço de nível médio e selecione **Manifesto** para abrir o editor manifesto.
1. Localize a propriedade da `knownClientApplications` matriz e adicione o ID do cliente da aplicação do cliente como um elemento.
1. Guarde o manifesto selecionando **Save**.

## <a name="service-to-service-access-token-request"></a>Pedido de acesso serviço ao serviço

Para solicitar um sinal de acesso, faça um POST HTTP ao ponto final do Azure AD específico do inquilino com os seguintes parâmetros:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

O pedido do cliente é garantido por um segredo partilhado ou por um certificado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Pedido de acesso com um segredo partilhado

Ao utilizar um segredo partilhado, um pedido de acesso serviço-a-serviço contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório | O tipo do pedido simbólico. Um pedido oBO utiliza um JSON Web Token (JWT) para que o valor seja **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| afirmação |obrigatório | O valor do token de acesso utilizado no pedido. |
| client_id |obrigatório | O ID da aplicação atribuído ao serviço de chamada durante o registo com a Azure AD. Para encontrar o ID da aplicação no portal Azure, selecione **Ative Directory,** escolha o diretório e, em seguida, selecione o nome da aplicação. |
| client_secret |obrigatório | A chave registada para o serviço de chamadas em Azure AD. Este valor deveria ter sido observado no momento da inscrição. |
| recurso |obrigatório | A aplicação ID URI do serviço recetor (recurso seguro). Para encontrar a aplicação ID URI no portal Azure, selecione **Ative Directy** e escolha o diretório. Selecione o nome da aplicação, escolha **Todas as definições,** e, em seguida, selecione **Propriedades**. |
| requested_token_use |obrigatório | Especifica como o pedido deve ser processado. No fluxo em nome, o valor deve ser **on_behalf_of**. |
| scope |obrigatório | Uma lista de espaços separadas de âmbitos para o pedido simbólico. Para o OpenID Connect, o âmbito **aberto** deve ser especificado.|

#### <a name="example"></a>Exemplo

O seguinte HTTP POST solicita um sinal de acesso para a https://graph.microsoft.com Web API. O `client_id` serviço identifica o serviço que solicita o sinal de acesso.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.ewogICJhdWQiOiAiaHR0cHM6Ly9ncmFwaC5taWNyb3NvZnQuY29tIiwKICAiaXNzIjogImh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLAogICJpYXQiOiAxNDkzNDIzMTY4LAogICJuYmYiOiAxNDkzNDIzMTY4LAogICJleHAiOiAxNDkzNDY2OTUxLAogICJhY3IiOiAiMSIsCiAgImFpbyI6ICJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsCiAgImFtciI6IFsKICAgICJwd2QiCiAgXSwKICAiYXBwaWQiOiAiNjI1MzkxYWYtYzY3NS00M2U1LThlNDQtZWRkM2UzMGNlYjE1IiwKICAiYXBwaWRhY3IiOiAiMSIsCiAgImVfZXhwIjogMzAyNjgzLAogICJmYW1pbHlfbmFtZSI6ICJUZXN0IiwKICAiZ2l2ZW5fbmFtZSI6ICJOYXZ5YSIsCiAgImlwYWRkciI6ICIxNjcuMjIwLjEuMTc3IiwKICAibmFtZSI6ICJOYXZ5YSBUZXN0IiwKICAib2lkIjogIjFjZDRiY2FjLWI4MDgtNDIzYS05ZTJmLTgyN2ZiYjFiYjczOSIsCiAgInBsYXRmIjogIjMiLAogICJwdWlkIjogIjEwMDMzRkZGQTEyRUQ3RkUiLAogICJzY3AiOiAiVXNlci5SZWFkIiwKICAic3ViIjogIjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLAogICJ0aWQiOiAiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwKICAidW5pcXVlX25hbWUiOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1cG4iOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1dGkiOiAieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsCiAgInZlciI6ICIxLjAiCn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Pedido de acesso aceso com certificado

Um pedido de acesso serviço-a-serviço com um certificado contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório | O tipo do pedido simbólico. Um pedido de OBO utiliza um símbolo de acesso JWT para que o valor seja **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| afirmação |obrigatório | O valor do símbolo utilizado no pedido. |
| client_id |obrigatório | O ID da aplicação atribuído ao serviço de chamada durante o registo com a Azure AD. Para encontrar o ID da aplicação no portal Azure, selecione **Ative Directory,** escolha o diretório e, em seguida, selecione o nome da aplicação. |
| client_assertion_type |obrigatório |O valor deve ser`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |obrigatório | Um Token Web JSON que cria e assina com o certificado que registou como credenciais para a sua aplicação. Consulte [as credenciais](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) de certificado para saber sobre o formato de afirmação e sobre como registar o seu certificado.|
| recurso |obrigatório | A aplicação ID URI do serviço recetor (recurso seguro). Para encontrar a aplicação ID URI no portal Azure, selecione **Ative Directy** e escolha o diretório. Selecione o nome da aplicação, escolha **Todas as definições,** e, em seguida, selecione **Propriedades**. |
| requested_token_use |obrigatório | Especifica como o pedido deve ser processado. No fluxo em nome, o valor deve ser **on_behalf_of**. |
| scope |obrigatório | Uma lista de espaços separadas de âmbitos para o pedido simbólico. Para o OpenID Connect, o âmbito **aberto** deve ser especificado.|

Estes parâmetros são quase os mesmos que com o pedido por segredo partilhado, exceto que o `client_secret parameter` é substituído por dois parâmetros: e `client_assertion_type` `client_assertion` .

#### <a name="example"></a>Exemplo

O seguinte HTTP POST solicita um sinal de acesso para a https://graph.microsoft.com Web API com um certificado. O `client_id` serviço identifica o serviço que solicita o sinal de acesso.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Resposta de acesso serviço ao serviço

Uma resposta de sucesso é uma resposta JSON OAuth 2.0 com os seguintes parâmetros:

| Parâmetro | Descrição |
| --- | --- |
| token_type |Indica o valor do tipo simbólico. O único tipo que a Azure AD suporta é **o Bearer.** Para obter mais informações sobre fichas ao portador, consulte o Quadro de [Autorização OAuth 2.0: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |O âmbito de acesso concedido no símbolo. |
| expires_in |O tempo de duração do token de acesso é válido (em segundos). |
| expires_on |O tempo em que o sinal de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até ao tempo de validade. Este valor é usado para determinar a vida útil de fichas em cache. |
| recurso |A aplicação ID URI do serviço recetor (recurso seguro). |
| access_token |O sinal de acesso solicitado. O serviço de chamada pode usar este símbolo para autenticar o serviço recetor. |
| id_token |O símbolo de identificação solicitado. O serviço de chamada pode usar este símbolo para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| refresh_token |O símbolo de atualização para o sinal de acesso solicitado. O serviço de chamada pode usar este token para solicitar outro sinal de acesso após o termo do presente sinal de acesso. |

### <a name="success-response-example"></a>Exemplo de resposta ao sucesso

O exemplo seguinte mostra uma resposta de sucesso a um pedido de acesso para a https://graph.microsoft.com Web API.

```json
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.microsoft.com",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Exemplo de resposta a erros

O ponto final do token Azure AD devolve uma resposta de erro quando tenta adquirir um símbolo de acesso para uma API a jusante que é definida com uma política de Acesso Condicional (por exemplo, autenticação de vários fatores). O serviço de nível médio deve apresentar este erro à aplicação do cliente para que a aplicação do cliente possa fornecer a interação do utilizador para satisfazer a política de Acesso Condicional.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Use o sinal de acesso para aceder ao recurso seguro

O serviço de nível médio pode utilizar o sinal de acesso adquirido para efazer pedidos autenticados para a API web a jusante, definindo o símbolo no `Authorization` cabeçalho.

### <a name="example"></a>Exemplo

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Afirmações saml obtidas com um fluxo OAuth2.0 OBO

Alguns serviços web baseados em OAuth precisam de aceder a outros APIs de serviço web que aceitam afirmações SAML em fluxos não interativos. O Azure Ative Directory pode fornecer uma afirmação SAML em resposta a um fluxo em nome que utiliza um serviço web baseado em SAML como recurso-alvo.

>[!NOTE]
>Esta é uma extensão não padrão do fluxo OAuth 2.0 On-Behalf-Of que permite uma aplicação baseada em OAuth2 para aceder a pontos finais do serviço web API que consomem tokens SAML.

> [!TIP]
> Quando liga para um serviço web protegido por SAML a partir de uma aplicação web frontal, pode simplesmente ligar para a API e iniciar um fluxo normal de autenticação interativa com a sessão existente do utilizador. Só precisa de utilizar um fluxo OBO quando uma chamada de serviço ao serviço requer um token SAML para fornecer contexto ao utilizador.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Obtenha um token SAML usando um pedido oBO com um segredo partilhado

Um pedido de serviço ao serviço para uma afirmação SAML contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório | O tipo do pedido simbólico. Para um pedido que utilize um JWT, o valor deve ser **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| afirmação |obrigatório | O valor do token de acesso utilizado no pedido.|
| client_id |obrigatório | O ID da aplicação atribuído ao serviço de chamada durante o registo com a Azure AD. Para encontrar o ID da aplicação no portal Azure, selecione **Ative Directory,** escolha o diretório e, em seguida, selecione o nome da aplicação. |
| client_secret |obrigatório | A chave registada para o serviço de chamadas em Azure AD. Este valor deveria ter sido observado no momento da inscrição. |
| recurso |obrigatório | A aplicação ID URI do serviço recetor (recurso seguro). Este é o recurso que será o símbolo do Público da SAML. Para encontrar a aplicação ID URI no portal Azure, selecione **Ative Directy** e escolha o diretório. Selecione o nome da aplicação, escolha **Todas as definições,** e, em seguida, selecione **Propriedades**. |
| requested_token_use |obrigatório | Especifica como o pedido deve ser processado. No fluxo em nome, o valor deve ser **on_behalf_of**. |
| requested_token_type | obrigatório | Especifica o tipo de ficha solicitada. O valor pode ser **urna:ietf:params:oauth:token-type:saml2** ou **urn:ietf:params:oauth:token-type:saml1** dependendo dos requisitos do recurso acessado. |

A resposta contém um símbolo SAML codificado em UTF8 e Base64url.

- **SubjectConfirmationData para uma afirmação SAML obtida a partir de uma chamada OBO**: Se a aplicação-alvo requer um valor destinatário em **Dados de Confirmação,** então o valor deve ser um URL de resposta não wildcard na configuração da aplicação de recursos.
- **O nó DeConfirmação do Sujeito**: O nó não pode conter um atributo **InResponseTo** uma vez que não faz parte de uma resposta SAML. O pedido que recebe o símbolo SAML deve poder aceitar a afirmação saml sem um atributo **InResponseTo.**

- **Consentimento**: O consentimento deve ter sido concedido para receber um símbolo SAML contendo dados do utilizador num fluxo OAuth. Para obter informações sobre permissões e obtenção do consentimento do administrador, consulte [Permissões e consentimento no Ponto final do Diretório Ativo Azure v1.0](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent).

### <a name="response-with-saml-assertion"></a>Resposta com afirmação SAML

| Parâmetro | Descrição |
| --- | --- |
| token_type |Indica o valor do tipo simbólico. O único tipo que a Azure AD suporta é **o Bearer.** Para obter mais informações sobre fichas ao portador, consulte [OAuth 2.0 Quadro de Autorização: Utilização do Token ao portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |O âmbito de acesso concedido no símbolo. |
| expires_in |O tempo de duração do token de acesso é válido (em segundos). |
| expires_on |O tempo em que o sinal de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até ao tempo de validade. Este valor é usado para determinar a vida útil de fichas em cache. |
| recurso |A aplicação ID URI do serviço recetor (recurso seguro). |
| access_token |O parâmetro que devolve a afirmação do SAML. |
| refresh_token |O símbolo refrescante. O serviço de chamada pode usar este token para solicitar outro sinal de acesso após a alegação atual do SAML expirar. |

- token_type: Portador
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- recurso:`https://api.contoso.com`
- access_token: \< Afirmação saml\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \< Ficha refrescante\>

## <a name="client-limitations"></a>Limitações de clientes

Clientes públicos com URLs de resposta wildcard não podem usar um `id_token` para fluxos OBO. No entanto, um cliente confidencial ainda pode resgatar fichas de **acesso** adquiridas através do fluxo de subvenção implícito, mesmo que o cliente público tenha um URI de redirecionamento wildcard registado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o protocolo OAuth 2.0 e outra forma de realizar a autenticação serviço-a-serviço que utiliza credenciais de cliente:

* [Serviço de autenticação de serviço utilizando subsídio de credenciais de cliente OAuth 2.0 em Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 em Azure AD](v1-protocols-oauth-code.md)
