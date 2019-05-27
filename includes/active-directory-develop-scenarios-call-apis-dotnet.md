---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 0196d39f5b131bc54e00412beb7fdf10b7352336
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121867"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Propriedades de authenticationresult que no MSAL.NET

Os métodos para adquirir tokens retornam uma `AuthenticationResult` (ou, para os métodos async, um `Task<AuthenticationResult>`.

No MSAL.NET, `AuthenticationResult` expõe:

- `AccessToken` para a API Web para aceder aos recursos. Este parâmetro é uma cadeia de caracteres, normalmente, um base64 codificado JWT, mas o cliente nunca deve ser dentro do token de acesso. O formato não é garantido permaneça estável e podem ser encriptados para o recurso. As pessoas escrever códigos, dependendo do conteúdo de token de acesso no cliente é uma das maiores origens de erros e quebras de lógica de cliente. Consulte também [tokens de acesso](../articles/active-directory/develop/access-tokens.md)
- `IdToken` para o usuário (este parâmetro é um JWT codificado). Consulte [Tokens de ID](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` Indica a data/hora quando o token expira
- `TenantId` contém o inquilino no qual o utilizador foi encontrado. Para os utilizadores convidados (cenários do Azure AD B2B), o ID de inquilino é o inquilino de convidado, o inquilino não exclusivo.
Quando o token é entregue para um utilizador, `AuthenticationResult` também contém informações sobre este utilizador. Para fluxos de cliente confidencial em que os tokens são solicitados com nenhum utilizador (para o aplicativo), estas informações de utilizador são nulas.
- O `Scopes` para que o token foi emitido.
- O Id exclusivo para o utilizador.

### <a name="iaccount"></a>IAccount

MSAL.NET define a noção de conta (por meio do `IAccount` interface). Esta alteração de última hora fornece a semântica de certa: o fato de que o mesmo utilizador pode ter várias contas, no Azure diferente diretórios AD. Também MSAL.NET fornece melhores informações no caso de cenários de convidado, são fornecidas informações de conta de raiz.
O diagrama seguinte mostra a estrutura do `IAccount` interface:

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

O `AccountId` classe identifica uma conta num inquilino específico. Ele tem as seguintes propriedades:

| Propriedade | Descrição |
|----------|-------------|
| `TenantId` | Uma representação de cadeia de caracteres para um GUID, que é o ID do inquilino onde reside a conta. |
| `ObjectId` | Uma representação de cadeia de caracteres para um GUID, que é o ID do utilizador que é proprietário da conta no inquilino. |
| `Identifier` | Identificador exclusivo para a conta. `Identifier` é a concatenação de `ObjectId` e `TenantId` separados por uma vírgula e são não codificada em base64. |

O `IAccount` interface representa informações sobre uma única conta. O mesmo utilizador pode estar presente em inquilinos diferentes, ou seja, um utilizador pode ter várias contas. Seus membros são:

| Propriedade | Descrição |
|----------|-------------|
| `Username` | Uma cadeia de caracteres que contém o valor visualizáveis no formato de UserPrincipalName (UPN), por exemplo, john.doe@contoso.com. Essa cadeia de caracteres pode ser nula, enquanto o HomeAccountId e HomeAccountId.Identifier não serão nula. Esta propriedade substitui o `DisplayableId` propriedade do `IUser` nas versões anteriores do MSAL.NET. |
| `Environment` | Uma cadeia de caracteres que contém o fornecedor de identidade para esta conta, por exemplo, `login.microsoftonline.com`. Esta propriedade substitui o `IdentityProvider` propriedade do `IUser`, exceto pelo fato de `IdentityProvider` também tinha informações sobre o inquilino (para além do ambiente de nuvem), ao passo que aqui o valor é apenas o anfitrião. |
| `HomeAccountId` | AccountId da conta do principal do utilizador. Esta propriedade identifica unicamente o usuário em inquilinos do Azure AD. |

### <a name="using-the-token-to-call-a-protected-api"></a>Usando o token para chamar uma API protegida

Uma vez a `AuthenticationResult` foi devolvido pelo MSAL (no `result`), terá de adicioná-lo para o cabeçalho de autorização de HTTP, antes de efetuar a chamada para aceder à API Web protegida.

```CSharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```