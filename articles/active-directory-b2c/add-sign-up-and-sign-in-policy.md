---
title: Configurar um fluxo de inscrição e de inscrição
titleSuffix: Azure AD B2C
description: Saiba como configurar um fluxo de inscrição e inscrição no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: fbcdef0002a227d5319fc01e625146480e4c99fc
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043755"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Configurar um fluxo de inscrição e de inscrição no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>Entrada e fluxo de inscrição

A política de inscrição e inscrição permite aos utilizadores: 

* Inscreva-se com conta local
* Iniciar s-in com conta local
* Inscreva-se ou inscreva-se com uma conta social
* Reposição de palavras-passe

![Fluxo de edição de perfis](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não o fez, [registe uma aplicação web no Azure Ative Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Crie um fluxo de utilizador de inscrição e de inscrição

O fluxo de utilizador de inscrição e de entrada lida com experiências de inscrição e inscrição com uma única configuração. Os utilizadores da sua aplicação são conduzidos pelo caminho certo, dependendo do contexto.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **os fluxos do Utilizador** e, em seguida, selecione Novo fluxo de **utilizador**.

    ![Página de fluxos de utilizador no portal com novo botão de fluxo do utilizador realçado](./media/add-sign-up-and-sign-in-policy/signup-signin-user-flow.png)

1. Na página De fluxo do **utilizador,** selecione o **'Iniciar's e inscreva-se no** fluxo do utilizador.

    ![Selecione uma página de fluxo de utilizador com iniciar s inscrição e iniciar surgiu no fluxo realçado](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. Em **Selecione uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**. (Saiba mais sobre[as](user-flow-versions.md) versões de fluxo de utilizador.)

    ![Crie a página de fluxo do utilizador no portal Azure com propriedades destacadas](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *signo de inscrição em 1*.
1. Para **fornecedores de identidade**, selecione **e-mail inscrever-se**.
1. Para **atributos e reclamações do Utilizador,** escolha as reclamações e atributos que pretende recolher e enviar do utilizador durante a inscrição. Por exemplo, selecione **Mostrar mais**, e depois escolha atributos e reclamações para **País/Região,** **Nome de Exibição** e **Código Postal**. Clique em **OK**.

    ![Página de seleção de atributos e reclamações com três reclamações selecionadas](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente preparado para o nome.
2. Siga os passos para [lidar com o fluxo para "Esqueci a sua senha?"](add-password-reset-policy.md?pivots=b2c-user-flow.md#self-service-password-reset-recommended) dentro da política de inscrição ou inscrição.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador executar** e, em seguida, selecione Iniciar s **inscrição agora**.

    ![Executar a página de fluxo do utilizador no portal com o botão de fluxo do utilizador Executar realçado](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.png)

1. Introduza um endereço de e-mail válido, clique em **Enviar código de verificação,** insira o código de verificação que recebe e, em seguida, selecione **Verificar código**.
1. Introduza uma nova senha e confirme a senha.
1. Selecione o seu país e região, insira o nome que deseja exibido, introduza um código postal e, em seguida, clique em **Criar**. O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si.
1. Agora pode executar o fluxo do utilizador novamente e deverá ser capaz de iniciar scontabilidade com a conta que criou. O token devolvido inclui as alegações que selecionou de país/região, nome e código postal.

> [!NOTE]
> A experiência "Executar o fluxo do utilizador" não é atualmente compatível com o tipo URL de resposta SPA utilizando o fluxo de código de autorização. Para utilizar a experiência "Executar o fluxo do utilizador" com este tipo de aplicações, registe um URL de resposta do tipo "Web" e possibilite o fluxo implícito tal como descrito [aqui](tutorial-register-spa.md).

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>Criar uma política de inscrição e inscrição

As políticas personalizadas são um conjunto de ficheiros XML que envia para o seu inquilino Azure AD B2C para definir viagens de utilizador. Fornecemos pacotes de iniciação com várias políticas pré-construídas, incluindo: inscrição e início de sessão, reset de palavra-passe e política de edição de perfis. Para obter mais informações, consulte [Começar com políticas personalizadas em Azure AD B2C.](custom-policy-get-started.md)

::: zone-end

## <a name="next-steps"></a>Passos seguintes

* Adicione uma [sindens de saúde com o provedor de identidade social.](add-identity-provider.md)
* Configurar um [fluxo de reset de palavra-passe](add-password-reset-policy.md).
