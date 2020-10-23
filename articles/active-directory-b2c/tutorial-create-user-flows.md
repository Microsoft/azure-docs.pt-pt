---
title: Tutorial - Criar fluxos de utilizadores - Azure Ative Directory B2C
description: Siga este tutorial para aprender como criar fluxos de utilizador no portal Azure para ativar a inscrição, o início e a edição de perfil do utilizador para as suas aplicações no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: af0fafe98c3ca48ecced46cef6fb5fe8876c7f50
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215999"
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

> [!IMPORTANT]
> Mudamos a forma como referenciamos as versões de fluxo de utilizadores. Anteriormente, oferecemos versões V1 (prontas para produção) e versões V1.1 e V2 (pré-visualização). Agora, consolidamos os fluxos de utilizador nas versões **Recomendado** (pré-visualização da próxima geração) e **Standard** (geralmente disponível). Todos os fluxos de pré-visualização de V1.1 e V2 estão em vias de depreciação até 1 de agosto de **2021**. Para mais informações, consulte [as versões de fluxo do utilizador em Azure AD B2C](user-flow-versions.md).

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

1. Na página De fluxo do **utilizador,** selecione o **'Iniciar's e inscreva-se no** fluxo do utilizador.

    ![Selecione uma página de fluxo de utilizador com iniciar s inscrição e iniciar surgiu no fluxo realçado](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. Em **Selecione uma versão**, selecione **Recomendado**e, em seguida, selecione **Criar**. (Saiba mais sobre[as](user-flow-versions.md) versões de fluxo de utilizador.)

    ![Crie a página de fluxo do utilizador no portal Azure com propriedades destacadas](./media/tutorial-create-user-flows/select-version.png)

1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *signo de inscrição em 1*.
1. Para **fornecedores de identidade**, selecione **Inscrição de e-mail**.
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

> [!NOTE]
> A experiência "Executar o fluxo do utilizador" não é atualmente compatível com o tipo URL de resposta SPA utilizando o fluxo de código de autorização. Para utilizar a experiência "Executar o fluxo do utilizador" com este tipo de aplicações, registe um URL de resposta do tipo "Web" e possibilite o fluxo implícito tal como descrito [aqui](tutorial-register-spa.md).

## <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de utilizador de edição de perfil

Se pretender que os utilizadores editem o seu perfil na sua aplicação, utilize um fluxo de utilizador de edição de perfil.

1. No menu da página geral do inquilino Azure AD B2C, selecione **os fluxos do Utilizador**e, em seguida, selecione Novo fluxo de **utilizador**.
1. Na página De fluxo do **utilizador,** selecione o fluxo do utilizador **de edição de perfil.** 
1. Em **Selecione uma versão**, selecione **Recomendado**e, em seguida, selecione **Criar**.
1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *perfilando1*.
1. Para **fornecedores de identidade**, selecione Local Account **SignIn**.
2. Para **os atributos do Utilizador,** escolha os atributos que pretende que o cliente possa editar no seu perfil. Por exemplo, selecione **Mostrar mais**, e, em seguida, escolha ambos os atributos e reclamações para o nome **de Exibição** e **o título de Job**. Clique em **OK**.
3. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente anexado ao nome.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique no **fluxo do utilizador executar**e, em seguida, iniciar scontabilidade com a conta que criou anteriormente.
1. Tem agora a oportunidade de alterar o nome de exibição e o título de trabalho para o utilizador. Clique em **Continue** (Continuar). O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si.

## <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de utilizador de redefinição de palavra-passe

Para permitir que os utilizadores da sua aplicação repuvam a sua palavra-passe, utilize uma palavra-passe para redefinir o fluxo do utilizador.

1. No menu geral do inquilino Azure AD B2C, selecione **os fluxos do utilizador**e, em seguida, selecione Novo fluxo de **utilizador**.
1. Na página De fluxo do **utilizador,** selecione o fluxo do utilizador **de redefinição de palavra-passe.** 
1. Em **Selecione uma versão**, selecione **Recomendado**e, em seguida, selecione **Criar**.
1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *passwordreste1*.
1. Para **os fornecedores de identidade,** ative **a palavra-passe Reset utilizando o endereço de e-mail.**
2. Nas reclamações da Aplicação, clique **em Mostrar mais** e escolha as reclamações que pretende devolvidas nos tokens de autorização enviados de volta para a sua aplicação. Por exemplo, selecione **ID de Objeto do Utilizador**.
3. Clique em **OK**.
4. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente anexado ao nome.

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
