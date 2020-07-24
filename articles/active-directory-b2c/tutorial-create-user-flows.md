---
title: Tutorial - Criar fluxos de utilizadores - Azure Ative Directory B2C
description: Saiba como criar fluxos de utilizador no portal Azure para ativar a inscrição, o sessão e a edição de perfil do utilizador para as suas aplicações no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cc6673615c85b34975d6743da6da88ca841bcf35
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87005366"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutorial: Criar fluxos de utilizadores no Azure Ative Directory B2C

Nas suas aplicações poderá ter [fluxos de utilizador](user-flow-overview.md) que permitem aos utilizadores iniciar sessão, iniciar sessão ou gerir o seu perfil. Pode criar vários fluxos de utilizadores de diferentes tipos no seu inquilino Azure Ative Directory B2C (Azure AD B2C) e usá-los nas suas aplicações conforme necessário. Os fluxos dos utilizadores podem ser reutilizados através das aplicações.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Crie um fluxo de utilizador de inscrição e de inscrição
> * Criar um fluxo de utilizador de edição de perfil
> * Criar um fluxo de utilizador de redefinição de palavra-passe

Este tutorial mostra-lhe como criar alguns fluxos recomendados de utilizador utilizando o portal Azure. Se estiver à procura de informações sobre como configurar um fluxo de credenciais de senha do proprietário de recursos (ROPC) na sua aplicação, consulte [configurar o fluxo de credenciais de senha do proprietário do recurso em Azure AD B2C](configure-ropc.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Registe as suas aplicações](tutorial-register-applications.md) que fazem parte dos fluxos de utilizador que pretende criar.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Crie um fluxo de utilizador de inscrição e de inscrição

O fluxo de utilizador de inscrição e de entrada lida com experiências de inscrição e inscrição com uma única configuração. Os utilizadores da sua aplicação são conduzidos pelo caminho certo, dependendo do contexto.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

    ![Inquilina B2C, Diretório e Painel de Assinatura, Portal Azure](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **os fluxos do Utilizador**e, em seguida, selecione Novo fluxo de **utilizador**.

    ![Página de fluxos de utilizador no portal com novo botão de fluxo do utilizador realçado](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. No separador **Recomendado,** selecione o **'Iniciar s-se) e inscreva-se no** fluxo do utilizador.

    ![Selecione uma página de fluxo de utilizador com iniciar s inscrição e iniciar surgiu no fluxo realçado](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *signo de inscrição em 1*.
1. Para **fornecedores de identidade**, selecione **Inscrição de e-mail**.

    ![Crie a página de fluxo do utilizador no portal Azure com propriedades destacadas](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. Para **atributos e reclamações do Utilizador,** escolha as reclamações e atributos que pretende recolher e enviar do utilizador durante a inscrição. Por exemplo, selecione **Mostrar mais**, e depois escolha atributos e reclamações para **País/Região,** **Nome de Exibição**e **Código Postal**. Clique em **OK**.

    ![Página de seleção de atributos e reclamações com três reclamações selecionadas](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente preparado para o nome.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador executar**e, em seguida, selecione Iniciar s **inscrição agora**.

    ![Executar a página de fluxo do utilizador no portal com o botão de fluxo do utilizador Executar realçado](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Introduza um endereço de e-mail válido, clique em **Enviar código de verificação,** insira o código de verificação que recebe e, em seguida, selecione **Verificar código**.
1. Introduza uma nova senha e confirme a senha.
1. Selecione o seu país e região, insira o nome que deseja exibido, introduza um código postal e, em seguida, clique em **Criar**. O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si.
1. Agora pode executar o fluxo do utilizador novamente e deverá ser capaz de iniciar scontabilidade com a conta que criou. O token devolvido inclui as alegações que selecionou de país/região, nome e código postal.

## <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de utilizador de edição de perfil

Se pretender que os utilizadores editem o seu perfil na sua aplicação, utilize um fluxo de utilizador de edição de perfil.

1. No menu da página geral do inquilino Azure AD B2C, selecione **os fluxos do Utilizador**e, em seguida, selecione Novo fluxo de **utilizador**.
1. Selecione o fluxo do utilizador **de edição de perfil** no **separador Recomendado.**
1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *perfilando1*.
1. Para **fornecedores de identidade**, selecione Local Account **SignIn**.
1. Para **os atributos do Utilizador,** escolha os atributos que pretende que o cliente possa editar no seu perfil. Por exemplo, selecione **Mostrar mais**, e, em seguida, escolha ambos os atributos e reclamações para o nome **de Exibição** e **o título de Job**. Clique em **OK**.
1. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente anexado ao nome.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique no **fluxo do utilizador executar**e, em seguida, iniciar scontabilidade com a conta que criou anteriormente.
1. Tem agora a oportunidade de alterar o nome de exibição e o título de trabalho para o utilizador. Clique em **Continue** (Continuar). O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si.

## <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de utilizador de redefinição de palavra-passe

Para permitir que os utilizadores da sua aplicação repuvam a sua palavra-passe, utilize uma palavra-passe para redefinir o fluxo do utilizador.

1. No menu geral do inquilino Azure AD B2C, selecione **os fluxos do utilizador**e, em seguida, selecione Novo fluxo de **utilizador**.
1. Selecione o fluxo do utilizador **de redefinição de palavra-passe** no **separador Recomendado.**
1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *passwordreste1*.
1. Para **os fornecedores de identidade,** ative **a palavra-passe Reset utilizando o endereço de e-mail.**
1. Nas reclamações da Aplicação, clique **em Mostrar mais** e escolha as reclamações que pretende devolvidas nos tokens de autorização enviados de volta para a sua aplicação. Por exemplo, selecione **ID de Objeto do Utilizador**.
1. Clique em **OK**.
1. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente anexado ao nome.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador**executar, verifique o endereço de e-mail da conta que criou anteriormente e selecione **Continue**.
1. Tem agora a oportunidade de alterar a palavra-passe para o utilizador. Altere a palavra-passe e **selecione Continue**. O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a:

> [!div class="checklist"]
> * Crie um fluxo de utilizador de inscrição e de inscrição
> * Criar um fluxo de utilizador de edição de perfil
> * Criar um fluxo de utilizador de redefinição de palavra-passe

Em seguida, saiba adicionar fornecedores de identidade às suas aplicações para permitir o sismo do utilizador com fornecedores como Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft ou Twitter.

> [!div class="nextstepaction"]
> [Adicione fornecedores de identidade às suas aplicações >](tutorial-add-identity-providers.md)
