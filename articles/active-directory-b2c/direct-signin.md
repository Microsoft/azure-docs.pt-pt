---
title: Configurar o acesso direto usando o Azure Ative Directory B2C / Microsoft Docs
description: Saiba como pré-epopular o nome de inscrição ou redirecione diretamente para um provedor de identidade social.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9e7c537e85039675f27fa3e276b6b964ce1679b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388600"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Configurar o acesso direto usando o Azure Ative Directory B2C

Ao iniciar a inscrição para a sua aplicação utilizando o Azure Ative Directory (AD) B2C, pode pré-fazer o pré-in ou iniciar sposição direta a um fornecedor específico de identidade social, como Facebook, LinkedIn ou uma conta Microsoft.

## <a name="prepopulate-the-sign-in-name"></a>Pré-apouular o nome de inscrição

Durante uma viagem de utilizador de entrada, uma aplicação de parte de gestão pode visar um utilizador ou nome de domínio específico. Ao direcionar um utilizador, uma aplicação pode especificar, no pedido de autorização, o `login_hint` parâmetro de consulta com o nome de entrada do utilizador. O Azure AD B2C povoa automaticamente o nome de entrada, enquanto o utilizador apenas necessita de fornecer a palavra-passe.

![Inscreva-se na página com login_hint param de consulta realçado em URL](./media/direct-signin/login-hint.png)

O utilizador é capaz de alterar o valor na caixa de texto de entrada de inscrição.

Se estiver a utilizar uma política personalizada, substitua o `SelfAsserted-LocalAccountSignin-Email` perfil técnico. Na `<InputClaims>` secção, defina o Valor Padrão da reivindicação do signInName para `{OIDC:LoginHint}` . A `{OIDC:LoginHint}` variável contém o valor do `login_hint` parâmetro. Azure AD B2C lê o valor da reivindicação do signInName e pré-povoa a caixa de texto signInName.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Redirecione o s-in para um prestador social

Se configurar a jornada de entrada para a sua aplicação incluir contas sociais, como Facebook, LinkedIn ou Google, pode especificar o `domain_hint` parâmetro. Este parâmetro de consulta fornece uma dica ao Azure AD B2C sobre o fornecedor de identidade social que deve ser usado para iniciar sação. Por exemplo, se a aplicação especificar, o `domain_hint=facebook.com` sin-in vai diretamente para a página de entrada no Facebook.

![Inscreva-se na página com domain_hint param de consulta realçado em URL](./media/direct-signin/domain-hint.png)

Se estiver a utilizar uma política personalizada, pode configurar o nome de domínio utilizando o `<Domain>domain name</Domain>` elemento XML de qualquer `<ClaimsProvider>` .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


