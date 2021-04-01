---
title: Deixe uma organização como utilizador convidado - Azure Ative Directory
description: Mostra como um utilizador convidado Azure AD B2B pode deixar uma organização utilizando o Painel de Acesso.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc3ef7d168c17ec10fe64925adbda7044a2a4c82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87909315"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Deixar uma organização como utilizador convidado

Um utilizador convidado do Azure Ative Directory (Azure AD) B2B pode decidir deixar uma organização a qualquer momento se já não precisar de usar aplicações dessa organização ou manter qualquer associação. Um utilizador pode deixar uma organização por conta própria, sem ter de contactar um administrador.

> [!NOTE]
> Um utilizador convidado não pode sair de uma organização se a sua conta for desativada no arrendatário ou no inquilino de recursos. Se a sua conta estiver desativada, o utilizador convidado terá de contactar o administrador do arrendatário, que pode eliminar a conta do hóspede ou ativar a conta de hóspedes para que o utilizador possa sair da organização.

## <a name="leave-an-organization"></a>Sair de uma organização

Para deixar uma organização, siga estes passos.

1. Aceda à sua página de Perfil do Painel de Acesso, fazendo um dos seguintes passos:
   
   - No [portal Azure,](https://portal.azure.com)clique no seu nome na parte superior direita e selecione **Ver conta**.
   - Abra o painel [de acesso,](https://myapps.microsoft.com)clique no seu nome na parte superior direita e, ao lado de **Organizações,** selecione o ícone de definições (engrenagem).
 
   ![Screenshot mostrando as definições do utilizador no Painel de Acesso](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Se ainda não se inscreveu na organização que pretende deixar, ao abrigo das **Organizações,** clique **no Sign in para deixar** o link da organização ao lado do nome da organização. Depois de iniciar sposição, clique novamente no seu nome na parte superior direita e ao lado das **Organizações**, selecione o ícone de definições (engrenagem).

3. Sob **organizações,** encontre a organização que pretende deixar, e selecione **Leave organization**.

   ![Screenshot mostrando a opção de organização Leave na interface do utilizador](media/leave-the-organization/LeaveOrg.png)

4. Quando solicitado para confirmar, selecione **Leave**. 

## <a name="account-removal"></a>Remoção de conta

Quando um utilizador sai de uma organização, a conta de utilizador é "apagada suavemente" no diretório. Por predefinição, o objeto do utilizador desloca-se para a área de **utilizadores eliminados** em Azure AD, mas não é permanentemente eliminado durante 30 dias. Esta eliminação suave permite ao administrador restaurar a conta do utilizador (incluindo grupos e permissões), se o utilizador fizer um pedido para restaurar a conta dentro do período de 30 dias.

Se desejar, um administrador inquilino pode apagar permanentemente a conta a qualquer momento durante o período de 30 dias. Para efetuar este procedimento:

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.
2. Em **Gerir**, selecione **Utilizadores**.
3. **Selecione utilizadores eliminados**.
4. Selecione a caixa de verificação ao lado de um utilizador eliminado e, em seguida, **selecione Delete permanentemente**.

Se eliminar permanentemente um utilizador, esta ação é irrevogável.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral do Azure AD B2B, veja [o que é a colaboração Azure AD B2B?](what-is-b2b.md)



