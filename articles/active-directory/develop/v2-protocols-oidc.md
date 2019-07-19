---
title: Plataforma Microsoft Identity e o protocolo OpenID Connect | Azure
description: Crie aplicativos Web usando a implementação da plataforma de identidade da Microsoft do protocolo de Autenticação OpenID Connect.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: be7d4164bd1a412c69c3b5adfe20cf83d699d2b4
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304797"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Plataforma Microsoft Identity e o protocolo OpenID Connect

O OpenID Connect é um protocolo de autenticação criado no OAuth 2,0 que você pode usar para conectar com segurança um usuário a um aplicativo Web. Quando você usa a implementação do ponto de extremidade da plataforma de identidade da Microsoft do OpenID Connect, você pode adicionar entrada e acesso à API para seus aplicativos baseados na Web. Este artigo mostra como fazer isso independentemente do idioma e descreve como enviar e receber mensagens HTTP sem usar nenhuma biblioteca de código-fonte aberto da Microsoft.

> [!NOTE]
> O ponto de extremidade da plataforma de identidade da Microsoft não dá suporte a todos os cenários e recursos do Azure Active Directory (Azure AD). Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

O [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) estende o protocolo de *autorização* OAuth 2,0 para usar como um protocolo de *autenticação* , para que você possa fazer logon único usando OAuth. O OpenID Connect apresenta o conceito de um *token de ID*, que é um token de segurança que permite ao cliente verificar a identidade do usuário. O token de ID também obtém informações básicas de perfil sobre o usuário. Como o OpenID Connect estende o OAuth 2,0, os aplicativos podem adquirir tokens de *acesso*com segurança, que podem ser usados para acessar recursos que são protegidos por um [servidor de autorização](active-directory-v2-protocols.md#the-basics). O ponto de extremidade da plataforma Microsoft Identity também permite que aplicativos de terceiros registrados no Azure AD emitam tokens de acesso para recursos protegidos, como APIs Web. Para obter mais informações sobre como configurar um aplicativo para emitir tokens de acesso, consulte [como registrar um aplicativo com o ponto de extremidade da plataforma de identidade da Microsoft](quickstart-register-app.md). Recomendamos que você use o OpenID Connect se estiver criando um [aplicativo Web](v2-app-types.md#web-apps) hospedado em um servidor e acessado por meio de um navegador.

## <a name="protocol-diagram-sign-in"></a>Diagrama de protocolo: Iniciar sessão

O fluxo de entrada mais básico tem as etapas mostradas no próximo diagrama. Cada etapa é descrita detalhadamente neste artigo.

![Protocolo OpenID Connect: Iniciar sessão](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Buscar o documento de metadados do OpenID Connect

O OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para que um aplicativo faça logon. Isso inclui informações como as URLs a serem usadas e o local das chaves de assinatura públicas do serviço. Para o ponto de extremidade da plataforma de identidade da Microsoft, este é o documento de metadados do OpenID Connect que você deve usar:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Experimente! Clique [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) para ver a `common` configuração dos locatários.

O `{tenant}` pode usar um dos quatro valores:

| Valor | Descrição |
| --- | --- |
| `common` |Os usuários com um conta Microsoft pessoal e uma conta corporativa ou de estudante do Azure AD podem entrar no aplicativo. |
| `organizations` |Somente usuários com contas corporativas ou de estudante do Azure AD podem entrar no aplicativo. |
| `consumers` |Somente os usuários com um conta Microsoft pessoal podem entrar no aplicativo. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Somente os usuários de um locatário específico do Azure AD (sejam eles Membros no diretório com uma conta corporativa ou de estudante ou convidados no diretório com um conta Microsoft pessoal) podem entrar no aplicativo. O nome de domínio amigável do locatário do Azure AD ou o identificador GUID do locatário pode ser usado. Você também pode usar o locatário do consumidor `9188040d-6c67-4c5b-b112-36a304b66dad`, no lugar `consumers` do locatário.  |

Os metadados são um documento simples JavaScript Object Notation (JSON). Consulte o trecho a seguir para obter um exemplo. O conteúdo do trecho de código é totalmente descrito na [especificação do OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/{tenant}\/discovery\/v2.0\/keys",

  ...

}
```

Se seu aplicativo tiver chaves de assinatura personalizadas como resultado do uso do recurso de [mapeamento de declarações](active-directory-claims-mapping.md) , você deverá acrescentar `appid` um parâmetro de consulta que contém a ID do aplicativo para `jwks_uri` começar a apontar para as informações de chave de assinatura do aplicativo. Por exemplo: `https://login.microsoftonline.com/{tenant}/.well-known/v2.0/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contém um `jwks_uri` de `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

Normalmente, você usaria esse documento de metadados para configurar uma biblioteca ou um SDK do OpenID Connect; a biblioteca usaria os metadados para fazer seu trabalho. No entanto, se você não estiver usando uma biblioteca pré-criada do OpenID Connect, poderá seguir as etapas no restante deste artigo para fazer logon em um aplicativo Web usando o ponto de extremidade da plataforma Microsoft Identity.

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de entrada

Quando seu aplicativo Web precisa autenticar o usuário, ele pode direcionar o usuário para `/authorize` o ponto de extremidade. Essa solicitação é semelhante ao primeiro segmento do fluxo de [código de autorização do OAuth 2,0](v2-oauth2-auth-code-flow.md), com estas distinções importantes:

* A solicitação deve incluir o `openid` escopo `scope` no parâmetro.
* O `response_type` parâmetro deve incluir `id_token`.
* A solicitação deve incluir o `nonce` parâmetro.

> [!IMPORTANT]
> Para solicitar com êxito um token de ID do ponto de extremidade/Authorization, o registro do aplicativo no [portal de registro](https://portal.azure.com) deve ter a concessão implícita de id_tokens habilitada na guia Autenticação (que define `oauth2AllowIdTokenImplicitFlow` o sinalizador no [ manifesto](reference-app-manifest.md) do aplicativo `true`para). Se não estiver habilitado, um `unsupported_response` erro será retornado: "O valor fornecido para o parâmetro de entrada ' response_type ' não é permitido para este cliente. O valor esperado é ' code ' "

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
> Clique no link a seguir para executar essa solicitação. Depois de entrar, seu navegador será redirecionado para `https://localhost/myapp/`, com um token de ID na barra de endereços. Observe que essa solicitação usa `response_mode=fragment` (apenas para fins de demonstração). Recomendamos que você use `response_mode=form_post`o.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Necessário | Você pode usar o `{tenant}` valor no caminho da solicitação para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers`e os identificadores de locatário. Para obter mais informações, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Necessário | A **ID do aplicativo (cliente)** que a [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `response_type` | Necessário | Deve incluir `id_token` para entrar no OpenID Connect. Ele também pode incluir outros `response_type` valores, `code`como. |
| `redirect_uri` | Recomendado | O URI de redirecionamento do seu aplicativo, em que as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no portal, exceto que ele deve ser codificado por URL. Se não estiver presente, o ponto de extremidade selecionará um redirect_uri registrado aleatoriamente para enviar o usuário de volta para o. |
| `scope` | Necessário | Uma lista de escopos separados por espaços. Para o OpenID Connect, ele deve incluir o `openid`escopo, que se traduz na permissão "entrar" na interface do usuário de consentimento. Você também pode incluir outros escopos nesta solicitação para solicitar o consentimento. |
| `nonce` | Necessário | Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no valor id_token resultante como uma declaração. O aplicativo pode verificar esse valor para atenuar os ataques de reprodução de token. O valor normalmente é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| `response_mode` | Recomendado | Especifica o método que deve ser usado para enviar o código de autorização resultante de volta para seu aplicativo. Pode ser `form_post` ou `fragment`. Para aplicativos Web, é recomendável `response_mode=form_post`usar o, para garantir a transferência mais segura de tokens para seu aplicativo. |
| `state` | Recomendado | Um valor incluído na solicitação que também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação entre sites forjado](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que o usuário estava. |
| `prompt` | Opcional | Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são `login`, `none`e `consent`. A `prompt=login` declaração força o usuário a inserir suas credenciais nessa solicitação, o que nega o logon único. A `prompt=none` declaração é o oposto. Essa declaração garante que o usuário não seja apresentado a nenhum prompt interativo em. Se a solicitação não puder ser concluída silenciosamente por meio do logon único, o ponto de extremidade da plataforma de identidade da Microsoft retornará um erro. A `prompt=consent` declaração dispara a caixa de diálogo de consentimento do OAuth após o usuário entrar. A caixa de diálogo solicita que o usuário conceda permissões ao aplicativo. |
| `login_hint` | Opcional | Você pode usar esse parâmetro para preencher previamente o campo de nome de usuário e endereço de email da página de entrada do usuário, se você souber o nome de usuário antes do tempo. Geralmente, os aplicativos usam esse parâmetro durante a reautenticação, depois de já extrair o nome de usuário de uma entrada anterior usando `preferred_username` a declaração. |
| `domain_hint` | Opcional | O realm do usuário em um diretório federado.  Isso ignora o processo de descoberta baseado em email que o usuário passa na página de entrada, para uma experiência de usuário um pouco mais simplificada. Para locatários federados por meio de um diretório local como AD FS, isso geralmente resulta em uma entrada direta devido à sessão de logon existente. |

Neste ponto, é solicitado que o usuário insira suas credenciais e conclua a autenticação. O ponto de extremidade da plataforma de identidade da Microsoft verifica se o usuário consentiu nas permissões indicadas no parâmetro de `scope` consulta. Se o usuário não tiver consentido nenhuma dessas permissões, o ponto de extremidade da plataforma de identidade da Microsoft solicitará que o usuário consentisse nas permissões necessárias. Você pode ler mais sobre [permissões, consentimento e aplicativos de vários locatários](v2-permissions-and-consent.md).

Depois que o usuário é autenticado e concede consentimento, o ponto de extremidade da plataforma de identidade da Microsoft retorna uma resposta ao seu aplicativo no URI de redirecionamento indicado usando o método especificado no `response_mode` parâmetro.

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida quando você usa `response_mode=form_post` esta aparência:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `id_token` | O token de ID que o aplicativo solicitou. Você pode usar o `id_token` parâmetro para verificar a identidade do usuário e iniciar uma sessão com o usuário. Para obter mais informações sobre tokens de ID e seu conteúdo, consulte a [ `id_tokens` referência](id-tokens.md). |
| `state` | Se um `state` parâmetro for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para o URI de redirecionamento para que o aplicativo possa tratá-las. Uma resposta de erro é parecida com esta:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de caracteres de código de erro que você pode usar para classificar tipos de erros que ocorrem e reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa raiz de um erro de autenticação. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de autorização

A tabela a seguir descreve os códigos de erro que podem ser `error` retornados no parâmetro da resposta de erro:

| Código de erro | Descrição | Ação do cliente |
| --- | --- | --- |
| `invalid_request` | Erro de protocolo, como um parâmetro ausente, necessário. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento que normalmente é capturado durante o teste inicial. |
| `unauthorized_client` | O aplicativo cliente não pode solicitar um código de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |
| `access_denied` | O proprietário do recurso negou o consentimento. |O aplicativo cliente pode notificar o usuário de que ele não pode continuar, a menos que o usuário consentisse. |
| `unsupported_response_type` |O servidor de autorização não oferece suporte ao tipo de resposta na solicitação. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento que normalmente é capturado durante o teste inicial. |
| `server_error` | O servidor encontrou um erro inesperado. |Repita a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a um erro temporário. |
| `temporarily_unavailable` | O servidor está temporariamente muito ocupado para lidar com a solicitação. |Repita a solicitação. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a uma condição temporária. |
| `invalid_resource` | O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou não está configurado corretamente. |Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |

## <a name="validate-the-id-token"></a>Validar o token de ID

Apenas receber um id_token não é suficiente para autenticar o usuário; Você deve validar a assinatura id_token's e verificar as declarações no token de acordo com os requisitos do aplicativo. O ponto de extremidade da plataforma de identidade da Microsoft usa [JWTs (tokens Web JSON)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Você pode optar por validar o `id_token` no código do cliente, mas uma prática comum é enviar o `id_token` para um servidor de back-end e fazer a validação lá. Depois de validar a assinatura do id_token, há algumas declarações que serão necessárias para verificar. Consulte a [ `id_token` referência](id-tokens.md) para obter mais informações, incluindo [validação](id-tokens.md#validating-an-id_token) de tokens e [informações importantes sobre substituição de chave de assinatura](active-directory-signing-key-rollover.md). É recomendável fazer uso de uma biblioteca para análise e validação de tokens – há pelo menos uma disponível para a maioria das linguagens e plataformas.

Você também pode desejar validar declarações adicionais dependendo do seu cenário. Algumas validações comuns incluem:

* Garantindo que o usuário/organização tenha se inscrito no aplicativo.
* Garantindo que o usuário tenha autorização/privilégios adequados
* Garantindo que uma determinada força de autenticação tenha ocorrido, como a autenticação multifator.

Depois de validar o id_token, você pode iniciar uma sessão com o usuário e usar as declarações no id_token para obter informações sobre o usuário em seu aplicativo. Essas informações podem ser usadas para exibição, registros, personalização, etc.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída

Quando você deseja desconectar o usuário do seu aplicativo, não é suficiente limpar os cookies do aplicativo ou encerrar a sessão do usuário. Você também deve redirecionar o usuário para o ponto de extremidade da plataforma de identidade da Microsoft para sair. Se você não fizer isso, o usuário se reautenticará em seu aplicativo sem inserir suas credenciais novamente, pois ele terá uma sessão de logon único válida com o ponto de extremidade da plataforma de identidade da Microsoft.

Você pode redirecionar o usuário `end_session_endpoint` para o listado no documento de metadados do OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parâmetro | Condição | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Recomendado | A URL para a qual o usuário é redirecionado depois de sair com êxito. Se o parâmetro não estiver incluído, o usuário será mostrado uma mensagem genérica gerada pelo ponto de extremidade da plataforma Microsoft Identity. Essa URL deve corresponder a um dos URIs de redirecionamento registrados para seu aplicativo no portal de registro de aplicativo. |

## <a name="single-sign-out"></a>Fim de sessão único

Quando você redireciona o usuário para o `end_session_endpoint`, o ponto de extremidade da plataforma de identidade da Microsoft limpa a sessão do usuário no navegador. No entanto, o usuário ainda pode estar conectado a outros aplicativos que usam contas da Microsoft para autenticação. Para permitir que esses aplicativos desconectem o usuário simultaneamente, o ponto de extremidade da plataforma de identidade da Microsoft envia uma `LogoutUrl` solicitação HTTP Get para o registrado de todos os aplicativos aos quais o usuário está conectado no momento. Os aplicativos devem responder a essa solicitação limpando todas as sessões que identificam o usuário `200` e retornando uma resposta. Se você quiser dar suporte ao logout único em seu aplicativo, deverá implementar tal como `LogoutUrl` no código do aplicativo. Você pode definir o `LogoutUrl` no portal de registro de aplicativo.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagrama de protocolo: Aquisição de token de acesso

Muitos aplicativos Web precisam não apenas conectar o usuário, mas também acessar um serviço Web em nome do usuário usando OAuth. Esse cenário combina o OpenID Connect para autenticação de usuário ao obter simultaneamente um código de autorização que você pode usar para obter tokens de acesso se estiver usando o fluxo de código de autorização OAuth.

O fluxo de entrada e aquisição de token completo do OpenID Connect é semelhante ao próximo diagrama. Descrevemos cada etapa detalhadamente nas próximas seções do artigo.

![Protocolo OpenID Connect: Aquisição de token](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Obter tokens de acesso
Para adquirir tokens de acesso, modifique a solicitação de entrada:

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
> Clique no link a seguir para executar essa solicitação. Depois de entrar, seu navegador será redirecionado para `https://localhost/myapp/`, com um token de ID e um código na barra de endereços. Observe que essa solicitação usa `response_mode=fragment` apenas para fins de demonstração. Recomendamos que você use `response_mode=form_post`o.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Ao incluir escopos de permissão na solicitação e usando `response_type=id_token code`, o ponto de extremidade da plataforma de identidade da Microsoft garante que o usuário tenha consentido as permissões `scope` indicadas no parâmetro de consulta. Ele retorna um código de autorização para o aplicativo a ser trocado por um token de acesso.

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida do uso `response_mode=form_post` da aparência é a seguinte:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `id_token` | O token de ID que o aplicativo solicitou. Você pode usar o token de ID para verificar a identidade do usuário e iniciar uma sessão com o usuário. Você encontrará mais detalhes sobre tokens de ID e seu conteúdo na [ `id_tokens` referência](id-tokens.md). |
| `code` | O código de autorização solicitado pelo aplicativo. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Um código de autorização é de curta duração. Normalmente, um código de autorização expira em cerca de 10 minutos. |
| `state` | Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para o URI de redirecionamento para que o aplicativo possa tratá-las adequadamente. Uma resposta de erro é parecida com esta:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de caracteres de código de erro que você pode usar para classificar tipos de erros que ocorrem e reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa raiz de um erro de autenticação. |

Para obter uma descrição de possíveis códigos de erro e respostas de cliente recomendadas, consulte [códigos de erro para erros de ponto de extremidade de autorização](#error-codes-for-authorization-endpoint-errors).

Quando você tem um código de autorização e um token de ID, você pode conectar o usuário e obter tokens de acesso em seu nome. Para conectar o usuário, você deve validar o token de ID [exatamente como descrito](id-tokens.md#validating-an-id_token). Para obter tokens de acesso, siga as etapas descritas na [documentação do fluxo de código OAuth](v2-oauth2-auth-code-flow.md#request-an-access-token).
