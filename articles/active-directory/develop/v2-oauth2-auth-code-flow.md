---
title: Fluxo de código de autorização OAuth-plataforma de identidade da Microsoft | Azure
description: Crie aplicativos Web usando a implementação da plataforma de identidade da Microsoft do protocolo de autenticação OAuth 2,0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 031890b389e78c4ca01e6d6ae52430db865ede2f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931066"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Plataforma de identidade da Microsoft e fluxo de código de autorização do OAuth 2,0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

A concessão de código de autorização OAuth 2,0 pode ser usada em aplicativos que estão instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Usando a implementação da plataforma Microsoft Identity do OAuth 2,0, você pode adicionar entrada e acesso à API para seus aplicativos móveis e de área de trabalho. Este guia é independente da linguagem e descreve como enviar e receber mensagens HTTP sem utilizar nenhuma das [bibliotecas de autenticação de código aberto Azure.](reference-v2-libraries.md)

Este artigo descreve como programar diretamente em relação ao protocolo em seu aplicativo.  Quando possível, recomendamos que você use as MSAL (bibliotecas de autenticação da Microsoft) com suporte em vez de [adquirir tokens e chamar APIs da Web protegidas](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Veja também os [aplicativos de exemplo que usam MSAL](sample-v2-code.md).

> [!NOTE]
> Nem todos os cenários de Azure Active Directory & recursos têm suporte do ponto de extremidade da plataforma Microsoft Identity. Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

O fluxo de código de autorização do OAuth 2,0 é descrito na [seção 4,1 da especificação do OAuth 2,0](https://tools.ietf.org/html/rfc6749). É usado para realizar autenticação e autorização na maioria dos tipos de aplicações, incluindo [aplicações web](v2-app-types.md#web-apps) e [aplicações instaladas de forma nativa.](v2-app-types.md#mobile-and-native-apps) O fluxo permite que as aplicações adquiram de forma segura access_tokens que podem ser usadas para aceder a recursos protegidos pelo ponto final da plataforma de identidade da Microsoft.

## <a name="protocol-diagram"></a>Diagrama de protocolo

Em um alto nível, todo o fluxo de autenticação para um aplicativo nativo/móvel parece um pouco assim:

![Fluxo de código de autenticação OAuth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Solicitar um código de autorização

O fluxo do código de autorização começa com o cliente direcionando o usuário para o ponto de extremidade `/authorize`. Neste pedido, o cliente solicita a `openid`, `offline_access`, e `https://graph.microsoft.com/mail.read `permissões do utilizador.  Algumas permissões são restritas à administração, por exemplo, escrevendo dados para o diretório de uma organização usando `Directory.ReadWrite.All`. Se a sua aplicação solicitar o acesso a uma dessas permissões de um utilizador organizacional, o utilizador recebe uma mensagem de erro que diz não estar autorizada a consentir com as permissões da sua aplicação. Para solicitar o acesso a âmbitos restritos de administração, deve solicitá-los diretamente a um administrador da empresa.  Para mais informações, leia [as permissões restritas](v2-permissions-and-consent.md#admin-restricted-permissions)à Administração .

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [!TIP]
> Clique no link abaixo para executar esta solicitação! Após a essão, o seu navegador deve ser redirecionado para `https://localhost/myapp/` com um `code` na barra de endereços.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parâmetro    | Obrigatório/opcional | Descrição |
|--------------|-------------|--------------|
| `tenant`    | Necessário    | O valor de `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | Necessário    | A **ID do aplicativo (cliente)** que a [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo.  |
| `response_type` | Necessário    | Deve incluir `code` para o fluxo do código de autorização.       |
| `redirect_uri`  | Necessário | O redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das redirect_uris que registou no portal, exceto que deve ser codificada. Para aplicações nativas e móveis, deve utilizar o valor padrão de `https://login.microsoftonline.com/common/oauth2/nativeclient`.   |
| `scope`  | Necessário    | Uma lista separada por espaços de [escopos](v2-permissions-and-consent.md) aos quais você deseja que o usuário concorde.  Para a `/authorize` perna do pedido, esta pode cobrir vários recursos, permitindo que a sua app obtenha o consentimento para várias APIs web que pretende ligar. |
| `response_mode`   | recomendado | Especifica o método que deve ser usado para enviar o token resultante de volta ao seu aplicativo. Pode ser um dos seguintes procedimentos:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` fornece o código como parâmetro de corda de consulta no seu URI redirecionamento. Se estiver a solicitar um símbolo de identificação utilizando o fluxo implícito, não pode utilizar `query` conforme especificado na [especificação OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Se estiver a pedir apenas o código, pode usar `query`, `fragment`ou `form_post`. `form_post` executa um POST contendo o código para o seu redirecionamento URI. Para mais informações, consulte o [protocolo OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | recomendado | Um valor incluído na solicitação que também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo que você desejar. Um valor exclusivo gerado aleatoriamente geralmente é usado para [impedir ataques de solicitação entre sites forjado](https://tools.ietf.org/html/rfc6749#section-10.12). O valor também pode codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |
| `prompt`  | opcional    | Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos neste momento são `login`, `none`e `consent`.<br/><br/>- `prompt=login` obrigará o utilizador a introduzir as suas credenciais nesse pedido, negando um único sinal.<br/>- `prompt=none` é o oposto - irá garantir que o utilizador não seja apresentado com qualquer tipo de solicitação interativa. Se o pedido não puder ser concluído silenciosamente através de um único sinal, o ponto final da plataforma de identidade da Microsoft devolverá um erro `interaction_required`.<br/>- `prompt=consent` irá desencadear o diálogo de consentimento da OAuth após o utilizador iniciar a sua sessão, pedindo ao utilizador que conceda permissões à aplicação. |
| `login_hint`  | opcional    | Pode ser usado para preencher previamente o campo nome de usuário/endereço de email da página de entrada do usuário, se você souber seu nome de usuário antes do tempo. Muitas vezes as aplicações usam este parâmetro durante a reautenticação, tendo já extraído o nome de utilizador de um início de sessão anterior utilizando a `preferred_username` reivindicação.   |
| `domain_hint`  | opcional    | Pode ser um dos `consumers` ou `organizations`.<br/><br/>Se for incluído, ele ignorará o processo de descoberta baseado em email que o usuário passa na página de entrada, levando a uma experiência de usuário um pouco mais simplificada. Muitas vezes as aplicações usam este parâmetro durante a reautenticação, extraindo o `tid` de um início de sessão anterior. Se o valor da reclamação `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, deve utilizar `domain_hint=consumers`. Caso contrário, use `domain_hint=organizations`.  |
| `code_challenge_method` | opcional    | O método utilizado para codificar o `code_verifier` para o parâmetro `code_challenge`. Pode ser um dos seguintes valores:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Se for excluído, assume-se `code_challenge` texto simples se `code_challenge` estiver incluído. A plataforma de identidade da Microsoft suporta tanto `plain` como `S256`. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | opcional | Usado para proteger as concessões de código de autorização por meio da chave de prova de troca de código (PKCE) de um cliente nativo. Necessário se `code_challenge_method` estiver incluído. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

Neste ponto, o usuário será solicitado a inserir suas credenciais e concluir a autenticação. O ponto de extremidade da plataforma Microsoft Identity também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope`. Se o usuário não tiver consentido nenhuma dessas permissões, ele solicitará que o usuário consenti as permissões necessárias. Os detalhes de [permissões, consentimento e aplicações multi-inquilinos são fornecidos aqui.](v2-permissions-and-consent.md)

Assim que o utilizador autenticar e conceder o consentimento, o ponto final da plataforma de identidade da Microsoft devolverá uma resposta à sua aplicação no `redirect_uri`indicado, utilizando o método especificado no parâmetro `response_mode`.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem sucedida usando `response_mode=query` parece:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parâmetro | Descrição  |
|-----------|--------------|
| `code` | O authorization_code que a app solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Authorization_codes são de curta duração, normalmente expiram após cerca de 10 minutos. |
| `state` | Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação possa manuseá-las adequadamente:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição  |
|----------|------------------|
| `error`  | Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de autorização

A tabela seguinte descreve os vários códigos de erro que podem ser devolvidos no parâmetro `error` da resposta ao erro.

| Código de Erro  | Descrição    | Ação do cliente   |
|-------------|----------------|-----------------|
| `invalid_request` | Erro de protocolo, como um parâmetro necessário ausente. | Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente detectado durante o teste inicial. |
| `unauthorized_client` | O aplicativo cliente não tem permissão para solicitar um código de autorização. | Esse erro geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |
| `access_denied`  | Consentimento negado pelo proprietário do recurso  | O aplicativo cliente pode notificar o usuário de que ele não pode continuar, a menos que o usuário consentisse. |
| `unsupported_response_type` | O servidor de autorização não oferece suporte ao tipo de resposta na solicitação. | Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente detectado durante o teste inicial.  |
| `server_error`  | O servidor encontrou um erro inesperado.| Repita a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada para um erro temporário. |
| `temporarily_unavailable`   | O servidor está temporariamente muito ocupado para lidar com a solicitação. | Repita a solicitação. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a uma condição temporária. |
| `invalid_resource`  | O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou não está configurado corretamente. | Esse erro indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |
| `login_required` | Muitos usuários ou nenhum usuário encontrado | O cliente solicitou a autenticação silenciosa (`prompt=none`), mas um único utilizador não foi encontrado. Isso pode significar que há vários usuários ativos na sessão ou nenhum usuário. Isto tem em conta o inquilino escolhido (por exemplo, se houver duas contas Azure AD ativas e uma conta Microsoft, e `consumers` é escolhida, a autenticação silenciosa funcionará). |
| `interaction_required` | A solicitação requer interação do usuário. | É necessária uma etapa de autenticação adicional ou um consentimento. Tente novamente o pedido sem `prompt=none`. |

## <a name="request-an-access-token"></a>Solicitar um token de acesso

Agora que adquiriu uma authorization_code e foi-lhe concedida permissão pelo utilizador, pode resgatar a `code` por uma `access_token` ao recurso pretendido. Faça-o enviando um pedido de `POST` para o `/token` ponto final:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Tente executar esta solicitação no postmaster! (Não se esqueça de substituir o `code`) [![Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parâmetro  | Obrigatório/opcional | Descrição     |
|------------|-------------------|----------------|
| `tenant`   | Necessário   | O valor de `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | Necessário  | O ID de Aplicação (cliente) que o [portal Azure – Página](https://go.microsoft.com/fwlink/?linkid=2083908) de registos de aplicações atribuída à sua app. |
| `grant_type` | Necessário   | Deve ser `authorization_code` para o fluxo do código de autorização.   |
| `scope`      | Necessário   | Uma lista de escopos separados por espaços. Os escopos solicitados nesse segmento devem ser equivalentes a ou um subconjunto dos escopos solicitados no primeiro segmento. Os âmbitos devem ser todos de um único recurso, juntamente com os âmbitos oIDC (`profile`, `openid`, `email`). Para obter uma explicação mais detalhada dos âmbitos, consulte [permissões, consentimento e âmbitos.](v2-permissions-and-consent.md) |
| `code`          | Necessário  | O authorization_code que adquiriu na primeira etapa do fluxo. |
| `redirect_uri`  | Necessário  | O mesmo valor redirect_uri que foi usado para adquirir o authorization_code. |
| `client_secret` | necessário para aplicativos Web | O segredo do aplicativo que você criou no portal de registro de aplicativo para seu aplicativo. Não deve usar o segredo da aplicação numa aplicação nativa porque client_secrets não pode ser armazenado de forma fiável em dispositivos. É necessário para aplicações web e APIs web, que têm a capacidade de armazenar o client_secret de forma segura no lado do servidor.  O segredo do cliente deve ser codificado por URL antes de ser enviado.  |
| `code_verifier` | opcional  | O mesmo code_verifier que foi usado para obter o authorization_code. Obrigatório se PKCE foi usado na solicitação de concessão de código de autorização. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta de token bem-sucedida terá A seguinte aparência:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parâmetro     | Descrição   |
|---------------|------------------------------|
| `access_token`  | O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API da Web.  |
| `token_type`    | Indica o valor do tipo de token. O único tipo ao qual o Azure AD dá suporte é portador |
| `expires_in`    | Por quanto tempo o token de acesso é válido (em segundos). |
| `scope`         | Os âmbitos para os que o access_token é válido. |
| `refresh_token` | Um token de atualização OAuth 2,0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o token de acesso atual expirar. Refresh_tokens são de longa duração, e podem ser usados para manter o acesso aos recursos por longos períodos de tempo. Para mais detalhes sobre o refrescante de um token de acesso, consulte a [secção abaixo](#refresh-the-access-token). <br> **Nota:** Só for solicitado se `offline_access` âmbito de aplicação. |
| `id_token`      | Um JWT (token Web JSON). O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. Para mais informações sobre id_tokens, consulte a [`id_token reference`](id-tokens.md). <br> **Observação:** Fornecido somente se `openid` escopo foi solicitado. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro terão a seguinte aparência:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parâmetro         | Descrição    |
|-------------------|----------------|
| `error`       | Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| `error_codes` | Uma lista de códigos de erro específicos do STS que podem ajudar no diagnóstico.  |
| `timestamp`   | A hora em que o erro ocorreu. |
| `trace_id`    | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| `correlation_id` | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |

### <a name="error-codes-for-token-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de token

| Código de Erro         | Descrição        | Ação do cliente    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Erro de protocolo, como um parâmetro necessário ausente. | Corrigir e reenviar a solicitação   |
| `invalid_grant`    | O código de autorização ou o verificador de código PKCE é inválido ou expirou. | Experimente um novo pedido para o ponto final `/authorize` e verifique se o parâmetro code_verifier estava correto.  |
| `unauthorized_client` | O cliente autenticado não está autorizado a usar esse tipo de concessão de autorização. | Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |
| `invalid_client` | Falha na autenticação do cliente.  | As credenciais do cliente não são válidas. Para corrigir, o administrador do aplicativo atualiza as credenciais.   |
| `unsupported_grant_type` | O servidor de autorização não oferece suporte ao tipo de concessão de autorização. | Altere o tipo de concessão na solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e ser detectado durante o teste inicial. |
| `invalid_resource` | O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou não está configurado corretamente. | Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD.  |
| `interaction_required` | A solicitação requer interação do usuário. Por exemplo, uma etapa de autenticação adicional é necessária. | Repita a solicitação com o mesmo recurso.  |
| `temporarily_unavailable` | O servidor está temporariamente muito ocupado para lidar com a solicitação. | Repita a solicitação. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a uma condição temporária. |

## <a name="use-the-access-token"></a>Usar o token de acesso

Agora que adquiriu com sucesso uma `access_token`, pode usar o símbolo em pedidos de APIs web, incluindo-o no cabeçalho `Authorization`:

> [!TIP]
> Executar esta solicitação no postmaster! (Substitua primeiro o cabeçalho `Authorization`) [![Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Atualizar o token de acesso

Access_tokens são de curta duração, e deve refreá-los depois de expirarem para continuar a aceder aos recursos. Pode fazê-lo submetendo mais um pedido de `POST` ao `/token` ponto final, desta vez fornecendo a `refresh_token` em vez da `code`.  Os tokens de atualização são válidos para todas as permissões para as as que o seu cliente já recebeu o consentimento - assim, um token de atualização emitido sobre um pedido de `scope=mail.read` pode ser usado para solicitar um novo sinal de acesso para `scope=api://contoso.com/api/UseResource`.  

Os tokens de atualização não têm tempos de vida especificados. Normalmente, os tempos de vida de tokens de atualização são relativamente longos. No entanto, em alguns casos, os tokens de atualização expiram, são revogados ou não têm privilégios suficientes para a ação desejada. A sua aplicação precisa de esperar e lidar [com erros devolvidos corretamente pelo ponto final da emissão](#error-codes-for-token-endpoint-errors) de fichas. 

Embora os tokens de atualização não sejam revogados quando usados para adquirir novos tokens de acesso, você deve descartar o token de atualização antigo. A [especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-6) diz: "O servidor de autorização MAY emite um novo token de atualização, caso em que o cliente deve descartar o antigo token de atualização e substituí-lo pelo novo token de atualização. O servidor de autorização pode revogar o token de atualização antigo depois de emitir um novo token de atualização para o cliente. "  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Tente executar esta solicitação no postmaster! (Não se esqueça de substituir o `refresh_token`) [![Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| Parâmetro     |                | Descrição        |
|---------------|----------------|--------------------|
| `tenant`        | Necessário     | O valor de `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | Necessário    | A **ID do aplicativo (cliente)** que a [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `grant_type`    | Necessário    | Deve ser `refresh_token` para esta perna do fluxo do código de autorização. |
| `scope`         | Necessário    | Uma lista de escopos separados por espaços. Os âmbitos solicitados nesta perna devem ser equivalentes ou a um subconjunto dos âmbitos solicitados na perna de pedido de authorization_code original. Se os escopos especificados nessa solicitação abrangerem vários servidores de recursos, o ponto de extremidade da plataforma de identidade da Microsoft retornará um token para o recurso especificado no primeiro escopo. Para obter uma explicação mais detalhada dos âmbitos, consulte [permissões, consentimento e âmbitos.](v2-permissions-and-consent.md) |
| `refresh_token` | Necessário    | O refresh_token que adquiriu na segunda etapa do fluxo. |
| `client_secret` | necessário para aplicativos Web | O segredo do aplicativo que você criou no portal de registro de aplicativo para seu aplicativo. Não deve ser usado numa aplicação nativa, porque client_secrets não podem ser armazenados de forma fiável em dispositivos. É necessário para aplicações web e APIs web, que têm a capacidade de armazenar o client_secret de forma segura no lado do servidor. |

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta de token bem-sucedida terá A seguinte aparência:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parâmetro     | Descrição         |
|---------------|-------------------------------------------------------------|
| `access_token`  | O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API da Web. |
| `token_type`    | Indica o valor do tipo de token. O único tipo ao qual o Azure AD dá suporte é portador |
| `expires_in`    | Por quanto tempo o token de acesso é válido (em segundos).   |
| `scope`         | Os âmbitos para os que o access_token é válido.    |
| `refresh_token` | Um novo token de atualização OAuth 2,0. Você deve substituir o token de atualização antigo por esse token de atualização adquirido recentemente para garantir que seus tokens de atualização permaneçam válidos pelo mais tempo possível. <br> **Nota:** Só for solicitado se `offline_access` âmbito de aplicação.|
| `id_token`      | Um JWT (token Web JSON) não assinado. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. Para mais informações sobre id_tokens, consulte a [`id_token reference`](id-tokens.md). <br> **Observação:** Fornecido somente se `openid` escopo foi solicitado. |

#### <a name="error-response"></a>Resposta de erro

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parâmetro         | Descrição                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação.           |
| `error_codes` |Uma lista de códigos de erro específicos do STS que podem ajudar no diagnóstico. |
| `timestamp` | A hora em que o erro ocorreu. |
| `trace_id` | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| `correlation_id` | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |

Para obter uma descrição dos códigos de erro e da ação recomendada pelo cliente, consulte os códigos de [erro para erros de ponto final simbólicos](#error-codes-for-token-endpoint-errors).
