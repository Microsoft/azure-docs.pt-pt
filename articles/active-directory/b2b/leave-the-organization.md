---
title: Deixar uma organização como um usuário convidado-Azure Active Directory
description: Mostra como um usuário convidado do Azure AD B2B pode sair de uma organização usando o painel de acesso.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272503"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Deixar uma organização como um usuário convidado

Um usuário convidado do B2B do Azure Active Directory (Azure AD) pode decidir deixar uma organização a qualquer momento se não precisar mais usar aplicativos dessa organização ou manter qualquer associação. Um usuário pode deixar uma organização por conta própria, sem precisar entrar em contato com um administrador.

> [!NOTE]
> Um usuário convidado não poderá deixar uma organização se sua conta estiver desabilitada no locatário inicial ou no locatário do recurso. Se sua conta estiver desabilitada, o usuário convidado precisará entrar em contato com o administrador de locatários, que pode excluir a conta de convidado ou habilitar a conta de convidado para que o usuário possa sair da organização.

## <a name="leave-an-organization"></a>Sair de uma organização

Para sair de uma organização, siga estas etapas.

1. Vá para a página de perfil do painel de acesso seguindo uma das seguintes etapas:
   
   - Na [portal do Azure](https://portal.azure.com), clique em seu nome no canto superior direito e selecione **Exibir conta**.
   - Abra o [painel de acesso](https://myapps.microsoft.com), clique em seu nome no canto superior direito e, ao lado de **organizações**, selecione o ícone de configurações (engrenagem).
 
   ![Captura de tela mostrando as configurações do usuário no painel de acesso](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Se você ainda não tiver entrado na organização que deseja deixar, em **organizações**, clique no link **entrar para sair da organização** ao lado do nome da organização. Depois de entrar, clique em seu nome novamente no canto superior direito e, ao lado de **organizações**, selecione o ícone de configurações (engrenagem).

3. Em **organizações**, localize a organização que você deseja deixar e selecione sair da **organização**.

   ![Captura de tela mostrando a opção sair da organização na interface do usuário](media/leave-the-organization/LeaveOrg.png)

4. Quando solicitado a confirmar, selecione **sair**. 

## <a name="account-removal"></a>Remoção de conta

Quando um usuário deixa uma organização, a conta de usuário é "com exclusão reversível" no diretório. Por padrão, o objeto de usuário é movido para a área **usuários excluídos** no Azure AD, mas não é excluído permanentemente por 30 dias. Essa exclusão flexível permite que o administrador restaure a conta de usuário (incluindo grupos e permissões), se o usuário fizer uma solicitação para restaurar a conta dentro do período de 30 dias.

Se desejado, um administrador de locatários pode excluir permanentemente a conta a qualquer momento durante o período de 30 dias. Para efetuar este procedimento:

1. Na [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.
2. Em **Gerir**, selecione **Utilizadores**.
3. Selecione **usuários excluídos**.
4. Marque a caixa de seleção ao lado de um usuário excluído e, em seguida, selecione **excluir permanentemente**.

Se você excluir permanentemente um usuário, essa ação será irrevogável.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral do Azure AD B2B, consulte [o que é a colaboração B2B do Azure ad?](what-is-b2b.md)



