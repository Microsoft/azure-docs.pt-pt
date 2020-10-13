---
title: ficheiro de inclusão
description: ficheiro de inclusão
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76308939"
---
### <a name="authenticationresult-properties-in-msalnet"></a>AutenticaçãoRespretenha propriedades em MSAL.NET

Os métodos para adquirir fichas de retorno `AuthenticationResult` . Para métodos async, `Task<AuthenticationResult>` devoluções.

Em MSAL.NET, `AuthenticationResult` expõe:

- `AccessToken` para a API web aceder a recursos. Este parâmetro é uma corda, geralmente um JWT codificado base-64. O cliente nunca deve olhar dentro do token de acesso. O formato não é garantido para permanecer estável, e pode ser encriptado para o recurso. Escrever código que depende do acesso a conteúdos simbólicos no cliente é uma das maiores fontes de erros e quebras lógicas do cliente. Para mais informações, consulte [os tokens de acesso.](../articles/active-directory/develop/access-tokens.md)
- `IdToken` para o utilizador. Este parâmetro é um JWT codificado. Para mais informações, consulte [fichas de identificação.](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` diz a data e a hora em que o token expira.
- `TenantId` contém o inquilino no qual o utilizador foi encontrado. Para os utilizadores convidados em cenários B2B do Azure Ative Directory (Azure AD), o ID do inquilino é o inquilino convidado, não o inquilino único.
Quando o token é entregue para um utilizador, `AuthenticationResult` também contém informações sobre este utilizador. Para fluxos confidenciais de clientes onde são solicitadas fichas sem utilizador para a aplicação, esta informação do utilizador é nula.
- O `Scopes` para o qual o símbolo foi emitido.
- O ID único para o utilizador.

### <a name="iaccount"></a>IAconselho

MSAL.NET define a noção de uma conta através da `IAccount` interface. Esta mudança de rutura fornece a semântica certa. O mesmo utilizador pode ter várias contas, em diferentes diretórios AD do Azure. Além disso, MSAL.NET fornece melhor informação no caso dos cenários dos hóspedes porque a informação da conta de casa é fornecida.
O diagrama seguinte mostra a estrutura da `IAccount` interface.

![Estrutura de interface IAccount](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

A `AccountId` classe identifica uma conta num inquilino específico com os imóveis indicados na tabela seguinte.

| Propriedade | Descrição |
|----------|-------------|
| `TenantId` | Uma representação de cordas para um GUID, que é a identificação do inquilino onde a conta reside. |
| `ObjectId` | Uma representação de cordas para um GUID, que é a identificação do utilizador que possui a conta no arrendatário. |
| `Identifier` | Identificador único para a conta. `Identifier` é a concatenação `ObjectId` de e separada por uma `TenantId` vírgula. Não estão codificados na Base 64. |

A `IAccount` interface representa informação sobre uma única conta. O mesmo utilizador pode estar presente em diferentes inquilinos, o que significa que um utilizador pode ter várias contas. Os seus membros são apresentados na tabela seguinte.

| Propriedade | Descrição |
|----------|-------------|
| `Username` | Uma cadeia que contém o valor exibivel no formato UserPrincipalName (UPN), por exemplo, john.doe@contoso.com . Esta cadeia pode ser nula, ao contrário do HomeAccountId e do HomeAccountId.Identifier, que não será nulo. Esta propriedade substitui a `DisplayableId` propriedade de `IUser` em versões anteriores de MSAL.NET. |
| `Environment` | Uma cadeia que contém o fornecedor de identidade para esta conta, por exemplo, `login.microsoftonline.com` . Este imóvel substitui a `IdentityProvider` propriedade `IUser` de, exceto que `IdentityProvider` também tinha informações sobre o inquilino, além do ambiente em nuvem. Aqui, o valor é apenas o anfitrião. |
| `HomeAccountId` | A identificação da conta da casa para o utilizador. Esta propriedade identifica exclusivamente o utilizador através dos inquilinos Azure AD. |

### <a name="use-the-token-to-call-a-protected-api"></a>Use o símbolo para chamar uma API protegida

Depois de `AuthenticationResult` ser devolvido pela MSAL em , `result` adicione-o ao cabeçalho de autorização HTTP antes de esjoar a chamada para aceder à API web protegida.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```