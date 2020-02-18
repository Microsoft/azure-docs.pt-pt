---
title: Migrar aplicações e APIs para b2clogin.com
titleSuffix: Azure AD B2C
description: Aprenda a utilizar b2clogin.com nos seus URLs redirecionais para o Diretório Ativo Azure B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa1e6d8705cf4aed975ed0940087f243a06a9019
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2020
ms.locfileid: "77372691"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Desative URLs de redirecionamento para b2clogin.com para o Diretório Ativo Azure B2C

Quando configurar um fornecedor de identidade para o sign-up e o login na sua aplicação Azure Ative Directory B2C (Azure AD B2C), precisa especificar um URL de redirecionamento. Não deve continuar a fazer referência *login.microsoftonline.com* nas suas aplicações e APIs. Em vez disso, utilize *b2clogin.com* para todas as novas aplicações e emigrar as aplicações existentes de *login.microsoftonline.com* para *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Depreciação de login.microsoftonline.com

Em 04 de dezembro de 2019, anunciou-se a retirada prevista do apoio login.microsoftonline.com em Azure AD B2C em 04 de dezembro de **2020**:

[O Diretório Ativo Azure B2C está a depreciar login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

A depreciação do login.microsoftonline.com entra em vigor para todos os inquilinos do Azure AD B2C em 04 de dezembro de 2020, proporcionando aos inquilinos existentes um (1) ano para migrar para b2clogin.com. Os novos inquilinos criados após 04 de dezembro de 2019 não aceitarão pedidos de login.microsoftonline.com. Todas as funcionalidades permanecem as mesmas no ponto final b2clogin.com.

A depreciação da login.microsoftonline.com não afeta os inquilinos do Azure Ative Directory. Apenas os inquilinos do Azure Ative Directory B2C são afetados por esta mudança.

## <a name="benefits-of-b2clogincom"></a>Benefícios da b2clogin.com

Quando utiliza *b2clogin.com* como URL de redirecionamento:

* O espaço consumido no cabeçalho de cookies pelos serviços da Microsoft é reduzido.
* Os seus URLs redirecionais já não precisam de incluir uma referência à Microsoft.
* O código do lado do cliente JavaScript é suportado (atualmente em [pré-visualização)](user-flow-javascript-overview.md)em páginas personalizadas. Devido a restrições de segurança, os elementos do código JavaScript e do formulário HTML são removidos das páginas personalizadas se utilizar *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Visão geral das alterações necessárias

Existem várias modificações que poderá ser necessário fazer para migrar as suas aplicações para *b2clogin.com:*

* Altere o URL de redirecionamento nas aplicações do seu fornecedor de identidade para *b2clogin.com de*referência .
* Atualize as suas aplicações Azure AD B2C para utilizar *b2clogin.com* no fluxo de utilizador e referências de ponto final simbólico.
* Atualize quaisquer **Origens Permitidas** que tenha definido nas definições cors para [personalização](custom-policy-ui-customization.md)da interface do utilizador .

## <a name="change-identity-provider-redirect-urls"></a>Alterar urLs de redirecionamento do fornecedor de identidade

No site de cada fornecedor de identidade em que criou uma aplicação, altere todos os URLs confiáveis para redirecionar para `your-tenant-name.b2clogin.com` em vez de *login.microsoftonline.com*.

Existem dois formatos que pode utilizar para os seus URLs redirecionais b2clogin.com. O primeiro proporciona o benefício de não ter "Microsoft" em qualquer lugar do URL usando o ID do Inquilino (um GUID) em vez do nome de domínio do seu inquilino:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

A segunda opção utiliza o nome do seu domínio de inquilino sob a forma de `your-tenant-name.onmicrosoft.com`. Por exemplo:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Para ambos os formatos:

* Substitua `{your-tenant-name}` pelo nome do seu inquilino Azure AD B2C.
* Remova `/te` se existir no URL.

## <a name="update-your-applications-and-apis"></a>Atualize as suas aplicações e APIs

O código nas aplicações e APIs ativadas por Azure AD B2C pode referir-se a `login.microsoftonline.com` em vários locais. Por exemplo, o seu código pode ter referências aos fluxos de utilizador e pontos finais simbólicos. Atualize o seguinte a `your-tenant-name.b2clogin.com`de referência:

* Ponto final de autorização
* Ponto final de token
* Emitentes de token

Por exemplo, o ponto final da autoridade para a política de inscrição/inscrição de Contoso seria agora:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Para obter informações sobre aplicações web baseadas em OWIN para b2clogin.com, consulte [Migrate uma API web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).

Para migrar a API Management APIs protegida pelo Azure AD B2C, consulte a [emigração para b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) secção de [Secure a Azure API Management API com Azure AD B2C](secure-api-management.md).

## <a name="microsoft-authentication-library-msal"></a>Biblioteca de Autenticação da Microsoft (MSAL)

### <a name="validateauthority-property"></a>Propriedade ValidaAuthority

Se estiver a utilizar [MSAL.NET][msal-dotnet] v2 ou mais cedo, detete te a propriedade **ValidaAuthority** para `false` na instantânea do cliente para permitir que os redirecionamentos *b2clogin.com*. Esta definição não é necessária para MSAL.NET v3 ou superior.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Se estiver a utilizar [o MSAL para o JavaScript:][msal-js]

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre aplicações web baseadas em OWIN para b2clogin.com, consulte [Migrate uma API web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).

Para migrar a API Management APIs protegida pelo Azure AD B2C, consulte a [emigração para b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) secção de [Secure a Azure API Management API com Azure AD B2C](secure-api-management.md).

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
