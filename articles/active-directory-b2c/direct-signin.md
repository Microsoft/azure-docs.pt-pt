---
title: Configurar direto início de sessão através do Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como pré-povoar o nome de início de sessão ou redirecionar diretamente para um fornecedor de identidade social.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 869097ac3b91e55d5dbf948680450f31efafd359
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511117"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Configurar direto início de sessão através do Azure Active Directory B2C

Quando configurar o início de sessão para a sua aplicação com o Azure Active Directory (AD) B2C, pode preencher o nome de início de sessão ou direto início de sessão para um fornecedor de identidade específicas de redes sociais, como o Facebook, LinkedIn ou uma conta Microsoft. 

## <a name="prepopulate-the-sign-in-name"></a>Pré-povoar o nome de início de sessão

Durante um percurso do utilizador de início de sessão, um aplicativo de parte confiável pode visar um nome de utilizador ou domínio específico. Quando a filtragem de um utilizador, pode especificar uma aplicação, no pedido de autorização, o `login_hint` parâmetro com o nome de utilizador de início de sessão de consulta. O Azure AD B2C povoa automaticamente o nome de início de sessão, enquanto o utilizador só tem de fornecer a palavra-passe.

![utilizando a sugestão de início de sessão](./media/direct-signin/login-hint.png) 

O usuário é capaz de alterar o valor na caixa de texto início de sessão.

Se estiver a utilizar uma política personalizada, substituir o `SelfAsserted-LocalAccountSignin-Email` perfil técnico. Na `<InputClaims>` secção, defina o atributo ValorPredefinido da afirmação signInName para `{OIDC:LoginHint}`. O `{OIDC:LoginHint}` variável contém o valor do `login_hint` parâmetro. O Azure AD B2C lê o valor da afirmação signInName e preenche a caixa de texto signInName antecipadamente.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Redirecionar o início de sessão para um fornecedor de redes sociais

Se tiver configurado a jornada de início de sessão para a sua aplicação incluir as contas de redes sociais, como o Facebook, LinkedIn ou o Google, pode especificar o `domain_hint` parâmetro. Este parâmetro de consulta oferece uma sugestão para o Azure AD B2C sobre o fornecedor de identidade social que deve ser utilizado para iniciar sessão. Por exemplo, se o aplicativo especifica `domain_hint=facebook.com`, início de sessão vai diretamente para a página de início de sessão do Facebook.

![utilizar a sugestão de domínio](./media/direct-signin/domain-hint.png) 

Se estiver a utilizar uma política personalizada, pode configurar o nome de domínio utilizando o `<Domain>domain name</Domain>` elemento XML de qualquer `<ClaimsProvider>`. 

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


