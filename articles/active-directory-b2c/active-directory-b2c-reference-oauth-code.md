---
title: Fluxo de código de autorização - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como criar aplicações web utilizando o protocolo de autenticação do Azure AD B2C e o OpenID Connect.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7157682d7952529f9dfa98e8bc8707df9cfe944f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509249"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Fluxo de código de autorização de OAuth 2.0 no Azure Active Directory B2C

Pode utilizar a concessão de código de autorização de OAuth 2.0 nas aplicações instaladas num dispositivo para obter acesso a recursos protegidos, como as APIs web. Ao utilizar o Azure Active Directory B2C (Azure AD B2C) a implementação do OAuth 2.0, pode adicionar inscrição, início de sessão, e as tarefas de outra gestão de identidades às suas aplicações móveis e de Desktops. Este artigo é independente de idioma. Neste artigo, descrevemos como enviar e receber mensagens HTTP sem utilizar quaisquer bibliotecas de código-fonte aberto.

O fluxo de código de autorização de OAuth 2.0 é descrito em [secção 4.1 da especificação de OAuth 2.0](https://tools.ietf.org/html/rfc6749). Pode usá-lo para autenticação e autorização na maioria [tipos de aplicativos](active-directory-b2c-apps.md), incluindo aplicações web e aplicativos instalados nativamente. Pode utilizar o fluxo de código de autorização de OAuth 2.0 para adquirir os tokens de acesso e tokens de atualização para as suas aplicações, que podem ser utilizadas para aceder a recursos que são protegidos com segurança uma [servidor de autorização](active-directory-b2c-reference-protocols.md).  O token de atualização permite ao cliente adquirir acesso nova (e atualizar) tokens assim que o token de acesso expira, normalmente, depois de uma hora.

Este artigo enfoca a **os clientes públicos** fluxo de código de autorização de OAuth 2.0. Um cliente público é qualquer aplicativo de cliente que não pode ser confiável para manter em segurança a integridade de uma palavra-passe secreta. Isto inclui aplicações móveis, aplicativos de desktop e, essencialmente, qualquer aplicação executada num dispositivo que tem de obter os tokens de acesso. 

> [!NOTE]
> Para adicionar gerenciamento de identidade para uma aplicação web com o Azure AD B2C, utilize [OpenID Connect](active-directory-b2c-reference-oidc.md) em vez de OAuth 2.0.

O Azure AD B2C estende o padrão de que fluxos do OAuth 2.0 para fazer mais do que a autenticação e autorização simples. Ele introduz o [parâmetro de fluxo de utilizador](active-directory-b2c-reference-policies.md). Com fluxos de utilizador, pode utilizar o OAuth 2.0 para adicionar experiências de utilizador à sua aplicação, tais como inscrição, início de sessão e gestão de perfis. Fornecedores de identidade que utilizam o protocolo de OAuth 2.0 incluem [Amazon](active-directory-b2c-setup-amzn-app.md), [Azure Active Directory](active-directory-b2c-setup-oidc-azure-active-directory.md), [Facebook](active-directory-b2c-setup-fb-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [ Google](active-directory-b2c-setup-goog-app.md), e [LinkedIn](active-directory-b2c-setup-li-app.md).

Os pedidos HTTP de exemplo neste artigo, utilizamos o nosso diretório do Azure AD B2C de exemplo, **fabrikamb2c.onmicrosoft.com**. Também utilizamos nossos fluxos de utilizador e a aplicação de exemplo. Pode experimentar os pedidos utilizando estes valores, ou pode substituí-los com seus próprios valores.
Saiba como [obter seus próprios fluxos de utilizador, aplicação e diretório do Azure AD B2C](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Obter um código de autorização
O fluxo de código de autorização começa com o cliente que direciona o utilizador para o `/authorize` ponto final. Esta é a parte interativa do fluxo, em que o utilizador efetua uma ação. Este pedido, o cliente indica no `scope` parâmetro as permissões que ele necessita obter do usuário. Na `p` parâmetro, ele indica que o fluxo de utilizador para executar. Os três exemplos seguintes (com quebras de linha para facilitar a leitura) cada utilizam um fluxo de utilizador diferente.

### <a name="use-a-sign-in-user-flow"></a>Utilizar um fluxo de início de sessão do utilizador
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-user-flow"></a>Utilizar um fluxo de inscrição de utilizador
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-user-flow"></a>Utilizar um fluxo de utilizador de Editar perfil
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal do Azure](https://portal.azure.com). |
| response_type |Necessário |O tipo de resposta, que tem de incluir `code` para o fluxo de código de autorização. |
| redirect_uri |Necessário |O URI de redirecionamento de seu aplicativo, onde as respostas enviadas e recebidas pela sua aplicação. Ele deve corresponder exatamente um dos URIs que registou no portal de redirecionamento, exceto pelo fato de que tem de ser codificados de URL. |
| scope |Necessário |Uma lista de âmbitos separadas por espaços. Um valor de âmbito único indica ao Azure Active Directory (Azure AD) ambas as permissões que estão a ser solicitada. Com o ID de cliente como o âmbito indica que o aplicativo precisa de um token de acesso que pode ser utilizado em relação a seu próprio serviço ou a web API, representado pelo mesmo ID de cliente.  O `offline_access` âmbito indica que o aplicativo precisa de um token de atualização de longa duração acesso a recursos. Também pode utilizar o `openid` âmbito para pedir um token de ID do Azure AD B2C. |
| response_mode |Recomendado |O método que utilizar para enviar o código de autorização resultante de volta à aplicação. Pode ser `query`, `form_post`, ou `fragment`. |
| state |Recomendado |Um valor incluído no pedido que pode ser uma cadeia de caracteres de qualquer conteúdo que pretende utilizar. Normalmente, um valor exclusivo gerado aleatoriamente é usado, para impedir ataques de falsificação de solicitação entre sites. O estado também é usado para codificar as informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu. Por exemplo, a página que o usuário estava em ou o fluxo de utilizador que está a ser executado. |
| p |Necessário |O fluxo de utilizador que é executado. É o nome de um fluxo de utilizador que é criado no diretório do Azure AD B2C. O valor de nome de fluxo de utilizador deve iniciar com **b2c\_1\_** . Para saber mais sobre fluxos de utilizador, veja [fluxos de utilizador do Azure AD B2C](active-directory-b2c-reference-policies.md). |
| linha de comandos |Opcional |O tipo de interação do utilizador que é necessário. Atualmente, o único valor válido é `login`, que força o utilizador introduza as credenciais desse pedido. Início de sessão único não irão surtir efeito. |

Neste momento, é pedido ao utilizador para concluir o fluxo de trabalho do fluxo de utilizador. Isto pode envolver o utilizador introduzir o nome de utilizador e palavra-passe, início de sessão com uma identidade de redes sociais, inscrever-se para o diretório ou qualquer outro número de passos. Ações do usuário dependem de como o fluxo de utilizador está definido.

Depois do utilizador concluir o fluxo de utilizador, o AD do Azure devolve uma resposta à sua aplicação com o valor que utilizou para `redirect_uri`. Ele usa o método especificado no `response_mode` parâmetro. A resposta é exatamente o mesmo para cada um dos cenários de ação do utilizador, independentemente do fluxo de utilizador que foi executado.

Uma resposta com êxito, que utiliza `response_mode=query` semelhante ao seguinte:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parâmetro | Descrição |
| --- | --- |
| code |O código de autorização que solicitou a aplicação. A aplicação pode utilizar o código de autorização para pedir um token de acesso para um recurso de destino. Códigos de autorização são muito curta duração. Normalmente, esta expira após cerca de 10 minutos. |
| state |Consulte a descrição completa da tabela na secção anterior. Se um `state` parâmetro está incluído na solicitação, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se o `state` valores no pedido e resposta são idênticos. |

Respostas de erro também podem ser enviadas para o URI de redirecionamento para que a aplicação pode processar corretamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de código de erro que pode usar para classificar os tipos de erros que ocorrem. Também pode utilizar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |
| state |Consulte a descrição completa na tabela anterior. Se um `state` parâmetro está incluído na solicitação, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se o `state` valores no pedido e resposta são idênticos. |

## <a name="2-get-a-token"></a>2. Obter um token
Agora que comprou um código de autorização, poderá resgatá a `code` de um token para o recurso pretendido ao enviar um pedido POST para o `/token` ponto final. No Azure AD B2C, o único recurso que pode pedir um token para é a API web back-end da aplicação. A Convenção de que é utilizada para pedir um token a próprio é usar o ID de cliente da sua aplicação como o âmbito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| p |Necessário |O fluxo de utilizador que foi utilizado para adquirir o código de autorização. Não é possível utilizar um fluxo de utilizador diferente neste pedido. Observe que adicionar este parâmetro para o *cadeia de consulta*, não no corpo da mensagem. |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal do Azure](https://portal.azure.com). |
| grant_type |Necessário |O tipo de concessão. Para o fluxo de código de autorização, o tipo de concessão deve ser `authorization_code`. |
| scope |Recomendado |Uma lista de âmbitos separadas por espaços. Indica um valor de âmbito único para o Azure AD ambas as permissões que estão a ser solicitada. Com o ID de cliente como o âmbito indica que o aplicativo precisa de um token de acesso que pode ser utilizado em relação a seu próprio serviço ou a web API, representado pelo mesmo ID de cliente.  O `offline_access` âmbito indica que o aplicativo precisa de um token de atualização de longa duração acesso a recursos.  Também pode utilizar o `openid` âmbito para pedir um token de ID do Azure AD B2C. |
| code |Necessário |O código de autorização que obteve no leg primeiro do fluxo. |
| redirect_uri |Necessário |O URI da aplicação em que recebeu o código de autorização de redirecionamento. |

Uma resposta com êxito de token tem esta aparência:

```
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
| not_before |A hora em que o token é considerado válido, na hora "Epoch". |
| token_type |O valor de tipo de token. O único tipo que o Azure AD suporta é portador. |
| access_token |O assinado JSON Web Token (JWT) que pediu. |
| scope |Os âmbitos que o token é válido para. Também pode utilizar âmbitos para tokens de cache para utilização posterior. |
| expires_in |O período de tempo que o token é válido (em segundos). |
| refresh_token |Um token de atualização de OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens adicionais depois do atual expire. Tokens de atualização são vida longa. Pode usá-los para manter o acesso aos recursos por longos períodos de tempo. Para obter mais informações, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Respostas de erro tem esta aparência:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de código de erro que pode usar para classificar os tipos de erros que ocorrem. Também pode utilizar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |

## <a name="3-use-the-token"></a>3. Utilizar o token
Agora que adquirir um token de acesso com êxito, pode utilizar o token em pedidos para o back-end web APIs, incluindo-na `Authorization` cabeçalho:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Atualizar o token
Tokens de acesso e tokens de ID são curta duração. Depois de esta expira, tem de atualizar os utilizadores continuem a aceder aos recursos. Para fazer isso, enviar outro pedido POST para o `/token` ponto final. Desta vez, forneça o `refresh_token` em vez do `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&client_secret=JqQX2PNo9bpM0uEihUPzyrh&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| p |Necessário |O fluxo de utilizador que foi utilizado para adquirir o token de atualização original. Não é possível utilizar um fluxo de utilizador diferente neste pedido. Observe que adicionar este parâmetro para o *cadeia de consulta*, não no corpo da mensagem. |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal do Azure](https://portal.azure.com). |
| client_secret |Necessário |O client_secret associado à sua client_id no [portal do Azure](https://portal.azure.com). |
| grant_type |Necessário |O tipo de concessão. Para este vertente do fluxo de código de autorização, o tipo de concessão deve ser `refresh_token`. |
| scope |Recomendado |Uma lista de âmbitos separadas por espaços. Indica um valor de âmbito único para o Azure AD ambas as permissões que estão a ser solicitada. Com o ID de cliente como o âmbito indica que o aplicativo precisa de um token de acesso que pode ser utilizado em relação a seu próprio serviço ou a web API, representado pelo mesmo ID de cliente.  O `offline_access` âmbito indica que a aplicação terá um token de atualização de longa duração acesso a recursos.  Também pode utilizar o `openid` âmbito para pedir um token de ID do Azure AD B2C. |
| redirect_uri |Opcional |O URI da aplicação em que recebeu o código de autorização de redirecionamento. |
| refresh_token |Necessário |O token de atualização original que obteve no segundo leg do fluxo. |

Uma resposta com êxito de token tem esta aparência:

```
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
| not_before |A hora em que o token é considerado válido, na hora "Epoch". |
| token_type |O valor de tipo de token. O único tipo que o Azure AD suporta é portador. |
| access_token |O JWT assinado que pediu. |
| scope |Os âmbitos que o token é válido para. Também pode utilizar os âmbitos para tokens de cache para utilização posterior. |
| expires_in |O período de tempo que o token é válido (em segundos). |
| refresh_token |Um token de atualização de OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens adicionais depois do atual expire. Atualizar tokens são vida longa e pode ser utilizados para manter o acesso aos recursos por longos períodos de tempo. Para obter mais informações, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Respostas de erro tem esta aparência:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de código de erro que pode usar para classificar tipos de erros que ocorrem. Também pode utilizar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Utilizar o seu próprio diretório do Azure AD B2C
Para experimentar estes pedidos por conta própria, conclua os passos seguintes. Substitua os valores de exemplo, que utilizámos neste artigo com seus próprios valores.

1. [Criar um diretório do Azure AD B2C](active-directory-b2c-get-started.md). Utilize o nome do seu diretório nos pedidos.
2. [Criar uma aplicação](active-directory-b2c-app-registration.md) para obter um ID de aplicação e um URI de redirecionamento. Incluem um cliente nativo na sua aplicação.
3. [Criar os seus fluxos de utilizador](active-directory-b2c-reference-policies.md) para obter nomes de fluxo de seu usuário.

