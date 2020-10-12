---
title: As credenciais de cliente da OAuth 2.0 fluem na plataforma de identidade da Microsoft Rio Azure
description: Construa aplicações web utilizando a implementação da plataforma de identidade da Microsoft do protocolo de autenticação OAuth 2.0.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 7/27/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e5fe8e751077bc04850879d27827c197767a81c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87759075"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Plataforma de identidade da Microsoft e fluxo de credenciais de clientes OAuth 2.0

Você pode usar a concessão de credenciais de [cliente OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.4) especificada no RFC 6749, às vezes chamado *OAuth de duas pernas,* para aceder a recursos hospedados na Web usando a identidade de uma aplicação. Este tipo de concessão é comumente usado para interações servidor-a-servidor que devem ser executadas em segundo plano, sem interação imediata com um utilizador. Este tipo de aplicações são muitas vezes referidas como *daemons* ou *contas de serviço.*

Este artigo descreve como programar diretamente contra o protocolo na sua aplicação. Quando possível, recomendamos que utilize as Bibliotecas de Autenticação da Microsoft (MSAL) suportadas para [adquirir fichas e chamar APIs web seguras](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Dê também uma olhada nas aplicações de [amostra que utilizam o MSAL.](sample-v2-code.md)

O OAuth 2.0 concede o fluxo de credenciais ao cliente que permite que um serviço web (cliente confidencial) utilize as suas próprias credenciais, em vez de se fazer passar por utilizador, para autenticar quando liga para outro serviço web. Neste cenário, o cliente é tipicamente um serviço web de nível médio, um serviço daemon ou um site. Para um nível de garantia mais elevado, a plataforma de identidade da Microsoft também permite que o serviço de chamadas utilize um certificado (em vez de um segredo partilhado) como credencial.

No fluxo de credenciais do cliente, as permissões são concedidas diretamente ao próprio pedido por um administrador. Quando a aplicação apresenta um símbolo a um recurso, o recurso reforça que a própria app tem autorização para realizar uma ação, uma vez que não existe nenhum utilizador envolvido na autenticação.  Este artigo abrange ambas as etapas necessárias para [autorizar um pedido de chamada API,](#application-permissions)bem como como obter os [tokens necessários para chamar a essa API.](#get-a-token)

## <a name="protocol-diagram"></a>Diagrama de protocolo

Todo o fluxo de credenciais do cliente se parece com o diagrama seguinte. Descrevemos cada um dos passos mais tarde neste artigo.

![Diagrama mostrando o fluxo de credenciais do cliente](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Obtenha autorização direta

Uma aplicação normalmente recebe autorização direta para aceder a um recurso de uma de duas maneiras:

* [Através de uma lista de controlo de acesso (ACL) no recurso](#access-control-lists)
* [Através da atribuição de permissão de candidatura em Azure AD](#application-permissions)

Estes dois métodos são os mais comuns na Azure AD e recomendamo-los para clientes e recursos que executam o fluxo de credenciais do cliente. Um recurso também pode optar por autorizar os seus clientes de outras formas. Cada servidor de recursos pode escolher o método que faz mais sentido para a sua aplicação.

### <a name="access-control-lists"></a>Lista de controlo de acesso

Um fornecedor de recursos pode impor um controlo de autorização com base numa lista de IDs de aplicação (cliente) a que conhece e concede um nível específico de acesso. Quando o recurso recebe um token do ponto final da plataforma de identidade da Microsoft, pode descodificar o token e extrair o ID da aplicação do cliente a partir do `appid` e `iss` alegações. Em seguida, compara a aplicação com uma lista de controlo de acesso (ACL) que mantém. A granularidade e o método da ACL podem variar substancialmente entre os recursos.

Um caso de uso comum é usar um ACL para executar testes para uma aplicação web ou para uma API web. A API web pode conceder apenas um subconjunto de permissões completas a um cliente específico. Para realizar testes de ponta a ponta na API, crie um cliente de teste que adquira fichas a partir do ponto final da plataforma de identidade da Microsoft e, em seguida, envie-os para a API. Em seguida, a API verifica a ACL para o ID de aplicação do cliente de teste para acesso total a toda a funcionalidade da API. Se utilizar este tipo de ACL, certifique-se de validar não só o valor do autor da `appid` chamada, mas também validar que `iss` o valor do token é de confiança.

Este tipo de autorização é comum para daemons e contas de serviço que precisam de aceder a dados pertencentes a utilizadores de consumidores que tenham contas pessoais da Microsoft. Para os dados pertencentes a organizações, recomendamos que obtenha a autorização necessária através de permissões de aplicação.

> [!NOTE]
> Para permitir este padrão de autorização baseado em ACL, a Azure AD não requer que as aplicações sejam autorizadas a obter fichas para outra aplicação - para que os tokens apenas de aplicações possam ser emitidos sem `roles` reclamação. As aplicações que expõem ASDis devem implementar controlos de permissão para aceitar fichas.

### <a name="application-permissions"></a>Permissões de inscrição

Em vez de utilizar ACLs, pode utilizar APIs para expor um conjunto de permissões de **aplicação**. Uma permissão de pedido é concedida a um pedido pelo administrador de uma organização, e pode ser usada apenas para aceder a dados pertencentes a essa organização e seus funcionários. Por exemplo, o Microsoft Graph expõe várias permissões de aplicação para fazer o seguinte:

* Leia o correio em todas as caixas de correio
* Leia e escreva correio em todas as caixas de correio
* Enviar correio como qualquer utilizador
* Ler os dados do diretório

Para utilizar permissões de aplicação com a sua própria API (ao contrário do Microsoft Graph), tem primeiro de [expor a API](quickstart-configure-app-expose-web-apis.md) definindo os âmbitos no registo de aplicações da API no portal Azure. Em seguida, [configurar o acesso à API](quickstart-configure-app-access-web-apis.md) selecionando essas permissões no registo de aplicações da sua aplicação do cliente. Caso não tenha exposto quaisquer âmbitos no registo da sua app da API, não poderá especificar permissões de aplicação para essa API no registo da aplicação do seu cliente no portal Azure.

Ao autenticar como uma aplicação (ao contrário de um utilizador), não pode utilizar *permissões delegadas* - âmbitos que são concedidos por um utilizador. Deve utilizar permissões de aplicação, também conhecidas como funções, que são concedidas por um administrador para a aplicação ou por pré-autorização pela API web.

Para mais informações sobre permissões de aplicação, consulte [permissões e consentimento.](v2-permissions-and-consent.md#permission-types)

#### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: Inscreva o utilizador na sua aplicação

Normalmente, quando constrói uma aplicação que utiliza permissões de aplicação, a aplicação requer uma página ou vista na qual o administrador aprova as permissões da aplicação. Esta página pode fazer parte do fluxo de entrada da aplicação, parte das definições da aplicação, ou pode ser um fluxo dedicado de "connect". Em muitos casos, faz sentido que a app mostre esta vista de "conectar" apenas depois de um utilizador ter assinado com uma conta microsoft de trabalho ou escola.

Se assinar o utilizador na sua aplicação, pode identificar a organização a que o utilizador pertence antes de pedir ao utilizador que aprove as permissões da aplicação. Embora não seja estritamente necessário, pode ajudá-lo a criar uma experiência mais intuitiva para os seus utilizadores. Para iniciar sômsim do utilizador, siga os [nossos tutoriais de protocolo de plataforma de identidade da Microsoft.](active-directory-v2-protocols.md)

#### <a name="request-the-permissions-from-a-directory-admin"></a>Solicite as permissões a um administrador de diretório

Quando estiver pronto para solicitar permissões do administrador da organização, pode redirecionar o utilizador para o *ponto final de consentimento da*plataforma de identidade da Microsoft.

> [!TIP]
> Tente executar este pedido no Carteiro! (Use o seu próprio ID de aplicação para obter os melhores resultados - a aplicação tutorial não solicitará permissões úteis.) [ ![ Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

Dica pro: Tente colar o seguinte pedido num browser.

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | O inquilino do diretório que quer pedir autorização. Isto pode ser em formato GUIADO ou friendly name. Se você não sabe a que inquilino o utilizador pertence e você quer deixá-los entrar com qualquer inquilino, use `common` . |
| `client_id` | Necessário | O **ID da Aplicação (cliente)** que o [portal Azure – Experiência de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuído à sua app. |
| `redirect_uri` | Necessário | O URI de redirecionamento onde deseja que a resposta seja enviada para que a sua aplicação seja tratada. Deve corresponder exatamente a um dos URIs de redirecionamento que registou no portal, exceto que deve ser codificado URL, e pode ter segmentos de caminho adicionais. |
| `state` | Recomendado | Um valor que está incluído no pedido que também é devolvido na resposta simbólica. Pode ser uma série de conteúdos que queiras. O Estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou a visualização em que se encontravam. |

Neste momento, a Azure AD impõe que apenas um administrador inquilino pode assinar o pedido completo. O administrador será solicitado a aprovar todas as permissões de aplicação direta que solicitou para a sua aplicação no portal de registo de aplicações.

##### <a name="successful-response"></a>Resposta bem sucedida

Se o administrador aprovar as permissões para a sua aplicação, a resposta bem sucedida é a seguinte:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| --- | --- |
| `tenant` | O inquilino do diretório que concedeu ao seu pedido as permissões que solicitou, em formato GUID. |
| `state` | Um valor que está incluído no pedido que também é devolvido na resposta simbólica. Pode ser uma série de conteúdos que queiras. O Estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou a visualização em que se encontravam. |
| `admin_consent` | Definido para **Verdadeiro**. |

##### <a name="error-response"></a>Resposta de erro

Se o administrador não aprovar as permissões para a sua aplicação, a resposta falhada é a seguinte:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de código de erro que pode utilizar para classificar tipos de erros e que pode usar para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro. |

Depois de ter recebido uma resposta bem sucedida do ponto final de fornecimento de aplicações, a sua aplicação obteve as permissões de aplicação direta que solicitou. Agora pode pedir um sinal para o recurso que quiser.

## <a name="get-a-token"></a>Obter um símbolo

Depois de ter adquirido a autorização necessária para o seu pedido, prossiga com a aquisição de fichas de acesso para APIs. Para obter um token utilizando a concessão de credenciais de cliente, envie um pedido POST para o ponto final da plataforma de identidade da `/token` Microsoft:

> [!TIP]
> Tente executar este pedido no Carteiro! (Use o seu próprio ID de aplicação para obter os melhores resultados - a aplicação tutorial não solicitará permissões úteis.) [ ![ Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Pedido de acesso a um sinal com um segredo partilhado

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```Bash
# Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | O inquilino do diretório planeia operar contra, em formato GUID ou nome de domínio. |
| `client_id` | Necessário | A identificação da aplicação que está atribuída à sua aplicação. Pode encontrar esta informação no portal onde registou a sua aplicação. |
| `scope` | Necessário | O valor passado para o `scope` parâmetro neste pedido deve ser o identificador de recursos (ID URI de aplicação) do recurso que pretende, afixado com o `.default` sufixo. Para o exemplo do Microsoft Graph, o valor é `https://graph.microsoft.com/.default` . <br/>Este valor indica que, de todas as permissões de aplicação direta configuradas para a sua aplicação, o ponto final deverá emitir um token para os que estão associados ao recurso que pretende utilizar. Para saber mais sobre o `/.default` âmbito, consulte a documentação de [consentimento.](v2-permissions-and-consent.md#the-default-scope) |
| `client_secret` | Necessário | O segredo do cliente que gerou para a sua aplicação no portal de registo de aplicações. O segredo do cliente deve ser codificado por URL antes de ser enviado. |
| `grant_type` | Necessário | Deve ser definido para `client_credentials` . |

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Pedido de token de acesso com certificado

```HTTP
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
| `tenant` | Obrigatório | O inquilino do diretório planeia operar contra, em formato GUID ou nome de domínio. |
| `client_id` | Necessário |O ID da aplicação (cliente) que está atribuído à sua aplicação. |
| `scope` | Necessário | O valor passado para o `scope` parâmetro neste pedido deve ser o identificador de recursos (ID URI de aplicação) do recurso que pretende, afixado com o `.default` sufixo. Para o exemplo do Microsoft Graph, o valor é `https://graph.microsoft.com/.default` . <br/>Este valor informa que, de todas as permissões de aplicação direta que configura para a sua aplicação, deverá emitir um token para os que estão associados ao recurso que pretende utilizar. Para saber mais sobre o `/.default` âmbito, consulte a documentação de [consentimento.](v2-permissions-and-consent.md#the-default-scope) |
| `client_assertion_type` | Necessário | O valor deve ser definido para `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` . |
| `client_assertion` | Necessário | Uma afirmação (um token web JSON) que precisa de criar e assinar com o certificado que registou como credenciais para a sua aplicação. Leia sobre [as credenciais de certificado](active-directory-certificate-credentials.md) para saber como registar o seu certificado e o formato da afirmação.|
| `grant_type` | Necessário | Deve ser definido para `client_credentials` . |

Note que os parâmetros são quase os mesmos que no caso do pedido por segredo partilhado, exceto que o parâmetro client_secret é substituído por dois parâmetros: um client_assertion_type e client_assertion.

### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta com êxito tem o seguinte aspeto:

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parâmetro | Descrição |
| --- | --- |
| `access_token` | O sinal de acesso solicitado. A aplicação pode usar este token para autenticar o recurso seguro, como por exemplo uma API web. |
| `token_type` | Indica o valor do tipo símbolo. O único tipo que a plataforma de identidade da Microsoft suporta é `bearer` . |
| `expires_in` | A quantidade de tempo que um token de acesso é válido (em segundos). |

### <a name="error-response"></a>Resposta de erro

Uma resposta de erro é assim:

```json
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
| `error` | Uma cadeia de código de erro que pode utilizar para classificar tipos de erros que ocorrem e para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |
| `error_codes` | Uma lista de códigos de erro específicos do STS que podem ajudar no diagnóstico. |
| `timestamp` | A hora em que ocorreu o erro. |
| `trace_id` | Um identificador único para o pedido de ajuda com diagnósticos. |
| `correlation_id` | Um identificador único para o pedido de ajuda com diagnósticos em todos os componentes. |

## <a name="use-a-token"></a>Use um símbolo

Agora que adquiriu um símbolo, use o símbolo para fazer pedidos ao recurso. Quando o token expirar, repita o pedido para o `/token` ponto final para adquirir um novo token de acesso.

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```bash
# Pro tip: Try the following command! (Replace the token with your own.)

curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Amostras de código e outra documentação

Leia a [documentação geral](https://aka.ms/msal-net-client-credentials) das credenciais do cliente da Biblioteca de Autenticação do Microsoft

| Sample | Plataforma |Descrição |
|--------|----------|------------|
|[active-directório-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2.1 Consola | Uma aplicação simples .NET Core que exibe os utilizadores de um inquilino que consulta o Microsoft Graph utilizando a identidade da aplicação, em vez de em nome de um utilizador. A amostra também ilustra a variação utilizando certificados para autenticação. |
|[active-directório-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Uma aplicação web que sincroniza dados a partir do Gráfico do Microsoft utilizando a identidade da aplicação, em vez de em nome de um utilizador. |
