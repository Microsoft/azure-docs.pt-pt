---
title: Plataforma de identidade da Microsoft e fluxo de código de autorização OAuth 2.0 Rio Azure
titleSuffix: Microsoft identity platform
description: Construa aplicações web utilizando a implementação da plataforma de identidade da Microsoft do protocolo de autenticação OAuth 2.0.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6648cfb717ade4b842e8ff470a46bf744b630363
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88612321"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Plataforma de identidade da Microsoft e fluxo de código de autorização OAuth 2.0

A bolsa de código de autorização OAuth 2.0 pode ser usada em aplicações que são instaladas num dispositivo para ter acesso a recursos protegidos, como APIs web. Utilizando a implementação da plataforma de identidade microsoft de OAuth 2.0, pode adicionar acesso de sôs-in e API às suas aplicações móveis e desktop.

Este artigo descreve como programar diretamente contra o protocolo na sua aplicação usando qualquer idioma.  Quando possível, recomendamos que utilize as Bibliotecas de Autenticação da Microsoft (MSAL) suportadas para [adquirir fichas e chamar APIs web seguras](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Dê também uma olhada nas aplicações de [amostra que utilizam o MSAL.](sample-v2-code.md)

O fluxo de código de autorização OAuth 2.0 é descrito na [secção 4.1 da especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749). É usado para realizar autenticação e autorização na maioria dos tipos de aplicações, incluindo aplicações de [página única,](v2-app-types.md#single-page-apps-javascript) [aplicações web](v2-app-types.md#web-apps)e [aplicações instaladas de forma nativa.](v2-app-types.md#mobile-and-native-apps) O fluxo permite que as aplicações adquiram de forma segura access_tokens que podem ser usadas para aceder a recursos protegidos pelo ponto final da plataforma de identidade da Microsoft, bem como a refresh tokens para obter access_tokens adicionais, e fichas de ID para o utilizador assinado.

## <a name="protocol-diagram"></a>Diagrama de protocolo

A um nível elevado, todo o fluxo de autenticação para uma aplicação é um pouco parecido com este:

![Fluxo de código OAuth Auth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Redirecionar configuração URI necessária para aplicações de uma só página

O fluxo de código de autorização para aplicações de página única requer alguma configuração adicional.  Siga as instruções para [criar a sua aplicação de uma página](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) para marcar corretamente o seu URI de redirecionamento, conforme ativado para o CORS. Para atualizar um URI de redirecionamento existente para ativar o CORS, abra o editor manifesto e desabrove o `type` campo para o seu URI de redirecionamento para a `spa` `replyUrlsWithType` secção. Também pode clicar no URI de redirecionamento na secção "Web" do separador Autenticação e selecionar os URIs que pretende migrar para utilizar o fluxo de código de autorização.

O `spa` tipo de redirecionamento é retrocompatível com o fluxo implícito. As aplicações que utilizam atualmente o fluxo implícito para obter fichas podem mover-se para o `spa` tipo URI de redirecionamento sem problemas e continuar a usar o fluxo implícito.

Se tentar utilizar o fluxo de código de autorização e ver este erro:

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

Em seguida, precisa de visitar o registo da sua aplicação e atualizar o URI de redirecionamento para que a sua aplicação `spa` escreva.

## <a name="request-an-authorization-code"></a>Solicitar um código de autorização

O fluxo de código de autorização começa com o cliente a direcionar o utilizador para o `/authorize` ponto final. Neste pedido, o cliente solicita o `openid` `offline_access` , e `https://graph.microsoft.com/mail.read ` permissões do utilizador.  Algumas permissões são restritas a administradores, por exemplo, escrever dados para o diretório de uma organização através da utilização `Directory.ReadWrite.All` de . Se a sua aplicação solicitar o acesso a uma destas permissões de um utilizador organizacional, o utilizador recebe uma mensagem de erro que diz não estar autorizado a consentir com as permissões da sua aplicação. Para solicitar o acesso a âmbitos restritos de administração, deverá solicitá-los diretamente a um administrador da empresa.  Para mais informações, leia [as permissões restritas a administrador.](v2-permissions-and-consent.md#admin-restricted-permissions)

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> Clique no link abaixo para executar este pedido! Após a sua sessão, o seu navegador deve ser redirecionado para `https://localhost/myapp/` um na barra de `code` endereços.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parâmetro    | Obrigatório/opcional | Descrição |
|--------------|-------------|--------------|
| `tenant`    | obrigatório    | O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos `common` `organizations` são, `consumers` e os identificadores de inquilinos. Para mais detalhes, consulte [o protocolo básico.](active-directory-v2-protocols.md#endpoints)  |
| `client_id`   | obrigatório    | O **ID da Aplicação (cliente)** que o [portal Azure – Experiência de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuído à sua app.  |
| `response_type` | obrigatório    | Deve incluir `code` para o fluxo de código de autorização.       |
| `redirect_uri`  | obrigatório | O redirect_uri da sua app, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das redirect_uris que registou no portal, exceto que deve estar codificada url. Para aplicações móveis & nativas, deve utilizar o valor predefinido de `https://login.microsoftonline.com/common/oauth2/nativeclient` .   |
| `scope`  | obrigatório    | Uma lista de [âmbitos separados](v2-permissions-and-consent.md) pelo espaço a que o utilizador consinta.  Para a `/authorize` parte do pedido, isto pode abranger vários recursos, permitindo que a sua app obtenha o consentimento para várias APIs web que pretende ligar. |
| `response_mode`   | recomendado | Especifica o método que deve ser usado para enviar o símbolo resultante de volta para a sua aplicação. Pode ser um dos seguintes:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` fornece o código como um parâmetro de cadeia de consulta no seu URI de redirecionamento. Se estiver a solicitar um token de identificação utilizando o fluxo implícito, não pode utilizar `query` como especificado na [especificação OpenID.](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations) Se está a solicitar apenas o código, pode `query` `fragment` usar, ou `form_post` . `form_post` executa um POST contendo o código para o seu URI de redirecionamento. Para mais informações, consulte [o protocolo OpenID Connect](../azuread-dev/v1-protocols-openid-connect-code.md).  |
| `state`                 | recomendado | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de conteúdos que desejes. Um valor único gerado aleatoriamente é normalmente usado para [prevenir ataques de falsificação de pedidos de trans-locais](https://tools.ietf.org/html/rfc6749#section-10.12). O valor também pode codificar informações sobre o estado do utilizador na app antes do pedido de autenticação ocorrer, como a página ou a vista em que se encontravam. |
| `prompt`  | opcional    | Indica o tipo de interação do utilizador que é necessária. Os únicos valores válidos neste momento são `login` `none` , e `consent` .<br/><br/>- `prompt=login` forçará o utilizador a introduzir as suas credenciais nesse pedido, negando um único sinal.<br/>- `prompt=none` é o oposto - irá garantir que o utilizador não é apresentado com qualquer posição interativa. Se o pedido não puder ser concluído silenciosamente através de um único sinal, o ponto final da plataforma de identidade da Microsoft retornará um `interaction_required` erro.<br/>- `prompt=consent` irá acionar o diálogo de consentimento OAuth após o utilizador entrar, pedindo ao utilizador que conceda permissões à aplicação.<br/>- `prompt=select_account` interromperá uma única experiência de sessão de acesso, fornecendo a experiência de seleção de contas, enumerando todas as contas em sessão ou qualquer conta lembrada ou uma opção para escolher usar uma conta diferente completamente.<br/> |
| `login_hint`  | opcional    | Pode ser usado para pré-preenchimento do nome de utilizador/endereço de endereço de e-mail da página de inscrição para o utilizador, se souber o seu nome de utilizador com antecedência. Muitas vezes as aplicações utilizam este parâmetro durante a reautorização, tendo já extraído o nome de utilizador de um pré-início de súmato utilizando a `preferred_username` reclamação.   |
| `domain_hint`  | opcional    | Se incluído, irá ignorar o processo de descoberta baseado em e-mail que o utilizador passa na página de entrada, levando a uma experiência de utilizador um pouco mais simplificada - por exemplo, enviando-os para o seu fornecedor de identidade federado. Muitas vezes as aplicações usam este parâmetro durante a reaudição, extraindo o `tid` de um início de súming anterior. Se o `tid` valor da reclamação `9188040d-6c67-4c5b-b112-36a304b66dad` for, deve utilizar `domain_hint=consumers` . Caso contrário, `domain_hint=organizations` utilize.  |
| `code_challenge`  | recomendado / necessário | Utilizado para garantir concessões de código de autorização através da Chave de Prova para Troca de Códigos (PKCE). Necessário se `code_challenge_method` estiver incluído. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). Isto agora é recomendado para todos os tipos de aplicações - aplicações nativas, SPAs e clientes confidenciais, como aplicações web. |
| `code_challenge_method` | recomendado / necessário | O método usado para codificar o `code_verifier` `code_challenge` parâmetro. Isto *deve* ser `S256` , mas a especificação permite a utilização de `plain` se por alguma razão o cliente não pode suportar SHA256. <br/><br/>Se excluído, `code_challenge` presume-se que é texto simples se `code_challenge` estiver incluído. A plataforma de identidade da Microsoft suporta ambos `plain` e `S256` . Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). Isto é necessário para [aplicações de página única usando o fluxo de código de autorização](reference-third-party-cookies-spas.md).|


Neste momento, o utilizador será convidado a introduzir as suas credenciais e a completar a autenticação. O ponto final da plataforma de identidade da Microsoft também garantirá que o utilizador consentiu nas permissões indicadas no `scope` parâmetro de consulta. Se o utilizador não tiver consentido em nenhuma dessas permissões, solicitará ao utilizador que consinta nas permissões necessárias. Detalhes de [permissões, consentimento e aplicações multi-inquilinos são fornecidos aqui.](v2-permissions-and-consent.md)

Assim que o utilizador autenticar e conceder o consentimento, o ponto final da plataforma de identidade da Microsoft devolverá uma resposta à sua aplicação no indicado `redirect_uri` , utilizando o método especificado no `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta bem sucedida `response_mode=query` parece:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parâmetro | Descrição  |
|-----------|--------------|
| `code` | O authorization_code que a aplicação solicitou. A aplicação pode usar o código de autorização para solicitar um token de acesso para o recurso alvo. Authorization_codes são de curta duração, normalmente expiram após cerca de 10 minutos. |
| `state` | Se um parâmetro de estado for incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se os valores do Estado no pedido e resposta são idênticos. |

Também pode receber um token de acesso e um sinal de identificação se solicitar um e tiver a subvenção implícita ativada no seu registo de inscrição.  Isto é por vezes referido como o "fluxo híbrido", e é usado por quadros como ASP.NET.

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para `redirect_uri` a aplicação para que a aplicação possa manuseá-las adequadamente:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição  |
|----------|------------------|
| `error`  | Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usado para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto final de autorização

A tabela seguinte descreve os vários códigos de erro que podem ser devolvidos no `error` parâmetro da resposta de erro.

| Código de Erro  | Descrição    | Ação do Cliente   |
|-------------|----------------|-----------------|
| `invalid_request` | Erro de protocolo, como um parâmetro exigido em falta. | Corrija e reenvia o pedido. Este é um erro de desenvolvimento tipicamente apanhado durante os testes iniciais. |
| `unauthorized_client` | O pedido do cliente não está autorizado a solicitar um código de autorização. | Este erro ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino AZure AD do utilizador. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| `access_denied`  | Proprietário de recursos negado consentimento  | A aplicação do cliente pode notificar o utilizador de que não pode prosseguir a menos que o utilizador consinta. |
| `unsupported_response_type` | O servidor de autorização não suporta o tipo de resposta no pedido. | Corrija e reenvia o pedido. Este é um erro de desenvolvimento tipicamente apanhado durante os testes iniciais.  |
| `server_error`  | O servidor encontrou um erro inesperado.| Recandidutar o pedido. Estes erros podem resultar de condições temporárias. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada para um erro temporário. |
| `temporarily_unavailable`   | O servidor está temporariamente demasiado ocupado para lidar com o pedido. | Recandidutar o pedido. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a uma condição temporária. |
| `invalid_resource`  | O recurso alvo é inválido porque não existe, a Azure AD não pode encontrá-lo, ou não está corretamente configurado. | Este erro indica que o recurso, se existir, não foi configurado no arrendatário. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| `login_required` | Muitos ou nenhum utilizadores encontrados | O cliente solicitou a autenticação silenciosa , `prompt=none` mas um único utilizador não conseguiu encontrar. Isto pode significar que existem vários utilizadores ativos na sessão, ou nenhum utilizadores. Isto tem em conta o inquilino escolhido (por exemplo, se houver duas contas AD AZure ativas e uma conta Microsoft, e `consumers` for escolhida, a autenticação silenciosa funcionará). |
| `interaction_required` | O pedido requer interação do utilizador. | É necessário um passo ou consentimento de autenticação adicional. Re-julgar o pedido sem `prompt=none` . |

## <a name="request-an-access-token"></a>Pedir um token de acesso

Agora que adquiriu um authorization_code e foi-lhe concedida permissão pelo utilizador, pode resgatar o `code` para um `access_token` recurso desejado. Faça-o enviando um `POST` pedido para o ponto `/token` final:

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
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Tente executar este pedido no Carteiro! (Não se esqueça de substituir o `code` ) [ ![ Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parâmetro  | Obrigatório/opcional | Descrição     |
|------------|-------------------|----------------|
| `tenant`   | obrigatório   | O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos `common` `organizations` são, `consumers` e os identificadores de inquilinos. Para mais detalhes, consulte [o protocolo básico.](active-directory-v2-protocols.md#endpoints)  |
| `client_id` | obrigatório  | O ID da Aplicação (cliente) que o [portal Azure – Página de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuiu à sua app. |
| `grant_type` | obrigatório   | Deve ser `authorization_code` para o fluxo de código de autorização.   |
| `scope`      | opcional   | Uma lista de âmbitos separados pelo espaço. Os âmbitos devem ser todos de um único recurso, juntamente com os âmbitos OIDC `profile` (, `openid` . . `email` . Para obter uma explicação mais detalhada dos âmbitos, consulte [permissões, consentimento e âmbitos](v2-permissions-and-consent.md). Trata-se de uma extensão da Microsoft ao fluxo de código de autorização, destinado a permitir que as aplicações declarem o recurso para o qual pretendem o símbolo durante o resgate simbólico.|
| `code`          | obrigatório  | O authorization_code que adquiriu na primeira parte do fluxo. |
| `redirect_uri`  | obrigatório  | O mesmo valor redirect_uri que foi usado para adquirir o authorization_code. |
| `client_secret` | necessário para aplicações confidenciais da web | O segredo da aplicação que criou no portal de registo de aplicações para a sua aplicação. Não deve usar o segredo da aplicação numa aplicação nativa ou numa aplicação de página única porque client_secrets não podem ser armazenados de forma fiável em dispositivos ou páginas web. É necessário para aplicações web e APIs web, que têm a capacidade de armazenar o client_secret de forma segura no lado do servidor.  O segredo do cliente deve ser codificado por URL antes de ser enviado. Para obter mais informações sobre a codificação uri, consulte a [especificação de sintaxe genérica URI](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | recomendado  | O mesmo code_verifier que foi usado para obter o authorization_code. Exigido se o PKCE foi utilizado no pedido de concessão de código de autorização. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

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
| `access_token`  | O sinal de acesso solicitado. A aplicação pode usar este token para autenticar o recurso seguro, como uma API web.  |
| `token_type`    | Indica o valor do tipo símbolo. O único tipo que a Azure AD suporta é o Portador |
| `expires_in`    | Quanto tempo o token de acesso é válido (em segundos). |
| `scope`         | Os âmbitos pelos access_token são válidos. Opcional - isto não é normal, e se omitido o token será para os âmbitos solicitados na parte inicial do fluxo. |
| `refresh_token` | Um token de atualização OAuth 2.0. A aplicação pode usar este token de acesso adicional após o fim do token de acesso atual. Refresh_tokens são de longa duração e podem ser usadas para manter o acesso aos recursos por longos períodos de tempo. Para obter mais detalhes sobre a resta finalção de um token de acesso, consulte a [secção abaixo](#refresh-the-access-token). <br> **Nota:** Só for fornecida se `offline_access` o âmbito de aplicação for solicitado. |
| `id_token`      | Um token web JSON (JWT). A aplicação pode descodificar os segmentos deste token para solicitar informações sobre o utilizador que se inscreveu. A aplicação pode cache os valores e exibi-los, e os clientes confidenciais podem usá-lo para autorização. Para mais informações sobre id_tokens, consulte o [`id_token reference`](id-tokens.md) . <br> **Nota:** Só for fornecida se `openid` o âmbito de aplicação for solicitado. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro serão como:

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
| `error`       | Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usado para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |
| `error_codes` | Uma lista de códigos de erro específicos do STS que podem ajudar no diagnóstico.  |
| `timestamp`   | A hora em que ocorreu o erro. |
| `trace_id`    | Um identificador único para o pedido que pode ajudar no diagnóstico. |
| `correlation_id` | Um identificador único para o pedido que pode ajudar no diagnóstico em todos os componentes. |

### <a name="error-codes-for-token-endpoint-errors"></a>Códigos de erro para erros de ponto final simbólicos

| Código de Erro         | Descrição        | Ação do Cliente    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Erro de protocolo, como um parâmetro exigido em falta. | Corrija o pedido ou registo de aplicações e reenvia o pedido   |
| `invalid_grant`    | O código de autorização ou verificador de código PKCE é inválido ou expirou. | Experimente um novo pedido para o `/authorize` ponto final e verifique se o parâmetro code_verifier estava correto.  |
| `unauthorized_client` | O cliente autenticado não está autorizado a usar este tipo de concessão de autorização. | Isto ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino AZure AD do utilizador. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| `invalid_client` | A autenticação do cliente falhou.  | As credenciais do cliente não são válidas. Para corrigir, o administrador de aplicação atualiza as credenciais.   |
| `unsupported_grant_type` | O servidor de autorização não suporta o tipo de concessão de autorização. | Altere o tipo de subvenção no pedido. Este tipo de erro só deve ocorrer durante o desenvolvimento e ser detetado durante os testes iniciais. |
| `invalid_resource` | O recurso alvo é inválido porque não existe, a Azure AD não pode encontrá-lo, ou não está corretamente configurado. | Isto indica que o recurso, se existir, não foi configurado no arrendatário. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD.  |
| `interaction_required` | Não standard, como a especificação OIDC exige apenas no `/authorize` ponto final. O pedido requer interação do utilizador. Por exemplo, é necessário um passo adicional de autenticação. | Reda o `/authorize` pedido com os mesmos âmbitos. |
| `temporarily_unavailable` | O servidor está temporariamente demasiado ocupado para lidar com o pedido. | Recandidutar o pedido depois de um pequeno atraso. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a uma condição temporária. |
|`consent_required` | O pedido requer o consentimento do utilizador. Este erro não é padrão, uma vez que normalmente só é devolvido no `/authorize` ponto final de acordo com as especificações do OIDC. Devolvido quando `scope` um parâmetro foi usado no fluxo de resgate de código que a aplicação do cliente não tem permissão para solicitar.  | O cliente deve enviar o utilizador de volta para o `/authorize` ponto final com o âmbito correto para desencadear o consentimento. |
|`invalid_scope` | O âmbito solicitado pela app é inválido.  | Atualizar o valor do parâmetro de âmbito no pedido de autenticação para um valor válido. |

> [!NOTE]
> As aplicações de página única podem receber um `invalid_request` erro que indique que o resgate de token de origem cruzada só é permitido para o tipo de cliente 'Aplicação de página única'.  Isto indica que o reorientação uri utilizado para solicitar o token não foi marcado como um `spa` URI de redirecionamento.  Reveja as etapas de registo de [pedidos](#redirect-uri-setup-required-for-single-page-apps) sobre como ativar este fluxo.

## <a name="use-the-access-token"></a>Use o token de acesso

Agora que adquiriu com sucesso `access_token` um, pode usar o token em pedidos para web APIs, incluindo-o no `Authorization` cabeçalho:

> [!TIP]
> Execute este pedido no Carteiro! (Substitua o `Authorization` cabeçalho primeiro) [ ![ Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Refresque o token de acesso

Access_tokens são de curta duração, e você deve refresca-los depois que expiram para continuar a aceder aos recursos. Pode fazê-lo submetendo outro `POST` pedido ao `/token` ponto final, desta vez fornecendo o `refresh_token` em vez do `code` .  Os tokens de atualização são válidos para todas as permissões para as quais o seu cliente já recebeu o consentimento - assim, um token de atualização emitido num pedido `scope=mail.read` de pedido pode ser usado para solicitar um novo token de acesso para `scope=api://contoso.com/api/UseResource` .

Refresh tokens para aplicativos web e aplicativos nativos não têm vidas especificadas. Tipicamente, as vidas de fichas de atualização são relativamente longas. No entanto, em alguns casos, os tokens de atualização expiram, são revogados, ou carecem de privilégios suficientes para a ação desejada. A sua aplicação precisa de esperar e lidar com [erros devolvidos pelo ponto final de emissão](#error-codes-for-token-endpoint-errors) de token corretamente. No entanto, as aplicações de uma página única recebem um token com uma vida útil de 24 horas, exigindo uma nova autenticação todos os dias.  Isto pode ser feito silenciosamente num iframe quando os cookies de 3ª parte estão ativados, mas deve ser feito em um quadro de nível superior (ou navegação de página completa ou um pop-up) em navegadores sem cookies de 3ª parte, como o Safari.

Embora os tokens de atualização não sejam revogados quando usados para adquirir novos tokens de acesso, espera-se que deite fora o velho token da atualização. A [especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-6) diz: "O servidor de autorização MAY emite um novo token de atualização, caso em que o cliente DEVE descartar o token de atualização antiga e substituí-lo pelo novo token de atualização. O servidor de autorização pode revogar o velho token da atualização depois de emitir um novo token de atualização para o cliente."

>[!IMPORTANT]
> Para tokens de atualização enviados para um URI de redirecionamento registado como `spa` , o token de atualização expirará após 24 horas. Tokens de atualização adicionais adquiridos usando o token de atualização inicial irão suportar esse tempo de validade, por isso as aplicações devem estar preparadas para recortar o fluxo de código de autorização usando uma autenticação interativa para obter um novo token de atualização a cada 24 horas. Os utilizadores não têm de introduzir as suas credenciais, e normalmente nem sequer vêem nenhum UX, apenas uma recarga da sua aplicação - mas o navegador deve visitar a página de login num quadro de nível superior para ver a sessão de login.  Isto deve-se a funcionalidades de [privacidade nos navegadores que bloqueiam cookies de terceiros.](reference-third-party-cookies-spas.md)

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
> Tente executar este pedido no Carteiro! (Não se esqueça de substituir o `refresh_token` ) [ ![ Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| Parâmetro     | Tipo           | Descrição        |
|---------------|----------------|--------------------|
| `tenant`        | obrigatório     | O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos `common` `organizations` são, `consumers` e os identificadores de inquilinos. Para mais detalhes, consulte [o protocolo básico.](active-directory-v2-protocols.md#endpoints)   |
| `client_id`     | obrigatório    | O **ID da Aplicação (cliente)** que o [portal Azure – Experiência de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuído à sua app. |
| `grant_type`    | obrigatório    | Deve ser `refresh_token` para esta parte do fluxo de código de autorização. |
| `scope`         | obrigatório    | Uma lista de âmbitos separados pelo espaço. Os âmbitos solicitados nesta perna devem ser equivalentes ou a um subconjunto dos âmbitos solicitados na perna de pedido original authorization_code. Se os âmbitos especificados neste pedido abrangerem vários servidores de recursos, então o ponto final da plataforma de identidade da Microsoft devolverá um token para o recurso especificado no primeiro âmbito. Para obter uma explicação mais detalhada dos âmbitos, consulte [permissões, consentimento e âmbitos](v2-permissions-and-consent.md). |
| `refresh_token` | obrigatório    | O refresh_token que adquiriu na segunda parte do fluxo. |
| `client_secret` | necessário para aplicações web | O segredo da aplicação que criou no portal de registo de aplicações para a sua aplicação. Não deve ser utilizado numa aplicação nativa, porque client_secrets não podem ser armazenados de forma fiável em dispositivos. É necessário para aplicações web e APIs web, que têm a capacidade de armazenar o client_secret de forma segura no lado do servidor. Este segredo tem de ser codificado por URL. Para obter mais informações, consulte a [especificação de sintaxe genérica URI](https://tools.ietf.org/html/rfc3986#page-12). |

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
| `access_token`  | O sinal de acesso solicitado. A aplicação pode usar este token para autenticar o recurso seguro, como uma API web. |
| `token_type`    | Indica o valor do tipo símbolo. O único tipo que a Azure AD suporta é o Portador |
| `expires_in`    | Quanto tempo o token de acesso é válido (em segundos).   |
| `scope`         | Os âmbitos pelos access_token são válidos.    |
| `refresh_token` | Um novo token de atualização OAuth 2.0. Deverá substituir o antigo token de atualização por este token de atualização recentemente adquirido para garantir que os seus tokens de atualização permaneçam válidos o máximo de tempo possível. <br> **Nota:** Só for fornecida se `offline_access` o âmbito de aplicação for solicitado.|
| `id_token`      | Um JSON Web Token (JWT) não assinado. A aplicação pode descodificar os segmentos deste token para solicitar informações sobre o utilizador que se inscreveu. A aplicação pode cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. Para mais informações sobre id_tokens, consulte o [`id_token reference`](id-tokens.md) . <br> **Nota:** Só for fornecida se `openid` o âmbito de aplicação for solicitado. |

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
| `error`           | Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usado para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação.           |
| `error_codes` |Uma lista de códigos de erro específicos do STS que podem ajudar no diagnóstico. |
| `timestamp` | A hora em que ocorreu o erro. |
| `trace_id` | Um identificador único para o pedido que pode ajudar no diagnóstico. |
| `correlation_id` | Um identificador único para o pedido que pode ajudar no diagnóstico em todos os componentes. |

Para obter uma descrição dos códigos de erro e da ação recomendada do cliente, consulte [códigos de erro para erros de ponto final simbólicos](#error-codes-for-token-endpoint-errors).
