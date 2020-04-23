---
title: As credenciais de clientes OAuth 2.0 fluem na plataforma de identidade da Microsoft Azure
description: Construa aplicações web utilizando a implementação da plataforma de identidade Microsoft do protocolo de autenticação OAuth 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 36a5fdf990432e3a41cf8fc578fa20b4910250b2
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868444"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Plataforma de identidade microsoft e o fluxo de credenciais de clientes OAuth 2.0

Você pode usar a bolsa de credenciais de [cliente OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.4) especificada no RFC 6749, por vezes chamado *OAuth de duas pernas,* para aceder a recursos web hospedados usando a identidade de uma aplicação. Este tipo de subvenção é comumente usado para interações servidor-servidor que devem ser executados em segundo plano, sem interação imediata com um utilizador. Estes tipos de aplicações são muitas vezes referidos como *daemons* ou contas de *serviço.*

Este artigo descreve como programar diretamente contra o protocolo na sua aplicação. Sempre que possível, recomendamos que utilize as Bibliotecas de Autenticação da Microsoft (MSAL) suportadas em vez de adquirir fichas e ligar para [APIs web protegidos](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Veja também as [aplicações de amostra que utilizam o MSAL.](sample-v2-code.md)

As credenciais de cliente OAuth 2.0 concedem fluxo de fluxo permite que um serviço web (cliente confidencial) utilize as suas próprias credenciais, em vez de se fazer passar por utilizador, para autenticar quando chama outro serviço web. Neste cenário, o cliente é tipicamente um serviço web de nível médio, um serviço daemon ou um web site. Para um nível mais elevado de garantia, a plataforma de identidade da Microsoft também permite que o serviço de chamadas utilize um certificado (em vez de um segredo partilhado) como credencial.

No *OAuth*de três patas mais típico, é concedida permissão a um pedido de cliente para aceder a um recurso em nome de um utilizador específico. A permissão é delegada do utilizador para a aplicação, geralmente durante o processo de [consentimento.](v2-permissions-and-consent.md) No entanto, nas credenciais do cliente (*OAuth de duas pernas*), as permissões são concedidas diretamente à própria aplicação. Quando a aplicação apresenta um símbolo a um recurso, o recurso impõe que a própria app tenha autorização para realizar uma ação e não o utilizador.

## <a name="protocol-diagram"></a>Diagrama de protocolo

O fluxo de credenciais de cliente inteiro parece semelhante ao diagrama seguinte. Descrevemos cada um dos passos mais tarde neste artigo.

![Diagrama mostrando o fluxo de credenciais do cliente](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Obtenha autorização direta

Uma aplicação normalmente recebe autorização direta para aceder a um recurso de uma de duas maneiras:

* [Através de uma lista de controlo de acesso (ACL) no recurso](#access-control-lists)
* [Através de atribuição de permissão de pedido em Azure AD](#application-permissions)

Estes dois métodos são os mais comuns em Azure AD e recomendamo-los para clientes e recursos que executam o fluxo de credenciais de cliente. Um recurso também pode optar por autorizar os seus clientes de outras formas. Cada servidor de recursos pode escolher o método que faz mais sentido para a sua aplicação.

### <a name="access-control-lists"></a>Lista de controlo de acesso

Um fornecedor de recursos pode impor um controlo de autorização com base numa lista de iDs de aplicação (cliente) que conhece e concede um nível específico de acesso. Quando o recurso recebe um símbolo do ponto final da plataforma de identidade da Microsoft, pode `appid` descodificar o símbolo e extrair o ID da aplicação do cliente do e sinistros. `iss` Em seguida, compara a aplicação com uma lista de controlo de acesso (ACL) que mantém. A granularidade e o método da ACL podem variar substancialmente entre recursos.

Um caso de uso comum é usar um ACL para executar testes para uma aplicação web ou para uma API web. A API web pode conceder apenas um subconjunto de permissões completas a um cliente específico. Para realizar testes de ponta a ponta na API, crie um cliente de teste que adquira fichas do ponto final da plataforma de identidade da Microsoft e, em seguida, envie-os para a API. A API verifica então a ACL para obter o ID de aplicação do cliente de teste para acesso total a toda a funcionalidade da API. Se utilizar este tipo de ACL, certifique-se de `appid` validar não `iss` só o valor do chamador, mas também validar que o valor do token é de confiança.

Este tipo de autorização é comum para daemons e contas de serviço que precisam de aceder a dados de utilizadores de consumo que tenham contas pessoais da Microsoft. Para dados pertencentes a organizações, recomendamos que obtenha a autorização necessária através de permissões de pedido.

### <a name="application-permissions"></a>Permissões de candidatura

Em vez de utilizar ACLs, pode utilizar APIs para expor um conjunto de **permissões**de aplicação . Uma autorização de pedido é concedida a um pedido pelo administrador de uma organização, e só pode ser usada para aceder a dados pertencentes a essa organização e aos seus colaboradores. Por exemplo, o Microsoft Graph expõe várias permissões de aplicação para fazer o seguinte:

* Leia o correio em todas as caixas de correio
* Ler e escrever correio em todas as caixas de correio
* Enviar correio como qualquer utilizador
* Ler os dados do diretório

Para mais informações sobre permissões de aplicação, vá ao [Microsoft Graph](https://developer.microsoft.com/graph).

Para utilizar permissões de aplicação na sua aplicação, siga os passos discutidos nas secções seguintes.


> [!NOTE]
> Ao autenticar como aplicação, ao contrário de um utilizador, não é possível utilizar "permissões delegadas" (âmbitos concedidos por um utilizador).  Deve utilizar "permissões de pedido", também conhecidas como "funções", que são concedidas por um administrador para o pedido (ou através de pré-autorização pela Web API).


#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicite as permissões no portal de registo da aplicação

1. Registe-se e crie uma aplicação através da nova experiência de registos de [aplicações (Pré-visualização).](quickstart-register-app.md)
2. Vá à sua aplicação na experiência de registos da App (Pré-visualização). Navegue para a secção **de Certificados & segredos,** e adicione um novo segredo de **cliente,** porque precisará de pelo menos um segredo de cliente para solicitar um símbolo.
3. Localize a secção de **permissões da API** e, em seguida, adicione as **permissões** de aplicação que a sua aplicação necessita.
4. **Guarde** o registo da aplicação.

#### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: Inscreva o utilizador na sua aplicação

Normalmente, quando se constrói uma aplicação que utiliza permissões de aplicação, a aplicação requer uma página ou visualização na qual o administrador aprova as permissões da aplicação. Esta página pode fazer parte do fluxo de entrada da aplicação, parte das definições da aplicação, ou pode ser um fluxo dedicado de "connect". Em muitos casos, faz sentido que a app mostre esta visão de "connect" apenas depois de um utilizador ter assinado com uma conta de trabalho ou escola da Microsoft.

Se assinar o utilizador na sua aplicação, pode identificar a organização a que o utilizador pertence antes de pedir ao utilizador que aprove as permissões da aplicação. Embora não seja estritamente necessário, pode ajudá-lo a criar uma experiência mais intuitiva para os seus utilizadores. Para iniciar sessão com o utilizador, siga os [nossos tutoriais](active-directory-v2-protocols.md)de protocolo de plataforma de identidade da Microsoft .

#### <a name="request-the-permissions-from-a-directory-admin"></a>Solicite as permissões de um administrador de diretório

Quando estiver pronto para solicitar permissões ao administrador da organização, pode redirecionar o utilizador para o *ponto final*de consentimento da plataforma de identidade da Microsoft .

> [!TIP]
> Tente executar este pedido no Carteiro! (Use o seu próprio ID de aplicação para obter os melhores resultados - a aplicação tutorial não solicitará permissões úteis.) [Tente executar este pedido no Carteiro ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

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
| `tenant` | Necessário | O inquilino do diretório de quem quer pedir autorização. Isto pode ser em formato DE NOME GUIA ou amigável. Se não sabe a que inquilino pertence o utilizador e quer deixá-los `common`assinar com qualquer inquilino, use. |
| `client_id` | Necessário | O ID de **Aplicação (cliente)** que o [portal Azure – App registra](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída à sua app. |
| `redirect_uri` | Necessário | O URI redireciona do URI onde pretende que a resposta seja enviada para a sua aplicação manusear. Deve corresponder exatamente a uma das URIs redirecionais que registou no portal, exceto que deve ser codificada por URL, e pode ter segmentos de caminho adicionais. |
| `state` | Recomendado | Um valor que está incluído no pedido que também é devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que queira. O Estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que se encontrava. |

Neste momento, a Azure AD impõe que apenas um administrador de inquilino pode assinar o pedido completo. O administrador será solicitado a aprovar todas as permissões de aplicação direta que solicitou para a sua aplicação no portal de registo da aplicação.

##### <a name="successful-response"></a>Resposta bem sucedida

Se o administrador aprovar as permissões para a sua aplicação, a resposta bem sucedida é a seguinte:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| --- | --- |
| `tenant` | O inquilino de diretório que concedeu ao seu pedido as permissões que solicitou, em formato GUID. |
| `state` | Um valor que está incluído no pedido que também é devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que queira. O Estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que se encontrava. |
| `admin_consent` | Definido para **Verdadeiro**. |

##### <a name="error-response"></a>Resposta de erro

Se o administrador não aprovar as permissões para o seu pedido, a resposta falhada é a seguinte:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de código de erro que pode usar para classificar tipos de erros e que pode usar para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro. |

Depois de ter recebido uma resposta bem-sucedida do ponto final do fornecimento de aplicações, a sua aplicação obteve as permissões de aplicação direta que solicitou. Agora pode pedir um sinal para o recurso que quiser.

## <a name="get-a-token"></a>Pegue um símbolo

Depois de ter adquirido a autorização necessária para o seu pedido, proceda à aquisição de fichas de acesso para APIs. Para obter um sinal através da concessão de credenciais de cliente, envie um pedido POST para o ponto final da `/token` plataforma de identidade da Microsoft:

> [!TIP]
> Tente executar este pedido no Carteiro! (Use o seu próprio ID de aplicação para obter os melhores resultados - a aplicação tutorial não solicitará permissões úteis.) [Tente executar este pedido no Carteiro ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Pedido de acesso com um segredo partilhado

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
| `tenant` | Necessário | O inquilino do diretório a aplicação planeia operar contra, em formato GUID ou nome de domínio. |
| `client_id` | Necessário | O ID da aplicação que é atribuído à sua aplicação. Pode encontrar esta informação no portal onde registou a sua aplicação. |
| `scope` | Necessário | O valor passado `scope` para o parâmetro neste pedido deve ser o identificador de recursos (ID `.default` URI de aplicação) do recurso que pretende, afixado com o sufixo. Para o exemplo do Microsoft `https://graph.microsoft.com/.default`Graph, o valor é . <br/>Este valor diz à plataforma de identidade da Microsoft que de todas as permissões de aplicação direta que configuraste para a sua aplicação, o ponto final deve emitir um símbolo para os associados ao recurso que pretende utilizar. Para saber mais `/.default` sobre o âmbito, consulte a documentação do [consentimento.](v2-permissions-and-consent.md#the-default-scope) |
| `client_secret` | Necessário | O segredo do cliente que gerou para a sua aplicação no portal de registo de aplicações. O segredo do cliente deve ser codificado antes de ser enviado. |
| `grant_type` | Necessário | Tem de `client_credentials`ser definido para. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Pedido de acesso aceso com certificado

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
| `tenant` | Necessário | O inquilino do diretório a aplicação planeia operar contra, em formato GUID ou nome de domínio. |
| `client_id` | Necessário |O ID de aplicação (cliente) que é atribuído à sua aplicação. |
| `scope` | Necessário | O valor passado `scope` para o parâmetro neste pedido deve ser o identificador de recursos (ID `.default` URI de aplicação) do recurso que pretende, afixado com o sufixo. Para o exemplo do Microsoft `https://graph.microsoft.com/.default`Graph, o valor é . <br/>Este valor informa o ponto final da plataforma de identidade da Microsoft que de todas as permissões de aplicação direta que configurapara a sua aplicação, deve emitir um símbolo para os associados ao recurso que pretende utilizar. Para saber mais `/.default` sobre o âmbito, consulte a documentação do [consentimento.](v2-permissions-and-consent.md#the-default-scope) |
| `client_assertion_type` | Necessário | O valor deve `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`ser fixado para . |
| `client_assertion` | Necessário | Uma afirmação (um símbolo web JSON) que precisa de criar e assinar com o certificado que registou como credenciais para a sua aplicação. Leia sobre [credenciais](active-directory-certificate-credentials.md) de certificado para saber como registar o seu certificado e o formato da afirmação.|
| `grant_type` | Necessário | Tem de `client_credentials`ser definido para. |

Note-se que os parâmetros são quase os mesmos que no caso do pedido por segredo partilhado, exceto que o parâmetro client_secret é substituído por dois parâmetros: um client_assertion_type e client_assertion.

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
| `access_token` | O sinal de acesso solicitado. A aplicação pode usar este símbolo para autenticar o recurso seguro, como por exemplo a uma API web. |
| `token_type` | Indica o valor do tipo simbólico. O único tipo que a `bearer`plataforma de identidade da Microsoft suporta é . |
| `expires_in` | O tempo que um token de acesso é válido (em segundos). |

### <a name="error-response"></a>Resposta de erro

Uma resposta de erro é a seguinte:

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
| `error` | Uma cadeia de código de erro que pode usar para classificar tipos de erros que ocorrem e para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |
| `error_codes` | Uma lista de códigos de erro específicos do STS que podem ajudar nos diagnósticos. |
| `timestamp` | O momento em que ocorreu o erro. |
| `trace_id` | Um identificador único para o pedido de ajuda com diagnósticos. |
| `correlation_id` | Um identificador único para o pedido de ajuda com diagnósticos em todos os componentes. |

## <a name="use-a-token"></a>Use um símbolo

Agora que adquiriu um símbolo, use o símbolo para fazer pedidos ao recurso. Quando o símbolo expirar, repita o `/token` pedido para o ponto final para adquirir um novo sinal de acesso.

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

Leia as credenciais do [cliente sobrea documentação](https://aka.ms/msal-net-client-credentials) da Biblioteca de Autenticação da Microsoft

| Sample | Plataforma |Descrição |
|--------|----------|------------|
|[active-directy-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2.1 Consola | Uma aplicação simples .NET Core que exibe os utilizadores de um inquilino que consulta o Microsoft Graph usando a identidade da aplicação, em vez de em nome de um utilizador. A amostra também ilustra a variação utilizando certificados para autenticação. |
|[active-directy-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Uma aplicação web que sincroniza dados do Microsoft Graph utilizando a identidade da aplicação, em vez de em nome de um utilizador. |
