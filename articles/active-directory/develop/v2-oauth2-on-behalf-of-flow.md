---
title: Plataforma de identidade Microsoft e OAuth2.0 Em nome do fluxo [ Azure
description: Este artigo descreve como usar mensagens HTTP para implementar o serviço de autenticação de serviço utilizando o fluxo OAuth2.0 On-Behalf-Of.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3d3e071d5f2f181f5b17e79f2f1097394d0ebaf3
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868429"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Plataforma de identidade da Microsoft e OAuth 2.0 Em Nome do Fluxo


O OAuth 2.0 On-Behalf-Of flow (OBO) serve o caso de utilização em que uma aplicação invoca uma API de serviço/web, que por sua vez precisa de chamar outro serviço/Web API. A ideia é propagar a identidade e permissões de utilizador delegadas através da cadeia de pedidos. Para que o serviço de nível médio faça pedidos autenticados ao serviço a jusante, este precisa de garantir um sinal de acesso da plataforma de identidade da Microsoft, em nome do utilizador.

Este artigo descreve como programar diretamente contra o protocolo na sua aplicação.  Sempre que possível, recomendamos que utilize as Bibliotecas de Autenticação da Microsoft (MSAL) suportadas em vez de adquirir fichas e ligar para [APIs web protegidos](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Veja também as [aplicações de amostra que utilizam o MSAL.](sample-v2-code.md)

> [!NOTE]
> A partir de maio de 2018, alguns fluxos implícitos derivados `id_token` não podem ser usados para o fluxo de OBO. As aplicações de uma página única (SPAs) devem passar um sinal de **acesso** a um cliente confidencial de nível médio para executar os fluxos oBO. Para obter mais informações sobre quais os clientes que podem realizar chamadas OBO, consulte [limitações](#client-limitations).

## <a name="protocol-diagram"></a>Diagrama de protocolo

Assuma que o utilizador foi autenticado numa aplicação utilizando o fluxo de concessão de código de [autorização OAuth 2.0](v2-oauth2-auth-code-flow.md) ou outro fluxo de login. Neste ponto, a aplicação tem um sinal de acesso *para API A* (token A) com as reclamações do utilizador e o consentimento para aceder à API web de nível médio (API A). Agora, a API A precisa de fazer um pedido autenticado à Web API (API B).

Os passos que se seguem constituem o fluxo obo e são explicados com a ajuda do seguinte diagrama.

![Mostra o fluxo OAuth2.0 On-Behalf-Of](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. O pedido do cliente faz um pedido à API A com token A (com uma `aud` reclamação de API A).
1. AAPI A autentica a plataforma de identidade da Microsoft token endpoint e solicita um sinal para aceder ao API B.
1. O ponto final da plataforma de identidade da Microsoft valida as credenciais da API A juntamente com o token A e emite o sinal de acesso para API B (token B) para API A.
1. O token B é definido pela API A no cabeçalho de autorização do pedido à API B.
1. Os dados do recurso seguro são devolvidos pela API B à API A, e daí para o cliente.

> [!NOTE]
> Neste cenário, o serviço de nível médio não tem interação do utilizador para obter o consentimento do utilizador para aceder à API a jusante. Por isso, a opção de conceder acesso à API a jusante é apresentada antecipadamente como parte do passo de consentimento durante a autenticação. Para aprender a configurar isto para a sua aplicação, consulte [o consentimento para a aplicação de nível médio](#gaining-consent-for-the-middle-tier-application).

## <a name="service-to-service-access-token-request"></a>Pedido de acesso serviço ao serviço

Para solicitar um token de acesso, faça um HTTP POST para a plataforma de identidade específica do inquilino Microsoft token endpoint com os seguintes parâmetros.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Existem dois casos dependendo se a aplicação do cliente opta por ser assegurada por um segredo partilhado ou um certificado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Pedido de acesso com um segredo partilhado

Ao utilizar um segredo partilhado, um pedido de acesso serviço-a-serviço contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `grant_type` | Necessário | O tipo de pedido simbólico. Para um pedido utilizando um JWT, o valor deve ser `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Necessário | A aplicação (cliente) ID que [o portal Azure - Página](https://go.microsoft.com/fwlink/?linkid=2083908) de registos de aplicações atribuiu à sua app. |
| `client_secret` | Necessário | O segredo do cliente que gerou para a sua aplicação na página de registos do portal Azure - App. |
| `assertion` | Necessário | O valor do símbolo utilizado no pedido.  Este token deve ter uma audiência da app que faz este `client-id` pedido OBO (a app denotada pelo campo). |
| `scope` | Necessário | Uma lista de espaços separadas de âmbitos para o pedido simbólico. Para mais informações, consulte [os seus âmbitos.](v2-permissions-and-consent.md) |
| `requested_token_use` | Necessário | Especifica como o pedido deve ser processado. No caudal da OBO, o `on_behalf_of`valor deve ser fixado para . |

#### <a name="example"></a>Exemplo

O seguinte HTTP POST solicita um token `user.read` de acesso https://graph.microsoft.com e um token de atualização com margem para a Web API.

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Pedido de acesso aceso com certificado

Um pedido de acesso serviço-a-serviço com um certificado contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `grant_type` | Necessário | O tipo do pedido simbólico. Para um pedido utilizando um JWT, o valor deve ser `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Necessário |  A aplicação (cliente) ID que [o portal Azure - Página](https://go.microsoft.com/fwlink/?linkid=2083908) de registos de aplicações atribuiu à sua app. |
| `client_assertion_type` | Necessário | O valor `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`deve ser. |
| `client_assertion` | Necessário | Uma afirmação (um símbolo web JSON) que precisa de criar e assinar com o certificado que registou como credenciais para a sua aplicação. Para saber registar o seu certificado e o formato da afirmação, consulte [as credenciais](active-directory-certificate-credentials.md)de certificado . |
| `assertion` | Necessário | O valor do símbolo utilizado no pedido. |
| `requested_token_use` | Necessário | Especifica como o pedido deve ser processado. No caudal da OBO, o `on_behalf_of`valor deve ser fixado para . |
| `scope` | Necessário | Uma lista de âmbitos separados do espaço para o pedido simbólico. Para mais informações, consulte [os seus âmbitos.](v2-permissions-and-consent.md)|

Note que os parâmetros são quase os mesmos que no `client_secret` caso do pedido por segredo `client_assertion_type` `client_assertion`partilhado, exceto que o parâmetro é substituído por dois parâmetros: a e .

#### <a name="example"></a>Exemplo

O seguinte HTTP POST solicita um `user.read` sinal https://graph.microsoft.com de acesso com margem para a Web API com um certificado.

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Serviço de acesso ao serviço resposta simbólica

Uma resposta de sucesso é uma resposta JSON OAuth 2.0 com os seguintes parâmetros.

| Parâmetro | Descrição |
| --- | --- |
| `token_type` | Indica o valor do tipo simbólico. O único tipo que a `Bearer`plataforma de identidade da Microsoft suporta é . Para obter mais informações sobre fichas ao portador, consulte o Quadro de [Autorização OAuth 2.0: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | O âmbito de acesso concedido no símbolo. |
| `expires_in` | O tempo, em segundos, que o sinal de acesso é válido. |
| `access_token` | O sinal de acesso solicitado. O serviço de chamada pode usar este símbolo para autenticar o serviço recetor. |
| `refresh_token` | O símbolo de atualização para o sinal de acesso solicitado. O serviço de chamada pode usar este token para solicitar outro sinal de acesso após o termo do presente sinal de acesso. O token de atualização `offline_access` só é fornecido se o âmbito for solicitado. |

### <a name="success-response-example"></a>Exemplo de resposta ao sucesso

O exemplo seguinte mostra uma resposta de sucesso a https://graph.microsoft.com um pedido de acesso para a Web API.

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> O símbolo de acesso acima é um símbolo v1.0-formatado. Isto porque o símbolo é fornecido com base no **recurso** que está a ser acedido. O Microsoft Graph está configurado para aceitar fichas v1.0, pelo que a plataforma de identidade da Microsoft produz fichas de acesso v1.0 quando um cliente pede fichas para o Microsoft Graph. Apenas as aplicações devem analisar fichas de acesso. Os clientes **não devem** inspecioná-los.

### <a name="error-response-example"></a>Exemplo de resposta a erros

Uma resposta de erro é devolvida pelo ponto final simbólico ao tentar adquirir um símbolo de acesso para a API a jusante, se a API a jusante tiver uma política de acesso condicional (como a autenticação de vários fatores) definida nele. O serviço de nível médio deve apresentar este erro à aplicação do cliente para que a aplicação do cliente possa fornecer a interação do utilizador para satisfazer a política de Acesso Condicional.

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

Agora, o serviço de nível médio pode usar o símbolo adquirido acima para fazer pedidos autenticados para `Authorization` a API web a jusante, definindo o símbolo no cabeçalho.

### <a name="example"></a>Exemplo

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Obtenção de consentimento para a aplicação de nível médio

Dependendo da arquitetura ou utilização da sua aplicação, poderá considerar diferentes estratégias para garantir que o fluxo oBo seja bem sucedido. Em todos os casos, o objetivo final é garantir o consentimento adequado para que a aplicação do cliente possa ligar para a aplicação de nível médio, e a aplicação de nível médio tem permissão para ligar para o recurso back-end.

> [!NOTE]
> Anteriormente, o sistema de conta da Microsoft (contas pessoais) não suportava o campo "Aplicação de cliente conhecido", nem podia mostrar consentimento combinado.  Isto foi adicionado e todas as aplicações na plataforma de identidade da Microsoft podem usar a abordagem de aplicação de cliente conhecida para obter o consentimento para chamadas OBO.

### <a name="default-and-combined-consent"></a>/.padrão e consentimento combinado

A aplicação de nível médio adiciona o cliente à lista de aplicações de clientes conhecidos no seu manifesto, e então o cliente pode desencadear um fluxo de consentimento combinado para si mesmo e para a aplicação de nível médio. No ponto final da plataforma de identidade [ `/.default` ](v2-permissions-and-consent.md#the-default-scope)da Microsoft, isto é feito utilizando o âmbito . Ao acionar um ecrã de consentimento `/.default`utilizando aplicações conhecidas do cliente e, o ecrã de consentimento mostrará permissões tanto para o cliente **para** a API de nível médio, como também solicitará quaisquer permissões que sejam necessárias pela API de nível médio. O utilizador dá consentimento para ambas as aplicações e, em seguida, o fluxo OBO funciona.

### <a name="pre-authorized-applications"></a>Pedidos pré-autorizados

Os recursos podem indicar que uma determinada aplicação tem sempre permissão para receber determinados âmbitos. Isto é principalmente útil para tornar as ligações entre um cliente frontal e um recurso de back-end mais perfeito. Um recurso pode declarar múltiplas aplicações pré-autorizadas - qualquer aplicação deste tipo pode solicitar essas permissões num fluxo OBO e recebê-las sem que o utilizador forneça o seu consentimento.

### <a name="admin-consent"></a>Consentimento do administrador

Um administrador de inquilino pode garantir que os pedidos têm permissão para ligar para as APIs necessárias, fornecendo consentimento administrativo para o pedido de nível médio. Para isso, o administrador pode encontrar a aplicação de nível médio no seu inquilino, abrir a página de permissões necessárias e optar por dar permissão para a app. Para saber mais sobre o consentimento da administração, consulte a documentação de [consentimento e permissões.](v2-permissions-and-consent.md)

### <a name="use-of-a-single-application"></a>Utilização de uma única aplicação

Em alguns cenários, você pode ter apenas um único emparelhamento de cliente de nível médio e frontal. Neste cenário, poderá ser mais fácil fazer desta uma aplicação única, negando completamente a necessidade de uma aplicação de nível médio. Para autenticar entre a frente e a Web API, pode utilizar cookies, um id_token ou um sinal de acesso solicitado para a própria aplicação. Em seguida, solicite o consentimento desta única aplicação para o recurso back-end.

## <a name="client-limitations"></a>Limitações de clientes

Se um cliente usa o fluxo implícito para obter uma id_token, e esse cliente também tem wildcards num URL de resposta, o id_token não pode ser usado para um fluxo obo.  No entanto, as fichas de acesso adquiridas através do fluxo de subvenção implícita ainda podem ser resgatadas por um cliente confidencial, mesmo que o cliente iniciado tenha um URL de resposta wildcard registado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o protocolo OAuth 2.0 e outra forma de realizar o serviço de serviço auth usando credenciais de cliente.

* [Bolsa de credenciais de clientes OAuth 2.0 na plataforma de identidade da Microsoft](v2-oauth2-client-creds-grant-flow.md)
* [Fluxo de código OAuth 2.0 na plataforma de identidade da Microsoft](v2-oauth2-auth-code-flow.md)
* [Usando `/.default` o âmbito](v2-permissions-and-consent.md#the-default-scope)
