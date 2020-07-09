---
title: Solicite um token de acesso - Azure Ative Directy B2C / Microsoft Docs
description: Saiba como solicitar um token de acesso a partir do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: de5c478ac6641fe5b1e342c063d134f70084b2ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201451"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Solicite um token de acesso no Azure Ative Directory B2C

Um *token de acesso* contém alegações que pode usar no Azure Ative Directory B2C (Azure AD B2C) para identificar as permissões concedidas às suas APIs. Ao ligar para um servidor de recursos, um token de acesso deve estar presente no pedido HTTP. Um token de acesso é denotado como **access_token** nas respostas de Azure AD B2C.

Este artigo mostra-lhe como solicitar um token de acesso para uma aplicação web e API web. Para obter mais informações sobre fichas em Azure AD B2C, consulte a [visão geral dos tokens no Azure Ative Directory B2C](tokens-overview.md).

> [!NOTE]
> **As cadeias Web API (Em nome)não são suportadas pelo Azure AD B2C.** - Muitas arquiteturas incluem uma API web que precisa chamar outra API web a jusante, ambas protegidas pelo Azure AD B2C. Este cenário é comum em clientes que têm uma API web de volta, que por sua vez chama um outro serviço. Este cenário de API web acorrentado pode ser suportado utilizando a concessão de credencial do portador do portador da OAuth 2.0 JWT, também conhecida como o fluxo On-Behalf-Of. No entanto, o fluxo On-Behalf-Of não é atualmente implementado em Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

- [Crie um fluxo de utilizador](tutorial-create-user-flows.md) para permitir que os utilizadores se inscrevam e se inscrevam na sua aplicação.
- Se ainda não o fez, [adicione uma aplicação web API ao seu inquilino Azure Ative Directory B2C.](add-web-api-application.md)

## <a name="scopes"></a>Âmbitos

Os âmbitos fornecem uma forma de gerir permissões para recursos protegidos. Quando um token de acesso é solicitado, a aplicação do cliente precisa de especificar as permissões desejadas no parâmetro de **âmbito** do pedido. Por exemplo, para especificar o Valor de **Âmbito** da `read` API que tem a App **ID URI** de `https://contoso.onmicrosoft.com/api` , o âmbito seria `https://contoso.onmicrosoft.com/api/read` .

São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, os utilizadores da API Web podem ter acesso de leitura e escrita ou podem ter apenas acesso só de leitura. Para adquirir várias permissões no mesmo pedido, pode adicionar múltiplas entradas no parâmetro de **âmbito** único do pedido, separado por espaços.

O exemplo a seguir mostra os âmbitos descodificados num URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

O exemplo a seguir mostra os âmbitos codificados num URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Se solicitar mais âmbitos do que o concedido para o seu pedido de cliente, a chamada terá sucesso se pelo menos uma permissão for concedida. A alegação **do SCP** no token de acesso resultante é povoada apenas com as permissões que foram concedidas com sucesso. A norma OpenID Connect especifica vários valores especiais de âmbito. Os seguintes âmbitos representam a permissão para aceder ao perfil do utilizador:

- **openid** - Solicita um sinal de identificação.
- **offline_access** - Solicita um token de atualização utilizando [fluxos do Código Auth](authorization-code-flow.md).

Se o **parâmetro response_type** num `/authorize` pedido `token` incluir, o parâmetro de **âmbito** deve incluir pelo menos um âmbito de recursos diferente e que `openid` será `offline_access` concedido. Caso contrário, o `/authorize` pedido falha.

## <a name="request-a-token"></a>Pedir um token

Para solicitar um sinal de acesso, precisa de um código de autorização. Abaixo está um exemplo de um pedido ao `/authorize` ponto final para um código de autorização. Os domínios personalizados não são suportados para utilização com fichas de acesso. Utilize o seu domínio tenant-name.onmicrosoft.com no URL de pedido.

No exemplo seguinte, substitui-se estes valores:

- `<tenant-name>`- O nome do seu inquilino Azure AD B2C.
- `<policy-name>`- O nome da sua política personalizada ou fluxo de utilizador.
- `<application-ID>`- O identificador de aplicações da aplicação web que registou para suportar o fluxo do utilizador.
- `<redirect-uri>`- O **URI de redirecionamento** que inseriu quando registou o pedido de cliente.

```http
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

A resposta com o código de autorização deve ser semelhante a este exemplo:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Depois de receber com sucesso o código de autorização, pode usá-lo para solicitar um token de acesso:

```http
POST <tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Deve ver algo semelhante à seguinte resposta:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

Ao utilizar https://jwt.ms para examinar o token de acesso que foi devolvido, deve ver algo semelhante ao seguinte exemplo:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Próximos passos

- Saiba como [configurar fichas em Azure AD B2C](configure-tokens.md)
