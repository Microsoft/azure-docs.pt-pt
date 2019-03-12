---
title: Utilizar v2.0 do Azure AD para aceder a recursos seguros, sem interação do utilizador | Documentos da Microsoft
description: Crie aplicativos web, utilizando a implementação do Azure AD do protocolo de autenticação OAuth 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
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
ms.openlocfilehash: d25963d44960ec3ab15fdee2c264c3bf18e26c2a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540573"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>O Azure Active Directory v 2.0 e o fluxo de credenciais de cliente OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Pode utilizar o [concessão de credenciais de cliente OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.4) especificado no RFC 6749, por vezes denominado *polvo de dois OAuth*, para aceder a recursos hospedados na web utilizando a identidade de um aplicativo. Este tipo de concessão é geralmente utilizado para interações de servidor para servidor que devem ser executado em segundo plano, sem interação imediata com um utilizador. Esses tipos de aplicativos são, muitas vezes, denominados *daemons* ou *contas de serviço*.

As credenciais de cliente OAuth 2.0 concedem o fluxo permite-que um serviço da web (cliente confidencial) para utilizar as suas próprias credenciais, em vez de representar um utilizador, para autenticação ao chamar outro serviço web. Neste cenário, o cliente é normalmente um serviço web de camada intermediária, um serviço de daemon ou um web site. Para um nível mais elevado de garantia, a plataforma de identidade da Microsoft também permite que o serviço de chamada utilizar um certificado (em vez de um segredo partilhado) como uma credencial.

> [!NOTE]
> O ponto final v2.0 não suporta todos os cenários do Azure AD e funcionalidades. Para determinar se deve utilizar o ponto final v2.0, leia sobre [v2.0 limitações](active-directory-v2-limitations.md).

Em mais típica *polvo de três OAuth*, um aplicativo cliente é concedido permissão para aceder a um recurso em nome de um utilizador específico. A permissão é delegada do usuário ao aplicativo, normalmente durante a [consentimento](v2-permissions-and-consent.md) processo. No entanto, nas credenciais de cliente (*polvo de dois OAuth*) fluxo, são concedidas permissões diretamente para o aplicativo propriamente dito. Quando a aplicação apresenta um token a um recurso, o recurso impõe que a aplicação em si tem autorização para efetuar uma ação e não pelo usuário. 

## <a name="protocol-diagram"></a>Diagrama de protocolo

O fluxo de credenciais de cliente inteira é semelhante para o diagrama seguinte. Nós descrevemos, cada um dos passos neste artigo.

![Fluxo de credenciais de cliente](./media/v2-oauth2-client-creds-grant-flow/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Obter autorização direta

Uma aplicação recebe normalmente direta autorização para aceder a um recurso de uma de duas formas: 

* [Por meio de uma lista de controlo de acesso (ACL) no recurso](#access-control-lists)
* [Por meio de atribuição de permissão da aplicação no Azure AD](#application-permissions)

Esses dois métodos são as mais comuns no Azure AD e recomendamos-los para os clientes e recursos que executam o cliente do fluxo de credenciais. Um recurso também pode optar por autorizar os clientes de outras formas. Cada servidor de recurso pode escolher o método que faz mais sentido para sua aplicação.

### <a name="access-control-lists"></a>Lista de controlo de acesso

Um fornecedor de recursos poderá impor uma verificação de autorização com base numa lista de aplicação (cliente) IDs que ele sabe e concede um nível específico de acesso a. Quando o recurso recebe um token a partir do ponto final v2.0, ele pode decodificar o token e extrair o ID de aplicação do cliente dos `appid` e `iss` afirmações. Em seguida, ele compara o aplicativo em relação a uma lista de controlo de acesso (ACL) que mantém. A ACL granularidade e método podem variar significativamente entre os recursos.

Um caso de uso comum é usar uma ACL para executar testes de um aplicativo web ou para uma API web. A API web pode conceder apenas um subconjunto de todas as permissões para um cliente específico. Para executar testes de ponta a ponta na API, crie um cliente de teste que adquire tokens a partir do ponto final v2.0 e, em seguida, envia-os para a API. A API, em seguida, verifica a ACL para o ID de aplicação do cliente de teste para acesso completo à funcionalidade de toda a API. Se usar esse tipo de ACL, certifique-se de que validar não apenas do chamador `appid` valor, mas também validar que o `iss` o valor do token é confiável.

Este tipo de autorização é comum daemons e contas de serviço que precisam de aceder a dados pertencentes a utilizadores de consumidor com contas pessoais da Microsoft. Para os dados pertencentes a organizações, é recomendável que obtém a autorização necessária através de permissões de aplicação.

### <a name="application-permissions"></a>Permissões de aplicações

Em vez de utilizar ACLs, pode usar APIs para expor um conjunto de permissões de aplicação. Uma permissão de aplicação é concedida a uma aplicação ao administrador da organização e pode ser utilizada apenas para aceder a dados pertencentes a essa organização e seus funcionários. Por exemplo, o Microsoft Graph expõe várias permissões de aplicação para fazer o seguinte:

* Ler correio em todas as caixas de correio
* Ler e escrever correio em todas as caixas de correio
* Enviar e-mails como um utilizador qualquer
* Ler dados do diretório

Para obter mais informações sobre as permissões de aplicação, aceda a [Microsoft Graph](https://developer.microsoft.com/graph).

Para utilizar permissões de aplicação na sua aplicação, siga as etapas abordadas nas secções seguintes.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar as permissões no portal de registo de aplicação

1. Registar e criar uma aplicação através da [portal de registo de aplicação](quickstart-v2-register-an-app.md) ou no novo [experiência de registos de aplicações (pré-visualização)](quickstart-register-app.md).
1. Aceda à sua aplicação no portal do que utilizou para se registar ou criar a sua aplicação. Terá de utilizar pelo menos um segredo de aplicação, ao criar a sua aplicação.
1. Localize a **permissões de API** secção e, em seguida, adicione o **permissões de aplicação** que a sua aplicação necessita.
1. **Guardar** o registo de aplicações.

#### <a name="recommended-sign-the-user-in-to-your-app"></a>Recomendado: Sessão do utilizador para a sua aplicação

Normalmente, quando cria uma aplicação que utiliza as permissões de aplicação, a aplicação requer uma vista em que o administrador aprova as permissões da aplicação ou página. Nesta página pode fazer parte de iniciar sessão no flow a aplicação, parte integrante das definições da aplicação, ou pode ser um fluxo de "ligar" dedicada. Em muitos casos, faz sentido para a aplicação mostre isso "ligar" modo de exibição apenas depois de um utilizador ter iniciado sessão com uma empresa ou escola conta Microsoft.

Se o utilizador inicia sessão à sua aplicação, pode identificar a organização à qual o utilizador pertence a antes de solicita ao usuário aprovar as permissões de aplicação. Embora não seja estritamente necessário, ele pode ajudá-lo a criar uma experiência mais intuitiva para os seus utilizadores. Para iniciar a sessão do utilizador no, siga nosso [tutoriais de protocolo de v2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar as permissões de um administrador do diretório

Quando estiver pronto para solicitar permissões de administrador da organização, pode redirecionar o utilizador para a versão 2.0 *ponto final de consentimento de administração*.

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
| `tenant` | Necessário | O inquilino do diretório que pretende pedir permissão do. Isso pode ser no formato de nome amigável ou de GUID. Se não sabe qual inquilino pertence o usuário e pretende que os mesmos possam iniciar sessão com qualquer inquilino, utilize `common`. |
| `client_id` | Necessário | O ID de aplicação (cliente) atribuído à sua aplicação. Pode encontrar estas informações no portal onde registado a sua aplicação. |
| `redirect_uri` | Necessário | O URI de redirecionamento do qual pretende que a resposta a serem enviados para a sua aplicação processar. Ele deve corresponder exatamente um dos URIs que registou no portal de redirecionamento, exceto pelo fato de que tem de ser codificado de URL, e pode ter segmentos de caminho adicionais. |
| `state` | Recomendado | Um valor que está incluído no pedido que também é devolvido na resposta de token. Pode ser uma cadeia de caracteres de qualquer conteúdo que pretende. O estado é usado para codificar as informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, como a página ou a vista estivessem na. |

Neste momento, o Azure AD impõe que apenas um administrador de inquilino pode iniciar sessão para concluir o pedido. O administrador será pedido para aprovar todas as permissões de aplicação direta que solicitou para a sua aplicação no portal de registo de aplicação.

##### <a name="successful-response"></a>Resposta com êxito

Se o administrador aprova as permissões para a sua aplicação, a resposta com êxito esta aparência:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| --- | --- |
| `tenant` | O inquilino do diretório concedidas as permissões pedidos, no formato GUID de seu aplicativo. |
| `state` | Um valor que está incluído no pedido que também é devolvido na resposta de token. Pode ser uma cadeia de caracteres de qualquer conteúdo que pretende. O estado é usado para codificar as informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, como a página ou a vista estivessem na. |
| `admin_consent` | Defina como **True**. |

##### <a name="error-response"></a>Resposta de erro

Se o administrador não aprova as permissões para a sua aplicação, a resposta com falhas é semelhante a isto:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de caracteres de código de erro que pode usar para classificar tipos de erros, e o que pode utilizar para reagir a erros. |
| `error_description` | Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro. |

Depois que recebeu uma resposta com êxito a partir do ponto de extremidade de aprovisionamento de aplicação, a aplicação obteve as permissões de aplicação direta pedidos. Agora pode solicitar um token para o recurso que pretende.

## <a name="get-a-token"></a>Obter um token

Depois de adquirir a autorização necessária para a sua aplicação, continue com a aquisição de tokens de acesso para as APIs. Para obter um token com o cliente de concessão de credenciais, envie um pedido POST para o `/token` ponto final v2.0:

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Pedido de token de acesso com um segredo partilhado

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
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Necessário | O inquilino do diretório do aplicativo planos operar em relação a, no formato de nome de domínio ou de GUID. |
| `client_id` | Necessário | O ID de aplicação que está atribuído à sua aplicação. Pode encontrar estas informações no portal onde registado a sua aplicação. |
| `scope` | Necessário | O valor transmitido para o `scope` parâmetro neste pedido deve ser o identificador de recurso (o URI de ID da aplicação) do recurso que pretende, afixação com o `.default` sufixo. No exemplo do Microsoft Graph, o valor é `https://graph.microsoft.com/.default`. </br>Este valor indica o ponto final v2.0 que todos os aplicativos direto das permissões de que configurou para a sua aplicação, o ponto final deve emitir um token para aquelas associadas com o recurso que pretende utilizar. Para saber mais sobre o `/.default` escopo, consulte a [consentimento documentação](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Necessário | O segredo de aplicação que gerou para a sua aplicação no portal de registo de aplicação. O segredo do cliente tem de ser codificados de URL antes de serem enviados. |
| `grant_type` | Necessário | Tem de ser definido como `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Pedido de token de acesso com um certificado

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
| `tenant` | Necessário | O inquilino do diretório do aplicativo planos operar em relação a, no formato de nome de domínio ou de GUID. |
| `client_id` | Necessário |O ID de aplicação (cliente) atribuído à sua aplicação. |
| `scope` | Necessário | O valor transmitido para o `scope` parâmetro neste pedido deve ser o identificador de recurso (o URI de ID da aplicação) do recurso que pretende, afixação com o `.default` sufixo. No exemplo do Microsoft Graph, o valor é `https://graph.microsoft.com/.default`. <br>Este valor informa o ponto final v2.0 que todos os aplicativos direto das permissões de que configurou para a sua aplicação, que ele deverá emitir um token para aquelas associadas com o recurso que pretende utilizar. Para saber mais sobre o `/.default` escopo, consulte a [consentimento documentação](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Necessário | O valor tem de ser definido `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Necessário | Uma asserção (um token de web JSON) que precisa para criar e assinar com o certificado é registado como as credenciais para a sua aplicação. Leia sobre [credenciais de certificado](active-directory-certificate-credentials.md) para saber como registar o seu certificado e o formato da asserção.|
| `grant_type` | Necessário | Tem de ser definido como `client_credentials`. |

Tenha em atenção que os parâmetros são quase os mesmos que no caso do pedido de segredo partilhado, exceto que o parâmetro client_secret é substituído por dois parâmetros: um client_assertion_type e client_assertion.

### <a name="successful-response"></a>Resposta com êxito

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
| `access_token` | O token de acesso solicitado. A aplicação pode utilizar este token para autenticar para o recurso protegido, por exemplo, para uma API Web. |
| `token_type` | Indica o valor de tipo de token. O único tipo, que é o Azure AD suporta `bearer`. |
| `expires_in` | A quantidade de tempo que um token de acesso é válido (em segundos). |

### <a name="error-response"></a>Resposta de erro

Uma resposta de erro tem esta aparência:

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
| `error` | Uma cadeia de código de erro que pode usar para classificar tipos de erros que ocorrem e reagir a erros. |
| `error_description` | Uma mensagem de erro específicas que pode ajudá-lo a identificar a causa de raiz de um erro de autenticação. |
| `error_codes` | Uma lista de códigos de erro do STS específicas que poderá ajudar com o diagnóstico. |
| `timestamp` | A hora em que ocorreu o erro. |
| `trace_id` | Um identificador exclusivo para o pedido para o ajudar a com o diagnóstico. |
| `correlation_id` | Um identificador exclusivo para o pedido para com o diagnóstico de componentes. |

## <a name="use-a-token"></a>Utilizar um token

Agora que adquirir um token, utilize o token para fazer pedidos para o recurso. Quando o token expira, repita o pedido para o `/token` ponto final para adquirir um token de acesso de novo.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Exemplos de código e outra documentação

Leitura a [documentação de visão geral de credenciais de cliente](https://aka.ms/msal-net-client-credentials) partir da biblioteca de autenticação da Microsoft

| Sample | Plataforma |Descrição |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | Consola do .NET core 2.1 | Uma aplicação .NET Core simple que apresenta os utilizadores de um inquilino do Microsoft Graph com a identidade da aplicação, em vez de nome de um utilizador a consultar. O exemplo também ilustra a variação utilizar certificados para autenticação. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Um aplicativo web que sincroniza os dados do Microsoft Graph com a identidade da aplicação, em vez de nome de um utilizador. |
