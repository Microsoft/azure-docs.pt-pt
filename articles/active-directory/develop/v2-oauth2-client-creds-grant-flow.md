---
title: Fluxo de credenciais do cliente OAuth 2,0 na plataforma Microsoft Identity | Azure
description: Crie aplicativos Web usando a implementação da plataforma de identidade da Microsoft do protocolo de autenticação OAuth 2,0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ebdebd48710e892ec8f1a96eeba0f91228024420
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700537"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Plataforma de identidade da Microsoft e o fluxo de credenciais do cliente OAuth 2,0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Você pode usar a [concessão de credenciais de cliente OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.4) especificada na RFC 6749, às vezes chamada de *OAuth de duas pernas*, para acessar recursos hospedados na Web usando a identidade de um aplicativo. Esse tipo de concessão é comumente usado para interações de servidor para servidor que devem ser executadas em segundo plano, sem interação imediata com um usuário. Esses tipos de aplicativos são geralmente chamados de *daemons* ou *contas de serviço*.

Este artigo descreve como programar diretamente em relação ao protocolo em seu aplicativo. Quando possível, recomendamos que você use as MSAL (bibliotecas de autenticação da Microsoft) com suporte em vez de [adquirir tokens e chamar APIs da Web protegidas](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Veja também os [aplicativos de exemplo que usam MSAL](sample-v2-code.md).

O fluxo de concessão de credenciais de cliente do OAuth 2,0 permite que um serviço Web (cliente confidencial) Use suas próprias credenciais, em vez de representar um usuário, para autenticar ao chamar outro serviço Web. Nesse cenário, o cliente é normalmente um serviço Web de camada intermediária, um serviço de daemon ou um site da Web. Para um nível mais alto de garantia, a plataforma de identidade da Microsoft também permite que o serviço de chamada use um certificado (em vez de um segredo compartilhado) como uma credencial.

> [!NOTE]
> O ponto de extremidade da plataforma de identidade da Microsoft não dá suporte a todos os cenários e recursos do Azure AD. Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

No OAuth mais comum de *três pernas*, um aplicativo cliente recebe permissão para acessar um recurso em nome de um usuário específico. A permissão é delegada do usuário para o aplicativo, geralmente durante o processo de [consentimento](v2-permissions-and-consent.md) . No entanto, no fluxo de credenciais do cliente (*OAuth de duas pernas*), as permissões são concedidas diretamente ao próprio aplicativo. Quando o aplicativo apresenta um token para um recurso, o recurso impõe que o próprio aplicativo tenha autorização para executar uma ação e não o usuário.

## <a name="protocol-diagram"></a>Diagrama de protocolo

Todo o fluxo de credenciais do cliente é semelhante ao diagrama a seguir. Descrevemos cada uma das etapas mais adiante neste artigo.

![Diagrama mostrando o fluxo de credenciais do cliente](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Obter autorização direta

Um aplicativo normalmente recebe autorização direta para acessar um recurso de uma das duas maneiras:

* [Por meio de uma ACL (lista de controle de acesso) no recurso](#access-control-lists)
* [Por meio da atribuição de permissão de aplicativo no Azure AD](#application-permissions)

Esses dois métodos são os mais comuns no Azure AD e são recomendados para clientes e recursos que executam o fluxo de credenciais do cliente. Um recurso também pode optar por autorizar seus clientes de outras maneiras. Cada servidor de recursos pode escolher o método que faz mais sentido para seu aplicativo.

### <a name="access-control-lists"></a>Listas de controlo de acesso

Um provedor de recursos pode impor uma verificação de autorização com base em uma lista de IDs de aplicativo (cliente) que ele conhece e concede um nível específico de acesso ao. Quando o recurso recebe um token do ponto de extremidade da plataforma Microsoft Identity, ele pode decodificar o token e extrair a ID do aplicativo do cliente do `appid` e `iss` declarações. Em seguida, ele compara o aplicativo com uma ACL (lista de controle de acesso) que ele mantém. A granularidade e o método da ACL podem variar substancialmente entre os recursos.

Um caso de uso comum é usar uma ACL para executar testes para um aplicativo Web ou para uma API da Web. A API da Web pode conceder apenas um subconjunto de permissões completas para um cliente específico. Para executar testes de ponta a ponta na API, crie um cliente de teste que adquire tokens do ponto de extremidade da plataforma Microsoft Identity e, em seguida, os envia para a API. Em seguida, a API verifica a ACL para a ID do aplicativo do cliente de teste para ter acesso completo à funcionalidade inteira da API. Se você usar esse tipo de ACL, certifique-se de validar não apenas o valor de `appid` do chamador, mas também validar que o valor de `iss` do token é confiável.

Esse tipo de autorização é comum para daemons e contas de serviço que precisam acessar dados de propriedade de usuários do consumidor que têm contas pessoais da Microsoft. Para dados de propriedade de organizações, recomendamos que você obtenha a autorização necessária por meio de permissões de aplicativo.

### <a name="application-permissions"></a>Permissões de aplicativo

Em vez de usar ACLs, você pode usar APIs para expor um conjunto de **permissões de aplicativo**. Uma permissão de aplicativo é concedida a um aplicativo pelo administrador de uma organização e pode ser usada somente para acessar dados pertencentes a essa organização e a seus funcionários. Por exemplo, Microsoft Graph expõe várias permissões de aplicativo para fazer o seguinte:

* Ler emails em todas as caixas de correio
* Ler e gravar emails em todas as caixas de correio
* Enviar email como qualquer usuário
* Ler dados do diretório

Para obter mais informações sobre permissões de aplicativo, acesse [Microsoft Graph](https://developer.microsoft.com/graph).

Para usar permissões de aplicativo em seu aplicativo, siga as etapas discutidas nas próximas seções.


> [!NOTE]
> Ao autenticar como um aplicativo, em oposição a um usuário, você não pode usar "permissões delegadas" (escopos que são concedidos por um usuário).  Você deve usar "permissões de aplicativo", também conhecidas como "funções", que são concedidas por um administrador para o aplicativo (ou via pré-autorização pela API Web).    


#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar as permissões no portal de registro de aplicativo

1. Registre e crie um aplicativo por meio da nova [experiência de registros de aplicativo (versão prévia)](quickstart-register-app.md).
2. Vá para o aplicativo na experiência de Registros de aplicativo (versão prévia). Navegue até a seção **certificados & segredos** e adicione um **novo segredo do cliente**, pois você precisará de pelo menos um segredo do cliente para solicitar um token.
3. Localize a seção **permissões de API** e adicione as permissões de **aplicativo** que seu aplicativo requer.
4. **Salve** o registro do aplicativo.

#### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: conectar o usuário ao seu aplicativo

Normalmente, quando você cria um aplicativo que usa permissões de aplicativo, o aplicativo requer uma página ou exibição na qual o administrador aprova as permissões do aplicativo. Essa página pode fazer parte do fluxo de entrada do aplicativo, parte das configurações do aplicativo ou pode ser um fluxo de "conexão" dedicado. Em muitos casos, faz sentido que o aplicativo mostre esse modo de exibição "conectar" somente depois que um usuário tiver entrado com uma conta Microsoft corporativa ou de estudante.

Se você conectar o usuário ao seu aplicativo, poderá identificar a organização à qual o usuário pertence antes de solicitar que o usuário aprove as permissões do aplicativo. Embora não seja estritamente necessário, ele pode ajudá-lo a criar uma experiência mais intuitiva para seus usuários. Para conectar o usuário, siga nossos [tutoriais de protocolo de plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar as permissões de um administrador de diretório

Quando estiver pronto para solicitar permissões do administrador da organização, você poderá redirecionar o usuário para o *ponto de extremidade de consentimento do administrador*da plataforma de identidade da Microsoft.

> [!TIP]
> Tente executar esta solicitação no postmaster! (Use o seu próprio ID de aplicação para obter os melhores resultados - a aplicação tutorial não solicitará permissões úteis.) [![Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | O locatário do diretório do qual você deseja solicitar permissão. Isso pode estar no formato de nome amigável ou GUID. Se você não souber a qual locatário o usuário pertence e quiser permitir que eles entrem com qualquer locatário, use `common`. |
| `client_id` | Obrigatório | A **ID do aplicativo (cliente)** que a [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `redirect_uri` | Obrigatório | O URI de redirecionamento no qual você deseja que a resposta seja enviada para que seu aplicativo manipule. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no portal, exceto que ele deve ser codificado por URL e pode ter segmentos de caminho adicionais. |
| `state` | Recomendado | Um valor que é incluído na solicitação que também é retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |

Neste ponto, o Azure AD impõe que apenas um administrador de locatários possa entrar para concluir a solicitação. O administrador será solicitado a aprovar todas as permissões diretas do aplicativo que você solicitou para seu aplicativo no portal de registro de aplicativo.

##### <a name="successful-response"></a>Resposta bem-sucedida

Se o administrador aprovar as permissões para seu aplicativo, a resposta bem-sucedida terá esta aparência:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| --- | --- |
| `tenant` | O locatário de diretório que concedeu ao aplicativo as permissões que ele solicitou, no formato GUID. |
| `state` | Um valor que é incluído na solicitação que também é retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |
| `admin_consent` | Defina como **true**. |

##### <a name="error-response"></a>Resposta de erro

Se o administrador não aprovar as permissões para seu aplicativo, a resposta com falha terá esta aparência:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de caracteres de código de erro que você pode usar para classificar tipos de erros e que você pode usar para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa raiz de um erro. |

Depois de receber uma resposta bem-sucedida do ponto de extremidade de provisionamento de aplicativo, seu aplicativo terá as permissões diretas de aplicativo solicitadas. Agora você pode solicitar um token para o recurso desejado.

## <a name="get-a-token"></a>Obter um token

Depois de adquirir a autorização necessária para seu aplicativo, continue com a aquisição de tokens de acesso para APIs. Para obter um token usando a concessão de credenciais de cliente, envie uma solicitação POST para o `/token` ponto de extremidade da plataforma de identidade da Microsoft:

> [!TIP]
> Tente executar esta solicitação no postmaster! (Use o seu próprio ID de aplicação para obter os melhores resultados - a aplicação tutorial não solicitará permissões úteis.) [![Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: solicitação de token de acesso com um segredo compartilhado

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
// Replace {tenant} with your tenant! 
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | O locatário de diretório no qual o aplicativo planeja operar, no formato GUID ou nome de domínio. |
| `client_id` | Obrigatório | A ID do aplicativo que é atribuída ao seu aplicativo. Você pode encontrar essas informações no portal em que você registrou seu aplicativo. |
| `scope` | Obrigatório | O valor passado para o parâmetro `scope` nessa solicitação deve ser o identificador de recurso (URI de ID do aplicativo) do recurso desejado, afixado com o sufixo `.default`. Para o exemplo de Microsoft Graph, o valor é `https://graph.microsoft.com/.default`. <br/>Esse valor informa ao ponto de extremidade da plataforma Microsoft Identity que de todas as permissões diretas do aplicativo que você configurou para seu aplicativo, o ponto de extremidade deve emitir um token para aqueles associados ao recurso que você deseja usar. Para saber mais sobre o escopo de `/.default`, consulte a [documentação de consentimento](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Obrigatório | O segredo do cliente que você gerou para seu aplicativo no portal de registro de aplicativo. O segredo do cliente deve ser codificado por URL antes de ser enviado. |
| `grant_type` | Obrigatório | Tem de ser definido como `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: solicitação de token de acesso com um certificado

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | O locatário de diretório no qual o aplicativo planeja operar, no formato GUID ou nome de domínio. |
| `client_id` | Obrigatório |A ID do aplicativo (cliente) atribuída ao seu aplicativo. |
| `scope` | Obrigatório | O valor passado para o parâmetro `scope` nessa solicitação deve ser o identificador de recurso (URI de ID do aplicativo) do recurso desejado, afixado com o sufixo `.default`. Para o exemplo de Microsoft Graph, o valor é `https://graph.microsoft.com/.default`. <br/>Esse valor informa ao ponto de extremidade da plataforma Microsoft Identity que de todas as permissões diretas do aplicativo que você configurou para seu aplicativo, ele deve emitir um token para aqueles associados ao recurso que você deseja usar. Para saber mais sobre o escopo de `/.default`, consulte a [documentação de consentimento](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Obrigatório | O valor deve ser definido como `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Obrigatório | Uma asserção (um token Web JSON) que você precisa para criar e assinar com o certificado que você registrou como credenciais para seu aplicativo. Leia sobre [as credenciais do certificado](active-directory-certificate-credentials.md) para saber como registrar seu certificado e o formato da asserção.|
| `grant_type` | Obrigatório | Tem de ser definido como `client_credentials`. |

Note-se que os parâmetros são quase os mesmos que no caso do pedido por segredo partilhado, exceto que o parâmetro client_secret é substituído por dois parâmetros: um client_assertion_type e client_assertion.

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta com êxito tem o seguinte aspeto:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parâmetro | Descrição |
| --- | --- |
| `access_token` | O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como em uma API da Web. |
| `token_type` | Indica o valor do tipo de token. O único tipo com suporte da plataforma de identidade da Microsoft é `bearer`. |
| `expires_in` | A quantidade de tempo que um token de acesso é válido (em segundos). |

### <a name="error-response"></a>Resposta de erro

Uma resposta de erro é parecida com esta:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de caracteres de código de erro que você pode usar para classificar tipos de erros que ocorrem e reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar a identificar a causa raiz de um erro de autenticação. |
| `error_codes` | Uma lista de códigos de erro específicos do STS que podem ajudar com o diagnóstico. |
| `timestamp` | A hora em que o erro ocorreu. |
| `trace_id` | Um identificador exclusivo para a solicitação para ajudar com o diagnóstico. |
| `correlation_id` | Um identificador exclusivo para a solicitação para ajudar com o diagnóstico entre os componentes. |

## <a name="use-a-token"></a>Usar um token

Agora que você adquiriu um token, use o token para fazer solicitações para o recurso. Quando o token expirar, repita a solicitação para o ponto de extremidade `/token` para adquirir um novo token de acesso.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Exemplos de código e outras documentações

Leia a [documentação visão geral das credenciais do cliente](https://aka.ms/msal-net-client-credentials) na biblioteca de autenticação da Microsoft

| Exemplo | Plataforma |Descrição |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | Console do .NET Core 2,1 | Um aplicativo .NET Core simples que exibe os usuários de um locatário consultando o Microsoft Graph usando a identidade do aplicativo, em vez de em nome de um usuário. O exemplo também ilustra a variação usando certificados para autenticação. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Um aplicativo Web que sincroniza dados do Microsoft Graph usando a identidade do aplicativo, em vez de em nome de um usuário. |
