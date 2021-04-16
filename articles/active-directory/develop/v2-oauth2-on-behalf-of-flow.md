---
title: Plataforma de identidade microsoft e OAuth2.0 Em nome-of fluxo | Rio Azure
titleSuffix: Microsoft identity platform
description: Este artigo descreve como utilizar mensagens HTTP para implementar o serviço de autenticação de serviço utilizando o fluxo OAuth2.0 On-Behalf-Of.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/7/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1c8ea1580047910cb2d6634aad885d61e99113f3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529975"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Plataforma de identidade da Microsoft e OAuth 2.0 Em nome do fluxo


O OAuth 2.0 On-Behalf-Of flow (OBO) serve o caso de utilização quando uma aplicação invoca um serviço/web API, que por sua vez precisa de chamar outro serviço/web API. A ideia é propagar a identidade e permissões de utilizador delegadas através da cadeia de pedidos. Para que o serviço de nível médio faça pedidos autenticados para o serviço a jusante, este necessita de garantir um sinal de acesso a partir da plataforma de identidade da Microsoft, em nome do utilizador.

Este artigo descreve como programar diretamente contra o protocolo na sua aplicação.  Quando possível, recomendamos que utilize as Bibliotecas de Autenticação da Microsoft (MSAL) suportadas para [adquirir fichas e chamar APIs web seguras](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Dê também uma olhada nas aplicações de [amostra que utilizam o MSAL.](sample-v2-code.md)


A partir de maio de 2018, alguns derivados do fluxo implícito `id_token` não podem ser usados para o fluxo OBO. As aplicações de uma só página (SPAs) devem passar um **token** de acesso a um cliente confidencial de nível médio para realizar fluxos OBO em vez disso. Para obter mais informações sobre quais os clientes que podem realizar chamadas OBO, consulte [limitações.](#client-limitations)

## <a name="protocol-diagram"></a>Diagrama de protocolo

Suponha que o utilizador tenha sido autenticado numa aplicação utilizando o fluxo de concessão de [código de autorização OAuth 2.0](v2-oauth2-auth-code-flow.md) ou outro fluxo de login. Neste momento, a aplicação tem um token de acesso *para API A* (token A) com as reclamações do utilizador e consentimento para aceder à API web de nível médio (API A). Agora, a API A precisa de fazer um pedido autenticado à API web a jusante (API B).

Os passos que se seguem constituem o fluxo OBO e são explicados com a ajuda do diagrama seguinte.

![Mostra o fluxo OAuth2.0 Em nome](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. O pedido do cliente faz um pedido à API A com o token A (com `aud` uma reclamação da API A).
1. A API A autentica-se no ponto final de emissão de identidade da Microsoft e solicita um token para aceder à API B.
1. O ponto final de emissão de emissão de imitância da plataforma de identidade da Microsoft valida as credenciais da API A juntamente com o token A e emite o token de acesso da API B (token B) à API A.
1. O Token B é definido pela API A no cabeçalho de autorização do pedido à API B.
1. Os dados do recurso seguro são devolvidos pela API B à API A e, em seguida, ao cliente.

Neste cenário, o serviço de nível médio não tem qualquer interação do utilizador para obter o consentimento do utilizador para aceder à API a jusante. Por isso, a opção de conceder acesso à API a jusante é apresentada antecipadamente como parte do passo de consentimento durante a autenticação. Para aprender a configurar isto para a sua aplicação, consulte [Obter o consentimento para a aplicação de nível médio.](#gaining-consent-for-the-middle-tier-application)

## <a name="middle-tier-access-token-request"></a>Pedido de sinal de acesso de nível médio

Para solicitar um token de acesso, faça um HTTP POST para o ponto final da plataforma de identidade da Microsoft específica para o arrendatário com os seguintes parâmetros.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Existem dois casos dependendo se o pedido do cliente opta por ser garantido por um segredo partilhado ou por um certificado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Pedido de acesso a um sinal com um segredo partilhado

Ao utilizar um segredo partilhado, um pedido de sinal de acesso de serviço contém os seguintes parâmetros:

| Parâmetro | Tipo | Descrição |
| --- | --- | --- |
| `grant_type` | Obrigatório | O tipo de pedido simbólico. Para um pedido de utilização de um JWT, o valor deve ser `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Necessário | A aplicação (cliente) ID que [o portal Azure - Página de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuiu à sua app. |
| `client_secret` | Necessário | O segredo do cliente que gerou para a sua aplicação na página de registos do portal Azure - App. |
| `assertion` | Necessário | O sinal de acesso que foi enviado para a API de nível médio.  Este token deve ter uma reivindicação de audiência `aud` () da aplicação que faz este pedido OBO (a app denotada pelo `client-id` campo). As aplicações não podem resgatar um símbolo para uma aplicação diferente (por exemplo, se um cliente enviar um token API destinado a MS Graph, a API não pode resgatá-la usando o OBO.  Deveria, em vez disso, rejeitar o símbolo).  |
| `scope` | Necessário | Uma lista de âmbitos separados para o pedido simbólico. Para mais informações, consulte [os âmbitos.](v2-permissions-and-consent.md) |
| `requested_token_use` | Necessário | Especifica como o pedido deve ser processado. No fluxo OBO, o valor deve ser definido para `on_behalf_of` . |

#### <a name="example"></a>Exemplo

O seguinte HTTP POST solicita um token de acesso e atualização token com `user.read` âmbito para a https://graph.microsoft.com API web.

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyO{a lot of characters here}
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Pedido de token de acesso com certificado

Um pedido de sinal de acesso de serviço ao serviço com um certificado contém os seguintes parâmetros:

| Parâmetro | Tipo | Descrição |
| --- | --- | --- |
| `grant_type` | Obrigatório | O tipo de pedido simbólico. Para um pedido de utilização de um JWT, o valor deve ser `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Necessário |  A aplicação (cliente) ID que [o portal Azure - Página de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuiu à sua app. |
| `client_assertion_type` | Necessário | O valor deve `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` ser. |
| `client_assertion` | Necessário | Uma afirmação (um token web JSON) que precisa de criar e assinar com o certificado que registou como credenciais para a sua aplicação. Para saber como registar o seu certificado e o formato da afirmação, consulte [as credenciais](active-directory-certificate-credentials.md)de certificado. |
| `assertion` | Necessário |  O sinal de acesso que foi enviado para a API de nível médio.  Este token deve ter uma reivindicação de audiência `aud` () da aplicação que faz este pedido OBO (a app denotada pelo `client-id` campo). As aplicações não podem resgatar um símbolo para uma aplicação diferente (por exemplo, se um cliente enviar um token API destinado a MS Graph, a API não pode resgatá-la usando o OBO.  Deveria, em vez disso, rejeitar o símbolo).  |
| `requested_token_use` | Necessário | Especifica como o pedido deve ser processado. No fluxo OBO, o valor deve ser definido para `on_behalf_of` . |
| `scope` | Necessário | Uma lista de âmbitos separados pelo espaço para o pedido simbólico. Para mais informações, consulte [os âmbitos.](v2-permissions-and-consent.md)|

Note-se que os parâmetros são quase os mesmos que no caso do pedido por segredo partilhado, exceto que o `client_secret` parâmetro é substituído por dois parâmetros: a e `client_assertion_type` `client_assertion` .

#### <a name="example"></a>Exemplo

O seguinte HTTP POST solicita um token de acesso com `user.read` âmbito para a https://graph.microsoft.com API web com um certificado.

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiO{a lot of characters here}
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="middle-tier-access-token-response"></a>Resposta simbólica de acesso de nível médio

Uma resposta de sucesso é uma resposta JSON OAuth 2.0 com os seguintes parâmetros.

| Parâmetro | Descrição |
| --- | --- |
| `token_type` | Indica o valor do tipo símbolo. O único tipo que a plataforma de identidade da Microsoft suporta é `Bearer` . Para obter mais informações sobre fichas ao portador, consulte o [Quadro de Autorização OAuth 2.0: Utilização do Token ao portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | O âmbito de acesso concedido no token. |
| `expires_in` | O tempo, em segundos, que o token de acesso é válido. |
| `access_token` | O sinal de acesso solicitado. O serviço de chamadas pode utilizar este token para autenticar o serviço de receção. |
| `refresh_token` | O token de atualização para o token de acesso solicitado. O serviço de chamada pode usar este token para solicitar outro token de acesso após o termo do token de acesso atual. O token de atualização só é fornecido se o `offline_access` âmbito de aplicação for solicitado. |

### <a name="success-response-example"></a>Exemplo de resposta ao sucesso

O exemplo a seguir mostra uma resposta de sucesso a um pedido de acesso à https://graph.microsoft.com API web.

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4An{a lot of characters here}"
}
```

O token de acesso acima é um símbolo em formato v1.0 para o Microsoft Graph. Isto porque o formato token baseia-se no **recurso** que está a ser acedido e não está relacionado com os pontos finais utilizados para o solicitar. O Microsoft Graph está configurado para aceitar fichas v1.0, pelo que a plataforma de identidade da Microsoft produz fichas de acesso v1.0 quando um cliente solicita fichas para o Microsoft Graph. Outras aplicações podem indicar que querem fichas em formato v2.0, fichas em formato v1.0 ou até formatos simbólicos ou encriptados.  Tanto os pontos finais v1.0 como v2.0 podem emitir qualquer formato de token - desta forma o recurso pode sempre obter o formato certo de token independentemente de como ou onde o token foi solicitado pelo cliente. 

Apenas as aplicações devem olhar para os tokens de acesso. Os clientes **não devem** inspecioná-los. A inspeção de fichas de acesso a outras aplicações no seu código resultará na quebra inesperada da sua aplicação quando essa aplicação muda o formato dos seus tokens ou começa a criptografá-las. 

### <a name="error-response-example"></a>Exemplo de resposta a erros

Uma resposta de erro é devolvida pelo ponto final simbólico ao tentar adquirir um token de acesso para a API a jusante, se a API a jusante tiver uma política de Acesso Condicional (como [a autenticação de vários fatores)](../authentication/concept-mfa-howitworks.md)definida nele. O serviço de nível médio deve emergir este erro na aplicação do cliente para que a aplicação do cliente possa fornecer a interação do utilizador para satisfazer a política de Acesso Condicional.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Use o token de acesso para aceder ao recurso seguro

Agora, o serviço de nível médio pode usar o token adquirido acima para fazer pedidos autenticados para a API web a jusante, definindo o símbolo no `Authorization` cabeçalho.

### <a name="example"></a>Exemplo

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Afirmações SAML obtidas com um fluxo OBO OBO OAU.0

Alguns serviços web baseados em OAuth precisam de aceder a outros APIs de serviço web que aceitam afirmações DE SAML em fluxos não interativos. O Azure Ative Directory pode fornecer uma afirmação SAML em resposta a um fluxo On-Behalf-Of que utiliza um serviço web baseado em SAML como recurso alvo.

Trata-se de uma extensão não padrão do fluxo OAuth 2.0 On-Behalf-Of que permite a uma aplicação baseada em OAuth2 aceder a pontos finais de serviço web API que consomem fichas SAML.

> [!TIP]
> Quando você chama um serviço web protegido por SAML a partir de uma aplicação web frontal, você pode simplesmente ligar para a API e iniciar um fluxo de autenticação interativa normal com a sessão existente do utilizador. Só precisa de utilizar um fluxo OBO quando uma chamada de serviço para serviço requer um token SAML para fornecer o contexto do utilizador.
 
 ### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Obtenha um símbolo SAML usando um pedido OBO com um segredo compartilhado

Um pedido de serviço-a-serviço para uma afirmação SAML contém os seguintes parâmetros:

| Parâmetro | Tipo | Description |
| --- | --- | --- |
| grant_type |obrigatório | O tipo de pedido simbólico. Para um pedido que utilize um JWT, o valor deve ser **urna:ietf:params:oauth:grant-type:jwt-bearer**. |
| afirmação |obrigatório | O valor do token de acesso utilizado no pedido.|
| client_id |obrigatório | O ID da aplicação atribuído ao serviço de chamadas durante o registo com a Azure AD. Para encontrar o ID da aplicação no portal Azure, selecione **Ative Directory**, escolha o diretório e, em seguida, selecione o nome da aplicação. |
| client_secret |obrigatório | A chave registada para o serviço de chamadas em Azure AD. Este valor deveria ter sido anotado no momento da inscrição. |
| recurso |obrigatório | A aplicação ID URI do serviço recetor (recurso seguro). Este é o recurso que será o Público do símbolo DA SAML. Para encontrar a aplicação ID URI no portal Azure, selecione **Ative Directory** e escolha o diretório. Selecione o nome da aplicação, escolha **Todas as definições** e, em seguida, selecione **Propriedades**. |
| requested_token_use |obrigatório | Especifica como o pedido deve ser processado. No fluxo em nome do be, o valor deve ser **on_behalf_of**. |
| requested_token_type | obrigatório | Especifica o tipo de ficha solicitada. O valor pode ser **urna:ietf:params:oauth:token-type:saml2** ou **urn:ietf:params:oauth:token-type:saml1** dependendo dos requisitos do recurso acedido. |

A resposta contém um token SAML codificado em UTF8 e Base64url.

- **AssuntoConfirmationData para uma afirmação SAML proveniente de uma chamada OBO**: Se a aplicação-alvo requer um valor do destinatário no **SubjectConfirmationData,** então o valor deve ser um URL de resposta não wildcard na configuração da aplicação de recurso.
- **O nó SubjectConfirmationData**: O nó não pode conter um atributo **InResponseTo,** uma vez que não faz parte de uma resposta SAML. A aplicação que recebe o token SAML deve ser capaz de aceitar a afirmação SAML sem um atributo **InResponseTo.**

- **Consentimento**: O consentimento deve ter sido concedido para receber um token SAML contendo dados do utilizador sobre um fluxo OAuth. Para obter informações sobre permissões e obter o consentimento do administrador, consulte [permissões e consentimento no ponto final do Azure Ative Directory v1.0](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent).

### <a name="response-with-saml-assertion"></a>Resposta com afirmação SAML

| Parâmetro | Descrição |
| --- | --- |
| token_type |Indica o valor do tipo símbolo. O único tipo que a Azure AD suporta é **o Portador.** Para obter mais informações sobre fichas ao portador, consulte [o Quadro de Autorização OAuth 2.0: Utilização do Token ao portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |O âmbito de acesso concedido no token. |
| expires_in |O tempo de duração do token de acesso é válido (em segundos). |
| expires_on |O tempo em que o sinal de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0Z UTC até ao prazo de validade. Este valor é usado para determinar a vida útil de fichas em cache. |
| recurso |A aplicação ID URI do serviço recetor (recurso seguro). |
| access_token |O parâmetro que devolve a afirmação do SAML. |
| refresh_token |O token refresh. O serviço de chamadas pode usar este token para solicitar outro token de acesso após a expiração da afirmação atual da SAML. |

- token_type: Portador
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- recurso: `https://api.contoso.com`
- access_token: \<SAML assertion\>
- issued_token_type: urna:ietf:params:oauth:token-type:saml2
- refresh_token: \<Refresh token\>


## <a name="gaining-consent-for-the-middle-tier-application"></a>Obtenção de consentimento para a aplicação de nível médio

Dependendo da arquitetura ou uso da sua aplicação, poderá considerar diferentes estratégias para garantir que o fluxo OBO seja bem sucedido. Em todos os casos, o objetivo final é garantir que o consentimento adequado é dado para que a app do cliente possa ligar para a app de nível médio, e a aplicação de nível médio tem permissão para ligar para o recurso back-end.

> [!NOTE]
> Anteriormente, o sistema de conta microsoft (contas pessoais) não suportava o campo "aplicação de clientes conhecidos", nem podia apresentar consentimento combinado.  Isto foi adicionado e todas as aplicações na plataforma de identidade da Microsoft podem usar a abordagem conhecida da aplicação do cliente para obter o consentimento para chamadas OBO.

### <a name="default-and-combined-consent"></a>/.predefinido e consentimento combinado

A aplicação de nível médio adiciona o cliente à lista de aplicações de clientes conhecida no seu manifesto, e então o cliente pode desencadear um fluxo de consentimento combinado para si mesmo e para a aplicação de nível médio. Na plataforma de identidade da Microsoft, isto é feito utilizando o [ `/.default` âmbito.](v2-permissions-and-consent.md#the-default-scope) Ao desencadear um ecrã de consentimento utilizando aplicações de clientes conhecidas `/.default` e, o ecrã de consentimento mostrará permissões tanto para o cliente como para a API de nível médio, e também solicitará quaisquer permissões necessárias pela API de nível médio.  O utilizador fornece o consentimento para ambas as aplicações e, em seguida, o fluxo OBO funciona.

### <a name="pre-authorized-applications"></a>Pedidos pré-autorizados

Os recursos podem indicar que um determinado pedido tem sempre permissão para receber determinados âmbitos. Isto é principalmente útil para tornar as ligações entre um cliente frontal e um recurso de back-end mais perfeitas. Um recurso pode declarar várias aplicações pré-autorizadas - qualquer aplicação deste tipo pode solicitar estas permissões num fluxo OBO e recebê-las sem que o utilizador forneça o seu consentimento.

### <a name="admin-consent"></a>Consentimento do administrador

Um administrador inquilino pode garantir que os pedidos têm permissão para chamar as suas APIs necessárias, fornecendo consentimento administrativo para o pedido de nível médio. Para isso, o administrador pode encontrar a aplicação de nível médio no seu inquilino, abrir a página de permissões necessárias e optar por dar permissão para a app. Para saber mais sobre o consentimento da administração, consulte a documentação de [consentimento e permissões.](v2-permissions-and-consent.md)

### <a name="use-of-a-single-application"></a>Utilização de uma única aplicação

Em alguns cenários, você pode ter apenas um único emparelhamento de cliente de nível médio e frontal. Neste cenário, poderá ser mais fácil fazer desta uma única aplicação, negando a necessidade de uma aplicação de nível médio. Para autenticar entre a front-end e a API web, pode utilizar cookies, um id_token ou um token de acesso solicitado para a própria aplicação. Em seguida, solicite o consentimento deste único pedido para o recurso back-end.

## <a name="client-limitations"></a>Limitações do cliente

Se um cliente usa o fluxo implícito para obter uma id_token, e esse cliente também tem wildcards em um URL de resposta, o id_token não pode ser usado para um fluxo OBO.  No entanto, os tokens de acesso adquiridos através do fluxo implícito de subvenção ainda podem ser resgatados por um cliente confidencial, mesmo que o cliente iniciante tenha um URL de resposta wildcard registado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o protocolo OAuth 2.0 e outra forma de realizar o serviço de atendimento usando credenciais de cliente.

* [Bolsa de credenciais de cliente oauth 2.0 na plataforma de identidade da Microsoft](v2-oauth2-client-creds-grant-flow.md)
* [Fluxo de código OAuth 2.0 na plataforma de identidade da Microsoft](v2-oauth2-auth-code-flow.md)
* [Usando o `/.default` âmbito](v2-permissions-and-consent.md#the-default-scope)
