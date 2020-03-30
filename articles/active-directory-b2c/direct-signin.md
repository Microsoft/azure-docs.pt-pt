---
title: Configurar o início de sessão direta utilizando o Diretório Ativo Azure B2C [ Microsoft Docs
description: Aprenda a pré-povoar o nome de inscrição ou redirecione diretamente para um fornecedor de identidade social.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a02ad3ea43ae9d91489417bc314e3c23d54a958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188771"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Configurar o acesso direto utilizando o Diretório Ativo Azure B2C

Ao configurar o início de sessão para a sua aplicação utilizando o Azure Ative Directory (AD) B2C, pode pré-povoar o nome de entrada ou iniciar sessão direta a um fornecedor específico de identidade social, como facebook, LinkedIn ou uma conta Microsoft.

## <a name="prepopulate-the-sign-in-name"></a>Pré-povoar o nome de inscrição

Durante uma viagem de utilizador de entrada, uma aplicação de parte de fiação pode visar um utilizador ou nome de domínio específico. Ao direcionar um utilizador, uma aplicação pode especificar, no pedido de autorização, o `login_hint` parâmetro de consulta com o nome de inscrição do utilizador. O Azure AD B2C preenche automaticamente o nome de entrada, enquanto o utilizador apenas precisa fornecer a palavra-passe.

![Inscreva-se na página com login_hint param de consulta em destaque no URL](./media/direct-signin/login-hint.png)

O utilizador é capaz de alterar o valor na caixa de texto de entrada.

Se estiver a usar uma política `SelfAsserted-LocalAccountSignin-Email` personalizada, anule o perfil técnico. Na `<InputClaims>` secção, detete teo Valor Predefinido da alegação signName para `{OIDC:LoginHint}`. A `{OIDC:LoginHint}` variável contém `login_hint` o valor do parâmetro. O Azure AD B2C lê o valor da reclamação do signName Name e pré-povoa a caixa de texto signNameName.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Redirecione o acesso a um prestador social

Se configurar a jornada de acesso à sua aplicação para incluir contas sociais, como `domain_hint` Facebook, LinkedIn ou Google, pode especificar o parâmetro. Este parâmetro de consulta fornece uma dica ao Azure AD B2C sobre o fornecedor de identidade social que deve ser utilizado para o início de sessão. Por exemplo, se a `domain_hint=facebook.com`aplicação especificar , o sessão vai diretamente para a página de acesso ao Facebook.

![Inscreva-se na página com domain_hint param de consulta em destaque no URL](./media/direct-signin/domain-hint.png)

Se estiver a utilizar uma política personalizada, pode `<Domain>domain name</Domain>` configurar o `<ClaimsProvider>`nome de domínio utilizando o elemento XML de qualquer .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


