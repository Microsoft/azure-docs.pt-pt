---
title: Migrar aplicações e APIs para b2clogin.com
titleSuffix: Azure AD B2C
description: Saiba como utilizar b2clogin.com nos urls de redirecionamento para O Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4297ee64742b81e86eb8b85c0a6c405fac07d67f
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85386169"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Desa redirecione os URLs para b2clogin.com para o Azure Ative Directory B2C

Quando configurar um fornecedor de identidade para a inscrição e inscrição na sua aplicação Azure Ative Directory B2C (Azure AD B2C), tem de especificar um URL de redirecionamento. Não deve mais fazer referência *login.microsoftonline.com* nas suas aplicações e APIs. Em vez disso, utilize *b2clogin.com* para todas as novas aplicações e migrar as aplicações existentes de *login.microsoftonline.com* para *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Depreciação de login.microsoftonline.com

Em 04 de dezembro de 2019, anunciamos a retirada prevista do login.microsoftonline.com apoio em Azure AD B2C em **04 de dezembro de 2020**:

[O Azure Ative Directory B2C está a depreciar login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

A depreciação de login.microsoftonline.com entra em vigor para todos os inquilinos do Azure AD B2C em 04 de dezembro de 2020, proporcionando aos inquilinos existentes um (1) ano para migrarem para b2clogin.com. Os novos inquilinos criados após 04 de dezembro de 2019 não aceitarão pedidos de login.microsoftonline.com. Todas as funcionalidades permanecem as mesmas no b2clogin.com ponto final.

A depreciação de login.microsoftonline.com não afeta os inquilinos do Azure Ative Directory. Apenas os inquilinos do Azure Ative Directory B2C são afetados por esta mudança.

## <a name="benefits-of-b2clogincom"></a>Benefícios da b2clogin.com

Quando usa *b2clogin.com* como URL de redirecionamento:

* O espaço consumido no cabeçalho de cookies pelos serviços da Microsoft é reduzido.
* Os seus URLs de redirecionamento já não precisam de incluir uma referência à Microsoft.
* O código do lado do cliente JavaScript é suportado (atualmente em [pré-visualização)](user-flow-javascript-overview.md)em páginas personalizadas. Devido às restrições de segurança, os elementos de código JavaScript e HTML são removidos das páginas personalizadas se utilizar *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Visão geral das alterações necessárias

Existem várias modificações que poderá ter de fazer para migrar as suas aplicações para *b2clogin.com:*

* Altere o URL de redirecionamento nas aplicações do seu fornecedor de identidade para *b2clogin.com*de referência .
* Atualize as suas aplicações Azure AD B2C para utilizar *b2clogin.com* no fluxo de utilizador e referências de ponto final simbólico.
* Atualize quaisquer **Origens Permitidas** que tenha definido nas definições DO CORS para [personalização da interface do utilizador](custom-policy-ui-customization.md).

## <a name="change-identity-provider-redirect-urls"></a>Alterar urls de fornecedor de identidade

No site de cada fornecedor de identidade no qual criou uma aplicação, altere todos os URLs fidedignos para redirecionar para `your-tenant-name.b2clogin.com` em vez de *login.microsoftonline.com*.

Existem dois formatos que pode usar para os seus URLs de b2clogin.com redirecionamento. O primeiro proporciona o benefício de não ter "Microsoft" aparecendo em qualquer lugar do URL usando o ID do inquilino (a GUID) no lugar do nome de domínio do seu inquilino:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

A segunda opção utiliza o nome de domínio do seu inquilino sob a forma de `your-tenant-name.onmicrosoft.com` . Por exemplo:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Para ambos os formatos:

* `{your-tenant-name}`Substitua-o pelo nome do seu inquilino Azure AD B2C.
* Remova `/te` se existir na URL.

## <a name="update-your-applications-and-apis"></a>Atualize as suas aplicações e APIs

O código nas suas aplicações ativadas a Ad B2C e APIs podem referir-se `login.microsoftonline.com` em vários locais. Por exemplo, o seu código pode ter referências aos fluxos do utilizador e pontos finais simbólicos. Atualizar o seguinte para, em vez disso, fazer `your-tenant-name.b2clogin.com` referência:

* Ponto final de autorização
* Ponto final token
* Emitente token

Por exemplo, o ponto final da autoridade para a política de inscrição/inscrição de Contoso seria agora:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Para obter informações sobre a migração de aplicações web baseadas em OWIN para b2clogin.com, consulte [a Migração de uma API web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).

Para migrar APIs de Gestão AZure API protegidos pelo Azure AD B2C, consulte a secção [Migração para b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) de [Secure a API Management API API com Azure AD B2C](secure-api-management.md).

## <a name="microsoft-authentication-library-msal"></a>Biblioteca de Autenticação da Microsoft (MSAL)

### <a name="validateauthority-property"></a>Validar propriedade de propriedade de propriedade de propriedade de propriedade de propriedade

Se estiver a utilizar [MSAL.NET][msal-dotnet] v2 ou mais cedo, defina a propriedade **ValidateAuthority** `false` na instantânea do cliente para permitir redirecionamentos para *b2clogin.com*. Esta definição não é necessária para MSAL.NET v3 e acima.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Se estiver a utilizar o [MSAL para o JavaScript:][msal-js]

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

Para obter informações sobre a migração de aplicações web baseadas em OWIN para b2clogin.com, consulte [a Migração de uma API web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).

Para migrar APIs de Gestão AZure API protegidos pelo Azure AD B2C, consulte a secção [Migração para b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) de [Secure a API Management API API com Azure AD B2C](secure-api-management.md).

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
