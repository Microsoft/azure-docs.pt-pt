---
title: Autorizar o acesso a aplicações web com OpenID Connect & Azure AD [ Microsoft Docs
description: Este artigo descreve como usar mensagens HTTP para autorizar o acesso a aplicações web e APIs web no seu inquilino usando O Diretório Ativo Azure e OpenID Connect.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: b719e866852d2e865c16c62fddd8c549ae505b7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85551563"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizar o acesso a aplicações Web com o OpenID Connect e o Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) é uma camada de identidade simples construída em cima do protocolo OAuth 2.0. A OAuth 2.0 define mecanismos para obter e utilizar fichas de acesso para aceder a recursos [**protegidos,**](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) mas não definem métodos padrão para fornecer informações de identidade. O OpenID Connect implementa a autenticação como extensão do processo de autorização OAuth 2.0. Fornece informações sobre o utilizador final sob a forma de um [`id_token`](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) que verifica a identidade do utilizador e fornece informações básicas sobre o perfil sobre o utilizador.

O OpenID Connect é a nossa recomendação se estiver a construir uma aplicação web que esteja hospedada num servidor e acedida através de um browser.

## <a name="register-your-application-with-your-ad-tenant"></a>Registar a aplicação com o inquilino do AD
Em primeiro lugar, registe a sua candidatura junto do seu inquilino Azure Ative Directory (Azure AD). Esta ação dar-lhe-á um ID de Aplicação para a aplicação e ativá-lo-á para receber tokens.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
   
1. Escolha o seu inquilino Azure AD selecionando a sua conta no canto superior direito da página, seguindo-se a seleção da navegação do **Diretório** da Switch e, em seguida, selecionando o inquilino apropriado. 
   - Ignore este passo se tiver apenas um inquilino AZure AD na sua conta, ou se já selecionou o inquilino Azure AD apropriado.
   
1. No portal Azure, procure e selecione **O Diretório Ativo Azure**.
   
1. No menu esquerdo do **Azure Ative,** selecione **Registos de Aplicações**e, em seguida, selecione **Novo registo**.
   
1. Siga as instruções e crie uma nova aplicação. Não importa se é uma aplicação web ou um cliente público (mobile & desktop) para este tutorial, mas se você gostaria de exemplos específicos para aplicações web ou aplicações de clientes públicos, confira os [nossos quickstarts](v1-overview.md).
   
   - **Nome** é o nome da aplicação e descreve a sua aplicação aos utilizadores finais.
   - Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**
   - Forneça o **URI de redirecionamento**. Para aplicações web, este é o URL base da sua app onde os utilizadores podem iniciar sposição.  Por exemplo, `http://localhost:12345`. Para o cliente público (mobile & desktop), a Azure AD usa-o para devolver respostas simbólicas. Introduza um valor específico na aplicação.  Por exemplo, `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Uma vez concluída a inscrição, a Azure AD atribuirá à sua aplicação um identificador de cliente único (o ID da **aplicação).** Precisa deste valor nas próximas secções, por isso copie-o da página de candidatura.
   
1. Para encontrar a sua aplicação no portal Azure, selecione **registos de Aplicações**e, em seguida, selecione **Ver todas as aplicações**.

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação utilizando o OpenID Connect

O fluxo de entrada mais básico contém os seguintes passos - cada um deles é descrito em detalhe abaixo.

![Fluxo de autenticação de ligação OpenId](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Documento de metadados OpenID Connect

O OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para que uma aplicação realize o s-in. Isto inclui informações como os URLs a utilizar e a localização das chaves de assinatura pública do serviço. O documento de metadados OpenID Connect pode ser encontrado em:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Os metadados são um documento simples de Notação de Objetos JavaScript (JSON). Veja o seguinte corte para um exemplo. O conteúdo do snippet está totalmente descrito na [especificação OpenID Connect](https://openid.net). Note que fornecer um ID de inquilino em vez `common` de no lugar de {inquilino} acima resultará em URIs específicos do inquilino no objeto JSON devolvido.

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

Se a sua aplicação tiver teclas de assinatura personalizadas como resultado da utilização da funcionalidade [de mapeamento de sinistros,](../develop/active-directory-claims-mapping.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) tem de anexar um `appid` parâmetro de consulta que contenha o ID da aplicação, de forma a obter uma `jwks_uri` indicação para as informações-chave de assinatura da sua aplicação. Por exemplo: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contém um `jwks_uri` de `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` .

## <a name="send-the-sign-in-request"></a>Envie o pedido de inscrição

Quando a sua aplicação web necessitar de autenticar o utilizador, deve direcionar o utilizador para o `/authorize` ponto final. Este pedido é semelhante à primeira parte do Fluxo do Código de [Autorização OAuth 2.0,](v1-protocols-oauth-code.md)com algumas distinções importantes:

* O pedido deve incluir o âmbito `openid` do `scope` parâmetro.
* O `response_type` parâmetro deve incluir `id_token` .
* O pedido deve incluir o `nonce` parâmetro.

Então, um pedido de amostra seria assim:

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

| Parâmetro | Tipo | Descrição |
| --- | --- | --- |
| inquilino |obrigatório |O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos são identificadores de inquilinos, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `contoso.onmicrosoft.com` ou para `common` tokens independentes do inquilino |
| client_id |obrigatório |O ID da aplicação atribuído à sua app quando a registou no Azure AD. Pode encontrar isto no portal Azure. Clique em **Azure Ative Directory,** clique em **Registos de Aplicações,** escolha a aplicação e localize o ID da aplicação na página da aplicação. |
| response_type |obrigatório |Deve incluir `id_token` para o iniciar sê-in OpenID Connect. Também pode incluir outros response_types, tais `code` `token` como. |
| scope | recomendado | A especificação OpenID Connect requer o âmbito `openid` , que se traduz na permissão "Iniciar a sua inscrição" na UI de consentimento. Este e outros âmbitos OIDC são ignorados no ponto final v1.0, mas ainda é uma das melhores práticas para os clientes que cumprem padrões. |
| nonce |obrigatório |Um valor incluído no pedido, gerado pela app, que está incluído no resultado `id_token` como uma reclamação. A aplicação pode então verificar este valor para mitigar os ataques de reprodução de token. O valor é tipicamente uma corda aleatória, única ou GUID que pode ser usada para identificar a origem do pedido. |
| redirect_uri | recomendado |O redirect_uri da sua app, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das redirect_uris que registou no portal, exceto que deve estar codificada url. Se faltar, o agente do utilizador será enviado de volta para um dos URIs de redirecionamento registados para a aplicação, aleatoriamente. O comprimento máximo é de 255 bytes |
| response_mode |opcional |Especifica o método que deve ser usado para enviar o authorization_code resultante de volta para a sua aplicação. Os valores suportados são `form_post` para post de formulário *HTTP* e para fragmento `fragment` de *URL*. Para aplicações web, recomendamos a utilização `response_mode=form_post` para garantir a transferência mais segura de fichas para a sua aplicação. O padrão para qualquer fluxo, incluindo um id_token é `fragment` .|
| state |recomendado |Um valor incluído no pedido que é devolvido na resposta simbólica. Pode ser uma série de conteúdos que desejes. Um valor único gerado aleatoriamente é normalmente usado para [prevenir ataques de falsificação de pedidos de trans-locais](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou a vista em que estavam. |
| rápido |opcional |Indica o tipo de interação do utilizador que é necessária. Atualmente, os únicos valores válidos são 'login', 'nenhum', e 'consentimento'. `prompt=login` força o utilizador a introduzir as suas credenciais nesse pedido, negando um único sinal. `prompt=none` é o oposto - garante que o utilizador não é apresentado com qualquer solicitação interativa. Se o pedido não puder ser concluído silenciosamente através de um único sinal, o ponto final retorna um erro. `prompt=consent` aciona o diálogo de consentimento OAuth após a indicação do utilizador, pedindo ao utilizador que conceda permissões à aplicação. |
| login_hint |opcional |Pode ser usado para pré-preenchimento do nome de utilizador/endereço de endereço de e-mail da página de inscrição para o utilizador, se souber o seu nome de utilizador com antecedência. Muitas vezes as aplicações usam este parâmetro durante a reautornação, tendo já extraído o nome de utilizador de um pré-in usando a `preferred_username` alegação. |

Neste momento, pede-se ao utilizador que introduza as suas credenciais e complete a autenticação.

### <a name="sample-response"></a>Resposta de amostra

Uma resposta de amostra, enviada para o `redirect_uri` especificado no pedido de inscrição após a autenticação do utilizador, pode ser assim:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que a aplicação pediu. Pode utilizar `id_token` para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| state |Um valor incluído no pedido que também é devolvido na resposta simbólica. Um valor único gerado aleatoriamente é normalmente usado para [prevenir ataques de falsificação de pedidos de trans-locais](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou a vista em que estavam. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para `redirect_uri` a aplicação para que a aplicação possa manuseá-las adequadamente:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usado para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto final de autorização

A tabela seguinte descreve os vários códigos de erro que podem ser devolvidos no `error` parâmetro da resposta de erro.

| Código de Erro | Descrição | Ação do Cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro exigido em falta. |Corrija e reenvia o pedido. Este é um erro de desenvolvimento, e é tipicamente apanhado durante os testes iniciais. |
| unauthorized_client |O pedido do cliente não está autorizado a solicitar um código de autorização. |Isto ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino AZure AD do utilizador. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| access_denied |Proprietário de recursos negado consentimento |A aplicação do cliente pode notificar o utilizador de que não pode prosseguir a menos que o utilizador consinta. |
| unsupported_response_type |O servidor de autorização não suporta o tipo de resposta no pedido. |Corrija e reenvia o pedido. Este é um erro de desenvolvimento, e é tipicamente apanhado durante os testes iniciais. |
| server_error |O servidor encontrou um erro inesperado. |Recandidutar o pedido. Estes erros podem resultar de condições temporárias. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor está temporariamente demasiado ocupado para lidar com o pedido. |Recandidutar o pedido. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a uma condição temporária. |
| invalid_resource |O recurso-alvo é inválido porque não existe, a Azure AD não pode encontrá-lo, ou não está corretamente configurado. |Isto indica que o recurso, se existir, não foi configurado no arrendatário. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |

## <a name="validate-the-id_token"></a>Validar o id_token

Apenas receber um `id_token` não é suficiente para autenticar o utilizador; tem de validar a assinatura e verificar as reclamações nos `id_token` requisitos da sua aplicação. O ponto final Azure AD utiliza tokens web JSON (JWTs) e criptografia de chaves públicas para assinar fichas e verificar se são válidos.

Pode optar por validar o código do `id_token` cliente, mas uma prática comum é enviar o `id_token` para um servidor de backend e realizar a validação lá. 

Também pode pretender validar reclamações adicionais dependendo do seu cenário. Algumas validações comuns incluem:

* Garantindo que o utilizador/organização se inscreveu para a aplicação.
* Garantindo que o utilizador tem a devida autorização/privilégios utilizando as `wids` reclamações ou `roles` reclamações. 
* Garantindo uma certa força de autenticação, como a autenticação de vários fatores.

Uma vez `id_token` validado, poderá iniciar uma sessão com o utilizador e utilizar as reclamações na `id_token` aplicação para obter informações sobre o utilizador na sua aplicação. Estas informações podem ser utilizadas para exibição, registos, personalização, etc. Para mais informações sobre `id_tokens` e reclamações, leia [a AAD id_tokens.](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

## <a name="send-a-sign-out-request"></a>Enviar um pedido de sinalização

Quando pretender assinar o utilizador fora da app, não basta limpar os cookies da sua aplicação ou terminar a sessão com o utilizador. Também deve redirecionar o utilizador para a `end_session_endpoint` sinscê-lo. Se não o fizer, o utilizador poderá reautorá-lo à sua aplicação sem introduzir novamente as suas credenciais, pois terá uma sessão de sessão de sessão de sessão de inscrição única válida com o ponto final Azure AD.

Pode simplesmente redirecionar o utilizador para a `end_session_endpoint` lista no documento de metadados OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parâmetro | Tipo | Descrição |
| --- | --- | --- |
| post_logout_redirect_uri |recomendado |O URL para o que o utilizador deve ser redirecionado após a assinatura com sucesso.  Este URL deve corresponder a um dos URIs de redirecionamento registados para a sua aplicação no portal de registo de aplicações.  Se *post_logout_redirect_uri* não estiver incluído, o utilizador é mostrado uma mensagem genérica. |

## <a name="single-sign-out"></a>Fim de sessão único

Quando redireciona o utilizador para o `end_session_endpoint` AD , o Azure AD limpa a sessão do utilizador a partir do navegador. No entanto, o utilizador pode ainda ser inscrito noutras aplicações que utilizem a Azure AD para autenticação. Para permitir que essas aplicações assinem o utilizador em simultâneo, a AZure AD envia um pedido HTTP GET ao registo `LogoutUrl` de todas as aplicações a que o utilizador está atualmente inscrito. As aplicações devem responder a este pedido, limpando qualquer sessão que identifique o utilizador e devolvendo uma `200` resposta. Se pretender apoiar uma única assinatura na sua aplicação, deve implementar tal `LogoutUrl` no código da sua aplicação. Pode definir o `LogoutUrl` portal Azure:

1. Navegue até ao [portal Azure.](https://portal.azure.com)
2. Escolha o seu Diretório Ativo clicando na sua conta no canto superior direito da página.
3. A partir do painel de navegação à esquerda, escolha **O Diretório Ativo Azure,** em seguida, escolha **as inscrições da App** e selecione a sua aplicação.
4. Clique em **Definições,** em seguida, **Propriedades** e encontre a caixa de texto **URL logout.** 

## <a name="token-acquisition"></a>Aquisição de Token
Muitas aplicações web precisam não só de assinar o utilizador, mas também de aceder a um serviço web em nome desse utilizador usando o OAuth. Este cenário combina o OpenID Connect para a autenticação do utilizador, ao mesmo tempo que adquire um `authorization_code` que pode ser usado para obter o Fluxo de Código de Autorização `access_tokens` [OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Obter Tokens de acesso
Para adquirir fichas de acesso, precisa modificar o pedido de inscrição a partir de cima:

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

Ao incluir os âmbitos de permissão no pedido e na `response_type=code+id_token` utilização, o ponto final garante que o utilizador `authorize` consentiu nas permissões indicadas no `scope` parâmetro de consulta e devolve à sua aplicação um código de autorização para trocar por um token de acesso.

### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta bem sucedida, enviada para o `redirect_uri` `response_mode=form_post` uso, parece:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que a aplicação pediu. Pode utilizar `id_token` para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| code |O authorization_code que a aplicação solicitou. A aplicação pode usar o código de autorização para solicitar um token de acesso para o recurso alvo. Authorization_codes são de curta duração, e normalmente expiram após cerca de 10 minutos. |
| state |Se um parâmetro de estado for incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se os valores do Estado no pedido e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para `redirect_uri` a aplicação para que a aplicação possa manuseá-las adequadamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usado para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |

Para obter uma descrição dos possíveis códigos de erro e da ação recomendada do cliente, consulte [códigos de erro para obter erros de autorização](#error-codes-for-authorization-endpoint-errors)no ponto final .

Uma vez que tenha obtido uma autorização `code` e `id_token` uma, pode inscrever o utilizador e obter [fichas de acesso](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) em seu nome. Para iniciar sê-lo, tem de validar exatamente o `id_token` que se descreve acima. Para obter tokens de acesso, pode seguir os passos descritos na secção "Use o código de autorização para solicitar um token de acesso" da nossa documentação de fluxo de [código OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [tokens de acesso.](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Saiba mais sobre as [ `id_token` reivindicações e reivindicações.](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
