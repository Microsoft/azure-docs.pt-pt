---
title: Plataforma de identidade da Microsoft e fluxo de código de autorização OAuth | Azure
description: Criação de aplicativos Web usando a implementação da plataforma Microsoft Identity do protocolo de autenticação OAuth 2,0.
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
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: a20f540d4d220d5cf7171633b817a496d48b8128
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207717"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Plataforma de identidade da Microsoft e fluxo de código de autorização do OAuth 2,0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

A concessão de código de autorização OAuth 2,0 pode ser usada em aplicativos que estão instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Usando a implementação da plataforma Microsoft Identity do OAuth 2,0, você pode adicionar entrada e acesso à API para seus aplicativos móveis e de área de trabalho. Este guia é independente de linguagem e descreve como enviar e receber mensagens HTTP sem usar nenhuma das [bibliotecas de autenticação de código aberto do Azure](reference-v2-libraries.md).

Este artigo descreve como programar diretamente em relação ao protocolo em seu aplicativo.  Quando possível, recomendamos que você use as MSAL (bibliotecas de autenticação da Microsoft) com suporte em vez de [adquirir tokens e chamar APIs da Web protegidas](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Veja também os [aplicativos de exemplo que usam MSAL](sample-v2-code.md).

> [!NOTE]
> Nem todos os cenários de Azure Active Directory & recursos têm suporte do ponto de extremidade da plataforma Microsoft Identity. Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

O fluxo de código de autorização do OAuth 2,0 é descrito na [seção 4,1 da especificação do OAuth 2,0](https://tools.ietf.org/html/rfc6749). Ele é usado para executar autenticação e autorização na maioria dos tipos de aplicativos, incluindo [aplicativos Web](v2-app-types.md#web-apps) e [aplicativos instalados nativamente](v2-app-types.md#mobile-and-native-apps). O Flow permite que os aplicativos adquiram access_tokens com segurança que podem ser usados para acessar recursos protegidos pelo ponto de extremidade da plataforma Microsoft Identity.

## <a name="protocol-diagram"></a>Diagrama de protocolo

Em um alto nível, todo o fluxo de autenticação para um aplicativo nativo/móvel parece um pouco assim:

![Fluxo de código de autenticação OAuth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Solicitar um código de autorização

O fluxo do código de autorização começa com o cliente direcionando o usuário para o ponto de extremidade `/authorize`. Nessa solicitação, o cliente indica as permissões que precisa adquirir do usuário:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
```

> [!TIP]
> Clique no link abaixo para executar esta solicitação! Depois de entrar, seu navegador deve ser redirecionado para `https://localhost/myapp/` com um `code` na barra de endereços.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parâmetro    | Obrigatório/opcional | Descrição |
|--------------|-------------|--------------|
| `tenant`    | Necessário    | O valor de `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | Necessário    | A **ID do aplicativo (cliente)** que a [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo.  |
| `response_type` | Necessário    | Deve incluir `code` para o fluxo do código de autorização.       |
| `redirect_uri`  | Necessário | O redirect_uri de seu aplicativo, em que as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, exceto que ele deve ser codificado por URL. Para aplicativos móveis & nativos, você deve usar o valor padrão de `https://login.microsoftonline.com/common/oauth2/nativeclient`.   |
| `scope`  | Necessário    | Uma lista separada por espaços de [escopos](v2-permissions-and-consent.md) aos quais você deseja que o usuário concorde.  Para o `/authorize` trecho da solicitação, isso pode abranger vários recursos, permitindo que seu aplicativo receba consentimento para várias APIs Web que você deseja chamar. |
| `response_mode`   | aconselhável | Especifica o método que deve ser usado para enviar o token resultante de volta ao seu aplicativo. Pode ser um dos seguintes procedimentos:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` fornece o código como um parâmetro de cadeia de caracteres de consulta em seu URI de redirecionamento. Se você estiver solicitando um token de ID usando o fluxo implícito, não poderá usar `query` conforme especificado na [especificação OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Se você estiver solicitando apenas o código, poderá usar `query`, `fragment`ou `form_post`. `form_post` executa uma POSTAgem que contém o código para o URI de redirecionamento. Para obter mais informações, consulte [protocolo OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | aconselhável | Um valor incluído na solicitação que também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo que você desejar. Um valor exclusivo gerado aleatoriamente geralmente é usado para [impedir ataques de solicitação entre sites forjado](https://tools.ietf.org/html/rfc6749#section-10.12). O valor também pode codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |
| `prompt`  | adicional    | Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são `login`, `none`e `consent`.<br/><br/>- `prompt=login` forçará o usuário a inserir suas credenciais nessa solicitação, negando o logon único.<br/>- `prompt=none` for o oposto, ele garantirá que o usuário não seja apresentado a nenhum prompt interativo. Se a solicitação não puder ser concluída silenciosamente por meio de logon único, o ponto de extremidade da plataforma de identidade da Microsoft retornará um erro de `interaction_required`.<br/>- `prompt=consent` disparará a caixa de diálogo de consentimento do OAuth depois que o usuário fizer logon, solicitando que o usuário conceda permissões ao aplicativo. |
| `login_hint`  | adicional    | Pode ser usado para preencher previamente o campo nome de usuário/endereço de email da página de entrada do usuário, se você souber seu nome de usuário antes do tempo. Com frequência, os aplicativos usarão esse parâmetro durante a reautenticação, já tendo extraído o nome de usuário de uma entrada anterior usando a declaração de `preferred_username`.   |
| `domain_hint`  | adicional    | Pode ser uma das `consumers` ou `organizations`.<br/><br/>Se for incluído, ele ignorará o processo de descoberta baseado em email que o usuário passa na página de entrada, levando a uma experiência de usuário um pouco mais simplificada. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, extraindo o `tid` de uma entrada anterior. Se o valor de declaração de `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, você deverá usar `domain_hint=consumers`. Caso contrário, use `domain_hint=organizations`.  |
| `code_challenge_method` | adicional    | O método usado para codificar o `code_verifier` para o parâmetro `code_challenge`. Pode ser um dos seguintes valores:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Se for excluído, `code_challenge` será considerado texto sem formatação se `code_challenge` estiver incluído. A plataforma de identidade da Microsoft dá suporte a `plain` e `S256`. Para obter mais informações, consulte a [RFC PKCE](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | adicional | Usado para proteger as concessões de código de autorização por meio da chave de prova de troca de código (PKCE) de um cliente nativo. Necessário se `code_challenge_method` estiver incluído. Para obter mais informações, consulte a [RFC PKCE](https://tools.ietf.org/html/rfc7636). |

Neste ponto, o usuário será solicitado a inserir suas credenciais e concluir a autenticação. O ponto de extremidade da plataforma Microsoft Identity também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope`. Se o usuário não tiver consentido nenhuma dessas permissões, ele solicitará que o usuário consenti as permissões necessárias. Detalhes de [permissões, consentimento e aplicativos multilocatários são fornecidos aqui](v2-permissions-and-consent.md).

Depois que o usuário autenticar e conceder consentimento, o ponto de extremidade da plataforma de identidade da Microsoft retornará uma resposta ao seu aplicativo no `redirect_uri`indicado, usando o método especificado no parâmetro `response_mode`.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=query` é semelhante a:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parâmetro | Descrição  |
|-----------|--------------|
| `code` | O authorization_code que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Authorization_codes são de vida curta, normalmente eles expiram após cerca de 10 minutos. |
| `state` | Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas à `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

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

A tabela a seguir descreve os vários códigos de erro que podem ser retornados no parâmetro `error` da resposta de erro.

| Código de Erro  | Descrição    | Ação do cliente   |
|-------------|----------------|-----------------|
| `invalid_request` | Erro de protocolo, como um parâmetro necessário ausente. | Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente detectado durante o teste inicial. |
| `unauthorized_client` | O aplicativo cliente não tem permissão para solicitar um código de autorização. | Esse erro geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |
| `access_denied`  | Consentimento negado pelo proprietário do recurso  | O aplicativo cliente pode notificar o usuário de que ele não pode continuar, a menos que o usuário consentisse. |
| `unsupported_response_type` | O servidor de autorização não oferece suporte ao tipo de resposta na solicitação. | Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente detectado durante o teste inicial.  |
| `server_error`  | O servidor encontrou um erro inesperado.| Repita a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada para um erro temporário. |
| `temporarily_unavailable`   | O servidor está temporariamente muito ocupado para lidar com a solicitação. | Repita a solicitação. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a uma condição temporária. |
| `invalid_resource`  | O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou não está configurado corretamente. | Esse erro indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |
| `login_required` | Muitos usuários ou nenhum usuário encontrado | O cliente solicitou a autenticação silenciosa (`prompt=none`), mas não foi possível encontrar um único usuário. Isso pode significar que há vários usuários ativos na sessão ou nenhum usuário. Isso leva em conta o locatário escolhido (por exemplo, se houver duas contas do Azure AD ativas e uma conta Microsoft e `consumers` for escolhido, a autenticação silenciosa funcionará). |
| `interaction_required` | A solicitação requer interação do usuário. | É necessária uma etapa de autenticação adicional ou um consentimento. Repita a solicitação sem `prompt=none`. |

## <a name="request-an-access-token"></a>Solicitar um token de acesso

Agora que você adquiriu um authorization_code e recebeu permissão pelo usuário, você pode resgatar o `code` de um `access_token` para o recurso desejado. Faça isso enviando uma solicitação de `POST` para o ponto de extremidade de `/token`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Tente executar esta solicitação no postmaster! (Não se esqueça de substituir o `code`) [![tentar executar esta solicitação no postmaster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parâmetro  | Obrigatório/opcional | Descrição     |
|------------|-------------------|----------------|
| `tenant`   | Necessário   | O valor de `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | Necessário  | A ID do aplicativo (cliente) que a página [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuída ao seu aplicativo. |
| `grant_type` | Necessário   | Deve ser `authorization_code` para o fluxo do código de autorização.   |
| `scope`      | Necessário   | Uma lista de escopos separados por espaços. Os escopos solicitados nesse segmento devem ser equivalentes a ou um subconjunto dos escopos solicitados no primeiro segmento. Os escopos devem ser todos de um único recurso, juntamente com escopos OIDC (`profile`, `openid`, `email`). Para obter uma explicação mais detalhada dos escopos, consulte [permissões, consentimento e escopos](v2-permissions-and-consent.md). |
| `code`          | Necessário  | O authorization_code que você adquiriu no primeiro segmento do fluxo. |
| `redirect_uri`  | Necessário  | O mesmo valor de redirect_uri que foi usado para adquirir o authorization_code. |
| `client_secret` | necessário para aplicativos Web | O segredo do aplicativo que você criou no portal de registro de aplicativo para seu aplicativo. Você não deve usar o segredo do aplicativo em um aplicativo nativo porque client_secrets não pode ser armazenado de forma confiável em dispositivos. Ele é necessário para aplicativos Web e APIs Web, que têm a capacidade de armazenar o client_secret com segurança no lado do servidor.  O segredo do cliente deve ser codificado por URL antes de ser enviado.  |
| `code_verifier` | adicional  | O mesmo code_verifier usado para obter o authorization_code. Obrigatório se PKCE foi usado na solicitação de concessão de código de autorização. Para obter mais informações, consulte a [RFC PKCE](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta de token bem-sucedida terá A seguinte aparência:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parâmetro     | Descrição   |
|---------------|------------------------------|
| `access_token`  | O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API da Web.  |
| `token_type`    | Indica o valor do tipo de token. O único tipo ao qual o Azure AD dá suporte é portador |
| `expires_in`    | Por quanto tempo o token de acesso é válido (em segundos). |
| `scope`         | Os escopos para os quais o access_token é válido. |
| `refresh_token` | Um token de atualização OAuth 2,0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o token de acesso atual expirar. Refresh_tokens são de vida longa e podem ser usados para manter o acesso aos recursos por longos períodos de tempo. Para obter mais detalhes sobre como atualizar um token de acesso, consulte a [seção abaixo](#refresh-the-access-token). <br> **Observação:** Fornecido somente se `offline_access` escopo foi solicitado. |
| `id_token`      | Um JWT (token Web JSON). O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, consulte a [`id_token reference`](id-tokens.md). <br> **Observação:** Fornecido somente se `openid` escopo foi solicitado. |

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
| `invalid_grant`    | O código de autorização ou o verificador de código PKCE é inválido ou expirou. | Tente uma nova solicitação para o ponto de extremidade `/authorize` e verifique se o parâmetro code_verifier estava correto.  |
| `unauthorized_client` | O cliente autenticado não está autorizado a usar esse tipo de concessão de autorização. | Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD. |
| `invalid_client` | Falha na autenticação do cliente.  | As credenciais do cliente não são válidas. Para corrigir, o administrador do aplicativo atualiza as credenciais.   |
| `unsupported_grant_type` | O servidor de autorização não oferece suporte ao tipo de concessão de autorização. | Altere o tipo de concessão na solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e ser detectado durante o teste inicial. |
| `invalid_resource` | O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou não está configurado corretamente. | Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar ao usuário instruções para instalar o aplicativo e adicioná-lo ao Azure AD.  |
| `interaction_required` | A solicitação requer interação do usuário. Por exemplo, uma etapa de autenticação adicional é necessária. | Repita a solicitação com o mesmo recurso.  |
| `temporarily_unavailable` | O servidor está temporariamente muito ocupado para lidar com a solicitação. | Repita a solicitação. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a uma condição temporária. |

## <a name="use-the-access-token"></a>Usar o token de acesso

Agora que você adquiriu com êxito um `access_token`, pode usar o token em solicitações para APIs Web, incluindo-o no cabeçalho `Authorization`:

> [!TIP]
> Executar esta solicitação no postmaster! (Substitua o cabeçalho de `Authorization` primeiro) [![tentar executar esta solicitação no postmaster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Atualizar o token de acesso

Access_tokens são de curta duração e você deve atualizá-los depois que eles expirarem para continuar acessando os recursos. Você pode fazer isso enviando outra solicitação de `POST` para o ponto de extremidade `/token`, desta vez fornecendo a `refresh_token` em vez do `code`.  Os tokens de atualização são válidos para todas as permissões para as quais o cliente já recebeu consentimento, portanto, um token de atualização emitido em uma solicitação de `scope=mail.read` pode ser usado para solicitar um novo token de acesso para `scope=api://contoso.com/api/UseResource`.  

Os tokens de atualização não têm tempos de vida especificados. Normalmente, os tempos de vida de tokens de atualização são relativamente longos. No entanto, em alguns casos, os tokens de atualização expiram, são revogados ou não têm privilégios suficientes para a ação desejada. Seu aplicativo precisa esperar e tratar [os erros retornados pelo ponto de extremidade de emissão de token](#error-codes-for-token-endpoint-errors) corretamente. 

Embora os tokens de atualização não sejam revogados quando usados para adquirir novos tokens de acesso, você deve descartar o token de atualização antigo. A [especificação do OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-6) diz: "o servidor de autorização pode emitir um novo token de atualização; nesse caso, o cliente deve descartar o token de atualização antigo e substituí-lo pelo novo token de atualização. O servidor de autorização pode revogar o token de atualização antigo depois de emitir um novo token de atualização para o cliente. "  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Tente executar esta solicitação no postmaster! (Não se esqueça de substituir o `refresh_token`) [![tentar executar esta solicitação no postmaster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| Parâmetro     |                | Descrição        |
|---------------|----------------|--------------------|
| `tenant`        | Necessário     | O valor de `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | Necessário    | A **ID do aplicativo (cliente)** que a [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `grant_type`    | Necessário    | Deve ser `refresh_token` para esse segmento do fluxo de código de autorização. |
| `scope`         | Necessário    | Uma lista de escopos separados por espaços. Os escopos solicitados nesse segmento devem ser equivalentes a ou um subconjunto dos escopos solicitados no segmento original da solicitação de authorization_code. Se os escopos especificados nessa solicitação abrangerem vários servidores de recursos, o ponto de extremidade da plataforma de identidade da Microsoft retornará um token para o recurso especificado no primeiro escopo. Para obter uma explicação mais detalhada dos escopos, consulte [permissões, consentimento e escopos](v2-permissions-and-consent.md). |
| `refresh_token` | Necessário    | O refresh_token que você adquiriu no segundo segmento do fluxo. |
| `client_secret` | necessário para aplicativos Web | O segredo do aplicativo que você criou no portal de registro de aplicativo para seu aplicativo. Ele não deve ser usado em um aplicativo nativo, porque client_secrets não pode ser armazenado de forma confiável em dispositivos. Ele é necessário para aplicativos Web e APIs Web, que têm a capacidade de armazenar o client_secret com segurança no lado do servidor. |

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta de token bem-sucedida terá A seguinte aparência:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parâmetro     | Descrição         |
|---------------|-------------------------------------------------------------|
| `access_token`  | O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API da Web. |
| `token_type`    | Indica o valor do tipo de token. O único tipo ao qual o Azure AD dá suporte é portador |
| `expires_in`    | Por quanto tempo o token de acesso é válido (em segundos).   |
| `scope`         | Os escopos para os quais o access_token é válido.    |
| `refresh_token` | Um novo token de atualização OAuth 2,0. Você deve substituir o token de atualização antigo por esse token de atualização adquirido recentemente para garantir que seus tokens de atualização permaneçam válidos pelo mais tempo possível. <br> **Observação:** Fornecido somente se `offline_access` escopo foi solicitado.|
| `id_token`      | Um JWT (token Web JSON) não assinado. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, consulte a [`id_token reference`](id-tokens.md). <br> **Observação:** Fornecido somente se `openid` escopo foi solicitado. |

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

Para obter uma descrição dos códigos de erro e a ação recomendada do cliente, consulte [códigos de erro para erros de ponto de extremidade do token](#error-codes-for-token-endpoint-errors).
