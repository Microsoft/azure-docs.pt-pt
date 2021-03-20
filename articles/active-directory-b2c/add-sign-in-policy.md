---
title: Configurar um fluxo de entrada
titleSuffix: Azure Active Directory B2C
description: Saiba como configurar um fluxo de entrada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2310bd39c39036b6d6ac919517fa5539d7b70779
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581869"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>Configurar um fluxo de entrada no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>Visão geral do fluxo de entrada de entrada

A política de inscrição permite aos utilizadores: 

* Os utilizadores podem iniciar sação com uma Conta Local Azure AD B2C
* Inscreva-se ou inscreva-se com uma conta social
* Reposição de palavras-passe
* Os utilizadores não podem inscrever-se numa Conta Local Azure AD B2C. Para criar uma conta, um administrador pode utilizar [o portal Azure,](manage-users-portal.md#create-a-consumer-user)ou [MS Graph API](microsoft-graph-operations.md).

![Fluxo de edição de perfis](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não o fez, [registe uma aplicação web no Azure Ative Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>Criar um fluxo de utilizador de entrada

Para adicionar a política de inscrição:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **os fluxos do Utilizador** e, em seguida, selecione Novo fluxo de **utilizador**.
1. Na página De fluxo do **utilizador,** selecione o **Sinal no** fluxo do utilizador.
1. Em **Selecione uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**. (Saiba mais sobre[as](user-flow-versions.md) versões de fluxo de utilizador.)
1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *signo de inscrição em 1*.
1. Para **fornecedores de identidade**, selecione **e-mail de sismo**.
1. Para **reclamações de aplicação,** escolha as reclamações e atributos que pretende enviar para a sua aplicação. Por exemplo, selecione **Mostrar mais**, e, em seguida, escolha atributos e reclamações para **Display Name**, **Given Name**,  **Sobrenome** e **ID de Objeto do Utilizador**. Clique em **OK**.
1. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente preparado para o nome.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador executar**.
1. Deverá poder iniciar scontabilidade com a conta que criou (utilizando a MS Graph API), sem o link de inscrição. O token devolvido inclui as alegações que selecionou.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>Remover o link de inscrição

O perfil técnico **SelfAsserted-LocalAccountSignin-Email** é um perfil [autoafirmado,](self-asserted-technical-profile.md)que é invocado durante o fluxo de inscrição ou de inscrição. Para remover a ligação de inscrição, descreva os `setting.showSignupLink` metadados para `false` . Substitua os perfis técnicos SelfAsserted-LocalAccountSignin-Email no ficheiro de extensão. 

1. Abra o ficheiro de extensões da sua apólice. Por exemplo, _`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**_ .
1. Encontre o `ClaimsProviders` elemento. Se o elemento não existir, adicione-o.
1. Adicione o seguinte provedor de reclamações ao `ClaimsProviders` elemento:

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <Metadata>
              <Item Key="setting.showSignupLink">false</Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

1. Dentro `<BuildingBlocks>` do elemento, adicione o seguinte [ContentDefinition](contentdefinitions.md) para fazer referência à versão 1.2.0 ou URI de dados mais recentes:

    ```XML
    <!-- 
    <BuildingBlocks> 
      <ContentDefinitions>-->
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        </ContentDefinition>
      <!--
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

## <a name="update-and-test-your-policy"></a>Atualizar e testar a sua política

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
1. Selecione **o Quadro de Experiência de Identidade.**
1. Selecione **Carregar a Política Personalizada** e, em seguida, carregar o ficheiro de política que alterou, *TrustFrameworkExtensions.xml*.
1. Selecione a política de inscrição que fez o upload e clique no botão **Executar agora.**
1. Deverá poder iniciar scontabilidade com a conta que criou (utilizando a MS Graph API), sem o link de inscrição.

::: zone-end

## <a name="next-steps"></a>Passos seguintes

* Adicione uma [sindens de saúde com o provedor de identidade social.](add-identity-provider.md)
* Configurar um [fluxo de reset de palavra-passe](add-password-reset-policy.md).
