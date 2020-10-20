---
title: Fluxo de código de autorização - Azure Ative Directy B2C / Microsoft Docs
description: Saiba como construir aplicações web utilizando o protocolo de autenticação AZure AD B2C e OpenID Connect.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 9ae5632f2495ac5916ac8c86666e973c34d1b789
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215234"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Fluxo de código de autorização OAuth 2.0 no Azure Ative Directory B2C

Pode utilizar o código de autorização OAuth 2.0 em aplicações instaladas num dispositivo para ter acesso a recursos protegidos, como APIs web. Ao utilizar a implementação do OAuth 2.0 do Azure Ative Directory (Azure AD B2C), pode adicionar tarefas de inscrição, inscrição e outras tarefas de gestão de identidade às suas aplicações de página única, móveis e desktop. Este artigo é independente da linguagem. No artigo, descrevemos como enviar e receber mensagens HTTP sem utilizar bibliotecas de código aberto. Quando possível, recomendamos que utilize as Bibliotecas de Autenticação microsoft suportadas (MSAL). Veja as aplicações de [amostra que utilizam o MSAL.](code-samples.md)

O fluxo de código de autorização OAuth 2.0 é descrito na [secção 4.1 da especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749). Pode usá-lo para autenticação e autorização na maioria [dos tipos de aplicações,](application-types.md)incluindo aplicações web, aplicações de página única e aplicações instaladas nativamente. Pode utilizar o fluxo de código de autorização OAuth 2.0 para adquirir de forma segura fichas de acesso e fichas de atualização para as suas aplicações, que podem ser usadas para aceder a recursos que são protegidos por um [servidor de autorização.](protocols-overview.md)  O token de atualização permite ao cliente adquirir novos tokens de acesso (e atualização) uma vez que o token de acesso expira, normalmente após uma hora.

<!-- This article focuses on the **public clients** OAuth 2.0 authorization code flow. A public client is any client application that cannot be trusted to securely maintain the integrity of a secret password. This includes single-page applications, mobile apps, desktop applications, and essentially any application that runs on a device and needs to get access tokens. -->

> [!NOTE]
> Para adicionar a gestão de identidade a uma aplicação web utilizando o Azure AD B2C, utilize [o OpenID Connect](openid-connect.md) em vez de OAuth 2.0.

O Azure AD B2C alarga os fluxos padrão OAuth 2.0 para fazer mais do que simples autenticação e autorização. Introduz o fluxo do [utilizador.](user-flow-overview.md) Com os fluxos do utilizador, pode utilizar o OAuth 2.0 para adicionar experiências de utilizador à sua aplicação, tais como inscrição, inscrição e gestão de perfis. Os fornecedores de identidade que utilizam o protocolo OAuth 2.0 incluem [Amazon](identity-provider-amazon.md), [Azure Ative Directory](identity-provider-azure-ad-single-tenant.md), [Facebook,](identity-provider-facebook.md) [GitHub,](identity-provider-github.md) [Google](identity-provider-google.md)e [LinkedIn.](identity-provider-linkedin.md)

Para experimentar os pedidos HTTP neste artigo:

1. `{tenant}`Substitua-o pelo nome do seu inquilino Azure AD B2C.
1. `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`Substitua-a pelo ID da aplicação de uma aplicação que já se registou no seu inquilino Azure AD B2C.
1. `{policy}`Substitua-o pelo nome de uma apólice que criou no seu inquilino, por `b2c_1_sign_in` exemplo.

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Redirecionar configuração URI necessária para aplicações de uma só página

O fluxo de código de autorização para aplicações de página única requer alguma configuração adicional.  Siga as instruções para [criar a sua aplicação de uma página](tutorial-register-spa.md) para marcar corretamente o seu URI de redirecionamento, conforme ativado para o CORS. Para atualizar um URI de redirecionamento existente para ativar o CORS, abra o editor manifesto e desabrove o `type` campo para o seu URI de redirecionamento para a `spa` `replyUrlsWithType` secção. Também pode clicar no URI de redirecionamento na secção "Web" do separador Autenticação e selecionar os URIs que pretende migrar para utilizar o fluxo de código de autorização.

O `spa` tipo de redirecionamento é retrocompatível com o fluxo implícito. As aplicações que utilizam atualmente o fluxo implícito para obter fichas podem mover-se para o `spa` tipo URI de redirecionamento sem problemas e continuar a usar o fluxo implícito.

Se tentar utilizar o fluxo de código de autorização e ver este erro:

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

Em seguida, precisa de visitar o registo da sua aplicação e atualizar o URI de redirecionamento para que a sua aplicação `spa` escreva.

## <a name="1-get-an-authorization-code"></a>1. Obter um código de autorização
O fluxo de código de autorização começa com o cliente a direcionar o utilizador para o `/authorize` ponto final. Esta é a parte interativa do fluxo, onde o utilizador toma medidas. Neste pedido, o cliente indica no `scope` parâmetro as permissões que necessita de adquirir ao utilizador. Os três exemplos seguintes (com quebras de linha para a legibilidade) utilizam cada um um fluxo de utilizador diferente.


```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
|{inquilino}| Necessário | Nome do seu inquilino Azure AD B2C|
| {política} | Necessário | O fluxo do utilizador a ser executado. Especifique o nome de um fluxo de utilizador que criou no seu inquilino Azure AD B2C. Por exemplo: `b2c_1_sign_in` `b2c_1_sign_up` , ou . `b2c_1_edit_profile` . |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal Azure.](https://portal.azure.com) |
| response_type |Necessário |O tipo de resposta, que deve incluir `code` para o fluxo de código de autorização. |
| redirect_uri |Necessário |O URI redirecionado da sua app, onde as respostas de autenticação são enviadas e recebidas pela sua app. Deve corresponder exatamente a um dos URIs de redirecionamento que registou no portal, exceto que deve estar codificado por URL. |
| scope |Necessário |Uma lista de âmbitos separados pelo espaço. Um valor único de âmbito indica ao Azure Ative Directory (Azure AD) ambas as permissões que estão a ser solicitadas. A utilização do ID do cliente como o âmbito indica que a sua aplicação precisa de um token de acesso que pode ser usado contra o seu próprio serviço ou a API web, representado pelo mesmo ID do cliente.  O `offline_access` âmbito indica que a sua aplicação precisa de um token de atualização para o acesso de longa duração aos recursos. Também pode utilizar o `openid` âmbito para solicitar um token de ID do Azure AD B2C. |
| response_mode |Recomendado |O método que utiliza para enviar o código de autorização resultante de volta para a sua aplicação. Pode `query` `form_post` ser, ou `fragment` . . |
| state |Recomendado |Um valor incluído no pedido que pode ser uma série de qualquer conteúdo que queira utilizar. Normalmente, um valor único gerado aleatoriamente é usado para evitar ataques de falsificação de pedidos de trans-locais. O Estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer. Por exemplo, a página em que o utilizador estava, ou o fluxo de utilizador que estava a ser executado. |
| rápido |Opcional |O tipo de interação do utilizador que é necessária. Atualmente, o único valor válido é `login` o que obriga o utilizador a introduzir as suas credenciais nesse pedido. Uma única s inscrição não fará efeito. |
| code_challenge  | recomendado / necessário | Utilizado para garantir concessões de código de autorização através da Chave de Prova para Troca de Códigos (PKCE). Necessário se `code_challenge_method` estiver incluído. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). Isto agora é recomendado para todos os tipos de aplicações - aplicações nativas, SPAs e clientes confidenciais, como aplicações web. | 
| `code_challenge_method` | recomendado / necessário | O método usado para codificar o `code_verifier` `code_challenge` parâmetro. Isto *deve* ser `S256` , mas a especificação permite a utilização de `plain` se por alguma razão o cliente não pode suportar SHA256. <br/><br/>Se excluído, `code_challenge` presume-se que é texto simples se `code_challenge` estiver incluído. A plataforma de identidade da Microsoft suporta ambos `plain` e `S256` . Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). Isto é necessário para [aplicações de página única usando o fluxo de código de autorização](tutorial-register-spa.md).|

Neste momento, é solicitado ao utilizador que complete o fluxo de trabalho do utilizador. Isto pode envolver o utilizador inserindo o seu nome de utilizador e senha, inserindo-se com uma identidade social, inserindo-se no diretório ou qualquer outro número de etapas. As ações do utilizador dependem da definição do fluxo do utilizador.

Depois de o utilizador completar o fluxo do utilizador, o Azure AD devolve uma resposta à sua aplicação pelo valor utilizado para o valor utilizado `redirect_uri` . Utiliza o método especificado no `response_mode` parâmetro. A resposta é exatamente a mesma para cada um dos cenários de ação do utilizador, independentemente do fluxo de utilizador que foi executado.

Uma resposta bem sucedida que usa `response_mode=query` se parece com esta:

```http
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parâmetro | Descrição |
| --- | --- |
| code |O código de autorização que a app solicitou. A aplicação pode usar o código de autorização para solicitar um token de acesso para um recurso alvo. Os códigos de autorização são de curta duração. Normalmente, expiram após cerca de 10 minutos. |
| state |Consulte a descrição completa na tabela na secção anterior. Se um `state` parâmetro for incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se os `state` valores do pedido e resposta são idênticos. |

As respostas de erro também podem ser enviadas para o URI de redirecionamento para que a aplicação possa manuseá-las adequadamente:

```http
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode utilizar para classificar os tipos de erros que ocorrem. Também pode usar a corda para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |
| state |Consulte a descrição completa na tabela anterior. Se um `state` parâmetro for incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se os `state` valores do pedido e resposta são idênticos. |

## <a name="2-get-an-access-token"></a>2. Obter um token de acesso
Agora que adquiriu um código de autorização, pode resgatar o `code` símbolo do recurso pretendido enviando um pedido de CORREIO para o ponto `/token` final. No Azure AD B2C, pode [solicitar fichas de acesso para outras API's,](access-tokens.md#request-a-token) como de costume, especificando os seus âmbitos no pedido.

Também pode solicitar um token de acesso para a própria API web de back-end da sua aplicação por convenção de usar o ID do cliente da aplicação como o âmbito solicitado (o que resultará num token de acesso com esse iD do cliente como "público"):

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
|{inquilino}| Necessário | Nome do seu inquilino Azure AD B2C|
|{política}| Necessário| O fluxo de utilizador que foi utilizado para adquirir o código de autorização. Não é possível utilizar um fluxo de utilizador diferente neste pedido. |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal Azure.](https://portal.azure.com)|
| client_secret | Sim, em Web Apps | O segredo da aplicação que foi gerado no [portal Azure.](https://portal.azure.com/) Os segredos do cliente são usados neste fluxo para cenários de Web App, onde o cliente pode armazenar de forma segura um segredo do cliente. Para cenários de Aplicação Nativa (cliente público), os segredos dos clientes não podem ser armazenados de forma segura e, portanto, não são utilizados nesta chamada. Se usar um segredo de cliente, por favor altere-o periódico. |
| grant_type |Necessário |O tipo de subsídio. Para o fluxo de código de autorização, o tipo de subvenção deve ser `authorization_code` . |
| scope |Recomendado |Uma lista de âmbitos separados pelo espaço. Um único valor de âmbito indica ao Azure AD ambas as permissões que estão a ser solicitadas. A utilização do ID do cliente como o âmbito indica que a sua aplicação precisa de um token de acesso que pode ser usado contra o seu próprio serviço ou a API web, representado pelo mesmo ID do cliente.  O `offline_access` âmbito indica que a sua aplicação precisa de um token de atualização para o acesso de longa duração aos recursos.  Também pode utilizar o `openid` âmbito para solicitar um token de ID do Azure AD B2C. |
| code |Necessário |O código de autorização que adquiriu na primeira parte do fluxo. |
| redirect_uri |Necessário |O URI redirecionado do pedido onde recebeu o código de autorização. |
| code_verifier | recomendado | O mesmo code_verifier que foi usado para obter o authorization_code. Exigido se o PKCE foi utilizado no pedido de concessão de código de autorização. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

Uma resposta simbólica bem sucedida é a seguinte:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parâmetro | Descrição |
| --- | --- |
| not_before |O momento em que o símbolo é considerado válido, em tempo de época. |
| token_type |O valor do tipo símbolo. O único tipo que a Azure AD suporta é o Portador. |
| access_token |O JSON Web Token assinado (JWT) que pediu. |
| scope |Os âmbitos para os que o símbolo é válido. Também pode utilizar os âmbitos para cache tokens para utilização posterior. |
| expires_in |O tempo de duração que o token é válido (em segundos). |
| refresh_token |Um token de atualização OAuth 2.0. A aplicação pode usar este token para adquirir fichas adicionais após o fim do token atual. As fichas de atualização são de longa duração. Pode usá-los para reter o acesso aos recursos por longos períodos de tempo. Para obter mais informações, consulte a [referência simbólica Azure AD B2C](tokens-overview.md). |

As respostas de erro são assim:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode utilizar para classificar os tipos de erros que ocorrem. Também pode usar a corda para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |

## <a name="3-use-the-token"></a>3. Use o símbolo
Agora que adquiriu com sucesso um token de acesso, pode usar o token em pedidos para as suas APIs web de back-end, incluindo-o no `Authorization` cabeçalho:

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Refresque o símbolo
Os tokens de acesso e os tokens de identificação são de curta duração. Depois de expirarem, deve refresca-los para continuar a aceder aos recursos. Para isso, submeta outro pedido DEM para o `/token` ponto final. Desta vez, fornecer o `refresh_token` em vez `code` do:

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
|{inquilino}| Necessário | Nome do seu inquilino Azure AD B2C|
|{política} |Necessário |O fluxo de utilizador que foi usado para adquirir o token original da atualização. Não é possível utilizar um fluxo de utilizador diferente neste pedido. |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal Azure.](https://portal.azure.com) |
| client_secret | Sim, em Web Apps | O segredo da aplicação que foi gerado no [portal Azure.](https://portal.azure.com/) Os segredos do cliente são usados neste fluxo para cenários de Web App, onde o cliente pode armazenar de forma segura um segredo do cliente. Para cenários de Aplicação Nativa (cliente público), os segredos dos clientes não podem ser armazenados de forma segura e, portanto, não são utilizados nesta chamada. Se usar um segredo de cliente, por favor altere-o periódico. |
| grant_type |Necessário |O tipo de subsídio. Para esta parte do fluxo de código de autorização, o tipo de subvenção deve ser `refresh_token` . |
| scope |Recomendado |Uma lista de âmbitos separados pelo espaço. Um único valor de âmbito indica ao Azure AD ambas as permissões que estão a ser solicitadas. A utilização do ID do cliente como o âmbito indica que a sua aplicação precisa de um token de acesso que pode ser usado contra o seu próprio serviço ou a API web, representado pelo mesmo ID do cliente.  O `offline_access` âmbito indica que a sua aplicação necessitará de um token de atualização para o acesso de longa duração aos recursos.  Também pode utilizar o `openid` âmbito para solicitar um token de ID do Azure AD B2C. |
| redirect_uri |Opcional |O URI redirecionado do pedido onde recebeu o código de autorização. |
| refresh_token |Necessário |O token original da atualização que adquiriu na segunda parte do fluxo. |

Uma resposta simbólica bem sucedida é a seguinte:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parâmetro | Descrição |
| --- | --- |
| not_before |O momento em que o símbolo é considerado válido, em tempo de época. |
| token_type |O valor do tipo símbolo. O único tipo que a Azure AD suporta é o Portador. |
| access_token |O JWT assinado que pediu. |
| scope |Os âmbitos para os que o símbolo é válido. Também pode utilizar os telescópios para cache tokens para utilização posterior. |
| expires_in |O tempo de duração que o token é válido (em segundos). |
| refresh_token |Um token de atualização OAuth 2.0. A aplicação pode usar este token para adquirir fichas adicionais após o fim do token atual. Os tokens de atualização são de longa duração, e podem ser usados para manter o acesso aos recursos por longos períodos de tempo. Para obter mais informações, consulte a [referência simbólica Azure AD B2C](tokens-overview.md). |

As respostas de erro são assim:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode utilizar para classificar tipos de erros que ocorrem. Também pode usar a corda para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Use o seu próprio diretório Azure AD B2C
Para experimentar estes pedidos, complete os seguintes passos. Substitua os valores de exemplo que utilizamos neste artigo pelos seus próprios valores.

1. [Criar um diretório Azure AD B2C](tutorial-create-tenant.md). Use o nome do seu diretório nos pedidos.
2. [Crie uma aplicação](tutorial-register-applications.md) para obter um ID de aplicação e um URI redirecionado. Inclua um cliente nativo na sua aplicação.
3. [Crie os fluxos do seu utilizador](user-flow-overview.md) para obter os seus nomes de fluxo de utilizador.
