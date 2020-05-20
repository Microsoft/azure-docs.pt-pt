---
title: Plataforma de identidade da Microsoft e fluxo de código de autorização OAuth 2.0 [ Azure
titleSuffix: Microsoft identity platform
description: Construa aplicações web utilizando a implementação da plataforma de identidade Microsoft do protocolo de autenticação OAuth 2.0.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 2bc9bb8b79ca0a6f59e6c771109cf4d102cdd78e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682235"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Plataforma de identidade da Microsoft e fluxo de código de autorização OAuth 2.0

A concessão de código de autorização OAuth 2.0 pode ser utilizada em aplicações instaladas num dispositivo para ter acesso a recursos protegidos, como apis web. Utilizando a implementação da plataforma de identidade Microsoft do OAuth 2.0, pode adicionar acesso de sessão e Acesso API às suas aplicações móveis e desktop.

Este artigo descreve como programar diretamente contra o protocolo na sua aplicação usando qualquer idioma.  Sempre que possível, recomendamos que utilize as Bibliotecas de Autenticação da Microsoft (MSAL) suportadas em vez de adquirir fichas e ligar para [APIs web protegidos](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Veja também as [aplicações de amostra que utilizam o MSAL.](sample-v2-code.md)

O fluxo de código de autorização OAuth 2.0 é descrito na [secção 4.1 da especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749). É usado para realizar autenticação e autorização na maioria dos tipos de aplicações, incluindo [aplicações de página única,](v2-app-types.md#single-page-apps-javascript) [aplicações web](v2-app-types.md#web-apps)e [aplicações instaladas de forma nativa.](v2-app-types.md#mobile-and-native-apps) O fluxo permite que as aplicações adquiram de forma segura access_tokens que possam ser usadas para aceder a recursos protegidos pelo ponto final da plataforma de identidade da Microsoft, bem como tokens de atualização para obter access_tokens adicionais, e tokens de ID para o utilizador assinado.

## <a name="protocol-diagram"></a>Diagrama de protocolo

A um nível elevado, todo o fluxo de autenticação para uma aplicação é um pouco assim:

![Fluxo de Código Auth Auth OAuth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="setup-required-for-single-page-apps"></a>Configuração necessária para aplicações de uma só página

O fluxo de código de autorização para aplicações de página única requer alguma configuração adicional.  Enquanto [está a criar a sua aplicação,](howto-create-service-principal-portal.md)deve marcar o redirecionamento uri para a sua aplicação como `spa` um URI redirecionado. Isto faz com que o servidor de login permita que o CORS (partilha de recursos de origem cruzada) para a sua aplicação.  Isto é necessário para resgatar o código usando XHR.

Se tentar utilizar o fluxo do código de autorização e ver este erro:

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

Em seguida, você precisa visitar o registo da sua aplicação e atualizar o URI redirecionamento para a sua aplicação para escrever `spa` .

## <a name="request-an-authorization-code"></a>Solicitar um código de autorização

O fluxo de código de autorização começa com o cliente a direcionar o utilizador para o `/authorize` ponto final. Neste pedido, o cliente solicita `openid` as, `offline_access` e `https://graph.microsoft.com/mail.read ` permissões do utilizador.  Algumas permissões são restritas à administração, por exemplo, escrever dados para o diretório de uma organização utilizando `Directory.ReadWrite.All` . Se a sua aplicação solicitar o acesso a uma dessas permissões de um utilizador organizacional, o utilizador recebe uma mensagem de erro que diz não estar autorizada a consentir com as permissões da sua aplicação. Para solicitar o acesso a âmbitos restritos de administração, deve solicitá-los diretamente a um administrador da empresa.  Para mais informações, leia [as permissões restritas](v2-permissions-and-consent.md#admin-restricted-permissions)à Administração .

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
> Clique no link abaixo para executar este pedido! Após a essão, o seu navegador deve ser redirecionado para com um na barra de `https://localhost/myapp/` `code` endereços.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parâmetro    | Obrigatório/opcional | Descrição |
|--------------|-------------|--------------|
| `tenant`    | obrigatório    | O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos `common` `organizations` são, `consumers` e identificadores de inquilinos. Para mais detalhes, consulte [os fundamentos do protocolo.](active-directory-v2-protocols.md#endpoints)  |
| `client_id`   | obrigatório    | O ID de **Aplicação (cliente)** que o [portal Azure – App registra](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída à sua app.  |
| `response_type` | obrigatório    | Deve incluir `code` o fluxo do código de autorização.       |
| `redirect_uri`  | obrigatório | O redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das redirect_uris que registou no portal, exceto que deve ser codificada. Para aplicações móveis nativas &, deve utilizar o valor padrão de `https://login.microsoftonline.com/common/oauth2/nativeclient` .   |
| `scope`  | obrigatório    | Uma lista de [âmbitos separados](v2-permissions-and-consent.md) pelo espaço que pretende que o utilizador consinta.  Para a `/authorize` leg do pedido, isto pode cobrir vários recursos, permitindo que a sua app obtenha o consentimento para várias APIs web que pretende ligar. |
| `response_mode`   | recomendado | Especifica o método que deve ser usado para enviar o símbolo resultante de volta para a sua aplicação. Pode ser um dos seguintes:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query`fornece o código como parâmetro de corda de consulta no seu URI redirecionamento. Se estiver a solicitar um símbolo de identificação utilizando o fluxo implícito, não pode utilizar `query` como especificado na [especificação OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Se estiver a pedir apenas o código, pode `query` `fragment` usar, ou `form_post` . `form_post`executa um POST contendo o código para o seu redirecionamento URI. Para mais informações, consulte o [protocolo OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | recomendado | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que desejes. Um valor único gerado aleatoriamente é normalmente usado para [prevenir ataques de falsificação de pedidos de local.](https://tools.ietf.org/html/rfc6749#section-10.12) O valor também pode codificar informações sobre o estado do utilizador na app antes do pedido de autenticação ocorrer, como a página ou visualização em que estavam. |
| `prompt`  | opcional    | Indica o tipo de interação do utilizador que é necessária. Os únicos valores válidos neste momento `login` `none` são, e `consent` .<br/><br/>- `prompt=login`obrigará o utilizador a introduzir as suas credenciais nesse pedido, negando um único sinal.<br/>- `prompt=none`é o oposto - irá garantir que o utilizador não seja apresentado com qualquer tipo de solicitação interativa. Se o pedido não puder ser concluído silenciosamente através de um único sinal, o ponto final da plataforma de identidade da Microsoft devolverá um `interaction_required` erro.<br/>- `prompt=consent`irá desencadear o diálogo de consentimento da OAuth após a assinatura do utilizador, pedindo ao utilizador que conceda permissões à aplicação.<br/>- `prompt=select_account`interromperá o único sign-on que fornece experiência de seleção de conta, listando todas as contas em sessão ou em qualquer conta recordada ou uma opção de escolher utilizar uma conta diferente.<br/> |
| `login_hint`  | opcional    | Pode ser usado para pré-preencher o campo de endereços de utilizador/e-mail da página de sessão para o utilizador, se souber o seu nome de utilizador com antecedência. Muitas vezes as aplicações usam este parâmetro durante a reautenticação, tendo já extraído o nome de utilizador de um início de sessão anterior utilizando a `preferred_username` reclamação.   |
| `domain_hint`  | opcional    | Se incluído, ignorará o processo de descoberta baseado em e-mail que o utilizador passa na página de entrada, levando a uma experiência de utilizador um pouco mais simplificada - por exemplo, enviando-as para o seu fornecedor de identidade federado. Muitas vezes as aplicações usam este parâmetro durante a reautenticação, extraindo o `tid` de um início de sessão anterior. Se o valor da `tid` reclamação `9188040d-6c67-4c5b-b112-36a304b66dad` for, deve `domain_hint=consumers` utilizar. Caso contrário, use `domain_hint=organizations` .  |
| `code_challenge`  | recomendado /necessário | Utilizado para garantir subvenções de código de autorização através da chave de prova para troca de códigos (PKCE). Obrigatório se `code_challenge_method` estiver incluído. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). Isto agora é recomendado para todos os tipos de aplicações - aplicações nativas, SPAs e clientes confidenciais como aplicações web. |
| `code_challenge_method` | recomendado /necessário | O método utilizado para codificar `code_verifier` o `code_challenge` parâmetro. Pode ser um dos seguintes valores:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Se for excluído, `code_challenge` assume-se que é um texto simples se estiver `code_challenge` incluído. A plataforma de identidade da Microsoft suporta tanto `plain` como `S256` . Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). Isto é necessário para [aplicações](reference-third-party-cookies-spas.md)de página única utilizando o fluxo de código de autorização .|


Neste momento, o utilizador será solicitado a introduzir as suas credenciais e a completar a autenticação. O ponto final da plataforma de identidade da Microsoft também garantirá que o utilizador consentiu nas permissões indicadas no parâmetro de `scope` consulta. Se o utilizador não tiver consentido com nenhuma dessas permissões, pedirá ao utilizador que consinta nas permissões necessárias. Os detalhes de [permissões, consentimento e aplicações multi-inquilinos são fornecidos aqui.](v2-permissions-and-consent.md)

Assim que o utilizador autenticar e conceder o consentimento, o ponto final da plataforma de identidade da Microsoft devolverá uma resposta à sua aplicação no indicado `redirect_uri` , utilizando o método especificado no `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta bem sucedida usando `response_mode=query` parece:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parâmetro | Descrição  |
|-----------|--------------|
| `code` | O authorization_code que a app solicitou. A aplicação pode usar o código de autorização para solicitar um sinal de acesso para o recurso alvo. Authorization_codes são de curta duração, normalmente expiram após cerca de 10 minutos. |
| `state` | Se um parâmetro estatal estiver incluído no pedido, o mesmo valor deve figurar na resposta. A aplicação deve verificar se os valores do Estado no pedido e resposta são idênticos. |

Também pode receber um token de acesso e um documento de identificação se solicitar um e tiver a subvenção implícita ativada no registo da sua candidatura.  Isto é por vezes referido como o "fluxo híbrido", e é usado por estruturas como ASP.NET.

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para a aplicação para que `redirect_uri` a aplicação possa manuseá-las adequadamente:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição  |
|----------|------------------|
| `error`  | Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem, e pode ser usada para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto final de autorização

A tabela seguinte descreve os vários códigos de erro que podem ser devolvidos no `error` parâmetro da resposta ao erro.

| Código de Erro  | Descrição    | Ação do Cliente   |
|-------------|----------------|-----------------|
| `invalid_request` | Erro de protocolo, como um parâmetro em falta necessário. | Corrija e reenvie o pedido. Este é um erro de desenvolvimento tipicamente apanhado durante os testes iniciais. |
| `unauthorized_client` | O pedido do cliente não está autorizado a solicitar um código de autorização. | Este erro ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino Azure AD do utilizador. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| `access_denied`  | Proprietário de recursos negado consentimento  | A aplicação do cliente pode notificar o utilizador de que não pode prosseguir a menos que o utilizador consinta. |
| `unsupported_response_type` | O servidor de autorização não suporta o tipo de resposta no pedido. | Corrija e reenvie o pedido. Este é um erro de desenvolvimento tipicamente apanhado durante os testes iniciais.  |
| `server_error`  | O servidor encontrou um erro inesperado.| Tente o pedido. Estes erros podem resultar de condições temporárias. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada para um erro temporário. |
| `temporarily_unavailable`   | O servidor está temporariamente demasiado ocupado para lidar com o pedido. | Tente o pedido. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a uma condição temporária. |
| `invalid_resource`  | O recurso-alvo é inválido porque não existe, o Azure AD não consegue encontrá-lo, ou não está corretamente configurado. | Este erro indica que o recurso, caso exista, não foi configurado no inquilino. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| `login_required` | Demasiados ou nenhum utilizador encontrado | O cliente solicitou autenticação silenciosa, `prompt=none` mas um único utilizador não foi encontrado. Isto pode significar que existem vários utilizadores ativos na sessão, ou nenhum utilizador. Isto tem em conta o inquilino escolhido (por exemplo, se houver duas contas Azure AD ativas e uma conta Microsoft, e é escolhida, a `consumers` autenticação silenciosa funcionará). |
| `interaction_required` | O pedido requer interação do utilizador. | É necessária uma etapa de autenticação adicional ou consentimento. Tente novamente o pedido sem `prompt=none` . |

## <a name="request-an-access-token"></a>Solicite um sinal de acesso

Agora que adquiriu um authorization_code e foi-lhe concedida permissão pelo utilizador, pode resgatar o `code` por um `access_token` recurso pretendido. Faça-o enviando um `POST` pedido para o ponto `/token` final:

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Tente executar este pedido no Carteiro! (Não se esqueça de substituir `code` o) [ ![ Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parâmetro  | Obrigatório/opcional | Descrição     |
|------------|-------------------|----------------|
| `tenant`   | obrigatório   | O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos `common` `organizations` são, `consumers` e identificadores de inquilinos. Para mais detalhes, consulte [os fundamentos do protocolo.](active-directory-v2-protocols.md#endpoints)  |
| `client_id` | obrigatório  | O ID de Aplicação (cliente) que o [portal Azure – Página](https://go.microsoft.com/fwlink/?linkid=2083908) de registos de aplicações atribuída à sua app. |
| `grant_type` | obrigatório   | Deve ser `authorization_code` para o fluxo de código de autorização.   |
| `scope`      | obrigatório   | Uma lista de âmbitos separados pelo espaço. Os âmbitos solicitados nesta perna devem ser equivalentes ou a um subconjunto dos âmbitos solicitados na primeira perna. Os âmbitos devem ser todos de um único recurso, juntamente com os âmbitos oIDC `profile` (, `openid` . . `email` ). Para obter uma explicação mais detalhada dos âmbitos, consulte [permissões, consentimento e âmbitos.](v2-permissions-and-consent.md) |
| `code`          | obrigatório  | O authorization_code que adquiriu na primeira etapa do fluxo. |
| `redirect_uri`  | obrigatório  | O mesmo valor redirect_uri que foi usado para adquirir o authorization_code. |
| `client_secret` | necessário para aplicações confidenciais web | O segredo de aplicação que criou no portal de registo de aplicações para a sua aplicação. Não deve utilizar o segredo da aplicação numa aplicação nativa ou numa única página porque client_secrets não pode ser armazenado de forma fiável em dispositivos ou páginas web. É necessário para aplicações web e APIs web, que têm a capacidade de armazenar o client_secret de forma segura no lado do servidor.  O segredo do cliente deve ser codificado antes de ser enviado. Para obter mais informações sobre a codificação de uri, consulte a especificação de [Sintaxe Genérica URI](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | recomendado  | O mesmo code_verifier que foi usado para obter o authorization_code. Necessário se o PKCE foi utilizado no pedido de concessão de código de autorização. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta simbólica bem sucedida será como:

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
| `access_token`  | O sinal de acesso solicitado. A aplicação pode usar este símbolo para autenticar o recurso seguro, como uma API web.  |
| `token_type`    | Indica o valor do tipo simbólico. O único tipo que a Azure AD suporta é bearer |
| `expires_in`    | Quanto tempo o token de acesso é válido (em segundos). |
| `scope`         | Os âmbitos para os que o access_token é válido. |
| `refresh_token` | Um token refresh OAuth 2.0. A aplicação pode usar este token adquirir fichas de acesso adicionais após o token de acesso atual expirar. Refresh_tokens são de longa duração, e podem ser usados para manter o acesso aos recursos por longos períodos de tempo. Para mais detalhes sobre o refrescante de um token de acesso, consulte a [secção abaixo](#refresh-the-access-token). <br> **Nota:** Só for solicitado o âmbito se `offline_access` for solicitado âmbito. |
| `id_token`      | Um Token Web JSON (JWT). A aplicação pode descodificar os segmentos deste token para solicitar informações sobre o utilizador que assinou. A aplicação pode cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. Para mais informações sobre id_tokens, consulte o [`id_token reference`](id-tokens.md) . <br> **Nota:** Só for solicitado o âmbito se `openid` for solicitado âmbito. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro serão:

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
| `error`       | Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem, e pode ser usada para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |
| `error_codes` | Uma lista de códigos de erro específicos do STS que podem ajudar nos diagnósticos.  |
| `timestamp`   | O momento em que ocorreu o erro. |
| `trace_id`    | Um identificador único para o pedido que pode ajudar nos diagnósticos. |
| `correlation_id` | Um identificador único para o pedido que pode ajudar no diagnóstico através de componentes. |

### <a name="error-codes-for-token-endpoint-errors"></a>Códigos de erro para erros de ponto final simbólicos

| Código de Erro         | Descrição        | Ação do Cliente    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Erro de protocolo, como um parâmetro em falta necessário. | Corrija o pedido ou o registo da aplicação e reenvie o pedido   |
| `invalid_grant`    | O código de autorização ou verificador de código PKCE é inválido ou expirou. | Experimente um novo pedido para o `/authorize` ponto final e verifique se o parâmetro code_verifier estava correto.  |
| `unauthorized_client` | O cliente autenticado não está autorizado a usar este tipo de concessão de autorização. | Isto ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino Azure AD do utilizador. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| `invalid_client` | A autenticação do cliente falhou.  | As credenciais do cliente não são válidas. Para corrigir, o administrador de aplicação atualiza as credenciais.   |
| `unsupported_grant_type` | O servidor de autorização não suporta o tipo de concessão de autorização. | Alterar o tipo de subvenção no pedido. Este tipo de erro só deve ocorrer durante o desenvolvimento e ser detetado durante os testes iniciais. |
| `invalid_resource` | O recurso-alvo é inválido porque não existe, o Azure AD não consegue encontrá-lo, ou não está corretamente configurado. | Isto indica que o recurso, caso exista, não foi configurado no inquilino. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD.  |
| `interaction_required` | O pedido requer interação do utilizador. Por exemplo, é necessário um passo adicional de autenticação. | Tente o pedido com o mesmo recurso.  |
| `temporarily_unavailable` | O servidor está temporariamente demasiado ocupado para lidar com o pedido. | Tente o pedido. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a uma condição temporária. |

> [!NOTE]
> As aplicações de página única podem receber um erro indicando que o resgate por `invalid_request` token de origem cruzada só é permitido para o tipo de cliente 'Aplicação de página única'.  Isto indica que o uri redireccionamento utilizado para solicitar o símbolo não foi marcado como um `spa` URI redirecionado.  Reveja as etapas de registo de [candidaturas](#setup-required-for-single-page-apps) sobre como ativar este fluxo.

## <a name="use-the-access-token"></a>Use o símbolo de acesso

Agora que adquiriu `access_token` um, pode usar o símbolo em pedidos de APIs web, incluindo-o no `Authorization` cabeçalho:

> [!TIP]
> Execute este pedido no Carteiro! (Substitua primeiro o `Authorization` cabeçalho) [ ![ Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Refrescar o sinal de acesso

Access_tokens são de curta duração, e deve refreá-los depois de expirarem para continuar a aceder aos recursos. Pode fazê-lo submetendo outro `POST` pedido ao `/token` ponto final, desta vez fornecendo o `refresh_token` em vez do `code` .  Os tokens de atualização são válidos para todas as permissões para as as que o seu cliente já recebeu o consentimento - assim, um token de atualização emitido sobre um pedido para pode ser usado para solicitar um novo sinal de `scope=mail.read` acesso para `scope=api://contoso.com/api/UseResource` .

Tokens de atualização para aplicações web e aplicações nativas não têm períodos de vida especificados. Tipicamente, as vidas de tokens refrescantes são relativamente longas. No entanto, em alguns casos, as fichas de atualização expiram, são revogadas ou carecem de privilégios suficientes para a ação desejada. A sua aplicação precisa de esperar e lidar [com erros devolvidos corretamente pelo ponto final da emissão](#error-codes-for-token-endpoint-errors) de fichas. As aplicações de página única, no entanto, recebem um símbolo com uma vida útil de 24 horas, exigindo uma nova autenticação todos os dias.  Isto pode ser feito silenciosamente num iframe quando os cookies de terceiros estão ativados, mas deve ser feito em um quadro de nível superior (ou navegação de página inteira ou um popup) em navegadores sem cookies de terceiros como O Safari.

Embora os tokens de atualização não sejam revogados quando usados para adquirir novos tokens de acesso, espera-se que você deite fora o antigo token de atualização. A [especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-6) diz: "O servidor de autorização MAY emite um novo token de atualização, caso em que o cliente deve descartar o antigo token de atualização e substituí-lo pelo novo token de atualização. O servidor de autorização MAY revoga o antigo token de atualização depois de emitir um novo token de atualização para o cliente."

>[!IMPORTANT]
> Para fichas de atualização enviadas para um URI redirecionado registado como , o token de `spa` atualização expirará após 24 horas. Tokens de atualização adicionais adquiridos usando o token de atualização inicial irá levar ao longo desse tempo de validade, por isso as aplicações devem estar preparadas para reexecutar o fluxo do código de autorização usando uma autenticação interativa para obter um novo token de atualização a cada 24 horas. Os utilizadores não têm de introduzir as suas credenciais, e normalmente nem sequer verão nenhum UX, apenas uma recarga da sua aplicação - mas o navegador deve visitar a página de login num quadro de nível superior para ver a sessão de login.  Isto deve-se a funcionalidades de [privacidade nos navegadores que bloqueiam cookies de terceiros.](reference-third-party-cookies-spas.md)

```HTTP

// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Tente executar este pedido no Carteiro! (Não se esqueça de substituir `refresh_token` o) [ ![ Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| Parâmetro     |                | Descrição        |
|---------------|----------------|--------------------|
| `tenant`        | obrigatório     | O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos `common` `organizations` são, `consumers` e identificadores de inquilinos. Para mais detalhes, consulte [os fundamentos do protocolo.](active-directory-v2-protocols.md#endpoints)   |
| `client_id`     | obrigatório    | O ID de **Aplicação (cliente)** que o [portal Azure – App registra](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída à sua app. |
| `grant_type`    | obrigatório    | Deve ser `refresh_token` para esta perna do fluxo de código de autorização. |
| `scope`         | obrigatório    | Uma lista de âmbitos separados pelo espaço. Os âmbitos solicitados nesta perna devem ser equivalentes ou a um subconjunto dos âmbitos solicitados na perna de pedido de authorization_code original. Se os âmbitos especificados neste pedido abrangerem vários servidores de recursos, então o ponto final da plataforma de identidade da Microsoft devolverá um símbolo para o recurso especificado no primeiro âmbito. Para obter uma explicação mais detalhada dos âmbitos, consulte [permissões, consentimento e âmbitos.](v2-permissions-and-consent.md) |
| `refresh_token` | obrigatório    | O refresh_token que adquiriu na segunda etapa do fluxo. |
| `client_secret` | necessário para aplicações web | O segredo de aplicação que criou no portal de registo de aplicações para a sua aplicação. Não deve ser usado numa aplicação nativa, porque client_secrets não podem ser armazenados de forma fiável em dispositivos. É necessário para aplicações web e APIs web, que têm a capacidade de armazenar o client_secret de forma segura no lado do servidor. Este segredo tem de ser codificado por URL. Para mais informações, consulte a especificação de [Sintaxe Genérica URI](https://tools.ietf.org/html/rfc3986#page-12). |

#### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta simbólica bem sucedida será como:

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
| `access_token`  | O sinal de acesso solicitado. A aplicação pode usar este símbolo para autenticar o recurso seguro, como uma API web. |
| `token_type`    | Indica o valor do tipo simbólico. O único tipo que a Azure AD suporta é bearer |
| `expires_in`    | Quanto tempo o token de acesso é válido (em segundos).   |
| `scope`         | Os âmbitos para os que o access_token é válido.    |
| `refresh_token` | Um novo token de refrescamento OAuth 2.0. Deve substituir o antigo token de atualização por este token de atualização recém-adquirido para garantir que as suas fichas de atualização permaneçam válidas durante o maior tempo possível. <br> **Nota:** Só for solicitado o âmbito se `offline_access` for solicitado âmbito.|
| `id_token`      | Um JSON Web Token (JWT). A aplicação pode descodificar os segmentos deste token para solicitar informações sobre o utilizador que assinou. A aplicação pode cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. Para mais informações sobre id_tokens, consulte o [`id_token reference`](id-tokens.md) . <br> **Nota:** Só for solicitado o âmbito se `openid` for solicitado âmbito. |

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
| `error`           | Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem, e pode ser usada para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação.           |
| `error_codes` |Uma lista de códigos de erro específicos do STS que podem ajudar nos diagnósticos. |
| `timestamp` | O momento em que ocorreu o erro. |
| `trace_id` | Um identificador único para o pedido que pode ajudar nos diagnósticos. |
| `correlation_id` | Um identificador único para o pedido que pode ajudar no diagnóstico através de componentes. |

Para obter uma descrição dos códigos de erro e da ação recomendada pelo cliente, consulte os códigos de [erro para erros de ponto final simbólicos](#error-codes-for-token-endpoint-errors).
