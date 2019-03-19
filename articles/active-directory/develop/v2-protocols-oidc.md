---
title: O Azure Active Directory v 2.0 e o protocolo OpenID Connect | Documentos da Microsoft
description: Crie aplicativos web, utilizando a implementação da versão 2.0 do Azure AD do protocolo de autenticação OpenID Connect.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c20ae6acaf600cdde6e168c6db96deb7a28e9fa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112709"
---
# <a name="azure-active-directory-v20-and-the-openid-connect-protocol"></a>O Azure Active Directory v 2.0 e o protocolo OpenID Connect

OpenID Connect é um protocolo de autenticação criado no OAuth 2.0, que pode utilizar para assinar com segurança num utilizador a uma aplicação web. Quando usa a implementação do ponto final v2.0 do OpenID Connect, pode adicionar o início de sessão e acesso à API às suas aplicações baseadas na web. Este artigo mostra como fazer essa independentemente do idioma e descreve como enviar e receber mensagens HTTP sem utilizar quaisquer bibliotecas de código-fonte aberto da Microsoft.

> [!NOTE]
> O ponto final v2.0 não suporta todos os cenários do Azure Active Directory (Azure AD) e funcionalidades. Para determinar se deve utilizar o ponto final v2.0, leia sobre [v2.0 limitações](active-directory-v2-limitations.md).

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) expande o OAuth 2.0 *autorização* protocolo a utilizar como um *autenticação* de protocolo, para que pode fazer único início de sessão com a OAuth. OpenID Connect introduz o conceito de um *token de ID*, que é um token de segurança que permite que o cliente verificar a identidade do utilizador. O token de ID também obtém informações de perfil básicas sobre o utilizador. Porque o OpenID Connect expande o OAuth 2.0, as aplicações com segurança podem adquirir *tokens de acesso*, que podem ser utilizadas para aceder a recursos que estão protegidos por um [servidor de autorização](active-directory-v2-protocols.md#the-basics). O ponto final v2.0 também permite que as aplicações de terceiros que estão registadas com o Azure AD para emitir tokens de acesso para recursos protegidos, como as APIs da Web. Para obter mais informações sobre como configurar uma aplicação para emitir tokens de acesso, consulte [como registar uma aplicação com o ponto final v2.0](quickstart-v2-register-an-app.md). Recomendamos que utilize OpenID Connect, se estiver a criar uma [aplicação web](v2-app-types.md#web-apps) que é alojada num servidor e acedido através de um browser.

## <a name="protocol-diagram-sign-in"></a>Diagrama de protocolo: início de sessão

O fluxo de início de sessão mais básico tem os passos apresentados no diagrama seguinte. Cada passo é descrito em detalhe neste artigo.

![Protocolo do OpenID Connect: início de sessão](./media/v2-protocols-oidc/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>Obter o documento de metadados OpenID Connect

OpenID Connect descreve um documento de metadados que contém a maior parte das informações necessárias para uma aplicação efetuar o início de sessão. Isto inclui informações como os URLs para utilizar e a localização das chaves de assinatura pública do serviço. Para o ponto final v2.0, este é o documento de metadados OpenID Connect, deve utilizar:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Experimente! Clique em [ https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration ](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) para ver o `common` configuração de inquilinos.

O `{tenant}` pode efetuar uma das quatro valores:

| Value | Descrição |
| --- | --- |
| `common` |Os utilizadores com uma conta Microsoft pessoal e uma conta escolar ou profissional do Azure Active Directory (Azure AD) podem iniciar sessão para a aplicação. |
| `organizations` |Ou profissional de apenas os utilizadores com contas escolares do Azure AD podem iniciar sessão na aplicação. |
| `consumers` |Apenas os utilizadores com uma conta Microsoft pessoal podem iniciar sessão na aplicação. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` |Apenas os utilizadores com uma conta escolar ou profissional do específico do Azure AD inquilino pode iniciar sessão na aplicação. O nome de domínio amigável de inquilino do Azure AD ou identificador GUID do inquilino pode ser utilizado. |

Os metadados são um documento simples do JavaScript Object Notation (JSON). Consulte o seguinte fragmento para obter um exemplo. Conteúdo do trecho é completamente descrito no [especificação do OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

Normalmente, usaria este documento de metadados para configurar uma biblioteca de OpenID Connect ou SDK; a biblioteca usaria os metadados para realizar seu trabalho. No entanto, se não estiver a utilizar uma biblioteca de pré-compilação OpenID Connect, pode seguir os passos no restante deste artigo efetuar início de sessão numa aplicação web com o ponto final v2.0.

## <a name="send-the-sign-in-request"></a>Enviar o pedido de início de sessão

Quando a aplicação web tiver de autenticar o utilizador, pode direcionar o utilizador para o `/authorize` ponto final. Este pedido é semelhante do leg primeiro do [fluxo de código de autorização de OAuth 2.0](v2-oauth2-auth-code-flow.md), com essas distinções importantes:

* O pedido tem de incluir o `openid` definir o âmbito no `scope` parâmetro.
* O `response_type` parâmetro tem de incluir `id_token`.
* O pedido tem de incluir o `nonce` parâmetro.

> [!IMPORTANT]
> Para solicitar com êxito um token de ID, o registo de aplicações no [portal de registo](https://apps.dev.microsoft.com) tem de ter o **[concessão implícita](v2-oauth2-implicit-grant-flow.md)** ativado para o cliente Web. Se não estiver ativada, um `unsupported_response` vai ser devolvido o erro: "O valor fornecido para o parâmetro de entrada"response_type"não é permitido para este cliente. Valor esperado é 'code' "

Por exemplo:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Clique na ligação seguinte para executar este pedido. Depois de iniciar sessão, o navegador será redirecionado para `https://localhost/myapp/`, com um token de ID na barra de endereço. Tenha em atenção que esse pedido usa `response_mode=fragment` (para apenas a fins de demonstração). Recomendamos que utilize `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| inquilino |Necessário |Pode utilizar o `{tenant}` valor no caminho do pedido para controlar quem pode iniciar sessão na aplicação. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de inquilinos. Para obter mais informações, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id |Necessário |ID de aplicação que o [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) atribuído à sua aplicação. |
| response_type |Necessário |Tem de incluir `id_token` OpenID Connect para início de sessão. Também pode incluir outros `response_type` valores, tais como `code`. |
| redirect_uri |Recomendado |O URI de redirecionamento de seu aplicativo, onde as respostas podem ser enviadas e recebidas pela sua aplicação. Ele deve corresponder exatamente um dos URIs que registou no portal de redirecionamento, exceto pelo fato de que tem de ser codificado de URL. |
| scope |Necessário |Uma lista de âmbitos separadas por espaços. Para o OpenID Connect, tem de incluir o âmbito `openid`, que traduz-se a permissão "Iniciar sessão" no consentimento da interface do Usuário. Também pode incluir outros âmbitos neste pedido para pedir consentimento. |
| Valor de uso único |Necessário |Um valor incluído na solicitação, gerada pela aplicação, que será incluída o valor de id_token resultante como uma afirmação. A aplicação pode verificar este valor para mitigar ataques de repetição de token. O valor normalmente é uma cadeia aleatória, exclusiva que pode ser utilizada para identificar a origem do pedido. |
| response_mode |Recomendado |Especifica o método que deve ser utilizado para enviar o código de autorização resultante para a sua aplicação. Pode ser `form_post` ou `fragment`. Para aplicativos web, recomendamos que utilize `response_mode=form_post`, para garantir que a transferência mais segura de tokens para seu aplicativo. |
| state |Recomendado |Um valor incluído no pedido que também vai ser devolvido na resposta de token. Pode ser uma cadeia de caracteres de qualquer conteúdo que quer. Um valor exclusivo gerado aleatoriamente, normalmente, é utilizado para [impedir ataques de falsificação de solicitação](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, como a página ou a vista, que o usuário estava em. |
| linha de comandos |Opcional |Indica o tipo de interação do utilizador que é necessário. São os únicos valores válidos neste momento `login`, `none`, e `consent`. O `prompt=login` afirmação força o utilizador introduza as credenciais desse pedido, o que elimina o início de sessão único. O `prompt=none` afirmação é o oposto. Esta afirmação garante que não é apresentada ao utilizador com qualquer linha de comandos interativa tipo. Se o pedido não pode ser concluído silenciosamente por meio de início de sessão único, o ponto final v2.0 devolve um erro. O `prompt=consent` afirmação aciona a caixa de diálogo de consentimento do OAuth depois do utilizador inicia sessão. A caixa de diálogo pede ao utilizador para conceder permissões à aplicação. |
| login_hint |Opcional |Pode utilizar este parâmetro para preencher previamente o campo de endereço de e-mail e nome de utilizador da página início de sessão do utilizador, se souber o nome de utilizador antes do tempo. Muitas vezes, as aplicações utilizam este parâmetro durante a reautenticação, depois de já extrair o nome de utilizador de um anterior início de sessão utilizando o `preferred_username` de afirmação. |
| domain_hint |Opcional |Este valor pode ser `consumers` ou `organizations`. Se incluído, ele irá ignorar o processo de descoberta baseada em e-mail que o utilizador passa por na versão 2.0 início de sessão página, uma experiência de usuário ligeiramente mais simplificado. Muitas vezes, o aplicações utilizam este parâmetro durante a reautenticação, extraindo o `tid` o token de ID de afirmação. Se o `tid` é o valor de afirmação `9188040d-6c67-4c5b-b112-36a304b66dad` (o Microsoft Account consumidor inquilino), utilize `domain_hint=consumers`. Caso contrário, utilize `domain_hint=organizations`. |

Neste momento, é pedido ao utilizador para introduzir as respetivas credenciais e concluir a autenticação. O ponto final v2.0 verifica que o utilizador consentiu as permissões indicadas no `scope` parâmetro de consulta. Se o utilizador não consentiu a qualquer uma dessas permissões, o ponto final v2.0 pede ao utilizador para autorizar as permissões necessárias. Pode ler mais sobre [permissões e consentimento e aplicações multi-inquilino](v2-permissions-and-consent.md).

Depois do utilizador autentica e concede consentimento, o ponto final v2.0 devolve uma resposta para a sua aplicação o indicado no URI de redirecionamento utilizando o método especificado no `response_mode` parâmetro.

### <a name="successful-response"></a>Resposta com êxito

Uma resposta com êxito ao utilizar `response_mode=form_post` semelhante ao seguinte:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O token de ID que a aplicação pedida. Pode utilizar o `id_token` parâmetro para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Para obter mais informações sobre os tokens de ID e seu conteúdo, consulte a [ `id_tokens` referência](id-tokens.md). |
| state |Se um `state` parâmetro está incluído na solicitação, o mesmo valor deve aparecer na resposta. A aplicação deve verificar que os valores de estado no pedido e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

Respostas de erro também poderão ser enviadas para o URI de redirecionamento para que a aplicação pode manipulá-las. Uma resposta de erro tem esta aparência:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode usar para classificar tipos de erros que ocorrem e reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto final de autorização

A tabela seguinte descreve os códigos de erro que podem ser devolvidos no `error` parâmetro da resposta de erro:

| Código de erro | Descrição | Ação de cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um em falta, o parâmetro necessário. |Corrija e submeta novamente o pedido. Este é um erro de desenvolvimento que normalmente é capturado durante o teste inicial. |
| unauthorized_client |A aplicação de cliente não é possível pedir um código de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registado no Azure AD ou não for adicionado ao inquilino do Azure AD do utilizador. O aplicativo pode solicitar o utilizador com instruções para instalar a aplicação e adicione-o para o Azure AD. |
| access_denied |O proprietário do recurso negado consentimento. |A aplicação cliente pode notificar o utilizador que não é possível continuar a não ser que o utilizador permitir. |
| unsupported_response_type |O servidor de autorização não suporta o tipo de resposta no pedido. |Corrija e submeta novamente o pedido. Este é um erro de desenvolvimento que normalmente é capturado durante o teste inicial. |
| server_error |O servidor encontrou um erro inesperado. |Repita o pedido. Estes erros podem resultar de condições temporárias. A aplicação cliente pode explicar ao usuário que a sua resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor está temporariamente demasiado ocupado para processar o pedido. |Repita o pedido. A aplicação cliente pode explicar ao usuário que a sua resposta está atrasada devido a uma condição temporária. |
| invalid_resource |O recurso de destino é inválido porque não existe, do Azure AD não é possível encontrá-lo ou não está corretamente configurado. |Isto indica que o recurso, se existir, não foi configurado no inquilino. A aplicação pode pedir ao utilizador com instruções para instalar a aplicação e adicioná-lo para o Azure AD. |

## <a name="validate-the-id-token"></a>Validar o token de ID

Não é suficiente para autenticar o usuário; apenas receber uma id_token tem de validar a assinatura o id_token e verifique se as afirmações no token de conformidade com os requisitos da sua aplicação. Utiliza o ponto final v2.0 [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar os tokens e certifique-se de que são válidas.

Pode optar por validar a `id_token` num cliente de código, mas uma prática comum é enviar o `id_token` para um servidor de back-end e executar a validação lá. Assim que validar a assinatura do id_token, existem algumas declarações, que será necessário para verificar. Consulte a [ `id_token` referência](id-tokens.md) para obter mais informações, incluindo [validar Tokens](id-tokens.md#validating-an-id_token) e [importante informações sobre como assinar Rollover de chave](active-directory-signing-key-rollover.md). Recomendamos que fazendo uso de uma biblioteca para análise e a validar tokens – há, pelo menos, um disponível para a maioria das linguagens e plataformas.
<!--TODO: Improve the information on this-->

Também pode pretender validar afirmações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

* Garantir que a organização/utilizador tiver inscrito a aplicação.
* Garantir que o usuário tem autorização/privilégios apropriados
* Garantir que um determinada força da autenticação tiver ocorrido, como a autenticação multifator.

Depois de validar o id_token completamente, pode iniciar uma sessão com o utilizador e utilizar as afirmações no id_token para obter informações sobre o utilizador na sua aplicação. Estas informações podem ser utilizadas para apresentar, registos, personalização, etc.

## <a name="send-a-sign-out-request"></a>Enviar um pedido de fim de sessão

Quando deseja terminar sessão do utilizador da sua aplicação, não é suficiente para limpar os cookies da sua aplicação ou caso contrário, terminar a sessão do utilizador. Também tem de redirecionar o utilizador para o ponto final v2.0 para terminar sessão. Se não fizer isso, o utilizador reauthenticates à sua aplicação sem introduzir as respetivas credenciais novamente, porque eles terão uma válido única início de sessão com o ponto final v2.0.

Pode redirecionar o utilizador para o `end_session_endpoint` listados no documento de metadados OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parâmetro | Condição | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | Recomendado | O URL que o utilizador é redirecionado para depois de terminar com êxito. Se o parâmetro não for incluído, o utilizador é apresentado uma mensagem genérica, que é gerada pelo ponto final v2.0. Este URL deve corresponder a um de redirecionamento que URIs registado para a sua aplicação no portal de registo de aplicação. |

## <a name="single-sign-out"></a>Fim de sessão único

Quando redireciona o utilizador para o `end_session_endpoint`, o ponto final v2.0 limpa a sessão do utilizador do navegador. No entanto, o utilizador poderá ainda estar conectado a outras aplicações que utilizam contas da Microsoft para a autenticação. Para permitir que esses aplicativos iniciar sessão do utilizador horizontalmente em simultâneo, a versão 2.0 o ponto final envia um pedido HTTP GET para o registados `LogoutUrl` de todos os aplicativos que o utilizador tem atualmente sessão iniciado no. Aplicativos devem responder a essa solicitação limpar todas as sessões que identifica o usuário e retornando um `200` resposta. Se desejar oferecer suporte a fim de sessão em seu aplicativo único, tem de implementar como um `LogoutUrl` no código da aplicação. Pode definir o `LogoutUrl` partir do portal de registo da aplicação.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagrama de protocolo: Aquisição de token de acesso

Muitos aplicativos web precisam não apenas iniciar sessão do utilizador no, mas também para aceder a um serviço da web em nome do utilizador ao utilizar o OAuth. Este cenário combina o OpenID Connect para a autenticação de utilizador ao obter simultaneamente um código de autorização que pode utilizar para obter os tokens de acesso, se estiver a utilizar o fluxo de código de autorização de OAuth.

O fluxo de início de sessão e aquisição de token OpenID Connect completo é semelhante ao seguinte diagrama. Descreveremos cada etapa detalhadamente nas próximas seções deste artigo.

![Protocolo do OpenID Connect: Aquisição do token](./media/v2-protocols-oidc/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>Obter os tokens de acesso
Para adquirir os tokens de acesso, modifique o pedido de início de sessão:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Clique na ligação seguinte para executar este pedido. Depois de iniciar sessão, o seu navegador é redirecionado para `https://localhost/myapp/`, com um token de ID e um código na barra de endereço. Tenha em atenção que esse pedido usa `response_mode=fragment` fins apenas demonstrativos. Recomendamos que utilize `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

Incluindo âmbitos de permissão no pedido e utilize `response_type=id_token code`, o ponto final v2.0 garante que o utilizador consentiu as permissões indicadas no `scope` parâmetro de consulta. Devolve um código de autorização para a sua aplicação para o exchange para um token de acesso.

### <a name="successful-response"></a>Resposta com êxito

Uma resposta com êxito de utilizar `response_mode=form_post` semelhante ao seguinte:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O token de ID que a aplicação pedida. Pode usar o token de ID para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Encontrará mais detalhes sobre os tokens de ID e seu conteúdo no [ `id_tokens` referência](id-tokens.md). |
| Código |O código de autorização que solicitou a aplicação. A aplicação pode utilizar o código de autorização para pedir um token de acesso para o recurso de destino. Um código de autorização é muito curta duração. Normalmente, um código de autorização expira em cerca de 10 minutos. |
| state |Se um parâmetro de estado está incluído na solicitação, o mesmo valor deve aparecer na resposta. A aplicação deve verificar que os valores de estado no pedido e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

Respostas de erro também poderão ser enviadas para o URI de redirecionamento para que a aplicação pode processar corretamente. Uma resposta de erro tem esta aparência:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode usar para classificar tipos de erros que ocorrem e reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |

Para obter uma descrição de códigos de erro possíveis e respostas de cliente recomendado, consulte [códigos de erro para erros de autorização do ponto de extremidade](#error-codes-for-authorization-endpoint-errors).

Quando tiver um código de autorização e um token de ID, pode iniciar sessão do utilizador e obter os tokens de acesso em nome deles. Iniciar a sessão do utilizador no, tem de validar o token de ID [exatamente como descrito](id-tokens.md#validating-an-id_token). Para obter os tokens de acesso, siga os passos descritos em [documentação de fluxo de código do OAuth](v2-oauth2-auth-code-flow.md#request-an-access-token).
