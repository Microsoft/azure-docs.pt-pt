---
title: Protocolo OpenID Connect - Plataforma de identidade da Microsoft Azure
description: Construa aplicações web utilizando a implementação da plataforma de identidade Microsoft do protocolo de autenticação OpenID Connect.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 161f97dc99ce5ce16d7c40126b95a769c4b79621
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868322"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Plataforma de identidade da Microsoft e protocolo OpenID Connect

OpenID Connect é um protocolo de autenticação construído no OAuth 2.0 que pode utilizar para assinar de forma segura num utilizador uma aplicação web. Quando utilizar a implementação do OpenID Connect na plataforma de identidade da Microsoft, pode adicionar acesso de iniciação e acesso API às suas aplicações baseadas na Web. Este artigo mostra como fazer isto independentemente do idioma e descreve como enviar e receber mensagens HTTP sem usar quaisquer bibliotecas de código aberto da Microsoft.

[O OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) alarga o protocolo de *autorização* OAuth 2.0 para utilizar como protocolo de *autenticação,* para que possa fazer um único sinal usando o OAuth. O OpenID Connect introduz o conceito de ficha *de identificação,* que é um símbolo de segurança que permite ao cliente verificar a identidade do utilizador. O token ID também obtém informações básicas de perfil sobre o utilizador. Uma vez que o OpenID Connect estende o OAuth 2.0, as aplicações podem adquirir de forma segura fichas de *acesso,* que podem ser usadas para aceder a recursos que são protegidos por um servidor de [autorização.](active-directory-v2-protocols.md#the-basics) O ponto final da plataforma de identidade da Microsoft também permite que aplicações de terceiros que estejam registadas com a AD Azure emitam tokens de acesso para recursos seguros, como APIs web. Para obter mais informações sobre como configurar uma aplicação para emitir fichas de acesso, consulte [Como registar uma aplicação com o ponto final da plataforma de identidade da Microsoft](quickstart-register-app.md). Recomendamos que utilize o OpenID Connect se estiver a construir uma [aplicação web](v2-app-types.md#web-apps) que esteja hospedada num servidor e acessada através de um browser.

## <a name="protocol-diagram-sign-in"></a>Diagrama de protocolo: Iniciar sessão

O fluxo de entrada mais básico tem os passos mostrados no próximo diagrama. Cada passo é descrito em detalhe neste artigo.

![Protocolo OpenID Connect: Iniciar sessão](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Buscar o documento de metadados OpenID Connect

O OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para uma aplicação fazer o sessão. Isto inclui informações como os URLs a utilizar e a localização das chaves de assinatura pública do serviço. Para o ponto final da plataforma de identidade da Microsoft, este é o documento de metadados OpenID Connect que deve utilizar:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Experimente! Clique [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) para `common` ver a configuração dos inquilinos.

A `{tenant}` lata pode tomar um de quatro valores:

| Valor | Descrição |
| --- | --- |
| `common` |Os utilizadores com uma conta pessoal da Microsoft e uma conta de trabalho ou escola a partir do Azure AD podem iniciar sessão na aplicação. |
| `organizations` |Apenas os utilizadores com contas de trabalho ou de escola da Azure AD podem inscrever-se na aplicação. |
| `consumers` |Apenas os utilizadores com uma conta pessoal da Microsoft podem iniciar sessão na aplicação. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Apenas os utilizadores de um inquilino da AD Azure específico (sejam membros do diretório com uma conta de trabalho ou de escola, ou são hóspedes do diretório com uma conta pessoal da Microsoft) podem inscrever-se na aplicação. Ou pode ser usado o nome de domínio amigável do inquilino Azure AD ou o identificador GUID do inquilino. Você também pode usar `9188040d-6c67-4c5b-b112-36a304b66dad`o inquilino do `consumers` consumidor, no lugar do inquilino.  |

Os metadados são um simples documento de Notação de Objetos JavaScript (JSON). Consulte o seguinte corte, por exemplo. O conteúdo do snippet está totalmente descrito na [especificação OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```json
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

Se a sua aplicação tiver chaves de assinatura personalizadas como resultado `appid` da utilização da funcionalidade [de mapeamento de reclamações,](active-directory-claims-mapping.md) deve anexar um parâmetro de consulta contendo o ID da aplicação para obter um `jwks_uri` ponto de atenção para as informações chave de assinatura da sua aplicação. Por exemplo: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` `jwks_uri` contém `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`um de .

Normalmente, utilizaria este documento de metadados para configurar uma biblioteca OpenID Connect ou SDK; a biblioteca usaria os metadados para fazer o seu trabalho. No entanto, se não estiver a utilizar uma biblioteca OpenID Connect pré-construída, pode seguir os passos no restante deste artigo para fazer o início de sessão numa aplicação web utilizando o ponto final da plataforma de identidade da Microsoft.

## <a name="send-the-sign-in-request"></a>Envie o pedido de inscrição

Quando a sua aplicação web precisa de autenticar o `/authorize` utilizador, pode direcionar o utilizador para o ponto final. Este pedido é semelhante à primeira etapa do fluxo de código de [autorização OAuth 2.0,](v2-oauth2-auth-code-flow.md)com estas distinções importantes:

* O pedido deve `openid` incluir `scope` o âmbito do parâmetro.
* O `response_type` parâmetro deve `id_token`incluir .
* O pedido deve `nonce` incluir o parâmetro.

> [!IMPORTANT]
> Para solicitar com sucesso um símbolo de identificação do ponto final/autorização, o registo da aplicação no [portal](https://portal.azure.com) de registo `oauth2AllowIdTokenImplicitFlow` deve ter a concessão implícita de id_tokens ativada no separador Autenticação (que define a bandeira no manifesto de [aplicação](reference-app-manifest.md) para). `true` Se não estiver ativado, `unsupported_response` será devolvido um erro: "O valor previsto para o parâmetro de entrada 'response_type' não é permitido para este cliente. O valor esperado é 'código'"

Por exemplo:

```HTTP
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
> Clique no seguinte link para executar este pedido. Depois de iniciar sessão, o seu `https://localhost/myapp/`navegador será redirecionado para, com um token de identificação na barra de endereços. Note que este `response_mode=fragment` pedido utiliza (apenas para efeitos de demonstração). Recomendamos que `response_mode=form_post`utilize.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Necessário | Pode utilizar `{tenant}` o valor no caminho do pedido de controlo de quem pode iniciar sessão na aplicação. Os valores `common` `organizations`permitidos são, e `consumers`identificadores de inquilinos. Para mais informações, consulte o [protocolo básico](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Necessário | O ID de **Aplicação (cliente)** que o [portal Azure – App registra](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída à sua app. |
| `response_type` | Necessário | Deve `id_token` incluir o acesso ao OpenID Connect. Pode também incluir `response_type` outros valores, como. `code` |
| `redirect_uri` | Recomendado | O URI redirecionado da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a um dos URIs redirecionados registados no portal, exceto que deve ser codificado por URL. Se não estiver presente, o ponto final escolherá um redirect_uri registado aleatoriamente para enviar o utilizador de volta. |
| `scope` | Necessário | Uma lista de âmbitos separados pelo espaço. Para o OpenID Connect, `openid`deve incluir o âmbito, que se traduz na permissão "Iniciar sessão" na UI de consentimento. Pode também incluir outros âmbitos neste pedido de solicitação de consentimento. |
| `nonce` | Necessário | Um valor incluído no pedido, gerado pela app, que será incluído no valor id_token resultante como reclamação. A aplicação pode verificar este valor para mitigar ataques de repetição de tokens. O valor tipicamente é uma cadeia aleatória e única que pode ser usada para identificar a origem do pedido. |
| `response_mode` | Recomendado | Especifica o método que deve ser usado para enviar o código de autorização resultante de volta para a sua aplicação. Pode ser `form_post` ou `fragment`. Para aplicações web, `response_mode=form_post`recomendamos a utilização, para garantir a transferência mais segura de fichas para a sua aplicação. |
| `state` | Recomendado | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que queiras. Um valor único gerado aleatoriamente é normalmente usado para [evitar ataques de falsificação de](https://tools.ietf.org/html/rfc6749#section-10.12)pedidos de local. O Estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que o utilizador estava ligado. |
| `prompt` | Opcional | Indica o tipo de interação do utilizador que é necessária. Os únicos valores válidos `none`neste `consent`momento são, `login`e . A `prompt=login` reclamação obriga o utilizador a introduzir as suas credenciais nesse pedido, o que nega a inscrição individual. A `prompt=none` reivindicação é o oposto. Esta alegação garante que o utilizador não é apresentado com qualquer solicitação interativa. Se o pedido não puder ser concluído silenciosamente através de um único sinal, o ponto final da plataforma de identidade da Microsoft devolve um erro. A `prompt=consent` alegação aciona o diálogo de consentimento da OAuth após a assinatura do utilizador. O diálogo pede ao utilizador que conceda permissões à aplicação. |
| `login_hint` | Opcional | Pode utilizar este parâmetro para pré-preencher o nome de utilizador e o campo de endereços de e-mail da página de início de sessão para o utilizador, se souber o nome de utilizador com antecedência. Muitas vezes, as aplicações utilizam este parâmetro durante a reautenticação, depois `preferred_username` de já terem extraído o nome de utilizador de um início de sessão anterior, utilizando a alegação. |
| `domain_hint` | Opcional | O reino do utilizador num diretório federado.  Isto ignora o processo de descoberta baseado em e-mail que o utilizador passa na página de entrada, para uma experiência de utilizador um pouco mais simplificada. Para os inquilinos que são federados através de um diretório no local como a AD FS, isso resulta frequentemente num insessão sem emenda devido à sessão de login existente. |

Neste ponto, o utilizador é solicitado a introduzir as suas credenciais e a completar a autenticação. O ponto final da plataforma de identidade da Microsoft verifica que `scope` o utilizador consentiu nas permissões indicadas no parâmetro de consulta. Se o utilizador não tiver consentido com nenhuma dessas permissões, o ponto final da plataforma de identidade da Microsoft leva o utilizador a consentir com as permissões necessárias. Você pode ler mais sobre [permissões, consentimento e aplicativos multi-inquilinos.](v2-permissions-and-consent.md)

Após o utilizador autenticar e conceder o consentimento, o ponto final da plataforma de identidade da Microsoft `response_mode` devolve uma resposta à sua aplicação no URI de redirecionamento indicado utilizando o método especificado no parâmetro.

### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta bem `response_mode=form_post` sucedida quando se usa é assim:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `id_token` | O símbolo de identificação que a aplicação solicitou. Pode utilizar `id_token` o parâmetro para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Para obter mais informações sobre fichas [ `id_tokens` ](id-tokens.md)de identificação e seus conteúdos, consulte a referência . |
| `state` | Se `state` um parâmetro estiver incluído no pedido, o mesmo valor deve figurar na resposta. A aplicação deve verificar se os valores do Estado no pedido e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para o URI redirecionamento para que a aplicação possa manuseá-las. Uma resposta de erro é a seguinte:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de código de erro que pode usar para classificar tipos de erros que ocorrem e para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto final de autorização

A tabela seguinte descreve códigos de `error` erro que podem ser devolvidos no parâmetro da resposta ao erro:

| Código de erro | Descrição | Ação do cliente |
| --- | --- | --- |
| `invalid_request` | Erro de protocolo, como um parâmetro em falta e necessário. |Corrija e reenvie o pedido. Este é um erro de desenvolvimento que normalmente é apanhado durante os testes iniciais. |
| `unauthorized_client` | O pedido do cliente não pode solicitar um código de autorização. |Isto ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino Azure AD do utilizador. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| `access_denied` | O dono do recurso negou o consentimento. |A aplicação do cliente pode notificar o utilizador de que não pode prosseguir a menos que o utilizador consinta. |
| `unsupported_response_type` |O servidor de autorização não suporta o tipo de resposta no pedido. |Corrija e reenvie o pedido. Este é um erro de desenvolvimento que normalmente é apanhado durante os testes iniciais. |
| `server_error` | O servidor encontrou um erro inesperado. |Tente o pedido. Estes erros podem resultar de condições temporárias. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a um erro temporário. |
| `temporarily_unavailable` | O servidor está temporariamente demasiado ocupado para lidar com o pedido. |Tente o pedido. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a uma condição temporária. |
| `invalid_resource` | O recurso-alvo é inválido porque ou não existe, o Azure AD não consegue encontrá-lo, ou não está corretamente configurado. |Isto indica que o recurso, se existir, não foi configurado no inquilino. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |

## <a name="validate-the-id-token"></a>Validar o símbolo de identificação

Receber apenas uma id_token não é suficiente para autenticar o utilizador; deve validar a assinatura do id_token e verificar as reclamações no token de acordo com os requisitos da sua aplicação. O ponto final da plataforma de identidade da Microsoft utiliza [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chaves públicas para assinar fichas e verificar se são válidas.

Pode optar por `id_token` validar o código do cliente, `id_token` mas uma prática comum é enviar o para um servidor de backend e fazer a validação lá. Uma vez validado a assinatura do id_token, existem algumas reclamações que terá de verificar. Consulte [ `id_token` ](id-tokens.md) a referência para mais informações, incluindo [Validação de Fichas](id-tokens.md#validating-an-id_token) e [Informações Importantes sobre A Assinatura](active-directory-signing-key-rollover.md)de Capotamento de Chaves . Recomendamos a utilização de uma biblioteca para analisar e validar fichas - há pelo menos uma disponível para a maioria das línguas e plataformas.

Também pode querer validar reclamações adicionais dependendo do seu cenário. Algumas validações comuns incluem:

* Garantindo que o utilizador/organização se inscreveu para a aplicação.
* Garantir que o utilizador tem autorização/privilégios adequados
* A garantia de uma certa força de autenticação ocorreu, como a autenticação de vários fatores.

Uma vez validado o id_token, pode iniciar uma sessão com o utilizador e utilizar as reclamações no id_token para obter informações sobre o utilizador na sua aplicação. Estas informações podem ser utilizadas para exibição, registos, personalização, etc.

## <a name="send-a-sign-out-request"></a>Enviar um pedido de inscrição

Quando pretende assinar o utilizador a partir da sua aplicação, não basta limpar os cookies da sua aplicação ou terminar a sessão do utilizador. Também deve redirecionar o utilizador para o ponto final da plataforma de identidade da Microsoft para assinar. Se não o fizer, o utilizador reautentica a sua aplicação sem voltar a introduzir as suas credenciais, pois terá uma sessão de entrada única válida com o ponto final da plataforma de identidade da Microsoft.

Pode redirecionar o utilizador `end_session_endpoint` para a lista lista da lista no documento de metadados OpenID Connect:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parâmetro | Condição | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Recomendado | O URL para o que o utilizador é redirecionado depois de assinar com sucesso. Se o parâmetro não estiver incluído, o utilizador é mostrado uma mensagem genérica que é gerada pelo ponto final da plataforma de identidade da Microsoft. Este URL deve corresponder a um dos URIs redirecionados registados para a sua aplicação no portal de registo da aplicação. |

## <a name="single-sign-out"></a>Fim de sessão único

Quando redireciona `end_session_endpoint`o utilizador para o ponto final da plataforma de identidade da Microsoft, a plataforma de identidade da Microsoft iliba a sessão do utilizador a partir do navegador. No entanto, o utilizador poderá ainda ser inscrito noutras aplicações que utilizem as contas da Microsoft para autenticação. Para permitir que essas aplicações assinem o utilizador em simultâneo, o `LogoutUrl` ponto final da plataforma de identidade da Microsoft envia um pedido HTTP GET ao registo de todas as aplicações em que o utilizador está atualmente inscrito. As aplicações devem responder a este pedido, limpando `200` qualquer sessão que identifique o utilizador e devolvendo uma resposta. Se desejar apoiar uma única inscrição na sua aplicação, deve implementar tal `LogoutUrl` no código da sua aplicação. Pode definir `LogoutUrl` o a partir do portal de registo da aplicação.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagrama de protocolo: Aquisição de fichas de acesso

Muitas aplicações web precisam não só de inscrever o utilizador, mas também de aceder a um serviço web em nome do utilizador, utilizando a OAuth. Este cenário combina o OpenID Connect para a autenticação do utilizador, ao mesmo tempo que obtém um código de autorização que pode utilizar para obter fichas de acesso se estiver a utilizar o fluxo de código de autorização OAuth.

O fluxo completo de entrada e aquisição de token OpenID Connect é semelhante ao diagrama seguinte. Descrevemos cada passo em detalhe nas próximas secções do artigo.

![Protocolo OpenID Connect: Aquisição de Token](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Obtenha fichas de acesso
Para adquirir fichas de acesso, modifique o pedido de inscrição:

```HTTP
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
> Clique no seguinte link para executar este pedido. Depois de iniciar sessão, o `https://localhost/myapp/`seu navegador é redirecionado para, com um token de identificação e um código na barra de endereços. Note que este `response_mode=fragment` pedido utiliza apenas para fins de demonstração. Recomendamos que `response_mode=form_post`utilize.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Ao incluir os âmbitos de `response_type=id_token code`permissão no pedido e utilizando, o ponto final da plataforma `scope` de identidade da Microsoft garante que o utilizador consentiu nas permissões indicadas no parâmetro de consulta. Devolve um código de autorização à sua aplicação para trocar por um token de acesso.

### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta bem `response_mode=form_post` sucedida de usar parece assim:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `id_token` | O símbolo de identificação que a aplicação solicitou. Pode utilizar o símbolo de identificação para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Encontrará mais detalhes sobre fichas de identificação e seus conteúdos na [ `id_tokens` referência](id-tokens.md). |
| `code` | O código de autorização que a aplicação solicitou. A aplicação pode usar o código de autorização para solicitar um sinal de acesso para o recurso alvo. Um código de autorização é de curta duração. Normalmente, um código de autorização expira em cerca de 10 minutos. |
| `state` | Se um parâmetro estatal estiver incluído no pedido, o mesmo valor deve figurar na resposta. A aplicação deve verificar se os valores do Estado no pedido e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para o URI redirecionado para que a aplicação possa manuseá-las adequadamente. Uma resposta de erro é a seguinte:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de código de erro que pode usar para classificar tipos de erros que ocorrem e para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |

Para uma descrição de possíveis códigos de erro e respostas recomendadas ao cliente, consulte [códigos](#error-codes-for-authorization-endpoint-errors)de erro para erros de ponto final de autorização .

Quando tiver um código de autorização e um token de identificação, pode iniciar sessão e obter fichas de acesso em seu nome. Para iniciar sessão com o utilizador, tem de validar o token de identificação [exatamente como descrito](id-tokens.md#validating-an-id_token). Para obter fichas de acesso, siga os passos descritos na documentação de fluxo de [código OAuth](v2-oauth2-auth-code-flow.md#request-an-access-token).
