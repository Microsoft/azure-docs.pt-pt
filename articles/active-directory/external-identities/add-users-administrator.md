---
title: Adicionar utilizadores de colaboração B2B no portal Azure - Azure AD
description: Mostra como um administrador pode adicionar utilizadores convidados ao seu diretório a partir de uma organização parceira usando a colaboração B2B do Azure Ative Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: b503c7494d1b5e1821718fab5577bca88be65169
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442291"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Adicionar utilizadores de colaboração Azure Ative Directory B2B no portal Azure

Como utilizador a quem é atribuída qualquer uma das funções limitadas de diretório de administrador, pode utilizar o portal Azure para convidar utilizadores de colaboração B2B. Você pode convidar utilizadores convidados para o diretório, para um grupo, ou para uma aplicação. Depois de convidar um utilizador através de qualquer um destes métodos, a conta do utilizador convidado é adicionada ao Azure Ative Directory (Azure AD), com um tipo de utilizador de *Guest*. O utilizador convidado deve então resgatar o seu convite para aceder aos recursos. Um convite de um utilizador não expira.

Depois de adicionar um utilizador convidado ao diretório, pode enviar ao utilizador convidado um link direto para uma aplicação partilhada, ou o utilizador convidado pode clicar no URL de resgate no e-mail de convite. Para obter mais informações sobre o processo de resgate, consulte o [pedido de colaboração B2B de resgate](redemption-experience.md).

> [!IMPORTANT]
> Deve seguir os passos em [Como-a: Adicione as informações de privacidade da sua organização no Azure Ative Directory](../fundamentals/active-directory-properties-area.md) para adicionar o URL da declaração de privacidade da sua organização. Como parte do processo de resgate de convites pela primeira vez, um utilizador convidado deve consentir com os seus termos de privacidade para continuar. 

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que as configurações de colaboração externa da sua organização estão configuradas de modo a que possa convidar os hóspedes. Por predefinição, todos os utilizadores e administradores podem convidar os hóspedes. Mas as políticas de colaboração externa da sua organização podem ser configuradas para evitar que certos tipos de utilizadores ou administradores convidem os hóspedes. Para saber como visualizar e definir estas políticas, consulte [a colaboração externa do Enable B2B e gerir quem pode convidar os hóspedes.](delegate-invitations.md)

## <a name="add-guest-users-to-the-directory"></a>Adicione utilizadores convidados ao diretório

Para adicionar utilizadores de colaboração B2B ao diretório, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como um utilizador a quem é atribuída uma função de diretório de administrador limitado ou o papel de Convidado Convidado.
2. Procure e selecione **Azure Ative Directory** a partir de qualquer página.
3. Em **Gerir**, selecione **Utilizadores**.
4. Selecione **Novo utilizador convidado**.

   ![Mostra onde o novo utilizador convidado está na UI](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
5. Na nova página do **utilizador,** selecione **Convidar utilizador** e, em seguida, adicionar as informações do utilizador convidado. 

    > [!NOTE]
    > Os endereços de e-mail do grupo não são suportados; inserir o endereço de e-mail para um indivíduo. Além disso, alguns fornecedores de e-mail permitem que os utilizadores adicionem um símbolo mais (+) e um texto adicional aos seus endereços de e-mail para ajudar em coisas como a filtragem de caixas de entrada. No entanto, a Azure AD não suporta atualmente mais símbolos em endereços de e-mail. Para evitar problemas de entrega, omita o símbolo mais e quaisquer caracteres que o seguem até ao símbolo @.

   - **O nome.** O primeiro e último nome do utilizador convidado.
   - **Endereço de e-mail (obrigatório)**. O endereço de e-mail do utilizador convidado.
   - **Mensagem pessoal (opcional)** Inclua uma mensagem de boas-vindas pessoal ao utilizador convidado.
   - **Grupos**: Pode adicionar o utilizador convidado a um ou mais grupos existentes, ou pode fazê-lo mais tarde.
   - **Função diretório**: Se necessitar de permissões administrativas Azure AD para o utilizador, pode adicioná-las a uma função AZure AD. 

7. Selecione **Convidar** para enviar automaticamente o convite ao utilizador convidado. 
 
Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.


![Mostra utilizador B2B com tipo de utilizador convidado](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Adicionar utilizadores convidados a um grupo
Se precisar de adicionar manualmente os utilizadores de colaboração B2B a um grupo, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Procure e selecione **Azure Ative Directory** a partir de qualquer página.
3. Em **Gestão**, selecione **Grupos**.
4. Selecione um grupo (ou clique em **Novo grupo** para criar um novo). É uma boa ideia incluir na descrição do grupo que o grupo contém utilizadores convidados B2B.
5. Selecione **Membros**. 
6. Faça um dos seguintes:
   - Se o utilizador convidado já existir no diretório, procure o utilizador B2B. Selecione o utilizador e, em seguida, clique **em Selecionar** para adicionar o utilizador ao grupo.
   - Se o utilizador convidado já não existir no diretório, convide-os para o grupo digitando o seu endereço de e-mail na caixa de pesquisa, digitando uma mensagem pessoal opcional e, em seguida, clicando em **Select**. O convite é automaticamente enviado ao utilizador convidado.
     
     ![Adicione botão de convite para adicionar membros convidados](./media/add-users-administrator/GroupInvite.png)
   
Também pode utilizar grupos dinâmicos com colaboração Azure AD B2B. Para mais informações, consulte [os grupos Dinâmicos e a colaboração B2B do Azure Ative Directory](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Adicionar utilizadores convidados a uma aplicação

Para adicionar utilizadores de colaboração B2B a uma aplicação, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Procure e selecione **Azure Ative Directory** a partir de qualquer página.
3. Em **Gestão**, selecione **Aplicações da Empresa**Todas as  >  **aplicações**.
4. Selecione a aplicação à qual pretende adicionar utilizadores convidados.
5. No painel de instrumentos da aplicação, selecione **Utilizadores Totais** para abrir o painel **de Utilizadores e grupos.**

    ![Botão total de utilizadores para adicionar utilizadores e grupos abertos](./media/add-users-administrator/AppUsersAndGroups.png)

6. Selecione **Adicionar utilizador**.
7. Em **'Adicionar Atribuição'**( **Utilizador e grupos).**
8. Faça um dos seguintes:
   - Se o utilizador convidado já existir no diretório, procure o utilizador B2B. Selecione o utilizador, clique em **Selecionar**e, em seguida, clique **em Atribuir** para adicionar o utilizador à aplicação.
   - Se o utilizador convidado já não existir no diretório, no **membro Select ou convidar um utilizador externo,** digite o endereço de e-mail do utilizador. Na caixa de mensagens, escreva uma mensagem pessoal opcional. Debaixo da caixa de mensagens, clique em **Convidar**.
           
       ![Adicione botão de convite para adicionar membros convidados](./media/add-users-administrator/AppInviteUsers.png)
   
      Clique **em Selecionar**e, em seguida, clique em **Atribuir** para adicionar o utilizador à aplicação. Um convite sai automaticamente para o utilizador convidado.

9. O utilizador convidado aparece na lista de **Utilizadores e grupos** da aplicação com o papel atribuído de **Acesso Predefinido**. Se quiser alterar o papel, faça o seguinte:
   - Selecione o utilizador convidado e, em seguida, **selecione Editar**. 
   - Em **Atribuição de Edição**, clique em **'Selecione Role'** e selecione a função que pretende atribuir ao utilizador selecionado.
   - Clique em **Selecionar**.
   - Clique em **Atribuir**.
 
## <a name="resend-invitations-to-guest-users"></a>Reenviar convites a utilizadores convidados

Se um utilizador convidado ainda não tiver resgatado o seu convite, pode reensirar o e-mail do convite.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Procure e selecione **Azure Ative Directory** a partir de qualquer página.
3. Em **Gerir**, selecione **Utilizadores**.
5. Selecione a conta do utilizador.
6. Em **Gestão**, selecione **Profile**.
7. Se o utilizador ainda não aceitou o convite, está disponível uma opção **de convite Resend.** Selecione este botão para reensar.

   ![Reencaiar a opção de convite no perfil do utilizador](./media/add-users-administrator/b2b-user-resend-invitation.png)

> [!NOTE]
> Se reencaminhar um convite que inicialmente direcionou o utilizador para uma aplicação específica, entenda que o link no novo convite leva o utilizador ao Painel de Acesso de nível superior.

## <a name="next-steps"></a>Passos seguintes

- Para saber como os administradores não-AD não-Azure podem adicionar utilizadores convidados B2B, veja [como é que os trabalhadores da informação adicionam utilizadores de colaboração B2B?](add-users-information-worker.md)
- Para obter informações sobre o e-mail de convite, consulte [os elementos do e-mail de convite de colaboração B2B.](invitation-email-elements.md)