---
title: Pedir tokens de acesso - Azure Active Directory B2C | Microsoft Docs
description: Saiba como pedir um token de acesso do Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/26/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a633a7656a287c0b8df050b6d14afb73c27a7460
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382148"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Pedir tokens de acesso no Azure Active Directory B2C

Os *tokens de acesso* contêm afirmações que podem ser utilizadas no Azure Active Directory B2C (Azure AD B2C) para identificar as permissões concedidas às suas APIs. Ao chamar um servidor de recursos, tem de estar presente u token de acesso no pedido HTTP. Nas respostas do Azure AD B2C, os tokens de acesso são denotados como **access_token**.

Este artigo mostra-lhe como pedir um token de acesso para uma aplicação Web e uma API Web. Para obter mais informações sobre os tokens no Azure AD B2C, veja a [descrição geral dos tokens no Azure Active Directory B2C](tokens-overview.md).

> [!NOTE]
> **O Azure AD B2C não suporta cadeias de API Web (on-behalf-of).** - Muitas arquiteturas incluem uma API Web que precisa chamar outra API Web a jusante, estando ambas protegidas pelo Azure AD B2C. Este cenário é comum nos clientes que têm um back-end de API Web, o qual, por sua vez, chama outro serviço. Este cenário de API Web em cadeia pode ser suportado mediante a utilização da concessão de credencial de portador do OAuth 2.0 JWT, também conhecida como fluxo "on-behalf-of". No entanto, o fluxo "on-behalf-of" não está implementado no Azure AD B2C neste momento.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar um fluxo de utilizador](tutorial-create-user-flows.md) para permitir que os utilizadores se inscrevam e iniciem sessão na sua aplicação.
- Se ainda não o tiver feito, [adicione uma aplicação API ao seu inquilino do Azure Active Directory B2C](add-web-api-application.md).

## <a name="scopes"></a>Âmbitos

Os âmbitos proporcionam uma forma de gerir as permissões a recursos protegidos. Quando é pedido um token de acesso, a aplicação cliente tem de especificar as permissões desejadas no parâmetro **scope** do pedido. Por exemplo, para especificar o **Valor de Âmbito** de `read` da API que tem o **URI de ID de Aplicação** de `https://contoso.onmicrosoft.com/api`, o âmbito deve ser `https://contoso.onmicrosoft.com/api/read`.

São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, os utilizadores da API Web podem ter acesso de leitura e escrita ou podem ter apenas acesso só de leitura. Para adquirir múltiplas permissões no mesmo pedido, pode adquirir várias entradas no parâmetro **scope** individual do pedido, separado por espaços.

O exemplo seguinte mostra os âmbitos descodificados num URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

O exemplo seguinte mostra os âmbitos codificados num URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Se pedir mais âmbitos do que aqueles que forem concedidos à sua aplicação cliente, a chamada é bem-sucedida se for concedida pelo menos uma permissão. .A afirmação **scp** no token de acesso resultante é preenchida apenas com as permissões que foram devidamente concedidas. 

### <a name="openid-connect-scopes"></a>Âmbitos de ligação OpenID

A norma OpenID Connect especifica vários valores de âmbitos especiais. Os âmbitos seguintes representam a permissão para aceder ao perfil do utilizador:

- **openid** - pede um token de ID.
- **offline_access** - peça um token de atualização com os [fluxos de Código de Autorização](authorization-code-flow.md).
- **0000000-0000-0000-0000-000000000000000000000** - Usando o ID do cliente como o âmbito indica que a sua aplicação precisa de um token de acesso que pode ser usado contra o seu próprio serviço ou API web, representado pelo mesmo ID do cliente.

Se o parâmetro **response_type** num pedido `/authorize` incluir `token`, o parâmetro **scope** tem de incluir pelo menos um âmbito de recurso que não `openid` e `offline_access` que vão ser concedidos. Caso contrário, o pedido `/authorize` falha.

## <a name="request-a-token"></a>Pedir tokens

Para pedir tokens de acesso, precisa de um código de autorização. Pode ver um exemplo de um pedido ao ponto final `/authorize` para um código de autorização. Não é suportada a utilização de domínios personalizados com tokens de acesso. Utilize o domínio tenant-name.onmicrosoft.com no URL do pedido.

No exemplo seguinte, vai substituir estes valores:

- `<tenant-name>` - o nome do inquilino do Azure AD B2C.
- `<policy-name>` - o nome da política personalizada ou do fluxo de utilizador.
- `<application-ID>` - o identificador da aplicação Web que registou para suportar o fluxo de utilizador.
- `<application-ID-URI>` - O identificador de aplicações URI que definiu no Âmbito da Expor uma lâmina **API** da aplicação do cliente.
- `<scope-name>` - O nome do âmbito que adicionou ao abrigo **da Exposição de uma** lâmina API da aplicação do cliente.
- `<redirect-uri>` - o **URI de redirecionamento** que introduziu quando registou a aplicação cliente.

```http
GET https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=<application-ID-URI>/<scope-name>
&response_type=code
```

A resposta com o código de autorização deve ser semelhante à deste exemplo:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Depois de receber o código de autorização, pode utilizá-lo para pedir um token de acesso:

```http
POST <tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=<application-ID-URI>/<scope-name>
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Deverá ver algo semelhante à resposta seguinte:

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

Quando utilizar https://jwt.ms para examinar o token de acesso que foi devolvido, deverá ver algo semelhante ao exemplo seguinte:

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

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como [configurar os tokens no Azure AD B2C](configure-tokens.md)
