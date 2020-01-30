---
title: Protocolo OpenID Connect-plataforma Microsoft Identity | Azure
description: Construa aplicações web utilizando a implementação da plataforma de identidade Microsoft do protocolo de autenticação OpenID Connect.
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0ed1cb6a080a35fa81c6a859f88d987020c8504c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773329"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Plataforma de identidade da Microsoft e protocolo OpenID Connect

OpenID Connect é um protocolo de autenticação construído no OAuth 2.0 que pode utilizar para assinar de forma segura num utilizador uma aplicação web. Quando utilizar a implementação do OpenID Connect na plataforma de identidade da Microsoft, pode adicionar acesso de iniciação e acesso API às suas aplicações baseadas na Web. Este artigo mostra como fazer isto independentemente do idioma e descreve como enviar e receber mensagens HTTP sem usar quaisquer bibliotecas de código aberto da Microsoft.

> [!NOTE]
> O ponto final da plataforma de identidade da Microsoft não suporta todos os cenários e funcionalidades do Azure Ative Directory (Azure AD). Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

[O OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) alarga o protocolo de *autorização* OAuth 2.0 para utilizar como protocolo de *autenticação,* para que possa fazer um único sinal usando o OAuth. O OpenID Connect introduz o conceito de ficha *de identificação,* que é um símbolo de segurança que permite ao cliente verificar a identidade do utilizador. O token ID também obtém informações básicas de perfil sobre o utilizador. Uma vez que o OpenID Connect estende o OAuth 2.0, as aplicações podem adquirir de forma segura fichas de *acesso,* que podem ser usadas para aceder a recursos que são protegidos por um servidor de [autorização.](active-directory-v2-protocols.md#the-basics) O ponto final da plataforma de identidade da Microsoft também permite que aplicações de terceiros que estejam registadas com a AD Azure emitam tokens de acesso para recursos seguros, como APIs Web. Para obter mais informações sobre como configurar uma aplicação para emitir fichas de acesso, consulte [Como registar uma aplicação com o ponto final da plataforma de identidade da Microsoft](quickstart-register-app.md). Recomendamos que utilize o OpenID Connect se estiver a construir uma [aplicação web](v2-app-types.md#web-apps) que esteja hospedada num servidor e acessada através de um browser.

## <a name="protocol-diagram-sign-in"></a>Diagrama de protocolo: Iniciar sessão

O fluxo de entrada mais básico tem os passos mostrados no próximo diagrama. Cada passo é descrito em detalhe neste artigo.

![Protocolo OpenID Connect: Iniciar sessão](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Buscar o documento de metadados OpenID Connect

O OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para uma aplicação fazer o sessão. Isso inclui informações como as URLs a serem usadas e o local das chaves de assinatura públicas do serviço. Para o ponto final da plataforma de identidade da Microsoft, este é o documento de metadados OpenID Connect que deve utilizar:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Experimente! Clique [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) para ver a configuração dos inquilinos `common`.

O `{tenant}` pode ter um de quatro valores:

| Valor | Descrição |
| --- | --- |
| `common` |Os utilizadores com uma conta pessoal da Microsoft e uma conta de trabalho ou escola a partir do Azure AD podem iniciar sessão na aplicação. |
| `organizations` |Apenas os utilizadores com contas de trabalho ou de escola da Azure AD podem inscrever-se na aplicação. |
| `consumers` |Apenas os utilizadores com uma conta pessoal da Microsoft podem iniciar sessão na aplicação. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Apenas os utilizadores de um inquilino da AD Azure específico (sejam membros do diretório com uma conta de trabalho ou de escola, ou são hóspedes do diretório com uma conta pessoal da Microsoft) podem inscrever-se na aplicação. O nome de domínio amigável do locatário do Azure AD ou o identificador GUID do locatário pode ser usado. Você também pode usar o inquilino de consumo, `9188040d-6c67-4c5b-b112-36a304b66dad`, no lugar do inquilino `consumers`.  |

Os metadados são um simples documento de Notação de Objetos JavaScript (JSON). Consulte o seguinte corte, por exemplo. O conteúdo do snippet está totalmente descrito na [especificação OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

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

Se a sua aplicação tiver chaves de assinatura personalizadas como resultado da utilização da funcionalidade [de mapeamento de sinistros,](active-directory-claims-mapping.md) deve anexar um parâmetro de consulta de `appid` contendo o ID da aplicação, a fim de obter um `jwks_uri` apontando para as informações chave de assinatura da sua aplicação. Por exemplo: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contém um `jwks_uri` de `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

Normalmente, utilizaria este documento de metadados para configurar uma biblioteca OpenID Connect ou SDK; a biblioteca usaria os metadados para fazer o seu trabalho. No entanto, se não estiver a utilizar uma biblioteca OpenID Connect pré-construída, pode seguir os passos no restante deste artigo para fazer o início de sessão numa aplicação web utilizando o ponto final da plataforma de identidade da Microsoft.

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de entrada

Quando a sua aplicação web precisa de autenticar o utilizador, pode direcionar o utilizador para o `/authorize` ponto final. Este pedido é semelhante à primeira etapa do fluxo de código de [autorização OAuth 2.0,](v2-oauth2-auth-code-flow.md)com estas distinções importantes:

* O pedido deve incluir o âmbito `openid` no parâmetro `scope`.
* O parâmetro `response_type` deve incluir `id_token`.
* O pedido deve incluir o parâmetro `nonce`.

> [!IMPORTANT]
> Para solicitar com sucesso um símbolo de identificação do ponto final/autorização, o registo da aplicação no portal de [registo](https://portal.azure.com) deve ter a concessão implícita de id_tokens habilitada no separador Autenticação (que define a bandeira `oauth2AllowIdTokenImplicitFlow` no manifesto de [aplicação](reference-app-manifest.md) para `true`). Se não estiver ativado, será devolvido um erro `unsupported_response`: "O valor previsto para o parâmetro de entrada 'response_type' não é permitido para este cliente. O valor esperado é 'código'"

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
> Clique no seguinte link para executar este pedido. Depois de iniciar sessão, o seu navegador será redirecionado para `https://localhost/myapp/`, com um token de identificação na barra de endereços. Note que este pedido utiliza `response_mode=fragment` (apenas para efeitos de demonstração). Recomendamos que utilize `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | Pode utilizar o valor `{tenant}` no caminho do pedido de controlo de quem pode iniciar sessão na aplicação. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatário. Para mais informações, consulte o [protocolo básico](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Obrigatório | A **ID do aplicativo (cliente)** que a [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `response_type` | Obrigatório | Deve incluir `id_token` para o acesso ao OpenID Connect. Pode também incluir outros valores `response_type`, como `code`. |
| `redirect_uri` | Recomendado | O URI de redirecionamento do seu aplicativo, em que as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Deve corresponder exatamente a um dos URIs redirecionados registados no portal, exceto que deve ser codificado por URL. Se não estiver presente, o ponto final escolherá um redirect_uri registado aleatoriamente para enviar o utilizador de volta. |
| `scope` | Obrigatório | Uma lista de escopos separados por espaços. Para o OpenID Connect, deve incluir o âmbito `openid`, que se traduz na permissão "Inscreva-o" na UI de consentimento. Pode também incluir outros âmbitos neste pedido de solicitação de consentimento. |
| `nonce` | Obrigatório | Um valor incluído no pedido, gerado pela app, que será incluído no valor id_token resultante como reclamação. A aplicação pode verificar este valor para mitigar ataques de repetição de tokens. O valor tipicamente é uma cadeia aleatória e única que pode ser usada para identificar a origem do pedido. |
| `response_mode` | Recomendado | Especifica o método que deve ser usado para enviar o código de autorização resultante de volta para a sua aplicação. Pode ser `form_post` ou `fragment`. Para aplicações web, recomendamos a utilização de `response_mode=form_post`, para garantir a transferência mais segura de fichas para a sua aplicação. |
| `state` | Recomendado | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que queiras. Um valor único gerado aleatoriamente é normalmente usado para [evitar ataques de falsificação de](https://tools.ietf.org/html/rfc6749#section-10.12)pedidos de local. O Estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que o utilizador estava ligado. |
| `prompt` | Opcional | Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos neste momento são `login`, `none`e `consent`. A alegação `prompt=login` obriga o utilizador a introduzir as suas credenciais nesse pedido, o que nega a inscrição única. A `prompt=none` afirmação é o oposto. Esta alegação garante que o utilizador não é apresentado com qualquer solicitação interativa. Se o pedido não puder ser concluído silenciosamente através de um único sinal, o ponto final da plataforma de identidade da Microsoft devolve um erro. A alegação `prompt=consent` aciona o diálogo de consentimento da OAuth após a assinatura do utilizador. O diálogo pede ao utilizador que conceda permissões à aplicação. |
| `login_hint` | Opcional | Pode utilizar este parâmetro para pré-preencher o nome de utilizador e o campo de endereços de e-mail da página de início de sessão para o utilizador, se souber o nome de utilizador com antecedência. Muitas vezes, as aplicações utilizam este parâmetro durante a reautenticação, depois de já terem extraído o nome de utilizador de um início de sessão anterior, utilizando a `preferred_username` reivindicação. |
| `domain_hint` | Opcional | O reino do utilizador num diretório federado.  Isto ignora o processo de descoberta baseado em e-mail que o utilizador passa na página de entrada, para uma experiência de utilizador um pouco mais simplificada. Para os inquilinos que são federados através de um diretório no local como a AD FS, isso resulta frequentemente num insessão sem emenda devido à sessão de login existente. |

Neste ponto, o utilizador é solicitado a introduzir as suas credenciais e a completar a autenticação. O ponto final da plataforma de identidade da Microsoft verifica que o utilizador consentiu nas permissões indicadas no parâmetro de consulta `scope`. Se o utilizador não tiver consentido com nenhuma dessas permissões, o ponto final da plataforma de identidade da Microsoft leva o utilizador a consentir com as permissões necessárias. Você pode ler mais sobre [permissões, consentimento e aplicativos multi-inquilinos.](v2-permissions-and-consent.md)

Após o utilizador autenticar e conceder o consentimento, o ponto final da plataforma de identidade da Microsoft devolve uma resposta à sua aplicação no URI de redirecionamento indicado utilizando o método especificado no parâmetro `response_mode`.

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem sucedida quando se usa `response_mode=form_post` se parece com isto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `id_token` | O token de ID que o aplicativo solicitou. Pode utilizar o parâmetro `id_token` para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Para obter mais informações sobre fichas de identificação e seus conteúdos, consulte a [referência`id_tokens`](id-tokens.md). |
| `state` | Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para o URI redirecionamento para que a aplicação possa manuseá-las. Uma resposta de erro é parecida com esta:

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

A tabela seguinte descreve códigos de erro que podem ser devolvidos no parâmetro `error` da resposta ao erro:

| Código de erro | Descrição | Ação do cliente |
| --- | --- | --- |
| `invalid_request` | Erro de protocolo, como um parâmetro em falta e necessário. |Corrija e reenvie a solicitação. Este é um erro de desenvolvimento que normalmente é apanhado durante os testes iniciais. |
| `unauthorized_client` | O pedido do cliente não pode solicitar um código de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| `access_denied` | O dono do recurso negou o consentimento. |O aplicativo cliente pode notificar o usuário de que ele não pode continuar, a menos que o usuário consentisse. |
| `unsupported_response_type` |O servidor de autorização não oferece suporte ao tipo de resposta na solicitação. |Corrija e reenvie a solicitação. Este é um erro de desenvolvimento que normalmente é apanhado durante os testes iniciais. |
| `server_error` | O servidor encontrou um erro inesperado. |Repita a solicitação. Esses erros podem resultar de condições temporárias. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a um erro temporário. |
| `temporarily_unavailable` | O servidor está temporariamente muito ocupado para lidar com a solicitação. |Repita a solicitação. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a uma condição temporária. |
| `invalid_resource` | O recurso-alvo é inválido porque ou não existe, o Azure AD não consegue encontrá-lo, ou não está corretamente configurado. |Isto indica que o recurso, se existir, não foi configurado no inquilino. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |

## <a name="validate-the-id-token"></a>Validar o token de ID

Receber apenas uma id_token não é suficiente para autenticar o utilizador; deve validar a assinatura do id_token e verificar as reclamações no token de acordo com os requisitos da sua aplicação. O ponto final da plataforma de identidade da Microsoft utiliza [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chaves públicas para assinar fichas e verificar se são válidas.

Pode optar por validar o `id_token` no código do cliente, mas uma prática comum é enviar o `id_token` para um servidor de backend e fazer a validação lá. Uma vez validado a assinatura do id_token, existem algumas reclamações que terá de verificar. Consulte a [referência`id_token`](id-tokens.md) para mais informações, incluindo [Validação de Fichas](id-tokens.md#validating-an-id_token) e [Informações Importantes sobre A Assinatura](active-directory-signing-key-rollover.md)de Capotamento de Chaves . Recomendamos a utilização de uma biblioteca para analisar e validar fichas - há pelo menos uma disponível para a maioria das línguas e plataformas.

Também pode querer validar reclamações adicionais dependendo do seu cenário. Algumas validações comuns incluem:

* Garantindo que o utilizador/organização se inscreveu para a aplicação.
* Garantir que o utilizador tem autorização/privilégios adequados
* A garantia de uma certa força de autenticação ocorreu, como a autenticação de vários fatores.

Uma vez validado o id_token, pode iniciar uma sessão com o utilizador e utilizar as reclamações no id_token para obter informações sobre o utilizador na sua aplicação. Estas informações podem ser utilizadas para exibição, registos, personalização, etc.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída

Quando pretende assinar o utilizador a partir da sua aplicação, não basta limpar os cookies da sua aplicação ou terminar a sessão do utilizador. Também deve redirecionar o utilizador para o ponto final da plataforma de identidade da Microsoft para assinar. Se não o fizer, o utilizador reautentica a sua aplicação sem voltar a introduzir as suas credenciais, pois terá uma sessão de entrada única válida com o ponto final da plataforma de identidade da Microsoft.

Pode redirecionar o utilizador para o `end_session_endpoint` listado no documento de metadados OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parâmetro | Condição | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Recomendado | O URL para o que o utilizador é redirecionado depois de assinar com sucesso. Se o parâmetro não estiver incluído, o utilizador é mostrado uma mensagem genérica que é gerada pelo ponto final da plataforma de identidade da Microsoft. Este URL deve corresponder a um dos URIs redirecionados registados para a sua aplicação no portal de registo da aplicação. |

## <a name="single-sign-out"></a>Fim de sessão único

Quando redireciona o utilizador para o `end_session_endpoint`, o ponto final da plataforma de identidade da Microsoft iliba a sessão do utilizador a partir do navegador. No entanto, o utilizador poderá ainda ser inscrito noutras aplicações que utilizem as contas da Microsoft para autenticação. Para permitir que essas aplicações assinem o utilizador em simultâneo, o ponto final da plataforma de identidade da Microsoft envia um pedido HTTP GET para o `LogoutUrl` registado de todas as aplicações em que o utilizador está atualmente inscrito. As aplicações devem responder a este pedido, limpando qualquer sessão que identifique o utilizador e devolvendo uma resposta `200`. Se pretender apoiar uma única inscrição na sua aplicação, deve implementar esse `LogoutUrl` no código da sua aplicação. Pode definir o `LogoutUrl` a partir do portal de registo da aplicação.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagrama de protocolo: Aquisição de fichas de acesso

Muitas aplicações web precisam não só de inscrever o utilizador, mas também de aceder a um serviço web em nome do utilizador, utilizando a OAuth. Este cenário combina o OpenID Connect para a autenticação do utilizador, ao mesmo tempo que obtém um código de autorização que pode utilizar para obter fichas de acesso se estiver a utilizar o fluxo de código de autorização OAuth.

O fluxo completo de entrada e aquisição de token OpenID Connect é semelhante ao diagrama seguinte. Descrevemos cada passo em detalhe nas próximas secções do artigo.

![Protocolo OpenID Connect: Aquisição de Token](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Obter tokens de acesso
Para adquirir fichas de acesso, modifique o pedido de inscrição:

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
> Clique no seguinte link para executar este pedido. Depois de iniciar sessão, o seu navegador é redirecionado para `https://localhost/myapp/`, com um token de identificação e um código na barra de endereços. Note que este pedido utiliza `response_mode=fragment` apenas para fins de demonstração. Recomendamos que utilize `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Ao incluir os âmbitos de permissão no pedido e utilizando `response_type=id_token code`, o ponto final da plataforma de identidade da Microsoft garante que o utilizador consentiu nas permissões indicadas no parâmetro de consulta `scope`. Devolve um código de autorização à sua aplicação para trocar por um token de acesso.

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem sucedida ao usar `response_mode=form_post` se parece com isto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `id_token` | O token de ID que o aplicativo solicitou. Você pode usar o token de ID para verificar a identidade do usuário e iniciar uma sessão com o usuário. Encontrará mais detalhes sobre fichas de identificação e seus conteúdos na [referência`id_tokens`.](id-tokens.md) |
| `code` | O código de autorização solicitado pelo aplicativo. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Um código de autorização é de curta duração. Normalmente, um código de autorização expira em cerca de 10 minutos. |
| `state` | Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para o URI redirecionado para que a aplicação possa manuseá-las adequadamente. Uma resposta de erro é parecida com esta:

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

Para uma descrição de possíveis códigos de erro e respostas recomendadas ao cliente, consulte [códigos](#error-codes-for-authorization-endpoint-errors)de erro para erros de ponto final de autorização .

Quando tiver um código de autorização e um token de identificação, pode iniciar sessão e obter fichas de acesso em seu nome. Para iniciar sessão com o utilizador, tem de validar o token de identificação [exatamente como descrito](id-tokens.md#validating-an-id_token). Para obter fichas de acesso, siga os passos descritos na documentação de fluxo de [código OAuth](v2-oauth2-auth-code-flow.md#request-an-access-token).
