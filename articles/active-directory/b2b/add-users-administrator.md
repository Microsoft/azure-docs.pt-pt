---
title: Adicionar utilizadores de colaboração B2B no portal do Azure - Azure Active Directory | Documentos da Microsoft
description: Mostra como um administrador pode adicionar utilizadores convidados para o seu diretório de uma organização de parceiro através da colaboração B2B do Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: mimart
author: msmimart
manager: celested
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef0a4e4a05427b1ed83b017b7a49862596aaf50a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799410"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Adicionar utilizadores de colaboração do Azure Active Directory B2B no portal do Azure

Como um utilizador a quem é atribuído a qualquer uma das funções de diretório administrador limitado, pode utilizar o portal do Azure para convidar utilizadores de colaboração B2B. Pode convidar utilizadores convidados para o diretório, a um grupo ou a uma aplicação. Depois de convidar um utilizador através de qualquer um destes métodos, conta do utilizador convidado é adicionada ao Azure Active Directory (Azure AD), com um tipo de utilizador de *convidado*. O utilizador convidado, em seguida, têm de resgatar o convite para aceder aos recursos.

Depois de adicionar um utilizador convidado ao diretório, pode optar por enviar o utilizador convidado uma ligação direta para uma aplicação partilhada ou o utilizador convidado pode clique no URL de resgate no e-mail de convite. Para obter mais informações sobre o processo de resgate, consulte [resgate de convite de colaboração do B2B](redemption-experience.md).

> [!IMPORTANT]
> Deve seguir os passos em [procedimentos: Adicionar informações de privacidade da sua organização no Azure Active Directory](https://aka.ms/adprivacystatement) para adicionar o URL da declaração de privacidade da sua organização. Como parte do processo de resgate de convite de tempo primeiro, um utilizador convidado tem de dar consentimento para os seus termos de privacidade para continuar. 

## <a name="add-guest-users-to-the-directory"></a>Adicionar utilizadores convidados para o diretório

Para adicionar utilizadores de colaboração B2B ao diretório, siga estes passos:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como um utilizador que seja atribuído qualquer do administrador limitado de funções de diretório.
2. No painel de navegação, selecione **do Azure Active Directory**.
3. Em **Gerir**, selecione **Utilizadores**.
4. Selecione **Novo utilizador convidado**.

   ![Mostra onde o novo utilizador convidado está na interface do Usuário](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
   > [!NOTE]
   > O **novo utilizador convidado** opção também está disponível na **organizacionais relações** página. Na **do Azure Active Directory**, em **gerir**, selecione **relações organizacionais**.

5. Em **Nome de utilizador**, introduza o endereço de e-mail do utilizador externo. Também tem a opção de incluir uma mensagem de boas-vindas. Por exemplo:

   ![Mostra onde o novo utilizador convidado está na interface do Usuário](./media/add-users-administrator/InviteGuest.png) 

    > [!NOTE]
    > Não são suportados endereços de e-mail de grupo, Introduza o endereço de e-mail para um indivíduo. Além disso, alguns fornecedores de e-mail permitem aos utilizadores adicionar um plus símbolo (+) e texto adicional para os respetivos endereços de e-mail para o ajudar com coisas como a filtragem de caixa de entrada. No entanto, o Azure AD atualmente não suporta mais símbolos endereços de e-mail. Para evitar problemas de entrega, omita o símbolo de adição e quaisquer carateres até a seguir o símbolo @.

6. Selecione **Convidar** para enviar automaticamente o convite ao utilizador convidado. 
 
Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.


![Mostra o utilizador B2B com o tipo de utilizador convidado](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Adicionar utilizadores convidados a um grupo
Se precisar de adicionar manualmente utilizadores de colaboração B2B para um grupo como um administrador do Azure AD, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. No painel de navegação, selecione **do Azure Active Directory**.
3. Sob **Manage**, selecione **grupos**.
4. Selecione um grupo (ou clique em **novo grupo** para criar um novo). É uma boa idéia para incluir na descrição do grupo que o grupo contém utilizadores convidados B2B.
5. Selecione **membros**. 
6. Efetue uma das seguintes ações:
   - Se o utilizador convidado já existe no diretório, procure o utilizador B2B. Selecione o utilizador e, em seguida, clique em **selecione** para adicionar o utilizador ao grupo.
   - Se o utilizador convidado ainda não existir no diretório, convidá-los ao grupo, escrevendo o respetivo endereço de e-mail na caixa de pesquisa, escrevendo uma mensagem pessoal opcional e, em seguida, clicar em **selecione**. O convite sai automaticamente para o utilizador convidado.
     
     ![Adicionar botão de convite para adicionar membros convidados](./media/add-users-administrator/GroupInvite.png)
   
Também pode usar grupos dinâmicos com a colaboração B2B do Azure AD. Para obter mais informações, consulte [grupos dinâmicos e de colaboração do Azure Active Directory B2B](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Adicionar utilizadores convidados a uma aplicação

Para adicionar utilizadores de colaboração B2B a uma aplicação como administrador do Azure AD, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. No painel de navegação, selecione **do Azure Active Directory**.
3. Sob **Manage**, selecione **aplicações empresariais** > **todos os aplicativos**.
4. Selecione a aplicação à qual pretende adicionar utilizadores convidados.
5. No dashboard da aplicação, selecione **Total de utilizadores** para abrir o **utilizadores e grupos** painel.

    ![Total de botão de utilizadores para adicionar abra utilizadores e grupos](./media/add-users-administrator/AppUsersAndGroups.png)

6. Selecione **adicionar utilizador**.
7. Sob **adicionar atribuição**, selecione **grupos de utilizadores e**.
8. Efetue uma das seguintes ações:
   - Se o utilizador convidado já existe no diretório, procure o utilizador B2B. Selecione o utilizador, clique em **selecionar**e, em seguida, clique em **atribuir** para adicionar o utilizador para a aplicação.
   - Se o utilizador convidado ainda não existir no diretório, em **selecione o membro ou Convide um utilizador externo**, escreva o endereço de e-mail do utilizador. Na caixa de mensagem, escreva uma mensagem pessoal opcional. Na caixa de mensagem, clique em **convidar**.
           
       ![Adicionar botão de convite para adicionar membros convidados](./media/add-users-administrator/AppInviteUsers.png)
   
      Clique em **selecionar**e, em seguida, clique em **atribuir** para adicionar o utilizador para a aplicação. Um convite sai automaticamente para o utilizador convidado.

9. O utilizador convidado é apresentado do aplicativo **utilizadores e grupos** lista com a função atribuída de **predefinido acesso**. Se pretender alterar a função, efetue o seguinte:
   - Selecione o utilizador convidado e, em seguida, selecione **editar**. 
   - Sob **editar atribuição**, clique em **selecionar função**e selecione a função que pretende atribuir ao utilizador selecionado.
   - Clique em **Selecionar**.
   - Clique em **Atribuir**.
 
## <a name="resend-invitations-to-guest-users"></a>Volte a enviar convites para os utilizadores convidados

Se um utilizador convidado não tem ainda a resgatar o convite, pode reenviar o e-mail de convite.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. No painel de navegação, selecione **do Azure Active Directory**.
3. Em **Gerir**, selecione **Utilizadores**.
5. Selecione a conta de utilizador.
6. Sob **Manage**, selecione **perfil**.
7. Se o utilizador ainda não aceitou o convite, um **reenviar convite** opção está disponível. Selecione este botão reenviar.

   ![Reenviar convite opção no perfil do usuário](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> Se volte a enviar um convite que originalmente direcionados ao utilizador para uma aplicação específica, compreenda que o link no novo convite leva o usuário para o painel de acesso de nível superior em vez disso.

## <a name="next-steps"></a>Passos Seguintes

- Para saber como os administradores não pertencente ao Azure AD podem adicionar utilizadores convidados B2B, consulte [como os operadores de informações adicionam utilizadores de colaboração do B2B?](add-users-information-worker.md)
- Para obter informações sobre o e-mail de convite, consulte [os elementos da mensagem de e-mail de convite de colaboração B2B](invitation-email-elements.md).

