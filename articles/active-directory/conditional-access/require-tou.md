---
title: Início rápido - necessitam de termos de utilização para ser aceite antes de aceder a aplicações na cloud que estão protegidas pelo Azure Active Directory condicional acesso | Documentos da Microsoft
description: Este guia de introdução, saiba como pode exigir que os seus termos de utilização são aceites antes do acesso a aplicações na cloud selecionada é concedido pelo Azure Active Directory condicional acesso.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a3523a050a021f3a98c144efe14d692704fba63
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112221"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Início rápido: Exigir a termos de utilização para ser aceite antes de aceder a aplicações na cloud

Antes de aceder a determinadas aplicações na cloud no seu ambiente, pode querer obter consentimento de utilizadores na forma de aceitar os termos de utilização (ToU). Acesso condicional do Azure Active Directory (Azure AD) fornecem-lhe:

- Um método simples para configurar os termos de utilização
- A opção para exigir a aceitar os termos de utilização por meio de uma política de acesso condicional  

Este início rápido mostra como configurar uma [política de acesso condicional do Azure AD](../active-directory-conditional-access-azure-portal.md) que requer um termos de utilização sejam aceites para uma aplicação em nuvem selecionado no seu ambiente.

![Criar política](./media/require-tou/5555.png)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o cenário neste início rápido, precisa de:

- **Acesso a uma edição do Azure AD Premium** -acesso condicional do Azure AD é um recurso do Azure AD Premium.
- **Uma conta de teste chamado Isabella Simonsen** – se não saiba como criar uma conta de teste, consulte [adicionar utilizadores com base na cloud](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testar o início de sessão

O objetivo deste passo é obter uma impressão da experiência de início de sessão sem uma política de acesso condicional.

**Para testar o início de sessão:**

1. Inicie sessão no seu [portal do Azure](https://portal.azure.com/) como Isabella Simonsen.
1. Termine a sessão.

## <a name="create-your-terms-of-use"></a>Criar os termos de utilização

Esta secção fornece os passos para criar um exemplo de termos de utilização. Quando cria um termos de utilização, selecione um valor para **impor com modelos de política de acesso condicional**. Selecionando **política personalizada do** abre a caixa de diálogo para criar uma nova política de acesso condicional, assim que tem sido criado os termos de utilização.

**Para criar os termos de utilização:**

1. No Microsoft Word, crie um novo documento.

1. Tipo **meu termos de utilização**e, em seguida, guarde o documento no seu computador como **mytou.pdf**.

1. Inicie sessão no seu [portal do Azure](https://portal.azure.com) como administrador global, administrador de segurança ou um administrador de acesso condicional.

1. No portal do Azure, na barra de navegação esquerda, clique em **do Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Na **do Azure Active Directory** página, além do **segurança** secção, clique em **acesso condicional**.

   ![Acesso Condicional](./media/require-tou/03.png)

1. Na **Manage** secção, clique em **termos de utilização**.

   ![Termos de utilização](./media/require-tou/04.png)

1. No menu na parte superior, clique em **novos termos**.

   ![Termos de utilização](./media/require-tou/05.png)

1. Sobre o **novos termos de utilização** página:

   ![Termos de utilização](./media/require-tou/112.png)

   1. Na **nome** caixa de texto, tipo **My TOU**.

   1. Na **nome a apresentar** caixa de texto, tipo **My TOU**.

   1. Carregue os seus termos de utilize um ficheiro PDF.

   1. Como **linguagem**, selecione **inglês**.

   1. Como **exigir que os utilizadores expandam os termos de utilização**, selecione **no**.

   1. Como **impor com modelos de política de acesso condicional**, selecione **política personalizada do**.

   1. Clique em **Criar**.

## <a name="create-your-conditional-access-policy"></a>Criar a sua política de acesso condicional

Esta secção mostra como criar a política de acesso condicional necessária. O cenário neste início rápido utiliza:

- Portal do Azure como marcador de posição para uma aplicação de cloud que requer o termos de utilização a ser aceites. 
- O utilizador de exemplo para testar a política de acesso condicional.  

Na sua política, defina:

| Definição | Value |
| --- | --- |
| Utilizadores e grupos | Isabella Simonsen |
| Aplicações na cloud | Microsoft Azure Management |
| Conceder acesso | Meu termos de utilização |

![Criar política](./media/require-tou/1234.png)

**Para configurar a política de acesso condicional:**

1. No **New** na página a **nome** caixa de texto, tipo **necessitam de termos de utilização para Isabella**.

   ![Name](./media/require-tou/71.png)

1. Na **atribuição** secção, clique em **utilizadores e grupos**.

   ![Utilizadores e grupos](./media/require-tou/06.png)

1. Sobre o **utilizadores e grupos** página:

   ![Utilizadores e grupos](./media/require-tou/24.png)

   1. Clique em **selecionar utilizadores e grupos**e, em seguida, selecione **utilizadores e grupos**.

   1. Clique em **Selecionar**.

   1. Sobre o **selecionar** , selecione **Isabella Simonsen**e, em seguida, clique em **selecionar**.

   1. Sobre o **utilizadores e grupos** página, clique em **feito**.

1. Clique em **aplicações na Cloud**.

   ![Aplicações na cloud](./media/require-tou/08.png)

1. Sobre o **aplicações na Cloud** página:

   ![Selecione aplicações na cloud](./media/require-tou/26.png)

   1. Clique em **selecionar aplicações**.

   1. Clique em **Selecionar**.

   1. Sobre o **selecionar** , selecione **Microsoft Azure Management**e, em seguida, clique em **selecionar**.

   1. Sobre o **aplicações na Cloud** página, clique em **feito**.

1. Na **controlos de acesso** secção, clique em **concessão**.

   ![Controlos de acesso](./media/require-tou/10.png)

1. Sobre o **concessão** página:

   ![Concessão](./media/require-tou/111.png)

   1. Selecione **conceder acesso**.

   1. Selecione **meu TOU**.

   1. Clique em **Selecionar**.

1. Na **ativar política** secção, clique em **no**.

   ![Ativar política](./media/require-tou/18.png)

1. Clique em **Criar**.

## <a name="evaluate-a-simulated-sign-in"></a>Avaliar um simulado início de sessão

Agora que configurou a política de acesso condicional, provavelmente quer saber se ele funciona conforme esperado. Como primeiro passo, utilize o acesso condicional e se a política de ferramenta para simular um início de sessão do seu utilizador de teste. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação.  

Para inicializar o que se o conjunto de ferramentas de avaliação da política,:

- **Isabella Simonsen** como utilizador
- **Microsoft Azure Management** como aplicação de cloud

Clicar **e se** cria um relatório de simulação, que mostra:

- **Exigir a termos de utilização para Isabella** em **políticas que serão aplicadas**
- **Meu TOU** como **conceder controlos**.

![E se a ferramenta de política](./media/require-tou/79.png)

**Para avaliar a sua política de acesso condicional:**

1. Sobre o [acesso condicional - políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) página, no menu na parte superior, clique em **e se**.  

   ![E se](./media/require-tou/14.png)

1. Clique em **usuários**, selecione **Isabella Simonsen**e, em seguida, clique em **selecionar**.

   ![Utilizador](./media/require-tou/15.png)

1. Para selecionar uma aplicação na cloud:

   ![Aplicações na cloud](./media/require-tou/16.png)

   1. Clique em **aplicações na Cloud**.

   1. Sobre o **página de aplicações na Cloud**, clique em **selecionar aplicações**.

   1. Clique em **Selecionar**.

   1. Sobre o **selecionar** , selecione **Microsoft Azure Management**e, em seguida, clique em **selecionar**.

   1. Na página de aplicações na cloud, clique em **feito**.

1. Clique em **e se**.

## <a name="test-your-conditional-access-policy"></a>Testar a sua política de acesso condicional

Na secção anterior, aprendeu como avaliar um simulado início de sessão. Além de uma simulação, também deve testar sua política de acesso condicional para garantir que funciona conforme esperado.

Para testar a sua política, tente iniciar sessão no seu [portal do Azure](https://portal.azure.com) usando sua **Isabella Simonsen** conta de teste. Deverá ver uma caixa de diálogo que requer que aceite os termos de utilização.

![Termos de utilização](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o utilizador de teste e a política de acesso condicional:

- Se não souber como eliminar um utilizador do Azure AD, veja [eliminar utilizadores do Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Para eliminar a política, selecione a política e, em seguida, clique em **eliminar** na barra de ferramentas de acesso rápido.

    ![Multi-Factor Authentication](./media/require-tou/33.png)

- Para eliminar os termos de utilização, selecioná-lo e, em seguida, clique em **eliminar termos** na barra de ferramentas na parte superior.

    ![Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Exigir a MFA para aplicações específicas](app-based-mfa.md)
> [bloquear o acesso quando é detetado um risco de sessão](app-sign-in-risk.md)
