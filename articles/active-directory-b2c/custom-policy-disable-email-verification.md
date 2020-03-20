---
title: Desative a verificação de e-mail durante a inscrição do cliente com uma política personalizada
titleSuffix: Azure AD B2C
description: Saiba como desativar a verificação de e-mail durante a inscrição do cliente no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13a5fa6a030d876d92651ca587e37fdc6a3ec600
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136147"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Desative a verificação de e-mail durante a inscrição do cliente usando uma política personalizada no Diretório Ativo Azure B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em introdução [às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada de trabalho para inscrição e inscrição com contas sociais e locais.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Adicione os metadados ao perfil técnico autoafirmado

O perfil técnico **LocalAccountSignUpWithLogonEmail** é um [autoafirmado](self-asserted-technical-profile.md), que é invocado durante o fluxo de inscrição. Para desativar a verificação de e-mail, detete te metadados `EnforceEmailVerification` como falsos. Anular os perfis técnicos LocalAccountSignUpWithLogonEmail no ficheiro de extensão. 

1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>.
1. Encontre o elemento `ClaimsProviders`. Se o elemento não existir, adicione-o.
1. Adicione o seguinte fornecedor de reclamações ao elemento `ClaimsProviders`:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>Testar a política personalizada

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione registos de **Aplicações**.
4. Selecione Quadro de **Experiência de Identidade**.
5. Selecione **'Upload Custom Policy'** e, em seguida, faça upload dos dois ficheiros de política que alterou.
2. Selecione a política de inscrição ou de inscrição que fez o upload e clique no botão **Executar agora.**
3. Deverá poder inscrever-se através de um endereço de e-mail sem a validação.


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [perfil técnico autoafirmado](self-asserted-technical-profile.md) na referência iEF.
