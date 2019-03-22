---
title: Tutorial - criar fluxos de utilizador - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como criar fluxos de utilizador para as suas aplicações no Azure Active Directory B2C no portal do Azure.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b76d56b17973c04992a8855917c814ced649bd0f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338263"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutorial: Criar fluxos de utilizador no Azure Active Directory B2C

Em seus aplicativos, talvez tenha [fluxos de utilizador](active-directory-b2c-reference-policies.md) que permitem aos utilizadores inscrever, inicie sessão ou gerir o seu perfil. Pode criar vários fluxos de utilizador de tipos diferentes no seu inquilino do Azure Active Directory (Azure AD) B2C e utilizá-los em seus aplicativos, conforme necessário. Fluxos de utilizador podem ser reutilizados em todas as aplicações.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um fluxo de utilizador de inscrição e início de sessão
> * Criar um fluxo de utilizador de edição de perfil
> * Criar um fluxo de utilizador de reposição de palavra-passe

Este tutorial mostra-lhe como criar alguns fluxos recomendados ao usuário com o portal do Azure. Se estiver procurando por informações sobre como configurar credenciais de palavra-passe de proprietário do recurso fluxo (ROPC) na sua aplicação, veja [configurar as credenciais de palavra-passe do proprietário de recursos de fluxo no Azure AD B2C](configure-ropc.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Registar as suas aplicações](tutorial-register-applications.md) que fazem parte dos fluxos de utilizador que pretende criar. 

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Criar um fluxo de utilizador de inscrição e início de sessão

O fluxo de utilizador de inscrição e início de sessão lida com experiências de inscrição e início de sessão com uma configuração única. Os utilizadores da sua aplicação são liderados para baixo o caminho certo dependendo do contexto.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.

    ![Mude para o diretório da subscrição](./media/tutorial-create-user-flows/switch-directories.png)

3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. No menu da esquerda, selecione **fluxos de utilizador**e, em seguida, selecione **novo fluxo de utilizador**.

    ![Selecione o novo fluxo de utilizador](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

5. Selecione o **inscrição e início de sessão** fluxo de utilizador no separador recomendado.

    ![Selecione o fluxo de utilizador de inscrição e início de sessão](./media/tutorial-create-user-flows/signup-signin-type.png)

6. Introduza um **nome** para o fluxo de utilizador. Por exemplo, *signupsignin1*.
7. Para **fornecedores de identidade**, selecione **inscrição de E-Mail**.

    ![Defina as propriedades de fluxo](./media/tutorial-create-user-flows/signup-signin-properties.png)

8. Para **atributos de utilizador e afirmações**, escolha as afirmações e atributos que pretende recolher e a enviar do usuário durante a inscrição. Por exemplo, seleccione **mostrar mais**e, em seguida, escolha **país/região**, **nome a apresentar**, e **Código Postal**. Clique em **OK**.

    ![Selecione os atributos e afirmações](./media/tutorial-create-user-flows/signup-signin-attributes.png)

9. Clique em **criar** para adicionar o fluxo de utilizador. Um prefixo de *B2C_1* automaticamente é acrescentado ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de utilizador

1. Na página de descrição geral do fluxo de utilizador que criou, selecione **executar o fluxo de utilizador**.
2. Para **aplicativo**, selecione a aplicação web com o nome *webapp1* que registou anteriormente. O **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **executar o fluxo de utilizador**e, em seguida, selecione **Inscreva-se agora**.

    ![Executar o fluxo de utilizador](./media/tutorial-create-user-flows/signup-signin-run-now.png)

4. Introduza um endereço de e-mail válido, clique em **enviar código de verificação**e, em seguida, introduza o código de verificação que recebe.
5. Introduza uma nova palavra-passe e confirme a palavra-passe.
6. Introduza o nome que pretende que sejam apresentado, selecione seu país e região, introduza um código postal e, em seguida, clique em **criar**. O token é retornado para `https://jwt.ms` e deverá ser apresentada para.
7. Agora, pode executar o fluxo de utilizador novamente e deve ser capaz de iniciar sessão com a conta que criou. O token devolvido inclui as afirmações que selecionou de nome, o país e o código postal.

## <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de utilizador de edição de perfil

Se quiser permitir que os utilizadores editar o respetivo perfil em seu aplicativo, usar um fluxo de utilizador de edição de perfil.

1. No menu da esquerda, selecione **fluxos de utilizador**e, em seguida, selecione **novo fluxo de utilizador**.
2. Selecione o **edição de perfil** fluxo de utilizador no separador recomendado.
3. Introduza um **nome** para o fluxo de utilizador. Por exemplo, *profileediting1*.
4. Para **fornecedores de identidade**, selecione **início de sessão de conta Local**.
5. Para **atributos de utilizador**, escolha os atributos que pretende que o cliente para conseguir editar no respetivo perfil. Por exemplo, seleccione **mostrar mais**e, em seguida, escolha **nome a apresentar** e **cargo**. Clique em **OK**.
6. Clique em **criar** para adicionar o fluxo de utilizador. Um prefixo de *B2C_1* automaticamente é acrescentado ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de utilizador

1. Na página de descrição geral do fluxo de utilizador que criou, selecione **executar o fluxo de utilizador**.
2. Para **aplicativo**, selecione a aplicação web com o nome *webapp1* que registou anteriormente. O **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **executar o fluxo de utilizador**e, em seguida, inicie sessão com a conta que criou anteriormente.
4. Agora tem a oportunidade de alterar o título de nome e a tarefa de apresentação para o utilizador. Clique em **Continue** (Continuar). O token é retornado para `https://jwt.ms` e deverá ser apresentada para.

## <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de utilizador de reposição de palavra-passe

É possível que pode permitir que o utilizador da sua aplicação para repor a palavra-passe, se necessário. Para ativar a reposição de palavra-passe, utilize um fluxo de utilizador de reposição de palavra-passe.

1. No menu da esquerda, selecione **fluxos de utilizador**e, em seguida, selecione **novo fluxo de utilizador**.
2. Selecione o **reposição de palavra-passe** fluxo de utilizador no separador recomendado.
3. Introduza um **nome** para o fluxo de utilizador. Por exemplo, *passwordreset1*.
4. Para **fornecedores de identidade**, ative **repor a palavra-passe com endereço de e-mail**.
5. Em afirmações de aplicação, clique em **mostrar mais** e escolha as afirmações que quer que sejam devolvidas nos tokens de autorização enviados para a sua aplicação. Por exemplo, selecione **ID de Objeto do Utilizador**.
6. Clique em **OK**.
7. Clique em **criar** para adicionar o fluxo de utilizador. Um prefixo de *B2C_1* automaticamente é acrescentado ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de utilizador

1. Na página de descrição geral do fluxo de utilizador que criou, selecione **executar o fluxo de utilizador**.
2. Para **aplicativo**, selecione a aplicação web com o nome *webapp1* que registou anteriormente. O **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **executar o fluxo de utilizador**e, em seguida, inicie sessão com a conta que criou anteriormente.
4. Agora tem a oportunidade de alterar a palavra-passe do utilizador. Clique em **Continue** (Continuar). O token é retornado para `https://jwt.ms` e deverá ser apresentada para.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como:

> [!div class="checklist"]
> * Criar um fluxo de utilizador de inscrição e início de sessão
> * Criar um fluxo de utilizador de edição de perfil
> * Criar um fluxo de utilizador de reposição de palavra-passe

> [!div class="nextstepaction"]
> [Adicionar fornecedores de identidade às suas aplicações no Azure Active Directory B2C](tutorial-add-identity-providers.md)