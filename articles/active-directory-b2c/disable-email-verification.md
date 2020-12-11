---
title: Desativar a verificação de e-mail durante a inscrição do cliente
titleSuffix: Azure AD B2C
description: Saiba como desativar a verificação de e-mail durante a inscrição do cliente no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9d24bd65f1bd473560585ec9d594ddc77d858177
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111456"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Desativar a verificação de e-mail durante a inscrição do cliente no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Por padrão, o Azure Ative Directory B2C (Azure AD B2C) verifica o endereço de e-mail do seu cliente para contas locais (contas para utilizadores que se inscrevam com endereço de e-mail ou nome de utilizador). O Azure AD B2C garante endereços de e-mail válidos, exigindo que os clientes os verifiquem durante o processo de inscrição. Também impede que atores maliciosos utilizem processos automatizados para gerar contas fraudulentas nas suas aplicações.

Alguns desenvolvedores de aplicações preferem ignorar a verificação de e-mail durante o processo de inscrição e, em vez disso, fazer com que os clientes verifiquem o seu endereço de e-mail mais tarde. Para o suportar, o Azure AD B2C pode ser configurado para desativar a verificação de e-mail. Ao fazê-lo, cria-se um processo de inscrição mais suave e dá aos desenvolvedores a flexibilidade para diferenciar os clientes que verificaram o seu endereço de e-mail de clientes que não o fizeram.

> [!WARNING]
> A desativação da verificação de emails no processo de inscrição pode levar a spam. Se desativar a verificação de e-mail fornecida por Azure AD B2C, recomendamos que implemente um sistema de verificação de substituição.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>Desativar a verificação do e-mail

::: zone pivot="b2c-user-flow"

Siga estes passos para desativar a verificação de e-mail:

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Utilize o filtro **de subscrição Diretório +** no menu superior para selecionar o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **fluxos de utilizador**.
1. Selecione o fluxo do utilizador para o qual pretende desativar a verificação de e-mail. Por exemplo, *B2C_1_signinsignup.*
1. Selecione **layouts de página**.
1. Selecione **a página de inscrição de conta local**.
1. Nos **atributos do Utilizador**, selecione Endereço de **E-mail.**
1. No **drop-down de verificação requere,** selecione **Nº**.
1. Selecione **Guardar**. A verificação por e-mail está agora desativada para este fluxo de utilizador.

::: zone-end

::: zone pivot="b2c-custom-policy"
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
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>Teste a sua política 

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Utilize o filtro **de subscrição Diretório +** no menu superior para selecionar o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **fluxos de utilizador**.
1. Selecione o fluxo do utilizador para o qual pretende desativar a verificação de e-mail. Por exemplo, *B2C_1_signinsignup.*
1. Para testar a sua política, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador executar**
1. Você deve ser capaz de se inscrever usando um endereço de e-mail sem a validação.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o ficheiro do partido que conta

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
1. Selecione **o Quadro de Experiência de Identidade.**
1. Selecione **''Personal's Personal Policy ' 'Upload'** e, em seguida, faça o upload dos dois ficheiros de política que alterou.
1. Selecione a política de inscrição ou de inscrição que fez o upload e clique no botão **Executar agora.**
1. Você deve ser capaz de se inscrever usando um endereço de e-mail sem a validação.

::: zone-end


## <a name="next-steps"></a>Passos seguintes

- Saiba como [personalizar a interface de utilizador no Azure Ative Directory B2C](customize-ui-with-html.md)

