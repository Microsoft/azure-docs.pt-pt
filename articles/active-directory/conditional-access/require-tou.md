---
title: Acesso Condicional requer termos de uso - Azure Ative Directory
description: Neste arranque rápido, aprende como pode exigir que os seus termos de utilização sejam aceites antes de o acesso a aplicações de nuvem selecionadas ser concedido pelo Azure Ative Directory Conditional Access.
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
ms.openlocfilehash: 8ab484e8caaffaf57f19f1fcd1e65f4b8e723f86
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077902"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Quickstart: Exigir que os termos de uso sejam aceites antes de aceder a aplicações na nuvem

Antes de aceder a certas aplicações em nuvem no seu ambiente, poderá querer obter o consentimento dos utilizadores na forma de aceitar os seus termos de utilização (ToU). O Azure Ative Directy (Azure AD) Conditional Access fornece-lhe:

- Um método simples para configurar a ToU
- A opção de exigir a aceitação dos seus termos de uso através de uma política de acesso condicional  

Este quickstart mostra como configurar uma [política de acesso condicional Ad Ad Azure](./overview.md) que requer que uma ToU seja aceite para uma aplicação de nuvem selecionada no seu ambiente.

:::image type="content" source="./media/require-tou/5555.png" alt-text="Screenshot do portal Azure. Um painel que define uma política chamada Require T O U para Isabella é visível." border="false":::

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar o cenário neste arranque rápido, você precisa:

- **O acesso a uma edição Azure AD Premium** - Azure AD Conditional Access é uma capacidade AZure AD Premium.
- **Uma conta de teste chamada Isabella Simonsen** - Se não sabe como criar uma conta de teste, consulte [utilizadores baseados na nuvem](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Teste o seu s-in

O objetivo deste passo é obter uma impressão da experiência de inscrição sem uma política de acesso condicional.

**Para testar o seu sôm-in:**

1. Inscreva-se no seu [portal Azure](https://portal.azure.com/) como Isabella Simonsen.
1. Termine a sessão.

## <a name="create-your-terms-of-use"></a>Crie os seus termos de utilização

Esta secção fornece-lhe os passos para criar uma amostra de ToU. Ao criar uma ToU, seleciona um valor para **impor com modelos de política de acesso condicional.** A seleção da **política Personalizada** abre o diálogo para criar uma nova política de Acesso Condicional assim que a sua ToU foi criada.

**Para criar os seus termos de utilização:**

1. No Microsoft Word, crie um novo documento.
1. Digite **os meus termos de utilização** e guarde o documento no seu computador como **mytou.pdf** .
1. Inscreva-se no seu [portal Azure](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de acesso condicional.
1. No portal Azure, na navbar esquerda, clique no **Diretório Ativo Azure** .

   ![Azure Active Directory](./media/require-tou/02.png)

1. Na página **Azure Ative Directory,** na secção **Segurança,** clique em **Acesso Condicional** .

   ![Acesso Condicional](./media/require-tou/03.png)

1. Na secção **Gerir,** clique em **Termos de utilização** .

   :::image type="content" source="./media/require-tou/04.png" alt-text="Screenshot do portal Azure. Um painel que define uma política chamada Require T O U para Isabella é visível." border="false":::

1. No menu em cima, clique em **Novos termos.**

   :::image type="content" source="./media/require-tou/05.png" alt-text="Screenshot do portal Azure. Um painel que define uma política chamada Require T O U para Isabella é visível." border="false":::

1. Na página **novos termos de utilização:**

   :::image type="content" source="./media/require-tou/112.png" alt-text="Screenshot do portal Azure. Um painel que define uma política chamada Require T O U para Isabella é visível." border="false":::

   1. Na caixa de texto **Name,** **digite A Minha TOU.**
   1. Na caixa de texto **do nome do Visor,** **digite A Minha TOU** .
   1. Faça upload dos seus termos de utilização do ficheiro PDF.
   1. Como **Língua** , selecione **Inglês** .
   1. Como **Exigir que os utilizadores expandam os termos de utilização** , selecione **On** .
   1. Como **Impor com modelos de política de acesso condicional,** selecione **Política personalizada** .
   1. Clique em **Criar** .

## <a name="create-your-conditional-access-policy"></a>Crie a sua política de Acesso Condicional

Esta secção mostra como criar a política de acesso condicional necessária. O cenário neste arranque rápido utiliza:

- O portal Azure como espaço reservado para uma aplicação em nuvem que requer que o seu ToU seja aceite. 
- O utilizador da amostra para testar a política de Acesso Condicional.  

Na sua política, desa um set:

| Definição | Valor |
| --- | --- |
| Utilizadores e grupos | Isabel Simonsen |
| Aplicações na cloud | Microsoft Azure Management |
| Conceder acesso | O meu TOU |

:::image type="content" source="./media/require-tou/1234.png" alt-text="Screenshot do portal Azure. Um painel que define uma política chamada Require T O U para Isabella é visível." border="false":::

**Para configurar a sua política de acesso condicional:**

1. Na **nova** página, na caixa de texto **Name,** tipo **Exigir TOU para Isabella** .

   ![Name](./media/require-tou/71.png)

1. Na secção **Atribuição,** clique em **Utilizadores e grupos** .

   :::image type="content" source="./media/require-tou/06.png" alt-text="Screenshot do portal Azure. Um painel que define uma política chamada Require T O U para Isabella é visível." border="false":::

1. Na página **de Utilizadores e grupos:**

   :::image type="content" source="./media/require-tou/24.png" alt-text="Screenshot do portal Azure. Um painel que define uma política chamada Require T O U para Isabella é visível." border="false":::

   1. Clique **em Selecionar utilizadores e grupos** e, em seguida, selecione **Utilizadores e grupos** .
   1. Clique em **Selecionar** .
   1. Na página **Select,** selecione **Isabella Simonsen** e, em seguida, clique em **Select** .
   1. Na página **de Utilizadores e grupos,** clique em **'Fazer'.**
1. Clique **em aplicativos Cloud** .

   :::image type="content" source="./media/require-tou/08.png" alt-text="Screenshot do portal Azure. Um painel que define uma política chamada Require T O U para Isabella é visível." border="false":::

1. Na página de **aplicativos Cloud:**

   ![Selecione aplicativos de nuvem](./media/require-tou/26.png)

   1. Clique **em Selecionar apps** .
   1. Clique em **Selecionar** .
   1. Na página **Select,** selecione **Microsoft Azure Management** e, em seguida, clique em **Select** .
   1. Na página de **aplicativos Cloud,** clique em **'Fazer'.**
1. Na secção **controlos de acesso,** clique em **Grant** .

   ![Controlos de acesso](./media/require-tou/10.png)

1. Na página **grant:**

   ![Concessão](./media/require-tou/111.png)

   1. Selecione **Conceder acesso** .
   1. Selecione **o meu TOU** .
   1. Clique em **Selecionar** .
1. Na secção **De Ativação,** clique **em On** .

   ![Ativar política](./media/require-tou/18.png)

1. Clique em **Criar** .

## <a name="evaluate-a-simulated-sign-in"></a>Avaliar um sinal simulado

Agora que configuraste a tua política de Acesso Condicional, provavelmente queres saber se funciona como esperado. Como primeiro passo, utilize o Acesso Condicional e se a ferramenta de política simular uma entrada do utilizador de teste. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação.  

Para rubricar a ferramenta de avaliação de políticas **E,** desemocione:

- **Isabella Simonsen** como utilizadora
- **Microsoft Azure Management** como app cloud

Clicar **em E Se** criar um relatório de simulação que mostra:

- **Exigir TOU para Isabella** ao abrigo **de políticas que se aplicarão**
- **A minha TOU** como **Grant Controls.**

![E se a ferramenta de política](./media/require-tou/79.png)

**Para avaliar a sua política de Acesso Condicional:**

1. Na página [Acesso Condicional - Políticas,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) no menu em cima, clique em **E Se** .  

   ![Hipótese](./media/require-tou/14.png)

1. Clique **em Utilizadores,** selecione **Isabella Simonsen** e, em seguida, clique em **Select** .

   ![Utilizador](./media/require-tou/15.png)

1. Para selecionar uma aplicação em nuvem:

   :::image type="content" source="./media/require-tou/16.png" alt-text="Screenshot do portal Azure. Um painel que define uma política chamada Require T O U para Isabella é visível." border="false":::

   1. Clique **em aplicativos Cloud** .
   1. Na página de **aplicações cloud** , clique **em Selecionar apps** .
   1. Clique em **Selecionar** .
   1. Na página **Select,** selecione **Microsoft Azure Management** e, em seguida, clique em **Select** .
   1. Na página de aplicativos na nuvem, clique em **'Fazer'.**
1. Clique **em E Se** .

## <a name="test-your-conditional-access-policy"></a>Teste a sua política de Acesso Condicional

Na secção anterior, aprendeu a avaliar um s-in simulado. Além de uma simulação, deve também testar a sua política de Acesso Condicional para garantir que funciona como esperado.

Para testar a sua política, tente entrar no seu [portal Azure](https://portal.azure.com) utilizando a sua conta de teste **Isabella Simonsen.** Deve ver um diálogo que o exija a aceitar os seus termos de utilização.

:::image type="content" source="./media/require-tou/57.png" alt-text="Screenshot do portal Azure. Um painel que define uma política chamada Require T O U para Isabella é visível." border="false":::

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o utilizador de teste e a política de acesso condicional:

- Se não souber eliminar um utilizador AD Azure, consulte [eliminar os utilizadores do AZure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Para eliminar a sua política, selecione a sua política e, em seguida, clique em **Eliminar** na barra de ferramentas de acesso rápido.

    :::image type="content" source="./media/require-tou/33.png" alt-text="Screenshot do portal Azure. Um painel que define uma política chamada Require T O U para Isabella é visível." border="false":::

- Para eliminar os seus termos de utilização, selecione-o e, em seguida, clique em **Eliminar os termos** na barra de ferramentas em cima.

    :::image type="content" source="./media/require-tou/29.png" alt-text="Screenshot do portal Azure. Um painel que define uma política chamada Require T O U para Isabella é visível." border="false":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Exigir o MFA para aplicações específicas](../authentication/tutorial-enable-azure-mfa.md)