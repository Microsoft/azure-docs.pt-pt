---
title: Tutorial - Criar fluxos de utilizadores - Diretório Ativo Azure B2C
description: Saiba como criar fluxos de utilizadores no portal Azure para permitir a inscrição, o início e a edição de perfil do utilizador para as suas aplicações no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51adbb74635f66ca86347b536dc2607566dcb725
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183572"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutorial: Criar fluxos de utilizadores no Diretório Ativo Azure B2C

Nas suas aplicações pode ter [fluxos de utilizadores](user-flow-overview.md) que permitem aos utilizadores inscreverem-se, iniciarem sessão ou gerirem o seu perfil. Pode criar vários fluxos de utilizadores de diferentes tipos no seu inquilino Azure Ative Directory B2C (Azure AD B2C) e utilizá-los nas suas aplicações conforme necessário. Os fluxos de utilizadores podem ser reutilizados através de aplicações.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um fluxo de utilizador de inscrição e inscrição
> * Criar um fluxo de utilizador de edição de perfil
> * Criar uma palavra-passe redefinir o fluxo do utilizador

Este tutorial mostra-lhe como criar alguns fluxos de utilizador recomendados utilizando o portal Azure. Se procura informações sobre como configurar um fluxo de credenciais de senha do proprietário de recursos (ROPC) na sua aplicação, consulte Configurar as credenciais de senha do proprietário de [recursos fluem no Azure AD B2C](configure-ropc.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Registe as suas aplicações](tutorial-register-applications.md) que fazem parte dos fluxos de utilizador que pretende criar.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Criar um fluxo de utilizador de inscrição e inscrição

O fluxo de utilizador de inscrição e de entrada de entrada manuseia experiências de inscrição e de inscrição com uma única configuração. Os utilizadores da sua aplicação são conduzidos pelo caminho certo, dependendo do contexto.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.

    ![Painel de inquilinos, diretórios e assinaturas B2C, portal Azure](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Termos de Políticas,** selecione **fluxos de utilizador (políticas)** e, em seguida, selecione **novo fluxo de utilizador**.

    ![Página de fluxos de utilizador no portal com novo botão de fluxo do utilizador realçado](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. No **separador Recomendado,** selecione o **'Iniciar' e iniciar sessão no** fluxo do utilizador.

    ![Selecione uma página de fluxo do utilizador com O Sign up e assine o fluxo realçado](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *inscrição1*.
1. Para **fornecedores de identidade,** selecione inscrição por **e-mail**.

    ![Criar página de fluxo de utilizadores no portal Azure com propriedades em destaque](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. Para **atributos e reclamações do Utilizador,** escolha as reclamações e atributos que pretende recolher e enviar do utilizador durante o arranque. Por exemplo, selecione **Mostrar mais**, e depois escolher atributos e reivindicações para **País/Região,** **Nome de Exibição**, e **Código Postal**. Clique em **OK**.

    ![Atribuições e página de seleção de reclamações com três reclamações selecionadas](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente anexado ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo do utilizador

1. Selecione o fluxo de utilizador que criou para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. O **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **Executar fluxo de utilizador**, e, em seguida, selecione **'Inscreva-se' agora**.

    ![Executar página de fluxo do utilizador no portal com botão de fluxo do utilizador executar realçado](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Introduza um endereço de e-mail válido, clique em Enviar código de **verificação,** introduza o código de verificação que recebe e, em seguida, selecione **Verificar código**.
1. Introduza uma nova senha e confirme a senha.
1. Selecione o seu país e região, introduza o nome que deseja exibido, introduza um código postal e, em seguida, clique em **Criar**. O símbolo é devolvido à `https://jwt.ms` e deve ser exibido a si.
1. Agora pode executar o fluxo do utilizador novamente e deverá poder iniciar sessão com a conta que criou. O símbolo devolvido inclui as alegações que selecionou de país/região, nome e código postal.

## <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de utilizador de edição de perfil

Se pretender ativar os utilizadores para editar em sua aplicação, utiliza um fluxo de utilizador de edição de perfil.

1. No menu da página geral do inquilino Azure AD B2C, selecione **fluxos de utilizador (políticas)** e, em seguida, selecione **Novo fluxo de utilizador**.
1. Selecione o fluxo do utilizador de **edição** de Perfil no **separador Recomendado.**
1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *edição de perfis1*.
1. Para **fornecedores de identidade,** selecione **Local Account SignIn**.
1. Para **atributos do Utilizador,** escolha os atributos que pretende que o cliente possa editar no seu perfil. Por exemplo, selecione **Mostrar mais**, e, em seguida, escolha ambos os atributos e reivindicações para o nome **de exibição** e **o título de trabalho**. Clique em **OK**.
1. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente anexado ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo do utilizador

1. Selecione o fluxo de utilizador que criou para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. O **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique no **fluxo do utilizador executar**, e depois inscreva-se na conta que criou anteriormente.
1. Tem agora a oportunidade de alterar o nome de exibição e o título de trabalho para o utilizador. Clique em **Continue** (Continuar). O símbolo é devolvido à `https://jwt.ms` e deve ser exibido a si.

## <a name="create-a-password-reset-user-flow"></a>Criar uma palavra-passe redefinir o fluxo do utilizador

Para permitir que os utilizadores da sua aplicação reponham a sua palavra-passe, utilize um fluxo de utilizador de redefinição de palavra-passe.

1. No menu de visão geral do inquilino Azure AD B2C, selecione **fluxos de utilizador (políticas)** e, em seguida, selecione **novo fluxo de utilizador**.
1. Selecione o fluxo de utilizador de reset da **palavra-passe** no **separador Recomendado.**
1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *passwordreset1*.
1. Para **os fornecedores de Identidade,** ative **redefinir a palavra-passe utilizando o endereço de e-mail**.
1. No âmbito das reclamações da Aplicação, clique em **Mostrar mais** e escolha as reclamações que deseja devolvidas nas fichas de autorização enviadas de volta para a sua aplicação. Por exemplo, selecione **ID de Objeto do Utilizador**.
1. Clique em **OK**.
1. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente anexado ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo do utilizador

1. Selecione o fluxo de utilizador que criou para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. O **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique no **fluxo do utilizador run,** verifique o endereço de e-mail da conta que criou anteriormente e selecione **Continuar**.
1. Tem agora a oportunidade de alterar a palavra-passe para o utilizador. Altere a palavra-passe e selecione **Continuar**. O símbolo é devolvido à `https://jwt.ms` e deve ser exibido a si.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a:

> [!div class="checklist"]
> * Criar um fluxo de utilizador de inscrição e inscrição
> * Criar um fluxo de utilizador de edição de perfil
> * Criar uma palavra-passe redefinir o fluxo do utilizador

Em seguida, saiba adicionar fornecedores de identidade às suas aplicações para permitir o início de sessão de utilizadores com fornecedores como Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft ou Twitter.

> [!div class="nextstepaction"]
> [Adicione fornecedores de identidade às suas aplicações >](tutorial-add-identity-providers.md)