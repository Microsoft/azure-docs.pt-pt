---
title: Fluxo de código de autorização de OAuth do Azure AD v2.0 | Documentos da Microsoft
description: Criando aplicativos web com implementação do Azure AD do protocolo de autenticação OAuth 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb2329a2a67e2e54084d2289cb8cb1a18663b329
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593680"
---
# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>Protocolos de v2.0 - fluxo de código de autorização de OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

A concessão de código de autorização de OAuth 2.0 pode ser utilizada nas aplicações que são instaladas num dispositivo para obter acesso a recursos protegidos, como as APIs web. Usando a implementação do v2.0 de modelo de aplicação do OAuth 2.0, pode adicionar a iniciar sessão e a API de acesso às suas aplicações móveis e de Desktops. Este guia é independente de idioma e descreve como enviar e receber mensagens HTTP sem utilizar qualquer um da [bibliotecas de autenticação de código-fonte aberto do Azure](active-directory-authentication-libraries.md).

> [!NOTE]
> Nem todos os cenários do Azure Active Directory e funcionalidades são compatíveis com o ponto final v2.0. Para determinar se deve utilizar o ponto final v2.0, leia sobre [v2.0 limitações](active-directory-v2-limitations.md).

O fluxo de código de autorização de OAuth 2.0 é descrito em [secção 4.1 da especificação de OAuth 2.0](https://tools.ietf.org/html/rfc6749). Ele é usado para realizar a autenticação e autorização na maioria dos tipos de aplicações, incluindo [aplicações web](v2-app-types.md#web-apps) e [instalado nativamente aplicações](v2-app-types.md#mobile-and-native-apps). O fluxo permite que aplicações em segurança adquirir access_tokens que pode ser utilizado para aceder aos recursos protegidos pelo ponto final v2.0. 

## <a name="protocol-diagram"></a>Diagrama de protocolo

Num alto nível, o fluxo de autenticação completa para uma aplicação nativa/móvel parece um pouco com isto:

![Fluxo de código de autenticação do OAuth](./media/v2-oauth2-auth-code-flow/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Pedir um código de autorização

O fluxo de código de autorização começa com o cliente que direciona o utilizador para o `/authorize` ponto final. Este pedido, o cliente indica as permissões necessárias para adquirir do usuário:

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
> Clique na ligação abaixo para executar este pedido! Depois de iniciar sessão, o browser deve ser redirecionado para `https://localhost/myapp/` com um `code` na barra de endereço.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parâmetro    | Obrigatório/opcional | Descrição |
|--------------|-------------|--------------|
| `tenant`    | obrigatório    | O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de inquilinos. Para obter mais detalhes, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | obrigatório    | ID de aplicação (cliente) que o portal de registo ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) ou **registos das aplicações (pré-visualização)** experiência no portal do Azure, atribuído a sua aplicação.  |
| `response_type` | obrigatório    | Tem de incluir `code` para o fluxo de código de autorização.       |
| `redirect_uri`  | Recomendado | O redirect_uri da sua aplicação, onde as respostas podem ser enviadas e recebidas pela sua aplicação. Ele deve corresponder exatamente um dos redirect_uris registado no portal, exceto pelo fato tem de ser codificados de url. Para aplicações de dispositivos móveis e nativas, deve usar o valor predefinido de `https://login.microsoftonline.com/common/oauth2/nativeclient`.   |
| `scope`  | obrigatório    | Uma lista separada por espaço de [âmbitos](v2-permissions-and-consent.md) que pretende que o utilizador para autorizar. |
| `response_mode`   | Recomendado | Especifica o método que deve ser utilizado para enviar a cópia de token resultante à sua aplicação. Pode ser um dos seguintes procedimentos:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` Fornece o código como um parâmetro de cadeia de caracteres de consulta no seu URI de redirecionamento. Se estiver solicitando um token de ID com o fluxo implícito, é possível utilizar `query` conforme especificado no [especificação de OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Se estiver solicitando apenas do código, pode usar `query`, `fragment`, ou `form_post`. `form_post` executa uma POSTAGEM que contém o código para o seu URI de redirecionamento. Para mais informações, veja [protocolo OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | Recomendado | Um valor incluído no pedido que também vai ser devolvido na resposta de token. Pode ser uma cadeia de caracteres de qualquer conteúdo que desejar. Um valor exclusivo gerado aleatoriamente é normalmente utilizado para [impedir ataques de falsificação de solicitação](https://tools.ietf.org/html/rfc6749#section-10.12). O valor pode também codificar a informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, como a página ou a vista estivessem na. |
| `prompt`  | opcional    | Indica o tipo de interação do utilizador que é necessário. São os únicos valores válidos neste momento `login`, `none`, e `consent`.<br/><br/>- `prompt=login` irá forçar o utilizador para introduzir as respetivas credenciais na solicitação, eliminando-início de sessão único.<br/>- `prompt=none` é o oposto – ele garantirá que não é apresentada ao utilizador com qualquer linha de comandos interativa tipo. Se o pedido não é possível concluir silenciosamente por meio de início de sessão único, o ponto final v2.0 retornará um `interaction_required` erro.<br/>- `prompt=consent` irá acionar a caixa de diálogo de consentimento do OAuth depois do utilizador inicia sessão, solicitando que o usuário para conceder permissões à aplicação. |
| `login_hint`  | opcional    | Pode ser usada para preencher previamente o campo de endereço de e-mail/nome de utilizador da página início de sessão do utilizador, se souber que o respetivo nome de utilizador antes do tempo. Aplicações, muitas vezes, irão utilizar este parâmetro durante a reautenticação, já após extrair o nome de utilizador de um anterior início de sessão com o `preferred_username` de afirmação.   |
| `domain_hint`  | opcional    | Pode ser um dos `consumers` ou `organizations`.<br/><br/>Se incluído, ele irá ignorar o processo de descoberta baseada em e-mail que o utilizador passa pela página v2.0 início de sessão, que leva a uma experiência de usuário ligeiramente mais simplificada. Muitas vezes, aplicações, irão utilizar este parâmetro durante a reautenticação, extraindo o `tid` de um anterior início de sessão. Se o `tid` é o valor de afirmação `9188040d-6c67-4c5b-b112-36a304b66dad`, deve usar `domain_hint=consumers`. Caso contrário, utilize `domain_hint=organizations`.  |
| `code_challenge_method` | opcional    | O método utilizado para codificar a `code_verifier` para o `code_challenge` parâmetro. Pode ser um dos seguintes valores:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Se excluído, `code_challenge` é considerado como texto sem formatação se `code_challenge` está incluído. Azure AAD v2.0 oferece suporte a ambos `plain` e `S256`. Para obter mais informações, consulte a [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | opcional | Utilizado para proteger concessões de código de autorização por meio da chave de prova para código Exchange (PKCE) de um cliente nativo. Se necessário `code_challenge_method` está incluído. Para obter mais informações, consulte a [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

Neste momento, o usuário será solicitado para introduzir as respetivas credenciais e concluir a autenticação. O ponto final v2.0 também vai assegurar que o utilizador consentiu as permissões indicadas no `scope` parâmetro de consulta. Se o utilizador não consentiu a qualquer uma dessas permissões, pedirá ao utilizador para autorizar as permissões necessárias. Detalhes da [permissões e consentimento e aplicações multi-inquilino são fornecidas aqui](v2-permissions-and-consent.md).

Depois do utilizador autentica e concede o consentimento, o ponto final v2.0 irá devolver uma resposta à sua aplicação no indicado `redirect_uri`, utilizando o método especificado no `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta com êxito

Uma resposta com êxito utilizando `response_mode=query` se parece com:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parâmetro | Descrição  |
|-----------|--------------|
| `code` | Authorization_code que solicitou a aplicação. A aplicação pode utilizar o código de autorização para pedir um token de acesso para o recurso de destino. Authorization_codes vida curta, normalmente, esta expira após cerca de 10 minutos. |
| `state` | Se um parâmetro de estado está incluído na solicitação, o mesmo valor deve aparecer na resposta. A aplicação deve verificar que os valores de estado no pedido e resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

Respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação pode processar corretamente:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição  |
|----------|------------------|
| `error`  | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para reagir a erros. |
| `error_description` | Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação do desenvolvedor. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto final de autorização

A tabela seguinte descreve os vários códigos de erro que podem ser devolvidos no `error` parâmetro da resposta de erro.

| Código de Erro  | Descrição    | Ação de cliente   |
|-------------|----------------|-----------------|
| `invalid_request` | Erro de protocolo, como um parâmetro necessário em falta. | Corrija e submeta novamente o pedido. Este é um erro de desenvolvimento normalmente capturado durante o teste inicial. |
| `unauthorized_client` | A aplicação de cliente não tem permissão para pedir um código de autorização. | Este erro ocorre normalmente quando a aplicação de cliente não está registrada no Azure AD ou não for adicionada ao inquilino do Azure AD do utilizador. A aplicação pode pedir ao utilizador com instruções para a instalação do aplicativo e adicioná-lo para o Azure AD. |
| `access_denied`  | Proprietário do recurso negado consentimento  | A aplicação cliente pode notificar o utilizador que não é possível continuar a não ser que o utilizador permitir. |
| `unsupported_response_type` | O servidor de autorização não suporta o tipo de resposta no pedido. | Corrija e submeta novamente o pedido. Este é o desenvolvimento de um erro, normalmente, é capturado durante o teste inicial.  |
| `server_error`  | O servidor encontrou um erro inesperado.| Repita o pedido. Estes erros podem resultar de condições temporárias. A aplicação cliente pode explicar ao usuário que a sua resposta é atrasada para um erro temporário. |
| `temporarily_unavailable`   | O servidor está temporariamente demasiado ocupado para processar o pedido. | Repita o pedido. A aplicação cliente pode explicar ao usuário que a sua resposta está atrasada devido a uma condição temporária. |
| `invalid_resource`  | O recurso de destino é inválido porque não existe, do Azure AD não é possível encontrá-lo ou não está corretamente configurado. | Este erro indica que o recurso, se existir, não foi configurado no inquilino. A aplicação pode pedir ao utilizador com instruções para a instalação do aplicativo e adicioná-lo para o Azure AD. |
| `login_required` | Demasiados ou não foram encontrados utilizadores | O cliente solicitou a autenticação silenciosa (`prompt=none`), mas não foi encontrado um único utilizador. Isso pode significar que existem vários utilizadores ativos na sessão ou não existem utilizadores. Isso leva em conta o inquilino escolhido (por exemplo, se existem 2 contas do AAD Active Directory e uma MSA, e `consumers` for escolhida, funcionará autenticação silenciosa). |
| `interaction_required` | O pedido requer interação do utilizador. | É necessário um passo de autenticação adicional ou consentimento. Repita o pedido sem `prompt=none`. |

## <a name="request-an-access-token"></a>Pedir um token de acesso

Agora que comprou um authorization_code e tenha sido concedida permissão pelo utilizador, poderá resgatá a `code` para um `access_token` para o recurso pretendido. Faça isso enviando um `POST` pedido para o `/token` ponto final:

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
> Experimente executar este pedido no Postman! (Não se esqueça de substituir a `code`) [ ![executar no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Parâmetro  | Obrigatório/opcional | Descrição     |
|------------|-------------------|----------------|
| `tenant`   | obrigatório   | O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de inquilinos. Para obter mais detalhes, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | obrigatório  | ID de aplicação que o portal de registo ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuído a sua aplicação. |
| `grant_type` | obrigatório   | Tem de ser `authorization_code` para o fluxo de código de autorização.   |
| `scope`      | obrigatório   | Uma lista de âmbitos separadas por espaços. Os âmbitos solicitados neste leg tem de ser equivalente a ou um subconjunto dos âmbitos solicitada no leg primeiro. Se os âmbitos especificados neste pedido abrangem vários servidores de recursos, o ponto final v2.0 irá devolver um token para o recurso especificado no âmbito da primeira. Para obter uma explicação mais detalhada de âmbitos, consulte [permissões e consentimento e âmbitos](v2-permissions-and-consent.md). |
| `code`          | obrigatório  | Authorization_code que obteve no leg primeiro do fluxo. |
| `redirect_uri`  | obrigatório  | O valor de redirect_uri mesmo que foi utilizado para adquirir o authorization_code. |
| `client_secret` | necessária para as aplicações web | O segredo de aplicação que criou no portal de registo de aplicação para a sua aplicação. Não deve ser utilizada num aplicativo nativo, porque client_secrets não podem ser armazenados com confiança nos dispositivos. É necessário para aplicações web e APIs, que têm a capacidade de armazenar o client_secret em segurança no lado do servidor web.  O segredo do cliente tem de ser codificados de URL antes de serem enviados.  |
| `code_verifier` | opcional  | O code_verifier mesmo que foi utilizado para obter o authorization_code. Necessário se PKCE foi utilizada no pedido de concessão do código de autorização. Para obter mais informações, consulte a [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Resposta com êxito

Uma resposta com êxito de token terá o seguinte aspeto:

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
| `access_token`  | O token de acesso solicitado. A aplicação pode utilizar este token para autenticar para o recurso protegido, como uma API web.  |
| `token_type`    | Indica o valor de tipo de token. O único tipo que o Azure AD suporta é portador |
|` expires_in`    | O tempo que o token de acesso é válido (em segundos). |
| `scope`         | Os âmbitos que o access_token é válido para. |
| `refresh_token` | Um token de atualização de OAuth 2.0. A aplicação pode utilizar este token adquirir os tokens de acesso adicionais depois do token de acesso atual expira. Refresh_tokens são vida longa e pode ser utilizado para manter o acesso aos recursos por longos períodos de tempo. Para obter mais detalhes sobre como atualizar um token de acesso, consulte a [secção abaixo](#refresh-the-access-token). <br> **Nota:** Só será fornecido se `offline_access` âmbito foi pedido. |
| `id_token`      | Um JSON Web tokens (JWT). A aplicação pode decodificar os segmentos deste token solicite informações sobre o utilizador que iniciou sessão. A aplicação pode armazenar em cache os valores e exibi-los, mas não deverá confiar nos mesmos para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, consulte a [ `id_token reference` ](id-tokens.md). <br> **Nota:** Só será fornecido se `openid` âmbito foi pedido. |

### <a name="error-response"></a>Resposta de erro

Respostas de erro terá o seguinte aspeto:

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
| `error`       | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para reagir a erros. |
| `error_description` | Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação do desenvolvedor. |
| `error_codes` | Uma lista de códigos de erro do STS específicas que podem ajudar no diagnóstico.  |
| `timestamp`   | A hora em que ocorreu o erro. |
| `trace_id`    | Um identificador exclusivo para o pedido que pode ajudar no diagnóstico. |
| `correlation_id` | Um identificador exclusivo para o pedido que pode ajudar no diagnóstico em componentes. |

### <a name="error-codes-for-token-endpoint-errors"></a>Códigos de erro para erros de ponto final do token

| Código de Erro         | Descrição        | Ação de cliente    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Erro de protocolo, como um parâmetro necessário em falta. | Corrigir e submeta novamente o pedido   |
| `invalid_grant`    | O código de autorização ou o Verificador de código PKCE é inválido ou expirou. | Experimente um novo pedido para o `/authorize` ponto final e certifique-se de que o parâmetro de code_verifier estava correto.  |
| `unauthorized_client` | O cliente autenticado não está autorizado a utilizar este tipo de concessão de autorização. | Isso geralmente ocorre quando o aplicativo cliente não está registado no Azure AD ou não for adicionado ao inquilino do Azure AD do utilizador. A aplicação pode pedir ao utilizador com instruções para a instalação do aplicativo e adicioná-lo para o Azure AD. |
| `invalid_client` | Falha na autenticação de cliente.  | As credenciais de cliente não são válidas. Para corrigir o problema, o administrador do aplicativo atualiza as credenciais.   |
| `unsupported_grant_type` | O servidor de autorização não suporta o tipo de concessão de autorização. | Altere o tipo de concessão no pedido. Este tipo de erro deve ocorrer apenas durante o desenvolvimento e ser detetado durante o teste inicial. |
| `invalid_resource` | O recurso de destino é inválido porque não existe, do Azure AD não é possível encontrá-lo ou não está corretamente configurado. | Isto indica que o recurso, se existir, não foi configurado no inquilino. A aplicação pode pedir ao utilizador com instruções para a instalação do aplicativo e adicioná-lo para o Azure AD.  |
| `interaction_required` | O pedido requer interação do utilizador. Por exemplo, um passo de autenticação adicional é necessário. | Repita o pedido com o mesmo recurso.  |
| `temporarily_unavailable` | O servidor está temporariamente demasiado ocupado para processar o pedido. | Repita o pedido. A aplicação cliente pode explicar ao usuário que a sua resposta está atrasada devido a uma condição temporária. |

## <a name="use-the-access-token"></a>Utilizar o token de acesso

Agora que tive adquirido com sucesso um `access_token`, pode utilizar o token em pedidos para as APIs da Web, incluindo-na `Authorization` cabeçalho:

> [!TIP]
> Execute este pedido no Postman! (Substitua a `Authorization` cabeçalho primeiro) [ ![executar no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Atualizar o token de acesso

Access_tokens tiverem vida curta e deve atualizá-los depois que ocorra para continuar a aceder a recursos. Pode fazê-lo ao submeter outro `POST` pedido para o `/token` ponto final, desta vez, desde que o `refresh_token` em vez do `code`.  Tokens de atualização são válidas para todas as permissões que o cliente já recebeu o consentimento para - portanto, um token de atualização emitido um pedido para `scope=mail.read` pode ser utilizado para pedir um novo token de acesso para `scope=api://contoso.com/api/UseResource`.  

Atualizar tokens não têm tempo de vida especificado. Normalmente, os tempos de vida de tokens de atualização são relativamente longos. No entanto, em alguns casos, tokens de atualização expirarem, são revogados ou não têm privilégios suficientes para a ação desejada. Seu aplicativo precisa esperar e manipular [erros devolvidos pelo ponto de final de emissão de token](#error-codes-for-token-endpoint-errors) corretamente.  Tenha em atenção que os tokens de atualização não são revogados quando utilizados para adquirir novos tokens de acesso. 

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Experimente executar este pedido no Postman! (Não se esqueça de substituir a `refresh_token`) [ ![executar no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Parâmetro     |                | Descrição        |
|---------------|----------------|--------------------|
| `tenant`        | obrigatório     | O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de inquilinos. Para obter mais detalhes, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | obrigatório    | O **ID da aplicação (cliente)** que o portal de registo de aplicação ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) ou **registos de aplicações (pré-visualização)** tem experiência no portal do Azure atribuído a sua aplicação.  |
| `grant_type`    | obrigatório    | Tem de ser `refresh_token` para este vertente do fluxo de código de autorização. |
| `scope`         | obrigatório    | Uma lista de âmbitos separadas por espaços. Os âmbitos solicitados neste leg tem de ser equivalente a ou um subconjunto dos âmbitos solicitada no leg de pedido de authorization_code original. Se os âmbitos especificados neste pedido abrangem vários servidores de recursos, o ponto final v2.0 irá devolver um token para o recurso especificado no âmbito da primeira. Para obter uma explicação mais detalhada de âmbitos, consulte [permissões e consentimento e âmbitos](v2-permissions-and-consent.md). |
| `refresh_token` | obrigatório    | Refresh_token que obteve no segundo leg do fluxo. |
| `redirect_uri`  | obrigatório    | O valor de redirect_uri mesmo que foi utilizado para adquirir o authorization_code. |
| `client_secret` | necessária para as aplicações web | O segredo de aplicação que criou no portal de registo de aplicação para a sua aplicação. Não deve ser utilizada num aplicativo nativo, porque client_secrets não podem ser armazenados com confiança nos dispositivos. É necessário para aplicações web e APIs, que têm a capacidade de armazenar o client_secret em segurança no lado do servidor web.                                                                                                                                                    |

#### <a name="successful-response"></a>Resposta com êxito

Uma resposta com êxito de token terá o seguinte aspeto:

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
| `access_token`  | O token de acesso solicitado. A aplicação pode utilizar este token para autenticar para o recurso protegido, como uma API web. |
| `token_type`    | Indica o valor de tipo de token. O único tipo que o Azure AD suporta é portador |
| `expires_in`    | O tempo que o token de acesso é válido (em segundos).   |
| `scope`         | Os âmbitos que o access_token é válido para.    |
| `refresh_token` | Um novo token de atualização de OAuth 2.0. Deve substituir o token de atualização antigo com este token de atualização recentemente adquirida para garantir que seus tokens de atualização mantêm-se válidas durante mais tempo. <br> **Nota:** Só será fornecido se `offline_access` âmbito foi pedido.|
| `id_token`      | Um não assinados JSON Web Token (JWT). A aplicação pode decodificar os segmentos deste token solicite informações sobre o utilizador que iniciou sessão. A aplicação pode armazenar em cache os valores e exibi-los, mas não deverá confiar nos mesmos para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, consulte a [ `id_token reference` ](id-tokens.md). <br> **Nota:** Só será fornecido se `openid` âmbito foi pedido. |

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
| `error`           | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para reagir a erros. |
| `error_description` | Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação do desenvolvedor.           |
| `error_codes` |Uma lista de códigos de erro do STS específicas que podem ajudar no diagnóstico. |
| `timestamp` | A hora em que ocorreu o erro. |
| `trace_id` | Um identificador exclusivo para o pedido que pode ajudar no diagnóstico. |
| `correlation_id` | Um identificador exclusivo para o pedido que pode ajudar no diagnóstico em componentes. |

Para obter uma descrição dos códigos de erro e a ação de cliente recomendado, consulte [códigos de erro para erros de ponto final do token](#error-codes-for-token-endpoint-errors).
