---
title: Autorize o acesso à aplicação web com o OpenID Connect & Azure AD  Microsoft Docs
description: Este artigo descreve como usar mensagens HTTP para autorizar o acesso a aplicações web e APIs web no seu inquilino usando o Azure Ative Directory e OpenID Connect.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 6eb71bb4fdaa602dcbce5d351c261493647f3caa
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700809"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizar o acesso a aplicações web utilizando o OpenID Connect e o Diretório Ativo Azure

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) é uma simples camada de identidade construída em cima do protocolo OAuth 2.0. O OAuth 2.0 define mecanismos para obter e utilizar [**fichas**](access-tokens.md) de acesso para aceder a recursos protegidos, mas não definem métodos padrão para fornecer informações de identidade. A OpenID Connect implementa a autenticação como uma extensão ao processo de autorização OAuth 2.0. Fornece informações sobre o utilizador final sob a forma de uma [`id_token`](id-tokens.md) que verifica a identidade do utilizador e fornece informações básicas sobre o utilizador.

O OpenID Connect é a nossa recomendação se estiver a construir uma aplicação web que esteja hospedada num servidor e acessada através de um browser.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação usando OpenID Connect

O fluxo de entrada mais básico contém os seguintes passos - cada um deles é descrito em detalhe abaixo.

![Fluxo de autenticação de ligação openid](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Documento de metadados OpenID Connect

O OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para uma aplicação realizar o sessão. Isso inclui informações como as URLs a serem usadas e o local das chaves de assinatura públicas do serviço. O documento de metadados OpenID Connect pode ser encontrado em:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Os metadados são um simples documento de Notação de Objetos JavaScript (JSON). Consulte o seguinte corte, por exemplo. O conteúdo do snippet está totalmente descrito na [especificação OpenID Connect](https://openid.net). Note que fornecer uma identificação de inquilino em vez de `common` no lugar de {inquilino} acima resultará em URIs específicos do inquilino no objeto JSON devolvido.

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    "userinfo_endpoint":"https://login.microsoftonline.com/{tenant}/openid/userinfo",
    ...
}
```

Se a sua aplicação tiver chaves de assinatura personalizadas como resultado da utilização da funcionalidade [de mapeamento de sinistros,](active-directory-claims-mapping.md) deve anexar um parâmetro de consulta de `appid` contendo o ID da aplicação, a fim de obter um `jwks_uri` apontando para as informações chave de assinatura da sua aplicação. Por exemplo: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contém um `jwks_uri` de `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de entrada

Quando a sua aplicação web precisa de autenticar o utilizador, deve direcionar o utilizador para o ponto final `/authorize`. Este pedido é semelhante à primeira etapa do Fluxo de Código de [Autorização OAuth 2.0,](v1-protocols-oauth-code.md)com algumas distinções importantes:

* O pedido deve incluir o âmbito `openid` no parâmetro de `scope`.
* O parâmetro `response_type` deve incluir `id_token`.
* O pedido deve incluir o parâmetro `nonce`.

Assim, um pedido de amostra seria assim:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| tenant |required |O valor de `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são identificadores de inquilinos, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para fichas independentes de inquilinos |
| client_id |required |O ID de aplicação atribuído à sua aplicação quando o registou no Azure AD. Pode encontrar isso no portal Azure. Clique no **Diretório Ativo do Azure,** clique em Registos de **Aplicações,** escolha a aplicação e localize o ID da aplicação na página de aplicação. |
| response_type |required |Deve incluir `id_token` para o acesso ao OpenID Connect. Pode também incluir outros response_types, como `code` ou `token`. |
| scope | recomendado | A especificação OpenID Connect requer o âmbito `openid`, que se traduz na permissão "Iniciar sessão" na UI de consentimento. Este e outros âmbitos oIDC são ignorados no ponto final da v1.0, mas ainda é uma melhor prática para clientes compatíveis com padrões. |
| nonce |required |Um valor incluído no pedido, gerado pela app, que está incluído no `id_token` resultante como reivindicação. O aplicativo pode, então, verificar esse valor para atenuar os ataques de reprodução de token. O valor é tipicamente uma cadeia aleatória, única ou GUID que pode ser usada para identificar a origem do pedido. |
| redirect_uri | recomendado |O redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das redirect_uris que registou no portal, exceto que deve ser codificada. Caso não tenha, o agente de utilizador será enviado de volta para um dos URIs redirecionados registados para a aplicação, aleatoriamente. O comprimento máximo é de 255 bytes |
| response_mode |opcional |Especifica o método que deve ser usado para enviar o authorization_code resultante de volta para a sua aplicação. Os valores suportados são `form_post` para post de *formulário HTTP* e `fragment` para fragmento *de URL*. Para aplicações web, recomendamos a utilização de `response_mode=form_post` para garantir a transferência mais segura de fichas para a sua aplicação. O padrão para qualquer fluxo, incluindo um id_token, é `fragment`.|
| state |recomendado |Um valor incluído no pedido que é devolvido na resposta simbólica. Pode ser uma cadeia de caracteres de qualquer conteúdo que você desejar. Um valor exclusivo gerado aleatoriamente geralmente é usado para [impedir ataques de solicitação entre sites forjado](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |
| aviso |opcional |Indica o tipo de interação do usuário que é necessário. Atualmente, os únicos valores válidos são 'login', 'nenhum' e 'consentimento'. `prompt=login` obriga o utilizador a introduzir as suas credenciais nesse pedido, negando um único sinal. `prompt=none` é o oposto - garante que o utilizador não é apresentado com qualquer tipo de solicitação interativa. Se o pedido não puder ser concluído silenciosamente através de um sinal único, o ponto final devolve um erro. `prompt=consent` aciona o diálogo de consentimento da OAuth após o utilizador iniciar a sua sessão, pedindo ao utilizador que conceda permissões à aplicação. |
| login_hint |opcional |Pode ser usado para preencher previamente o campo nome de usuário/endereço de email da página de entrada do usuário, se você souber seu nome de usuário antes do tempo. Muitas vezes as aplicações utilizam este parâmetro durante a reautenticação, tendo já extraído o nome de utilizador de um início de sessão anterior utilizando a `preferred_username` reivindicação. |

Neste momento, é solicitado ao utilizador que introduza as suas credenciais e complete a autenticação.

### <a name="sample-response"></a>Resposta de amostra

Uma resposta da amostra, enviada para o `redirect_uri` especificada no pedido de início de sessão após a autenticação do utilizador, pode parecer-se com esta:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que a app solicitou. Pode utilizar o `id_token` para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| state |Um valor incluído no pedido que também é devolvido na resposta simbólica. Um valor exclusivo gerado aleatoriamente geralmente é usado para [impedir ataques de solicitação entre sites forjado](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação possa manuseá-las adequadamente:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de autorização

A tabela seguinte descreve os vários códigos de erro que podem ser devolvidos no parâmetro `error` da resposta ao erro.

| Código de Erro | Descrição | Ação do cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro necessário ausente. |Corrija e reenvie a solicitação. Este é um erro de desenvolvimento, e é tipicamente apanhado durante os testes iniciais. |
| unauthorized_client |O pedido do cliente não está autorizado a solicitar um código de autorização. |Isto ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino Azure AD do utilizador. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |
| access_denied |Consentimento negado pelo proprietário do recurso |A aplicação do cliente pode notificar o utilizador de que não pode proceder a menos que o utilizador consinta. |
| unsupported_response_type |O servidor de autorização não oferece suporte ao tipo de resposta na solicitação. |Corrija e reenvie a solicitação. Este é um erro de desenvolvimento, e é tipicamente apanhado durante os testes iniciais. |
| server_error |O servidor encontrou um erro inesperado. |Repita a solicitação. Esses erros podem resultar de condições temporárias. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor está temporariamente muito ocupado para lidar com a solicitação. |Repita a solicitação. A aplicação do cliente pode explicar ao utilizador que a sua resposta é adiada devido a uma condição temporária. |
| invalid_resource |O recurso-alvo é inválido porque não existe, a AD Azure não o encontra, ou não está corretamente configurado. |Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |

## <a name="validate-the-id_token"></a>Validar a id_token

Receber apenas um `id_token` não é suficiente para autenticar o utilizador; deve validar a assinatura e verificar as reclamações no `id_token` de acordo com os requisitos da sua aplicação. O ponto final do Azure AD utiliza jSON Web Tokens (JWTs) e criptografia de chaves públicas para assinar fichas e verificar se são válidas.

Pode optar por validar o `id_token` no código do cliente, mas uma prática comum é enviar o `id_token` para um servidor de backend e realizar a validação lá. 

Também pode querer validar reclamações adicionais dependendo do seu cenário. Algumas validações comuns incluem:

* Garantindo que o utilizador/organização se inscreveu para a aplicação.
* Garantir que o utilizador tem autorização/privilégios adequados utilizando as reclamações `wids` ou `roles`. 
* A garantia de uma certa força de autenticação ocorreu, como a autenticação de vários fatores.

Depois de ter validado a `id_token`, pode iniciar uma sessão com o utilizador e utilizar as reclamações no `id_token` para obter informações sobre o utilizador na sua aplicação. Estas informações podem ser utilizadas para exibição, registos, personalização, etc. Para mais informações sobre `id_tokens` e reclamações, leia [a AAD id_tokens](id-tokens.md).

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída

Quando pretende assinar o utilizador fora da aplicação, não é suficiente para limpar os cookies da sua aplicação ou terminar a sessão com o utilizador. Deve também redirecionar o utilizador para o `end_session_endpoint` para a inscrição. Se não o fizer, o utilizador poderá reautenticar a sua aplicação sem voltar a introduzir as suas credenciais, pois terá uma única sessão de inscrição válida com o ponto final da AD Azure.

Pode simplesmente redirecionar o utilizador para o `end_session_endpoint` listado no documento de metadados OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| post_logout_redirect_uri |recomendado |O URL para o que o utilizador deve ser redirecionado após o sucesso da assinatura.  Este URL deve corresponder a um dos URIs redirecionados registados para a sua aplicação no portal de registo da aplicação.  Se *post_logout_redirect_uri* não estiver incluído, o utilizador é mostrado uma mensagem genérica. |

## <a name="single-sign-out"></a>Fim de sessão único

Quando redireciona o utilizador para o `end_session_endpoint`, o Azure AD limpa a sessão do utilizador a partir do navegador. No entanto, o utilizador poderá ainda ser inscrito noutras aplicações que utilizem a AD Azure para autenticação. Para permitir que essas aplicações assinem o utilizador em simultâneo, a Azure AD envia um pedido HTTP GET para o `LogoutUrl` registado de todas as aplicações em que o utilizador está atualmente inscrito. As aplicações devem responder a este pedido, limpando qualquer sessão que identifique o utilizador e devolvendo uma resposta `200`. Se pretender apoiar uma única assinatura na sua aplicação, deve implementar esse `LogoutUrl` no código da sua aplicação. Pode definir o `LogoutUrl` a partir do portal Azure:

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Escolha o seu Diretório Ativo clicando na sua conta no canto superior direito da página.
3. Do painel de navegação à esquerda, escolha **o Diretório Ativo Azure,** em seguida, escolha **os registos** da App e selecione a sua aplicação.
4. Clique em **Definições,** em **seguida, propriedades** e encontre a caixa de texto **URL de logout.** 

## <a name="token-acquisition"></a>Aquisição de Token
Muitas aplicações web precisam não só de assinar o utilizador, mas também de aceder a um serviço web em nome desse utilizador que usa a OAuth. Este cenário combina openID Connect para autenticação do utilizador ao mesmo tempo que adquire um `authorization_code` que pode ser usado para obter `access_tokens` usando o Fluxo de Código de [Autorização OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Obter fichas de acesso
Para adquirir fichas de acesso, é necessário modificar o pedido de inscrição a partir de cima:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Ao incluir os âmbitos de permissão no pedido e utilizando `response_type=code+id_token`, o ponto final `authorize` garante que o utilizador consentiu nas permissões indicadas no parâmetro de consulta `scope` e devolve à sua aplicação um código de autorização para trocar por um token de acesso.

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem sucedida, enviada para o `redirect_uri` usando `response_mode=form_post`, parece:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que a app solicitou. Pode utilizar o `id_token` para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| code |O authorization_code que a app solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Authorization_codes são de curta duração, e normalmente expiram após cerca de 10 minutos. |
| state |Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação possa manuseá-las adequadamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Para obter uma descrição dos possíveis códigos de erro e da ação recomendada pelo cliente, consulte [códigos](#error-codes-for-authorization-endpoint-errors)de erro para erros de ponto final de autorização .

Assim que tiver uma autorização `code` e um `id_token`, pode inscrever o utilizador e obter [fichas](access-tokens.md) de acesso em seu nome. Para iniciar a sua inscrição, deve validar o `id_token` exatamente como descrito acima. Para obter fichas de acesso, pode seguir os passos descritos na secção "Use o código de autorização para solicitar um sinal de acesso" da nossa documentação de fluxo de [código OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [tokens](access-tokens.md)de acesso.
* Saiba mais sobre o [`id_token` e reivindicações.](id-tokens.md)
