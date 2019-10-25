---
title: Início rápido – bloquear o acesso quando um risco de sessão for detectado com Azure Active Directory acesso condicional | Microsoft Docs
description: Neste guia de início rápido, você aprende a configurar uma política de acesso condicional do Azure Active Directory (Azure AD) para bloquear entradas com base em riscos de sessão.
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
ms.openlocfilehash: 619f1ea3bae001d25eb520f43da33ca94a3160c8
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880343"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Início rápido: bloquear o acesso quando um risco de sessão for detectado com Azure Active Directory acesso condicional  

Para manter seu ambiente protegido, talvez você queira bloquear usuários suspeitos de entrar. A [proteção de identidade do Azure Active Directory (AD do Azure)](../active-directory-identityprotection.md) analisa cada entrada e calcula a probabilidade de uma tentativa de entrada não ter sido executada pelo proprietário legítimo de uma conta de usuário. A probabilidade (baixa, média, alta) é indicada na forma de um valor calculado chamado [níveis de risco de entrada](conditions.md#sign-in-risk). Ao definir a condição de risco de entrada, você pode configurar uma política de acesso condicional para responder a níveis de risco de entrada específicos.

Este guia de início rápido mostra como configurar uma [política de acesso condicional](../active-directory-conditional-access-azure-portal.md) que bloqueia uma entrada quando um nível de risco de entrada configurado for detectado.

![Criar política](./media/app-sign-in-risk/1000.png)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o cenário deste tutorial, precisa de:

- **Acesso a uma edição Azure ad Premium P2** – embora o acesso condicional seja um recurso Azure ad Premium P1, você precisa de uma edição P2 porque o cenário deste início rápido requer a proteção de identidade.
- **Proteção de identidade** -o cenário neste guia de início rápido requer que o Identity Protection esteja habilitado. Se você não souber como habilitar a proteção de identidade, consulte [habilitando Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md).
- **Tor browser** – o [navegador do Tor](https://www.torproject.org/projects/torbrowser.html.en) foi projetado para ajudá-lo a preservar sua privacidade online. A proteção de identidade detecta uma entrada de um navegador Tor como entradas de endereços IP anônimos, que têm um nível de risco médio. Para obter mais informações, consulte [Azure Active Directory as detecções de risco](../reports-monitoring/concept-risk-events.md).  
- **Uma conta de teste chamada Alain Charon** -se você não souber como criar uma conta de teste, consulte [Adicionar usuários baseados em nuvem](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testar sua entrada

O objetivo desta etapa é garantir que sua conta de teste possa acessar seu locatário usando o navegador Tor.

**Para testar sua entrada:**

1. Entre em seu [portal do Azure](https://portal.azure.com) como **Alain Charon**.
1. Termine a sessão.

## <a name="create-your-conditional-access-policy"></a>Criar sua política de acesso condicional

O cenário neste guia de início rápido usa uma entrada de um navegador Tor para gerar entradas detectadas **de detecção de risco de endereços IP anônimos** . O nível de risco dessa detecção de risco é médio. Para responder a essa detecção de risco, defina a condição de risco de entrada como médio. Em um ambiente de produção, você deve definir a condição de risco de entrada como alta ou média e alta.

Esta seção mostra como criar a política de acesso condicional necessária. Em sua política, defina:

| Definição | Valor |
| --- | --- |
| Utilizadores e grupos | Alain Charon  |
| Aplicativos de nuvem | Todos os aplicativos de nuvem |
| Risco de entrada | Médio |
| Conceder | Bloquear acesso |

![Criar política](./media/app-sign-in-risk/130.png)

**Para configurar sua política de acesso condicional:**

1. Entre em seu [portal do Azure](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Na portal do Azure, na barra de navegação à esquerda, clique em **Azure Active Directory**.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. Na página **Azure Active Directory** , na seção **segurança** , clique em **acesso condicional**.

   ![Acesso Condicional](./media/app-sign-in-risk/03.png)

1. Na página **acesso condicional** , na barra de ferramentas na parte superior, clique em **Adicionar**.

   ![Nome](./media/app-sign-in-risk/108.png)

1. Na página **novo** , na caixa de texto **nome** , digite **bloquear acesso para nível de risco médio**.

   ![Nome](./media/app-sign-in-risk/104.png)

1. Na seção **atribuição** , clique em **usuários e grupos**.

   ![Utilizadores e grupos](./media/app-sign-in-risk/06.png)

1. Na página **usuários e grupos** :

   ![Acesso Condicional](./media/app-sign-in-risk/107.png)

   1. Clique em **Selecionar usuários e grupos**e selecione **usuários e grupos**.
   1. Clique em **Selecionar**.
   1. Na página **selecionar** , selecione **Alain Charon**e clique em **selecionar**.
   1. Na página **usuários e grupos** , clique em **concluído**.
1. Clique em **aplicativos de nuvem**.

   ![Aplicativos de nuvem](./media/app-sign-in-risk/08.png)

1. Na página **aplicativos de nuvem** :

   ![Acesso Condicional](./media/app-sign-in-risk/109.png)

   1. Clique em **todos os aplicativos de nuvem**.
   1. Clique em **Concluído**.
1. Clique em **condições**.

   ![Controles de acesso](./media/app-sign-in-risk/19.png)

1. Na página **condições** :

   ![Nível de risco de entrada](./media/app-sign-in-risk/21.png)

   1. Clique em **risco de entrada**.
   1. Como **Configurar**, clique em **Sim**.
   1. Em nível de risco de entrada, selecione **médio**.
   1. Clique em **Selecionar**.
   1. Na página **condições** , clique em **concluído**.
1. Na seção **controles de acesso** , clique em **conceder**.

   ![Controles de acesso](./media/app-sign-in-risk/10.png)

1. Na página **conceder** :

   ![Acesso Condicional](./media/app-sign-in-risk/105.png)

   1. Selecione **bloquear acesso**.
   1. Clique em **Selecionar**.
1. Na seção **habilitar política** , clique **em ativar**.

   ![Habilitar política](./media/app-sign-in-risk/18.png)

1. Clique em **Criar**.

## <a name="evaluate-a-simulated-sign-in"></a>Avaliar uma entrada simulada

Agora que você configurou sua política de acesso condicional, provavelmente desejará saber se ela funciona conforme o esperado. Como uma primeira etapa, use a ferramenta de política de acesso condicional **What If** para simular uma entrada de seu usuário de teste. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação.  

Quando você executa a **ferramenta de política What If** para esse cenário, o **nível bloquear o acesso para risco médio** deve ser listado em **políticas que serão aplicadas**.

![Utilizador](./media/app-sign-in-risk/117.png)

**Para avaliar sua política de acesso condicional:**

1. Na página [acesso condicional-políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) , no menu na parte superior, clique em **What If**.  

   ![What If](./media/app-sign-in-risk/14.png)

1. Clique em **usuário**, selecione **Alan Charon** na página **usuários** e, em seguida, clique em **selecionar**.

   ![Utilizador](./media/app-sign-in-risk/116.png)

1. **Em risco de entrada**, selecione **médio**.

   ![Utilizador](./media/app-sign-in-risk/119.png)

1. Clique em **What If**.

## <a name="test-your-conditional-access-policy"></a>Testar sua política de acesso condicional

Na seção anterior, você aprendeu como avaliar uma entrada simulada. Além de uma simulação, você também deve testar sua política de acesso condicional para certificar-se de que ela funciona conforme o esperado.

Para testar sua política, tente entrar no seu [portal do Azure](https://portal.azure.com) como **Alan Charon** usando o navegador Tor. Sua tentativa de entrada deve ser bloqueada pela política de acesso condicional.

![Autenticação multifator](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o usuário de teste, o navegador Tor e a política de acesso condicional:

- Se você não souber como excluir um usuário do Azure AD, consulte [excluir usuários do Azure ad](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Para excluir sua política, selecione sua política e clique em **excluir** na barra de ferramentas de acesso rápido.

   ![Autenticação multifator](./media/app-sign-in-risk/33.png)

- Para obter instruções para remover o navegador Tor, consulte [desinstalando](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Exigir que os termos de uso sejam aceitos](require-tou.md)
> [exigir MFA para aplicativos específicos](app-based-mfa.md)
