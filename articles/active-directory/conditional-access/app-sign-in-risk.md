---
title: Utilizar deteções de risco com acesso condicional de diretório ativo Azure
description: Neste arranque rápido, você aprende como pode configurar uma política de Acesso Condicional azure Ative (Azure AD) para bloquear inscrições com base nos riscos da sessão.
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
ms.openlocfilehash: be447b001b0b2e14999aac98ba2125f8cbfe9853
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77186618"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Quickstart: Bloquear o acesso quando um risco de sessão é detetado com acesso condicional do Diretório Ativo Azure  

Para manter o ambiente protegido, é melhor bloquear utilizadores suspeitos de iniciars o sessão. A [Azure Ative Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analisa cada entrada e calcula a probabilidade de uma tentativa de entrada não ter sido realizada pelo legítimo proprietário de uma conta de utilizador. A probabilidade (baixa, média, alta) é indicada sob a forma de um valor calculado chamado [níveis de risco de inscrição](concept-conditional-access-conditions.md#sign-in-risk). Ao definir a condição de risco de entrada, pode configurar uma política de Acesso Condicional para responder a níveis específicos de risco de entrada.

Este quickstart mostra como configurar uma política de [acesso condicional](../active-directory-conditional-access-azure-portal.md) que bloqueia um início de sessão quando um nível de risco configurado foi detetado.

![Criar política](./media/app-sign-in-risk/1000.png)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o cenário deste tutorial, precisa de:

- **Acesso a uma edição Azure AD Premium P2** - Embora o Acesso Condicional seja uma capacidade Azure AD Premium P1, você precisa de uma edição P2 porque o cenário neste quickstart requer Proteção de Identidade.
- **Proteção de Identidade** - O cenário neste arranque rápido requer a proteção da identidade ativada. Se não souber como ativar a Proteção de Identidade, consulte a Proteção de [Identidade ativa do Diretivo Ativo Do Azure](../identity-protection/overview-identity-protection.md).
- **Tor Browser** - O [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) foi concebido para o ajudar a preservar a sua privacidade online. A Proteção de Identidade deteta um sessão a partir de um Tor Browser como sign-ins de endereços IP anónimos, que têm um nível de risco médio. Para mais informações, consulte deteções de risco do [Diretório Ativo Azure.](../reports-monitoring/concept-risk-events.md)  
- Uma conta de **teste chamada Alain Charon** - Se não sabe como criar uma conta de teste, consulte [Adicionar utilizadores baseados na nuvem](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Teste o seu sign-in

O objetivo deste passo é garantir que a sua conta de teste pode aceder ao seu inquilino usando o Tor Browser.

**Para testar o seu sign-in:**

1. Inscreva-se no seu [portal Azure](https://portal.azure.com) como **Alain Charon.**
1. Termine a sessão.

## <a name="create-your-conditional-access-policy"></a>Crie a sua política de acesso condicional

O cenário neste quickstart utiliza um sessão de sessão a partir de um Tor Browser para gerar um **Sign-ins** detetado a partir de endereços IP anónimos de deteção de risco. O nível de risco desta deteção de risco é médio. Para responder a esta deteção de risco, define a condição de risco de início de sessão para médio. Em ambiente de produção, deve definir a condição de risco de inscrição, seja para alta ou para média e alta.

Esta secção mostra como criar a política de acesso condicional necessária. Na sua política, estabeleça:

| Definição | Valor |
| --- | --- |
| Utilizadores e grupos | Alain Charon  |
| Aplicativos em nuvem | Todas as aplicações em nuvem |
| Risco de inscrição | Médio |
| Concessão | Bloquear acesso |

![Criar política](./media/app-sign-in-risk/130.png)

**Para configurar a sua política de Acesso Condicional:**

1. Inscreva-se no seu [portal Azure](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. No portal Azure, no navbar esquerdo, clique no **Diretório Ativo Azure**.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. Na página do **Diretório Ativo Azure,** na secção **Segurança,** clique em **Acesso Condicional**.

   ![Acesso Condicional](./media/app-sign-in-risk/03.png)

1. Na página **de Acesso Condicional,** na barra de ferramentas por cima, clique em **Adicionar**.

   ![Nome](./media/app-sign-in-risk/108.png)

1. Na página **Nova,** na caixa de texto **Name,** digite **o acesso do bloco para o nível de risco médio**.

   ![Nome](./media/app-sign-in-risk/104.png)

1. Na secção **Atribuição,** clique em **Utilizadores e grupos**.

   ![Utilizadores e grupos](./media/app-sign-in-risk/06.png)

1. Na página **utilizadores e grupos:**

   ![Acesso Condicional](./media/app-sign-in-risk/107.png)

   1. Clique em **Selecionar utilizadores e grupos**e, em seguida, selecione **Utilizadores e grupos**.
   1. Clique em **Selecionar**.
   1. Na página **Select,** selecione **Alain Charon**e, em seguida, clique em **Selecionar**.
   1. Na página **Utilizadores e grupos,** clique **em Done**.
1. Clique em **aplicativos Cloud**.

   ![Aplicativos em nuvem](./media/app-sign-in-risk/08.png)

1. Na página de **aplicações cloud:**

   ![Acesso Condicional](./media/app-sign-in-risk/109.png)

   1. Clique em **todas as aplicações em nuvem**.
   1. Clique em **Concluído**.
1. Clique **em Condições**.

   ![Controlos de acesso](./media/app-sign-in-risk/19.png)

1. Na página **Condições:**

   ![Nível de risco de início de sessão](./media/app-sign-in-risk/21.png)

   1. Clique **no risco de iniciar sessão**.
   1. Como **Configurar,** clique **sim.**
   1. Como nível de risco de inscrição, selecione **Médio**.
   1. Clique em **Selecionar**.
   1. Na página **Condições,** clique **em Done**.
1. Na secção **de controlos de acesso,** clique em **Grant**.

   ![Controlos de acesso](./media/app-sign-in-risk/10.png)

1. Na **página** Grant:

   ![Acesso Condicional](./media/app-sign-in-risk/105.png)

   1. Selecione **acesso ao bloco**.
   1. Clique em **Selecionar**.
1. Na secção **política Ativação,** clique **em .**

   ![Ativar política](./media/app-sign-in-risk/18.png)

1. Clique em **Criar**.

## <a name="evaluate-a-simulated-sign-in"></a>Avaliar um sessão de inscrição simulada

Agora que configuraste a tua política de Acesso Condicional, provavelmente queres saber se funciona como esperado. Como primeiro passo, utilize o Acesso Condicional e se a **ferramenta política** simular um sessão do utilizador do teste. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação.  

Quando executar o **instrumento de política** para este cenário, o acesso do Bloco para o **nível de risco médio** deve ser listado no âmbito de Políticas que se **aplicarão**.

![Utilizador](./media/app-sign-in-risk/117.png)

**Para avaliar a sua política de Acesso Condicional:**

1. Na página [Acesso Condicional - Políticas,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) no menu em cima, clique no **What If**.  

   ![Hipótese](./media/app-sign-in-risk/14.png)

1. Clique em **Utilizador,** **selecione Alan Charon** na página **Utilizadores** e, em seguida, clique em **Selecionar**.

   ![Utilizador](./media/app-sign-in-risk/116.png)

1. Como **risco de iniciar sessão,** selecione **Médio**.

   ![Utilizador](./media/app-sign-in-risk/119.png)

1. Clique **no que se**.

## <a name="test-your-conditional-access-policy"></a>Teste a sua política de acesso condicional

Na secção anterior, aprendeu a avaliar um sinal de inscrição simulado. Além de uma simulação, deve também testar a sua política de Acesso Condicional para garantir que funciona como esperado.

Para testar a sua política, tente iniciar sessão no seu [portal Azure](https://portal.azure.com) como **Alan Charon** usando o Tor Browser. A sua tentativa de inscrição deve ser bloqueada pela sua política de acesso condicional.

![Multi-Factor Authentication](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o utilizador do teste, o Tor Browser e a política de Acesso Condicional:

- Se não souber apagar um utilizador da AD Azure, consulte [Apagar utilizadores do Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Para eliminar a sua política, selecione a sua política e, em seguida, clique em **Eliminar** na barra de ferramentas de acesso rápido.

   ![Multi-Factor Authentication](./media/app-sign-in-risk/33.png)

- Para obter instruções para remover o Navegador Tor, consulte [Desinstalar](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Exigir termos de uso para ser aceite](require-tou.md)
> [Exigir MFA para aplicações específicas](app-based-mfa.md)
