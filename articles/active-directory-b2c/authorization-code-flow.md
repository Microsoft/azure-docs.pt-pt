---
title: Fluxo de código de autorização-Azure Active Directory B2C | Microsoft Docs
description: Saiba como criar aplicativos Web usando o Azure AD B2C e o protocolo de Autenticação OpenID Connect.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 6ddfefb69e53ec4801d913e01b00158a4905d2df
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846942"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Fluxo de código de autorização do OAuth 2,0 em Azure Active Directory B2C

Você pode usar a concessão de código de autorização do OAuth 2,0 em aplicativos instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Usando a implementação de Azure Active Directory B2C (Azure AD B2C) do OAuth 2,0, você pode adicionar tarefas de inscrição, entrada e outras de gerenciamento de identidade para seus aplicativos móveis e de área de trabalho. Este artigo é independente de linguagem. No artigo, descrevemos como enviar e receber mensagens HTTP sem usar nenhuma biblioteca de código-fonte aberto.

O fluxo de código de autorização do OAuth 2,0 é descrito na [seção 4,1 da especificação do OAuth 2,0](https://tools.ietf.org/html/rfc6749). Você pode usá-lo para autenticação e autorização na maioria dos [tipos de aplicativos](application-types.md), incluindo aplicativos Web e aplicativos instalados nativamente. Você pode usar o fluxo de código de autorização do OAuth 2,0 para adquirir tokens de acesso e atualizar tokens com segurança para seus aplicativos, que podem ser usados para acessar recursos que são protegidos por um [servidor de autorização](protocols-overview.md).  O token de atualização permite que o cliente adquira novos tokens de acesso (e atualização) quando o token de acesso expirar, normalmente após uma hora.

Este artigo se concentra no fluxo de código de autorização dos **clientes públicos** do OAuth 2,0. Um cliente público é qualquer aplicativo cliente que não pode ser confiável para manter a integridade de uma senha secreta com segurança. Isso inclui aplicativos móveis, aplicativos de área de trabalho e, essencialmente, qualquer aplicativo executado em um dispositivo e precisa obter tokens de acesso.

> [!NOTE]
> Para adicionar o gerenciamento de identidade a um aplicativo Web usando Azure AD B2C, use o [OpenID Connect](openid-connect.md) em vez do OAuth 2,0.

Azure AD B2C estende os fluxos padrão do OAuth 2,0 para fazer mais do que a autenticação e a autorização simples. Ele apresenta o [fluxo do usuário](user-flow-overview.md). Com fluxos de usuário, você pode usar o OAuth 2,0 para adicionar experiências de usuário ao seu aplicativo, como inscrição, entrada e gerenciamento de perfil. Os provedores de identidade que usam o protocolo OAuth 2,0 incluem [Amazon](identity-provider-amazon.md), [Azure Active Directory](identity-provider-azure-ad-single-tenant.md), [Facebook](identity-provider-facebook.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md)e [LinkedIn](identity-provider-linkedin.md).

Para experimentar as solicitações HTTP neste artigo:

1. Substitua `{tenant}` com o nome do seu inquilino do Azure AD B2C.
1. Substitua `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` pela ID do aplicativo que você registrou anteriormente em seu locatário Azure AD B2C.
1. Substitua `{policy}` pelo nome de uma apólice que criou no seu inquilino, por exemplo, `b2c_1_sign_in`.

## <a name="1-get-an-authorization-code"></a>1. obter um código de autorização
O fluxo do código de autorização começa com o cliente direcionando o usuário para o ponto de extremidade `/authorize`. Essa é a parte interativa do fluxo, onde o usuário executa a ação. Nessa solicitação, o cliente indica no parâmetro `scope` as permissões que ele precisa adquirir do usuário. Os três exemplos a seguir (com quebras de linha para facilitar a leitura) usam um fluxo de usuário diferente.


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
|vários| Obrigatório | Nome do seu locatário de Azure AD B2C|
| regras | Obrigatório | O fluxo do usuário a ser executado. Especifique o nome de um fluxo de usuário que você criou em seu locatário Azure AD B2C. Por exemplo: `b2c_1_sign_in`, `b2c_1_sign_up`ou `b2c_1_edit_profile`. |
| client_id |Obrigatório |A ID do aplicativo atribuída ao seu aplicativo no [portal do Azure](https://portal.azure.com). |
| response_type |Obrigatório |O tipo de resposta, que deve incluir `code` para o fluxo do código de autorização. |
| redirect_uri |Obrigatório |O URI de redirecionamento do seu aplicativo, em que as respostas de autenticação são enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no portal, exceto que ele deve ser codificado por URL. |
| scope |Obrigatório |Uma lista de escopos separados por espaços. Um único valor de escopo indica a Azure Active Directory (Azure AD) as permissões que estão sendo solicitadas. O uso da ID do cliente como o escopo indica que seu aplicativo precisa de um token de acesso que possa ser usado em relação ao seu próprio serviço ou API Web, representado pela mesma ID do cliente.  O âmbito `offline_access` indica que a sua aplicação precisa de um token de atualização para o acesso de longa duração aos recursos. Você também pode usar o escopo de `openid` para solicitar um token de ID de Azure AD B2C. |
| response_mode |Recomendado |O método que você usa para enviar o código de autorização resultante de volta para seu aplicativo. Pode ser `query`, `form_post`ou `fragment`. |
| state |Recomendado |Um valor incluído na solicitação que pode ser uma cadeia de caracteres de qualquer conteúdo que você deseja usar. Normalmente, um valor exclusivo gerado aleatoriamente é usado, para evitar ataques de solicitação entre sites forjado. O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação tenha ocorrido. Por exemplo, a página em que o usuário estava ou o fluxo do usuário que estava sendo executado. |
| aviso |Opcional |O tipo de interação do usuário que é necessário. Atualmente, o único valor válido é `login`, o que força o usuário a inserir suas credenciais nessa solicitação. O logon único não entrará em vigor. |

Neste ponto, o usuário será solicitado a concluir o fluxo de trabalho do fluxo do usuário. Isso pode envolver o usuário que está inserindo seu nome de usuários e senha, entrando com uma identidade social, inscrevendo-se no diretório ou qualquer outro número de etapas. As ações do usuário dependem de como o fluxo do usuário é definido.

Depois de o utilizador completar o fluxo de utilizador, o Azure AD devolve uma resposta à sua aplicação pelo valor utilizado para `redirect_uri`. Utiliza o método especificado no parâmetro `response_mode`. A resposta é exatamente a mesma para cada um dos cenários de ação do usuário, independentemente do fluxo do usuário que foi executado.

Uma resposta bem sucedida que usa `response_mode=query` se parece com isto:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parâmetro | Descrição |
| --- | --- |
| code |O código de autorização solicitado pelo aplicativo. O aplicativo pode usar o código de autorização para solicitar um token de acesso para um recurso de destino. Os códigos de autorização têm vida muito curta. Normalmente, eles expiram após cerca de 10 minutos. |
| state |Consulte a descrição completa na tabela na seção anterior. Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de `state` na solicitação e na resposta são idênticos. |

As respostas de erro também podem ser enviadas para o URI de redirecionamento para que o aplicativo possa tratá-las adequadamente:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de caracteres de código de erro que você pode usar para classificar os tipos de erros que ocorrem. Você também pode usar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa raiz de um erro de autenticação. |
| state |Consulte a descrição completa na tabela anterior. Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de `state` na solicitação e na resposta são idênticos. |

## <a name="2-get-a-token"></a>2. obter um token
Agora que você adquiriu um código de autorização, pode resgatar o `code` de um token para o recurso pretendido enviando uma solicitação POST para o ponto de extremidade `/token`. No Azure AD B2C, você pode [solicitar tokens de acesso para outras APIs](access-tokens.md#request-a-token) como de costume, especificando seus escopos na solicitação.

Você também pode solicitar um token de acesso para a própria API Web de back-end do seu aplicativo por convenção de usar a ID do cliente do aplicativo como o escopo solicitado (o que resultará em um token de acesso com essa ID de cliente como o "público"):

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
|vários| Obrigatório | Nome do seu locatário de Azure AD B2C|
|regras| Obrigatório| O fluxo do usuário que foi usado para adquirir o código de autorização. Você não pode usar um fluxo de usuário diferente nesta solicitação. |
| client_id |Obrigatório |A ID do aplicativo atribuída ao seu aplicativo no [portal do Azure](https://portal.azure.com).|
| client_secret | Sim, em aplicativos Web | O segredo do aplicativo que foi gerado no [portal do Azure](https://portal.azure.com/). Os segredos do cliente são usados nesse fluxo para cenários de aplicativos Web, em que o cliente pode armazenar com segurança um segredo do cliente. Para cenários de aplicativo nativo (cliente público), os segredos do cliente não podem ser armazenados com segurança e, portanto, não são usados nesta chamada. Se você usar um segredo do cliente, altere-o periodicamente. |
| grant_type |Obrigatório |O tipo de concessão. Para o fluxo do código de autorização, o tipo de subvenção deve ser `authorization_code`. |
| scope |Recomendado |Uma lista de escopos separados por espaços. Um único valor de escopo indica ao Azure AD as permissões que estão sendo solicitadas. O uso da ID do cliente como o escopo indica que seu aplicativo precisa de um token de acesso que possa ser usado em relação ao seu próprio serviço ou API Web, representado pela mesma ID do cliente.  O âmbito `offline_access` indica que a sua aplicação precisa de um token de atualização para o acesso de longa duração aos recursos.  Você também pode usar o escopo de `openid` para solicitar um token de ID de Azure AD B2C. |
| code |Obrigatório |O código de autorização que você adquiriu no primeiro segmento do fluxo. |
| redirect_uri |Obrigatório |O URI de redirecionamento do aplicativo em que você recebeu o código de autorização. |

Uma resposta de token bem-sucedida tem esta aparência:

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
| not_before |A hora em que o token é considerado válido, no momento da época. |
| token_type |O valor do tipo de token. O único tipo ao qual o Azure AD dá suporte é portador. |
| access_token |O JWT (token Web JSON) assinado que você solicitou. |
| scope |Os escopos para os quais o token é válido. Você também pode usar escopos para armazenar tokens em cache para uso posterior. |
| expires_in |O período de tempo em que o token é válido (em segundos). |
| refresh_token |Um token de atualização OAuth 2,0. O aplicativo pode usar esse token para adquirir tokens adicionais depois que o token atual expirar. Os tokens de atualização têm vida longa. Você pode usá-los para manter o acesso a recursos por longos períodos de tempo. Para obter mais informações, consulte a [referência de token Azure ad B2C](tokens-overview.md). |

As respostas de erro têm a seguinte aparência:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de caracteres de código de erro que você pode usar para classificar os tipos de erros que ocorrem. Você também pode usar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa raiz de um erro de autenticação. |

## <a name="3-use-the-token"></a>3. usar o token
Agora que você adquiriu com êxito um token de acesso, você pode usar o token em solicitações para suas APIs Web de back-end, incluindo-o no cabeçalho `Authorization`:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. atualizar o token
Tokens de acesso e tokens de ID são de curta duração. Depois que eles expirarem, você deverá atualizá-los para continuar a acessar os recursos. Para fazer isso, envie outra solicitação POST para o ponto de extremidade `/token`. Desta vez, forneça o `refresh_token` em vez do `code`:

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
|vários| Obrigatório | Nome do seu locatário de Azure AD B2C|
|regras |Obrigatório |O fluxo do usuário que foi usado para adquirir o token de atualização original. Você não pode usar um fluxo de usuário diferente nesta solicitação. |
| client_id |Obrigatório |A ID do aplicativo atribuída ao seu aplicativo no [portal do Azure](https://portal.azure.com). |
| client_secret | Sim, em aplicativos Web | O segredo do aplicativo que foi gerado no [portal do Azure](https://portal.azure.com/). Os segredos do cliente são usados nesse fluxo para cenários de aplicativos Web, em que o cliente pode armazenar com segurança um segredo do cliente. Para cenários de aplicativo nativo (cliente público), os segredos do cliente não podem ser armazenados com segurança e, portanto, não são usados nesta chamada. Se você usar um segredo do cliente, altere-o periodicamente. |
| grant_type |Obrigatório |O tipo de concessão. Para esta perna do fluxo do código de autorização, o tipo de subvenção deve ser `refresh_token`. |
| scope |Recomendado |Uma lista de escopos separados por espaços. Um único valor de escopo indica ao Azure AD as permissões que estão sendo solicitadas. O uso da ID do cliente como o escopo indica que seu aplicativo precisa de um token de acesso que possa ser usado em relação ao seu próprio serviço ou API Web, representado pela mesma ID do cliente.  O âmbito `offline_access` indica que a sua aplicação necessitará de um token de atualização para o acesso de longa duração aos recursos.  Você também pode usar o escopo de `openid` para solicitar um token de ID de Azure AD B2C. |
| redirect_uri |Opcional |O URI de redirecionamento do aplicativo em que você recebeu o código de autorização. |
| refresh_token |Obrigatório |O token de atualização original que você adquiriu no segundo segmento do fluxo. |

Uma resposta de token bem-sucedida tem esta aparência:

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
| not_before |A hora em que o token é considerado válido, no momento da época. |
| token_type |O valor do tipo de token. O único tipo ao qual o Azure AD dá suporte é portador. |
| access_token |O JWT assinado que você solicitou. |
| scope |Os escopos para os quais o token é válido. Você também pode usar os escopos para armazenar tokens em cache para uso posterior. |
| expires_in |O período de tempo em que o token é válido (em segundos). |
| refresh_token |Um token de atualização OAuth 2,0. O aplicativo pode usar esse token para adquirir tokens adicionais depois que o token atual expirar. Os tokens de atualização são de longa duração e podem ser usados para manter o acesso aos recursos por longos períodos de tempo. Para obter mais informações, consulte a [referência de token Azure ad B2C](tokens-overview.md). |

As respostas de erro têm a seguinte aparência:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de caracteres de código de erro que você pode usar para classificar tipos de erros que ocorrem. Você também pode usar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa raiz de um erro de autenticação. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Usar seu próprio diretório de Azure AD B2C
Para testar essas solicitações por conta própria, conclua as etapas a seguir. Substitua os valores de exemplo usados neste artigo pelos seus próprios valores.

1. [Crie um diretório Azure ad B2C](tutorial-create-tenant.md). Use o nome do seu diretório nas solicitações.
2. [Crie um aplicativo](tutorial-register-applications.md) para obter uma ID do aplicativo e um URI de redirecionamento. Inclua um cliente nativo em seu aplicativo.
3. [Crie seus fluxos de usuário](user-flow-overview.md) para obter seus nomes de fluxo de usuário.
