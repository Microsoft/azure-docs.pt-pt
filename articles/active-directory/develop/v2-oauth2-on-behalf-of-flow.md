---
title: O Azure AD v2.0 OAuth2.0 em-nome-de fluxo | Documentos da Microsoft
description: Este artigo descreve como utilizar mensagens HTTP para implementar a autenticação de serviço para serviço com o OAuth2.0 fluxo em-nome-de.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cc9b2b38ae0ba97e5a29d58d1605e5452224e4b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445760"
---
# <a name="azure-active-directory-v20-and-oauth-20-on-behalf-of-flow"></a>Azure Active Directory v 2.0 e o fluxo do OAuth 2.0 On-Behalf-Of

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

O fluxo do OAuth 2.0 On-Behalf-Of (OBO) serve o caso de utilização em que um aplicativo invoca um serviço/API web, que por sua vez tem de chamar outro serviço/API web. A idéia é propagar a identidade de utilizador delegado e permissões através da cadeia de pedido. Para o serviço de camada intermediária fazer pedidos autenticados para o serviço downstream, ele precisa de proteger um token de acesso do Azure Active Directory (Azure AD), em nome do utilizador.

> [!NOTE]
> O ponto final v2.0 não suporta todos os cenários do Azure AD e funcionalidades. Para determinar se deve utilizar o ponto final v2.0, leia sobre [v2.0 limitações](active-directory-v2-limitations.md). Especificamente, os aplicativos cliente conhecidos não são suportados para aplicações com a conta Microsoft (MSA) e públicos do Azure AD. Portanto, um padrão comum de consentimento para OBO não irá funcionar para clientes que iniciar sessão tanto pessoais e contas escolares ou profissionais. Para saber mais sobre como lidar com este passo do fluxo, veja [obtendo o consentimento para a aplicação de camada intermediária](#gaining-consent-for-the-middle-tier-application).


> [!IMPORTANT]
> A partir de Maio de 2018, alguns fluxo implícito derivado `id_token` não pode ser utilizado para o OBO flow. Aplicações de página única (SPAs) devem passar um **acesso** token para uma camada intermediária cliente confidencial para efetuar OBO fluxos em vez disso. Para mais informações sobre os quais os clientes possam executar chamadas OBO, veja [limitações](#client-limitations).

## <a name="protocol-diagram"></a>Diagrama de protocolo

Partem do princípio de que o utilizador foi autenticado numa aplicação com o [fluxo de concessão de código de autorização de OAuth 2.0](v2-oauth2-auth-code-flow.md). Neste momento, o aplicativo tem um token de acesso *para API A* (token A) com afirmações do utilizador e o consentimento para acessar a camada intermediária web API (API A). Agora, API A tem de fazer um pedido autenticado para o web API (API B) a jusante.

Os passos que se seguem constituem o fluxo OBO e são explicados com a ajuda de que o diagrama seguinte.

![OAuth2.0 em-nome-de fluxo](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. A aplicação cliente faz um pedido para API A com um token de (com um `aud` de afirmação de API A).
1. API A se autentica o ponto de final de emissão de token do Azure AD e solicita um token para aceder a b de API.
1. O ponto de final de emissão de token do Azure AD valida as credenciais de uma API com um token e emite o token de acesso para a API B (token B).
1. Token B está definido no cabeçalho de autorização do pedido para o b de API.
1. Dados a partir do recurso protegido são devolvidos pelo b de API.

> [!NOTE]
> Neste cenário, o serviço de camada intermediária tem sem interação do utilizador para obter o consentimento do utilizador para aceder à API de downstream. Por conseguinte, a opção para conceder acesso à API de downstream é apresentada inicialmente como parte do consentimento passo durante a autenticação. Para saber como definir esta opção para a sua aplicação, veja [obtendo o consentimento para a aplicação de camada intermediária](#gaining-consent-for-the-middle-tier-application). 

## <a name="service-to-service-access-token-request"></a>Pedido de token de acesso de serviço a serviço

Para pedir um token de acesso, crie um HTTP POST para o ponto de final token com os seguintes parâmetros v2.0 específico de inquilino.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Existem dois casos, dependendo se o aplicativo cliente escolhe a ser protegido por um certificado ou um segredo partilhado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Pedido de token de acesso com um segredo partilhado

Ao usar um segredo partilhado, um pedido de token de acesso de serviço para serviço contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `grant_type` | Necessário | O tipo de pedido de token. Para um pedido usando um JWT, o valor tem de ser `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Necessário | ID de aplicação (cliente) que o [portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou no novo [portal de registos (pré-visualização) de aplicação](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) tem atribuído à sua aplicação. |
| `client_secret` | Necessário | O segredo de aplicação que gerou para a sua aplicação no portal que utilizou para registar a sua aplicação. |
| `assertion` | Necessário | O valor do token utilizado no pedido. |
| `scope` | Necessário | Lista de âmbitos para o pedido de token separados por um espaço. Para obter mais informações, consulte [âmbitos](v2-permissions-and-consent.md). |
| `requested_token_use` | Necessário | Especifica a forma como a solicitação deve ser processada. No fluxo OBO, o valor tem de ser definido `on_behalf_of`. |

#### <a name="example"></a>Exemplo

A seguinte mensagem de HTTP solicita um token de acesso e o token de atualização com `user.read` definir o âmbito para o https://graph.microsoft.com web API.

```
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

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Pedido de token de acesso com um certificado

Um pedido de token de acesso de serviço para serviço com um certificado contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `grant_type` | Necessário | O tipo de pedido de token. Para um pedido usando um JWT, o valor tem de ser `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Necessário | ID de aplicação (cliente) que o [portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou no novo [portal de registos (pré-visualização) de aplicação](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) tem atribuído à sua aplicação. |
| `client_assertion_type` | Necessário | O valor tem de ser `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Necessário | Uma asserção (um token de web JSON) que precisa para criar e assinar com o certificado é registado como as credenciais para a sua aplicação. Para saber como registar o seu certificado e o formato da asserção, veja [credenciais de certificado](active-directory-certificate-credentials.md). |
| `assertion` | Necessário | O valor do token utilizado no pedido. |
| `requested_token_use` | Necessário | Especifica a forma como a solicitação deve ser processada. No fluxo OBO, o valor tem de ser definido `on_behalf_of`. |
| `scope` | Necessário | Uma lista de âmbitos para o pedido de token separadas por espaços. Para obter mais informações, consulte [âmbitos](v2-permissions-and-consent.md).|

Tenha em atenção que os parâmetros são quase os mesmos como, no caso do pedido de segredo partilhado, exceto que o `client_secret` parâmetro é substituído por dois parâmetros: um `client_assertion_type` e `client_assertion`.

#### <a name="example"></a>Exemplo

A seguinte mensagem de HTTP solicita um token de acesso com `user.read` definir o âmbito para o https://graph.microsoft.com API web com um certificado.

```
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

## <a name="service-to-service-access-token-response"></a>Serviço de resposta de token de acesso de serviço

Uma resposta de êxito é uma resposta JSON OAuth 2.0 com os seguintes parâmetros.

| Parâmetro | Descrição |
| --- | --- |
| `token_type` | Indica o valor de tipo de token. O único tipo, que é o Azure AD suporta `Bearer`. Para obter mais informações sobre os tokens de portador, consulte o [Framework de autorização do OAuth 2.0: Utilização de Token de portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | O âmbito de acesso concedido no token. |
| `expires_in` | O período de tempo, em segundos, que o token de acesso é válido. |
| `access_token` | O token de acesso solicitado. O serviço de chamada pode utilizar este token para autenticar para o serviço de recebimento. |
| `refresh_token` | O token de atualização para o token de acesso solicitado. O serviço de chamada pode utilizar este token para solicitar outro token de acesso após o token de acesso atual expira. O token de atualização só é fornecido se o `offline_access` âmbito foi pedido. |

### <a name="success-response-example"></a>Exemplo de resposta de êxito

O exemplo seguinte mostra uma resposta de êxito para um pedido de um acesso token para o https://graph.microsoft.com web API.

```
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
> O token de acesso acima é um token formatada v1.0. Isto acontece porque o token é fornecido com base no recurso que está sendo acessado. O Microsoft Graph pedidos v1.0 tokens, para que o AD do Azure produz tokens de acesso de v1.0 quando um cliente solicitar tokens para o Microsoft Graph. Devem ver apenas as aplicações de tokens de acesso. Os clientes não é preciso inspecioná-los. 

### <a name="error-response-example"></a>Exemplo de resposta de erro

Uma resposta de erro devolvida pelo ponto final do token ao tentar adquirir um token de acesso para a API de downstream, se a API de downstream tem uma política de acesso condicional (por exemplo, o multi-factor authentication) definido nela. O serviço de camada intermediária deve solucionar este erro para o aplicativo cliente, para que a aplicação cliente pode fornecer a interação do usuário para satisfazer a política de acesso condicional.

```
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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Utilizar o token de acesso para aceder ao recurso protegido

Agora o serviço de camada intermediária pode utilizar o token adquirido acima para fazer pedidos autenticados para a API, web a jusante, definindo o token no `Authorization` cabeçalho.

### <a name="example"></a>Exemplo

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Obtenção de consentimento para a aplicação de camada intermediária

Consoante o público-alvo para a sua aplicação, poderá considerar estratégias diferentes para garantir que o fluxo OBO é efetuada com êxito. Em todos os casos, o objetivo final é garantir que recebe o consentimento adequado. Como isso ocorrer, no entanto, depende dos utilizadores que seu aplicativo oferece suporte. 

### <a name="consent-for-azure-ad-only-applications"></a>Consentimento para as aplicações do Azure AD só

#### <a name="default-and-combined-consent"></a>/.default e combinado de consentimento

Para aplicativos que só precisam de iniciar sessão no trabalho contas escolares ou profissionais, a abordagem de "Aplicações de cliente conhecido" tradicional é suficiente. A aplicação de camada intermediária adiciona o cliente à lista de aplicações do cliente conhecidos em seu manifesto e, em seguida, o cliente pode acionar um fluxo de consentimento combinado para si próprio e a aplicação de camada intermediária. O ponto final v2.0, isso é feito com o [ `/.default` âmbito](v2-permissions-and-consent.md#the-default-scope). Quando acionar um ecrã de consentimento a utilizar as aplicações de cliente conhecidos e `/.default`, o ecrã de consentimento mostrará as permissões para ambos os o cliente para a API de camada intermediária e também pedir as permissões que são necessárias para a API de camada intermediária. O utilizador efetua a confirmação para ambas as aplicações e, em seguida, o fluxo OBO funciona. 

Neste momento, o sistema de contas Microsoft pessoa não suporta o consentimento combinado e então, essa abordagem não funciona para aplicações que pretendem inscrever-se especificamente em contas pessoais. As contas Microsoft pessoais, a ser utilizadas como contas de convidado num inquilino são processadas utilizando o sistema do Azure AD e podem aceder através de consentimento combinado. 

#### <a name="pre-authorized-applications"></a>Aplicações previamente autorizadas

Uma nova funcionalidade do portal de pré-visualização do aplicativo é "aplicações previamente autorizadas". Dessa forma, um recurso pode indicar que um determinado aplicativo sempre tem permissão para receber determinados âmbitos. Isto é principalmente útil para tornar as ligações entre um cliente front-end e um recurso de back-end mais integrada. Um recurso pode declarar a várias aplicações previamente autorizadas - qualquer aplicação desse tipo pode pedir estas permissões num OBO fluam e recebem-las sem que o usuário fornece o consentimento.

#### <a name="admin-consent"></a>Consentimento de admin

Um administrador de inquilinos pode garantir que aplicativos tem permissão para chamar suas APIs necessários ao fornecer o consentimento de administrador para a aplicação de camada intermediária. Para fazer isso, o administrador pode encontrar a aplicação de camada intermediária no seu inquilino, abra a página de permissões necessárias e optar por conceder a permissão da aplicação. Para saber mais sobre o consentimento de administrador, veja a [documentação de permissões e consentimento](v2-permissions-and-consent.md). 

### <a name="consent-for-azure-ad--microsoft-account-applications"></a>Consentimento para o Azure AD + aplicações de conta da Microsoft

Devido a restrições no modelo de permissões para contas pessoais e a falta de um inquilino de governação, os requisitos de consentimento para contas pessoais são um pouco diferentes do Azure AD. Não existe nenhum inquilino para fornecer consentimento ao nível do inquilino, nem está aí a capacidade de fazer combinado de consentimento. Portanto, outras estratégias presentes próprios - tenha em atenção que esses recursos funcionam para aplicativos que só precisa de suportar também a contas do Azure AD. 

#### <a name="use-of-a-single-application"></a>Utilização de uma única aplicação

Em alguns cenários, pode ter apenas um único emparelhamento de cliente de camada intermediária e front-end. Neste cenário, talvez ache mais fácil tornar isso um único aplicativo, eliminando a necessidade de uma aplicação de camada intermediária completamente. Para autenticar entre o front-end e a API web, pode utilizar cookies, um id_token ou um token de acesso solicitado da própria aplicação. Em seguida, pedir consentimento desta aplicação única para o recurso de back-end. 

## <a name="client-limitations"></a>Limitações de cliente

Se um cliente utiliza o fluxo implícito para obter uma id_token e que o cliente tem também caracteres curinga numa URL de resposta, o id_token não pode ser utilizado para um fluxo de OBO.  No entanto, os tokens de acesso adquiridos por meio do fluxo de concessão implícita ainda podem ser resgatados por um cliente confidencial, mesmo que o cliente de início tem um caráter universal o URL de resposta registado. 

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o protocolo de OAuth 2.0 e a outra forma de efetuar a autenticação de serviço para serviço com as credenciais de cliente.

* [Concessão de credenciais de cliente de OAuth 2.0 na versão 2.0 do Azure AD](v2-oauth2-client-creds-grant-flow.md)
* [Fluxo de código do OAuth 2.0 na versão 2.0 do Azure AD](v2-oauth2-auth-code-flow.md)
* [Usando o `/.default` âmbito](v2-permissions-and-consent.md#the-default-scope) 
