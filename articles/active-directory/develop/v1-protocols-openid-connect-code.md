---
title: Compreender o fluxo de código de autenticação OpenID Connect no Azure AD | Documentos da Microsoft
description: Este artigo descreve como utilizar mensagens HTTP para autorizar o acesso a aplicações web e APIs web no seu inquilino através do Azure Active Directory e o OpenID Connect.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/4/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 281e1109964ac64853b8b82525579b7ff4de0d2f
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406410"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizar o acesso a aplicações web com OpenID Connect e Azure Active Directory

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) é uma camada de identidade simples criada com base no protocolo de OAuth 2.0. OAuth 2.0 define os mecanismos de obter e utilizar [ **tokens de acesso** ](access-tokens.md) para aceder a recursos protegidos, mas eles não defina métodos padrão para fornecer informações de identidade. OpenID Connect implementa a autenticação como uma extensão para o processo de autorização de OAuth 2.0. Fornece informações sobre o utilizador final na forma de um [ `id_token` ](id-tokens.md) que verifica a identidade do utilizador e fornece informações de perfil básicas sobre o utilizador.

OpenID Connect é a nossa recomendação se estiver a criar uma aplicação web que é alojada num servidor e acessada através de um browser.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação através do OpenID Connect

O fluxo de início de sessão mais básico contém os seguintes passos, cada um deles é descrita detalhadamente abaixo.

![Fluxo de autenticação de ligação do OpenId](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Documento de metadados do OpenID Connect

OpenID Connect descreve um documento de metadados que contém a maior parte das informações necessárias para uma aplicação efetuar o início de sessão. Isto inclui informações como os URLs para utilizar e a localização das chaves de assinatura pública do serviço. O documento de metadados OpenID Connect pode ser encontrado em:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Os metadados são um documento simples do JavaScript Object Notation (JSON). Consulte o seguinte fragmento para obter um exemplo. Conteúdo do trecho é completamente descrito no [especificação do OpenID Connect](https://openid.net). Tenha em atenção nesse inquilino fornecendo vez `common` no local de {inquilino} acima irá resultar em URIs de inquilino específico no objeto JSON devolvido.

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/common/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/common/oauth2/token",
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

## <a name="send-the-sign-in-request"></a>Enviar o pedido de início de sessão

Quando seu aplicativo web precisa para autenticar o utilizador, tem de direcionar o utilizador para o `/authorize` ponto final. Este pedido é semelhante do leg primeiro do [fluxo de código do OAuth 2.0 autorização](v1-protocols-oauth-code.md), com algumas diferenças importantes:

* O pedido tem de incluir o âmbito `openid` no `scope` parâmetro.
* O `response_type` parâmetro tem de incluir `id_token`.
* O pedido tem de incluir o `nonce` parâmetro.

Portanto, um pedido de exemplo teria o seguinte aspeto:

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
| inquilino |obrigatório |O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação. Os valores permitidos são identificadores de inquilino, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens de independente de inquilino |
| client_id |obrigatório |O Id de aplicação atribuída à aplicação quando registou com o Azure AD. Pode encontrar isto no Portal do Azure. Clique em **do Azure Active Directory**, clique em **registos das aplicações**, escolha a aplicação e localize o Id da aplicação na página de aplicativo. |
| response_type |obrigatório |Tem de incluir `id_token` OpenID Connect para início de sessão. Também pode incluir outros response_types, tal como `code` ou `token`. |
| scope | Recomendado | A especificação de OpenID Connect exige que o âmbito `openid`, que traduz-se a permissão "Iniciar sessão" no consentimento da interface do Usuário. Este e outros âmbitos OIDC são ignorados no ponto final da versão 1.0, mas ainda é uma prática recomendada para clientes compatíveis com os padrões. |
| Valor de uso único |obrigatório |Um valor incluído na solicitação, gerada pela aplicação, que está incluída no resultante `id_token` como uma afirmação. A aplicação pode, em seguida, verifique se este valor para mitigar ataques de repetição de token. O valor é, normalmente, uma cadeia de caracteres aleatória, exclusiva ou o GUID que pode ser utilizado para identificar a origem do pedido. |
| redirect_uri | Recomendado |O redirect_uri da sua aplicação, onde as respostas podem ser enviadas e recebidas pela sua aplicação. Ele deve corresponder exatamente um dos redirect_uris registado no portal, exceto pelo fato tem de ser codificados de url. Se estiver em falta, o agente de utilizador será enviado para um dos URIs registado para a aplicação, aleatoriamente de redirecionamento. |
| response_mode |opcional |Especifica o método que deve ser utilizado para enviar o authorization_code resultante para a sua aplicação. Valores suportados são `form_post` para *postagem de formulário do HTTP* e `fragment` para *fragmento da URL*. Para aplicativos web, recomendamos que utilize `response_mode=form_post` para garantir que a transferência mais segura de tokens para seu aplicativo. O padrão para qualquer fluxo, incluindo um id_token é `fragment`.|
| state |Recomendado |Um valor incluído no pedido que é devolvido na resposta de token. Pode ser uma cadeia de caracteres de qualquer conteúdo que desejar. Um valor exclusivo gerado aleatoriamente é normalmente utilizado para [impedir ataques de falsificação de solicitação](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, como a página ou a vista estivessem na. |
| linha de comandos |opcional |Indica o tipo de interação do utilizador que é necessário. Atualmente, os únicos valores válidos são 'login', 'none' e "consentimento". `prompt=login` força o utilizador introduza as credenciais desse pedido, eliminando-início de sessão único. `prompt=none` é o oposto - assegura que o utilizador não é apresentado qualquer linha de comandos interativa tipo. Se o pedido não pode ser concluído silenciosamente por meio de início de sessão único, o ponto final devolve um erro. `prompt=consent` acionadores o OAuth consentimento a caixa de diálogo depois do utilizador inicia sessão, solicitando que o usuário para conceder permissões à aplicação. |
| login_hint |opcional |Pode ser usada para preencher previamente o campo de endereço de e-mail/nome de utilizador da página início de sessão do utilizador, se souber que o respetivo nome de utilizador antes do tempo. Aplicações, muitas vezes, utilizam este parâmetro durante a reautenticação, já após extrair o nome de utilizador de um anterior início de sessão com o `preferred_username` de afirmação. |

Neste momento, é pedido ao utilizador para introduzir as respetivas credenciais e concluir a autenticação.

### <a name="sample-response"></a>Resposta de amostra

Uma resposta de exemplo, depois do utilizador autenticado, pode ter um aspeto semelhante a esta:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que solicitou a aplicação. Pode utilizar o `id_token` para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| state |Um valor incluído no pedido que também é devolvido na resposta de token. Um valor exclusivo gerado aleatoriamente é normalmente utilizado para [impedir ataques de falsificação de solicitação](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, como a página ou a vista estivessem na. |

### <a name="error-response"></a>Resposta de erro

Respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação pode processar corretamente:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação do desenvolvedor. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto final de autorização

A tabela seguinte descreve os vários códigos de erro que podem ser devolvidos no `error` parâmetro da resposta de erro.

| Código de Erro | Descrição | Ação de cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro necessário em falta. |Corrija e submeta novamente o pedido. Este é um erro de desenvolvimento e normalmente é capturado durante o teste inicial. |
| unauthorized_client |A aplicação de cliente não tem permissão para pedir um código de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registado no Azure AD ou não for adicionado ao inquilino do Azure AD do utilizador. A aplicação pode pedir ao utilizador com instruções para a instalação do aplicativo e adicioná-lo para o Azure AD. |
| access_denied |Proprietário do recurso negado consentimento |A aplicação cliente pode notificar o utilizador que não é possível continuar a não ser que o utilizador permitir. |
| unsupported_response_type |O servidor de autorização não suporta o tipo de resposta no pedido. |Corrija e submeta novamente o pedido. Este é um erro de desenvolvimento e normalmente é capturado durante o teste inicial. |
| server_error |O servidor encontrou um erro inesperado. |Repita o pedido. Estes erros podem resultar de condições temporárias. A aplicação cliente pode explicar ao usuário que a sua resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor está temporariamente demasiado ocupado para processar o pedido. |Repita o pedido. A aplicação cliente pode explicar ao usuário que a sua resposta está atrasada devido a uma condição temporária. |
| invalid_resource |O recurso de destino é inválido porque não existe, do Azure AD não é possível encontrá-lo ou não está corretamente configurado. |Isto indica que o recurso, se existir, não foi configurado no inquilino. A aplicação pode pedir ao utilizador com instruções para a instalação do aplicativo e adicioná-lo para o Azure AD. |

## <a name="validate-the-idtoken"></a>Validar o id_token

Apenas receber uma `id_token` não é suficiente para autenticar o usuário; tem de validar a assinatura e verifique se as afirmações no `id_token` conformidade com os requisitos da sua aplicação. O ponto final do Azure utiliza JSON Web Tokens (JWTs) e criptografia de chave pública para assinar os tokens e certifique-se de que são válidas.

Pode optar por validar a `id_token` num cliente de código, mas uma prática comum é enviar o `id_token` para um servidor de back-end e executar a validação lá. 

Também pode pretender validar afirmações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

* Garantir que a organização/utilizador tiver inscrito a aplicação.
* Garantir que o usuário tem autorização/privilégios apropriados com o `wids` ou `roles` afirmações. 
* Garantir que um determinada força da autenticação tiver ocorrido, como a autenticação multifator.

Depois de validar o `id_token`, pode iniciar uma sessão com o utilizador e utilizar as afirmações no `id_token` para obter informações sobre o utilizador na sua aplicação. Estas informações podem ser utilizadas para apresentar, registos, personalização, etc. Para obter mais informações sobre `id_tokens` e afirmações, leia [AAD id_tokens](id-tokens.md).

## <a name="send-a-sign-out-request"></a>Enviar um pedido de fim de sessão

Quando deseja iniciar sessão do utilizador fora da aplicação, não é suficiente limpar os cookies da sua aplicação ou do fim; caso contrário, a sessão com o utilizador. Também tem de redirecionar o utilizador para o `end_session_endpoint` para fim de sessão. Se não conseguir fazê-lo, o utilizador irá conseguir autenticar-se à sua aplicação sem introduzir as respetivas credenciais novamente, porque eles terão uma única início de sessão em sessão válida com o ponto final do Azure.

Simplesmente pode redirecionar o utilizador para o `end_session_endpoint` listados no documento de metadados OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| post_logout_redirect_uri |Recomendado |O URL que o utilizador deve ser redirecionado para depois de terminar a sessão com êxito. Se não incluído, o utilizador é apresentado uma mensagem genérica. |

## <a name="single-sign-out"></a>Fim de sessão único

Quando redireciona o utilizador para o `end_session_endpoint`, do Azure AD limpa a sessão do utilizador do navegador. No entanto, o utilizador poderá ainda estar conectado a outros aplicativos que utilizam o Azure AD para autenticação. Para permitir que esses aplicativos para o utilizador terminar sessão em simultâneo, o Azure AD envia um pedido HTTP GET para registrada `LogoutUrl` de todos os aplicativos que o utilizador tem atualmente sessão iniciado no. Aplicativos devem responder a essa solicitação limpar todas as sessões que identifica o usuário e retornando um `200` resposta. Se desejar suportar o início de sessão único na sua aplicação, tem de implementar como, por exemplo um `LogoutUrl` no código da aplicação. Pode definir o `LogoutUrl` do portal do Azure:

1. Navegue para o [Portal do Azure](https://portal.azure.com).
2. Escolha o seu Active Directory ao clicar na sua conta no canto superior direito da página.
3. No painel de navegação esquerda, escolha **do Azure Active Directory**, em seguida, escolha **registos das aplicações** e selecione a aplicação.
4. Clique em **definições**, em seguida, **propriedades** e localize o **URL de fim de sessão** caixa de texto. 

## <a name="token-acquisition"></a>Aquisição do token
Muitos aplicativos web precisam não apenas iniciar sessão do utilizador no, mas também acessar um serviço da web em nome de utilizador com a OAuth. Este cenário combina OpenID Connect para a autenticação de utilizador durante a aquisição em simultâneo um `authorization_code` que pode ser usado para obter `access_tokens` utilizando o [fluxo de código de autorização do OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Obter os Tokens de acesso
Para adquirir os tokens de acesso, terá de modificar o pedido de início de sessão acima:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Incluindo âmbitos de permissão no pedido e usando `response_type=code+id_token`, o `authorize` ponto final garante que o utilizador consentiu as permissões indicadas no `scope` parâmetro de consulta e retornar um código de autorização para o exchange para a sua aplicação um token de acesso.

### <a name="successful-response"></a>Resposta com êxito

Uma resposta com êxito utilizando `response_mode=form_post` se parece com:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que solicitou a aplicação. Pode utilizar o `id_token` para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| Código |Authorization_code que solicitou a aplicação. A aplicação pode utilizar o código de autorização para pedir um token de acesso para o recurso de destino. Authorization_codes tiverem vida curta e normalmente expiram após cerca de 10 minutos. |
| state |Se um parâmetro de estado está incluído na solicitação, o mesmo valor deve aparecer na resposta. A aplicação deve verificar que os valores de estado no pedido e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

Respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação pode processar corretamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação do desenvolvedor. |

Para obter uma descrição de códigos de erro possíveis e sua ação recomendada de cliente, consulte [códigos de erro para erros de autorização do ponto de extremidade](#error-codes-for-authorization-endpoint-errors).

Assim que obteve uma autorização `code` e uma `id_token`, pode iniciar sessão do utilizador e obter [tokens de acesso](access-tokens.md) em seu nome. Iniciar a sessão do utilizador no, é necessário validar o `id_token` exatamente como descrito acima. Para obter os tokens de acesso, pode seguir os passos descritos na secção "Utilizar o código de autorização para pedir um token de acesso" nosso [documentação de fluxo de código do OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre o [tokens de acesso](access-tokens.md).
* Saiba mais sobre o [ `id_token` afirmações e](id-tokens.md).
