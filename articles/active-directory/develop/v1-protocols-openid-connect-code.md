---
title: Entender o fluxo de código de autenticação do OpenID Connect no Azure AD | Microsoft Docs
description: Este artigo descreve como usar mensagens HTTP para autorizar o acesso a aplicativos Web e APIs Web em seu locatário usando o Azure Active Directory e o OpenID Connect.
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
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a82571260f5da679202e96f5e6f72aa2db6788a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834682"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizar o acesso a aplicativos Web usando OpenID Connect e Azure Active Directory

O [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) é uma camada de identidade simples criada com base no protocolo OAuth 2,0. O OAuth 2,0 define mecanismos para obter e usar tokens de [**acesso**](access-tokens.md) para acessar recursos protegidos, mas eles não definem métodos padrão para fornecer informações de identidade. O OpenID Connect implementa a autenticação como uma extensão para o processo de autorização do OAuth 2,0. Ele fornece informações sobre o usuário final na forma de um [`id_token`](id-tokens.md) que verifica a identidade do usuário e fornece informações básicas de perfil sobre o usuário.

O OpenID Connect é a nossa recomendação se você estiver criando um aplicativo Web hospedado em um servidor e acessado por meio de um navegador.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação usando o OpenID Connect

O fluxo de entrada mais básico contém as etapas a seguir, cada uma delas é descrita em detalhes abaixo.

![Fluxo de autenticação do OpenId Connect](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Documento de metadados do OpenID Connect

O OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para que um aplicativo execute a entrada. Isso inclui informações como as URLs a serem usadas e o local das chaves de assinatura públicas do serviço. O documento de metadados do OpenID Connect pode ser encontrado em:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Os metadados são um documento simples JavaScript Object Notation (JSON). Consulte o trecho a seguir para obter um exemplo. O conteúdo do trecho de código é totalmente descrito na [especificação do OpenID Connect](https://openid.net). Observe que fornecer uma ID de locatário em `common` vez de {Tenant} acima resultará em URIs específicos de locatário no objeto JSON retornado.

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

Se seu aplicativo tiver chaves de assinatura personalizadas como resultado do uso do recurso de [mapeamento de declarações](active-directory-claims-mapping.md) , você deverá acrescentar `appid` um parâmetro de consulta que contém a ID do aplicativo para `jwks_uri` começar a apontar para as informações de chave de assinatura do aplicativo. Por exemplo: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contém um `jwks_uri` de `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de entrada

Quando seu aplicativo Web precisa autenticar o usuário, ele deve direcionar o usuário para `/authorize` o ponto de extremidade. Essa solicitação é semelhante à primeira perna do fluxo de [código de autorização do OAuth 2,0](v1-protocols-oauth-code.md), com algumas distinções importantes:

* A solicitação deve incluir o escopo `openid` `scope` no parâmetro.
* O `response_type` parâmetro deve incluir `id_token`.
* A solicitação deve incluir o `nonce` parâmetro.

Portanto, uma solicitação de exemplo ficaria assim:

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
| tenant |obrigatório |O `{tenant}` valor no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são identificadores de locatário, por exemplo `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` , `contoso.onmicrosoft.com` ou `common` ou para tokens independentes de locatário |
| client_id |obrigatório |A ID do aplicativo atribuída ao seu aplicativo quando você o registrou com o Azure AD. Você pode encontrá-lo na portal do Azure. Clique em **Azure Active Directory**, clique em **registros do aplicativo**, escolha o aplicativo e localize a ID do aplicativo na página do aplicativo. |
| response_type |obrigatório |Deve incluir `id_token` para entrar no OpenID Connect. Ele também pode incluir outros response_types, `code` como ou. `token` |
| scope | aconselhável | A especificação do OpenID Connect requer o `openid`escopo, que se traduz na permissão "entrar" na interface do usuário de consentimento. Esse e outros escopos OIDC são ignorados no ponto de extremidade v 1.0, mas ainda é uma prática recomendada para clientes em conformidade com os padrões. |
| nonce |obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que está incluído no resultado `id_token` como uma declaração. O aplicativo pode, então, verificar esse valor para atenuar os ataques de reprodução de token. O valor normalmente é uma cadeia de caracteres aleatória, exclusiva ou GUID, que pode ser usada para identificar a origem da solicitação. |
| redirect_uri | aconselhável |O redirect_uri do seu aplicativo, em que as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, exceto que ele deve ser codificado por URL. Se estiver ausente, o agente do usuário será enviado de volta para um dos URIs de redirecionamento registrados para o aplicativo, aleatoriamente. O comprimento máximo é de 255 bytes |
| response_mode |opcional |Especifica o método que deve ser usado para enviar o authorization_code resultante de volta ao seu aplicativo. Os valores com `form_post` suporte são para o *formulário http post* e `fragment` para o *fragmento de URL*. Para aplicativos Web, é recomendável `response_mode=form_post` usar o para garantir a transferência mais segura de tokens para seu aplicativo. O padrão para qualquer fluxo, incluindo um id_token `fragment`, é.|
| state |aconselhável |Um valor incluído na solicitação que é retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo que você desejar. Um valor exclusivo gerado aleatoriamente geralmente é usado para [impedir ataques de solicitação entre sites forjado](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |
| aviso |opcional |Indica o tipo de interação do usuário que é necessário. Atualmente, os únicos valores válidos são ' login ', ' none ' e ' consent '. `prompt=login`força o usuário a inserir suas credenciais nessa solicitação, negando o logon único. `prompt=none`é o oposto, garante que o usuário não seja apresentado a nenhum prompt interativo. Se a solicitação não puder ser concluída silenciosamente por meio de logon único, o ponto de extremidade retornará um erro. `prompt=consent`dispara a caixa de diálogo de consentimento do OAuth após o usuário entrar, solicitando que o usuário conceda permissões ao aplicativo. |
| login_hint |opcional |Pode ser usado para preencher previamente o campo nome de usuário/endereço de email da página de entrada do usuário, se você souber seu nome de usuário antes do tempo. Geralmente, os aplicativos usam esse parâmetro durante a reautenticação, já ter extraído o nome de usuário de uma `preferred_username` entrada anterior usando a declaração. |

Neste ponto, o usuário será solicitado a inserir suas credenciais e concluir a autenticação.

### <a name="sample-response"></a>Resposta de amostra

Uma resposta de exemplo, enviada para `redirect_uri` o especificado na solicitação de entrada após a autenticação do usuário, poderia ter esta aparência:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que o aplicativo solicitou. Você pode usar o `id_token` para verificar a identidade do usuário e iniciar uma sessão com o usuário. |
| state |Um valor incluído na solicitação que também é retornado na resposta do token. Um valor exclusivo gerado aleatoriamente geralmente é usado para [impedir ataques de solicitação entre sites forjado](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas `redirect_uri` para o para que o aplicativo possa tratá-las adequadamente:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de autorização

A tabela a seguir descreve os vários códigos de erro que podem ser retornados `error` no parâmetro da resposta de erro.

| Código de Erro | Descrição | Ação do cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro necessário ausente. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento e normalmente é capturado durante o teste inicial. |
| unauthorized_client |O aplicativo cliente não tem permissão para solicitar um código de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |
| access_denied |Consentimento negado pelo proprietário do recurso |O aplicativo cliente pode notificar o usuário de que ele não pode continuar, a menos que o usuário consentisse. |
| unsupported_response_type |O servidor de autorização não oferece suporte ao tipo de resposta na solicitação. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento e normalmente é capturado durante o teste inicial. |
| server_error |O servidor encontrou um erro inesperado. |Repita a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor está temporariamente muito ocupado para lidar com a solicitação. |Repita a solicitação. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a uma condição temporária. |
| invalid_resource |O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou não está configurado corretamente. |Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |

## <a name="validate-the-id_token"></a>Validar o id_token

Apenas receber um `id_token` não é suficiente para autenticar o usuário; você deve validar a assinatura e verificar as declarações `id_token` nos requisitos de cada aplicativo. O ponto de extremidade do Azure AD usa JWTs (tokens Web JSON) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Você pode optar por validar o `id_token` no código do cliente, mas uma prática comum é enviar o `id_token` para um servidor de back-end e executar a validação ali. 

Você também pode desejar validar declarações adicionais dependendo do seu cenário. Algumas validações comuns incluem:

* Garantindo que o usuário/organização tenha se inscrito no aplicativo.
* Garantir que o usuário tenha autorização/privilégios adequados usando `wids` as `roles` declarações ou. 
* Garantindo que uma determinada força de autenticação tenha ocorrido, como a autenticação multifator.

Depois de validar o `id_token`, você pode iniciar uma sessão com o usuário e usar as declarações `id_token` no para obter informações sobre o usuário em seu aplicativo. Essas informações podem ser usadas para exibição, registros, personalização, etc. Para obter mais informações `id_tokens` sobre declarações e, leia [AAD id_tokens](id-tokens.md).

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída

Quando você deseja desconectar o usuário do aplicativo, não é suficiente limpar os cookies do aplicativo ou encerrar a sessão com o usuário. Você também deve redirecionar o usuário `end_session_endpoint` para o para sair. Se você não conseguir fazer isso, o usuário poderá se autenticar novamente em seu aplicativo sem inserir suas credenciais novamente, pois eles terão uma sessão de logon único válida com o ponto de extremidade do Azure AD.

Você pode simplesmente redirecionar o usuário `end_session_endpoint` para o listado no documento de metadados do OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| post_logout_redirect_uri |aconselhável |A URL para a qual o usuário deve ser redirecionado após a saída bem-sucedida. Se não estiver incluído, o usuário será exibido como uma mensagem genérica. |

## <a name="single-sign-out"></a>Fim de sessão único

Quando você redireciona o usuário para o `end_session_endpoint`, o Azure ad limpa a sessão do usuário no navegador. No entanto, o usuário ainda pode estar conectado a outros aplicativos que usam o Azure AD para autenticação. Para permitir que esses aplicativos desconectem o usuário simultaneamente, o Azure ad envia uma solicitação HTTP Get para `LogoutUrl` o registrado de todos os aplicativos aos quais o usuário está conectado no momento. Os aplicativos devem responder a essa solicitação limpando todas as sessões que identificam o usuário `200` e retornando uma resposta. Se você quiser dar suporte ao logout único em seu aplicativo, deverá implementar tal como `LogoutUrl` no código do aplicativo. Você pode definir o `LogoutUrl` no portal do Azure:

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Escolha sua Active Directory clicando em sua conta no canto superior direito da página.
3. No painel de navegação à esquerda, escolha **Azure Active Directory**, em seguida, escolha **registros de aplicativo** e selecione seu aplicativo.
4. Clique em **configurações**, depois em **Propriedades** e localize a caixa de texto **URL de logout** . 

## <a name="token-acquisition"></a>Aquisição de token
Muitos aplicativos Web precisam não apenas conectar o usuário, mas também acessar um serviço Web em nome desse usuário usando OAuth. Esse cenário combina o OpenID Connect para autenticação de usuário ao adquirir simultaneamente `authorization_code` um que pode ser usado para `access_tokens` obter o uso do [fluxo de código de autorização OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Obter tokens de acesso
Para adquirir tokens de acesso, você precisa modificar a solicitação de entrada acima:

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

Ao incluir escopos de permissão na solicitação e `response_type=code+id_token`usar, `authorize` o ponto de extremidade garante que o usuário consentiu nas permissões indicadas `scope` no parâmetro de consulta e retorna ao aplicativo um código de autorização para trocar um token de acesso.

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida, enviada ao usando `redirect_uri` `response_mode=form_post`, é semelhante a:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que o aplicativo solicitou. Você pode usar o `id_token` para verificar a identidade do usuário e iniciar uma sessão com o usuário. |
| code |O authorization_code que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Authorization_codes são de curta duração e, normalmente, expiram após cerca de 10 minutos. |
| state |Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas `redirect_uri` para o para que o aplicativo possa tratá-las adequadamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Para obter uma descrição dos possíveis códigos de erro e sua ação de cliente recomendada, consulte [códigos de erro para erros de ponto de extremidade de autorização](#error-codes-for-authorization-endpoint-errors).

Depois de obter uma autorização `code` e um `id_token`, você pode conectar o usuário e obter tokens de [acesso](access-tokens.md) em seu nome. Para conectar o usuário, você deve validar o `id_token` exatamente como descrito acima. Para obter tokens de acesso, você pode seguir as etapas descritas na seção "usar o código de autorização para solicitar um token de acesso" da nossa [documentação de fluxo de código OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre os [tokens de acesso](access-tokens.md).
* Saiba mais sobre as [ `id_token` declarações e](id-tokens.md).
