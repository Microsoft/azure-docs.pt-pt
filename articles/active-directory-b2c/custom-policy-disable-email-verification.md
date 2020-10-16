---
title: Desativar a verificação de e-mail durante a inscrição do cliente com uma política personalizada
titleSuffix: Azure AD B2C
description: Saiba como desativar a verificação de e-mail durante a inscrição do cliente com uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9372a9caff5aefb53bfa8adf7eb1c68d2a3b7b2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259620"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Desativar a verificação de e-mail durante a inscrição do cliente usando uma política personalizada no Azure Ative Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Começar com políticas personalizadas.](custom-policy-get-started.md) Você deve ter uma política personalizada de trabalho para se inscrever e iniciar sação com contas sociais e locais.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Adicione os metadados ao perfil técnico autoafirmado

O perfil técnico **LocalAccountSignUpWithLogonEmail** é um perfil [autoafirmado,](self-asserted-technical-profile.md)que é invocado durante o fluxo de inscrição. Para desativar a verificação de e-mail, desative os `EnforceEmailVerification` metadados em falso. Anular os perfis técnicos localAccountSignUpWithLogonEmail no ficheiro de extensão. 

1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Encontre o `ClaimsProviders` elemento. Se o elemento não existir, adicione-o.
1. Adicione o seguinte provedor de reclamações ao `ClaimsProviders` elemento:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
4. Selecione **o Quadro de Experiência de Identidade.**
5. Selecione **''Personal's Personal Policy ' 'Upload'** e, em seguida, faça o upload dos dois ficheiros de política que alterou.
2. Selecione a política de inscrição ou de inscrição que fez o upload e clique no botão **Executar agora.**
3. Você deve ser capaz de se inscrever usando um endereço de e-mail sem a validação.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [perfil técnico autoafirmado](self-asserted-technical-profile.md) na referência IEF.
