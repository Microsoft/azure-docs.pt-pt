---
title: Autorize o acesso à aplicação web com o OpenID Connect & Azure AD [ Microsoft Docs
description: Este artigo descreve como usar mensagens HTTP para autorizar o acesso a aplicações web e APIs web no seu inquilino usando o Azure Ative Directory e OpenID Connect.
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
ms.openlocfilehash: eef8174056be7e6be35cea56788c0a519d02944e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154445"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizar o acesso a aplicações Web com o OpenID Connect e o Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) é uma simples camada de identidade construída em cima do protocolo OAuth 2.0. O OAuth 2.0 define mecanismos para obter e utilizar [**fichas**](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) de acesso para aceder a recursos protegidos, mas não definem métodos padrão para fornecer informações de identidade. A OpenID Connect implementa a autenticação como uma extensão ao processo de autorização OAuth 2.0. Fornece informações sobre o utilizador final [`id_token`](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) sob a forma de um que verifica a identidade do utilizador e fornece informações básicas sobre o utilizador.

O OpenID Connect é a nossa recomendação se estiver a construir uma aplicação web que esteja hospedada num servidor e acessada através de um browser.

## <a name="register-your-application-with-your-ad-tenant"></a>Registar a aplicação com o inquilino do AD
Em primeiro lugar, registe a sua candidatura junto do seu inquilino azure Ative Directory (Azure AD). Esta ação dar-lhe-á um ID de Aplicação para a aplicação e ativá-lo-á para receber tokens.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
   
1. Escolha o seu inquilino Azure AD selecionando a sua conta no canto superior direito da página, seguido selecionando a navegação do **Diretório switch** e, em seguida, selecionando o inquilino apropriado. 
   - Ignore este passo se tiver apenas um inquilino Azure AD sob a sua conta, ou se já selecionou o inquilino da AD Azure apropriado.
   
1. No portal Azure, procure e selecione **Azure Ative Directory**.
   
1. No menu esquerdo do **Diretório Ativo Azure,** selecione Registos de **Aplicações,** e, em seguida, selecione **Nova inscrição**.
   
1. Siga os avisos e crie uma nova aplicação. Não importa se se trata de uma aplicação web ou de um cliente público (mobile & desktop) para este tutorial, mas se quiser exemplos específicos para aplicações web ou aplicações de clientes públicos, confira os nossos [quickstarts](v1-overview.md).
   
   - **Nome** é o nome da aplicação e descreve a sua aplicação aos utilizadores finais.
   - Nos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .
   - Forneça o **Redirect URI**. Para aplicações web, este é o URL base da sua aplicação onde os utilizadores podem iniciar sessão.  Por exemplo, `http://localhost:12345`. Para clientes públicos (mobile & desktop), a Azure AD usa-o para devolver respostas simbólicas. Introduza um valor específico na aplicação.  Por exemplo, `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Assim que tiver concluído o registo, a Azure AD atribuirá à sua aplicação um identificador de cliente único (o ID de **aplicação).** Precisa deste valor nas secções seguintes, por isso copie-o a partir da página de candidatura.
   
1. Para encontrar a sua aplicação no portal Azure, selecione **registos**de Aplicações e, em seguida, selecione **Ver todas as aplicações**.

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação usando OpenID Connect

O fluxo de entrada mais básico contém os seguintes passos - cada um deles é descrito em detalhe abaixo.

![Fluxo de autenticação de ligação openid](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Documento de metadados OpenID Connect

O OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para uma aplicação realizar o sessão. Isto inclui informações como os URLs a utilizar e a localização das chaves de assinatura pública do serviço. O documento de metadados OpenID Connect pode ser encontrado em:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Os metadados são um simples documento de Notação de Objetos JavaScript (JSON). Consulte o seguinte corte, por exemplo. O conteúdo do snippet está totalmente descrito na [especificação OpenID Connect](https://openid.net). Note que fornecer uma identificação de inquilino em vez `common` de em vez de {inquilino} acima resultará em URIs específicos do inquilino no objeto JSON devolvido.

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

Se a sua aplicação tiver chaves de assinatura personalizadas como resultado `appid` da utilização da funcionalidade [de mapeamento de reclamações,](../develop/active-directory-claims-mapping.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) deve anexar um parâmetro de consulta contendo o ID da aplicação para obter um `jwks_uri` ponto de atenção para as informações chave de assinatura da sua aplicação. Por exemplo: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` `jwks_uri` contém `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`um de .

## <a name="send-the-sign-in-request"></a>Envie o pedido de inscrição

Quando a sua aplicação web precisa de autenticar o `/authorize` utilizador, deve direcionar o utilizador para o ponto final. Este pedido é semelhante à primeira etapa do Fluxo de Código de [Autorização OAuth 2.0,](v1-protocols-oauth-code.md)com algumas distinções importantes:

* O pedido deve `openid` incluir `scope` o âmbito do parâmetro.
* O `response_type` parâmetro deve `id_token`incluir .
* O pedido deve `nonce` incluir o parâmetro.

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
| inquilino |necessário |O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos são identificadores `contoso.onmicrosoft.com` `common` de inquilinos, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou para fichas independentes de inquilinos |
| client_id |necessário |O ID de aplicação atribuído à sua aplicação quando o registou no Azure AD. Pode encontrar isso no portal Azure. Clique no **Diretório Ativo do Azure,** clique em Registos de **Aplicações,** escolha a aplicação e localize o ID da aplicação na página de aplicação. |
| response_type |necessário |Deve `id_token` incluir o acesso ao OpenID Connect. Pode também incluir outros response_types, tais como `code` ou `token`. |
| scope | recomendado | A especificação OpenID Connect `openid`requer o âmbito, que se traduz na permissão "Inscreva-o" na UI de consentimento. Este e outros âmbitos oIDC são ignorados no ponto final da v1.0, mas ainda é uma melhor prática para clientes compatíveis com padrões. |
| nonce |necessário |Um valor incluído no pedido, gerado pela app, que está `id_token` incluído no resultado como uma reclamação. A aplicação pode então verificar este valor para mitigar ataques de repetição de tokens. O valor é tipicamente uma cadeia aleatória, única ou GUID que pode ser usada para identificar a origem do pedido. |
| redirect_uri | recomendado |O redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das redirect_uris que registou no portal, exceto que deve ser codificada. Caso não tenha, o agente de utilizador será enviado de volta para um dos URIs redirecionados registados para a aplicação, aleatoriamente. O comprimento máximo é de 255 bytes |
| response_mode |opcional |Especifica o método que deve ser usado para enviar o authorization_code resultante de volta para a sua aplicação. Os valores `form_post` suportados destinam-se ao post de *formulário HTTP* e `fragment` ao fragmento de *URL*. Para aplicações web, `response_mode=form_post` recomendamos a utilização para garantir a transferência mais segura de fichas para a sua aplicação. O padrão para qualquer fluxo, incluindo um id_token é `fragment`.|
| state |recomendado |Um valor incluído no pedido que é devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que desejes. Um valor único gerado aleatoriamente é normalmente usado para [prevenir ataques de falsificação de pedidos de local.](https://tools.ietf.org/html/rfc6749#section-10.12) O Estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que estavam. |
| pronta |opcional |Indica o tipo de interação do utilizador que é necessária. Atualmente, os únicos valores válidos são 'login', 'nenhum' e 'consentimento'. `prompt=login`força o utilizador a introduzir as suas credenciais nesse pedido, negando um único sinal. `prompt=none`é o oposto - garante que o utilizador não é apresentado com qualquer tipo de solicitação interativa. Se o pedido não puder ser concluído silenciosamente através de um sinal único, o ponto final devolve um erro. `prompt=consent`ativa o diálogo de consentimento da OAuth após a assinatura do utilizador, pedindo ao utilizador que conceda permissões à aplicação. |
| login_hint |opcional |Pode ser usado para pré-preencher o campo de endereços de utilizador/e-mail da página de sessão para o utilizador, se souber o seu nome de utilizador com antecedência. Muitas vezes as aplicações utilizam este parâmetro durante a reautenticação, `preferred_username` tendo já extraído o nome de utilizador de um início de sessão anterior utilizando a reclamação. |

Neste momento, é solicitado ao utilizador que introduza as suas credenciais e complete a autenticação.

### <a name="sample-response"></a>Resposta de amostra

Uma resposta da amostra, enviada para o `redirect_uri` pedido de inscrição especificado após a autenticação do utilizador, pode parecer-se com esta:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que a aplicação solicitou. Pode utilizar `id_token` o utilizador para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| state |Um valor incluído no pedido que também é devolvido na resposta simbólica. Um valor único gerado aleatoriamente é normalmente usado para [prevenir ataques de falsificação de pedidos de local.](https://tools.ietf.org/html/rfc6749#section-10.12) O Estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que estavam. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro `redirect_uri` também podem ser enviadas para a aplicação para que a aplicação possa manuseá-las adequadamente:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem, e pode ser usada para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto final de autorização

A tabela seguinte descreve os vários códigos `error` de erro que podem ser devolvidos no parâmetro da resposta ao erro.

| Código de Erro | Descrição | Ação do Cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro em falta necessário. |Corrija e reenvie o pedido. Este é um erro de desenvolvimento, e é tipicamente apanhado durante os testes iniciais. |
| unauthorized_client |O pedido do cliente não está autorizado a solicitar um código de autorização. |Isto ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino Azure AD do utilizador. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| access_denied |Proprietário de recursos negado consentimento |A aplicação do cliente pode notificar o utilizador de que não pode proceder a menos que o utilizador consinta. |
| unsupported_response_type |O servidor de autorização não suporta o tipo de resposta no pedido. |Corrija e reenvie o pedido. Este é um erro de desenvolvimento, e é tipicamente apanhado durante os testes iniciais. |
| server_error |O servidor encontrou um erro inesperado. |Tente o pedido. Estes erros podem resultar de condições temporárias. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor está temporariamente demasiado ocupado para lidar com o pedido. |Tente o pedido. A aplicação do cliente pode explicar ao utilizador que a sua resposta é adiada devido a uma condição temporária. |
| invalid_resource |O recurso-alvo é inválido porque não existe, a AD Azure não o encontra, ou não está corretamente configurado. |Isto indica que o recurso, caso exista, não foi configurado no inquilino. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |

## <a name="validate-the-id_token"></a>Validar a id_token

Apenas receber `id_token` um não é suficiente para autenticar o utilizador; deve validar a assinatura e `id_token` verificar as reclamações de acordo com os requisitos da sua aplicação. O ponto final do Azure AD utiliza jSON Web Tokens (JWTs) e criptografia de chaves públicas para assinar fichas e verificar se são válidas.

Pode optar por `id_token` validar o código do cliente, `id_token` mas uma prática comum é enviar o para um servidor de backend e executar a validação lá. 

Também pode querer validar reclamações adicionais dependendo do seu cenário. Algumas validações comuns incluem:

* Garantindo que o utilizador/organização se inscreveu para a aplicação.
* Garantindo que o utilizador tem autorização/privilégios adequados utilizando as `wids` ou `roles` reclamações adequadas. 
* A garantia de uma certa força de autenticação ocorreu, como a autenticação de vários fatores.

Uma vez validado `id_token`o , pode iniciar uma sessão com `id_token` o utilizador e utilizar as reclamações na aplicação para obter informações sobre o utilizador na sua aplicação. Estas informações podem ser utilizadas para exibição, registos, personalização, etc. Para mais `id_tokens` informações e reclamações, leia [a AAD id_tokens](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="send-a-sign-out-request"></a>Enviar um pedido de inscrição

Quando pretende assinar o utilizador fora da aplicação, não é suficiente para limpar os cookies da sua aplicação ou terminar a sessão com o utilizador. Também deve redirecionar o `end_session_endpoint` utilizador para a inscrição. Se não o fizer, o utilizador poderá reautenticar a sua aplicação sem voltar a introduzir as suas credenciais, pois terá uma única sessão de inscrição válida com o ponto final da AD Azure.

Pode simplesmente redirecionar o `end_session_endpoint` utilizador para a lista lista da lista no documento de metadados OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| post_logout_redirect_uri |recomendado |O URL para o que o utilizador deve ser redirecionado após o sucesso da assinatura.  Este URL deve corresponder a um dos URIs redirecionados registados para a sua aplicação no portal de registo da aplicação.  Se *post_logout_redirect_uri* não estiver incluído, o utilizador é mostrado uma mensagem genérica. |

## <a name="single-sign-out"></a>Fim de sessão único

Quando redireciona `end_session_endpoint`o utilizador para o , o Azure AD limpa a sessão do utilizador a partir do navegador. No entanto, o utilizador poderá ainda ser inscrito noutras aplicações que utilizem a AD Azure para autenticação. Para permitir que essas aplicações assinem o utilizador em simultâneo, `LogoutUrl` a Azure AD envia um pedido HTTP GET ao registo de todas as aplicações a que o utilizador está atualmente inscrito. As aplicações devem responder a este pedido, limpando `200` qualquer sessão que identifique o utilizador e devolvendo uma resposta. Se desejar apoiar uma única assinatura na sua aplicação, deve implementar tal `LogoutUrl` no código da sua aplicação. Pode definir `LogoutUrl` o do portal Azure:

1. Navegue para o [portal Azure.](https://portal.azure.com)
2. Escolha o seu Diretório Ativo clicando na sua conta no canto superior direito da página.
3. Do painel de navegação à esquerda, escolha **o Diretório Ativo Azure,** em seguida, escolha **os registos** da App e selecione a sua aplicação.
4. Clique em **Definições,** em **seguida, propriedades** e encontre a caixa de texto **URL de logout.** 

## <a name="token-acquisition"></a>Aquisição de Token
Muitas aplicações web precisam não só de assinar o utilizador, mas também de aceder a um serviço web em nome desse utilizador que usa a OAuth. Este cenário combina openID Connect para autenticação `authorization_code` do utilizador ao `access_tokens` mesmo tempo que adquire um que pode ser usado para obter o Fluxo de Código de [Autorização OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

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

Ao incluir os âmbitos de `response_type=code+id_token`permissão no pedido e utilização, o `authorize` ponto final `scope` garante que o utilizador consentiu nas permissões indicadas no parâmetro de consulta e devolve à sua aplicação um código de autorização para trocar por um token de acesso.

### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta bem sucedida, enviada para o `redirect_uri` uso, `response_mode=form_post`parece:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que a aplicação solicitou. Pode utilizar `id_token` o utilizador para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| code |O authorization_code que a app solicitou. A aplicação pode usar o código de autorização para solicitar um sinal de acesso para o recurso alvo. Authorization_codes são de curta duração, e normalmente expiram após cerca de 10 minutos. |
| state |Se um parâmetro estatal estiver incluído no pedido, o mesmo valor deve figurar na resposta. A aplicação deve verificar se os valores do Estado no pedido e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro `redirect_uri` também podem ser enviadas para a aplicação para que a aplicação possa manuseá-las adequadamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem, e pode ser usada para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |

Para obter uma descrição dos possíveis códigos de erro e da ação recomendada pelo cliente, consulte [códigos](#error-codes-for-authorization-endpoint-errors)de erro para erros de ponto final de autorização .

Assim que tiver uma `code` autorização `id_token`e um, pode inscrever o utilizador e obter [fichas](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) de acesso em seu nome. Para iniciar sessão com o `id_token` utilizador, tem de validar exatamente como acima descrito. Para obter fichas de acesso, pode seguir os passos descritos na secção "Use o código de autorização para solicitar um sinal de acesso" da nossa documentação de fluxo de [código OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [tokens](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)de acesso.
* Saiba mais sobre as [ `id_token` reivindicações.](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
