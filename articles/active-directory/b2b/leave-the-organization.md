---
title: Deixe uma organização como utilizador convidado - Azure Ative Directory
description: Mostra como um utilizador convidado Azure AD B2B pode deixar uma organização utilizando o Painel de Acesso.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bce67c81b924d768826402b707c41c085b7767b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74272503"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Deixe uma organização como utilizador convidado

Um utilizador convidado do Azure Ative Directory (Azure AD) B2B pode decidir deixar uma organização a qualquer momento se já não precisar de usar apps dessa organização ou manter qualquer associação. Um utilizador pode deixar uma organização por conta própria, sem ter de contactar um administrador.

> [!NOTE]
> Um utilizador hóspede não pode sair de uma organização se a sua conta for desativada no inquilino da casa ou no inquilino de recursos. Se a sua conta estiver desativada, o utilizador convidado terá de contactar o administrador do arrendatário, que pode eliminar a conta de hóspedes ou ativar a conta de hóspedes para que o utilizador possa sair da organização.

## <a name="leave-an-organization"></a>Sair de uma organização

Para deixar uma organização, siga estes passos.

1. Aceda à página de Perfil do Painel de Acesso fazendo um dos seguintes passos:
   
   - No [portal Azure,](https://portal.azure.com)clique no seu nome na parte superior direita e selecione **Ver conta**.
   - Abra o painel de [acesso,](https://myapps.microsoft.com)clique no seu nome na direita superior e ao lado das **Organizações,** selecione o ícone de definições (engrenagem).
 
   ![Screenshot mostrando as definições do utilizador no Painel de Acesso](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Se ainda não se inscreveu na organização que pretende deixar, ao abrigo das **Organizações,** clique **no Signin para deixar** o link da organização ao lado do nome da organização. Depois de ter assinado, clique novamente no seu nome na parte superior direita e ao lado das **Organizações,** selecione o ícone de definições (engrenagem).

3. No âmbito das **Organizações,** encontre a organização que pretende deixar e selecione **organização Leave**.

   ![Screenshot mostrando a opção de organização Leave na interface do utilizador](media/leave-the-organization/LeaveOrg.png)

4. Quando lhe for pedido que confirmasse, selecione **Leave**. 

## <a name="account-removal"></a>Remoção de conta

Quando um utilizador sai de uma organização, a conta de utilizador é "suavemente eliminada" no diretório. Por predefinição, o objeto de utilizador desloca-se para a área de **utilizadores Eliminados** em AD Azure, mas não é permanentemente eliminado durante 30 dias. Esta eliminação suave permite ao administrador restaurar a conta de utilizador (incluindo grupos e permissões), se o utilizador fizer um pedido para restaurar a conta dentro do período de 30 dias.

Se desejar, um administrador de inquilino pode apagar permanentemente a conta a qualquer momento durante o período de 30 dias. Para efetuar este procedimento:

1. No [portal Azure,](https://portal.azure.com)selecione **Azure Ative Directory**.
2. Em **Gerir**, selecione **Utilizadores**.
3. Selecione **utilizadores Eliminados**.
4. Selecione a caixa de verificação ao lado de um utilizador eliminado e, em seguida, **selecione Apagar permanentemente**.

Se eliminar permanentemente um utilizador, esta ação é irrevogável.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral do Azure AD B2B, veja o que é a [colaboração Azure AD B2B?](what-is-b2b.md)



