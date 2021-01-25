---
title: Plataforma de identidade microsoft e protocolo OpenID Connect | Rio Azure
titleSuffix: Microsoft identity platform
description: Construa aplicações web utilizando a implementação da plataforma de identidade microsoft do protocolo de autenticação OpenID Connect.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: de1fcdc259de3f72e35feb411bcc836354352eb4
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752599"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Plataforma de identidade da Microsoft e protocolo OpenID Connect

OpenID Connect (OIDC) é um protocolo de autenticação construído no OAuth 2.0 que pode utilizar para assinar de forma segura num utilizador para uma aplicação. Quando utilizar a implementação da plataforma de identidade da Microsoft do OpenID Connect, pode adicionar acesso de s-in e API às suas aplicações. Este artigo mostra como fazê-lo independentemente da linguagem e descreve como enviar e receber mensagens HTTP sem utilizar quaisquer [bibliotecas de código aberto da Microsoft.](reference-v2-libraries.md)

[O OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) alarga o protocolo de *autorização* OAuth 2.0 para utilização como protocolo de *autenticação,* para que possa fazer um único início de sposição utilizando o OAuth. O OpenID Connect introduz o conceito de um símbolo de *ID,* que é um símbolo de segurança que permite ao cliente verificar a identidade do utilizador. O token de ID também obtém informações básicas de perfil sobre o utilizador. Introduz também o [ponto final do UserInfo](userinfo.md), uma API que devolve informações sobre o utilizador. 


## <a name="protocol-diagram-sign-in"></a>Diagrama de protocolo: Início de sação

O fluxo de entrada mais básico tem os passos mostrados no diagrama seguinte. Cada passo é descrito em detalhe neste artigo.

![Protocolo openID connect: Iniciar sção](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Pegue o documento de metadados OpenID Connect

O OpenID Connect descreve um documento de metadados [(RFC)](https://openid.net/specs/openid-connect-discovery-1_0.html) que contém a maioria das informações necessárias para que uma aplicação faça o sôm. Isto inclui informações como os URLs a utilizar e a localização das chaves de assinatura pública do serviço. Pode encontrar este documento anexando o caminho do documento de descoberta para a URL da autoridade:

Caminho do documento de descoberta: `/.well-known/openid-configuration`

Autoridade: `https://login.microsoftonline.com/{tenant}/v2.0`

A `{tenant}` lata leva um de quatro valores:

| Valor | Descrição |
| --- | --- |
| `common` |Os utilizadores com uma conta pessoal da Microsoft e uma conta de trabalho ou escola a partir do Azure AD podem iniciar scontabilidade na aplicação. |
| `organizations` |Apenas os utilizadores com contas de trabalho ou escola da Azure AD podem iniciar scontabilidade na aplicação. |
| `consumers` |Apenas os utilizadores com uma conta pessoal da Microsoft podem iniciar scontabilidade na aplicação. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Apenas os utilizadores de um inquilino específico da Azure AD (sejam membros do diretório com uma conta de trabalho ou de escola, ou se são hóspedes no diretório com uma conta pessoal da Microsoft) podem inscrever-se na aplicação. Ou o nome de domínio amigável do inquilino AZure AD ou o identificador GUID do inquilino podem ser usados. Você também pode usar o inquilino de `9188040d-6c67-4c5b-b112-36a304b66dad` consumo, no lugar do `consumers` inquilino.  |

A autoridade difere entre as nuvens nacionais - por exemplo, `https://login.microsoftonline.de` para o exemplo da Azure AD Alemanha. Se não utilizar a nuvem pública, por favor reveja os [pontos finais da nuvem nacional](authentication-national-cloud.md#azure-ad-authentication-endpoints) para encontrar o adequado para si. Certifique-se de que o arrendatário e `/v2.0/` está presente no seu pedido para que possa utilizar a versão v2.0 do ponto final.

> [!TIP]
> Experimente! Clique [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) para ver a `common` configuração.

### <a name="sample-request"></a>Pedido de amostra

Para chamar o ponto final userinfo para a autoridade comum na nuvem pública, use o seguinte:

```http
GET /common/v2.0/.well-known/openid-configuration
Host: login.microsoftonline.com
```

### <a name="sample-response"></a>Resposta de amostra

Os metadados são um documento simples de Notação de Objetos JavaScript (JSON). Veja o seguinte corte para um exemplo. O conteúdo está totalmente descrito na [especificação OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```json
{
  "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize",
  "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys",
  "userinfo_endpoint": "https://graph.microsoft.com/oidc/userinfo",
  "subject_types_supported": [
      "pairwise"
  ],
  ...

}
```

Se a sua aplicação tiver teclas de assinatura personalizadas como resultado da utilização da funcionalidade [de mapeamento de sinistros,](active-directory-claims-mapping.md) tem de anexar um `appid` parâmetro de consulta que contenha o ID da aplicação, de forma a obter uma `jwks_uri` indicação para as informações-chave de assinatura da sua aplicação. Por exemplo: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contém um `jwks_uri` de `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` .

Normalmente, utilizaria este documento de metadados para configurar uma biblioteca OpenID Connect ou SDK; a biblioteca usaria os metadados para fazer o seu trabalho. No entanto, se não estiver a utilizar uma biblioteca OpenID Connect pré-construída, pode seguir os passos no restante deste artigo para fazer o sismo numa aplicação web utilizando a plataforma de identidade da Microsoft.

## <a name="send-the-sign-in-request"></a>Envie o pedido de inscrição

Quando a sua aplicação web precisa de autenticar o utilizador, pode direcionar o utilizador para o `/authorize` ponto final. Este pedido é semelhante à primeira etapa do fluxo do código de [autorização OAuth 2.0,](v2-oauth2-auth-code-flow.md)com estas importantes distinções:

* O pedido deve incluir o `openid` âmbito do `scope` parâmetro.
* O `response_type` parâmetro deve incluir `id_token` .
* O pedido deve incluir o `nonce` parâmetro.

> [!IMPORTANT]
> Para solicitar com sucesso um token de ID do ponto final /autorização, o registo da aplicação no portal de [registo](https://portal.azure.com) deve ter a concessão implícita de id_tokens habilitado no separador autenticação (que define a `oauth2AllowIdTokenImplicitFlow` bandeira no manifesto de [candidatura](reference-app-manifest.md) `true` para). Se não estiver ativado, será devolvido um `unsupported_response` erro: "O valor fornecido para o parâmetro de entrada 'response_type' não é permitido para este cliente. Valor esperado é 'código'"

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

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | Pode utilizar o `{tenant}` valor no caminho do pedido para controlar quem pode iniciar sê-lo na aplicação. Os valores permitidos `common` `organizations` são, `consumers` e os identificadores de inquilinos. Para mais informações, consulte [o protocolo básico.](active-directory-v2-protocols.md#endpoints) |
| `client_id` | Necessário | O **ID da Aplicação (cliente)** que o [portal Azure – Experiência de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuído à sua app. |
| `response_type` | Necessário | Deve incluir `id_token` para o iniciar sê-in OpenID Connect. Também pode incluir outros `response_type` valores, tais `code` como. |
| `redirect_uri` | Recomendado | O URI redirecionado da sua app, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a um dos URIs de redirecionamento que registou no portal, exceto que deve ser codificado URL. Se não estiver presente, o ponto final escolherá um redirect_uri registado aleatoriamente para enviar o utilizador de volta. |
| `scope` | Necessário | Uma lista de âmbitos separados pelo espaço. Para o OpenID Connect, deve incluir o âmbito `openid` , que se traduz na permissão "Iniciar a sua inscrição" na UI de consentimento. Pode também incluir outros âmbitos neste pedido de consentimento. |
| `nonce` | Necessário | Um valor incluído no pedido, gerado pela app, que será incluído no valor id_token resultante como reclamação. A aplicação pode verificar este valor para mitigar os ataques de reprodução de token. O valor é tipicamente uma corda aleatória e única que pode ser usada para identificar a origem do pedido. |
| `response_mode` | Recomendado | Especifica o método que deve ser usado para enviar o código de autorização resultante de volta para a sua aplicação. Pode ser `form_post` ou `fragment`. Para aplicações web, recomendamos a `response_mode=form_post` utilização, para garantir a transferência mais segura de fichas para a sua aplicação. |
| `state` | Recomendado | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de conteúdos que quiser. Um valor único gerado aleatoriamente é normalmente usado para [evitar ataques de falsificação de pedidos de trans-locais](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que o utilizador estava ligado. |
| `prompt` | Opcional | Indica o tipo de interação do utilizador que é necessária. Os únicos valores válidos neste momento são `login` `none` , e `consent` . A `prompt=login` alegação obriga o utilizador a introduzir as suas credenciais nesse pedido, o que nega uma única inscrição. A `prompt=none` reivindicação é o oposto. Esta alegação garante que o utilizador não é apresentado com qualquer solicitação interativa. Se o pedido não puder ser concluído silenciosamente através de um único sinal, a plataforma de identidade da Microsoft retorna um erro. A `prompt=consent` alegação desencadeia o diálogo de consentimento OAuth após a indicação do utilizador. O diálogo pede ao utilizador que conceda permissões à aplicação. |
| `login_hint` | Opcional | Pode utilizar este parâmetro para pré-preenchimento do nome de utilizador e do campo de endereço de e-mail da página de entrada para o utilizador, caso conheça o nome de utilizador com antecedência. Muitas vezes, as aplicações usam este parâmetro durante a reauthentication, depois de já terem extraído o nome de utilizador de um pré-início de súd in, utilizando a `preferred_username` alegação. |
| `domain_hint` | Opcional | O reino do utilizador num diretório federado.  Isto ignora o processo de descoberta baseado em e-mail que o utilizador passa na página de entrada, para uma experiência de utilizador um pouco mais simplificada. Para os inquilinos que são federados através de um diretório no local como o AD FS, isso resulta frequentemente num login sem emenda por causa da sessão de login existente. |

Neste momento, o utilizador é solicitado a introduzir as suas credenciais e completar a autenticação. A plataforma de identidade da Microsoft verifica que o utilizador consentiu nas permissões indicadas no `scope` parâmetro de consulta. Se o utilizador não tiver consentido com nenhuma dessas permissões, a plataforma de identidade da Microsoft solicita ao utilizador que consinta nas permissões necessárias. Você pode ler mais sobre [permissões, consentimento e aplicativos multi-inquilinos.](v2-permissions-and-consent.md)

Após a autenticação e concessão de consentimento do utilizador, a plataforma de identidade da Microsoft devolve uma resposta à sua aplicação no URI de redirecionamento indicado, utilizando o método especificado no `response_mode` parâmetro.

### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta bem sucedida quando se usa `response_mode=form_post` assim:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `id_token` | O sinal de identificação que a aplicação solicitou. Pode utilizar o `id_token` parâmetro para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Para obter mais informações sobre fichas de identificação e seus conteúdos, consulte a [ `id_tokens` referência](id-tokens.md). |
| `state` | Se um `state` parâmetro for incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se os valores do Estado no pedido e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para o URI de redirecionamento para que a aplicação possa manuseá-las. Uma resposta de erro é assim:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de código de erro que pode utilizar para classificar tipos de erros que ocorrem e para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto final de autorização

A tabela a seguir descreve códigos de erro que podem ser devolvidos no `error` parâmetro da resposta de erro:

| Código de erro | Descrição | Ação do cliente |
| --- | --- | --- |
| `invalid_request` | Erro de protocolo, como um parâmetro em falta, exigido. |Corrija e reenvia o pedido. Este é um erro de desenvolvimento que normalmente é apanhado durante os testes iniciais. |
| `unauthorized_client` | O pedido do cliente não pode solicitar um código de autorização. |Isto ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino AZure AD do utilizador. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| `access_denied` | O proprietário do recurso negou o consentimento. |A aplicação do cliente pode notificar o utilizador de que não pode prosseguir a menos que o utilizador consinta. |
| `unsupported_response_type` |O servidor de autorização não suporta o tipo de resposta no pedido. |Corrija e reenvia o pedido. Este é um erro de desenvolvimento que normalmente é apanhado durante os testes iniciais. |
| `server_error` | O servidor encontrou um erro inesperado. |Recandidutar o pedido. Estes erros podem resultar de condições temporárias. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a um erro temporário. |
| `temporarily_unavailable` | O servidor está temporariamente demasiado ocupado para lidar com o pedido. |Recandidutar o pedido. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a uma condição temporária. |
| `invalid_resource` | O recurso alvo é inválido porque ou não existe, a Azure AD não pode encontrá-lo, ou não está corretamente configurado. |Isto indica que o recurso, se existe, não foi configurado no inquilino. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |

## <a name="validate-the-id-token"></a>Validar o token de ID

Receber uma id_token nem sempre é suficiente para autenticar o utilizador; poderá também ser necessário validar a assinatura do id_token e verificar as reclamações no token de acordo com os requisitos da sua aplicação. Como todas as plataformas OIDC, a plataforma de identidade da Microsoft utiliza [os Tokens Web JSON (JWTs)](https://tools.ietf.org/html/rfc7519) e a criptografia de chaves públicas para assinar fichas de identificação e verificar se são válidos.

Nem todas as aplicações beneficiam de verificar o token de ID - aplicações nativas e aplicações de página única, por exemplo, raramente beneficiam de validar o token de ID.  Alguém com acesso físico ao dispositivo (ou browser) pode contornar a validação de muitas formas - desde a edição do tráfego web até ao dispositivo para fornecer fichas e chaves falsas para simplesmente depurar a aplicação para saltar a lógica de validação.  Por outro lado, as aplicações web e as APIs que utilizam um token de ID para autorização devem validar cuidadosamente o símbolo de ID, uma vez que estão a obter acesso aos dados.

Uma vez validada a assinatura do id_token, há algumas reclamações que terá de verificar. Consulte a [ `id_token` referência](id-tokens.md) para mais informações, incluindo [a validação de tokens](id-tokens.md#validating-an-id_token) e [informações importantes sobre a assinatura da chave rollover](active-directory-signing-key-rollover.md). Recomendamos a utilização de uma biblioteca para analisar e validar tokens - há pelo menos uma disponível para a maioria dos idiomas e plataformas.

Também pode pretender validar reclamações adicionais dependendo do seu cenário. Algumas validações comuns incluem:

* Garantindo que o utilizador/organização se inscreveu para a aplicação.
* Garantindo que o utilizador tem a devida autorização/privilégios
* Assegurar uma certa força de autenticação, como a [autenticação de vários fatores.](../authentication/concept-mfa-howitworks.md)

Uma vez validado o id_token, pode iniciar uma sessão com o utilizador e utilizar as reclamações no id_token para obter informações sobre o utilizador na sua aplicação. Estas informações podem ser utilizadas para exibição, registos, personalização, etc.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagrama de protocolo: Aquisição de ficha de acesso

Muitas aplicações web precisam não só de assinar o utilizador, mas também de aceder a um serviço web em nome do utilizador utilizando o OAuth. Este cenário combina o OpenID Connect para autenticação do utilizador, ao mesmo tempo que obtém um código de autorização que pode utilizar para obter tokens de acesso se estiver a utilizar o fluxo de código de autorização OAuth.

O fluxo completo de entrada e aquisição do OpenID Connect fica semelhante ao diagrama seguinte. Descrevemos cada passo em detalhe nas próximas secções do artigo.

![Protocolo OpenID Connect: Aquisição token](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-an-access-token-to-call-userinfo"></a>Obtenha um token de acesso para ligar para o UserInfo

Para adquirir um token para o ponto final do UtilizadorInfo OIDC, modifique o pedido de inscrição:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20token                       // this will return both an id_token and an access token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid+profile+email                           // `openid` is required.  `profile` and `email` provide additional information in the UserInfo endpoint the same way they do in an ID token. 
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Também pode utilizar o fluxo de [código de autorização](v2-oauth2-auth-code-flow.md), o fluxo de código do [dispositivo,](v2-oauth2-device-code.md)ou um [token](v2-oauth2-auth-code-flow.md#refresh-the-access-token) de atualização no lugar `response_type=token` de obter um token para a sua aplicação.

> [!TIP]
> Clique no seguinte link para executar este pedido. Depois de iniciar seduca, o seu navegador é redirecionado para `https://localhost/myapp/` , com um token de ID e um token na barra de endereços. Note que este pedido utiliza `response_mode=fragment` apenas para fins de demonstração - para um webapp recomendamos a utilização `form_post` para segurança adicional sempre que possível. 
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid+profile+email&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

### <a name="successful-token-response"></a>Resposta simbólica bem sucedida

Uma resposta bem sucedida de usar `response_mode=form_post` se parece com isto:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
 access_token=eyJ0eXAiOiJKV1QiLCJub25jZSI6I....
 &token_type=Bearer
 &expires_in=3598
 &scope=email+openid+profile
 &id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI....
 &state=12345
```

Os parâmetros de resposta significam a mesma coisa, independentemente do fluxo usado para os adquirir.

| Parâmetro | Descrição |
| --- | --- |
| `access_token` | O token que será usado para chamar o ponto final do UserInfo.|
| `token_type` | Sempre "Portador" |
| `expires_in`| Quanto tempo até o sinal de acesso expirar, em segundos. |
| `scope` | As permissões concedidas no token de acesso.  Note que, uma vez que o ponto final do UserInfo está hospedado no MS Graph, pode haver âmbitos gráficos adicionais listados aqui (por exemplo, user.read) se foram previamente concedidos à app.  Isto porque um símbolo de um determinado recurso inclui sempre todas as permissões concedidas ao cliente.  |
| `id_token` | O sinal de identificação que a aplicação solicitou. Pode utilizar o token de identificação para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Você encontrará mais detalhes sobre fichas de identificação e seus conteúdos na [ `id_tokens` referência](id-tokens.md). |
| `state` | Se um parâmetro de estado for incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se os valores do Estado no pedido e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para o URI de redirecionamento para que a aplicação possa manuseá-las adequadamente. Uma resposta de erro é assim:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de código de erro que pode utilizar para classificar tipos de erros que ocorrem e para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |

Para obter uma descrição de possíveis códigos de erro e respostas recomendadas do cliente, consulte [códigos de erro para obter erros de ponto final de autorização](#error-codes-for-authorization-endpoint-errors).

Quando tiver um código de autorização e um token de identificação, pode iniciar sedutada e obter fichas de acesso em seu nome. Para iniciar sê-lo, tem de validar o token de identificação [exatamente como descrito](id-tokens.md#validating-an-id_token). Para obter fichas de acesso, siga os passos descritos na [documentação do fluxo de código OAuth](v2-oauth2-auth-code-flow.md#request-an-access-token).

### <a name="calling-the-userinfo-endpoint"></a>Chamando o ponto final do UserInfo

Reveja a [documentação Do UtilizadorInfo](userinfo.md#calling-the-api) para ver como a chamada o ponto final do UserInfo com este token.

## <a name="send-a-sign-out-request"></a>Enviar um pedido de sinalização

Quando pretender assinar o utilizador da sua aplicação, não basta limpar os cookies da sua aplicação ou terminar a sessão do utilizador. Também deve redirecionar o utilizador para a plataforma de identidade da Microsoft para assinar. Se não o fizer, o utilizador reaudiu para a sua aplicação sem introduzir novamente as suas credenciais, pois terá uma sessão de sessão de sessão de inscrição única válida com a plataforma de identidade da Microsoft.

Pode redirecionar o utilizador para a `end_session_endpoint` lista no documento de metadados OpenID Connect:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parâmetro | Condição | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Recomendado | O URL para o qual o utilizador é redirecionado após a assinatura com sucesso. Se o parâmetro não estiver incluído, o utilizador é mostrado uma mensagem genérica que é gerada pela plataforma de identidade da Microsoft. Este URL deve corresponder a um dos URIs de redirecionamento registados para a sua aplicação no portal de registo de aplicações. |

## <a name="single-sign-out"></a>Fim de sessão único

Quando redireciona o utilizador para o `end_session_endpoint` , a plataforma de identidade da Microsoft limpa a sessão do utilizador a partir do navegador. No entanto, o utilizador pode ainda ser inscrito noutras aplicações que utilizem as contas da Microsoft para autenticação. Para permitir que essas aplicações assinem o utilizador simultaneamente, a plataforma de identidade da Microsoft envia um pedido HTTP GET ao registo `LogoutUrl` de todas as aplicações a que o utilizador se encontra atualmente inscrito. As aplicações devem responder a este pedido, limpando qualquer sessão que identifique o utilizador e devolvendo uma `200` resposta. Se pretender apoiar uma única sinseção na sua aplicação, tem de implementar tal `LogoutUrl` no código da sua aplicação. Pode definir o `LogoutUrl` portal de registo de aplicações.

## <a name="next-steps"></a>Próximos passos

* Reveja a [documentação Do UtilizadorInfo](userinfo.md)
* Saiba como [personalizar os valores num token](active-directory-claims-mapping.md) com dados dos seus sistemas no local. 
* Saiba como [incluir reclamações padrão adicionais em fichas.](active-directory-optional-claims.md)  
