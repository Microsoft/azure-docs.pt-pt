---
title: Acesso Condicional requer termos de utilização - Diretório Ativo Azure
description: Neste arranque rápido, aprende como pode exigir que os seus termos de utilização sejam aceites antes de o acesso a aplicações de cloud selecionadas ser concedido pelo Azure Ative Directory Conditional Access.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74380093"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Quickstart: Exigir que os termos de uso sejam aceites antes de aceder a aplicações na nuvem

Antes de aceder a certas aplicações na nuvem no seu ambiente, é possível que queira obter o consentimento dos utilizadores sob a forma de aceitar os seus termos de utilização (ToU). O Acesso Condicional azure Ative (Azure AD) fornece-lhe:

- Um método simples para configurar a UA
- A opção de exigir a aceitação dos seus termos de utilização através de uma política de Acesso Condicional  

Este quickstart mostra como configurar uma política de [Acesso Condicional Azure AD](../active-directory-conditional-access-azure-portal.md) que requer que um ToU seja aceite para uma aplicação de nuvem selecionada no seu ambiente.

![Criar política](./media/require-tou/5555.png)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar o cenário neste arranque rápido, precisa de:

- **Acesso a uma edição Azure AD Premium** - Azure AD Conditional Access é uma capacidade Azure AD Premium.
- Uma conta de **teste chamada Isabella Simonsen** - Se não sabe como criar uma conta de teste, consulte Adicionar utilizadores [baseados na nuvem](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Teste o seu sign-in

O objetivo deste passo é obter uma impressão da experiência de inscrição sem uma política de Acesso Condicional.

**Para testar o seu sign-in:**

1. Inscreva-se no seu [portal Azure](https://portal.azure.com/) como Isabella Simonsen.
1. Termine a sessão.

## <a name="create-your-terms-of-use"></a>Crie os seus termos de utilização

Esta secção fornece-lhe os passos para criar uma amostra de ToU. Quando cria um ToU, seleciona um valor para **impor com modelos**de política de acesso condicional . A seleção da **política personalizada** abre o diálogo para criar uma nova política de Acesso Condicional assim que o seu ToU foi criado.

**Para criar os seus termos de utilização:**

1. No Microsoft Word, crie um novo documento.
1. Digite **os meus termos de utilização**e, em seguida, guarde o documento no seu computador como **mytou.pdf**.
1. Inscreva-se no seu [portal Azure](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. No portal Azure, no navbar esquerdo, clique no **Diretório Ativo Azure**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Na página do **Diretório Ativo Azure,** na secção **Segurança,** clique em **Acesso Condicional**.

   ![Acesso Condicional](./media/require-tou/03.png)

1. Na secção **Gerir,** clique em **Termos de utilização**.

   ![Termos de utilização](./media/require-tou/04.png)

1. No menu em cima, clique em **novos termos.**

   ![Termos de utilização](./media/require-tou/05.png)

1. Na página **de novos termos de utilização:**

   ![Termos de utilização](./media/require-tou/112.png)

   1. Na caixa de texto **Name,** **escreva My TOU**.
   1. Na caixa de **texto do nome display,** escreva **My TOU**.
   1. Faça upload dos seus termos de utilização do ficheiro PDF.
   1. Como **Idioma,** selecione **inglês.**
   1. Como **exigir que os utilizadores expandam os termos de utilização,** selecione **On**.
   1. Como **Aplicar com modelos**de política de acesso condicional, selecione a política **personalizada**.
   1. Clique em **Criar**.

## <a name="create-your-conditional-access-policy"></a>Crie a sua política de acesso condicional

Esta secção mostra como criar a política de acesso condicional necessária. O cenário neste arranque rápido usa:

- O portal Azure como espaço reservado para uma aplicação na nuvem que requer que o seu ToU seja aceite. 
- O utilizador da amostra para testar a política de Acesso Condicional.  

Na sua política, estabeleça:

| Definição | Valor |
| --- | --- |
| Utilizadores e grupos | Isabel Simonsen |
| Aplicativos em nuvem | Gestão Microsoft Azure |
| Conceder acesso | O meu tou |

![Criar política](./media/require-tou/1234.png)

**Para configurar a sua política de Acesso Condicional:**

1. Na página **Nova,** na caixa de texto **Name,** escreva **TOU para Isabella**.

   ![Nome](./media/require-tou/71.png)

1. Na secção **Atribuição,** clique em **Utilizadores e grupos**.

   ![Utilizadores e grupos](./media/require-tou/06.png)

1. Na página **utilizadores e grupos:**

   ![Utilizadores e grupos](./media/require-tou/24.png)

   1. Clique em **Selecionar utilizadores e grupos**e, em seguida, selecione **Utilizadores e grupos**.
   1. Clique em **Selecionar**.
   1. Na página **Select,** selecione **Isabella Simonsen**, e, em seguida, clique em **Selecionar**.
   1. Na página **Utilizadores e grupos,** clique **em Done**.
1. Clique em **aplicativos Cloud**.

   ![Aplicativos em nuvem](./media/require-tou/08.png)

1. Na página de **aplicações cloud:**

   ![Selecione aplicativos em nuvem](./media/require-tou/26.png)

   1. Clique em **selecionar aplicações**.
   1. Clique em **Selecionar**.
   1. Na página **Select,** selecione **Microsoft Azure Management**, e, em seguida, clique em **Selecionar**.
   1. Na página de **aplicações Cloud,** clique **em Done**.
1. Na secção **de controlos de acesso,** clique em **Grant**.

   ![Controlos de acesso](./media/require-tou/10.png)

1. Na **página** Grant:

   ![Concessão](./media/require-tou/111.png)

   1. Selecione **acesso ao Grant**.
   1. Selecione **o meu tou**.
   1. Clique em **Selecionar**.
1. Na secção **política Ativação,** clique **em .**

   ![Ativar política](./media/require-tou/18.png)

1. Clique em **Criar**.

## <a name="evaluate-a-simulated-sign-in"></a>Avaliar um sessão de inscrição simulada

Agora que configuraste a tua política de Acesso Condicional, provavelmente queres saber se funciona como esperado. Como primeiro passo, utilize o Acesso Condicional e se a ferramenta política simular um sessão do utilizador do teste. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação.  

Para inicializar a ferramenta de avaliação **de e-políticas,** definir:

- **Isabella Simonsen** como utilizadora
- **Microsoft Azure Management** como app cloud

Clicar **no que se** criar um relatório de simulação que mostra:

- **Exigir TOU para Isabella** ao abrigo de **políticas que se aplicarão**
- **O meu toU** como Controlo de **Subvenções.**

![E se ferramenta política](./media/require-tou/79.png)

**Para avaliar a sua política de Acesso Condicional:**

1. Na página [Acesso Condicional - Políticas,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) no menu em cima, clique no **What If**.  

   ![Hipótese](./media/require-tou/14.png)

1. Clique em **Utilizadores,** selecione **Isabella Simonsen,** e depois clique em **Selecionar**.

   ![Utilizador](./media/require-tou/15.png)

1. Para selecionar uma aplicação em nuvem:

   ![Aplicativos em nuvem](./media/require-tou/16.png)

   1. Clique em **aplicativos Cloud**.
   1. Na página de **aplicações Cloud,** clique em **Selecionar aplicações**.
   1. Clique em **Selecionar**.
   1. Na página **Select,** selecione **Microsoft Azure Management**, e, em seguida, clique em **Selecionar**.
   1. Na página de aplicações em nuvem, clique **em Done**.
1. Clique **no que se**.

## <a name="test-your-conditional-access-policy"></a>Teste a sua política de acesso condicional

Na secção anterior, aprendeu a avaliar um sinal de inscrição simulado. Além de uma simulação, deve também testar a sua política de Acesso Condicional para garantir que funciona como esperado.

Para testar a sua política, tente iniciar sessão no seu [portal Azure](https://portal.azure.com) utilizando a sua conta de teste **Isabella Simonsen.** Deve ver um diálogo que o exija aceitar os seus termos de utilização.

![Termos de utilização](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o utilizador do teste e a política de acesso condicional:

- Se não souber apagar um utilizador da AD Azure, consulte [Apagar utilizadores do Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Para eliminar a sua política, selecione a sua política e, em seguida, clique em **Eliminar** na barra de ferramentas de acesso rápido.

    ![Multi-factor authentication](./media/require-tou/33.png)

- Para eliminar os seus termos de utilização, selecione-os e, em seguida, clique em **Eliminar os termos** na barra de ferramentas por cima.

    ![Multi-factor authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Exigir MFA para aplicações específicas](app-based-mfa.md)
> Bloquear acesso quando um risco de[sessão é detetado](app-sign-in-risk.md)
