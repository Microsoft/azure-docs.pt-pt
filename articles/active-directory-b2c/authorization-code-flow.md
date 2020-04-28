---
title: Fluxo de código de autorização - Diretório Ativo Azure B2C / Microsoft Docs
description: Saiba como construir aplicações web utilizando o protocolo de autenticação Azure AD B2C e OpenID Connect.
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
ms.openlocfilehash: 8248ca0abb1d633786b09b894bcd6b1089ab2d8c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79260894"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Fluxo de código de autorização OAuth 2.0 no Diretório Ativo Azure B2C

Pode utilizar a concessão de código de autorização OAuth 2.0 em aplicações instaladas num dispositivo para ter acesso a recursos protegidos, como apis web. Ao utilizar a implementação do OAuth 2.0 do Diretório Ativo Azure B2C, pode adicionar as tarefas de inscrição, inscrição e outras tarefas de gestão de identidade às suas aplicações móveis e de desktop. Este artigo é independente da linguagem. No artigo, descrevemos como enviar e receber mensagens HTTP sem usar nenhuma biblioteca de código aberto.

O fluxo de código de autorização OAuth 2.0 é descrito na [secção 4.1 da especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749). Pode utilizá-lo para autenticação e autorização na maioria dos tipos de [aplicações,](application-types.md)incluindo aplicações web e aplicações instaladas de forma nativa. Pode utilizar o fluxo de código de autorização OAuth 2.0 para adquirir de forma segura fichas de acesso e atualização de fichas para as suas aplicações, que podem ser usadas para aceder a recursos que são protegidos por um servidor de [autorização](protocols-overview.md).  O token de atualização permite ao cliente adquirir novos tokens de acesso (e atualização) uma vez expirado o token de acesso, normalmente após uma hora.

Este artigo centra-se no fluxo de código de autorização dos **clientes públicos** OAuth 2.0. Um cliente público é qualquer aplicação de cliente que não seja de confiança para manter de forma segura a integridade de uma senha secreta. Isto inclui aplicações móveis, aplicações para desktop e essencialmente qualquer aplicação que execute num dispositivo e precise de ter acesso a fichas.

> [!NOTE]
> Para adicionar gestão de identidade a uma aplicação web utilizando o Azure AD B2C, utilize [o OpenID Connect](openid-connect.md) em vez do OAuth 2.0.

O Azure AD B2C alarga os fluxos oAuth 2.0 padrão para fazer mais do que simples autenticação e autorização. Introduz o [fluxo](user-flow-overview.md)do utilizador . Com os fluxos de utilizadores, pode utilizar o OAuth 2.0 para adicionar experiências de utilizador à sua aplicação, tais como inscrição, inscrição e gestão de perfis. Os fornecedores de identidade que utilizam o protocolo OAuth 2.0 incluem [amazon](identity-provider-amazon.md), [Azure Ative Directory](identity-provider-azure-ad-single-tenant.md), [Facebook](identity-provider-facebook.md), [GitHub,](identity-provider-github.md) [Google](identity-provider-google.md)e [LinkedIn](identity-provider-linkedin.md).

Para experimentar os pedidos http neste artigo:

1. Substitua `{tenant}` pelo nome do seu inquilino Azure AD B2C.
1. Substitua `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` com a identificação da aplicação de uma aplicação que já registou no seu inquilino Azure AD B2C.
1. Substitua-o `{policy}` pelo nome de uma apólice que `b2c_1_sign_in`criou no seu inquilino, por exemplo.

## <a name="1-get-an-authorization-code"></a>1. Obter um código de autorização
O fluxo de código de autorização começa `/authorize` com o cliente a direcionar o utilizador para o ponto final. Esta é a parte interativa do fluxo, onde o utilizador toma medidas. Neste pedido, o cliente `scope` indica no parâmetro as permissões que necessita de adquirir ao utilizador. Os seguintes três exemplos (com quebras de linha para a legibilidade) usam cada um um fluxo de utilizador diferente.


```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
```


| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
|{inquilino}| Necessário | Nome do seu inquilino Azure AD B2C|
| {política} | Necessário | O fluxo do utilizador para ser executado. Especifique o nome de um fluxo de utilizador que criou no seu inquilino Azure AD B2C. Por `b2c_1_sign_in`exemplo: `b2c_1_sign_up`, `b2c_1_edit_profile`ou . |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal Azure.](https://portal.azure.com) |
| response_type |Necessário |O tipo de resposta, que deve incluir `code` o fluxo do código de autorização. |
| redirect_uri |Necessário |O URI redirecionado da sua aplicação, onde as respostas de autenticação são enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das URIs redirecionadas que registou no portal, exceto que deve ser codificada por URL. |
| scope |Necessário |Uma lista de âmbitos separados pelo espaço. Um único valor de âmbito indica ao Azure Ative Directory (Azure AD) ambas as permissões que estão a ser solicitadas. A utilização do ID do cliente como âmbito indica que a sua aplicação necessita de um token de acesso que possa ser usado contra o seu próprio serviço ou API web, representado pelo mesmo ID do cliente.  O `offline_access` âmbito indica que a sua aplicação precisa de um token de atualização para o acesso de longa duração aos recursos. Também pode utilizar `openid` o âmbito para solicitar um token de identificação do Azure AD B2C. |
| response_mode |Recomendado |O método que utiliza para enviar o código de autorização resultante de volta para a sua aplicação. Pode `query`ser, `form_post`ou. `fragment` |
| state |Recomendado |Um valor incluído no pedido que pode ser uma série de qualquer conteúdo que queira utilizar. Normalmente, um valor único gerado aleatoriamente é usado, para evitar ataques de falsificação de pedidos de local. O Estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer. Por exemplo, a página em que o utilizador estava, ou o fluxo de utilizador que estava a ser executado. |
| pronta |Opcional |O tipo de interação do utilizador que é necessário. Atualmente, o único `login`valor válido é , o que obriga o utilizador a introduzir as suas credenciais nesse pedido. A única inscrição não produzirá efeito. |

Neste ponto, é solicitado ao utilizador que complete o fluxo de trabalho do fluxo de utilizadores. Isto pode implicar que o utilizador insete o seu nome de utilizador e senha, inscreva-se com uma identidade social, inscreva-se no diretório ou qualquer outro número de passos. As ações do utilizador dependem da definição do fluxo do utilizador.

Depois de o utilizador completar o fluxo de utilizador, o Azure AD devolve uma resposta à sua aplicação pelo valor que utilizou `redirect_uri`para . Utiliza o método especificado `response_mode` no parâmetro. A resposta é exatamente a mesma para cada um dos cenários de ação do utilizador, independentemente do fluxo de utilizador que foi executado.

Uma resposta bem `response_mode=query` sucedida que usa é assim:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parâmetro | Descrição |
| --- | --- |
| code |O código de autorização que a aplicação solicitou. A aplicação pode usar o código de autorização para solicitar um sinal de acesso para um recurso-alvo. Os códigos de autorização são de muito curta duração. Normalmente, expiram após cerca de 10 minutos. |
| state |Consulte a descrição completa da tabela na secção anterior. Se `state` um parâmetro estiver incluído no pedido, o mesmo valor deve figurar na resposta. A aplicação deve `state` verificar se os valores no pedido e resposta são idênticos. |

As respostas de erro também podem ser enviadas para o URI redireccional para que a aplicação possa manuseá-las adequadamente:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode usar para classificar os tipos de erros que ocorrem. Também pode usar a corda para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |
| state |Consulte a descrição completa na tabela anterior. Se `state` um parâmetro estiver incluído no pedido, o mesmo valor deve figurar na resposta. A aplicação deve `state` verificar se os valores no pedido e resposta são idênticos. |

## <a name="2-get-a-token"></a>2. Obter um símbolo
Agora que adquiriu um código de autorização, `code` pode resgatar o sinal de um token `/token` para o recurso pretendido enviando um pedido de CORREIO para o ponto final. No Azure AD B2C, pode solicitar fichas de [acesso para outros API's,](access-tokens.md#request-a-token) como de costume, especificando os seus âmbitos no pedido.

Também pode solicitar um sinal de acesso para a própria Web API da sua aplicação por convenção de utilização do ID do cliente da aplicação como âmbito solicitado (o que resultará num sinal de acesso com esse ID do cliente como "público"):

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
|{inquilino}| Necessário | Nome do seu inquilino Azure AD B2C|
|{política}| Necessário| O fluxo de utilizador que foi usado para adquirir o código de autorização. Não é possível utilizar um fluxo de utilizador diferente neste pedido. |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal Azure.](https://portal.azure.com)|
| client_secret | Sim, em Aplicações Web | O segredo de aplicação que foi gerado no [portal Azure.](https://portal.azure.com/) Os segredos do cliente são usados neste fluxo para cenários de Web App, onde o cliente pode armazenar de forma segura um segredo de cliente. Para cenários de App Nativa (cliente público), os segredos dos clientes não podem ser armazenados de forma segura e, portanto, não são utilizados nesta chamada. Se usar um segredo de cliente, por favor, troque-o periodontimemente. |
| grant_type |Necessário |O tipo de subvenção. Para o fluxo do código de `authorization_code`autorização, o tipo de subvenção deve ser . |
| scope |Recomendado |Uma lista de âmbitos separados pelo espaço. Um único valor de âmbito indica à Azure AD ambas as permissões que estão a ser solicitadas. A utilização do ID do cliente como âmbito indica que a sua aplicação necessita de um token de acesso que possa ser usado contra o seu próprio serviço ou API web, representado pelo mesmo ID do cliente.  O `offline_access` âmbito indica que a sua aplicação precisa de um token de atualização para o acesso de longa duração aos recursos.  Também pode utilizar `openid` o âmbito para solicitar um token de identificação do Azure AD B2C. |
| code |Necessário |O código de autorização que adquiriu na primeira perna do fluxo. |
| redirect_uri |Necessário |O URI redirecionamento do pedido onde recebeu o código de autorização. |

Uma resposta simbólica bem sucedida é a seguinte:

```JSON
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
| not_before |O tempo em que o símbolo é considerado válido, em época. |
| token_type |O valor do tipo simbólico. O único tipo que a Azure AD suporta é o Bearer. |
| access_token |O JSON Web Token (JWT) assinado que solicitou. |
| scope |Os âmbitos para os os que o símbolo é válido. Também pode utilizar os telescópios para obter tokens de cache para posterior utilização. |
| expires_in |O tempo que o símbolo é válido (em segundos). |
| refresh_token |Um token refresh OAuth 2.0. A aplicação pode usar este token para adquirir tokens adicionais após o token atual expirar. As fichas refrescantes são de longa duração. Pode usá-los para reter o acesso aos recursos por longos períodos de tempo. Para mais informações, consulte a [referência token Azure AD B2C](tokens-overview.md). |

As respostas de erro são assim:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode usar para classificar os tipos de erros que ocorrem. Também pode usar a corda para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |

## <a name="3-use-the-token"></a>3. Utilize o símbolo
Agora que adquiriu com sucesso um sinal de acesso, pode usar o símbolo em pedidos para as `Authorization` suas APIs web back-end, incluindo-o no cabeçalho:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Refrescar o símbolo
Fichas de acesso e fichas de identificação são de curta duração. Depois de expirarem, deve renová-los para continuar a aceder aos recursos. Para tal, submeta outro `/token` pedido do POST ao ponto final. Desta vez, `refresh_token` forneça o `code`em vez do:

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
|{inquilino}| Necessário | Nome do seu inquilino Azure AD B2C|
|{política} |Necessário |O fluxo do utilizador que foi usado para adquirir o token de atualização original. Não é possível utilizar um fluxo de utilizador diferente neste pedido. |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal Azure.](https://portal.azure.com) |
| client_secret | Sim, em Aplicações Web | O segredo de aplicação que foi gerado no [portal Azure.](https://portal.azure.com/) Os segredos do cliente são usados neste fluxo para cenários de Web App, onde o cliente pode armazenar de forma segura um segredo de cliente. Para cenários de App Nativa (cliente público), os segredos dos clientes não podem ser armazenados de forma segura e, portanto, não são utilizados nesta chamada. Se usar um segredo de cliente, por favor, troque-o periodontimemente. |
| grant_type |Necessário |O tipo de subvenção. Para esta perna do fluxo do código `refresh_token`de autorização, o tipo de subvenção deve ser . |
| scope |Recomendado |Uma lista de âmbitos separados pelo espaço. Um único valor de âmbito indica à Azure AD ambas as permissões que estão a ser solicitadas. A utilização do ID do cliente como âmbito indica que a sua aplicação necessita de um token de acesso que possa ser usado contra o seu próprio serviço ou API web, representado pelo mesmo ID do cliente.  O `offline_access` âmbito indica que a sua aplicação necessitará de um token de atualização para o acesso de longa duração aos recursos.  Também pode utilizar `openid` o âmbito para solicitar um token de identificação do Azure AD B2C. |
| redirect_uri |Opcional |O URI redirecionamento do pedido onde recebeu o código de autorização. |
| refresh_token |Necessário |O símbolo original que adquiriu na segunda etapa do fluxo. |

Uma resposta simbólica bem sucedida é a seguinte:

```JSON
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
| not_before |O tempo em que o símbolo é considerado válido, em época. |
| token_type |O valor do tipo simbólico. O único tipo que a Azure AD suporta é o Bearer. |
| access_token |O JWT assinado que pediu. |
| scope |Os âmbitos para os os que o símbolo é válido. Também pode utilizar os âmbitos para cache tokens para posterior utilização. |
| expires_in |O tempo que o símbolo é válido (em segundos). |
| refresh_token |Um token refresh OAuth 2.0. A aplicação pode usar este token para adquirir tokens adicionais após o token atual expirar. As fichas de atualização são de longa duração, e podem ser usadas para manter o acesso aos recursos por longos períodos de tempo. Para mais informações, consulte a [referência token Azure AD B2C](tokens-overview.md). |

As respostas de erro são assim:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode usar para classificar tipos de erros que ocorrem. Também pode usar a corda para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Use o seu próprio diretório Azure AD B2C
Para experimentar estes pedidos, complete os seguintes passos. Substitua os valores de exemplo que usamos neste artigo com os seus próprios valores.

1. [Crie um diretório Azure AD B2C.](tutorial-create-tenant.md) Use o nome do seu diretório nos pedidos.
2. [Crie uma aplicação](tutorial-register-applications.md) para obter um ID de aplicação e um URI redirecionamento. Inclua um cliente nativo na sua aplicação.
3. [Crie os fluxos do utilizador](user-flow-overview.md) para obter os nomes de fluxo do utilizador.
