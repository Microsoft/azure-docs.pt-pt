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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76308939"
---
### <a name="authenticationresult-properties-in-msalnet"></a>AutenticaçãoResulta propriedades em MSAL.NET

Os métodos para adquirir `AuthenticationResult`fichas devolvem. Para métodos `Task<AuthenticationResult>` de asincronização, devoluções.

Em MSAL.NET, `AuthenticationResult` expõe:

- `AccessToken`para a Web API aceder a recursos. Este parâmetro é uma corda, geralmente um JWT codificado pela Base-64. O cliente nunca deve olhar para dentro do sinal de acesso. O formato não é garantido para permanecer estável, e pode ser encriptado para o recurso. Escrever código que depende do conteúdo simbólico de acesso ao cliente é uma das maiores fontes de erros e quebras de lógica do cliente. Para mais informações, consulte [fichas](../articles/active-directory/develop/access-tokens.md)de acesso .
- `IdToken`para o utilizador. Este parâmetro é um JWT codificado. Para mais informações, consulte [fichas de identificação](../articles/active-directory/develop/id-tokens.md).
- `ExpiresOn`conta a data e a hora quando o token expira.
- `TenantId`contém o inquilino em que o utilizador foi encontrado. Para os utilizadores convidados em cenários B2B do Azure Ative Directory (Azure AD), o ID do inquilino é o inquilino convidado, não o inquilino único.
Quando o token é entregue `AuthenticationResult` para um utilizador, também contém informações sobre este utilizador. Para fluxos confidenciais de clientes onde são solicitados tokens sem utilizador para a aplicação, esta informação do utilizador é nula.
- O `Scopes` para o qual o símbolo foi emitido.
- A identificação única para o utilizador.

### <a name="iaccount"></a>IAccount

MSAL.NET define a noção de `IAccount` uma conta através da interface. Esta mudança de rutura fornece a semântica certa. O mesmo utilizador pode ter várias contas, em diferentes diretórios da Azure AD. Além disso, MSAL.NET fornece melhores informações no caso dos cenários dos hóspedes porque a informação sobre a conta doméstica é fornecida.
O diagrama seguinte mostra `IAccount` a estrutura da interface.

![Estrutura de interface IAccount](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

A `AccountId` classe identifica uma conta num inquilino específico com os imóveis apresentados na tabela seguinte.

| Propriedade | Descrição |
|----------|-------------|
| `TenantId` | Uma representação de cordas para um GUID, que é a identificação do inquilino onde a conta reside. |
| `ObjectId` | Uma representação de cordas para um GUID, que é a identificação do utilizador que é dono da conta no inquilino. |
| `Identifier` | Identificador único para a conta. `Identifier`é a concatenação de `ObjectId` e `TenantId` separado por uma vírcula. Não estão codificados na Base 64. |

A `IAccount` interface representa informações sobre uma única conta. O mesmo utilizador pode estar presente em diferentes inquilinos, o que significa que um utilizador pode ter várias contas. Os seus membros são mostrados na tabela seguinte.

| Propriedade | Descrição |
|----------|-------------|
| `Username` | Uma cadeia que contém o valor visualizado no formato UserPrincipalName (UPN), por exemplo, john.doe@contoso.com. Esta cadeia pode ser nula, ao contrário do HomeAccountId e do HomeAccountId.Identificador, que não será nulo. Esta propriedade substitui `DisplayableId` a `IUser` propriedade de em versões anteriores de MSAL.NET. |
| `Environment` | Uma cadeia que contém o fornecedor de `login.microsoftonline.com`identidade para esta conta, por exemplo, . Este imóvel `IdentityProvider` substitui `IUser`o imóvel de, exceto que `IdentityProvider` também tinha informações sobre o inquilino, além do ambiente de nuvem. Aqui, o valor é apenas o anfitrião. |
| `HomeAccountId` | A identificação da conta da conta do utilizador. Esta propriedade identifica exclusivamente o utilizador através dos inquilinos da Azure AD. |

### <a name="use-the-token-to-call-a-protected-api"></a>Use o símbolo para chamar uma API protegida

Depois `AuthenticationResult` de devolvido pela `result`MSAL, adicione-o ao cabeçalho de autorização http antes de efetuar a chamada para aceder à API da web protegida.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```