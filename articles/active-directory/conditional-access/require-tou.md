---
title: O acesso condicional requer termos de uso-Azure Active Directory
description: Neste guia de início rápido, você aprende como é possível exigir que os termos de uso sejam aceitos antes que o acesso aos aplicativos de nuvem selecionados seja concedido por Azure Active Directory acesso condicional.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dd1b4cf554e773f49a15ac5cedcbcc5b3e710b9
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380093"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Início rápido: exigir que os termos de uso sejam aceitos antes de acessar os aplicativos de nuvem

Antes de acessar determinados aplicativos de nuvem em seu ambiente, talvez você queira obter consentimento dos usuários na forma de aceitar os termos de uso (ToU). O acesso condicional do Azure Active Directory (AD do Azure) fornece:

- Um método simples para configurar o ToU
- A opção de exigir a aceitação dos seus termos de uso por meio de uma política de acesso condicional  

Este guia de início rápido mostra como configurar uma [política de acesso condicional do Azure ad](../active-directory-conditional-access-azure-portal.md) que exige que um ToU seja aceito para um aplicativo de nuvem selecionado em seu ambiente.

![Criar política](./media/require-tou/5555.png)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o cenário neste guia de início rápido, você precisa de:

- **Acesso a uma edição do Azure ad Premium** – o acesso condicional do Azure AD é uma funcionalidade Azure ad Premium.
- **Uma conta de teste chamada Isabella Simonsen** -se você não souber como criar uma conta de teste, consulte [Adicionar usuários baseados em nuvem](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testar sua entrada

O objetivo desta etapa é obter uma impressão da experiência de entrada sem uma política de acesso condicional.

**Para testar sua entrada:**

1. Entre em seu [portal do Azure](https://portal.azure.com/) como Isabella Simonsen.
1. Termine a sessão.

## <a name="create-your-terms-of-use"></a>Crie seus termos de uso

Esta seção fornece as etapas para criar um exemplo de ToU. Ao criar um ToU, você seleciona um valor para **impor com modelos de política de acesso condicional**. A seleção de **política personalizada** abre a caixa de diálogo para criar uma nova política de acesso condicional assim que sua ToU foi criada.

**Para criar seus termos de uso:**

1. No Microsoft Word, crie um novo documento.
1. Digite **meus termos de uso**e salve o documento em seu computador como **mytou. pdf**.
1. Entre em seu [portal do Azure](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Na portal do Azure, na barra de navegação à esquerda, clique em **Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Na página **Azure Active Directory** , na seção **segurança** , clique em **acesso condicional**.

   ![Acesso Condicional](./media/require-tou/03.png)

1. Na seção **gerenciar** , clique em **termos de uso**.

   ![Termos de utilização](./media/require-tou/04.png)

1. No menu na parte superior, clique em **novos termos**.

   ![Termos de utilização](./media/require-tou/05.png)

1. Na página **novos termos de uso** :

   ![Termos de utilização](./media/require-tou/112.png)

   1. Na caixa de texto **nome** , digite **meu tou**.
   1. Na caixa de texto **nome de exibição** , digite **meu tou**.
   1. Carregue seus termos de uso do arquivo PDF.
   1. Em **idioma**, selecione **Inglês**.
   1. Como **exigir que os usuários expandam os termos de uso**, selecione **ativado**.
   1. Como **impor com modelos de política de acesso condicional**, selecione **política personalizada**.
   1. Clique em **Criar**.

## <a name="create-your-conditional-access-policy"></a>Criar sua política de acesso condicional

Esta seção mostra como criar a política de acesso condicional necessária. O cenário neste guia de início rápido usa:

- O portal do Azure como espaço reservado para um aplicativo de nuvem que exige que seu ToU seja aceito. 
- O usuário de exemplo para testar a política de acesso condicional.  

Em sua política, defina:

| Definição | Valor |
| --- | --- |
| Utilizadores e grupos | Isabella Simonsen |
| Aplicativos de nuvem | Microsoft Azure Management |
| Conceder acesso | Meu TOU |

![Criar política](./media/require-tou/1234.png)

**Para configurar sua política de acesso condicional:**

1. Na página **novo** , na caixa de texto **nome** , digite **exigir tou para Isabella**.

   ![Nome](./media/require-tou/71.png)

1. Na seção **atribuição** , clique em **usuários e grupos**.

   ![Utilizadores e grupos](./media/require-tou/06.png)

1. Na página **usuários e grupos** :

   ![Utilizadores e grupos](./media/require-tou/24.png)

   1. Clique em **Selecionar usuários e grupos**e selecione **usuários e grupos**.
   1. Clique em **Selecionar**.
   1. Na página **selecionar** , selecione **Isabella Simonsen**e clique em **selecionar**.
   1. Na página **usuários e grupos** , clique em **concluído**.
1. Clique em **aplicativos de nuvem**.

   ![Aplicativos de nuvem](./media/require-tou/08.png)

1. Na página **aplicativos de nuvem** :

   ![Selecionar aplicativos de nuvem](./media/require-tou/26.png)

   1. Clique em **selecionar aplicativos**.
   1. Clique em **Selecionar**.
   1. Na página **selecionar** , selecione **Gerenciamento de Microsoft Azure**e clique em **selecionar**.
   1. Na página **aplicativos de nuvem** , clique em **concluído**.
1. Na seção **controles de acesso** , clique em **conceder**.

   ![Controles de acesso](./media/require-tou/10.png)

1. Na página **conceder** :

   ![Conceder](./media/require-tou/111.png)

   1. Selecione **conceder acesso**.
   1. Selecione **meu tou**.
   1. Clique em **Selecionar**.
1. Na seção **habilitar política** , clique **em ativar**.

   ![Ativar política](./media/require-tou/18.png)

1. Clique em **Criar**.

## <a name="evaluate-a-simulated-sign-in"></a>Avaliar uma entrada simulada

Agora que você configurou sua política de acesso condicional, provavelmente desejará saber se ela funciona conforme o esperado. Como uma primeira etapa, use a ferramenta de política de acesso condicional What If para simular uma entrada de seu usuário de teste. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação.  

Para inicializar a ferramenta de avaliação da política de **What If** , defina:

- **Isabella Simonsen** como usuário
- **Gerenciamento de Microsoft Azure** como aplicativo de nuvem

Clicar **What If** cria um relatório de simulação que mostra:

- **Exigir tou para Isabella** em **políticas que serão aplicadas**
- **Meus tou** como **conceder controles**.

![Ferramenta de política de hipóteses](./media/require-tou/79.png)

**Para avaliar sua política de acesso condicional:**

1. Na página [acesso condicional-políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) , no menu na parte superior, clique em **What If**.  

   ![What If](./media/require-tou/14.png)

1. Clique em **usuários**, selecione **Isabella Simonsen**e clique em **selecionar**.

   ![Utilizador](./media/require-tou/15.png)

1. Para selecionar um aplicativo de nuvem:

   ![Aplicativos de nuvem](./media/require-tou/16.png)

   1. Clique em **aplicativos de nuvem**.
   1. Na **página aplicativos de nuvem**, clique em **selecionar aplicativos**.
   1. Clique em **Selecionar**.
   1. Na página **selecionar** , selecione **Gerenciamento de Microsoft Azure**e clique em **selecionar**.
   1. Na página aplicativos de nuvem, clique em **concluído**.
1. Clique em **What If**.

## <a name="test-your-conditional-access-policy"></a>Testar sua política de acesso condicional

Na seção anterior, você aprendeu como avaliar uma entrada simulada. Além de uma simulação, você também deve testar sua política de acesso condicional para garantir que ela funcione conforme o esperado.

Para testar sua política, tente entrar no seu [portal do Azure](https://portal.azure.com) usando sua conta de teste do **Isabella Simonsen** . Você deve ver uma caixa de diálogo que exige que você aceite os termos de uso.

![Termos de utilização](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o usuário de teste e a política de acesso condicional:

- Se você não souber como excluir um usuário do Azure AD, consulte [excluir usuários do Azure ad](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Para excluir sua política, selecione sua política e clique em **excluir** na barra de ferramentas de acesso rápido.

    ![Multi-Factor Authentication](./media/require-tou/33.png)

- Para excluir os termos de uso, selecione-o e clique em **excluir termos** na barra de ferramentas na parte superior.

    ![Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Exigir MFA para aplicativos específicos](app-based-mfa.md)
> [bloquear o acesso quando um risco de sessão for detectado](app-sign-in-risk.md)
