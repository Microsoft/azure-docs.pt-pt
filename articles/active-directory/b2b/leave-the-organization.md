---
title: Sair de uma organização como um utilizador convidado - Azure Active Directory | Documentos da Microsoft
description: Mostra como um utilizador de convidado do Azure AD B2B pode deixar uma organização utilizando o painel de acesso.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75f91c037a2f05c999d388ce7bb16ad2d0c9cd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58005664"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Sair de uma organização como um utilizador convidado

Um utilizador de convidado do Azure Active Directory (Azure AD) B2B pode optar por deixar uma organização em qualquer altura, se já não precisar de utilizar aplicações dessa organização ou manter qualquer associação. Um utilizador pode deixar uma organização por conta própria, sem ter de contactar um administrador.

## <a name="leave-an-organization"></a>Sair de uma organização

Para deixar uma organização, siga estes passos.

1. Aceda à sua página de perfil do painel de acesso através de uma das seguintes ações:
   
   - Na [portal do Azure](https://portal.azure.com), clique em seu nome no canto superior direito e selecione **ver conta**.
   - Abra sua [painel de acesso](https://myapps.microsoft.com), clique em seu nome no canto superior direito e seguinte para **organizações**, selecione o ícone de definições (engrenagem).
 
   ![Captura de ecrã que mostra as definições de utilizador no painel de acesso](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Se ainda não tiver iniciado sessão para a organização pretende sair, em **organizações**, clique nas **iniciar sessão para sair da organização** link junto ao nome da organização. Depois de iniciar sessão, clique no nome novamente no canto superior direito e, junto a **organizações**, selecione o ícone de definições (engrenagem).

3. Sob **organizações**, localizar a organização que pretende sair e selecione **deixar a organização**.

   ![Captura de ecrã que mostra a opção de organização deixe na interface do usuário](media/leave-the-organization/LeaveOrg.png)

4. Quando lhe for pedido para confirmar, selecione **deixe**. 

## <a name="account-removal"></a>Remoção da conta

Quando um utilizador sai de uma organização, a conta de utilizador é "soft"eliminada no diretório. Por predefinição, o objeto de usuário se move para o **utilizadores eliminados** área no Azure AD, mas não é permanentemente eliminado durante 30 dias. Esta eliminação de forma recuperável permite que o administrador restaurar a conta de utilizador (incluindo os grupos e permissões), se o usuário faz uma solicitação para restaurar a conta dentro do período de 30 dias.

Se assim o desejar, um administrador inquilino pode eliminar permanentemente a conta em qualquer altura durante o período de 30 dias. Para efetuar este procedimento:

1. Na [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.
2. Em **Gerir**, selecione **Utilizadores**.
3. Selecione **utilizadores eliminados**.
4. Selecione a caixa de verificação junto a um utilizador eliminado e, em seguida, selecione **eliminar permanentemente**.

Se eliminar permanentemente um utilizador, esta ação é irrevogável.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral do Azure AD B2B, consulte [o que é a colaboração B2B do Azure AD?](what-is-b2b.md)



