---
title: Início rápido – bloquear o acesso quando um risco de sessão for detectado com Azure Active Directory Identity Protection | Microsoft Docs
description: Neste guia de início rápido, você aprende a configurar uma política de acesso condicional de risco de entrada do Azure Active Directory (Azure AD) Identity Protection para bloquear entradas com base em riscos de sessão.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: quickstart
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed5e5e26a27e13ba09ffcc97e0b2b0f1b37bc8bd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127704"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Início rápido: Bloquear o acesso quando um risco de sessão for detectado com Azure Active Directory Identity Protection  

Para manter seu ambiente protegido, convém bloquear a entrada de usuários suspeitos. A proteção de identidade do Azure Active Directory (AD do Azure) analisa cada entrada e calcula a probabilidade de uma tentativa de entrada não ter sido executada pelo proprietário legítimo de uma conta de usuário. A probabilidade (baixa, média, alta) é indicada na forma de um valor calculado chamado nível de risco de entrada. Ao definir a condição de risco de entrada, você pode configurar uma política de acesso condicional de risco de entrada para responder a níveis de risco de entrada específicos. 

Este guia de início rápido mostra como configurar uma política de acesso condicional de risco de entrada que bloqueia uma entrada quando um nível de risco de entrada médio e superior for detectado. 

![Criar política](./media/quickstart-sign-in-risk-policy/1004.png)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos 

Para concluir o cenário deste tutorial, precisa de:

- **Acesso a uma edição Azure ad Premium P2** – Azure ad Identity Protection é um recurso Azure ad Premium P2. 
- **Proteção de identidade** -o cenário neste guia de início rápido requer que o Identity Protection esteja habilitado. Se você não souber como habilitar a proteção de identidade, consulte [habilitando Azure Active Directory Identity Protection](../identity-protection/enable.md).
- **Tor browser** – o [navegador do Tor](https://www.torproject.org/projects/torbrowser.html.en) foi projetado para ajudá-lo a preservar sua privacidade online. A proteção de identidade detecta uma entrada de um navegador Tor como **entradas de endereços IP anônimos**, que tem um nível de risco médio. Para obter mais informações, consulte [Azure Active Directory as detecções de risco](../reports-monitoring/concept-risk-events.md).  
- **Uma conta de teste chamada Alain Charon** -se você não souber como criar uma conta de teste, consulte [Adicionar um novo usuário](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testar sua entrada 

O objetivo desta etapa é garantir que sua conta de teste possa acessar seu locatário usando o navegador Tor.

**Para testar sua entrada:**

1. Entre em seu [portal do Azure](https://portal.azure.com) como **Alain Charon**.
2. Termine a sessão. 

## <a name="create-your-conditional-access-policy"></a>Criar sua política de acesso condicional 

O cenário neste guia de início rápido usa uma entrada de um navegador Tor para gerar entradas detectadas **de detecção de risco de endereços IP anônimos** . O nível de risco dessa detecção de risco é médio. Para responder a essa detecção de risco, defina a condição de risco de entrada como médio. 

Esta seção mostra como criar a política de acesso condicional de risco de entrada necessária. Em sua política, defina:

|Definição |Value|
|---     | --- |
| Utilizadores  | Alain Charon  |
| Condições | Risco de entrada, médio e acima |
| Controlos | Bloquear acesso |

![Criar política](./media/quickstart-sign-in-risk-policy/201.png)

**Para configurar sua política de acesso condicional:**

1. Entre em seu [portal do Azure](https://portal.azure.com) como administrador global.
2. Vá para a [página de Azure ad Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
3. Na página **Azure ad Identity Protection** , na seção **Configurar** , clique em **política de risco de entrada**.
4. Na página política, na seção **atribuições** , clique em **usuários**.
5. Na página **usuários** , clique em **Selecionar usuários**.
6. Na página **Selecionar usuários** , selecione **Alain Charon**e clique em **selecionar**.
7. Na página **usuários** , clique em **concluído**. 
8. Na página política, na seção **atribuições** , clique em **condições**.
9. Na página **condições** , clique em **risco de entrada**.
10. Na página **risco de entrada** , selecione **médio e superior**e, em seguida, clique em **selecionar**. 
11. Na página **condições** , clique em **concluído**.
12. Na página política, na seção **controles** , clique em **acesso**.
13. Na página **acesso** , clique em **permitir acesso**, selecione **exigir autenticação**multifator e, em seguida, clique em **selecionar**.
14. Na página política, clique em **salvar**.  

## <a name="test-your-conditional-access-policy"></a>Testar sua política de acesso condicional

Para testar sua política, tente entrar no seu [portal do Azure](https://portal.azure.com) como **Alan Charon** usando o navegador Tor. Sua tentativa de entrada deve ser bloqueada pela política de acesso condicional.

![Multi-Factor Authentication](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o usuário de teste, o navegador Tor e desabilite a política de acesso condicional de risco de entrada:

- Se você não souber como excluir um usuário do Azure AD, consulte [como adicionar ou excluir usuários](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Para obter instruções para remover o navegador Tor, [](https://tb-manual.torproject.org/uninstalling/)consulte Desinstalando.
