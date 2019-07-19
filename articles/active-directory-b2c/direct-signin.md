---
title: Configurar a entrada direta usando o Azure Active Directory B2C | Microsoft Docs
description: Saiba como preencher previamente o nome de entrada ou redirecionar diretamente para um provedor de identidade social.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8c0f3d8f3f49001e1326688ccc794e19d1148e5d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846895"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Configurar a entrada direta usando o Azure Active Directory B2C

Ao configurar a entrada para seu aplicativo usando o Azure Active Directory (AD) B2C, você pode pré-popular o nome de entrada ou a entrada direta para um provedor de identidade social específico, como Facebook, LinkedIn ou um conta Microsoft.

## <a name="prepopulate-the-sign-in-name"></a>Preencher previamente o nome de entrada

Durante um percurso do usuário de entrada, um aplicativo de terceira parte confiável pode ter como destino um nome de domínio ou usuário específico. Ao direcionar um usuário, um aplicativo pode especificar, na solicitação de autorização, `login_hint` o parâmetro de consulta com o nome de entrada do usuário. Azure AD B2C popula automaticamente o nome de entrada, enquanto o usuário só precisa fornecer a senha.

![Página de entrada de entrada com o parâmetro de consulta login_hint realçado na URL](./media/direct-signin/login-hint.png)

O usuário é capaz de alterar o valor na caixa de texto de entrada.

Se você estiver usando uma política personalizada, substitua o `SelfAsserted-LocalAccountSignin-Email` perfil técnico. Na seção, defina o DefaultValue da declaração signInName como `{OIDC:LoginHint}`. `<InputClaims>` A `{OIDC:LoginHint}` variável contém o valor `login_hint` do parâmetro. Azure AD B2C lê o valor da declaração signInName e popula previamente a caixa de texto signInName.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Redirecionar a entrada para um provedor social

Se você configurou a jornada de entrada para seu aplicativo para incluir contas sociais, como Facebook, LinkedIn ou Google, você pode especificar o `domain_hint` parâmetro. Esse parâmetro de consulta fornece uma dica para Azure AD B2C sobre o provedor de identidade social que deve ser usado para entrar. Por exemplo, se o aplicativo especificar `domain_hint=facebook.com`, a entrada vai diretamente para a página de entrada do Facebook.

![Página de entrada de entrada com o parâmetro de consulta domain_hint realçado na URL](./media/direct-signin/domain-hint.png)

Se você estiver usando uma política personalizada, poderá configurar o nome de domínio usando o `<Domain>domain name</Domain>` elemento XML de qualquer `<ClaimsProvider>`um.

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


