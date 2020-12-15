---
title: Configurar o acesso direto usando o Azure Ative Directory B2C / Microsoft Docs
description: Saiba como pré-epopular o nome de inscrição ou redirecione diretamente para um provedor de identidade social.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f3b918fdf753cef75782a47ef157c282ef47e1ed
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503646"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Configurar o acesso direto usando o Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Ao iniciar a inscrição para a sua aplicação utilizando o Azure Ative Directory (AD) B2C, pode pré-fazer o pré-in ou iniciar sposição direta a um fornecedor específico de identidade social, como Facebook, LinkedIn ou uma conta Microsoft.

## <a name="prepopulate-the-sign-in-name"></a>Pré-apouular o nome de inscrição

Durante uma viagem de utilizador de entrada, uma aplicação de parte de gestão pode visar um utilizador ou nome de domínio específico. Ao direcionar um utilizador, uma aplicação pode especificar, no pedido de autorização, o `login_hint` parâmetro de consulta com o nome de entrada do utilizador. O Azure AD B2C povoa automaticamente o nome de entrada, enquanto o utilizador apenas necessita de fornecer a palavra-passe.

![Inscreva-se na página com login_hint param de consulta realçado em URL](./media/direct-signin/login-hint.png)

O utilizador é capaz de alterar o valor na caixa de texto de entrada de inscrição.

::: zone pivot="b2c-custom-policy"

Para suportar o parâmetro de sugestão de login, substitua o `SelfAsserted-LocalAccountSignin-Email` perfil técnico. Na `<InputClaims>` secção, defina o Valor Padrão da reivindicação do signInName para `{OIDC:LoginHint}` . A `{OIDC:LoginHint}` variável contém o valor do `login_hint` parâmetro. Azure AD B2C lê o valor da reivindicação do signInName e pré-povoa a caixa de texto signInName.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

## <a name="redirect-sign-in-to-a-social-provider"></a>Redirecione o s-in para um prestador social

Se configurar a jornada de entrada para a sua aplicação incluir contas sociais, como Facebook, LinkedIn ou Google, pode especificar o `domain_hint` parâmetro. Este parâmetro de consulta fornece uma dica ao Azure AD B2C sobre o fornecedor de identidade social que deve ser usado para iniciar sação. Por exemplo, se a aplicação especificar, o `domain_hint=facebook.com` sin-in vai diretamente para a página de entrada no Facebook.

![Inscreva-se na página com domain_hint param de consulta realçado em URL](./media/direct-signin/domain-hint.png)

::: zone pivot="b2c-user-flow"

O parâmetro de cadeia de sugestão de domínio pode definir-se para um dos seguintes domínios:

- amazon.com
- facebook.com
- github.com
- google.com
- linkedin.com
- microsoft.com
- qq.com
- twitter.com
- wechat.com
- weibo.com 
- Para [o Genérico OpenID Connect](identity-provider-generic-openid-connect.md), consulte a dica de [domínio](identity-provider-generic-openid-connect.md#response-mode).

::: zone-end

::: zone pivot="b2c-custom-policy"

Para suportar o parâmetro de dobragem de domínio, pode configurar o nome de domínio utilizando o `<Domain>domain name</Domain>` elemento XML de qualquer `<ClaimsProvider>` .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```

::: zone-end

