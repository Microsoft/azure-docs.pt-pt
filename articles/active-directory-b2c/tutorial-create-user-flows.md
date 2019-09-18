---
title: Tutorial – criar fluxos de usuário-Azure Active Directory B2C
description: Saiba como criar fluxos de usuário no portal do Azure para habilitar a inscrição, a entrada e a edição de perfil de usuário para seus aplicativos no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3633ffb09d71bd166cb7366f860662d9b91c7f07
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063402"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutorial: Criar fluxos de usuário no Azure Active Directory B2C

Em seus aplicativos, você pode ter [fluxos de usuário](active-directory-b2c-reference-policies.md) que permitem que os usuários se inscrevam, entrem ou gerenciem seu perfil. Você pode criar vários fluxos de usuário de diferentes tipos em seu locatário Azure Active Directory B2C (Azure AD B2C) e usá-los em seus aplicativos, conforme necessário. Os fluxos de usuário podem ser reutilizados em todos os aplicativos.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um fluxo de usuário de inscrição e entrada
> * Criar um fluxo de usuário de edição de perfil
> * Criar um fluxo de usuário de redefinição de senha

Este tutorial mostra como criar alguns fluxos de usuário recomendados usando o portal do Azure. Se você estiver procurando informações sobre como configurar um fluxo de credenciais de senha de proprietário de recurso (ROPC) em seu aplicativo, consulte [Configurar o fluxo de credenciais de senha do proprietário do recurso em Azure ad B2C](configure-ropc.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Registre seus aplicativos](tutorial-register-applications.md) que fazem parte dos fluxos de usuário que você deseja criar.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Criar um fluxo de usuário de inscrição e entrada

O fluxo de usuário de inscrição e entrada lida com experiências de inscrição e entrada com uma única configuração. Os usuários do seu aplicativo levaram o caminho certo, dependendo do contexto.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.

    ![Filtro de diretório e assinatura com locatário B2C selecionado](./media/tutorial-create-user-flows/switch-directories.PNG)

1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
1. No menu à esquerda em **políticas**, selecione **fluxos de usuário (políticas)** e, em seguida, selecione **novo fluxo de usuário**.

    ![Página fluxos do usuário no portal com o botão novo fluxo de usuário realçado](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na guia **recomendado** , selecione o fluxo de usuário **inscrever-se e entrar** .

    ![Selecione uma página de fluxo de usuário com a inscrição e o fluxo de entrada realçados](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Insira um **nome** para o fluxo de usuário. Por exemplo, *signupsignin1*.
1. Para **provedores de identidade**, selecione **inscrição de email**.

    ![Criar página de fluxo de usuário no portal do Azure com propriedades realçadas](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. Para **atributos e declarações de usuário**, escolha as declarações e os atributos que você deseja coletar e enviar do usuário durante a inscrição. Por exemplo, selecione **Mostrar mais**e, em seguida, escolha atributos e declarações para **país/região**, **nome de exibição**e **CEP**. Clique em **OK**.

    ![Página de seleção de atributos e declarações com três declarações selecionadas](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Clique em **criar** para adicionar o fluxo de usuário. Um prefixo de *B2C_1* é anexado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo do usuário

1. Selecione o fluxo de usuário que você criou para abrir sua página de visão geral e, em seguida, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *webapp1* que você registrou anteriormente. A **URL de resposta** deve `https://jwt.ms`mostrar.
1. Clique em **executar fluxo de usuário**e selecione **inscrever-se agora**.

    ![Página executar fluxo de usuário no portal com o botão Executar fluxo de usuário realçado](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Insira um endereço de email válido, clique em **enviar código de verificação**, insira o código de verificação que você recebe e, em seguida, selecione **verificar código**.
1. Insira uma nova senha e confirme a senha.
1. Selecione seu país e região, insira o nome que você deseja exibir, insira um CEP e, em seguida, clique em **criar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.
1. Agora você pode executar o fluxo do usuário novamente e deve ser capaz de entrar com a conta que você criou. O token retornado inclui as declarações que você selecionou país/região, nome e CEP.

## <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de usuário de edição de perfil

Se você quiser permitir que os usuários editem seu perfil em seu aplicativo, use um fluxo de usuário de edição de perfil.

1. No menu à esquerda da página Visão geral do locatário Azure AD B2C, selecione **fluxos de usuário (políticas)** e, em seguida, selecione **novo fluxo de usuário**.
1. Selecione o fluxo de usuário de **edição de perfil** na guia recomendado.
1. Insira um **nome** para o fluxo de usuário. Por exemplo, *profileediting1*.
1. Para **provedores de identidade**, selecione **entrada de conta local**.
1. Para **atributos de usuário**, escolha os atributos que você deseja que o cliente possa editar em seu perfil. Por exemplo, selecione **Mostrar mais**e, em seguida, escolha os atributos e as declarações para **nome de exibição** e **cargo**. Clique em **OK**.
1. Clique em **criar** para adicionar o fluxo de usuário. Um prefixo de *B2C_1* é anexado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo do usuário

1. Selecione o fluxo de usuário que você criou para abrir sua página de visão geral e, em seguida, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *webapp1* que você registrou anteriormente. A **URL de resposta** deve `https://jwt.ms`mostrar.
1. Clique em **executar fluxo de usuário**e entre com a conta que você criou anteriormente.
1. Agora você tem a oportunidade de alterar o nome de exibição e o cargo do usuário. Clique em **Continue** (Continuar). O token é retornado para `https://jwt.ms` e deve ser exibido para você.

## <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de usuário de redefinição de senha

Para permitir que os usuários do seu aplicativo redefinam sua senha, use um fluxo de usuário de redefinição de senha.

1. No menu à esquerda, selecione **fluxos de usuário (políticas)** e, em seguida, selecione **novo fluxo de usuário**.
1. Selecione o fluxo de usuário de redefinição de **senha** na guia recomendado.
1. Insira um **nome** para o fluxo de usuário. Por exemplo, *passwordreset1*.
1. Para **provedores de identidade**, habilite **Redefinir senha usando endereço de email**.
1. Em declarações do aplicativo, clique em **Mostrar mais** e escolha as declarações que você deseja que sejam retornadas nos tokens de autorização enviados de volta para seu aplicativo. Por exemplo, selecione **ID de Objeto do Utilizador**.
1. Clique em **OK**.
1. Clique em **criar** para adicionar o fluxo de usuário. Um prefixo de *B2C_1* é anexado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo do usuário

1. Selecione o fluxo de usuário que você criou para abrir sua página de visão geral e, em seguida, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *webapp1* que você registrou anteriormente. A **URL de resposta** deve `https://jwt.ms`mostrar.
1. Clique em **executar fluxo de usuário**, verifique o endereço de email da conta que você criou anteriormente e selecione **continuar**.
1. Agora você tem a oportunidade de alterar a senha do usuário. Altere a senha e selecione **continuar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um fluxo de usuário de inscrição e entrada
> * Criar um fluxo de usuário de edição de perfil
> * Criar um fluxo de usuário de redefinição de senha

Em seguida, saiba como adicionar provedores de identidade aos seus aplicativos para habilitar a entrada do usuário com provedores como o Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft ou Twitter.

> [!div class="nextstepaction"]
> [Adicionar provedores de identidade aos seus aplicativos >](tutorial-add-identity-providers.md)