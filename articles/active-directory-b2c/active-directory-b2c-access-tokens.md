---
title: Pedir um token de acesso - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como solicitar um token de acesso do Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1a545f1e0fd1360d9147280454fb8b75bf216152
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507382"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Pedir um token de acesso no Azure Active Directory B2C

Uma *token de acesso* contém declarações que pode utilizar no Azure Active Directory (Azure AD) B2C para identificar as permissões concedidas às suas APIs. Ao chamar um servidor de recurso, um token de acesso tem de estar presente na solicitação HTTP. Um token de acesso é denotado como **access_token** nas respostas do Azure AD B2C. 

Este artigo mostra-lhe como solicitar um token de acesso para um aplicativo web e a web API. Para obter mais informações sobre tokens no Azure AD B2C, consulte a [descrição geral de tokens no Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> **Cadeias de API da Web (em-nome-de) não é suportado pelo Azure AD B2C.** -Muitas arquiteturas incluem uma API web que precisa chamar outra API web a jusante, ambas protegidas pelo Azure AD B2C. Este cenário é comum em clientes que tenham um web API back-end, que por sua vez chama um outro serviço. Este cenário de API web em cadeia pode ser suportado utilizando a concessão de credencial de portador do OAuth 2.0 JWT, também conhecida como o fluxo em-nome-de. No entanto, o fluxo em-nome-de atualmente não é implementado no Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar um fluxo de utilizador](tutorial-create-user-flows.md) para permitir aos utilizadores inscrever-se e iniciar sessão na sua aplicação.
- Se ainda não fez isso, [adicionar uma web de aplicação de API com o seu inquilino do Azure Active Directory B2C](add-web-application.md).

## <a name="scopes"></a>Âmbitos

Âmbitos oferecem uma forma de gerir permissões para recursos protegidos. Quando é solicitado um token de acesso, a aplicação cliente tem de especificar as permissões pretendidas no **âmbito** parâmetro do pedido. Por exemplo, para especificar o **valor do âmbito** de `read` para a API com o **App ID URI** de `https://contoso.onmicrosoft.com/api`, o âmbito seria `https://contoso.onmicrosoft.com/api/read`.

São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, os utilizadores da API Web podem ter acesso de leitura e escrita ou podem ter apenas acesso só de leitura. Para adquirir várias permissões no mesmo pedido, pode adicionar várias entradas no único **âmbito** parâmetro do pedido, separado por espaços.

O exemplo seguinte mostra os âmbitos descodificados num URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

O exemplo seguinte mostra os âmbitos codificados numa URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Se pedir mais âmbitos do que as concedidas para a sua aplicação de cliente, a chamada terá êxito se pelo menos uma permissão for concedida. O **scp** afirmações no token de acesso resultante é preenchida com apenas as permissões que foram concedidas com êxito. O padrão de OpenID Connect especifica vários valores de âmbito especial. Os seguintes âmbitos representam a permissão para aceder ao perfil do usuário:

- **openid** -pede um token de ID.
- **offline_access** -um através do token de atualização de pedidos [fluxos de código de autenticação](active-directory-b2c-reference-oauth-code.md).

Se o **response_type** parâmetro numa `/authorize` pedido inclui `token`, o **âmbito** parâmetro tem de incluir o âmbito de pelo menos um recurso diferente de `openid` e `offline_access`que vai ser concedida. Caso contrário, o `/authorize` pedido falhar.

## <a name="request-a-token"></a>Solicitar um token

Para pedir um token de acesso, terá de um código de autorização. Segue-se um exemplo de um pedido para o `/authorize` ponto final para um código de autorização. Domínios personalizados não são suportados para utilização com tokens de acesso. Utilize o seu domínio de inquilino name.onmicrosoft.com no URL do pedido.

No exemplo seguinte, substitua estes valores:

- `<tenant-name>` -O nome do inquilino do Azure AD B2C.
- `<policy-name>` -O nome do seu fluxo de utilizador ou de política personalizado.
- `<application-ID>` -O identificador da aplicação da aplicação web que registrados para suportar o fluxo de utilizador.
- `<redirect-uri>` -O **URI de redirecionamento** que introduziu quando registou a aplicação cliente.

```
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code 
```

A resposta com o código de autorização deve ser semelhante a este exemplo:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Depois de receber com êxito o código de autorização, pode usá-lo para pedir um token de acesso:

```
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: https://<tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Deverá ver algo semelhante à seguinte resposta:

```
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

Quando utilizar https://jwt.ms para examinar o token de acesso que foi devolvido, deverá ver algo semelhante ao seguinte exemplo:

```
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

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como [configurar tokens no Azure AD B2C](configure-tokens.md)
