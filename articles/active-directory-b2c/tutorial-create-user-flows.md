---
title: Tutorial - criar fluxos de utilizador - Azure Active Directory B2C
description: Saiba como criar fluxos de utilizador no portal do Azure para ativar o início de sessão se, iniciar sessão e edição de perfis de utilizador para as suas aplicações no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 111196388d0e17ecde8a2055959f2f573e43ade8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056412"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutorial: Criar fluxos de utilizador no Azure Active Directory B2C

Em seus aplicativos podem ter [fluxos de utilizador](active-directory-b2c-reference-policies.md) que permitem aos utilizadores inscrever, inicie sessão ou gerir o seu perfil. Pode criar vários fluxos de utilizador de tipos diferentes no seu inquilino do Azure Active Directory (Azure AD) B2C e utilizá-los em seus aplicativos, conforme necessário. Fluxos de utilizador podem ser reutilizados em todas as aplicações.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um fluxo de utilizador de inscrição e início de sessão
> * Criar um fluxo de utilizador de edição de perfil
> * Criar um fluxo de utilizador de reposição de palavra-passe

Este tutorial mostra-lhe como criar alguns fluxos recomendados ao usuário com o portal do Azure. Se estiver à procura de informações sobre como configurar credenciais de palavra-passe de proprietário do recurso fluxo (ROPC) na sua aplicação, veja [configurar as credenciais de palavra-passe do proprietário de recursos de fluxo no Azure AD B2C](configure-ropc.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Registar as suas aplicações](tutorial-register-applications.md) que fazem parte dos fluxos de utilizador que pretende criar.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Criar um fluxo de utilizador de inscrição e início de sessão

O fluxo de utilizador de inscrição e início de sessão lida com experiências de inscrição e início de sessão com uma configuração única. Os utilizadores da sua aplicação são liderados para baixo o caminho certo dependendo do contexto.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.

    ![Mude para o diretório da subscrição](./media/tutorial-create-user-flows/switch-directories.PNG)

1. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
1. No menu à esquerda sob **políticas**, selecione **fluxos de utilizador (diretivas)** e, em seguida, selecione **novo fluxo de utilizador**.

    ![Selecione o novo fluxo de utilizador](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na **recomendado** separador, selecione a **inscrever-se e iniciar sessão** fluxo de utilizador.

    ![Selecione o fluxo de utilizador de inscrição e início de sessão](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Introduza um **nome** para o fluxo de utilizador. Por exemplo, *signupsignin1*.
1. Para **fornecedores de identidade**, selecione **inscrição de E-Mail**.

    ![Defina as propriedades de fluxo](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. Para **atributos de utilizador e afirmações**, escolha as afirmações e atributos que pretende recolher e a enviar do usuário durante a inscrição. Por exemplo, seleccione **mostrar mais**e, em seguida, escolha atributos e afirmações para **país/região**, **nome a apresentar**, e **Código Postal**. Clique em **OK**.

    ![Selecione os atributos e afirmações](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Clique em **criar** para adicionar o fluxo de utilizador. Um prefixo de *B2C_1* automaticamente é acrescentado ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de utilizador

1. Selecione o fluxo de utilizador que criou para abrir a página de descrição geral, em seguida, selecione **executar o fluxo de utilizador**.
1. Para **aplicativo**, selecione a aplicação web com o nome *webapp1* que registou anteriormente. O **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **executar o fluxo de utilizador**e, em seguida, selecione **Inscreva-se agora**.

    ![Executar o fluxo de utilizador](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Introduza um endereço de e-mail válido, clique em **enviar código de verificação**, introduza o código de verificação que recebem, em seguida, selecione **verificar código**.
1. Introduza uma nova palavra-passe e confirme a palavra-passe.
1. Selecione seu país e região, introduza o nome que pretende que sejam apresentado, introduza um código postal e, em seguida, clique em **criar**. O token é retornado para `https://jwt.ms` e deverá ser apresentada para.
1. Agora, pode executar o fluxo de utilizador novamente e deve ser capaz de iniciar sessão com a conta que criou. O token devolvido inclui as afirmações que selecionou de país/região, o nome e o código postal.

## <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de utilizador de edição de perfil

Se quiser permitir que os utilizadores editar o respetivo perfil em seu aplicativo, usar um fluxo de utilizador de edição de perfil.

1. No menu à esquerda da página de descrição geral de inquilino do Azure AD B2C, selecione **fluxos de utilizador (diretivas)** e, em seguida, selecione **novo fluxo de utilizador**.
1. Selecione o **edição de perfil** fluxo de utilizador no separador recomendado.
1. Introduza um **nome** para o fluxo de utilizador. Por exemplo, *profileediting1*.
1. Para **fornecedores de identidade**, selecione **início de sessão de conta Local**.
1. Para **atributos de utilizador**, escolha os atributos que pretende que o cliente para conseguir editar no respetivo perfil. Por exemplo, seleccione **mostrar mais**e, em seguida, escolha os atributos e afirmações para **nome a apresentar** e **cargo**. Clique em **OK**.
1. Clique em **criar** para adicionar o fluxo de utilizador. Um prefixo de *B2C_1* automaticamente é acrescentado ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de utilizador

1. Selecione o fluxo de utilizador que criou para abrir a página de descrição geral, em seguida, selecione **executar o fluxo de utilizador**.
1. Para **aplicativo**, selecione a aplicação web com o nome *webapp1* que registou anteriormente. O **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **executar o fluxo de utilizador**e, em seguida, inicie sessão com a conta que criou anteriormente.
1. Agora tem a oportunidade de alterar o título de nome e a tarefa de apresentação para o utilizador. Clique em **Continue** (Continuar). O token é retornado para `https://jwt.ms` e deverá ser apresentada para.

## <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de utilizador de reposição de palavra-passe

Para permitir que os utilizadores da sua aplicação para repor a palavra-passe, utilize um fluxo de utilizador de reposição de palavra-passe.

1. No menu da esquerda, selecione **fluxos de utilizador (diretivas)** e, em seguida, selecione **novo fluxo de utilizador**.
1. Selecione o **reposição de palavra-passe** fluxo de utilizador no separador recomendado.
1. Introduza um **nome** para o fluxo de utilizador. Por exemplo, *passwordreset1*.
1. Para **fornecedores de identidade**, ative **repor a palavra-passe com endereço de e-mail**.
1. Em afirmações de aplicação, clique em **mostrar mais** e escolha as afirmações que quer que sejam devolvidas nos tokens de autorização enviados para a sua aplicação. Por exemplo, selecione **ID de Objeto do Utilizador**.
1. Clique em **OK**.
1. Clique em **criar** para adicionar o fluxo de utilizador. Um prefixo de *B2C_1* automaticamente é acrescentado ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de utilizador

1. Selecione o fluxo de utilizador que criou para abrir a página de descrição geral, em seguida, selecione **executar o fluxo de utilizador**.
1. Para **aplicativo**, selecione a aplicação web com o nome *webapp1* que registou anteriormente. O **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **executar o fluxo de utilizador**, verifique se o endereço de e-mail da conta que anteriormente criado e selecione **continuar**.
1. Agora tem a oportunidade de alterar a palavra-passe do utilizador. Alterar a palavra-passe e selecione **continuar**. O token é retornado para `https://jwt.ms` e deverá ser apresentada para.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como:

> [!div class="checklist"]
> * Criar um fluxo de utilizador de inscrição e início de sessão
> * Criar um fluxo de utilizador de edição de perfil
> * Criar um fluxo de utilizador de reposição de palavra-passe

Em seguida, saiba como adicionar fornecedores de identidade às suas aplicações para ativar o utilizador inicie sessão com fornecedores, como o Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft ou Twitter.

> [!div class="nextstepaction"]
> [Adicionar fornecedores de identidade às suas aplicações >](tutorial-add-identity-providers.md)