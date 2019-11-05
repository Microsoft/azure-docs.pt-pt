---
title: Início rápido – exigir MFA (autenticação multifator) para aplicativos específicos com Azure Active Directory acesso condicional | Microsoft Docs
description: Neste guia de início rápido, você aprende como você pode vincular seus requisitos de autenticação ao tipo de aplicativo de nuvem acessado usando o acesso condicional do Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a23f8fc10e0e5a19be1f93cc6d6e5e8e301f86d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474009"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Início rápido: exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional

Para simplificar a experiência de entrada de seus usuários, talvez você queira permitir que eles entrem em seus aplicativos de nuvem usando um nome de usuário e uma senha. No entanto, muitos ambientes têm pelo menos alguns aplicativos para os quais é aconselhável exigir uma forma mais forte de verificação de conta, como a MFA (autenticação multifator). Essa política pode ser verdadeira para acesso ao sistema de email da sua organização ou aos seus aplicativos de RH. No Azure Active Directory (AD do Azure), você pode atingir essa meta com uma política de acesso condicional.

Este guia de início rápido mostra como configurar uma [política de acesso condicional do Azure ad](../active-directory-conditional-access-azure-portal.md) que requer autenticação multifator para um aplicativo de nuvem selecionado em seu ambiente.

![Exemplo de política de acesso condicional no portal do Azure](./media/app-based-mfa/32.png)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o cenário neste guia de início rápido, você precisa de:

- **Acesso a uma edição do Azure ad Premium** – o acesso condicional do Azure AD é uma funcionalidade Azure ad Premium.
- **Uma conta de teste chamada Isabella Simonsen** -se você não souber como criar uma conta de teste, consulte [Adicionar usuários baseados em nuvem](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

O cenário neste guia de início rápido requer que a MFA por usuário não esteja habilitada para sua conta de teste. Para obter mais informações, consulte [como exigir a verificação em duas etapas para um usuário](../authentication/howto-mfa-userstates.md).

## <a name="test-your-experience"></a>Teste sua experiência

O objetivo desta etapa é obter uma impressão da experiência sem uma política de acesso condicional.

**Para inicializar o ambiente:**

1. Entre em seu portal do Azure como Isabella Simonsen.
1. Termine a sessão.

## <a name="create-your-conditional-access-policy"></a>Criar sua política de acesso condicional

Esta seção mostra como criar a política de acesso condicional necessária. O cenário neste guia de início rápido usa:

- O portal do Azure como espaço reservado para um aplicativo de nuvem que requer MFA. 
- O usuário de exemplo para testar a política de acesso condicional.  

Em sua política, defina:

| Definição | Valor |
| --- | --- |
| Utilizadores e grupos | Isabella Simonsen |
| Aplicativos de nuvem | Gerenciamento de Microsoft Azure |
| Conceder acesso | Exigir autenticação multifator |

![Política de acesso condicional expandida](./media/app-based-mfa/31.png)

**Para configurar sua política de acesso condicional:**

1. Entre em seu [portal do Azure](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Na portal do Azure, procure e selecione **Azure Active Directory**.

   ![Azure Active Directory](./media/app-based-mfa/02.png)

1. Na página **Azure Active Directory** , na seção **segurança** , clique em **acesso condicional**.

   ![Acesso Condicional](./media/app-based-mfa/03.png)

1. Na página **acesso condicional** , na barra de ferramentas na parte superior, clique em **nova política**.

   ![Adicionar](./media/app-based-mfa/04.png)

1. Na **nova** página, na caixa de texto **nome** , digite **exigir MFA para acesso portal do Azure**.

   ![Nome](./media/app-based-mfa/05.png)

1. Na seção **atribuição** , clique em **usuários e grupos**.

   ![Utilizadores e grupos](./media/app-based-mfa/06.png)

1. Na página **usuários e grupos** , execute as seguintes etapas:

   ![Utilizadores e grupos](./media/app-based-mfa/24.png)

   1. Clique em **Selecionar usuários e grupos**e selecione **usuários e grupos**.
   1. Clique em **Selecionar**.
   1. Na página **selecionar** , selecione **Isabella Simonsen**e clique em **selecionar**.
   1. Na página **usuários e grupos** , clique em **concluído**.

1. Clique em **aplicativos de nuvem**.

   ![Aplicativos de nuvem](./media/app-based-mfa/08.png)

1. Na página **aplicativos de nuvem** , execute as seguintes etapas:

   ![Selecionar aplicativos de nuvem](./media/app-based-mfa/26.png)

   1. Clique em **selecionar aplicativos**.
   1. Clique em **Selecionar**.
   1. Na página **selecionar** , selecione **Gerenciamento de Microsoft Azure**e clique em **selecionar**.
   1. Na página **aplicativos de nuvem** , clique em **concluído**.

1. Na seção **controles de acesso** , clique em **conceder**.

   ![Controles de acesso](./media/app-based-mfa/10.png)

1. Na página **conceder** , execute as seguintes etapas:

   ![Conceder](./media/app-based-mfa/11.png)

   1. Selecione **conceder acesso**.
   1. Selecione **exigir autenticação multifator**.
   1. Clique em **Selecionar**.

1. Na seção **habilitar política** , clique **em ativar**.

   ![Habilitar política](./media/app-based-mfa/18.png)

1. Clique em **Criar**.

## <a name="evaluate-a-simulated-sign-in"></a>Avaliar uma entrada simulada

Agora que você configurou sua política de acesso condicional, provavelmente desejará saber se ela funciona conforme o esperado. Como uma primeira etapa, use a ferramenta de política de acesso condicional What If para simular uma entrada de seu usuário de teste. A simulação estima o impacto que essa entrada tem em suas políticas e gera um relatório de simulação.  

Para inicializar a ferramenta de avaliação da política de **What If** , defina:

- **Isabella Simonsen** como usuário
- **Gerenciamento de Microsoft Azure** como aplicativo de nuvem

Clicar **What If** cria um relatório de simulação que mostra:

- **Exigir MFA para acesso portal do Azure** em **políticas que serão aplicadas**
- **Exigir autenticação multifator** como **controles de concessão**.

![Ferramenta de política de hipóteses](./media/app-based-mfa/23.png)

**Para avaliar sua política de acesso condicional:**

1. Na página [acesso condicional-políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) , no menu na parte superior, clique em **What If**.  

   ![What If](./media/app-based-mfa/14.png)

1. Clique em **usuários**, selecione **Isabella Simonsen**e clique em **selecionar**.

   ![Utilizador](./media/app-based-mfa/15.png)

1. Para selecionar um aplicativo de nuvem, execute as seguintes etapas:

   ![Aplicativos de nuvem](./media/app-based-mfa/16.png)

   1. Clique em **aplicativos de nuvem**.
   1. Na **página aplicativos de nuvem**, clique em **selecionar aplicativos**.
   1. Clique em **Selecionar**.
   1. Na página **selecionar** , selecione **Gerenciamento de Microsoft Azure**e clique em **selecionar**.
   1. Na página aplicativos de nuvem, clique em **concluído**.

1. Clique em **What If**.

## <a name="test-your-conditional-access-policy"></a>Testar sua política de acesso condicional

Na seção anterior, você aprendeu como avaliar uma entrada simulada. Além de uma simulação, você também deve testar sua política de acesso condicional para garantir que ela funcione conforme o esperado.

Para testar sua política, tente entrar no seu [portal do Azure](https://portal.azure.com) usando sua conta de teste do **Isabella Simonsen** . Você deve ver uma caixa de diálogo que exige que você configure sua conta para verificação de segurança adicional.

![Multi-Factor Authentication](./media/app-based-mfa/22.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o usuário de teste e a política de acesso condicional:

- Se você não souber como excluir um usuário do Azure AD, consulte [excluir usuários do Azure ad](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Para excluir sua política, selecione sua política e clique em **excluir** na barra de ferramentas de acesso rápido.

    ![Multi-Factor Authentication](./media/app-based-mfa/33.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Exigir que os termos de uso sejam aceitos](require-tou.md)
> [bloquear o acesso quando um risco de sessão for detectado](app-sign-in-risk.md)
