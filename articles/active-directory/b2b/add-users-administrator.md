---
title: Adicionar usuários de colaboração B2B no portal do Azure-Azure AD
description: Mostra como um administrador pode adicionar usuários convidados ao diretório de uma organização parceira usando a colaboração B2B do Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf2ca3bde0cfee97adee8bb875df1522e95b1f53
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74869320"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure

Como um usuário que é atribuído a qualquer uma das funções de diretório de administrador limitadas, você pode usar o portal do Azure para convidar usuários de colaboração B2B. Você pode convidar usuários convidados para o diretório, para um grupo ou para um aplicativo. Depois de convidar um usuário por meio de qualquer um desses métodos, a conta do usuário convidado é adicionada ao Azure Active Directory (Azure AD), com um tipo de usuário *convidado*. O usuário convidado deve então resgatar seu convite para acessar recursos. Um convite de um usuário não expira.

Depois de adicionar um usuário convidado ao diretório, você pode enviar ao usuário convidado um link direto para um aplicativo compartilhado ou o usuário convidado pode clicar na URL de resgate no email de convite. Para obter mais informações sobre o processo de resgate, consulte [resgate de convite de colaboração B2B](redemption-experience.md).

> [!IMPORTANT]
> Você deve seguir as etapas em [como: adicionar as informações de privacidade da sua organização no Azure Active Directory](https://aka.ms/adprivacystatement) para adicionar a URL da política de privacidade da sua organização. Como parte do processo de resgate de convite pela primeira vez, um usuário convidado deve consentir seus termos de privacidade para continuar. 

## <a name="before-you-begin"></a>Antes de começar

Verifique se as configurações de colaboração externa da sua organização estão configuradas de forma que você tenha permissão para convidar convidados. Por padrão, todos os usuários e administradores podem convidar convidados. Mas as políticas de colaboração externa de sua organização podem ser configuradas para impedir que determinados tipos de usuários ou administradores convidam convidados. Para saber como exibir e definir essas políticas, consulte [habilitar colaboração externa B2B e gerenciar quem pode convidar convidados](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Adicionar usuários convidados ao diretório

Para adicionar usuários de colaboração B2B ao diretório, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) como um usuário que é atribuído a uma função de diretório de administrador limitada ou a função emissor de convite de convidado.
2. Pesquise e selecione **Azure Active Directory** em qualquer página.
3. Em **Gerir**, selecione **Utilizadores**.
4. Selecione **Novo utilizador convidado**.

   ![Mostra onde um novo usuário convidado está na interface do usuário](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
   > [!NOTE]
   > A opção **novo usuário convidado** também está disponível na página **relações organizacionais** . Em **Azure Active Directory**, em **gerenciar**, selecione **relações organizacionais**.

5. Na página **novo usuário** , selecione **convidar usuário** e, em seguida, adicione as informações do usuário convidado. 

    > [!NOTE]
    > Não há suporte para endereços de email de grupo; Insira o endereço de email de um indivíduo. Além disso, alguns provedores de email permitem que os usuários adicionem um símbolo de mais (+) e texto adicional aos seus endereços de email para ajudar com coisas como filtragem de caixa de entrada. No entanto, o Azure AD atualmente não dá suporte a símbolos de mais em endereços de email. Para evitar problemas de entrega, omita o símbolo de adição e todos os caracteres que o seguem até o símbolo @.

   - **Nomes.** O nome e o sobrenome do usuário convidado.
   - **Endereço de email (obrigatório)** . O endereço de email do usuário convidado.
   - **Mensagem pessoal (opcional)** Inclua uma mensagem de boas-vindas pessoal para o usuário convidado.
   - **Grupos**: você pode adicionar o usuário convidado a um ou mais grupos existentes ou pode fazer isso mais tarde.
   - **Função de diretório**: se você precisar de permissões administrativas do Azure ad para o usuário, poderá adicioná-las a uma função do Azure AD. 

7. Selecione **Convidar** para enviar automaticamente o convite ao utilizador convidado. 
 
Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.


![Mostra o usuário B2B com o tipo de usuário convidado](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Adicionar utilizadores convidados a um grupo
Se você precisar adicionar manualmente usuários de colaboração B2B a um grupo, siga estas etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Pesquise e selecione **Azure Active Directory** em qualquer página.
3. Em **gerenciar**, selecione **grupos**.
4. Selecione um grupo (ou clique em **novo grupo** para criar um novo). É uma boa ideia incluir na descrição do grupo que o grupo contém usuários convidados B2B.
5. Selecione **Membros**. 
6. Efetue uma das seguintes ações:
   - Se o usuário convidado já existir no diretório, procure o usuário B2B. Selecione o usuário e clique em **selecionar** para adicionar o usuário ao grupo.
   - Se o usuário convidado ainda não existir no diretório, convide-o para o grupo digitando seu endereço de email na caixa de pesquisa, digitando uma mensagem pessoal opcional e, em seguida, clicando em **selecionar**. O convite sai automaticamente para o usuário convidado.
     
     ![Adicionar botão convidar para adicionar membros convidados](./media/add-users-administrator/GroupInvite.png)
   
Você também pode usar grupos dinâmicos com a colaboração B2B do Azure AD. Para obter mais informações, consulte [grupos dinâmicos e Azure Active Directory colaboração B2B](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Adicionar utilizadores convidados a uma aplicação

Para adicionar usuários de colaboração B2B a um aplicativo, siga estas etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Pesquise e selecione **Azure Active Directory** em qualquer página.
3. Em **gerenciar**, selecione **aplicativos empresariais** > **todos os aplicativos**.
4. Selecione o aplicativo ao qual você deseja adicionar usuários convidados.
5. No painel do aplicativo, selecione **total de usuários** para abrir o painel **usuários e grupos** .

    ![Botão total de usuários para adicionar usuários e grupos abertos](./media/add-users-administrator/AppUsersAndGroups.png)

6. Selecione **Adicionar utilizador**.
7. Em **Adicionar atribuição**, selecione **usuário e grupos**.
8. Efetue uma das seguintes ações:
   - Se o usuário convidado já existir no diretório, procure o usuário B2B. Selecione o usuário, clique em **selecionar**e, em seguida, clique em **atribuir** para adicionar o usuário ao aplicativo.
   - Se o usuário convidado ainda não existir no diretório, em **selecionar membro ou convidar um usuário externo**, digite o endereço de email do usuário. Na caixa de mensagem, digite uma mensagem pessoal opcional. Na caixa de mensagem, clique em **convidar**.
           
       ![Adicionar botão convidar para adicionar membros convidados](./media/add-users-administrator/AppInviteUsers.png)
   
      Clique em **selecionar**e, em seguida, clique em **atribuir** para adicionar o usuário ao aplicativo. Um convite sai automaticamente para o usuário convidado.

9. O usuário convidado aparece na lista **usuários e grupos** do aplicativo com a função atribuída de **acesso padrão**. Se você quiser alterar a função, faça o seguinte:
   - Selecione o usuário convidado e, em seguida, selecione **Editar**. 
   - Em **Editar atribuição**, clique em **selecionar função**e selecione a função que você deseja atribuir ao usuário selecionado.
   - Clique em **Selecionar**.
   - Clique em **Atribuir**.
 
## <a name="resend-invitations-to-guest-users"></a>Reenviar convites a utilizadores convidados

Se um usuário convidado ainda não tiver resgatado seu convite, você poderá reenviar o email de convite.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Pesquise e selecione **Azure Active Directory** em qualquer página.
3. Em **Gerir**, selecione **Utilizadores**.
5. Selecione a conta de usuário.
6. Em **gerenciar**, selecione **perfil**.
7. Se o usuário ainda não tiver aceitado o convite, uma opção **reenviar convite** estará disponível. Selecione este botão para reenviar.

   ![Reenviar a opção de convite no perfil do usuário](./media/add-users-administrator/b2b-user-resend-invitation.png)

> [!NOTE]
> Se você reenviar um convite que originalmente direcionou o usuário para um aplicativo específico, entenda que o link no novo convite leva o usuário para o painel de acesso de nível superior.

## <a name="next-steps"></a>Passos seguintes

- Para saber como os administradores não Azure AD podem adicionar usuários convidados B2B, consulte [como os operadores de informações adicionam usuários de colaboração B2B?](add-users-information-worker.md)
- Para obter informações sobre o email de convite, consulte [os elementos do email de convite de colaboração B2B](invitation-email-elements.md).

