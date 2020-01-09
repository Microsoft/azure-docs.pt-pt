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
ms.openlocfilehash: 4e01dbb0036761215a9a05c464b20ead340a2e3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423768"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Propriedades de AuthenticationResult em MSAL.NET

Os métodos para adquirir tokens retornam um `AuthenticationResult` (ou, para os métodos assíncronos, um `Task<AuthenticationResult>`.

No MSAL.NET, o `AuthenticationResult` expõe:

- `AccessToken` para a API da Web acessar recursos. Esse parâmetro é uma cadeia de caracteres, geralmente um JWT codificado em base64, mas o cliente nunca deve olhar dentro do token de acesso. Não há garantia de que o formato permaneça estável e possa ser criptografado para o recurso. As pessoas que escrevem código dependendo do conteúdo do token de acesso no cliente são uma das maiores fontes de erros e quebras lógicas do cliente. Consulte também [tokens de acesso](../articles/active-directory/develop/access-tokens.md)
- `IdToken` para o usuário (esse parâmetro é um JWT codificado). Consulte [tokens de ID](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` informa a data/hora em que o token expira
- `TenantId` contém o locatário no qual o usuário foi encontrado. Para usuários convidados (cenários B2B do Azure AD), a ID do locatário é o locatário convidado, não o locatário exclusivo.
Quando o token é entregue para um usuário, `AuthenticationResult` também contém informações sobre esse usuário. Para fluxos de cliente confidenciais em que os tokens são solicitados sem usuário (para o aplicativo), essas informações de usuário são nulas.
- O `Scopes` para o qual o token foi emitido.
- A ID exclusiva para o usuário.

### <a name="iaccount"></a>IAccount

MSAL.NET define a noção de Account (por meio da interface `IAccount`). Essa alteração significativa fornece a semântica correta: o fato de que o mesmo usuário pode ter várias contas, em diferentes diretórios do Azure AD. Além disso, o MSAL.NET fornece informações melhores no caso de cenários de convidado, pois as informações da conta residencial são fornecidas.
O diagrama a seguir mostra a estrutura da interface `IAccount`:

![imagem](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

A classe `AccountId` identifica uma conta em um locatário específico. Ele tem as seguintes propriedades:

| Propriedade | Descrição |
|----------|-------------|
| `TenantId` | Uma representação de cadeia de caracteres para um GUID, que é a ID do locatário onde a conta reside. |
| `ObjectId` | Uma representação de cadeia de caracteres para um GUID, que é a ID do usuário que possui a conta no locatário. |
| `Identifier` | Identificador exclusivo da conta. `Identifier` é a concatenação de `ObjectId` e `TenantId` separados por uma vírgula e não são codificados em base64. |

A interface `IAccount` representa informações sobre uma única conta. O mesmo usuário pode estar presente em locatários diferentes, ou seja, um usuário pode ter várias contas. Seus membros são:

| Propriedade | Descrição |
|----------|-------------|
| `Username` | Uma cadeia de caracteres que contém o valor exibível no formato UserPrincipalName (UPN), por exemplo, john.doe@contoso.com. Essa cadeia de caracteres pode ser nula, enquanto o HomeAccountId e o HomeAccountId. Identifier não serão nulos. Essa propriedade substitui a propriedade `DisplayableId` de `IUser` nas versões anteriores do MSAL.NET. |
| `Environment` | Uma cadeia de caracteres que contém o provedor de identidade para essa conta, por exemplo, `login.microsoftonline.com`. Essa propriedade substitui a propriedade `IdentityProvider` de `IUser`, exceto que `IdentityProvider` também tinha informações sobre o locatário (além do ambiente de nuvem), enquanto aqui o valor é apenas o host. |
| `HomeAccountId` | AccountId da conta principal do usuário. Essa propriedade identifica exclusivamente o usuário em locatários do Azure AD. |

### <a name="using-the-token-to-call-a-protected-api"></a>Usando o token para chamar uma API protegida

Depois que o `AuthenticationResult` tiver sido retornado por MSAL (em `result`), você precisará adicioná-lo ao cabeçalho de autorização HTTP, antes de fazer a chamada para acessar a API Web protegida.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```