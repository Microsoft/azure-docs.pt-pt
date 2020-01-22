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
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76308939"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Propriedades de AuthenticationResult em MSAL.NET

Os métodos para adquirir tokens retornam `AuthenticationResult`. Para métodos assíncronos, `Task<AuthenticationResult>` retorna.

No MSAL.NET, o `AuthenticationResult` expõe:

- `AccessToken` para a API da Web acessar recursos. Esse parâmetro é uma cadeia de caracteres, geralmente um JWT codificado em base 64. O cliente nunca deve olhar para dentro do token de acesso. O formato não tem garantia de permanecer estável e pode ser criptografado para o recurso. Escrever código que depende do conteúdo do token de acesso no cliente é uma das maiores fontes de erros e quebras lógicas do cliente. Para obter mais informações, consulte [tokens de acesso](../articles/active-directory/develop/access-tokens.md).
- `IdToken` para o usuário. Esse parâmetro é um JWT codificado. Para obter mais informações, consulte [tokens de ID](../articles/active-directory/develop/id-tokens.md).
- `ExpiresOn` informa a data e a hora em que o token expira.
- `TenantId` contém o locatário no qual o usuário foi encontrado. Para usuários convidados em cenários B2B do Azure Active Directory (Azure AD), a ID do locatário é o locatário convidado, não o locatário exclusivo.
Quando o token é entregue para um usuário, `AuthenticationResult` também contém informações sobre esse usuário. Para fluxos de cliente confidenciais em que os tokens são solicitados sem usuário para o aplicativo, essas informações de usuário são nulas.
- O `Scopes` para o qual o token foi emitido.
- A ID exclusiva para o usuário.

### <a name="iaccount"></a>IAccount

MSAL.NET define a noção de uma conta por meio da interface `IAccount`. Essa alteração significativa fornece a semântica correta. O mesmo usuário pode ter várias contas, em diretórios diferentes do Azure AD. Além disso, o MSAL.NET fornece informações melhores no caso de cenários de convidado, pois as informações da conta doméstica são fornecidas.
O diagrama a seguir mostra a estrutura da interface `IAccount`.

![Estrutura de interface IAccount](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

A classe `AccountId` identifica uma conta em um locatário específico com as propriedades mostradas na tabela a seguir.

| Propriedade | Descrição |
|----------|-------------|
| `TenantId` | Uma representação de cadeia de caracteres para um GUID, que é a ID do locatário onde a conta reside. |
| `ObjectId` | Uma representação de cadeia de caracteres para um GUID, que é a ID do usuário que possui a conta no locatário. |
| `Identifier` | Identificador exclusivo da conta. `Identifier` é a concatenação de `ObjectId` e `TenantId` separados por uma vírgula. Eles não são codificados em base 64. |

A interface `IAccount` representa informações sobre uma única conta. O mesmo usuário pode estar presente em locatários diferentes, o que significa que um usuário pode ter várias contas. Seus membros são mostrados na tabela a seguir.

| Propriedade | Descrição |
|----------|-------------|
| `Username` | Uma cadeia de caracteres que contém o valor de exibição no formato UserPrincipalName (UPN), por exemplo, john.doe@contoso.com. Essa cadeia de caracteres pode ser nula, ao contrário de HomeAccountId e HomeAccountId. Identifier, que não será nulo. Essa propriedade substitui a propriedade `DisplayableId` de `IUser` nas versões anteriores do MSAL.NET. |
| `Environment` | Uma cadeia de caracteres que contém o provedor de identidade para essa conta, por exemplo, `login.microsoftonline.com`. Essa propriedade substitui a propriedade `IdentityProvider` de `IUser`, exceto que `IdentityProvider` também tinha informações sobre o locatário, além do ambiente de nuvem. Aqui, o valor é apenas o host. |
| `HomeAccountId` | A ID da conta da página inicial do usuário. Essa propriedade identifica exclusivamente o usuário em locatários do Azure AD. |

### <a name="use-the-token-to-call-a-protected-api"></a>Usar o token para chamar uma API protegida

Depois que `AuthenticationResult` for retornado por MSAL em `result`, adicione-o ao cabeçalho de autorização HTTP antes de fazer a chamada para acessar a API Web protegida.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```